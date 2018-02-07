## File Input/Output

You read from a file into memory and write from memory to a file.  
Before reading or writing a file, you need to open it:    
```python
fileobj = open(filename, mode)
```

*mode* is a string indicating the file's type and what you want to do with it.  
The first letter of *mode* indicates the *operation*:  
* r means read
* w means write. If the file does not exist, it's created. If the file does exist, it's overwritten
* x means write, but only if the file does not already exist.
* a means append (write after the end) if the file exists.  

The second letter of *mode* is the file's *type*:  
* t (or nothing) means text.
* b means binary

Before reading or writing a file, you need to *open* it. After opening the file, you call functions to read and write data, and you need to *close* the file at last.

### Write a Text File with write( )
