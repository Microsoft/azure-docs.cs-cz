---
title: Řešení potíží se změnami názvů zařízení virtuálních počítačů s Linux v Azure | Microsoft Docs
description: Vysvětluje, proč se mění názvy zařízení virtuálních počítačů se systémem Linux a jak tento problém vyřešit.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 31f64a504156134b1d622705d5301d9cd5a5f5b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88756822"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Řešení potíží se změnami názvu zařízení virtuálních počítačů se systémem Linux

Tento článek vysvětluje, proč se po restartování virtuálního počítače se systémem Linux nebo opětovném připojení datových disků mění názvy zařízení. Článek také poskytuje řešení pro tento problém.

## <a name="symptoms"></a>Příznaky
Při spouštění virtuálních počítačů se systémem Linux v Microsoft Azure může docházet k následujícím problémům:

- Po restartování se virtuální počítač nepodaří spustit.
- Když se datové disky odpojí a znovu připojí, změní se názvy zařízení na disku.
- Aplikace nebo skript, který odkazuje na disk pomocí názvu zařízení, se nezdařil, protože došlo ke změně názvu zařízení.

## <a name="cause"></a>Příčina

Cesty zařízení v systému Linux nezaručují konzistenci v rámci restartování. Názvy zařízení se skládají z hlavních čísel (písmen) a vedlejších čísel. Když ovladač úložného zařízení se systémem Linux zjistí nové zařízení, ovladač přiřadí hlavní a vedlejší čísla z dostupného rozsahu k zařízení. Po odebrání zařízení se čísla zařízení uvolní pro opakované použití.

K tomuto problému dochází, protože subsystém SCSI naplánoval kontrolu zařízení v systému Linux, aby probíhala asynchronně. V důsledku toho se může název cesty zařízení měnit v různých restartech.

## <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, použijte trvalé pojmenování. Existují čtyři způsoby použití trvalého pojmenovávání: podle popisku systému souborů, podle identifikátoru UUID, podle ID nebo podle cesty. Pro virtuální počítače Azure Linux doporučujeme použít popisek systému souborů nebo UUID.

Většina distribucí poskytuje `fstab` parametry **neúspěšné** nebo **nobootwait** . Tyto parametry umožňují spuštění systému v případě, že se nepodaří připojit disk při spuštění. Další informace o těchto parametrech najdete v dokumentaci k distribuci. Informace o tom, jak nakonfigurovat virtuální počítač Linux pro použití UUID při přidávání datového disku, najdete v tématu [připojení k virtuálnímu počítači se systémem Linux a připojení nového disku](../linux/add-disk.md#format-and-mount-the-disk).

Když je agent Azure Linux nainstalovaný na virtuálním počítači, používá agent udev pravidla k vytvoření sady symbolických odkazů pod cestou/dev/disk/Azure. Aplikace a skripty používají pravidla udev k identifikaci disků, které jsou připojené k virtuálnímu počítači, spolu s typem disku a logickými jednotkami.

Pokud jste fstab už upravili takovým způsobem, že se váš virtuální počítač nespouští a nemůžete k vašemu VIRTUÁLNÍmu počítači přihlášený protokol SSH, můžete použít [konzolu sériového portu VM](./serial-console-linux.md) k zadání [režimu Single User](./serial-console-grub-single-user-mode.md) a úpravě fstab.

### <a name="identify-disk-luns"></a>Identifikace logických jednotek disku

Aplikace používají logické jednotky (LUN) k vyhledání všech připojených disků a k vytváření symbolických odkazů. Agent Azure Linux zahrnuje pravidla udev, která nastavují symbolické odkazy z logické jednotky (LUN) na zařízení:

```console
$ tree /dev/disk/azure

/dev/disk/azure
├── resource -> ../../sdb
├── resource-part1 -> ../../sdb1
├── root -> ../../sda
├── root-part1 -> ../../sda1
└── scsi1
    ├── lun0 -> ../../../sdc
    ├── lun0-part1 -> ../../../sdc1
    ├── lun1 -> ../../../sdd
    ├── lun1-part1 -> ../../../sdd1
    ├── lun1-part2 -> ../../../sdd2
    └── lun1-part3 -> ../../../sdd3
```

Informace o logické jednotce (LUN) z účtu hosta systému Linux jsou načteny pomocí `lsscsi` nebo podobného nástroje:

```console
$ sudo lsscsi

[1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

[2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

[3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

[5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

[5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd
```

Informace o hostované logické jednotce se používají s metadaty předplatného Azure k vyhledání VHD v Azure Storage, který obsahuje data oddílu. Například můžete použít rozhraní příkazového `az` řádku:

```azurecli
$ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks
[
{
"caching": "None",
  "createOption": "empty",
"diskSizeGb": 1023,
  "image": null,
"lun": 0,
"managedDisk": null,
"name": "testVM-20170619-114353",
"vhd": {
  "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"
}
},
{
"caching": "None",
"createOption": "empty",
"diskSizeGb": 512,
"image": null,
"lun": 1,
"managedDisk": null,
"name": "testVM-20170619-121516",
"vhd": {
  "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"
  }
  }
]
```

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Zjištění identifikátorů UUID systému souborů pomocí blkid

Aplikace a skripty čtou výstup `blkid` (nebo podobné zdroje informací), aby mohli vytvořit symbolické odkazy v cestě/dev. Výstup zobrazuje identifikátory UUID všech disků připojených k virtuálnímu počítači a jejich přidruženému souboru zařízení:

```console
$ sudo blkid -s UUID

/dev/sr0: UUID="120B021372645f72"
/dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
/dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
/dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"
```

Pravidla udev agenta Azure Linux vytvoří sadu symbolických odkazů pod cestou/dev/disk/Azure:

```console
$ ls -l /dev/disk/azure

total 0
lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1
```

Aplikace používají odkazy k identifikaci zařízení spouštěcího disku a prostředku (dočasného) disku. V Azure by měly aplikace Hledat v cestách/dev/disk/Azure/root-part1 nebo/dev/disk/Azure-Resource-part1, aby tyto oddíly zjistili.

Všechny další oddíly ze `blkid` seznamu se nacházejí na datovém disku. Aplikace udržují UUID pro tyto oddíly a používají cestu k vyhledání názvu zařízení za běhu:

```console
$ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1
```


### <a name="get-the-latest-azure-storage-rules"></a>Získat nejnovější pravidla Azure Storage

Chcete-li získat nejnovější pravidla Azure Storage, spusťte následující příkazy:

```console
# sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
# sudo udevadm trigger --subsystem-match=block
```

## <a name="see-also"></a>Viz také

Další informace najdete v následujících článcích:

- [Ubuntu: použití UUID](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: Trvalé pojmenovávání](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: Jaké identifikátory UUID můžou](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Úvod do správy zařízení v moderním systému Linux](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

