---
title: Python 扁平化字典
description: 
date: 2022-07-24 19:05:13
categories:
    - Python 技巧
tags:
    - Python
---

## 引申

字典是 python 非常常用的数据类型, 字典存取效率高且存取方便  
一个字典可以存储大量数据, 为了便于区分还可以层层分级, 多层嵌套  

```py
student = {
    "petter" : {
        "age": 16,
        "hobby": "swim",
    },
    "lily": {
        "age": 15,
        "hobby": "sing",
    }
}

student["pttter"]                                # 字典取值
student["steven"] = {"age": 18}                  # 字典添加 key value
student["pttter"]["age"] = 17                    # 字典多层数据赋值 (petter 必须存在)
```

对于多层字典存取比较麻烦  
插入值多层的数据的时候需要考虑上层是否存在  

```py
student["pttter"]["hobby"]                       # 取值时要确保上一层 petter 必须存在
student["Bob"]["age"] = 18                       # KeyError 报错, 需先定义上一层 Bob
> KeyError: 'bob'
```

能否简化深层字典的存取方式  
插入值的时候能否忽略层级问题, 自动生成多级数据  

## 字典扁平化

如何简单快捷的进行快速存取深层字典呢?  
能否将字典简化成单层结构, 字典内就是 key 和 value  

```python
def flat_dict(dic, parent_key='', result={}):
    '''
    Description: 原生字典多层 key 通过分隔符连接写入 flat 字典
    Param dic dict: 原生字典
    Param parent_key dict: 父字典的 key 
    Return: None
    Attention: 任一层的字典 key value 都要保存
    '''        
    for key, value in dic.items():
        new_key = f"{parent_key}.{key}" if parent_key else key
        result[new_key] = value                  # 每深入一层, 就将 key 通过 . 并入
        if isinstance(value, dict):
           flat_dict(value, new_key, result)     # 对 value 时字典类型的值进行递归

flat = {}
flat_dict(student, '', flat)                     # 将扁平化键值对保存到 flat 中
> {                                              
    'petter': {'age': 16, 'hobby': 'swim'}, 
    'petter.age': 16,                            
    'petter.hobby': 'swim', 
    'lily': {'age': 15, 'hobby': 'sing'}, 
    'lily.age': 15, 
    'lily.hobby': 'sing'
}

flat['lily.age']                                 # 15 可以通过多层 key 取值
```

为了保证任意层 key 都能取值, 所有层字典都需要扁平化, 因此增加了字典容量

## 字典存取

能将字典扁平化后, 考虑如何存取  
魔改魔术方法 `setitem` 和 `getitem` 通过 [] 存取数据  

通过继承原始字典, 保留原生字典的属性和方法

```python
class FlatDict(dict): 

    def __init__(self, *args, **kwargs):
        '''
        Description: 初始化属性, flat(扁平化字典) separator(分隔符)
        Return: None
        Attention: 对象存一个原生字典和扁平化字典
        '''        
        super().__init__(*args, **kwargs)
        super().update(*args, **kwargs)
        self.flat = {}                           # 新增属性 flat 保存扁平化字典
        self.separator = '.'                     # 定义 key 连接符号

    def __setitem__(self, key, value):
        pass
 
    def __getitem__(self, key):
        pass
```

### 写入

|不带连接符号的 key|带连接符号的 key|
|:-|:-|
|使用字典原有赋值方法|解析 key 生成字典写入字典再扁平化|

```python
    def __setitem__(self, key, value):
        '''
        Description: 字典 [] 方式设置值
        Param key str: 原生字典
        Param value Any: 父字典的 key 
        Return: None
        Attention: 
        '''        
        if self.separator in key:
            self.update_dict(key, value)
        else:
            super().__setitem__(key, value)

    def update_dict(self, key, value):
        '''
        Description: 解析 key, 将多层 key 逐层解析写入原生字典
        Param key str: 字典 key, 多层 key 包含分隔符 
        Param value Any: 字典 value
        Return: None
        Attention: 
        '''        
        dic = self
        keys = key.split(self.separator)
        for k in keys[:-1]:
            dic.setdefault(k, {})
            if not isinstance(dic[k], dict):
                dic.update({k: {}})
            dic = dic[k]

        dic[keys[-1]] = value
        self.flat_dict(self)
```

### 取出

先尝试从从本体取出, 失败后从扁平化字典取值  

```python
    def __getitem__(self, key):
        '''
        Description: 字典 [] 获取值
        Param key str: 字典 key, 允许使用多层 key 
        Return Any: 字典 key 对应的 value 
        Attention: 
        '''        
        try:
            return super().__getitem__(key)
        except KeyError:
            return self.flat[key]
```

## 实现

`__str__` 能直接格式化打印结果
添加自定义分隔符

```python
from json import dumps

class FlatDict(dict): 
    """扁平化字典"""   

    def __init__(self, *args, **kwargs):
        '''
        Description: 初始化属性, flat(扁平化字典) separator(分隔符)
        Return: None
        Attention: 对象存一个原生字典和扁平化字典
        '''        
        super().__init__(*args, **kwargs)
        super().update(*args, **kwargs)
        self.flat = {}
        self.separator = '.'

    def update_dict(self, key, value):
        '''
        Description: 解析 key, 将多层 key 逐层解析写入原生字典
        Param key str: 字典 key, 多层 key 包含分隔符 
        Param value Any: 字典 value
        Return: None
        Attention: 
        '''        
        dic = self
        keys = key.split(self.separator)
        for k in keys[:-1]:
            dic.setdefault(k, {})
            if not isinstance(dic[k], dict):
                dic.update({k: {}})
            dic = dic[k]

        dic[keys[-1]] = value
        self.flat_dict(self)

    def flat_dict(self, dic, parent_key=''):
        '''
        Description: 原生字典多层 key 通过分隔符连接写入 flat 字典
        Param dic dict: 原生字典
        Param parent_key dict: 父字典的 key 
        Return: None
        Attention: 任一层的字典 key value 都要保存
        '''        
        for key, value in dic.items():
            new_key = f"{parent_key}{self.separator}{key}" if parent_key else key
            self.flat[new_key] = value
            if isinstance(value, dict):
                self.flat_dict(value, new_key)

    def __setitem__(self, key, value):
        '''
        Description: 字典 [] 方式设置值
        Param key str: 原生字典
        Param value Any: 父字典的 key 
        Return: None
        Attention: 
        '''        
        if self.separator in key:
            self.update_dict(key, value)
        else:
            super().__setitem__(key, value)

    def __getitem__(self, key):
        '''
        Description: 字典 [] 获取值
        Param key str: 字典 key, 允许使用多层 key 
        Return Any: 字典 key 对应的 value 
        Attention: 
        '''        
        try:
            return super().__getitem__(key)
        except KeyError:
            return self.flat[key]

    def __delitem__(self, key):
        '''
        Description: 字典删除 key-value
        Param key str: 字典 key, 不允许使用多层 key 
        Return: None
        Attention: 只允许使用原生字典的 key
        '''    
        super().__delitem__(key)
        self.flat = {}
        self.flat_dict(self)

    def __len__(self):
        '''
        Description: 获取原生字典长度
        Return int: 字典长度 
        Attention: 
        '''        
        return super().__len__()

    def update(self, *args, **kwargs):
        '''
        Description: 更新字典
        Return: None 
        Attention: 用法与原生字典一致
        '''        
        super().update(*args, **kwargs)
        self.flat = {}
        self.flat_dict(self)

    def __str__(self):
        '''
        Description: json 格式原生字典
        Return srt: 字典字符串
        Attention: 
        '''        
        return dumps(self, indent=4)

```

```py
flat = FlatDict()
flat.update({
    'a': {
        'b': { 'c': 1 },
    },
    'e':3,    
})
print(flat['a.b'])
flat['a.c.d'] = 2
print(flat)

{'c': 1}
{
    "a": {
        "b": {
            "c": 1
        },
        "c": {
            "d": 2
        }
    },
    "e": 3
}
```
