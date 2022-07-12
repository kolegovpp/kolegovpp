**Error adding qgis.org repository public key to apt keyring**

from https://gis.stackexchange.com/questions/332245/error-adding-qgis-org-repository-public-key-to-apt-keyring

use the next command:

```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-key 46B5721DBBD2996A
```
or more commands:

```bash
wget -qO - https://qgis.org/downloads/qgis-$(date +%Y).gpg.key | sudo gpg --no-default-keyring --keyring gnupg-ring:/etc/apt/trusted.gpg.d/qgis-archive.gpg --import
sudo chmod a+r /etc/apt/trusted.gpg.d/qgis-archive.gpg
```
