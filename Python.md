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
class Student(Structure):
    _pack_=1
    _fields_=[('name',c_char*10),
            ('serialnum',c_ushort,2),
            ('school',c_ushort,2),
            ('gradelevel',c_bool)]
student=Student()
memmove(addressof(student),record,sizeof(Student))
```

[ctypes — A foreign function library for Python](https://docs.python.org/3/library/ctypes.html)

## Reference

Python version: 3.10

[The Python Language Reference](https://docs.python.org/3/reference/index.html)

[The Python Standard Library](https://docs.python.org/3/library/index.html)
