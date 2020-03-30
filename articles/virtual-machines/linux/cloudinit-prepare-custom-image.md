---
title: Příprava image virtuálního počítače Azure pro použití s cloud-init
description: Jak připravit již existující image virtuálního počítače Azure pro nasazení s cloud-init
author: danis
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: danis
ms.openlocfilehash: fef41f4dc90c03e3efbe4c8a75e495c26eec64b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066821"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Příprava existující image virtuálního počítače Azure v Linuxu pro použití s cloud-init
Tento článek ukazuje, jak vzít existující virtuální počítač Azure a připravit ho na přeobsazené a připravené k použití cloud-init. Výsledná bitová kopie může být použita k nasazení nového virtuálního počítače nebo škálovacísady virtuálních strojů - z nichž jednu z nich lze dále přizpůsobit cloud-init v době nasazení.  Tyto skripty cloud-init se spustí při prvním spuštění, jakmile jsou prostředky zřízeny Azure. Další informace o tom, jak cloud-init funguje nativně v Azure a podporované distribuce Linuxu, najdete [v tématu cloud-init přehled](using-cloud-init.md)

## <a name="prerequisites"></a>Požadavky
Tento dokument předpokládá, že už máte spuštěný virtuální počítač Azure s podporovanou verzí operačního systému Linux. Již jste nakonfigurovali počítač tak, aby vyhovoval vašim potřebám, nainstalovali jste všechny požadované moduly, zpracovali všechny požadované aktualizace a testovali jste jej, abyste se ujistili, že splňuje vaše požadavky. 

## <a name="preparing-rhel-76--centos-76"></a>Příprava RHEL 7.6 / CentOS 7.6
Musíte SSH do virtuálního počítače s Linuxem a spustit následující příkazy, abyste mohli nainstalovat cloud-init.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

Aktualizujte `cloud_init_modules` část `/etc/cloud/cloud.cfg` v aplikaci tak, aby zahrnovala následující moduly:

```bash
- disk_setup
- mounts
```

Zde je ukázka toho, `cloud_init_modules` jak vypadá část pro obecné účely.

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

V aplikaci je třeba aktualizovat řadu úkolů týkajících se zřizování a zpracování dočasných `/etc/waagent.conf`disků. Chcete-li aktualizovat příslušná nastavení, spusťte následující příkazy.

```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cloud-init clean
```

Povolit jenom Azure jako zdroj dat pro `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` Azure Linux Agent vytvořením nového souboru pomocí editoru podle vašeho výběru s následujícím řádkem:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
```

Pokud má vaše stávající image Azure nakonfigurovaný odkládací soubor a chcete změnit konfiguraci odkládacího souboru pro nové image pomocí cloud-init, musíte odebrat existující odkládací soubor.

Pro obrázky založené na Red Hat - postupujte podle pokynů v následujícím dokumentu Red Hat s vysvětlením, jak [odstranit odkládací soubor](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

U obrazů CentOS s povoleným odkládací soubor můžete spustit následující příkaz pro vypnutí odkládacího souboru:

```bash
sudo swapoff /mnt/resource/swapfile
```

Ujistěte se, že `/etc/fstab` odkaz na odkládací soubor je odebrán z - měl by vypadat podobně jako následující výstup:

```output
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Chcete-li ušetřit místo a odebrat odkládací soubor, můžete spustit následující příkaz:

```bash
rm /mnt/resource/swapfile
```

## <a name="extra-step-for-cloud-init-prepared-image"></a>Další krok pro připravený obrázek cloud-init
> [!NOTE]
> Pokud byla vaše bitová kopie dříve připravená a nakonfigurovaná v **cloudu,** je třeba provést následující kroky.

Následující tři příkazy se používají jenom v případě, že virtuální ms, který upravujete jako novou specializovanou zdrojovou bitovou kopii, byl dříve zřízen cloud-init.  Není nutné je spouštět, pokud byla vaše image nakonfigurována pomocí agenta Azure Linux.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Dokončení nastavení linuxového agenta 
Všechny image platformy Azure mají nainstalovaný Agent Azure Linux, bez ohledu na to, jestli byl nakonfigurovaný cloud-init nebo ne.  Spusťte následující příkaz a dokončete zrušení zřízení uživatele z počítače s Linuxem. 

```bash
sudo waagent -deprovision+user -force
```

Další informace o příkazech zrušení zřízení agenta Azure Linuxu najdete v tématu [Azure Linux Agent](../extensions/agent-linux.md) další podrobnosti.

Ukončete relaci SSH a potom z prostředí bash spusťte následující příkazy AzureCLI, abyste navrátili, zobecnit a vytvořili novou image virtuálního počítače Azure.  Nahraďte `myResourceGroup` a `sourceVmName` příslušné informace odrážející váš zdrojVM.

```azurecli
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Další kroky
Další příklady změn konfigurace cloud-init najdete v následujících tématech:
 
- [Přidání dalšího uživatele Linuxu do virtuálního počítače](cloudinit-add-user.md)
- [Spuštění správce balíčků pro aktualizaci existujících balíčků při prvním spuštění](cloudinit-update-vm.md)
- [Změna názvu místního hostitele virtuálního_kmontíva](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfiguračních souborů a vložení klíčů](tutorial-automate-vm-deployment.md)
