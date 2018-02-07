
## 文件输入/输出

把数据从一个文件读入内存，然后从内存写入文件。读写一个文件之前需要打开它：

fileobj = open(filename, mode)

• fileobj是open( )返回的文件对象；  
• filename是该文件的字符串名；   
• mode是指明文件类型和操作的字符串。

mode的第一个字母表明对其的操作。

• r表示读模式。   
• w表示写模式。如果文件不存在则新创建，如果存在则重写新内容。  
• x表示在文件不存在的情况下新创建并写文件。   
• a表示如果文件存在，在文件末尾追加写内容。

mode的第二个字母是文件类型：  
• t（或者省略）代表文本类型；   
• b代表二进制文件。

打开文件之后就可以调用函数来读写数据，最后需要关闭文件，如果不关闭的话，看到的是一个空的文件。

### 使用write( )写文本文件

原数据：


```python
poem = '''There was a young lady named Bright, 
... Whose speed was far faster than light; 
... She started one day 
... In a relative way,
... And returned on the previous night.'''
```


```python
len(poem)
```




    153



以下代码将整首诗写到文件'relativity'中：


```python
fout = open('relativity', 'wt')
fout.write(poem)
fout.close()
```


```python
type(fout)
```




    _io.TextIOWrapper



函数write( )返回写入文件的字节数。和print()一样，它没有增加空格或者换行符。同样，你也可以在一个文本文件中使用print()：


```python
fout = open('relativity','wt')
print(poem, file = fout)
fout.close()
```


```python
fout1 = open('relativity1', 'x')
print(poem, file = fout1)
fout1.close()
```

这就产生了一个问题：到底是使用write( )还是print( )？print( )默认会在每个参数后面添加空格，在每行结束处添加换行。 在之前的例子中，它在文件relativity中默认添加了一个换行。为了使print( )与write( )有同样的输出，传入下面两个参数。

如果源字符串非常大，可以将数据分块，直到所有字符被写入：


```python
fout2 = open('relativity2', 'wt')
size = len(poem)
offset = 0
chunk = 100

while True:
    if offset > size:
        break
    fout2.write(poem[offset:offset+chunk])
    offset += chunk

fout2.close()  # 如果不关闭，看到的是一个空的文件。 
```

### 8.1.2　使用read( )、readline( )或者readlines( )读文本文件
你可以按照下面的示例那样，使用不带参数的read()函数一次读入文件的所有内容。但在 读入文件时要格外注意，1 GB的文件会用到相同大小的内存。


```python
fin = open('relativity','rt')
poem = fin.read()
fin.close()
poem
```




    'There was a young lady named Bright, \nWhose speed was far faster than light; \nShe started one day \nIn a relative way,\nAnd returned on the previous night.'




```python
type(poem)
```




    str



同样也可以设置最大的读入字符数限制read( )函数一次返回的大小。下面一次读入100个字符，然后把每一块拼接成原来的字符串poem：


```python
poem = ''
fin = open('relativity','rt')
chunk = 100

while True:
    fragment = fin.read(chunk)
    if not fragment:
        break
    poem = poem + fragment

fin.close()
poem
```




    'There was a young lady named Bright, \nWhose speed was far faster than light; \nShe started one day \nIn a relative way,\nAnd returned on the previous night.'



读到文件结尾之后，再次调用read()会返回空字符串（''） ，if not fragment条件被判为False。此时会跳出while True的循环。

当然，你也能使用readline()每次读入文件的一行。

读取文本文件最简单的方式是使用一个迭代器（iterator），它会每次返回一行。这和之前的例子类似，但代码会更短：


```python
poem = ''
fin = open('relativity','rt')

for line in fin:
    poem += line

fin.close()
poem
```




    'There was a young lady named Bright, \nWhose speed was far faster than light; \nShe started one day \nIn a relative way,\nAnd returned on the previous night.'




```python
print(poem)
```

    There was a young lady named Bright, 
    Whose speed was far faster than light; 
    She started one day 
    In a relative way,
    And returned on the previous night.


### 8.1.3　使用write( )写二进制文件

如果文件模式字符串中包含'b'，那么文件会以二进制模式打开。这种情况下，读写的是字节而不是字符串。

我们手边没有二进制格式的诗，所以直接在0~255产生256字节的值：


```python
bdata = bytes(range(0,256))
len(bdata)
```




    256




```python
fout3 = open('bfile', 'wb')
fout3.write(bdata)
fout3.close()
```

### 8.1.5　使用with自动关闭文件

如果你忘记关闭已经打开的一个文件， 在该文件对象不再被引用之后Python会关掉此文件。这也就意味着在一个函数中打开文件，没有及时关闭它，但是在函数结束时会被关掉。然而你可能会在一直运行中的函数或者程序的主要部分打开一个文件，应该强制剩下的所有写操作完成后再关闭文件。

Python的上下文管理器（context manager）会清理一些资源，例如打开的文件。它的形式为with expression as variable：


```python
with open('relativity','wt') as fout:
    fout.write(poem)
```

完成上下文管理器的代码后，文件会被自动关闭。

### 8.1.6　使用seek( )改变位置 

无论是读或者写文件，Python都会跟踪文件中的位置。函数tell( )返回距离文件开始处的字节偏移量。函数seek( )允许跳转到文件其他字节偏移量的位置。这意味着可以不用从头读取文件的每一个字节，直接跳到最后位置并只读一个字节也是可行的。



```python
fin = open('relativity', 'rt')
fin.seek(100)
```




    100



## 8.2　结构化的文本文件

对于简单的文本文件，唯一的结构层次是间隔的行。然而有时候需要更加结构化的文本，用于后续使用的程序保存数据或者向另外一个程序传送数据。结构化的文本有很多格式，区别它们的方法如下所示。 

• 分隔符，比如tab（'\t'）、逗号（','）或者竖线（'|'） 。逗号分隔值（CSV）就是这 样的例子。  
• '<'和'>'标签，例如XML和HTML。   
• 标点符号，例如JavaScript Object Notation（JSON2） 。   
• 缩进，例如YAML（即YAML Ain't Markup Language的缩写） ，要了解更多可以去搜索。  
• 混合的，例如各种配置文件。 

每一种结构化文件格式都能够被至少一种Python模块读写。

### 8.2.1　CSV

带分隔符的文件一般用作数据交换格式或者数据库。你可以人工读入CSV文件，每一次读取一行，在逗号分隔符处将每行分开，并添加结果到某些数据结构中，例如列表或者字典。但是，最好使用标准的csv模块，因为这样切分会得到更加复杂的信息。

• 除了逗号，还有其他可代替的分隔符：'|'和'\t'很常见。   
• 有些数据会有转义字符序列，如果分隔符出现在一块区域内，则整块都要加上引号或者在它之前加上转义字符。  
• 文件可能有不同的换行符，Unix系统的文件使用'\n'，Microsoft使用'\r\n'，Apple 之前使用'\r'而现在使用'\n'。  
• 在第一行可以加上列名。

首先读和写一个列表的行，每一行包含很多列：


```python
import csv
```


```python
with open('clxuser.csv', 'rt') as fin:  # a context manager
    cin = csv.reader(fin)
    user = [row for row in fin]  

print(user[0:10])
```

    ['user,region\n', '13846574259,DAXINGANLING\n', '15094621160,DAXINGANLING\n', '13555073746,DAXINGANLING\n', '13904578870,DAXINGANLING\n', '13845791722,DAXINGANLING\n', '13604871839,DAXINGANLING\n', '13846574489,DAXINGANLING\n', '13555081093,DAXINGANLING\n', '13504561788,DAXINGANLING\n']



```python
type(user)
```




    list


