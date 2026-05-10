# 专为Shaft2定制的mobile-ffmpeg（已弃用）

decoder:`mjpeg`

demuxer:`mjpeg,concat,image2`

encoder:`libwebp,libwebp_anim`

muxer:`webp`

protocol:`concat,format,null,scale`

parser:`mjpeg`

filter:`file,concat`

携带`zlib`和外部库`libwebp`

## 如何食用（优雅版）
```
repositories {
    mavenCentral()
    # or
    maven { url "https://repo1.maven.org/maven2"}
}
```
```
dependencies {
    implementation("io.github.wangwang-code:mobile-ffmpeg-mini:4.4-mini")
}
```
## 如何动手加料？

### 用于编译的宿主机线程不少于16，内存不少于4GB

### 准备以下

`debian 13.3.0` 也可以是别的，但可能会坎坷些

`autoconf automake libtool pkg-config curl git cmake` 无脑apt

`java 8.0.472-tem` 推荐使用SDKMAN装

`Android SDK Command-line Tools 2.1` 什么找不到，[看看我](https://androidsdkmanager.azurewebsites.net/cmdline-tools.html)，放哪随便，以`/root/android-sdk`为例

`Android Ndk 21.3.6528147` 别问为什么要指定这么死，乱用别的编不出来就受着，应该使用Android SDK来下，下面有讲

### 准备完就克隆本仓库，然后进行以下操作

- 同意Android SDK licenses

- 为毛要同意，方便后面gradlew自动补全

`yes | /root/android-sdk/tools/bin/sdkmanager --licenses --sdk_root=/root/android-sdk`

- 如果一切正常`/root/android-sdk/`下会多出个licenses文件夹

- 下载Android Ndk

`/root/android-sdk/tools/bin/sdkmanager --install "ndk;21.3.6528147" --sdk_root=/root/android-sdk`

- 然后就是添加变量`ANDROID_HOME`和`ANDROID_NDK_ROOT`

- ANDROID_HOME指向的路径应为tools文件夹的上一级，例如`/root/android-sdk/tools`，那么应ANDROID_HOME为`/root/android-sdk/`

`export ANDROID_HOME=/root/android-sdk/`

ANDROID_NDK_ROOT向的路径应为ndk文件夹下的21.3.6528147，如`/root/android-sdk/ndk/21.3.6528147/`

`export ANDROID_NDK_ROOT=/root/android-sdk/ndk/21.3.6528147/`

### 编译编一天环节，请确保现在是在大白天，不然大概率会见到明天的太阳，确保设置好了代理，“魔法”在接下来不会爆炸、断联（准备好备用的备用

- cd 进mobile-ffmpeg，确认含有.git文件夹，如果没有，请关闭本页面，忘记这段记忆，然后刷神人视频去

- 确认android.sh可以执行，如果想直接问我android.sh执行不了怎么办，而不是问AI/搜索，请关闭本页面，忘记这段记忆，然后刷神人视频去

- 电源正常，网络正常，时间大白天，手机满电，run！

`./android.sh --disable-arm-v7a --enable-libwebp`

- 如果出现红字，看看build.log，扔给AI，祝你好运

- 如果有类以下或者有`automake-1.15`字眼的，尝试添加`--reconf-LIBRARY`

```
configure.ac:7: error: version mismatch.  This is Automake 1.15.1,
configure.ac:7: but the definition used by this AM_INIT_AUTOMAKE
configure.ac:7: comes from Automake 1.15.  You should recreate
configure.ac:7: aclocal.m4 with aclocal and run automake again.
WARNING: 'automake-1.15' is probably too old.
           You should only need it if you modified 'Makefile.am' or
           'configure.ac' or m4 files included by 'configure.ac'.
           The 'automake' program is part of the GNU Automake package:
           <http://www.gnu.org/software/automake>
           It also requires GNU Autoconf, GNU m4 and Perl in order to run:
           <http://www.gnu.org/software/autoconf>
           <http://www.gnu.org/software/m4/>
           <http://www.perl.org/>
```

### 定制化

- 打开`mobile-ffmpeg/build/android-ffmpeg.sh`翻到`./configure`

- 留意以下选项，你也只能动这几个

```
    --disable-everything \
    --enable-decoder=mjpeg \
    --enable-demuxer=mjpeg,concat,image2 \
    --enable-encoder=libwebp,libwebp_anim \
    --enable-muxer=webp \
    --enable-protocol=file,concat \
    --enable-parser=mjpeg \
    --enable-filter=concat,format,null,scale \
    --enable-zlib \
    --enable-libwebp \
```

- 你可以添加解码器/编码器/demuxer/muxer/protocol/parser/filter
