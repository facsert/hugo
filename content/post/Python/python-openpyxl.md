---
title: Python openpyxl
description: 
date: 2022-09-05 20:12:37
categories:
    - Python 技巧
tags:
    - Python
---

python excel 模块

## 安装

```bash
 $ pip install openpyxl
```

## 创建读取

### 创建表

```python
 from openpyxl import Workbook

 wb = Workbook()                                 # 创建一个工作表
 sheet = wb.active                               # 激活第一个 sheet
 sheet['A1'] = 'hellow world'                    # sheet A1 赋值
 wb.save('test.xlsx')                            # 保存 excel 文件为 test.xlsx
 wb.close()                                      # 关闭工作表

 
 wb.create_sheet(name:str [, index:int = -1])    # 自定义 sheet 名称和位置 
 wb.create_sheet('first_sheet', 0)               # 在 0 插入 名为 first_sheet 的表
 sheet.title                                     # 获取sheet 的名称, 也可以重新赋值
 > first_sheet

 for sheet in wb:                                # 遍历工作表, 打印所有表名称
    print(sheet.title)

 wb.remove(sheet)                                # 删除 sheet, 参数是 sheet 对象
```

### 读取表

```python
 from openpyxl import load_workbook

 wb = load_workbook('test.xlsx')                 # 打开一个已存在的 excel 文件

 sheet_names = wb.sheetnames                     # 获取所有表名组成的列表
 > ['first', 'second', 'third']

 sheet = wb['first']                          
 sheet['A1']
 > 'hellow world'
```

## 读写

### 赋值和读取

```python
 sheet['A1'] = 'hellow world'                    # A1 赋值 hellow world
 sheet['A1'].value                               # 读取 A1 方格的值
 > 'hellow world' 

 grid = sheet.cell(row=1, column=1, value='ok')  # 第一列第一行赋值 ok, 返回一个 cell 对象
 sheel.cell(1, 1).value                          # 读取第一行第一列方格的值
 > 'ok'

 row_one =  sheet['A1': 'F1']                    # 获取多数方格, 返回 cell 对象的元组

```

### 多行遍历

- sheet.iter_rows() 每次返回一整行组成的元组
- sheet.iter_cols() 每次返回一整列组成的元组

|参数|含义|
|:-:|:-:|
|`min_row`    |起始行, default:None|
|`max_row`    |结束行, default:None|
|`min_col`    |起始列, default:None|
|`max_col`    |结束列, default:None|
|`values_only`|True:只返回值, False: 返回 cell 对象|

```python

 for row in sheet.iter_rows(                     
    min_row=1, max_col=3, max_row=2):            # 遍历第 1 至第 2 行, 取 3 列 
    for cell in row:
        print(cell)

 <Cell Sheet1.A1>
 <Cell Sheet1.B1>
 <Cell Sheet1.C1>
 <Cell Sheet1.A2>
 <Cell Sheet1.B2>
 <Cell Sheet1.C2>

 for col in sheet.iter_cols(
    min_row=1, max_col=3, max_row=2):            # 遍历第 1 至第 2 行, 取 3 列 
    for cell in col:
        print(cell)

<Cell Sheet1.A1>
<Cell Sheet1.A2>
<Cell Sheet1.B1>
<Cell Sheet1.B2>
<Cell Sheet1.C1>
<Cell Sheet1.C2>
```

## 样式

### 字体

```python
 from openpyxl.styles import Font

 cell.font = Font(
   name="微软雅黑",                                  # 字体
   size=15,                                         # 字体大小
   color="0000FF",                                  # 字体颜色，用16进制rgb表示
   bold=True,                                       # 是否加粗，True/False
   italic=True,                                     # 是否斜体，True/False
   strike=None,                                     # 是否使用删除线，True/False
   underline=None,                                  # 下划线, 可选'singleAccounting', 'double', 'single', 'doubleAccounting'
 )

```

### 填充颜色

```python
 from openpyxl.styles import PatternFill

 cell.fill = PatternFill(
    patternType="solid",                         # 填充类型
    fgColor="F562a4",                            # 前景色，16进制rgb
    bgColor="0000ff",                            # 背景色，16进制rgb
 )

 patternType:                                    # patternType 可选值
     none、solid、darkGray、mediumGray、lightGray、
     lightDown、lightGray、lightGrid
```

### 边框

```python
 from openpyxl.styles import Border, Side

 side = Side(
   style="medium",                               # 边框样式
   color="ff66dd",                               # 边框颜色，16进制rgb表示
 )

 style:                                          # style 可选值
     dashDot、dashDotDot、dashed、dotted、double、
     hair、medium、mediumDashDot、mediumDashDotDot、
     mediumDashed、slantDashDot、thick、thin

 cell.border = Border(
    top=side,                                    # 上边框使用定义的 side
    bottom=side,                                 # 下边框使用定义的 side
    left=side,                                   # 左边框使用定义的 side
    right=side,                                  # 右边框使用定义的 side
    diagonal=side                                # 对角线使用定义的 side
)

```

### 对齐

```python
 from openpyxl.styles import Alignment

 cell.alignment = Alignment(
    horizontal='left',                           # 水平对齐，可选general、left、center、right、fill、justify、centerContinuous、distributed
    vertical='top',                              # 垂直对齐， 可选top、center、bottom、justify、distributed
    text_rotation=0,                             # 字体旋转，0~180整数
    wrap_text=False,                             # 是否自动换行
    shrink_to_fit=False,                         # 是否缩小字体填充
    indent=0,                                    # 缩进值
)
```
