---
title: Příprava image virtuálního počítače Azure pro použití s nástrojem cloud-init | Dokumentace Microsoftu
description: Jak připravit předem existující image virtuálního počítače Azure pro nasazení s nástrojem cloud-init
services: virtual-machines-linux
documentationcenter: ''
author: danis
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/27/2019
ms.author: danis
ms.openlocfilehash: da539a5bebc1613115f89a7b47c513ce486b5e3a
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337307"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Příprava image existujícího virtuálního počítače Azure s Linuxem pro použití s nástrojem cloud-init
Tento článek popisuje, jak využít stávající virtuální počítač Azure a připravit ho bude opakovaně nasazeném a připravené k použití cloud-init. Výsledná bitová kopie lze použít k nasazení nového virtuálního počítače nebo škálovací sady virtuálních počítačů – každý z nich může potom dají dál přizpůsobit pomocí cloud-init v době nasazení.  Tyto skripty cloud-init spustit při prvním spuštění, jakmile se zřizují prostředky Azure. Další informace o tom, jak funguje cloud-init nativně v Azure a podporovaných distribucích systému Linux, najdete v části [přehled cloud-init](using-cloud-init.md)

## <a name="prerequisites"></a>Požadavky
Tento dokument předpokládá, že již máte spuštěné v Azure virtuální počítač s podporovanou verzi operačního systému Linux. Jste už nakonfigurovali počítač tak, aby odpovídala vašim potřebám, nainstalované všechny požadované moduly, zpracování všech požadovaných aktualizací a otestovali tak, že splňuje vaše požadavky. 

## <a name="preparing-rhel-76--centos-76"></a>Příprava RHEL 7.6 / CentOS 7.6
Budete si muset SSH do virtuálního počítače s Linuxem a spusťte následující příkazy, abyste mohli nainstalovat cloud-init.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

Aktualizace `cloud_init_modules` tématu `/etc/cloud/cloud.cfg` zahrnout následující moduly:
```bash
- disk_setup
- mounts
```

Toto je ukázka jaké pro obecné účely `cloud_init_modules` oddíl vypadá jako.
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
Počet úloh souvisejících se zřizováním a zpracování dočasné disky potřeba aktualizovat v `/etc/waagent.conf`. Spuštěním následujících příkazů aktualizujte odpovídající nastavení. 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cp /lib/systemd/system/waagent.service /etc/systemd/system/waagent.service
sed -i 's/After=network-online.target/WantedBy=cloud-init.service\\nAfter=network.service systemd-networkd-wait-online.service/g' /etc/systemd/system/waagent.service
systemctl daemon-reload
cloud-init clean
```
Povolit Azure jenom jako zdroj dat pro agenta Azure Linux tak, že vytvoříte nový soubor `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` pomocí editoru podle vašeho výběru s následujícími řádky:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
datasource:
   Azure:
     agent_command: [systemctl, start, waagent, --no-block]
```

Pokud vaše stávající image Azure má odkládací soubor nakonfigurovaný a chcete změnit konfiguraci odkládací soubor pro nové Image pomocí cloud-init, musíte odebrat existující odkládacího souboru.

Pro Red Hat na základě Image - postupujte podle pokynů následující Red Hat dokument s vysvětlením, jak [odebrat odkládací soubor](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

Image CentOS, stránkovacího souboru povolená můžete spustit následující příkaz k vypnutí možnosti stránkovacího souboru:
```bash
sudo swapoff /mnt/resource/swapfile
```

Zajištění odkaz stránkovacího souboru je odebrán z `/etc/fstab` -by měla vypadat podobně jako následující výstup:
```text
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Z důvodu šetření místem a odebrat odkládací soubor můžete spustit následující příkaz:
```bash
rm /mnt/resource/swapfile
```
## <a name="extra-step-for-cloud-init-prepared-image"></a>Přidat další krok pro cloud-init připravenou image
> [!NOTE]
> Pokud vaše image byla dříve **cloud-init** připravené a nakonfigurovanou image, musíte udělat následující kroky.

Následující tři příkazy používají pouze v případě přizpůsobení být novou bitovou kopii specializované zdrojového virtuálního počítače byla dříve povolené nástrojem cloud-init.  Nemusíte spouštět vaše image se nakonfigurovalo pomocí agenta Azure Linux.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Finalizuje se agenta pro Linux nastavení 
Všechny Image platformy Azure máte Azure Linux a nainstalovanými agenty, bez ohledu na to se nakonfigurovalo pomocí cloud-init, nebo ne.  Spusťte následující příkaz na dokončení zrušení zřízení uživatele v počítači s Linuxem. 

```bash
sudo waagent -deprovision+user -force
```

Další informace o příkazech zrušení zřízení agenta Azure Linux, najdete v článku [agenta Azure Linux](../extensions/agent-linux.md) další podrobnosti.

Ukončete relaci SSH a pak z vašeho prostředí bash spusťte následující příkazy AzureCLI uvolnění, generalizace a vytvořte novou image virtuálního počítače Azure.  Nahraďte `myResourceGroup` a `sourceVmName` odráží vaše sourceVM odpovídajícími informacemi.

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Další postup
Příklady cloud-init další změny konfigurace najdete tady:
 
- [Přidání dalších uživatelů Linuxu na virtuální počítač](cloudinit-add-user.md)
- [Spusťte Správce balíčků aktualizovat existující balíčky při prvním spuštění](cloudinit-update-vm.md)
- [Změnit místní název hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizovat konfigurační soubory a klíče pro vložení](tutorial-automate-vm-deployment.md)
