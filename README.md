# Libsquash

Portable, User-land SquashFS that can be easily linked and embedded within your application; a [squashfuse](https://github.com/vasi/squashfuse) fork.

[![Build status: Linux and Darwin](https://travis-ci.org/pmq20/libsquash.svg?branch=master)](https://travis-ci.org/pmq20/libsquash)
[![Build status: Windows](https://ci.appveyor.com/api/projects/status/f4htq948gag3l2k8/branch/master?svg=true)](https://ci.appveyor.com/project/pmq20/libsquash/branch/master)

## About the fork

This project was forked from https://github.com/vasi/squashfuse with the following modifications.

1. Removed fuse the dependency so that this library can be easily linked and embedded.
1. Read data from the memory instead of from a file,
which is by passing a pointer to an array of bytes that could be generated by
an independently installed [mksquashfs](http://squashfs.sourceforge.net/) tool
and be loaded into memory in advance.
1. Introduced vfd(virtual file descriptor) as a handle for follow-up
libsquash operations. The vfd is a non-negative integer that lives together with
other ordinary file descriptors of the process.
1. Added CMake so that Xcode and Vistual Studio Projects could be easily generated
1. Made it compile on 3 platforms simultanesly: Windows, Mac OS X and Linux
1. Added travis-ci for Linux and Darwin CI; appveyor for Windows CI
1. Added test fixtures and C-level tests
1. Added extra API's, see below.

## Building

On most systems you could build and test the library using the following commands,

    mkdir build
    cd build
    cmake ..
    cmake --build .
    ctest --verbose

## Extra API's

### `squash_stat(error, fs, path, buf)`

Obtains information about the file pointed to by `path` of a SquashFS `fs`.
The `buf` argument is a pointer to a stat structure as defined by
`<sys/stat.h>` and into which information is placed concerning the file.
Upon successful completion a value of `0` is returned.
Otherwise, a value of `-1` is returned and `error` is set to the reason of the error.

### `squash_lstat(error, fs, path, buf)`

Acts like `squash_stat()` except in the case where the named file is a symbolic link;
`squash_lstat()` returns information about the link,
while `squash_stat()` returns information about the file the link references.

### `squash_fstat(error, fs, vfd, buf)`

Obtains the same information as `squash_stat()`
about an open file known by the virtual file descriptor `vfd`.

### `squash_open(error, fs, path)`

Opens the file name specified by `path` of a SquashFS `fs` for reading.
If successful, `squash_open()` returns a non-negative integer, termed a vfd(virtual file descriptor).
It returns `-1` on failure and sets `error` to the reason of the error.
The file pointer (used to mark the current position within the file) is set to the beginning of the file.
The returned vfd should later be closed by `squash_close()`.

### `squash_close(error, vfd)`

Deletes a `vfd`(virtual file descriptor) from the per-process object reference table.
Upon successful completion, a value of `0` is returned.
Otherwise, a value of `-1` is returned and `error` is set to the reason of the error.

### `squash_read(error, vfd, buf, nbyte)`

Attempts to read `nbyte` bytes of data from the object
referenced by `vfs` into the buffer pointed to by `buf`,
starting at a position given by the pointer associated with `vfd` (see `squash_lseek`),
which is then incremented by the number of bytes actually read upon return.
When successful it returns the number of bytes actually read and placed in the buffer;
upon reading end-of-file, zero is returned;
Otherwise, a value of `-1` is returned and `error` is set to the reason of the error.

### `squash_lseek(error, vfd, offset, whence)`

Repositions the offset of `vfs` to the argument `offset`, according to the directive `whence`.
If `whence` is `SQUASH_SEEK_SET` then the offset is set to `offset` bytes;
if `whence` is `SQUASH_SEEK_CUR`, the `offset` is set to its current location plus `offset` bytes;
if `whence` is `SQUASH_SEEK_END`, the `offset` is set to the size of the file
and subsequent reads of the data return bytes of zeros.
The argument `fildes` must be an open virtual file descriptor.
Upon successful completion,
it returns the resulting offset location as measured in bytes from the beginning of the file.
Otherwise, a value of `-1` is returned and `error` is set to the reason of the error.

### `squash_readlink(error, fs, path, buf, bufsize)`

Places the contents of the symbolic link `path` of a SquashFS `fs`
in the buffer `buf`, which has size `bufsize`.
It does not append a NUL character to `buf`.
If it succeeds the call returns the count of characters placed in the buffer;
otherwise `-1` is returned and `error` is set to the reason of the error.

## Acknowledgment

Thank you [Dave Vasilevsky](https://github.com/vasi) for the excellent work of squashfuse!

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/pmq20/libsquash.

## License

Squashfuse is copyright (c) 2012-2014 Dave Vasilevsky <dave@vasilevsky.ca>

Copyright (c) 2016-2017 Minqi Pan, under terms of the [MIT License](http://opensource.org/licenses/MIT).
