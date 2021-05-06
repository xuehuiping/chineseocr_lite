- 这个仓库有3个分支。默认是onnx，还有一个是master
- 测试结果又一部分是onnx分支，有一部分是master分支



使用master分支

`git checkout master`

## 情况
`.so`  文件编译失败

原因是`Makefile`文件编写脚本有误。

## 原始Makefile文件

```
CXXFLAGS = -I include  -std=c++11 -O3 $(shell python3-config --cflags)
LDFLAGS = $(shell python3-config --ldflags)

DEPS = $(shell find include -xtype f)
CXX_SOURCES = pse.cpp

LIB_SO = pse.so

$(LIB_SO): $(CXX_SOURCES) $(DEPS)
	$(CXX) -o $@ $(CXXFLAGS) $(LDFLAGS) $(CXX_SOURCES) --shared -fPIC

clean:
	rm -rf $(LIB_SO)
```

## 报错
运行`python app.py  8080`，报错堆栈：

```
make: Entering directory '/home/xuehp/git/chineseocr_lite/psenet/pse'
g++ -o pse.so -I include  -std=c++11 -O3 -I/home/xuehp/anaconda3/envs/chineseocr_lite/include/python3.6m -I/home/xuehp/anaconda3/envs/chineseocr_lite/include/python3.6m  -Wno-unused-result -Wsign-compare -march=nocona -mtune=haswell -ftree-vectorize -fPIC -fstack-protector-strong -fno-plt -O3 -ffunction-sections -pipe -isystem /home/xuehp/anaconda3/envs/chineseocr_lite/include -fdebug-prefix-map=/tmp/build/80754af9/python_1614113050744/work=/usr/local/src/conda/python-3.6.13 -fdebug-prefix-map=/home/xuehp/anaconda3/envs/chineseocr_lite=/usr/local/src/conda-prefix -fuse-linker-plugin -ffat-lto-objects -flto-partition=none -flto -DNDEBUG -fwrapv -O3 -Wall -L/home/xuehp/anaconda3/envs/chineseocr_lite/lib/python3.6/config-3.6m-x86_64-linux-gnu -L/home/xuehp/anaconda3/envs/chineseocr_lite/lib -lpython3.6m -lpthread -ldl  -lutil -lrt -lm  -Xlinker -export-dynamic pse.cpp --shared -fPIC
g++: error: unrecognized command line option ‘-fno-plt’
Makefile:10: recipe for target 'pse.so' failed
make: *** [pse.so] Error 1
make: Leaving directory '/home/xuehp/git/chineseocr_lite/psenet/pse'
Traceback (most recent call last):
  File "app.py", line 12, in <module>
    from model import text_predict, crnn_handle
  File "/home/xuehp/git/chineseocr_lite/model.py", line 3, in <module>
    from psenet import PSENet, PSENetHandel
  File "/home/xuehp/git/chineseocr_lite/psenet/__init__.py", line 2, in <module>
    from .PSENET import PSENetHandel
  File "/home/xuehp/git/chineseocr_lite/psenet/PSENET.py", line 8, in <module>
    from .pse import decode as pse_decode
  File "/home/xuehp/git/chineseocr_lite/psenet/pse/__init__.py", line 10, in <module>
    raise RuntimeError('Cannot compile pse: {}'.format(BASE_DIR))
RuntimeError: Cannot compile pse: /home/xuehp/git/chineseocr_lite/psenet/pse
```

不认识参数`-fno-plt`

## 办法
看一下命令`python3-config --cflags`的具体参数，这个因机器而异

```
(chineseocr_lite) xuehp@haomeiya007:~/git/chineseocr_lite/psenet/pse$ python3-config --cflags
-I/home/xuehp/anaconda3/envs/chineseocr_lite/include/python3.6m -I/home/xuehp/anaconda3/envs/chineseocr_lite/include/python3.6m  -Wno-unused-result -Wsign-compare -march=nocona -mtune=haswell -ftree-vectorize -fPIC -fstack-protector-strong -fno-plt -O3 -ffunction-sections -pipe -isystem /home/xuehp/anaconda3/envs/chineseocr_lite/include -fdebug-prefix-map=/tmp/build/80754af9/python_1614113050744/work=/usr/local/src/conda/python-3.6.13 -fdebug-prefix-map=/home/xuehp/anaconda3/envs/chineseocr_lite=/usr/local/src/conda-prefix -fuse-linker-plugin -ffat-lto-objects -flto-partition=none -flto -DNDEBUG -fwrapv -O3 -Wall
```
确实有这个参数

删除这个参数，即替换`python3-config --cflags`命令。

编辑文件`Makefile`。编辑之后的文件见文件目录

```
(chineseocr_lite) xuehp@haomeiya007:~/git/chineseocr_lite/psenet/pse$ vi Makefile 
```
再次make
```
(chineseocr_lite) xuehp@haomeiya007:~/git/chineseocr_lite/psenet/pse$ make
g++ -o pse.so -I include  -std=c++11 -O3 -I/home/xuehp/anaconda3/envs/chineseocr_lite/include/python3.6m -I/home/xuehp/anaconda3/envs/chineseocr_lite/include/python3.6m  -Wno-unused-result -Wsign-compare -march=nocona -mtune=haswell -ftree-vectorize -fPIC -fstack-protector-strong -O3 -ffunction-sections -pipe -isystem /home/xuehp/anaconda3/envs/chineseocr_lite/include -fdebug-prefix-map=/tmp/build/80754af9/python_1614113050744/work=/usr/local/src/conda/python-3.6.13 -fdebug-prefix-map=/home/xuehp/anaconda3/envs/chineseocr_lite=/usr/local/src/conda-prefix -fuse-linker-plugin -ffat-lto-objects -flto-partition=none -flto -DNDEBUG -fwrapv -O3 -Wall  -L/home/xuehp/anaconda3/envs/chineseocr_lite/lib/python3.6/config-3.6m-x86_64-linux-gnu -L/home/xuehp/anaconda3/envs/chineseocr_lite/lib -lpython3.6m -lpthread -ldl  -lutil -lrt -lm  -Xlinker -export-dynamic pse.cpp --shared -fPIC
pse.cpp: In function ‘std::vector<std::vector<int> > pse::pse(pybind11::array_t<int, 1>, pybind11::array_t<unsigned char, 1>, int)’:
pse.cpp:32:29: warning: comparison between signed and unsigned integer expressions [-Wsign-compare]
         for (size_t i = 0; i<h; i++)
                             ^
pse.cpp:39:29: warning: comparison between signed and unsigned integer expressions [-Wsign-compare]
         for (size_t i = 0; i<h; i++)
                             ^
pse.cpp:42:32: warning: comparison between signed and unsigned integer expressions [-Wsign-compare]
             for(size_t j = 0; j<w; j++)
                                ^
(chineseocr_lite) xuehp@haomeiya007:~/git/chineseocr_lite/psenet/pse$ ls
include  __init__.py  Makefile	ncnn  pse35.pyd  pse36.pyd  pse.cpp  pse.so  __pycache__
```
.so文件已生成

## 启动服务

```
(chineseocr_lite) xuehp@haomeiya007:~/git/chineseocr_lite/psenet/pse$ cd ../../


(chineseocr_lite) xuehp@haomeiya007:~/git/chineseocr_lite$ python app.py  8080
```

