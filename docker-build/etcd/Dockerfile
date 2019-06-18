# Copyright 2019 Nokia
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM alpine:3.9
MAINTAINER Krisztian Lengyel <krisztian.lengyel@nokia.com>

ARG ETCD_VERSION
ENV GOPATH /build

COPY get-package build-pip /usr/bin/
COPY source-list.txt ${GOPATH}/


COPY rootfs/ /

RUN apk add py-pip \
&&  pip install --upgrade pip \
&&  mkdir -p ${GOPATH} \
&&  chmod 755 /usr/bin/build-pip /usr/bin/get-package \
\
&&  adduser -u 143 -D -H -s /sbin/nologin caas_etcd \
&&  chmod +x /etcdctl3 \
&&  chmod 644 ${GOPATH}/source-list.txt \
\
&&  apk add --no-cache --virtual .build-deps go godep curl tar build-base py-setuptools python2-dev \
&&  apk add --no-cache python2 py2-ipaddress py2-dnspython \
\
&&  build-pip python-urllib3 python-etcd \
\
&&  mkdir -p ${GOPATH}/src/github.com/coreos/etcd \
&&  curl -fsSL -k https://github.com/coreos/etcd/archive/v${ETCD_VERSION}.tar.gz | tar zx --strip-components=1 -C ${GOPATH}/src/github.com/coreos/etcd \
&&  cd ${GOPATH}/src/github.com/coreos/etcd \
&&  GOOS=linux ./build \
&&  mv bin/etcd /usr/bin/ \
&&  mv bin/etcdctl /usr/bin/ \
&&  mv /etcdctl3 /usr/bin/ \
\
&&  apk del .build-deps \
&&  rm -rf ${GOPATH} \
&&  rm -rf /build


WORKDIR /