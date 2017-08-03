# AlpineLinux with a glibc-2.23 and Oracle Java 8
FROM alpine:3.6

MAINTAINER Anastas Dancha <anapsix@random.io>
# thanks to Vladimir Krivosheev <develar@gmail.com> aka @develar for smaller image
# and Victor Palma <palma.victor@gmail.com> aka @devx for pointing it out

# Java Version and other ENV
ENV JAVA_VERSION_MAJOR=8 \
    JAVA_VERSION_MINOR=144 \
    JAVA_VERSION_BUILD=01 \
    JAVA_PACKAGE=jdk \
    JAVA_JCE=unlimited \
    JAVA_HOME=/opt/jdk \
    PATH=${PATH}:/opt/jdk/bin \
    GLIBC_VERSION=2.23-r4 \
    LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    TZ=Asia/Shanghai \
    IS_IN_CONTAINER=true


# install java
RUN set -ex && \
    apk upgrade --update && \
    apk add --update libstdc++ curl ca-certificates bash && \
    for pkg in glibc-${GLIBC_VERSION} glibc-bin-${GLIBC_VERSION} glibc-i18n-${GLIBC_VERSION}; do curl -sSL https://github.com/andyshinn/alpine-pkg-glibc/releases/download/${GLIBC_VERSION}/${pkg}.apk -o /tmp/${pkg}.apk; done && \
    apk add --allow-untrusted /tmp/*.apk && \
    ( /usr/glibc-compat/bin/localedef --force --inputfile POSIX --charmap UTF-8 C.UTF-8 || true ) && \
    echo "export LANG=C.UTF-8" > /etc/profile.d/locale.sh && \
    /usr/glibc-compat/sbin/ldconfig /lib /usr/glibc-compat/lib && \
    mkdir /opt && \
    curl -jksSLH "Cookie: oraclelicense=accept-securebackup-cookie" -o /tmp/java.tar.gz \
      http://download.oracle.com/otn-pub/java/jdk/${JAVA_VERSION_MAJOR}u${JAVA_VERSION_MINOR}-b${JAVA_VERSION_BUILD}/090f390dda5b47b9b721c7dfaa008135/${JAVA_PACKAGE}-${JAVA_VERSION_MAJOR}u${JAVA_VERSION_MINOR}-linux-x64.tar.gz && \
    gunzip /tmp/java.tar.gz && \
    tar -C /opt -xf /tmp/java.tar && \
    ln -s /opt/jdk1.${JAVA_VERSION_MAJOR}.0_${JAVA_VERSION_MINOR} /opt/jdk && \
    if [ "${JAVA_JCE}" == "unlimited" ]; then echo "Installing Unlimited JCE policy" >&2 && \
      curl -jksSLH "Cookie: oraclelicense=accept-securebackup-cookie" -o /tmp/jce_policy-${JAVA_VERSION_MAJOR}.zip \
        http://download.oracle.com/otn-pub/java/jce/${JAVA_VERSION_MAJOR}/jce_policy-${JAVA_VERSION_MAJOR}.zip && \
      cd /tmp && unzip /tmp/jce_policy-${JAVA_VERSION_MAJOR}.zip && \
      cp -v /tmp/UnlimitedJCEPolicyJDK8/*.jar /opt/jdk/jre/lib/security; \
    fi && \
    sed -i s/#networkaddress.cache.ttl=-1/networkaddress.cache.ttl=10/ $JAVA_HOME/jre/lib/security/java.security && \
    apk del curl glibc-i18n && \
    rm -rf /opt/jdk/*src.zip \
           /opt/jdk/lib/missioncontrol \
           /opt/jdk/lib/visualvm \
           /opt/jdk/lib/*javafx* \
           /opt/jdk/jre/plugin \
           /opt/jdk/jre/bin/javaws \
           /opt/jdk/jre/bin/jjs \
           /opt/jdk/jre/bin/orbd \
           /opt/jdk/jre/bin/pack200 \
           /opt/jdk/jre/bin/policytool \
           /opt/jdk/jre/bin/rmid \
           /opt/jdk/jre/bin/rmiregistry \
           /opt/jdk/jre/bin/servertool \
           /opt/jdk/jre/bin/tnameserv \
           /opt/jdk/jre/bin/unpack200 \
           /opt/jdk/jre/lib/javaws.jar \
           /opt/jdk/jre/lib/deploy* \
           /opt/jdk/jre/lib/desktop \
           /opt/jdk/jre/lib/*javafx* \
           /opt/jdk/jre/lib/*jfx* \
           /opt/jdk/jre/lib/amd64/libdecora_sse.so \
           /opt/jdk/jre/lib/amd64/libprism_*.so \
           /opt/jdk/jre/lib/amd64/libfxplugins.so \
           /opt/jdk/jre/lib/amd64/libglass.so \
           /opt/jdk/jre/lib/amd64/libgstreamer-lite.so \
           /opt/jdk/jre/lib/amd64/libjavafx*.so \
           /opt/jdk/jre/lib/amd64/libjfx*.so \
           /opt/jdk/jre/lib/ext/jfxrt.jar \
           /opt/jdk/jre/lib/ext/nashorn.jar \
           /opt/jdk/jre/lib/oblique-fonts \
           /opt/jdk/jre/lib/plugin.jar \
           && \
    echo "---------- Custom changes ----------" && \
    cd /tmp && \
    apk add curl shadow sudo coreutils procps wget python2 grep openssh-client nethogs sysstat && \
    echo "export LC_ALL=C.UTF-8" >> /etc/profile.d/locale.sh && \
    echo "export JAVA_HOME=/opt/jdk" >> /etc/profile.d/java.sh && \
    echo "export PATH=/opt/jdk/bin:$PATH" >> /etc/profile.d/java.sh && \
    echo "Defaults        env_keep=\"PATH JAVA_HOME LANG LC_ALL\"" >> /etc/sudoers.d/env_keep && \
    echo "---------- Change timezone to Shanghai ----------" && \
    apk add tzdata && \
    ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \
    echo "Asia/Shanghai" > /etc/timezone && \
    echo "---------- Add chinese fonts ----------" && \
    mkdir -p /usr/share/fonts && \
    curl -sSL https://raw.githubusercontent.com/Sartner/docker-alpine-java/master/fonts/cjkuni-ukai.tar.gz -o /tmp/cjkuni-ukai.tar.gz && \
    tar -zxvf /tmp/cjkuni-ukai.tar.gz -C /usr/share/fonts/ && rm -rf /tmp/cjkuni-ukai.tar.gz && \
    curl -sSL https://raw.githubusercontent.com/Sartner/docker-alpine-java/master/fonts/cjkuni-uming.tar.gz -o /tmp/cjkuni-uming.tar.gz && \
    tar -zxvf /tmp/cjkuni-uming.tar.gz -C /usr/share/fonts/ && rm -rf /tmp/cjkuni-uming.tar.gz && \
    apk add fontconfig mkfontscale mkfontdir && \
    mkfontscale && mkfontdir && fc-cache && \
    echo "---------- Add custom user ----------" && \
    useradd -mU ocean -s /sbin/nologin && \
    echo "ocean ALL=(ALL) NOPASSWD: /usr/sbin/nethogs" >> /etc/sudoers.d/ocean && \
    echo "---------- Change apk repo to aliyun ----------" && \
    echo "http://mirrors.aliyun.com/alpine/v3.6/main" > /etc/apk/repositories && \
    echo "http://mirrors.aliyun.com/alpine/v3.6/community" >> /etc/apk/repositories && \
    echo "---------- add profile ----------" && \
    touch /etc/profile.d/custom.sh && \
    echo "alias ll='ls -lrtFh --color=auto'" >> /etc/profile.d/custom.sh && \
    echo "export EDITOR=vim" >> /etc/profile.d/custom.sh && \
    echo "export TERM=xterm-color" >> /etc/profile.d/custom.sh && \
    echo "WHITE=\"\e[1;37m\";GREEN=\"\e[1;32m\";YELLOW=\"\e[1;33m\";BULE=\"\e[1;36m\";ORANGE_BG=\"\e[1;43m\";END=\"\e[m\";" >> /etc/profile.d/custom.sh && \
    echo "export PS1=\"\n\${WHITE}[\${END} (In-Container) \${GREEN}\u\${END}\${YELLOW}@\${END}\${BULE}\h\${END} \w \${WHITE}]\${END}\n\\$ \"" >> /etc/profile.d/custom.sh && \
    echo "---------- delete tmp files ----------" && \
    rm -rf -v /tmp/* /var/cache/apk/*

# EOF
