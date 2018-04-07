---
title: Přizpůsobení virtuálního počítače s Linuxem během vytváření v Azure pomocí cloudu init | Microsoft Docs
description: Jak používat cloudové init k přizpůsobení virtuálního počítače s Linuxem během vytváření pomocí Azure CLI 1.0
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: v-livech
ms.openlocfilehash: 2e9182a18a2827ed7f54f5fd042e5934b3b1fd5c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="use-cloud-init-to-customize-a-linux-vm-during-creation-with-the-azure-cli-10"></a>Použít cloudové init k přizpůsobení virtuálního počítače s Linuxem během vytváření pomocí Azure CLI 1.0
Tento článek ukazuje, jak chcete-li skript cloudu init nastavit název hostitele, aktualizace nainstalované balíčky, a spravovat uživatelské účty.  Skripty cloud init se označují jako při vytvoření virtuálního počítače z příkazového řádku Azure.  Tento článek vyžaduje:

* účet Azure ([získejte bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/))
* [rozhraní příkazového řádku Azure](../../cli-install-nodejs.md) s přihlášením `azure login`.
* Rozhraní příkazového řádku Azure *musí být v* režimu Azure Resource Manager`azure config mode arm`.

## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

- [Azure CLI 1.0](#quick-commands) – naše rozhraní příkazového řádku pro classic a resource správu modelech nasazení (v tomto článku)
- [Azure CLI 2.0](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – naše rozhraní příkazového řádku nové generace pro model nasazení správy prostředků

## <a name="quick-commands"></a>Rychlé příkazy
Vytvoření cloudové init.txt skript, který nastaví název hostitele, aktualizuje všechny balíčky a přidá uživatele sudo do systému Linux.

```sh
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```
Vytvořte skupinu prostředků ke spuštění virtuálních počítačů do.

```azurecli
azure group create myResourceGroup westus
```

Vytvoření virtuálního počítače s Linuxem pomocí cloudu init konfigurace během spouštění.

```azurecli
azure vm create \
  -g myResourceGroup \
  -n myVM \
  -l westus \
  -y Linux \
  -f myVMnic \
  -F myVNet \
  -P 10.0.0.0/22 \
  -j mySubnet \
  -k 10.0.0.0/24 \
  -Q canonical:ubuntuserver:14.04.2-LTS:latest \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -C cloud-init.txt
```

## <a name="detailed-walkthrough"></a>Podrobný postup
### <a name="introduction"></a>Úvod
Při spuštění nového virtuálního počítače s Linuxem se zobrazuje standardní virtuální počítač s Linuxem se nic, vlastní nebo připraveno k vašim potřebám. [Init cloudu](https://cloudinit.readthedocs.org) je standardní způsob vložení skriptu nebo konfigurace nastavení do tohoto virtuálního počítače s Linuxem, jako je spuštění pro první.

V Azure existují tři různé způsoby provést změny do virtuálního počítače s Linuxem, jako je právě nasazen nebo spuštěn.

* Vložit skripty s použitím init cloudu.
* Vložit skripty s použitím Azure [rozšíření VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Šablony Azure pomocí init cloudu.
* K pomocí šablony Azure [CustomScriptExtention](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Chcete-li vložit skripty kdykoli po spuštění:

* SSH ke spuštění příkazů přímo
* Vložit skripty s použitím Azure [rozšíření VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), imperativní nebo šablony Azure
* Nástroje pro správu konfigurace, jako Ansible, Salt, Chef nebo Puppet.

> [!NOTE]
> : Rozšíření VMAccess spustí skript jako kořenové stejným způsobem jako pomocí protokolu SSH můžete.  Však pomocí rozšíření virtuálního počítače umožňuje několik funkcí této nabídky Azure, které mohou být užitečné, v závislosti na vašem scénáři.
> 
> 

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Init cloudu dostupnosti pro virtuální počítač Azure rychlým vytvořením bitové kopie aliasy:
| Alias | Vydavatel | Nabídka | Skladová jednotka (SKU) | Verze | init cloudu |
|:--- |:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |nejnovější |ne |
| CoreOS |CoreOS |CoreOS |Stable |nejnovější |ano |
| Debian |credativ |Debian |8 |nejnovější |ne |
| openSUSE |SUSE |openSUSE |13.2 |nejnovější |ne |
| RHEL |Redhat |RHEL |7.2 |nejnovější |ne |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |nejnovější |ano |

Microsoft ve spolupráci s našimi partnery získat cloudu init zahrnuté a práci v bitové kopie, které poskytují do Azure.

## <a name="adding-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Přidání cloudu init skript k vytvoření virtuálních počítačů pomocí Azure CLI
Spusťte skript cloudu init při vytváření virtuálního počítače v Azure, zadejte soubor init cloudu pomocí Azure CLI `--custom-data` přepínače.

Vytvořte skupinu prostředků ke spuštění virtuálních počítačů do.

```azurecli
azure group create myResourceGroup westus
```

Vytvoření virtuálního počítače s Linuxem pomocí cloudu init konfigurace během spouštění.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubnet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud-init.txt
```

## <a name="creating-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Vytváření cloudu init skript, který nastaví název hostitele virtuálního počítače s Linuxem
Jedním z nejjednodušší a nejdůležitější nastavení pro všechny virtuální počítač s Linuxem bude název hostitele. Jsme tento parametr můžete snadno nastavit pomocí init cloudových – pomocí tohoto skriptu.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>Příklad cloudu init skript s názvem `cloud_config_hostname.txt`.
```sh
#cloud-config
hostname: myservername
```

Při prvním spuštění virtuálního počítače, nastaví tento skript cloudu init název hostitele na `myservername`.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_hostname.txt
```

Přihlášení a ověřte název hostitele nového virtuálního počítače.

```bash
ssh myVM
hostname
myservername
```

## <a name="creating-a-cloud-init-script-to-update-linux"></a>Vytváření cloudu init skriptu aktualizace Linux
Pro zabezpečení budete chtít vaší virtuálního počítače s Ubuntu aktualizovat při prvním spuštění.  Pomocí cloudu init, které jsme to udělat pomocí skriptu postupujte podle kroků, v závislosti na Linux distribuce, kterou používáte.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Ukázkový skript cloudu init `cloud_config_apt_upgrade.txt` pro Debian rodinu
```sh
#cloud-config
apt_upgrade: true
```

Po Linux, jsou aktualizovány všechny nainstalované balíčky prostřednictvím `apt-get`.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_apt_upgrade.txt
```

Přihlášení a ověření, jsou aktualizovány všechny balíčky.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="creating-a-cloud-init-script-to-add-a-user-to-linux"></a>Vytváření cloudu init skript k přidání uživatele do systému Linux
Jedním z první úlohy na žádné nové virtuální počítače Linux je chcete přidat uživatele pro sebe nebo nepoužívejte `root`. SSH klíče jsou vhodné pro zabezpečení a použitelnost a jejich přidání do `~/.ssh/authorized_keys` souboru pomocí tohoto skriptu init cloudu.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Ukázkový skript cloudu init `cloud_config_add_users.txt` pro Debian řadu
```sh
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Po Linux, jsou uvedené uživatele vytvořen a přidán do skupiny sudo.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_add_users.txt
```

Přihlášení a ověření nově vytvořeného uživatele.

```bash
ssh myVM
cat /etc/group
```

Výstup

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>Další kroky
Init cloudu se stává stále jednu standardní způsob, jak upravit virtuálním počítačům s Linuxem na spuštění. Azure má také rozšíření virtuálních počítačů, které umožňují upravit vaše LinuxVM na spouštěcí nebo když je spuštěná. Například můžete použít Azure VMAccessExtension resetovat informace SSH nebo uživatele, když běží virtuální počítač. S inicializací cloudu je zapotřebí restartování k resetování hesla.

[O rozšíření virtuálního počítače a funkce](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Spravovat uživatele, SSH a zkontrolujte nebo opravte disky na virtuálních počítačích Azure Linux pomocí rozšíření VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

