# files = file1 file2
# some_file: ${files}
# 	echo "Look at this variable: " ${files}
# 	touch some_file

# file1:
# 	touch file1
# file2:
# 	touch file2

# hhhh:
# 	echo "hhhh"

# hhh:
# 	rm -f file1 file2 some_file
print: $(wildcard *.c)
	ls -la  $?