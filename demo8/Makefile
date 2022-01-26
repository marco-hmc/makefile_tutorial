src=$(wildcard *.c ./sub/*.c)
dir=$(notdir $(src))
obj=$(patsubst %.c,%.o,$(dir) )

all:
	@echo $(src)
	@echo $(dir)
	@echo $(obj)
	@echo "end"
