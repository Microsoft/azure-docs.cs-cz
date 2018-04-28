---
title: Příprava image virtuálního počítače Azure pro použití s inicializací cloudu | Microsoft Docs
description: Tom, jak připravit bitovou kopii existující virtuální počítač Azure pro nasazení s inicializací cloudu
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: dda444e77f588cd1ba5989b393e9a3987241ef9a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Příprava stávající image virtuálního počítače s Linuxem Azure pro použití s inicializací cloudu
Tento článek ukazuje, jak využít existující virtuální počítač Azure a připravit opakovaně nasazeném a připravené k použití init cloudu. Výsledný obraz lze použít k nasazení nového virtuálního počítače nebo sady škálování virtuálního počítače – buď z nich může pak dále přizpůsobit podle cloudu init v době nasazení.  Tyto skripty cloudu init spustit při prvním spuštění počítače po prostředky se zřizují Azure. Další informace o cloudu init fungování nativně ve službě Azure a podporovaných distribucích systému Linux najdete v tématu [init cloudu – přehled](using-cloud-init.md)

## <a name="prerequisites"></a>Požadavky
Tento dokument předpokládá, že už máte Azure spuštění virtuálního počítače se systémem podporovanou verzi operačního systému Linux. Jste již nakonfigurovali počítač, aby vyhovovaly potřebám vaší nainstalované všechny požadované moduly, zpracovat všechny požadované aktualizace a otestovali k zajištění splňuje vaše požadavky. 

## <a name="preparing-rhel-74--centos-74"></a>Příprava RHEL 7.4 nebo CentOS 7.4
Je třeba se SSH do virtuálním počítačům s Linuxem a spusťte následující příkazy, aby bylo možné nainstalovat init cloudu.

```bash
sudo yum install -y cloud-init gdisk
sudo yum check-update cloud-init -y
sudo yum install cloud-init -y
```

Aktualizace `cloud_init_modules` kapitoly `/etc/cloud/cloud.cfg` zahrnout následující moduly:
```bash
- disk_setup
- mounts
```

Zde je ukázka jaké univerzální `cloud_init_modules` vypadá části.
```bash
cloud_init_modules:
 - migrator
 - bootcmd
 - write-files
 - growpart
 - resizefs
 - disk_setup
 - mounts
 - set_hostname
 - update_hostname
 - update_etc_hosts
 - rsyslog
 - users-groups
 - ssh
```
Počet úloh souvisejících se zřizováním a zpracování dočasné disky je třeba aktualizovat v `/etc/waagent.conf`. Spusťte následující příkazy aktualizace příslušná nastavení. 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
```
Povolit Azure jenom jako zdroj dat pro Azure Linux Agent tak, že vytvoříte nový soubor `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` pomocí editoru podle svého výběru s následujícími řádky:

```bash
# This configuration file is provided by the WALinuxAgent package.
datasource_list: [ Azure ]
```

Přidáte konfiguraci, kterou chcete vyřešit chyby registrace nezpracovaných název hostitele.
```bash
cat > /etc/cloud/hostnamectl-wrapper.sh <<\EOF
#!/bin/bash -e
if [[ -n $1 ]]; then
  hostnamectl set-hostname $1
else
  hostname
fi
EOF

chmod 0755 /etc/cloud/hostnamectl-wrapper.sh

cat > /etc/cloud/cloud.cfg.d/90-hostnamectl-workaround-azure.cfg <<EOF
# local fix to ensure hostname is registered
datasource:
  Azure:
    hostname_bounce:
      hostname_command: /etc/cloud/hostnamectl-wrapper.sh
EOF
```

Pokud vaše stávající image Azure má odkládací soubor nakonfigurovaný a chcete změnit konfiguraci souboru odkládacího souboru pro nové bitové kopie pomocí cloudu init, budete muset odebrat existující odkládací soubor.

Pro RedHat na základě bitové kopie – postupujte podle pokynů v následujícím dokumentu RedHat vysvětlením postup [odebrat odkládací soubor](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/5/html/Deployment_Guide/s2-swap-removing-file.html).

Pro Image CentOS s swapfile povolena můžete spustit následující příkaz k vypnutí swapfile:
```bash
sudo swapoff /mnt/resource/swapfile
```

Ujistěte se, odkaz swapfile je odebrán z `/etc/fstab` -ho by měl vypadat podobně jako následující výstup:
```text
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Uložte místa a odebrat odkládací soubor spuštěním následujícího příkazu:
```bash
rm /mnt/resource/swapfile
```
## <a name="extra-step-for-cloud-init-prepared-image"></a>Další krok pro cloud init připravené bitové kopie
> [!NOTE]
> Pokud image byla předtím **cloudu init** připravené a konfiguraci bitové kopie, musíte udělat následující kroky.

Následující tři příkazy se použít jen v případě přizpůsobení jako novou bitovou kopii specializované zdrojový virtuální počítač byl dříve vytváří init cloudu.  NENÍ nutné spustit tyto bitové kopie se nakonfigurovalo pomocí Azure Linux Agent.

```bash
sudo rm -rf /var/lib/cloud/instances/* 
sudo rm -rf /var/log/cloud-init*
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Dokončení agenta systému Linux nastavení 
Všechny Image platformy Azure mají Azure Linux Agent nainstalován, bez ohledu na to pokud byl nakonfigurovat pomocí init cloudu, nebo ne.  Spusťte následující příkaz k dokončení zrušení zřízení uživatele z počítače systému Linux. 

```bash
sudo waagent -deprovision+user -force
```

Další informace o příkazech deprovision Azure Linux Agent najdete v tématu [Azure Linux Agent](agent-user-guide.md) další podrobnosti.

Ukončení relace SSH a pak z vašeho prostředí bash, spusťte následující příkazy AzureCLI navrácení, generalize a vytvořit novou bitovou kopii virtuálního počítače Azure.  Nahraďte `myResourceGroup` a `sourceVmName` s příslušné informace, které odpovídají vaše sourceVM.

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Další postup
Mezi další cloudu init změny konfigurace naleznete v následujících tématech:
 
- [Přidání další uživatele Linux do virtuálního počítače](cloudinit-add-user.md)
- [Spusťte Správce balíčků aktualizovat existující balíčky na při prvním spuštění](cloudinit-update-vm.md)
- [Změňte název místního hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfigurační soubory a vložit klíče](tutorial-automate-vm-deployment.md)
