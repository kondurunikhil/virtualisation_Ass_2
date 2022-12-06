# virtualisation_Ass_2
CMPE 283 Assignment 2


1) Went to https://github.com/torvalds/linux, forked the repo to my account, and cloned it on our GCP VM:
 
2) cd ~ <br />
  git clone https://github.com/kondurunikhil/linux.git<br />
  cd linux<br />
  git status<br />
 git remote -v <br />
![git remote -v](https://github.com/kondurunikhil/virtualisation_Ass_2/blob/main/images/git_remote_v.png)


3) cp /boot/config-5.15.0-1025-gcp ~/linux/.config

4) Install necessary Tools
```
sudo apt-get install build-essential
sudo apt-get install kernel-package
sudo apt-get install ccache 
sudo apt-get install flex
sudo apt-get install bison
sudo apt-get install libssl-dev
sudo apt-get install libelf-dev
```
5) prepare the linux using make prepare command: <br />
```
make prepare
```
6) build all the modules that goes into the linux kernel:<br />
```
make -j 8 modules
```
7) build the linux kernel itself:  <br />
```
make -j 8
```

8) After the modules and kernel is built, we can package them into a format that suitable for booting inside our VM <br />
```
sudo make -j 8 INSTALL_MOD_STRIP=1 modules_install
```
9) Install linux kernal 
```
sudo make -j 8 install
```
