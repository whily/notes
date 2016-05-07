Installation notes for using GoGui with Pachi (DCNN enabled) on OS X.

### Install Caffe

Following the official installation guide:
http://caffe.berkeleyvision.org/install_osx.html

Firstly installing the dependencies:

    $ brew install -vd snappy leveldb gflags glog szip lmdb
    $ brew tap homebrew/science
    $ brew install hdf5 opencv
    $ brew install protobuf boost
    $ brew install openblas

Now download Caffe from github and compile it:

    $ cp Makefile.config.example Makefile.config
    # Adjust Makefile.config, see details below.
    $ make all
    $ make test
    $ make runtest
    # Following is to copy generated file needed to compile Pachi.
    $ cp -r build/src/caffe/proto include/caffe
    # Following is to copy library files required to run Pachi.
    $ cp -a build/lib/. /usr/local/lib/

In above adjustment for Makefile.config, following changes are needed:

1. Uncomment CPU_ONLY := 1 for CPU-only Caffe.
2. As we use OpenBlas, set it accordingly: BLAS := open,
   and uncomment BLAS_INCLUDE and BLAS_LIB lines related to OpenBlas.
3. Uncomment USE_OPENCV := 0 as there is some link issue.

### Install Pachi

Download Pachi from github. Modify Makefile:

1. Uncomment MAC=1.
2. Uncomment DCNN=1.
3. Uncomment and set CAFFE_LIB=/your/path/to/caffe/build/lib
   As there is some issue for Python.h when running make distribute in Caffe,
   the intermediate path is used directly.
4. Set include directories by modify the line starting with INCLUDES to
   INCLUDES=-I. -I/Users/whily/pkg/caffe.git/include -I/usr/local/include -I/usr/local/opt/openblas/include
5. Add lib path (for boost) after INCLUDES line above.
   SYS_LDFLAGS+=-L/usr/local/lib
6. Download DCNN file http://physik.de/CNNlast.tar.gz, and extract to Pachi's directory.

### Install GoGui

1. Just download it. In lib directory, run `java -jar gogui.jar` to start the GUI.

2. In dialog `Program | New Program`
   * For `Command`, supply the path to pachi.
     I set following parameters for Pachi: `-t=1200 threads=2,maximize_score`,
     with `maximize_score` making Pachi behaving more like human.
   * For `Working directory`, point to the directory containing Pachi.

Finally, enjoy a new game with DCNN enabled Pachi.
