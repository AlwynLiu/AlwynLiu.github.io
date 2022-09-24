---
title: pymysql获取excel数据批量写入数据库
date: 2021-03-16 15:33:35
tags: mysql
---

## 获取excel的数据

由于系统需要获取到的文档的格式是xlsx，又发现xlrd在2.0.2不支持读取xlsx的文档了，所以在用了openpyxl库去读取xlsx格式的excel


```python
from openpyxl import load_workbook
workbook = load_workbook('202102.xlsx')

# 读取所需数据表
detail_sheet = workbook['明细']
detail_sheet_json = []
for i in range(2, detail_sheet.max_row + 1):
    # 把数据写为一个元祖或是数组，使用pymysql进行批量插入
    sub_data = (detail_sheet.cell(i, 2).value,
                str(detail_sheet.cell(i, 3).value),
                detail_sheet.cell(i, 4).value,
                detail_sheet.cell(i, 5).value,
                detail_sheet.cell(i, 6).value,
                detail_sheet.cell(i, 7).value,
                detail_sheet.cell(i, 8).value,
                detail_sheet.cell(i, 9).value,
                str(detail_sheet.cell(i, 10).value),
                str(detail_sheet.cell(i, 11).value))
    detail_sheet_json.append(sub_data)
print('明细表的数据：', detail_sheet_json)

```

## 使用pymysql批量导入


```python
# 链接mysql数据库
import pymysql
from popy.env import Env
employee_purchase = Env.getCfg().Config.EMPLOYEE_PURCHASE_SYSTEM
connection = pymysql.connect(employee_purchase['host'], employee_purchase['user'], employee_purchase['passwd'],
                             employee_purchase['db'])
# 执行msyql数据的更新
with connection.cursor() as cursor:
    # 需要批量更新的sql
    updatesql = """insert into nx_order_info(wechat_name, employee_id, employee_name, order_address, dept, order_number,
    order_title, order_desc, order_count, order_price) values ((%s), (%s), (%s), (%s), (%s), (%s), (%s), (%s), (%s), (%s))"""
    try:
        # 采用执行传参的方式可以避免sql注入
        execute_result = cursor.executemany(updatesql, detail_sheet_json)
        connection.commit()
        # print('result:', result)
        print('执行完成execute_result:', execute_result)
    except Exception as e:
        print("反正是写出问题了的：", e)
```