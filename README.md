> # Arch Linux - Guia de Instalacao

> ## O projeto:

Guia de instalação do Arch Linux, e como instalar alguns programas e utilizar a AUR.

> ### O que é Arch Linux?
Um sistema operacional altamente personalisado, minimalista e leve.

Arch Linux é uma distribuição Linux para usuários avançados que buscam montar um sistema personalizado para uso específico, pois seu resultado refle o objetivo, gosto e estilo de seu usuário.

> ## Resultado:
Utilizando Xfce:
![result](https://user-images.githubusercontent.com/59848966/169779297-30bf58f4-450f-4a76-ad42-6d4fe85b2706.png)

Utilizando I3gaps:
![result2](https://user-images.githubusercontent.com/59848966/169884671-ecb1e727-35d3-4b4f-a1a9-c889fe2fca04.png)
Fonte da imagem: https://www.reddit.com/r/unixporn/comments/lrc7bu/i3gaps_minimalist_arch_linux_rice/

> # Instalação:

> ## 0. Aviso

Neste guia alguns comandos de exemplos foram transcritos como foram utilizados no meu caso, como "sda", que é o device utilizado na minha instalação, isso pode variar de "sda" para "sdb", "sdc"... Conforme o device o qual você desja instalar o Arch Linux, outros casos semelhantes ocorrem, por isso esteja atento.

> ## 1. Preparo

Para fazer o pen drive/cd bootável você precisa ter uma imagem ISO do Arch Linux, você pode baixar a imagem ISO do Arch Linux através do link oficial: https://www.archlinux.org/download/

Monte a imagem ISO em um pen drive/cd, você pode usar um programa para isso como o "Etcher" produzido pela Balena, você pode baixá-lo através do link: https://www.balena.io/etcher/

Se sua instalação for "DUAL BOOT" lembre-se de particionar seu computador, criando e separando uma partição para que nela seja instalado o Arch linux.

Já com o pen drive/cd bootável preparado, execute-o através da opção de BOOT no menu da BIOS da sua máquina, e siga os comandos abaixo:

> ## 2. O modo de boot

Verifique o modo de inicialização: (UEFI)

    # efivar -l

Se este comando listar as variáveis EFI, isso significa que você iniciou a operação com sucesso no modo EFI. Caso contrário, reinicie no menu de BOOT novamente e selecione o item correto lá, e não o item legacy-mode.

Se o diretório não existir, o sistema pode ser inicializado no modo BIOS ou CSM.

> ## 3. Wifi

Se for usar wifi, use "# rfkill list" para ver se tudo está desbloqueado, se n estiver use " # rfkill unblock all".

Use o comando "# ip link" para ver o nome da interface wireless se não for utilizar o cabo de rede diretamente no computador/nootbook.

    # wifi-menu nome_da_interface

Entre com a senha e pronto, estará conectado, saia da interface.

> ## 4. Layout do teclado e idioma da instalação

Carregue o teclado em pt-br:

    # loadkeys br-abnt2

Mude o idioma para português br:

    # nano /etc/locale.gen

Abra a pesquisa no arquivo com "Ctr-w" e digite "pt_BR" e tecle "enter", descomente a linha apagando o "#", salve a modificação com "Ctr-o" seguido de "Enter", e saia do arquivo com "Ctr-x".

Carregue a alteração do arquivo e exporte:

    # locale-gen && export LANG=pt_BR.UTF-8

> ## 5. Particionamento do disco (explicação)

Execute:

    # cfdisk

> :warning: Se sua instalação não é "DUAL BOOT", troque "sda5 sda6 sda7 sda8" por "sda1 sda2 sda3 sda4" sempre ao manipular as partições.

> :warning: Outro ponto importante é decidir o tamanho do swap, assunto esse que abre muitas discuções na internet, para escolher o tamanho do swap siga essas recomendações:

* Se o tamanho da sua RAM vai até 4GB: Utilize o dobro do valor total da RAM para Swap.

* Se o tamanho da sua RAM é maior que 4GB: Utilize 20% do valor total da RAM (Ex: 4GB de RAM = 1GB Swap).

* Se o máquina for um nootbook ou uma máquina que tenha "ibernação": Utilize no mínimo o valor total da RAM para Swap.

Crie partições e selecione seus respectivos tipos:

    +--------------------------------------------+
    | sda5 | 100M   | tipo: EFI System/BIOS boot |
    +------+--------+----------------------------+
    | sda6 | $VALOR | tipo: Swap                 |
    +------+--------+----------------------------+
    | sda7 | TOTAL  | tipo: Linux System         |
    +--------------------------------------------+

Salve as modificações na opção "escrever" ou "write" se você não executou os comandos de tradução.

> ## 6. Formatando as partições

No caso uso o sistema de arquivos ext4, se você preferir outro, cosulte Arch Wiki para entender melhor cada comando ou o manual mkfs.

    # mkfs.fat -F32 /dev/sda5

    # mkswap /dev/sda6

    # mkfs.ext4 /dev/sda7

> ## 7. Montando as partições

Algumas pastas vão precisar ser criadas (mkdir) para se utilizar como ponto de montagem:

    # mount /dev/sda7 /mnt

    # mkdir /mnt/boot

    # mount /dev/sda5 /mnt/boot

    # swapon /dev/sda6

> ## 8. Mirror

Pode-se alterar os mirros do Arch para, quem sabe, fazer o download mais rápido, esse passo é opcional (caso não deseje, continue no passo seguinte.

    # nano /etc/pacman.d/mirrorlist

Pressione “Ctrl+w” para pesquisarmos os espelhos. Digite BRAZIL e dê um Enter.

Você pode mover o mirror desejado para cima ("Ctr-k" e "Ctr+u" respectivamente), ou simplesmente comentar com um # os que você não quiser que sejam usados.

“Ctrl+o” para salvar e “Ctrl+x” para fechar o arquivo.

> ## 9. Instalando o Arch Linux

Execute:

    # pacstrap /mnt base base-devel linux linux-firmware

"base" são os pacotes básicos para a instalação (que não contém todas as ferramentas), "base-devel" (inclui as ferramentas necessárias para a construção (compilação e vinculação)), "linux" é o kernel escolhido, e "linux-firmware" é o gerenciador de hardware.

A instalação básica é feita a partir do comando "# pacstrap /mnt base linux linux-firmware", porém, futuramente ao buscar instalar algum programa/aplicativo/pacote, e não sendo possível através do "pacman" (gerenciador de pacotes Arch) será necessário realizar a instalação via repositório, sendo assim optei por colocar neste guia a instalação com o pacote "base-devel" que possui um sub pacote, que imita o root (fake-root), isso será util.

> ## 10. Gerando FSTAB

FSTAB vai dizer para o sistema onde estão montadas cada uma das partições:

    # genfstab -U -p /mnt >> /mnt/etc/fstab

Utilize "cat" no caminho acima para verificar se tudo está correto, confira, se não estiver desmonte as partições (umount -R /nome_da_partição) e corrija o erro.

    # cat /mnt/etc/fstab

> ## 11. Adentrando no sistema

Execute:

    # arch-chroot /mnt

> ## 12. Nano

O Arch Linux utiliza o pacote "pacman" como gerenciador de pacotes.

Faça download do editor de texto "nano":

    # pacman -S nano

Em seguida configure o idioma:

> ## 13. Locale & Time

Utilize o nano para editar o arquivo "locale.gen" que fica no diretório "/etc/":

    # nano /etc/locale.gen

Abra pesquisa no arquivo com "Ctr-w" e digite "pt_BR" e tecle "enter", descomente a linha apagando o "#", salve a modificação com "Ctr-o" seguido de "Enter", e saia do arquivo com "Ctr-x".

Carregue a alteração do arquivo com:

    # locale-gen

Use o comando echo para criar um arquivo já preenchido com o idioma desejado do sistema (isso servirá para quando o arch linux estiver finalzado e afetará a interface gráfica e outros componentes) no meu caso é português-br.

    # echo LANG=pt_BR.UTF-8 >> /etc/locale.conf

Você pode alterar data e hora depois, quando instalar a interface, assim como o fuso horário, mas se você quiser fazer isso agora, manualmente (como mo manual de instalação), você pode fazer também, para isso, é preciso criar um link simbólico semelhante ao comando:

    # ln -sf /usr/share/zoneinfo/Região/Cidade /etc/localtime

No meu caso, utilizando o horário de Brasília:

    # ln -sf /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime

> ## 14. Hostname

Adicione o nome da sua máquina em:

    # nano /etc/hostname

Escreva o nome do host para rede, exemplo: "arch" ou "ARCH" ou ainda "Arch" ou outro qualquer "nome_qualquer", fica a sua escolha. Salve a modificação com "Ctr-o" seguido de "Enter", e saia do arquivo com "Ctr-x".

> ## 15. Password

Adicine uma senha para root:

    # passwd

Essa senha é extremamente importante, não se esqueça dessa senha.

> ## 16. Modo de boot

Havendo dois sistemas operacionais distindos (Windos e Linux ou Arch Linux e Linux Ubuntu) instalados em uma máquina, é necessário um gerenciador para administrar esses dois sistemas operacionais quando a máquina for ligada.
O GRUB é um software criado para fazer este gerenciamento e através dele o usuário pode escolher qual sistema operacional deseja iniciar.

    +-----------------------------------------------------------------------+
    |                               Modo BIOS                               |
    +-----------------------------------------------------------------------+


    # pacman -S grub

    # grub-install --target=i386-pc --recheck /dev/sda


    +-----------------------------------------------------------------------+
    |                               Modo UEFI                               |
    +-----------------------------------------------------------------------+


    # pacman -S grub-efi-x86_64 efibootmgr

    # grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=arch_grub --recheck

> 17. Grub

Para que o Grub reconheça o seu Windows, é necessário a instalação dos pacotes os-prober e ntfs-3g:

    # pacman -S os-prober ntfs-3g

Por fim, gere o arquivo de configurações do Boot:

    # grub-mkconfig -o /boot/grub/grub.cfg

> 18. Driver de vídeo

Escolha um driver de vídeo segundo a definição do seu computador:

    +-----------------------------------------------------------------------+
    |                                 Intel                                 |
    +-----------------------------------------------------------------------+

    # pacman -S xf86-video-intel libgl mesa

    +-----------------------------------------------------------------------+
    |                                 Nvidia                                |
    +-----------------------------------------------------------------------+

    # pacman -S nvidia nvidia-libgl nvidia-settings mesa

    +-----------------------------------------------------------------------+
    |                                  AMD                                  |
    +-----------------------------------------------------------------------+

    # pacman -S mesa xf86-video-amdgpu

Caso você esteja usando o Arch em uma máquina virtual, como o VirtualBox, instale estes pacotes:

    # pacman -S virtualbox-guest-utils mesa mesa-libgl

> 19. Interface


Instalando uma interface gráfica:

> :warning: GDM e SDDM são gerenciadores de entrada, de login.

Escolha uma das interfaces abaixo e um gerenciador de login, ou pesquise ambos online, pois existem diversas interfaces gráficas, como sugestão, consulte a Arch Wiki.

    +-----------------------------------------------------------------------+
    |                                 Gnome                                 |
    +-----------------------------------------------------------------------+

    # pacman -S gdm

    # systemctl enable gdm
    
    # pacman -S gnome gnome-terminal nautilus

    +-----------------------------------------------------------------------+
    |                               KDS Plasma                              |
    +-----------------------------------------------------------------------+

    # pacman -S sddm

    # systemctl enable sddm

    # pacman -S plasma konsole dolphin

    +-----------------------------------------------------------------------+
    |                                  Xfce                                 |
    +-----------------------------------------------------------------------+

    # pacman -S lightdm lightdm-gtk-greeter xorg

    # systemctl enable lightdm

    # pacman -S xfce4 xfce4-goodies

> :warning: Lembrando que o Arch Linux é um sistema minimalista, alguns recursos como reconhecer outros devices e navagador de internet, precisarão ser instalados.

> 20. Criação de usuário

> :warning: É extremamente importante criar um usuário, pois não se deve manipular o sistema linux sempre logado como root (super usuário), pois certos comandos não podem ser revertidos.

    # useradd -m -g users -G wheel nome_do_usuario

    # passwd nome_do_usuario

Adicione esse usuário no arquivo "sudoers" para dar permissões a ele:

    # nano /etc/sudoers

No final do arquivo adicione:

    # nome_usuario ALL=(ALL) ALL

Se instalou a interface gráfica e se esqueceu de criar um usuário, tecle "Ctr+Alt+f2" e logue como root, crie um usuário padrão, o adicione no arquivo "sudoers", e execute o comando "reboot" para reiniciar.

> 21. Internet

Baixe o pacote necessário para acessar a internet, no caso "NetworkManager", e a interface para o mesmo:

    # pacman -S networkmanager network-manager-applet

Ative o acesso a internet ao iniciar o sistema utilizando o pacote "networkmanager", que já vem instalado, com o comando:

    # systemctl enable NetworkManager

Agora reinicie o sistema.

    # exit

    # ctr-d

    # reboot

Ao logar no gerenciador de entrada, já na interface gráfica e mude o layout do teclado caso deseje, para br-abnt2, va em configurações e mude "LAYOUTS DE ENTRADA" para "Portugues(Brasil)".

> ## Appendix:

Como já citei não é recomendado usar o Arch linux logado como root o tempo todo, sendo assim, para fazer download como usuário comum, será necessário sempre usar o comando "# sudo" (que fornece permissões ao usuário comum estando ele adicionado no arquivo "sudoers") antes de "# pacman -S..." caso contrário o terminal exigirá logar como root.

Para copiar e colar textos para o terminal linux use os atalhos "Ctr+C" no texto para copiar, e "Ctr+Shift+V" para colar no terminal, o contrário é verdadeiro ("Ctr+Shift+C" para "Ctr+V").

> 1. Programas Básicos

> Firefox (navegador):

    $ pacman -S firefox firefox-i18n-pt-br

> Screenfetch (dados do sistema via cli):

Adicione o logo da distro junto a informações do sistema ao iniciar o terminal:

    $ sudo pacman -S screenfetch

Adcione "screenfetch" no final do arquivo:

    $ sudo nano .bashrc

Salve com Ctr-O, Enter, e Ctr-X para sair.

> Gedit (editor de texto):

    $ sudo pacman -S gedit

Ou caso prifira (principalmente para desenvolvidores), instale o "Sublime Text 3 Dev" através dos repositórios da AUR, mais baixo está um pequeno tutorial que explica como baixar e instalar.

> Nomacs (visualizador de fotos):

    $ sudo pacman -S nomacs

> Flameshot (aplicação que tira screenshots):

    $ sudo pacman -S flameshot

Ou:

    $ sudo pacman -S deepin-screenshot

> Alsa (gerenciadores de áudio):

    $ sudo pacman -S alsa-lib alsa-tools alsa-utils alsa-oss

Utilise o comando "alsamixer" para configurar o áudio.

Pacote de compactação e descompactação de arquivos/pastas, use o tar (que já vem instalado):

Tar como usar:

    +---------------------------------------------------------------------------------+
    | Compactar                            |  Descompactar      |  Listar             |
    +---------------------------------------------------------------------------------+
    | tar cfv pacote.tar arquivo1 arquivo2 | tar xfv pacote.tar | tar -tvf pacote.tar |
    +---------------------------------------------------------------------------------+

É possível compactar 2 arquivos em um.

> 2. AUR

> :warning: Execute os comandos abaixo como usuário comum, e não como root.

Primeiro instale o git:

    $ sudo pacman -S git

Para baixar e instalar programas (repositórios) diretamente da AUR acesse a página do que deseja baixar (basta pesquisar o "nome_do_programa + aur") e clique no link da página.

Exemplo: https://aur.archlinux.org/visual-studio-code-bin.git

Ao clicar no link uma cópia do mesmo será feita.

Agora abra o terminal, navege para a pasta que deseja baixar o programa, pode ser a pasta de "Downloads" como exemplo, entre na pasta (cd Downloads) e cole (Ctr+Shift+V):

    $ git clone https://aur.archlinux.org/visual-studio-code-bin.git

    $ cd nome_do_pacote_programa_app

    $ makepkg -si

Lembrando que co comando "makepkg -si" não funcionará se você estiver como root, precisa ser executado como usuário comum, se você clonou o repositório como root ("git clone..."), o diretório baixado deverá de ser excluido como root usando o comando "rm -rf nome_do_diretório".

Com esses comandos poderão ser instalados qualquer programa diretamente da AUR.

> ## Referências bibliográficas (sem as quais não teria êxito):

Prof° Rossano Pablo Pinto - http://rossano.pro.br/fatec/cursos/soiiads/archlinux-site.txt

Arch Wiki - https://wiki.archlinux.org/index.php/installation_guide;

Arch Wiki - https://www.archlinux.org/download/;

balenaEtcher - https://www.balena.io/etcher/;

Dionatan Simioni - https://diolinux.com.br/2019/07/como-instalar-arch-linux-tutorial-iniciantes.html;

Antonino Praxedes - https://antoninopraxedes.wordpress.com/2016/01/16/dual-boot-instalacao-arch-linux-com-windows-10/;

Mayderson Sup3r-Us3r - https://github.com/Sup3r-Us3r/Arch-Install;

Paulo Oliveira - https://linuxsolutions.com.br/ajuda-linux-dia-663-tamanho-da-memoria-swap/
