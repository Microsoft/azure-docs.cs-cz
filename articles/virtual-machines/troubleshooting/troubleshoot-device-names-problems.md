---
title: Poradce při potížích se změnami názvu virtuálního počítače s Linuxem v Azure | Dokumenty společnosti Microsoft
description: Vysvětluje, proč se mění názvy zařízení virtuálních počítačů s Linuxem a jak problém vyřešit.
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
ms.openlocfilehash: 7d8a7e7e88837214042fb8f1c109c0b93bfe771b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058207"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Poradce při potížích se změnami názvu virtuálního počítače s Linuxem

Tento článek vysvětluje, proč se názvy zařízení mění po restartování virtuálního počítače s Linuxem nebo opětovném připojení datových disků. Článek také poskytuje řešení tohoto problému.

## <a name="symptoms"></a>Příznaky
Při spouštění virtuálních aplikací Linux v Microsoft Azure může dojít k následujícím problémům:

- Virtuální počítač se po restartování nespustí.
- Pokud jsou datové disky odpojeny a znovu připojeny, změní se názvy diskových zařízení.
- Aplikace nebo skript, který odkazuje na disk pomocí názvu zařízení, se nezdaří, protože se změnil název zařízení.

## <a name="cause"></a>Příčina

Cesty zařízení v Linuxu nejsou zaručeny konzistentní napříč restartováními. Názvy zařízení se skládají z hlavních čísel (písmen) a vedlejších čísel. Když ovladač paměťového zařízení Linux zjistí nové zařízení, ovladač přiřadí hlavním a vedlejším číslům z dostupného rozsahu zařízení. Po odebrání zařízení jsou čísla zařízení uvolněna pro opakované použití.

K problému dochází, protože skenování zařízení v Linuxu je naplánováno subsystémem SCSI asynchronně. V důsledku toho se název cesty zařízení může v rámci restartování lišit.

## <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, použijte trvalé pojmenování. Existují čtyři způsoby, jak používat trvalé pojmenování: podle popisku souborového systému, Podle UUID, ID nebo podle cesty. Doporučujeme použít popisek souborového systému nebo UUID pro virtuální počítače Azure Linux.

Většina distribucí poskytuje parametry `fstab` **nofail** nebo **nobootwait.** Tyto parametry umožňují spuštění systému při připojení disku při spuštění. Další informace o těchto parametrech naleznete v dokumentaci k distribuci. Informace o tom, jak nakonfigurovat virtuální počítač s Linuxem pro použití UUID při přidání datového disku, najdete [v tématu Připojení k virtuálnímu počítači SIP pro připojení nového disku](../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk).

Když je agent Azure Linux nainstalovaný na virtuálním počítači, agent používá pravidla Udev k vytvoření sady symbolických odkazů pod cestou /dev/disk/azure. Aplikace a skripty používají pravidla Udev k identifikaci disků, které jsou připojené k virtuálnímu počítače, spolu s typem disku a diskovými logickými stanicemi.

Pokud jste už fstab upravili tak, že se váš virtuální počítač nespouštějí a nemůžete s svitek do virtuálního počítače, můžete použít [konzolu Serial Console virtuálního](./serial-console-linux.md) počítače k přepnutí [režimu jednoho uživatele](./serial-console-grub-single-user-mode.md) a úpravě fstab.

### <a name="identify-disk-luns"></a>Identifikace diskových logických stanic

Aplikace používají luny k vyhledání všech připojených disků a k vytvoření symbolických odkazů. Agent Azure Linux obsahuje pravidla Udev, která nastavují symbolické odkazy z logické jednotky na zařízení:

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

Informace o logické jednotce z účtu `lsscsi` guest Linuxu se načítají pomocí nebo podobným nástrojem:

      $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

Informace o logické jednotce hosta se používají s metadaty předplatného Azure k vyhledání virtuálního pevného disku ve službě Azure Storage, která obsahuje data oddílu. Můžete například použít `az` zadávaný rok:

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

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Objevte souborový systém UUID pomocí blkid

Aplikace a skripty číst `blkid`výstup , nebo podobné zdroje informací, vytvořit symbolické odkazy v /dev cestě. Výstup zobrazuje UUID všech disků, které jsou připojené k virtuálnímu počítače a jejich přidružené mu souboru zařízení:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Pravidla agenta Azure Linuxu Udev vytvoří sadu symbolických odkazů pod cestou /dev/disk/azure:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Aplikace používají odkazy k identifikaci spouštěcího diskového zařízení a disku prostředků (dočasných). V Azure by aplikace měly hledat v cestách /dev/disk/azure/root-part1 nebo /dev/disk/azure-resource-part1, aby tyto oddíly zjistily.

Všechny další oddíly `blkid` ze seznamu jsou umístěny na datovém disku. Aplikace udržovat UUID pro tyto oddíly a pomocí cesty ke zjištění názvu zařízení za běhu:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1


### <a name="get-the-latest-azure-storage-rules"></a>Získejte nejnovější pravidla Azure Storage

Pokud chcete získat nejnovější pravidla Azure Storage, spusťte následující příkazy:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Viz také

Další informace najdete v těchto článcích:

- [Ubuntu: Použití UUID](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: Trvalé pojmenování](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: Co uuids může udělat pro vás](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Úvod do správy zařízení v moderním systému Linux](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

