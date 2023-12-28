## Clone this repo in home directory
```
git clone --recurse-submodules git@github.com:JoongunPark/IARPA-sst.git sst
```

## Build SST
* Install prerequisites
```
sudo apt -y install mpich python3-pip python3 python
```

* If you have to install them libraries locally, please follow the following instructions
```
cd ~/
mkdir -p local/bin

wget https://github.com/Kitware/CMake/releases/download/v3.22.2/cmake-3.22.2.tar.gz
tar -xzvf cmake-3.22.2.tar.gz
cd cmake-3.22.2
./bootstrap --prefix=$HOME/local
make all -j$(nproc)
make install

wget http://ftp.gnu.org/gnu/autoconf/autoconf-2.69.tar.gz
tar -xzvf autoconf-2.69.tar.gz
cd autoconf-2.69
./configure --prefix=$HOME/local
make all –j$(nproc) && make install

wget https://ftp.gnu.org/gnu/automake/automake-1.15.tar.gz
tar -xzvf automake-1.15.tar.gz
cd automake-1.15
./configure --prefix=$HOME/local
make all –j$(nproc) && make install

wget http://ftp.jaist.ac.jp/pub/GNU/libtool/libtool-2.4.2.tar.gz
tar -xzvf libtool-2.4.2.tar.gz
cd libtool-2.4.2
./configure --prefix=$HOME/local
make all && make install

export ACLOCAL_PATH=$HOME/local/share/aclocal:$ACLOCAL_PATH # for libtool
git clone --recurse-submodules git@github.com:protocolbuffers/protobuf.git
cd protobuf
git checkout 4.0.x
./autogen.sh
./configure --prefix=$HOME/local/
make all -j$(nproc)
make install

wget https://download.open-mpi.org/release/open-mpi/v4.1/openmpi-4.1.2.tar.gz
tar -xzvf openmpi-4.1.2.tar.gz
cd openmpi-4.1.2/
./configure --prefix=$HOME/local/
make all -j$(nproc)
make install

export PATH=$PATH:$MY_LOCAL/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$MY_LOCAL/lib
```

* Update environment variables
```
export ACLOCAL_PATH=$HOME/local/share/aclocal:$ACLOCAL_PATH # for libtool
export MY_LOCAL=$HOME/local
export PATH=$MY_LOCAL/bin:$PATH
export CPATH=$MY_LOCAL/include

export PRJ_PATH=$HOME/sst/sst-src
export SST_HOME_PATH=$HOME/sst/sst-home
export SST_CORE_HOME=$SST_HOME_PATH/sst-core
export SST_CORE_ROOT=$PRJ_PATH/sst-core
export SST_ELEMENTS_HOME=$SST_HOME_PATH/sst-elements
export SST_ELEMENTS_ROOT=$PRJ_PATH/sst-elements
export DRAMSIM3_HOME=$PRJ_PATH/DRAMSim3
export PATH=$SST_CORE_HOME/bin:$PATH
export PATH=$SST_ELEMENTS_HOME/bin:$PATH
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$SST_CORE_HOME/lib
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$SST_ELEMENTS_HOME/lib
export PYTHONPATH=$PYTHONPATH:$SST_HOME_PATH/sst-core/libexec
```

* Build SST-core
```
cd sst-src/sst-core
./autogen.sh
./configure --prefix=$SST_CORE_HOME
make all –j$(nproc)
make install -j$(nproc)
```

* Build DRAMSim3
```
cd sst-src/DRAMSim3
cmake CMakeLists.txt
make -j$(nproc)
```

* Build SST-elements
```
cd sst-src/sst-elements
./autogen.sh
./configure --prefix=$SST_ELEMENTS_HOME --with-dramsim3=$DRAMSIM3_HOME --with-sst-core=$SST_HOME_PATH/sst-core
make all –j$(nproc)
make install –j$(nproc)
```

* How to run the code
```
cd sst-src/sst-scripts/sst-scripts/arbiter/Phase1/

# Single Miranda core
cd ./Miranda/
sst withoutCache.py --model-options="--packet_count=10000"

# Multi miranda core
cd ./Miranda/
sst withoutCacheMultiCore.py --model-options="--packet_count=10000"

# Multi miranda core with failed security check
cd ./Miranda/
sst withoutCacheMultiCoreWrongAccess.py --model-options="--packet_count=10000"

# Single Vanadis core (default binary: Hello World)
cd ./Vanadis/
sst roundrobinarb_vanadis.py
```
