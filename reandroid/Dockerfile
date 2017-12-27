FROM ubuntu:16.04
MAINTAINER bla5r

# Setting env variables
ENV ANDROID_NDK_VERSION "r16b"
ENV ANDROID_SDK_VERSION "3859397"
ENV SMALI_VERSION "2.2.2"
ENV APKTOOL_VERSION "2.3.1"
ENV FRIDA_VERSION "10.6.28"

# Installing base packages
RUN apt update
RUN apt-get install -yqq \
	default-jdk \
	software-properties-common \
	unzip \
	zip \
	wget \
	git \
	androguard \
	build-essential \
	vim \
	nano \
	iptables \
	iputils-ping \
	python-protobuf \
	python-pip \
	python-crypto \
	python3-pip \
	protobuf-compiler \
	libprotobuf-java \
	ssh  \
	telnet \
	gdb-multiarch \
	nodejs \
	npm \
	tree \
	python3
RUN pip2 install --upgrade pip
RUN pip3 install --upgrade pip

# Creating working directory
RUN mkdir -p /opt

# Installing Android NDK
RUN wget -q -O "/opt/android-ndk-$ANDROID_NDK_VERSION-linux-x86-64.zip" "https://dl.google.com/android/repository/android-ndk-$ANDROID_NDK_VERSION-linux-x86_64.zip" && cd "/opt" && unzip "/opt/android-ndk-$ANDROID_NDK_VERSION-linux-x86-64.zip" && rm -f "/opt/android-ndk-$ANDROID_NDK_VERSION-linux-x86-64.zip"
RUN mv "/opt/android-ndk-$ANDROID_NDK_VERSION" "/opt/android-ndk-linux"
ENV NDK "/opt/android-ndk-$ANDROID_NDK_VERSION"

# Installing Android SDK
RUN wget -q -O "/opt/tools-linux.zip" "https://dl.google.com/android/repository/sdk-tools-linux-$ANDROID_SDK_VERSION.zip"
RUN unzip "/opt/tools-linux.zip" -d "/opt/android-sdk-linux"
RUN rm -f "/opt/tools-linux.zip"
RUN echo y | /opt/android-sdk-linux/tools/bin/sdkmanager "platform-tools"
ENV ANDROID_HOME "/opt/android-sdk-linux"
ENV PATH "$PATH:/opt:$ANDROID_HOME/tools:$ANDROID_HOME/bin:$ANDROID_HOME/platform-tools"

# Installing Smali/Baksmali
RUN mkdir -p "/opt/smali"
RUN wget -q -O "/opt/smali/smali.jar" "https://bitbucket.org/JesusFreke/smali/downloads/smali-$SMALI_VERSION.jar"
RUN wget -q -O "/opt/smali/baksmali.jar" "https://bitbucket.org/JesusFreke/smali/downloads/baksmali-$SMALI_VERSION.jar"

# Installing apktool
RUN mkdir -p "/opt/apktool"
RUN wget -q -O "/opt/apktool/apktool" "https://raw.githubusercontent.com/iBotPeaches/Apktool/master/scripts/linux/apktool"
RUN wget -q -O "/opt/apktool/apktool.jar" "https://bitbucket.org/iBotPeaches/apktool/downloads/apktool_$APKTOOL_VERSION.jar"
RUN chmod u+x /opt/apktool/apktool /opt/apktool/apktool.jar
ENV PATH $PATH:/opt/apktool

# Installing Frida
RUN mkdir -p "/opt/frida"
RUN pip install frida
RUN cd /opt && wget -q -O "/opt/frida/frida-server.xz" "https://github.com/frida/frida/releases/download/$FRIDA_VERSION/frida-server-$FRIDA_VERSION-android-arm.xz" && unxz "/opt/frida/frida-server.xz"

# Installing APKiD
RUN cd "/opt" && git clone "https://github.com/rednaga/APKiD" "apkid"
RUN cd "/opt/apkid" && git clone "https://github.com/rednaga/yara-python"
RUN cd "/opt/apkid/yara-python" && python setup.py install
RUN cd "/opt/apkid" && pip2 install apkid

# Installing r2
RUN cd "/opt" && git clone https://github.com/radare/radare2
RUN cd "/opt/radare2" && sys/install.sh && make symstall && r2pm init && pip install r2pipe

# Installing GEF
RUN pip install capstone unicorn keystone-engine ropper retdec-python
RUN wget -q -O- "https://github.com/hugsy/gef/raw/master/gef.sh" | sh
## Removing unicode
RUN sed -i 's/left_arrow = " \\u2190 "/left_arrow = "<-"/g' /root/.gdbinit-gef.py
RUN sed -i 's/right_arrow = " \\u2192 "/right_arrow = "->"/g' /root/.gdbinit-gef.py
RUN sed -i 's/down_arrow = "\\u21b3"/down_arrow = "\\\\->"/g' /root/.gdbinit-gef.py
RUN sed -i 's/horizontal_line = "\\u2500"/horizontal_line = "-"/g' /root/.gdbinit-gef.py
RUN sed -i 's/vertical_line = "\\u2502"/vertical_line = "|"/g' /root/.gdbinit-gef.py
RUN sed -i 's/cross = "\\u2718 "/cross = "x"/g' /root/.gdbinit-gef.py
RUN sed -i 's/tick = "\\u2713 "/tick = "v"/g' /root/.gdbinit-gef.py
RUN sed -i 's/gef_prompt = "gef\\u27a4  "/gef_prompt = "gef> "/g' /root/.gdbinit-gef.py

# Setting aliases
RUN echo "alias gdb-android='/opt/android-ndk-linux/prebuilt/linux-x86_64/bin/gdb'" >> "/root/.bashrc"
RUN echo "alias smali='java -jar smali.jar'" >> "/root/.bashrc"
RUN echo "alias baksmali='java -jar baksmali.jar'" >> "/root/.bashrc"

# Setting working directory
WORKDIR "/opt"
