FROM fedora
ENV GOPATH=/go
ENV PATH=$PATH:/go/bin
RUN dnf install -y git make gcc gcc-c++ which iproute iputils procps-ng vim-minimal tmux net-tools htop tar jq npm openssl-devel perl
ADD https://golang.org/dl/go1.15.6.linux-amd64.tar.gz /go/
RUN cd /go && tar -xvf * && mv /go/**/ /usr/local/
# only required for deployment script
RUN npm install -g ts-node && npm install -g typescript
ADD https://gethstore.blob.core.windows.net/builds/geth-linux-amd64-1.9.14-6d74d1e5.tar.gz /geth/
RUN cd /geth && tar -xvf * && mv /geth/**/geth /usr/bin/geth
# rust for the testing suite and the relayers / validator daemons
RUN curl https://sh.rustup.rs -sSf | sh -s -- -y
# the actual source code for this repo, this **only** includes checked in files!
# this is a bit of a pain but it does speed things up a lot
ADD peggy.tar.gz /
# this config file allows enablement of the legacy Amino api
ADD legacy-api-enable /
# build steps for all codebases in this repo, must be below the add statement
RUN pushd /peggy/orchestrator/ && PATH=$PATH:$HOME/.cargo/bin cargo build --all --release
RUN pushd /peggy/module/ && PATH=$PATH:/usr/local/go/bin GOPROXY=https://proxy.golang.org make && PATH=$PATH:/usr/local/go/bin make install
RUN pushd /peggy/solidity/ && npm install
