FROM alpine
RUN apk add --no-cache libffi-dev
RUN apk add --no-cache $(echo $(wget --no-check-certificate -qO- https://raw.githubusercontent.com/ethan522/nas-tools/master/package_list.txt))
RUN ln -sf /usr/share/zoneinfo/${TZ} /etc/localtime
RUN echo "${TZ}" > /etc/timezone
RUN ln -sf /usr/bin/python3 /usr/bin/python
RUN curl https://rclone.org/install.sh | bash
RUN if [ "$(uname -m)" = "x86_64" ]; then ARCH=amd64; elif [ "$(uname -m)" = "aarch64" ]; then ARCH=arm64; fi
RUN curl https://dl.min.io/client/mc/release/linux-${ARCH}/mc --create-dirs -o /usr/bin/mc
RUN chmod +x /usr/bin/mc
RUN pip install --upgrade pip setuptools wheel
RUN pip install cython
RUN pip install -r https://raw.githubusercontent.com/ethan522/nas-tools/master/requirements.txt
RUN apk del libffi-dev
RUN npm install pm2 -g
RUN rm -rf /tmp/* /root/.cache /var/cache/apk/*
ENV LANG="C.UTF-8" \
    TZ="Asia/Shanghai" \
    NASTOOL_CONFIG="/config/config.yaml" \
    NASTOOL_AUTO_UPDATE=true \
    NASTOOL_CN_UPDATE=true \
    NASTOOL_VERSION=v2.9.2 \
    PS1="\u@\h:\w \$ " \
    REPO_URL="https://github.com/ethan522/nastools.git" \
    PYPI_MIRROR="https://pypi.tuna.tsinghua.edu.cn/simple" \
    ALPINE_MIRROR="mirrors.ustc.edu.cn" \
    PUID=0 \
    PGID=0 \
    UMASK=000 \
    WORKDIR="/nas-tools"
WORKDIR ${WORKDIR}
RUN python_ver=$(python3 -V | awk '{print $2}')
RUN echo "${WORKDIR}/" > /usr/lib/python${python_ver%.*}/site-packages/nas-tools.pth
RUN echo 'fs.inotify.max_user_watches=524288' >> /etc/sysctl.conf
RUN echo 'fs.inotify.max_user_instances=524288' >> /etc/sysctl.conf
RUN git config --global pull.ff only
RUN git clone -b v2.9.2 ${REPO_URL} ${WORKDIR} --depth=1 --recurse-submodule
RUN git config --global --add safe.directory ${WORKDIR}
EXPOSE 3000
VOLUME ["/config"]
ENTRYPOINT ["/nas-tools/docker/entrypoint.sh"]