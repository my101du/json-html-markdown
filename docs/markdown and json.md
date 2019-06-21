# json 格式要求

## 整段数据是一个“数组”（[a, b, c]），最后渲染的 markdown 按元素的先后顺序显示出来


```json
[p, img, h1]
```

先后顺序

```md
this is a paragraph

![title](http://www.qq.com/logo.png)

# Tittle
```

## 完整写法：每一个元素（a/b/c）默认以“对象”的形式出现(除非缩写)，有多个属性. 不同类型(`type`)的元素，有不同的属性列表

```json
[
    {
        'type': 'h1',
        'attributes': {
            'text': 'this is Heading 1'
        }
    },
    {
        'type': 'p',
        'attributes': {
            'text': 'this is first line'
        }
    },
    {
        'type': 'img', 
        'attributes': {
            'title': 'this is a image',
            'src': 'https://www.qq.com/logo.png'
        }
    },
    {
        'type': 'ul',
        'attributes': {
            'items': ['the first line', 'the second line', 'the third line']
        }
    }
]
```

## 缩略写法：对于一些只需要简单显示一行文本/内容的元素，如 h1 h2 p img 等，可以简化为一个键值对，只有元素名称(如 `p/img`)和最基本的属性值(如 `text/href`)

```json
[
    {'p': 'this is first line'},
    {'h1': 'this is the Heading 1"},
    {'img': 'http://www.qq.com/logo.png'} 
]
```

对 `p` 还可以**更简化**的，不需要写成对象，直接是一个字符串

```json
[
    'this is the first paragraph',
    'this is the second paragraph',
    'this is the third paragraph'
]
```

## 同一行里有多个元素组合（不换行），例如 文本 + 超链接 + 加粗文本

通常行元素 h1 p 等只显示一个字符串。

```json
[{'h1': 'this is the line'}]
```

但是如果 h1 里还要显示 加粗,和 短代码 等等，像这样

![-w238](media/15472140943096.jpg)

只需要像下面这样

（1）把原来的 字符串 改成 数组（因为数组是依次显示的）
（2）行内拼接的字符串，用 `span`作为键（这样就不会换行，转换引擎只有 p h1 等才换行）
(3) 注意`a` `b`等等, 当它出现在一个数组内部，就是“行内”； 否则单独一行

```json
[
    {
        'h1': [
            {'span': 'I love'},
            {
                'a': {
                    'title': 'PHP',
                    'href': 'http://www.php.net'
                }
            },
            {'b': 'very much'}
        ]
    }
]
```

得到

```md
# I love [PHP](http://www.php.net) **very much**
```


## `children` 用于使用 H1 H2 H3 来生成**内容层级**，便于结构化数据（一眼看出章节关系）

虽然可以直接用 `[h1, h2, p, img h3, p, table]` 这样的顺序关系来“显示”markdown

但是无法体现出数据/内容的“层级”，更推荐用`children`来实现

```json

```


# 特性

* 每个节点的属性都固定，例如 children, name, id 等（便于搜索和存储）类似 Eagle 的 metadata 



```json
{
    // article content (not in the sections)
    content: [
        {
            'p': 'the md filename'
        },
        {
            'img': {
                'title': 'this is an image',
                'href': 'http://www.qq.com/logo.png'
            }
        }
    ], 
    children: [
        // section 1
        {
            // section 1 title: h1
            title: 'the first section, h1',
            // content in section 1 (not in the h2 area)
            content: [
                
            ],
            children: [
                
            ]
        }
    ]
}
```

得到如下

```md

```


关键词简洁, 最好能和 html 标签对应, 降低记忆成本，例如

```json
{'h1': {text: '}}
```

对于部分元素，自动兼容字符串和数组

```json
[
{
    'ul': 'this is single line'
},
{
    'ul': [
        'this is line 1',
        'this is line 2',
        'this is line 3'
    ]
}
]
```

支持一行/多行不同元素混写, 组合成一行，例如, 大标题里，除了文本，还有短代码和链接

```json
{
    'h1': [
        'Hello', //不写 key, 默认是 text
        {'text': 'I love php, sample: '},
        {'code': 'echo $a;'},
        {
            'a': {
                'text': 'Website',
                'href': 'http://www.php.net'
            }
        }
    ]
}
```


# 参考

## json to markdown



## markdown to json

### https://www.npmjs.com/package/md-2-json

1. markdown 按 title 转换成层级 json 
2. 反向转换 json 到 markdown

看代码，对 header  table text  space  paragraph 都可以支持

**问题**
对 a img ul 等语法支持不够


```md
# Heading 1
 
This is a para
 
- This is a list
 
## Heading 2
 
This is a para
```

```json
{
    "Heading 1": {
        raw: "This is a para\n - This is a list\n",
        "Heading 2": {
            raw: "This is a para\n"
        }
    }
}
```