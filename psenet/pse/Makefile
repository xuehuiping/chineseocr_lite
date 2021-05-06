CXXFLAGS = -I include  -std=c++11 -O3 -I/home/chenyx/anaconda3/envs/chineseocr_lite/include/python3.6m -I/home/chenyx/anaconda3/envs/chineseocr_lite/include/python3.6m  -Wno-unused-result -Wsign-compare -march=nocona -mtune=haswell -ftree-vectorize -fPIC -fstack-protector-strong -O3 -ffunction-sections -pipe -isystem /home/chenyx/anaconda3/envs/chineseocr_lite/include -fdebug-prefix-map=/tmp/build/80754af9/python_1614113050744/work=/usr/local/src/conda/python-3.6.13 -fdebug-prefix-map=/home/chenyx/anaconda3/envs/chineseocr_lite=/usr/local/src/conda-prefix -fuse-linker-plugin -ffat-lto-objects -flto-partition=none -flto -DNDEBUG -fwrapv -O3 -Wall 
LDFLAGS = $(shell python3-config --ldflags)

DEPS = $(shell find include -xtype f)
CXX_SOURCES = pse.cpp

LIB_SO = pse.so

$(LIB_SO): $(CXX_SOURCES) $(DEPS)
	$(CXX) -o $@ $(CXXFLAGS) $(LDFLAGS) $(CXX_SOURCES) --shared -fPIC

clean:
	rm -rf $(LIB_SO)
