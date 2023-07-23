---
title: Python 扁平化字典
description: 
date: 2022-07-19 19:05:13
categories:
    - Python 技巧
tags:
    - Python
---

<!--
 * @FilePath: \文档\Learning\python\python-flat-dict.md
 * @Author: facser
 * @Date: 2022-07-25 20:08:15
 * @LastEditTime: 2022-07-31 15:01:10
 * @LastEditors: facser
 * @Description: 
-->

# python flat dictionary

## 引申

字典经常被用来存取数据, 键值对的组合非常便于使用
一个字典可以存储大量数据, 为了便于区分还可以层层分级, 多层嵌套

对于多层字典存取比较麻烦
插入值多层的数据的时候需要考虑上层是否存在

能否简化深层字典的存取方式
插入值的时候能否忽略层级问题, 自动生成多级数据

## 字典扁平化

如何简单快捷的进行快速存取深层字典呢?
能否将字典简化成单层结构, 字典内就是 key 和 value

```python
def flat_dict(dic):
    for key, value in dic.items():
        if isinstance(value, dict):
            for k, v in flat_dict(value):
                k = '{key}.{k}'.format(key=key, k=k)
                yield (k, v)
        else:
            yield (key, value)
```

```python
if __name__ == '__main__':
    depth_dict = {'a':{'b': {'c': 0}, 'd':1}, 'c': 1}
    print(dict(flat_dict(depth_dict)))

>>> {'a.b.c': 0, 'a.d': 1, 'c': 1}
```

通过递归循环遍历深层字典, 把多层 key 通过分隔符连接
但是, 这样扁平化无法获取字典类型的 value

```python
def flat_dict(dic):
    for key, value in dic.items():
        yield (key, value)
        if isinstance(value, dict):
            for k, v in flat_dict(value):
                k = '{key}.{k}'.format(key=key, k=k)
                yield (k, v)  
```

```python
if __name__ == '__main__':
    depth_dict = {'a':{'b': {'c': 0}, 'd':1}, 'c': 1}
    print(dict(flat_dict(depth_dict)))

>>> {'a': {'b': {'c': 0}, 'd': 1}, 'a.b': {'c': 0}, 'a.b.c': 0, 'a.d': 1, 'c': 1}
```

增加了字典容量, 但是保存了所有 key 的值

## 字典存取

能将字典扁平化后, 考虑如何存取
魔改魔术方法 `setitem` 和 `getitem` 通过 [] 存取数据

保留字典原有属性和方法, 新数据类型继承字典类

```python

class Flat(dict):

    def __init__(self, depth):
        dict.__init__(self, depth)
        self.flat = OrderedDict()
        self.char_split = '.'

    def flat_dict(self):
        pass

    def update_dict(self, key, value):
        pass

    def __setitem__(self, key, value):
        pass
 
    def __getitem__(self, key):
        pass
```

### 写入

- 解析扁平化 key 生成深度字典, 且更新到深度字典
- 把数据写入深度字典和扁平化字典

```python
    def __setitem__(self, key, value):
        self.update_dict(key, value)

    def update_dict(self, key, value):
        key_list = key.split(self.char_split)
        first, last = key_list[0], key_list[-1]
        
        dic = self
        for k in key_list[:-1]:
            dic.setdefault(k, {})
            if not isinstance(dic[k], dict):
                dic.update({k: {}})
            dic = dic[k]

        dic.update({last: value})
        self.flat.update(self.flat_dict({first: self[first]}))
```

### 取出

```python
    def __getitem__(self, key):
        try:
            return dict.__getitem__(self, key)
        except KeyError:
            return self.flat[key]
```

## 实现

`__str__` 能直接格式化打印结果
添加自定义分隔符

```python
class Flat(dict):
    
    def __init__(self, depth):
        dict.__init__(self, depth)
        self.flat = OrderedDict()
        self.char_split = '.'

    def flat_dict(self, dic):
        for key, value in dic.items():
            yield (key, value)
            if isinstance(value, dict):
                for k, v in self.flat_dict(value):
                    k = '{key}{char}{k}'.format(
                        char=self.char_split,
                        key=key, k=k)
                    yield (k, v)

    def update_dict(self, key, value):

        key_list = key.split(self.char_split)
        first, last = key_list[0], key_list[-1]
        
        dic = self
        for k in key_list[:-1]:
            dic.setdefault(k, {})
            if not isinstance(dic[k], dict):
                dic.update({k: {}})
            dic = dic[k]

        dic.update({last: value})
        self.flat.update(self.flat_dict({first: self[first]}))
   
    def __setitem__(self, key, value):
        self.update_dict(key, value)
 
    def __getitem__(self, key):
        try:
            return dict.__getitem__(self, key)
        except KeyError:
            return self.flat[key]

    def __str__(self):
        return dumps(self, indent=4)
```

```python
flat = Flat()
flat['a.b.c'] = 1
flat['b.c.a'] = 2
print(flat['a.b'])
print(flat['b.c.a'])
print(flat)


{'c': 1}
2
{
    "a": {
        "b": {
            "c": 1
        }
    },
    "b": {
        "c": {
            "a": 2
        }
    }
}
```


