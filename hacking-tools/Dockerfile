FROM ubuntu:22.04

ENV TZ=Europe/Zurich
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update && apt-get upgrade -y && apt-get install \
    openvpn nmap dirb sqlmap hashcat hydra netcat \
    git build-essential libssl-dev zlib1g-dev yasm pkg-config libgmp-dev libpcap-dev libbz2-dev \ 
    zsh zsh-syntax-highlighting zsh-autosuggestions \
    git pip python3-virtualenv \
    curl wget unzip \
    vim vim-addon-manager \
    openssh-server net-tools \
    gcc gdb \
    language-pack-en \
    smbclient net-tools dnsutils \
    sudo -y
RUN git clone https://github.com/3ndG4me/KaliLists /usr/share/wordlists
RUN git clone https://gitlab.com/kalilinux/packages/webshells /usr/share/webshells
RUN git clone https://github.com/offensive-security/exploit-database.git /usr/share/exploit-database
RUN ln -s /usr/share/exploit-database/searchsploit /usr/sbin/searchsploit
RUN git clone https://github.com/openwall/john -b bleeding-jumbo /usr/src/john
RUN cd /usr/src/john/src && ./configure && make -s clean && make -sj4
RUN git clone https://github.com/carlospolop/PEASS-ng /usr/share/PEASS-ng
RUN git clone https://github.com/CiscoCXSecurity/enum4linux /usr/share/enum4linux
RUN sed -i 's#which $prog 2>&1#which $prog 2>/dev/null#g' /usr/share/enum4linux/enum4linux.pl
RUN pip install git+https://github.com/calebstewart/pwncat.git


RUN update-locale
RUN useradd -rm -d /home/hacker -G sudo -u 1001 hacker
RUN echo 'hacker:hacker' | chpasswd
USER hacker
RUN alias pwncat="python3 -m pwncat"
WORKDIR /home/hacker
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" \
    && sed -i 's/\(ZSH_THEME=\).\+/\1"agnoster"/g' ~/.zshrc
RUN echo "source /usr/share/zsh-autosuggestions/zsh-autosuggestions.zsh" >> ~/.zshrc
RUN echo "source /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ~/.zshrc 
RUN echo "set nu" > ~/.vimrc
CMD tail -f /dev/null