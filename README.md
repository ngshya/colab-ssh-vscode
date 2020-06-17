# colab-ssh-vscode
Google Colab with SSH and Visual Studio Code Server

Original Article: https://medium.com/@ngshya/colab-free-gpu-ssh-visual-studio-code-server-36fe1d3c5243

## Get ngrok token and start SSH session on Colab
```Python
# Install useful stuff
! apt install -- yes ssh screen nano htop ranger git > /dev/null

# SSH setting
! echo "root:carbonara" | chpasswd
! echo "PasswordAuthentication yes" > /etc/ssh/sshd_config
! echo "PermitUserEnvironment yes" >> /etc/ssh/sshd_config
! echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
! service ssh restart > /dev/null

# Download ngrok
! wget -q -c -nc https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip
! unzip -qq -n ngrok-stable-linux-amd64.zip

# Run ngrok
authtoken = "PUT_YOUR_TOKEN_HERE"
get_ipython().system_raw('./ngrok authtoken $authtoken && ./ngrok tcp 22 &')
! sleep 3

# Get the address for SSH
import requests
from re import sub
r = requests.get('http://localhost:4040/api/tunnels')
str_ssh = r.json()['tunnels'][0]['public_url']
str_ssh = sub("tcp://", "", str_ssh)
str_ssh = sub(":", " -p ", str_ssh)
str_ssh = "ssh root@" + str_ssh
print(str_ssh)
```

## Mount Google Drive and make some folders for vscode
```Python
from google.colab import drive
drive.mount('/googledrive')
! mkdir -p /googledrive/My\ Drive/colabdrive
! mkdir -p /googledrive/My\ Drive/colabdrive/root/.local/share/code-server
! ln -s /googledrive/My\ Drive/colabdrive /
! ln -s /googledrive/My\ Drive/colabdrive/root/.local/share/code-server /root/.local/share/
```
## Start Visual Studio Code Server
```Python
! curl -fsSL https://code-server.dev/install.sh | sh > /dev/null
! code-server --bind-addr 127.0.0.1:9999 --auth none &
```
