![HashMe](Resources/animation.gif =400x300)

#HashMe

##Introduction:

HashMe is a open-source Python3 program that can calculate all 
file digests (like md5, sha1, sha512 etc.) in one go.

##Usage

```bash
$ HashMe.py algorithm [algorithm ...] [option [options ...]]
```

For example:

Submit your confidential reports with hashes:

```bash
$ HashMe.py md5 sha1 -i file_name.xlsx -o md5sums sha1sums
```

OR

```bash
python.exe HashMe.py md5 sha1 -i file_name.doc > checksum.txt
```

Calculate hashes for multiple files:

```bash
$ HashMe.py md5 sha1 -i *.iso -o md5sums sha1sums
```

Is equivalent to:

```bash
$ md5sum *.iso > md5sums
$ sha1sum *.iso > sha1sums
```

## Command-line options

HashMe has some options that can be used to change the behavior:

* `-i file [file ...]` specifies input files to checksum. If no files
  are specified or if the filename is `-` stdin will be used.

* `-o file [file ...]` specifies output files where the results will
  be written. There must be the same number of output files as algorithms.
  If no output files are specified, stdout will be used.

* `--newline [dos, mac, unix, system]` changes the newline format.
  I tend to use Unix newlines everywhere, even on Windows. The default is
  `system`, which uses the current platform newline format.

* `--threads n` runs n threads in parallel. Threads consume input files
  one by one, calculating all the algorithms for each file. Regardless of
  which thread completes first, results will be printed in the same order
  specified as input. The default is to use a single thread. Use `--threads auto`
  to start as many threads as CPUs available.

##Requirements:

You will require Python 3.3+ to enjoy all the features of HashMe seamlessly.

## Portability

Information and error messages are written to stdout and stderr
respectively, using the current platform newline format and encoding.

The actual output (the digests) is always written in UTF-8, both when
writing to stdout or to files. When using the same `--newline format`,
output should be byte by byte identical between platforms.

The output is compatible to that of the coreutils tools and can be checked
with them (e.g. `md5sum -c`). HashMe always reads input in binary mode,
prepending an asterisk to the filename (like md5sum) on output. It makes
no sense to read input as text and md5sum defaulting to text has been a
source of problems (e.g. on Cygwin and Windows) in the past.

The exit status is 0 on success and 1 on errors. After an error,
HashMe skips the current file and proceeds with the next one
instead of aborting. It can be interrupted with Control + C.

HashMe is tested on Windows 7, 8 & 8.1 and on Debian (both x86 and x86-64)
using Python 3.3+. Older versions are not supported.

## Performance

The performance of HashMe depends on many factors:

* Whether the operation is IO-bound (slow hard disks, single algorithm)
  or CPU-bound (RAID or SSD, multiple or more complex algorithms).

* Whether there is [fadvise][] support.

* Performance of the IO-scheduler when running multiple threads. In
  particular, Windows is known to dramatically [degrade][] performance
  when multiple threads read multiple files at the same time.

* Whether the input files are currently cached. Unlikely on big files.

[fadvise]: https://docs.python.org/3/library/os.html#os.posix_fadvise
[degrade]: http://stackoverflow.com/questions/9191/how-to-obtain-good-concurrent-read-performance-from-disk

In the worst case scenario, the performance is comparable to that
of the coreutils tools plus Python's startup time.

In the best cases, specially when calculating multiple algorithms
like md5sums, sha1sums, sha256sums and sha512sums for a ton of isos
(like in Debian releases) HashMe can be many times faster.
Speedups of 4x or more are common.

Your mileage may vary.

## Status

This program is finished!

HashMe is feature-complete and has no known bugs. Unless issues are reported
I plan no further development on it other than maintenance.
