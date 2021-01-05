---
title: Příprava image virtuálního počítače Azure pro použití s cloudem a inicializací
description: Příprava existující image virtuálního počítače Azure pro nasazení pomocí Cloud-init
author: danis
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.date: 06/24/2019
ms.author: danis
ms.openlocfilehash: 3aed65b0319f9a80c5ebc45428ff0c380c33fc3d
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883261"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Příprava stávající image virtuálního počítače Azure pro Linux pro použití s cloudovým inicializací
V tomto článku se dozvíte, jak převést existující virtuální počítač Azure a připravit ho na opětovné nasazení a začít používat Cloud-init. Výsledná image se dá použít k nasazení nového virtuálního počítače nebo sady škálování virtuálních počítačů – jednu z nich pak můžete v době nasazení dále upravit pomocí Cloud-init.  Tyto skripty Cloud-init se spouštějí při prvním spuštění, jakmile se prostředky zřídí v Azure. Další informace o tom, jak nativně funguje Cloud-init v Azure a podporované distribuce Linux, najdete v článku [Přehled Cloud-init](using-cloud-init.md) .

## <a name="prerequisites"></a>Požadavky
Tento dokument předpokládá, že už máte spuštěný virtuální počítač Azure s podporovanou verzí operačního systému Linux. Počítač jste už nakonfigurovali tak, aby vyhovoval vašim potřebám, nainstalovaná všechny požadované moduly, zpracoval všechny požadované aktualizace a otestovali jste je, aby splňovaly vaše požadavky. 

## <a name="preparing-rhel-76--centos-76"></a>Příprava RHEL 7,6/CentOS 7,6
Pokud chcete nainstalovat Cloud-init, musíte se přes SSH do virtuálního počítače se systémem Linux a spustit následující příkazy.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install -y cloud-init 
```

Aktualizujte `cloud_init_modules` část v, `/etc/cloud/cloud.cfg` aby obsahovala následující moduly:

```bash
- disk_setup
- mounts
```

Tady je ukázka toho, co vypadá oddíl pro obecné účely `cloud_init_modules` .

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

Řada úloh týkajících se zřizování a manipulace s dočasnými disky se musí aktualizovat v `/etc/waagent.conf` . Spusťte následující příkazy a aktualizujte příslušná nastavení.

```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cloud-init clean
```

Umožněte pro agenta Azure Linux jenom Azure jako zdroj dat, a to tak, že vytvoříte nový soubor `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` pomocí editoru podle vašeho výběru s následujícím řádkem:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
```

Pokud má vaše stávající image Azure nakonfigurované odkládací soubor a chcete změnit konfiguraci stránkovacího souboru pro nové Image pomocí Cloud-init, je nutné odebrat existující odkládací soubor.

Pro image založené na Red Hat – postupujte podle pokynů v následujícím dokumentu Red Hat, který vysvětluje, jak [Odebrat odkládací soubor](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

Pro Image CentOS s povoleným swapfile můžete spustit následující příkaz, který vypne swapfile:

```bash
sudo swapoff /mnt/resource/swapfile
```

Ujistěte se, že odkaz na swapfile je odebraný z `/etc/fstab` – měl by vypadat nějak takto:

```output
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Pokud chcete ušetřit místo a odebrat odkládací soubor, můžete spustit následující příkaz:

```bash
rm /mnt/resource/swapfile
```

## <a name="extra-step-for-cloud-init-prepared-image"></a>Dodatečný krok pro připravenou bitovou kopii Cloud-init
> [!NOTE]
> Pokud byla image dříve připravená a nakonfigurovaná image **pro inicializaci cloudu** , musíte provést následující kroky.

Následující tři příkazy se použijí pouze v případě, že virtuální počítač, který přidáváte jako novou specializovanou zdrojovou image, byl dříve zřízen modulem Cloud-init.  Nemusíte je spouštět, pokud byla image nakonfigurovaná pomocí agenta Azure Linux.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Dokončuje se nastavení agenta pro Linux. 
Všechny Image platformy Azure mají nainstalovaného agenta Azure Linux bez ohledu na to, jestli byl nakonfigurovaný pomocí Cloud-init nebo ne.  Spuštěním následujícího příkazu dokončete zrušení zřízení uživatele z počítače se systémem Linux. 

```bash
sudo waagent -deprovision+user -force
```

Další informace o příkazech zrušení zřízení agenta Azure Linux naleznete v tématu [Agent Azure Linux](../extensions/agent-linux.md) , kde najdete další podrobnosti.

Ukončete relaci SSH a potom z prostředí bash spusťte následující příkazy Azure CLI k uvolnění, generalizaci a vytvoření nové image virtuálního počítače Azure.  Nahraďte `myResourceGroup` a `sourceVmName` odpovídajícími informacemi, které odpovídají vašemu sourceVM.

```azurecli
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Další kroky
Další příklady cloudových inicializací změn konfigurace najdete v následujících tématech:
 
- [Přidání dalšího uživatele se systémem Linux k virtuálnímu počítači](cloudinit-add-user.md)
- [Spusťte Správce balíčků, aby při prvním spuštění aktualizoval existující balíčky.](cloudinit-update-vm.md)
- [Změnit místní název hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfiguračních souborů a vkládání klíčů](tutorial-automate-vm-deployment.md)
