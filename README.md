# pwntools - CTF toolkit.
![pwntools logo](https://github.com/Gallopsled/pwntools/blob/stable/docs/source/logo.png?raw=true)

[![Docs](https://readthedocs.org/projects/pwntools/badge/?version=stable)](https://docs.pwntools.com/)
[![PyPI](https://img.shields.io/badge/pypi-v3.5.1-green.svg?style=flat)](https://pypi.python.org/pypi/pwntools/)
[![Travis](https://travis-ci.org/Gallopsled/pwntools.svg)](https://travis-ci.org/Gallopsled/pwntools)
[![Coveralls](https://img.shields.io/coveralls/Gallopsled/pwntools/dev.svg)](https://coveralls.io/github/Gallopsled/pwntools?branch=dev)
[![Twitter](https://img.shields.io/badge/twitter-pwntools-4099FF.svg?style=flat)](https://twitter.com/pwntools)
[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg?style=flat)](http://choosealicense.com/licenses/mit/)

pwntools is a CTF framework and exploit development library. Written in Python, it is designed for rapid prototyping and development, and intended to make exploit writing as simple as possible.

* * *

There are some features that added by [ddaa](http://ddaa.tw) as below:

## pwnlib.tube
### 1. The length limit for `send` and `sendline`.
The length limit is an efficient way to prevent from sending excess data by accident when we do not check the termination condition of input function carefully. 

```python
def send(self, data, n=-1):
    """send(data, n)

    Sends data. If the second argument `n` is given, only send the n bytes of data.

    Examples:

        >>> def p(x): print repr(x)
        >>> t = tube()
        >>> t.send_raw = p
        >>> t.send('a'*0x100, 0x10)
        ''aaaaaaaaaaaaaaaa''
    """
```

### 2. pwnlib.tube.leak function
The purpose of the leaking function is automatically receving the prefix and pack the address to an integer. For example, there is a off-by-one vulnerabiliy and we can fill the buffer up to leak the content on the stack. In the past, we may need to call `recvuntil` at first to filter redundant data. Then, we received the data which included memory address, such as stack frame pointer, and add paddings to unpack into an integer. Now, we can call `leak` to finish in a line.

```python
def leak(self, delim_start='', delim_end='\n', timeout=default):
    """leak() -> int
    Leak and pack the address into an integer.

    Examples:

        >>> t = tube()
        >>> t.unrecv('a'*10 + p32(0x1234) + '\n')
        >>> hex(t.leak(delim_start='a'*10))
        '0x1234'
    """
```

## pwnlib.elf
### 1. pwnlib.elf.binsh
We always need '/bin/sh' string when get the shell.

```python
    Examples:

        >>> libc = ELF('libc.so.6')
        >>> hex(libc.binsh)
        >>> '0x18c177'
```

### 2. Support full relro symbols
`pwnlib.elf` cannot parse symbols correctly in full relro binary after one glibc version. I made a workaround to fix the issue. It's work for me when I solved CTF challenge. Notice, it may cause some side effect. The related discussion can see at [here](http://github.com/Gallopsled/pwntools/pull/838). The official pwntools has solved the issue with [unicorn](https://github.com/Gallopsled/pwntools/pull/899), but I think it's too heavy.
