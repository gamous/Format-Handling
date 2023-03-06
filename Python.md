## String (Text)

### Linear Format

#### str.format

```python
f'Results of the {year} {event}'
```

```
replacement_field ::=  "{" [field_name] ["!" conversion] [":" format_spec] "}"
field_name        ::=  arg_name ("." attribute_name | "[" element_index "]")*
arg_name          ::=  [identifier | digit+]
attribute_name    ::=  identifier
element_index     ::=  digit+ | index_string
index_string      ::=  <any source character except "]"> +
conversion        ::=  "r" | "s" | "a"

format_spec       ::=  [[fill]align][sign][#][0][width][grouping_option][.precision][type]
fill              ::=  <any character>
align             ::=  "<" | ">" | "=" | "^"
sign              ::=  "+" | "-" | " "
width             ::=  digit+
grouping_option   ::=  "_" | ","
precision         ::=  digit+
type              ::=  "b" | "c" | "d" | "e" | "E" | "f" | "F" | "g" | "G" | "n" | "o" | "s" | "x" | "X" | "%"
```

[Format String Syntax](https://docs.python.org/3/library/string.html#format-string-syntax)

[Format Specification Mini-Language](https://docs.python.org/3/library/string.html#formatspec)

[Python reference: Lexical analysis #f-strings](https://docs.python.org/3/reference/lexical_analysis.html#f-strings)

### Linear Parse

#### re

```python
import re
ss_string="ssr://aes:123456@google.com:1"
ss_format=r"ssr://(?P<method>.+):(?P<password>.+)@(?P<server>.+):(?P<port>.+)"
ss_node=re.match(ss_format,ss_string).groupdict()
```

[re — Regular expression operations](https://docs.python.org/3/library/re.html)

#### parse

*Not a standard library but a set of elegent regex solution.*

```python
from parse import *
parse("It's {}, I love it!", "It's spam, I love it!")
```

[r1chardj0n3s/parse: Parse strings using a specification based on the Python format() syntax.](https://github.com/r1chardj0n3s/parse)

## Bytes(Binary)

### Format

#### struct.pack

```python
from struct import *
pack('hhl', 1, 2, 3)
calcsize('hhl')
```

[struct — Interpret bytes as packed binary data](https://docs.python.org/3/library/struct.html)

#### ctypes.stringat

```python
from ctypes import *
class Student(Structure):
    _pack_=1
    _fields_=[('name',c_char*10),
            ('serialnum',c_ushort),
            ('school',c_ushort),
            ('gradelevel',c_byte)]
    def _bytes_(self):
        return string_at(addressof(self),sizeof(self.__class__))

student=Student()
student.name=b'raymond'
student.serialnum=4658
student.school=264
student.gradlevel=8
print(student._bytes_())
```



[ctypes — A foreign function library for Python](https://docs.python.org/3/library/ctypes.html)

| Format | ctypes type    | C Type             | Python type            | Standard size | Notes    |
| :----- | :------------- | :----------------- | :--------------------- | :------------ | :------- |
| `x`    |                | pad byte           | no value               |               |          |
| `c`    | `c_char`       | char               | bytes of length 1      | 1             |          |
|        | `c_wchar`      | wchar_t            | string of length 1     | 2             |          |
| `b`    | `c_byte`       | signed char        | integer                | 1             | (1), (2) |
| `B`    | `c_ubyte`      | unsigned char      | integer                | 1             | (2)      |
| `?`    | `c_bool`       | _Bool              | bool                   | 1             | (1)      |
| `h`    | `c_short`      | short              | integer                | 2             | (2)      |
| `H`    | `c_ushort`     | unsigned short     | integer                | 2             | (2)      |
| `i`    | `c_int`        | int                | integer                | 4             | (2)      |
| `I`    | `c_uint`       | unsigned int       | integer                | 4             | (2)      |
| `l`    | `c_long`       | long               | integer                | 4             | (2)      |
| `L`    | `c_ulong`      | unsigned long      | integer                | 4             | (2)      |
| `q`    | `c_longlong`   | long long          | integer                | 8             | (2)      |
| `Q`    | `c_ulonglong`  | unsigned long long | integer                | 8             | (2)      |
| `n`    | `c_ssize_t`    | `ssize_t`          | integer                |               | (3)      |
| `N`    | `c_size_t`     | `size_t`           | integer                |               | (3)      |
| `e`    | `c_double`     | (6)                | float                  | 2             | (4)      |
| `f`    | `c_float`      | float              | float                  | 4             | (4)      |
| `d`    | `c_double`     | double             | float                  | 8             | (4)      |
|        | `c_longdouble` | long double        | float                  | 10            |          |
| `s`    |                | char[]             | bytes                  |               |          |
| `p`    |                | char[]             | bytes                  |               |          |
| `P`    | `c_void_p`     | void*              | integer or `None`      |               | (5)      |
|        | `c_char_p`     | `char *`           | bytes object or `None` |               |          |
|        | `c_wchar_p`    | `wchar_t *`        | string or `None`       |               |          |

### Parse

#### struct.unpack

```python
from struct import *
def bparse(format:str,buffer:bytes,order='<'):
    """
    format          ::= "[field_name]:[format_char]"
    field_name      ::= arg_name
    format_char     ::= "x" | "c" | "b" | "B" | "?" | "h" | "H" | "i" | "I" | "l" | "L" | "q" | "Q" | "n" | "N" | "e" | "f" | "d" | "s" | "p" | "P"
    order           ::= "@" | "=" | "<" | ">" | "!"
    """
    format=list(map(lambda s:s.split(":"),format.split()))
    vnames=[i[0] for i in format]
    format="".join([i[1] for i in format])
    result={i[0]:i[1] for i in zip(vnames,unpack(order+format, record))}
    return result

record = b'raymond   \x32\x12\x08\x01\x08'
student_fmt="name:10s serialnum:H school:H gradelevel:b"
bparse(student_fmt,record)
```

#### ctypes

```python
from ctypes import *
def C_Struct(fields,pack=1):
    class _BStruct(Structure):
        _pack_  = pack    #Pack aligin
        _fields_= fields  #Data format
        def _dict_(self):
            return {i[0]:getattr(self,i[0]) for i in self._fields_}
        def _bytes_(self):
            return string_at(addressof(self),sizeof(self.__class__))
        def parse(self,bstring):
            memmove(addressof(self),bstring,sizeof(self.__class__))
            return self._dict_()
    return _BStruct()

student=C_Struct([('name',c_char*10),('serialnum',c_ushort),('school',c_ushort),('gradelevel',c_byte)])
print(student.parse(record))
```

```python
from ctypes import *

def str_to_ctype(ts:str):
    type_name=ts
    array_size=0
    if(len(ts:=ts.split('*'))>1):
        type_name=ts[0]
        try:
            tz=int(ts[1])
            if(tz>0):array_size=tz
        except:
            pass
    match type_name:
        case "c_char":return c_char if array_size==0 else c_char*array_size
        case "c_int":return c_int if array_size==0 else c_int*array_size
        case "c_uint":return c_uint if array_size==0 else c_uint*array_size
        case "c_int8":return c_int8 if array_size==0 else c_int8*array_size
        case "c_int16":return c_int16 if array_size==0 else c_int16*array_size
        case "c_int32":return c_int32 if array_size==0 else c_int32*array_size
        case "c_int64":return c_int64 if array_size==0 else c_int64*array_size
        case "c_uint8":return c_uint8 if array_size==0 else c_uint8*array_size
        case "c_uint16":return c_uint16 if array_size==0 else c_uint16*array_size
        case "c_uint32":return c_uint32 if array_size==0 else c_uint32*array_size
        case "c_uint64":return c_uint64 if array_size==0 else c_uint64*array_size
        case "c_byte":return c_byte if array_size==0 else c_byte*array_size
        case "c_ubyte":return c_ubyte if array_size==0 else c_ubyte*array_size
        case "c_short":return c_short if array_size==0 else c_short*array_size
        case "c_ushort":return c_ushort if array_size==0 else c_ushort*array_size
        case "c_long":return c_long if array_size==0 else c_long*array_size
        case "c_ulong":return c_ulong if array_size==0 else c_ulong*array_size
        case "c_longlong":return c_longlong if array_size==0 else c_longlong*array_size
        case "c_ulonglong":return c_ulonglong if array_size==0 else c_ulonglong*array_size
        case "c_float":return c_float if array_size==0 else c_float*array_size
        case "c_double":return c_double if array_size==0 else c_double*array_size
        case "c_void_p":return c_void_p if array_size==0 else c_void_p*array_size

def c_struct(cls):
    annotations=cls.__annotations__
    fields=[(field,str_to_ctype(annotations[field])) for field in annotations]
    _pack= 1 if "_pack_" not in dir(cls) else cls._pack_
    class new_cls(Structure):
        _pack_  = _pack    #Pack aligin
        _fields_= fields        #Data format
        def __init__(self, data=None):
            super().__init__()
            if data:self.unpack(data)
        def __bytes__(self) -> bytes:
            return self.pack()
        def __repr__(self) -> str:
            return '<CStruct with fields('+', '.join([f"{i[0]}={getattr(self,i[0])}" for i in self._fields_])+')>'
        def __iter__(self):
            for i in self._fields_:
                yield (i[0],getattr(self,i[0]) )
        def pack(self):
            return string_at(addressof(self),sizeof(self.__class__))
        def unpack(self,raw):
            memmove(addressof(self),raw,sizeof(self.__class__))
            return dict(self)
    for item in set(dir(cls)) - set(dir(new_cls)):
        setattr(new_cls,item,getattr(cls,item))
    return new_cls

@c_struct
class ActorControl:
    _size_ = 0X18
    _pack_ = 4
    id:   'c_uint16' #('0X0')
    arg0: 'c_uint32' #('0X4')
    arg1: 'c_uint32' #('0X8')
    arg2: 'c_uint32' #('0XC')
    arg3: 'c_uint32' #('0X10')

```


## Reference

Python version: 3.10

[The Python Language Reference](https://docs.python.org/3/reference/index.html)

[The Python Standard Library](https://docs.python.org/3/library/index.html)

