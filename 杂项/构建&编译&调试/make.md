- makefile文件的基本格式

  - ```makefile
    target: dependencies
           steps to build target with dependencies
    ```

  - 目标、源文件、生成方式（shell）

- 例c多文件编译

  - ```makefile
    hellomake: hellomake.c hellofunc.c
         gcc -o hellomake hellomake.c hellofunc.c -I.
    
    clean:
    	rm -f *.o hellomake
    ```

  - 使用宏抽象

  - ```makefile
    # 定义编译器
    CC=gcc
    
    # 定义编译选项，-I. 表示在当前目录下查找头文件
    CFLAGS=-I.
    
    # 定义依赖的头文件
    DEPS = hellomake.h
    
    # 定义生成的对象文件
    OBJ = hellomake.o hellofunc.o 
    
    # 为每个 .c 文件定义一个规则，生成对应的 .o 文件
    # $@ 代表目标文件，$< 代表第一个依赖文件
    %.o: %.c $(DEPS)
    	$(CC) -c -o $@ $< $(CFLAGS)
    
    # 链接对象文件生成最终的可执行文件
    # $^ 代表所有的依赖文件
    hellomake: $(OBJ)
    	$(CC) -o $@ $^ $(CFLAGS)
    
    # 定义一个伪目标 clean，用于清理生成的文件
    # .PHONY 表示 clean 是一个伪目标
    .PHONY: clean
    clean:
    	rm -f *.o hellomake
    ```

- 