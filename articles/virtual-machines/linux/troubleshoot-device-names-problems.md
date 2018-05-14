---
title: Řešení potíží s změny názvu zařízení virtuálního počítače s Linuxem v Azure | Microsoft Docs
description: Vysvětluje, proč zařízení virtuálního počítače s Linuxem názvy změny a jak problém vyřešit.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 1b500cc279cf9b92a3358d66e6db59dbf41d73b8
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Řešení potíží s změny názvu zařízení virtuálního počítače s Linuxem

Tento článek vysvětluje, proč názvy zařízení změnit po restartování virtuálního počítače s Linuxem nebo znovu připojte datových disků. Tento článek také poskytuje řešení tohoto problému.

## <a name="symptoms"></a>Příznaky
Následující problémy se můžete setkat, když běží virtuální počítače s Linuxem v Microsoft Azure:

- Virtuální počítač se nepodaří spustit po restartování.
- Když datových disků jsou odpojit a znovu připojit, se mění názvy zařízení disků.
- Aplikace nebo skriptu, který odkazuje na disk pomocí názvu zařízení se nezdaří, protože došlo ke změně názvu zařízení.

## <a name="cause"></a>Příčina

Zařízení cesty v systému Linux nemusí být konzistentní napříč restartování. Názvy zařízení obsahovat hlavní čísla (písmena) a menší čísla. Ovladače zařízení úložiště Linux zjistí nové zařízení, ovladače přiřadí hlavní a podverze čísla z rozmezí do zařízení. Při odebrání zařízení čísla zařízení jsou uvolněny pro opakované použití.

K problému dochází, protože zařízení kontrolu v systému Linux je ještě naplánováno subsystémem SCSI asynchronně. V důsledku toho cesta zařízení se může lišit mezi restarty. 

## <a name="solution"></a>Řešení

Chcete-li vyřešit tento problém, použijte trvalé názvy. Existují čtyři způsoby použití trvalé pojmenování: pomocí systému souborů štítek, UUID, podle ID nebo cestu. Doporučujeme používat pro virtuální počítače Linux Azure popisek systému souborů nebo UUID. 

Zadejte většina distribuce `fstab` **nofail** nebo **nobootwait** parametry. Tyto parametry Povolit systému pro spuštění, když dojde k připojení při spuštění selhání disku. Podívejte se do dokumentace distribuční Další informace o těchto parametrů. Informace o tom, jak nakonfigurovat virtuální počítač s Linuxem používat UUID, když přidáte datový disk najdete v tématu [připojit k virtuálního počítače s Linuxem připojit nový disk](add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk). 

Když na virtuálním počítači je nainstalován agent Azure Linux, agent použije k vytvoření sadu symbolické odkazy na cestě /dev/disk/azure Udev pravidla. Aplikace a skripty, pomocí pravidel Udev k identifikaci disky, které jsou připojené k virtuálnímu počítači, společně s typ disku a disku logických jednotek.

### <a name="identify-disk-luns"></a>Identifikovat logické diskové jednotky

Aplikace používá logické jednotky k vyhledání všech připojených disků a vytvořit symbolické odkazy. Azure Linux agent obsahuje Udev pravidla, která nastavit symbolické odkazy z logické jednotky do zařízení:

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
                                 
Informace o logických jednotkách z účtu guest Linux je načíst s použitím `lsscsi` nebo podobného nástroje:

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

Aplikace a skripty, přečtěte si výstup `blkid`, nebo podobné zdroje informací, můžete vytvořit symbolické odkazy v cestě /dev. Výstup zobrazuje identifikátory UUID všech disků, které jsou připojené k virtuálnímu počítači a jejich přidružené zařízení soubor:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Pravidla Azure Linux agent Udev vytvořit sadu symbolické odkazy na cestě /dev/disk/azure:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Aplikace pomocí odkazů pro identifikaci spouštěcí disk zařízení a prostředků (dočasné) disku. V Azure aplikace by měla vypadat v /dev/disk/azure/root-part1 nebo /dev/disk/azure-resource-part1 cesty ke zjištění těchto oddílů.

Všechny další oddíly z `blkid` seznamu jsou umístěny na datový disk. Aplikace udržovat identifikátor UUID pro tyto oddíly a zjistit název zařízení v době běhu pomocí cesty:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1

    
### <a name="get-the-latest-azure-storage-rules"></a>Získat nejnovější pravidla Azure Storage

Chcete-li získat nejnovější pravidla Azure Storage, spusťte následující příkazy:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Další informace najdete v tématech

Další informace najdete v následujících článcích:

- [Ubuntu: Pomocí UUID](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: Trvalé pojmenování](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: Co identifikátory UUID využití.](https://www.linux.com/news/what-uuids-can-do-you)
- [Proces Udev: Úvod do správy zařízení v moderní systému Linux](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

