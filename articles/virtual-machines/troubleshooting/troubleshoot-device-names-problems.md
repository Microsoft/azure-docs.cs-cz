---
title: Řešení potíží s změny názvu zařízení virtuálního počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Vysvětluje, proč zařízení virtuálního počítače s Linuxem názvů změnit a jak problém vyřešit.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 59415d792ae02bc39d218eeead25dbd409482b6f
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413436"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Řešení potíží s změny názvu zařízení virtuálního počítače s Linuxem

Tento článek vysvětluje, proč změnit názvy zařízení po restartování virtuálního počítače s Linuxem nebo znovu připojit datové disky. Tento článek poskytuje také řešení tohoto problému.

## <a name="symptoms"></a>Příznaky
Při spouštění virtuálních počítačů s Linuxem v Microsoft Azure se můžete setkat s následujícími problémy:

- Virtuální počítač se nepodaří spustit po restartování.
- Když datové disky se odpojit a znovu připojit, změna názvů zařízení disku.
- Aplikace nebo skript, který odkazuje na disk s použitím názvu zařízení selže, protože došlo ke změně názvu zařízení.

## <a name="cause"></a>Příčina

Umístění zařízení v systému Linux nemusí být konzistentní vzhledem k aplikacím napříč restartování. Názvy zařízení jsou tvořeny hlavní čísla (písmena) a podverze. Ovladač systému Linux paměťového zařízení zjišťuje nové zařízení, ovladače přiřadí hlavní a dílčí čísla z rozsahu z dostupného rozsahu do zařízení. Při odebrání zařízení čísla zařízení jsou uvolněny pro opakované použití.

K problému dochází, protože zařízení vyhledávání v Linuxu je naplánováno podsystémem SCSI asynchronně. V důsledku toho cesta zařízení se mohou lišit mezi restarty. 

## <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, použijte trvalé pojmenování. Existují čtyři způsoby, jak používat trvalé pojmenování: pomocí systému souborů popisek, UUID, podle ID nebo podle cesty. Doporučujeme použít popisek systému souborů nebo UUID pro Linuxové virtuální počítače Azure. 

Zadejte většině distribucí `fstab` **nofail** nebo **nobootwait** parametry. Tyto parametry umožňují spustit, pokud se disk připojit při spuštění systému. Další informace o těchto parametrech v dokumentaci distribuce. Informace o tom, jak nakonfigurovat virtuální počítač s Linuxem pro použití UUID při přidání datového disku najdete v tématu [připojit k počítači s Linuxem připojit nový disk](../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk). 

Při instalaci agenta Azure Linux ve virtuálním počítači agent použije k vytvoření sadu symbolické odkazy na cestě /dev/disk/azure Udev pravidla. Aplikace a skripty pomocí procesu Udev pravidla Identifikujte disky, které jsou připojené k virtuálnímu počítači, spolu s typ disku a disk na logické jednotky.

### <a name="identify-disk-luns"></a>Identifikace disku logické jednotky

Aplikace používá logické jednotky k vyhledání všech připojených discích a vytvořit symbolické odkazy. Agent Azure Linux zahrnuje Udev pravidla, která nastavení symbolické odkazy z logické jednotky do zařízení:

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
                                 
Informace o logických jednotkách z účtu systému Linux hosta je načíst s použitím `lsscsi` nebo něco podobného:

       $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

Informace o logických jednotkách hosta se používá s metadaty předplatného Azure najít virtuální pevný disk ve službě Azure Storage, který obsahuje data oddílu. Například můžete použít `az` rozhraní příkazového řádku:

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

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Zjišťovat identifikátory UUID systému souborů pomocí blkid

Aplikace a skripty, přečtěte si výstup `blkid`, nebo podobně jako zdroje informací k sestavení kompletních symbolické odkazy v cestě /dev. Výstup zobrazuje identifikátory UUID všechny disky, které jsou připojené k virtuálnímu počítači a jejich přidružené zařízení souboru:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Pravidla Udev agenta Azure Linux vytvořit sadu symbolické odkazy na cestě /dev/disk/azure:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Aplikace pomocí odkazů k identifikaci spouštěcí disk zařízení a prostředků (dočasný) disku. V Azure aplikace by měla vypadat /dev/disk/azure/root-part1 nebo /dev/disk/azure-resource-part1 cesty ke zjištění těchto oddílů.

Všechny další oddíly z `blkid` seznamu jsou umístěny na datový disk. Aplikace udržovat UUID pro tyto oddíly a zjistit název zařízení za běhu pomocí cesty:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1

    
### <a name="get-the-latest-azure-storage-rules"></a>Získat nejnovější pravidla služby Azure Storage

Pokud chcete získat nejnovější pravidla služby Azure Storage, spusťte následující příkazy:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Další informace najdete v tématech

Další informace najdete v následujících článcích:

- [Ubuntu: Použití UUID](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: Trvalé pojmenování](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: Identifikátory UUID přínosech pro vás](https://www.linux.com/news/what-uuids-can-do-you)
- [Proces Udev: Úvod do správy zařízení ve službě moderního systému Linux](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

