# Opencv 第一个demo
> create by [afterloe](605728727@qq.com)  
> version is 1.0  
> MIT License  

## 创建git仓库
```
mkdir -p ~/Project/opencv_demo
cd ~/Project/opencv_demo
git init .

vim .gitignore

tmp/
CMakeFiles/


mkdir src tmp
```

## 编写demo源码
```
cd src
touch DisplayImage.cpp
vim DisplayImage.cpp
```

```cpp
#include <stdio.h>
#include <opencv2/opencv.hpp>

using namespace cv;

int main(int argc, char** argv) {
        if (2 != argc) {
                std::cout << "usage: DisplayImage.out <Image_Path>" << std::endl;
                return -1;
        }

        Mat image;
        image = imread(argv[1], 1);

        if (!image.data) {
                std::cout << "No image data" << std::endl;
        }

        namedWindow("Display Image", WINDOW_AUTOSIZE);
        imshow("Display Image", image);

        waitKey(0);

        return 0;
}
```

## 编写CMake文件
```
cd ../
touch CMakeLists.txt
vim CMakeLists.txt
```

```
cmake_minimum_required(VERSION 2.8)
project( DisplayImage )
find_package( OpenCV REQUIRED )
include_directories( ${OpenCV_INCLUDE_DIRS} )
add_executable( DisplayImage src/DisplayImage.cpp )
target_link_libraries( DisplayImage ${OpenCV_LIBS} )
```

## 编译
```
cmake .
make
```

## 测试
```
cd ~/Project/opencv_demo/tmp
wget http://pics0.baidu.com/feed/b58f8c5494eef01f3307b5ef891d1220bd317df5.jpeg?token=6083cfdc61f044b7b92254e67be1cdda&s=B9324F94542120B46200B498030090A3
mv 'b58f8c5494eef01f3307b5ef891d1220bd317df5.jpeg?token=6083cfdc61f044b7b92254e67be1cdda' b58.jpeg
cd ../
./DisplayImage tmp/b58.jpeg
```
