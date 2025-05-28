#networking 

Flags

| Flag | Description                                                                                                                                                                                                                                   |
| ---- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -n   | Don't convert addresses (i.e., host addresses, port numbers, etc.) to names.                                                                                                                                                                  |
| -t   | Don't print a timestamp on each dump line.                                                                                                                                                                                                    |
| -c   | Exit after receiving or reading _count_ packets. If the `--skip` option is used, the _count_ starts after the skipped packets.                                                                                                                |
| -l   | Make stdout line buffered. Useful if you want to see the data while capturing it. E.g.,<br><br>tcpdump -l \| tee dat                                                                                                                          |
| -U   | Similar to `-l` in its behavior, but it will cause output to be packet-buffered, so that the output is written to stdout at the end of each packet rather than at the end of each line; this is buffered on all platforms, including Windows. |
| -w   | Write the raw packets to _file_ rather than parsing and printing them out. They can later be printed with the -r option. Standard output is used if _file_ is ``-''.                                                                          |
