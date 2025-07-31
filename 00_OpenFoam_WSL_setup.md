---
# file information
title: "OpenFoam in Windows 11: WSL Setup"
date: 2025-05-11
author: "Lourenço Sassetti Mendes"
Area: languages
TOCTitle: Markdown
PageTitle: Markdown editing with Visual Studio Code
MetaDescription: Steps to configure OpenFOAM on WSL in Windows 11
---

# OpenFoam in Windows 11 using WSL: setup

**The efficient setup for operation OpenFoam and related open-source software in Windows 11**\
by Lourenço Sassetti Mendes

## Basic concepts

1. **<ins>Install WSL from the Microsoft Store</ins>**. Never via the Windows command line.

2. Use **<ins>Ubuntu</ins>**, as it's the easiest Linux distribution for installing OpenFOAM-related software and has the largest community.

2. **<ins>Graphical programs are installed exclusively on Windows</ins>** (e.g., ParaView, VS Code).

    - **Basic level**: you can use the _Windows File Explorer_ to manage files. So there's no need to install a graphical environment for Ubuntu. This makes it lighter, which is important on computers with fewer resources.

    - **HPC**: use a file manager directly on Ubuntu, to manage the HPC files using a GUI file explorer (the default is Nautilus, but I highly recommend **Nemo**).

3. For this setup, you'll need to add a few lines to the **~/.bashrc** file — but **don’t worry, it’s simple!**

4. Running **OpenFOAM** inside the Ubuntu directory is much faster than in a Windows directory, specially when writting and reading files. Trust me! But fOr small projects, such as the one shared using the __OneDrive__ app, it's ok to do it in a Windows directory.

---

## Install WSL on Windows 11

1. At the Microsoft Store, install **Ubuntu 24.04 LTS**
2. At the Microsoft Store, install **Powershell**
3. At the Microsoft Store, install **Visual Studio Code**
4. **ParaView**
    - Go to [https://www.paraview.org/download/](https://www.paraview.org/download/), and install the latest stable version.  
E.g. _ParaView-5.13.3-Windows-Python3.10-msvc2017-AMD64.msi_  
<ins>Note 1:</ins> no need to install the MS-MPI version.
5. (Optional) **Gnuplot**  
    - **Ubuntu instalation**: It's much easier (I prefer), but requires the Ubuntu GUI component (see if it makes sense to you in the **Advanced Ubuntu: Graphical user interface (GUI)** section).  
    - **Windows installation**: Go to [http://www.gnuplot.info/](http://www.gnuplot.info/), go to the [__Download__](http://www.gnuplot.info/download.html) section and install the latest stable version (E.g. __Gnuplot 6.0__). Else, if webpage security issues arise, go directly to [__Sourceforge gnuplot__](https://sourceforge.net/projects/gnuplot/files/gnuplot/) or [__Github gnuplot__](https://sourceforge.net/projects/gnuplot/files/gnuplot/).  


### Setup of Terminal App

1. Use WSL **Ubuntu** from the Windows **Terminal** app. `Windows Key`, then search `Terminal`, open app.
2. At the Windows toolbar (typically at the bottom of the screen), `Right Mouse Button` over the **Terminal** icon > 'Pin to taskbar'
2. Open **Terminal** app, go to **Settings** using `Ctrl + ,` or select the down arrow next to the `+` button at the top, and then **Settings**.  At the right column, select **Startup**, at the **Default profile**, choose your Ubuntu distribution (E.g. _Ubuntu 24.04.1 LTS_) as the default profile.


### Basic WSL commands
<ins>You don't need these commands for now.</ins>

In **Powershell**: `wsl`  
In **Command Prompt**: `wsl.exe`

WSL version:\
`wsl --version`

WSL update:\
`wsl --update`

WSL shutdown all distributions:\
`wsl --shutdown`

Get the installed linux distributions list:\
`wsl - l -v`

Completely remove a distribution: **WARNING: FROM PC ALSO!**  
`wsl --unregister <distroname>`

---

## Ubuntu 24.04 LTS

#### Basic tips
- Debian ou Ubuntu é +/- a mesma coisa.
- As linhas que começam com um # estão comentadas, ou seja, é texto para orientar o utilizador mas que não vai ser considerado como comando. Ou seja, não adianta copiar essas linhas. As restantes deve ser copiadas e executadas uma a uma.

### Launch Paraview installed on Windows
While using **OpenFoam**, it's much easier to setup a shortcut to open **ParaView** installed in Windows from the Ubuntu command line (in the **Terminal** app).

1. Find out the ParaView executables directory:
    * Go to the **Visual Studio Code** shortcut in  Windows program's launcher and check the path:     
    `Windows Key`, then search `Paraview`, under **Apps**, ##LSM CONTINUE HERE  
    <ins>__Don't forget to change the tabs' orientation in linux.__</ins>
    *

## ParaView
**remember to only install on Windows**

Check the path to the paraview command:  
`which paraview`

### advanced setup


add the following lines to the **~./bashrc** file:

```bash
############ USER new lines ##############
#### add folders to PATH - allows executable to be called
# scrips bin folder
export PATH=$PATH:$HOME/bin_of/shortcuts
 

#### customized commands
# alias to open windows programms from the Ubuntu terminal
alias code="'/mnt/c/Program Files/Microsoft VS Code/bin/code'"
alias explorer="explorer.exe ."
alias pv="paraview &"
 

#### OpenFOAM and ParaView
 
## load openfoam - uncomment the version you're using:

# openfoam.org
# source /opt/openfoam13/etc/bashrc

# openfoam.com
# source /usr/lib/openfoam/openfoam2506/etc/bashrc
# source "$WM_PROJECT_DIR/bin/tools/RunFunctions


## paraview
# allows to switch between paraview versions

# clear paraview links
alias pvReset='if [[ -n $ParaView_DIR ]]; then export PATH=$(echo :$PATH: | sed -e "s,$ParaView_DIR/bin,:,g" -e "s,$ParaView_DIR,:,g" -e 's/^://' -e 's/:$//' -e 's/::$//'); fi && unset $ParaView_DIR'
 
# setup paraview from windows
alias pvWin='pvReset &&\
               unset ParaView_VERSION_sub  &&\
               export ParaView_DIR=$HOME/bin_lnec/pvWin &&\
               unset ParaView_LIB_DIR &&\
               unset ParaView_DIR_main &&\
               export PATH=$ParaView_DIR:$PATH &&\
               echo "loaded windows paraview: "$(readlink -f $(which paraview))'
 
# set paraview from windows as the pre-loaded version
pvWin > /dev/null 2>&1
```

then let's create a directory to store the shortcuts to the Windows ParaView version:  
`mkdir -p ~/bin_of/shortcuts`

Type the following commands to create the **ParaView** shortcuts: 
```bash
cd ~/bin_of/pvWin
touch parafoam
code parafoam
```
Insert the following text in the **~/bin_of/shortcuts/parafoam** file:
```bash
#!/bin/bash

result=${PWD##*/}
result=`echo "$result.foam" | sed 's/ //g'`

echo creating file: $result 
touch $result

#$ParaView_DIR/bin/paraview $result &
paraview $result &
```

Open the file **make_pvWinLinks** with:
```bash
code ~/bin_of/shortcuts/make_pvWinLinks
```
Insert the following text in the **~/bin_of/pvWin/make_pvWinLinks** file:
```bash
#!/bin/bash

## default
versionLong="5.13.2"
pvdir="pvWin"

## alternative 'pvDir' variable, for several paraview versions installed
# versionShort=${versionLong//./}
# versionShort="513"             
# pvdir="pvWin$versionShort"     # alternative

mkdir -pv ~/bin_of/shortcuts/$pvdir

# for c in paraview pvbatch pvdataserver pvpython pvrenderserver pvserver       # complete shortcuts        # alternative to next line
for c in paraview pvbatch pvpython                                              # essential shortcuts
do
    ln -sv "/mnt/c/Program Files/ParaView $versionLong/bin/"$c".exe" ~/bin_of/shortcuts/$pvdir/$c
done
```
**Warning:** Make sure that the path to the ParaView executables directory is:  
**C:\Program Files\ParaView 5.13.2\bin**  
If not, correct the variable `versionLong="5.13.2"

```bash
chmod 755 parafoam              # give file 'parafoam' executable permissions
chmod 755 make_pvWinLinks       # give file 'make_pvWinLinks' executable permissions
./make_pvWinLinks               # execute the file to create or update the shortcuts
```

##### How to use:
- num tutorial, em vez de `touch xxxx.foam`, fazer apenas `parafoam`
- se não funcionar bem à primeira, fazer `pvWin` antes, para carregar o link para o ParaView do Windows
- a versão OpenFoam.org tem um paraview no ubuntu por defeito. Apenas funciona se a componente gráfica do ubuntu estiver instalada.



#### Advanced Ubuntu: Graphical user interface (GUI) 
<ins>Only for users of HPC or powerfull computers</ins>


1. Graphical user interface (GUI)
    ```bash
        sudo apt-get install x11-apps
        xeyes
    ```
    Do you see a pair of eyes following your mouse? Great! It's working.

2. Text editor with GUI
    ```  
    sudo apt-get install gedit
    ```
2. File manager  
    - **Nemo** (the best)
    ```bash
    sudo apt-get install nemo   # install
    nemo . &                    # open and use
    ```
    - **Nautlilus** (the default)
    ```  
    sudo apt-get install nautilus
    ```
3. Graphical programs manager: **Don't install. To be confirmed**
    ```bash
    sudo apt-get install gnome-tweaks  
    sudo apt-get install gnome-software
    sudo snap install snap-store
    ``` 
3. (Optional - probably not needed since Ubuntu 24) **Don't install. To be confirmed**
    Show minimize, maximize & close buttons on the right upper corner of the Ubuntu graphical windows
    ```  
    gsettings set org.gnome.desktop.wm.preferences button-layout ":minimize,maximize,close"
    ``` 

#### Basic command line commands
```bash
mkdir   # create a new directory
cp      # copy files or directories
mv      # move files or directories
ls      # list files and directories
ll      # list files and directories
rm      # delete files and directories
``` 

## OpenFOAM
in Ubuntu 24.04 LTS.

### OpenFOAM.com version 2506
Passos de instalação: [https://develop.openfoam.com/Development/openfoam/-/wikis/precompiled/debian](https://develop.openfoam.com/Development/openfoam/-/wikis/precompiled/debian)

Steps:
1. Add the repository  
`curl https://dl.openfoam.com/add-debian-repo.sh | sudo bash`
 
2. Update the repository information  
`sudo apt-get update`
 
3. Install preferred package. Eg,  
`sudo apt-get install openfoam2506-default`  
 A instalação (sudo apt-get install openfoam2506-default) pode demorar 10min a 1h

4. Use the openfoam shell session. Eg,  
`openfoam2506`
O último comando é para activar todos os comandos do openfoam. Usa-se porque é possível ter várias versões do OpenFOam instaladas e facilmente se muda entre elas.

    Check which OpenFOAM version you're usign:  
    `echo $WM_PROJECT_VERSION`

    Em alternativa, criamos um atalhos no ficheiro de configuração do Ubuntu: **~/.bashrc**. Abrir ficheiro executando `code ~/.bashrc` no **Ubuntu**.

    Acrescentamos, no final do ficheiro **~/.bashrc**, as seguintes linhas:
    ```
    source /usr/lib/openfoam/openfoam2506/etc/bashrc
    source "$WM_PROJECT_DIR/bin/tools/RunFunctions"
    ```
    E essential abrir e fechar uma linha de comandos Ubuntu para activar estes comandos, ou então escrever `bash`


5. Copiar os tutoriais para uma pasta na tua área, para poderes fazer todas as experiências, sem alterar os originais:
    ```   
    mkdir -p $FOAM_RUN
    cp -r $FOAM_TUTORIALS $FOAM_RUN
    ```

    Check your current path:  
    `pwd`

    Go to to the user tutorials directory:  
    ```  
    cd $FOAM_RUN
    ll
    cd tutorials
    ```  

6. Test the instalation:
    ```   
    cd $FOAM_RUN/tutorials/incompressible/icoFoam/cavity/cavity
    blockMesh
    icoFoam
    touch cavity.foam
    ```
    - Open **ParaView** in Windows and navigate to the WSL folder: **$FOAM_RUN/tutorials/incompressible/icoFoam/cavity/cavity**.  
    Open the file **cavity.foam**. 
    (tip: in the case folder, execute `pwd` to get the current directory full path)

7. The first tutorials:  
Link to tutorial Guide: [https://www.openfoam.com/documentation/tutorial-guide](https://www.openfoam.com/documentation/tutorial-guide).

    Acho boa ideia começares pelo caso 2.1, depois 2.2 e depois fazer e explorar mais o 4.1: é o mais importante.
    
    - **Case 4.1 - Dam break**: [https://www.openfoam.com/documentation/tutorial-guide/4-multiphase-flow#x13-670004](https://www.openfoam.com/documentation/tutorial-guide/4-multiphase-flow#x13-670004)  
        Começamos por ir à pasta que está nos tutoriais e duplicámos:
        ```   
        cp -r $FOAM_RUN/tutorials/multiphase/interFoam/laminar/damBreak/damBreak $FOAM_RUN
        cd $FOAM_RUN/damBreak
        ./Allrun
        parafoam
        ./Allclean
        ```

    - Also try these tutorials:
        ```
        cp -r $FOAM_RUN/tutorials/multiphase/interFoam/RAS/weirOverflow/ $FOAM_RUN
        cd $FOAM_RUN/weirOverflow

        cp -r $FOAM_RUN/tutorials/multiphase/interFoam/RAS/waterChannel/ $FOAM_RUN
        cd $FOAM_RUN/waterChannel
        ```
### OpenFOAM.org version 13 (2025)
https://openfoam.org/

#### Instalation
Comandos de instalação na linha de comandos do Ubuntu:  
[https://openfoam.org/download/13-ubuntu/](https://openfoam.org/download/13-ubuntu/)
```
sudo sh -c "wget -O - https://dl.openfoam.org/gpg.key > /etc/apt/trusted.gpg.d/openfoam.asc"
sudo add-apt-repository http://dl.openfoam.org/ubuntu
 
sudo apt update
sudo apt -y install openfoam13
. /opt/openfoam13/etc/bashrc
foamRun -help
```

#### Basic user Configuration  
In order to use the installed OpenFOAM package, complete the following:
1. Open the **~/.bashrc file** in the user’s home directory in an editor, e.g. by typing in a terminal window (note the dot):  
`gedit ~/.bashrc`
2. At the bottom of that file, add the following line and save the file (include the leading dot):  
`. /opt/openfoam13/etc/bashrc`
3. Open a new terminal window and test that the `foamRun` application, from the OpenFOAM package, is working by typing:  
`foamRun -help`
4. A “Usage” message should appear. Your installation and user configuration is complete.

#### Tutorials
Copiar os tutoriais para uma pasta do utilizador:
```bash
echo $FOAM_RUN                      # see the $FOAM_RUN variable value
mkdir -p $FOAM_RUN                  # create a directory with the path $FOAM_RUN
cp -r $FOAM_TUTORIALS $FOAM_RUN     # copy the original tutorials to the $FOAM_RUN directory
```
Basic Tutorials:  
https://doc.cfd.direct/openfoam/user-guide-v13/tutorials
 
 
Tutorials Example videos:  
- Weir Overflow by _Café e CFD_: [Youtube video 1](https://www.youtube.com/watch?v=w_YNbme15Q8)
- Water Channel by _NSIV OpenFOAM & Ansys_: [Youtube video 1](https://www.youtube.com/watch?v=0M1QS_OeQXE)

---

# Extras

### Visual Studio Code extensions
_**!Don't install for now. Needs confirmation!**_
* Remote Development
* PowerShell
* Slurm Syntax
* Python
* OpenFOAM
* Gnuplot

---

# Troubleshooting

 1. If you get the message:  
 `bash: ./Allrun: permission denied`  
 It's because the file doesn't have execution permissions.  
 Execute: `chmod 755 Allrun`

---

# Documentation
1. **Notes on CFD: General Principles**: [https://doc.cfd.direct/notes/cfd-general-principles/](https://doc.cfd.direct/notes/cfd-general-principles/). Essential book for CFD users from the creators of OpenFOAM.org.

 
