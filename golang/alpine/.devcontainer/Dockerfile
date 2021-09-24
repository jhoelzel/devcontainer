ARG GO_VERSION=1.17
ARG ALPINE_VERSION=3.14

#Get official alpine image
FROM golang:${GO_VERSION}-alpine${ALPINE_VERSION}
ARG TIMEZONE=Europe/Berlin
ARG LOCALE=en_US.UTF-8
ARG ARCH=amd64
ARG OS=linux
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=1000
ENV OHMYZSH_URL="https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh"
ENV AUTOCOMPLETEZSH_URL="https://github.com/marlonrichert/zsh-autocomplete.git"
#Timezone
ENV TZ=$TIMEZONE
RUN apk update
RUN apk add tzdata
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
ENV LANG $LOCALE
ENV LANGUAGE $LOCALE
ENV LC_ALL $LOCALE
ENV EDITOR=nano
RUN apk del tzdata

#Install packages and Go language server bash  zsh Make
RUN apk add -q --update --progress --no-cache sudo openssh-client bash zsh make docker-cli
#GIT & nano
RUN apk add curl git nano

#install golang dev req
RUN apk add gcc libc-dev musl-dev

#Setup user
RUN addgroup -S docker
RUN adduser $USERNAME -s /bin/sh -D -u $USER_UID $USER_GID --ingroup "docker"
RUN mkdir -p /etc/sudoers.d
RUN echo "$USERNAME ALL=(root) NOPASSWD:ALL" > /etc/sudoers.d/$USERNAME
RUN chmod 0440 /etc/sudoers.d/$USERNAME
USER $USERNAME
RUN mkdir /home/$USERNAME/.ssh
USER root

USER $USERNAME
#Intall go dev Modules /go/bin in module mode for vs code.
RUN go get -v github.com/uudashr/gopkgs/v2/cmd/gopkgs@latest
RUN go get -v github.com/ramya-rao-a/go-outline@latest
RUN go get -v github.com/cweill/gotests/gotests@latest
RUN go get -v github.com/fatih/gomodifytags@latest
RUN go get -v github.com/josharian/impl@latest
RUN go get -v github.com/haya14busa/goplay/cmd/goplay@latest
RUN go get -v github.com/go-delve/delve/cmd/dlv@latest
#RUN go get -v dlv-dap@latest //dlv-dap is not @latest compatible... vs code will install it for us once we start
RUN go get -v honnef.co/go/tools/cmd/staticcheck@latest
RUN go get -v golang.org/x/tools/gopls@latest
RUN go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
RUN go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
# Go Micro related go packages
RUN go install github.com/asim/go-micro/cmd/protoc-gen-micro/v3@latest

USER $USERNAME
#Install oh my zsh for themes
RUN sh -c "$(wget -O- $OHMYZSH_URL)" "" --unattended &> /dev/null
#Autocomplete for zsh
RUN git clone $AUTOCOMPLETEZSH_URL /home/$USERNAME/.oh-my-zsh/custom/plugins/zsh-autocomplete
ENV ENV="/home/$USERNAME/.ashrc"
RUN ZSH=/home/$USERNAME/.oh-my-zsh
#zsh config
RUN echo 'autoload -Uz compinit' >>~/.zshrc
RUN echo 'compinit' >> ~/.zshrc
RUN printf 'ZSH_THEME="robbyrussell"\nENABLE_CORRECTION="false"\nplugins=(git copyfile extract colorize dotenv encode64 golang helm timer kubectl kubectx zsh-autocomplete )\nsource /home/$USERNAME/.oh-my-zsh/oh-my-zsh.sh\n' > "/home/$USERNAME/.zshrc"
RUN echo "exec which zsh" > "/home/$USERNAME/.ashrc"

#extra key support
RUN printf 'bindkey  "^[[H"   beginning-of-line\n' >>~/.zshrc
RUN printf 'bindkey  "^[[F"   end-of-line\n' >>~/.zshrc
RUN printf 'bindkey  "^[[3~"  delete-char\n' >>~/.zshrc

USER root
# Make ZSH the default shell for the current user in the container
# To check that the shell was indeed added: `chsh -l` and you should see it in the  list.
ENV SHELL=/bin/zsh
ENTRYPOINT ["zsh"]