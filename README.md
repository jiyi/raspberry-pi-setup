# raspberry-pi-setup

## ssh免密登录登录

```shell
mkdir .ssh
cat << EOF > ~/.ssh/authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC6es+qqERmxiMnjteYJU3nmVGQkWswiHJwt7QHvr0NgXSV9Ak+M2tVZZ2LEiQJTGr8Gbe/nBCF12OEUEZWsMnVpuAR+9Dq3uxKO1VLpPpr0LoJmxqdGl2DktUPusLxtDZGXTio6Whei0ZbYhOdjY3KfNUFuREq5E6ekUg6OAEINmIuNcsJX+WDGsFecVFRyNGKVJe72KhasPZdO5Zc/GAW8tTGtgvbvt+54vJ8Hp/GBBxLQCPODkXh7jpc9/7D067DebUcAIVKpqA07ErYf90/0mlQL+18+1qq1cAp34P++Qb6iULRB2w4SqQ/N3RX1jeOXjBNG+ACs8l5sFJriLjx jiyixx@gmail.com
EOF
chmod 700 .ssh
chmod 600 .ssh/authorized_keys
```

## sudo 免密

```shell
sudo visudo
pi ALL=(ALL) NOPASSWD: ALL
```

## 换源

```shell
sudo sed -i 's|raspbian.raspberrypi.org|mirrors.ustc.edu.cn/raspbian|g' /etc/apt/sources.list
sudo sed -i 's|//archive.raspberrypi.org|//mirrors.ustc.edu.cn/archive.raspberrypi.org|g' /etc/apt/sources.list.d/raspi.list
sudo apt-get update
sudo apt-get upgrade
```

## 安装必备软件

```shell
sudo apt install fish vim byobu firefox-esr firefox-esr-l10n-zh-cn -y
cat /etc/shells
chsh -s /usr/bin/fish
```

## PocketLCD

``` shell
sudo vim /boot/config.txt
hdmi_force_hotplug=1
disable_overscan
overscan_left=0
overscan_right=0
overscan_top=0
overscan_bottom=0
display_rotate=1
gpu_mem=256
hdmi_timings=1080 0 18 16 48 1920 0 8 1 7 0 0 0 60 0  126000000 3
hdmi_group=2
hdmi_mode=87
hdmi_ignore_edid=0xa5000080
hdmi_cvt 1080 1920 60
max_framebuffer_width=1080
max_framebuffer_height=1920
```

## docker

```shell
sudo apt-get update
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    usb-release
# curl -fsSL "https://download.docker.com/linux/raspbian/gpg" | apt-key add -qq - >/dev/null
# curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
# 两个gpg一样
cat gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=armhf signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] http://mirrors.aliyun.com/docker-ce/linux/raspbian/ \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
sudo systemctl enable docker

vim .config/fish/config.fish
alias docker="sudo docker”

sudo vim /etc/docker/daemon.json
{
  "registry-mirrors" : [
    "http://docker.mirrors.ustc.edu.cn"
  ]
}
```

## clash

``` shell
# 7890代理端口
# 9090管理端口
docker run -d --name=clash -v "$PWD/config.yaml:/root/.config/clash/config.yaml" -p "7890:7890" -p "9090:9090" --restart=unless-stopped dreamacro/clash
```

用docker的话 allow-lan 必须为true，external-controller必须是 '0.0.0.0:9090'

## firefox

1password

先安装swichy，再设置proxy，不知道为什么用firefox自带的代理设置无法登录firefox账号
