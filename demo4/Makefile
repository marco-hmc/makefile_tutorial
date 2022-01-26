foo:=$(wildcard *.c)

hey.c: one.c two.c
	# Outputs "hey", since this is the first target
	echo $@

	# Outputs all prerequisites newer than the target
	echo $?

	# Outputs all prerequisites
	echo $^

	echo $(foo)
	touch hey.c

one.c:
	touch one.c

two.c:
	touch two.c

clean:
	rm -f hey.c one.c two.c