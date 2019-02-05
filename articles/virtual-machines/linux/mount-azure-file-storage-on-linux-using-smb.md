---
title: Připojení Azure File storage s linuxem pomocí protokolu SMB | Dokumentace Microsoftu
description: Postup připojení Azure File storage s linuxem pomocí protokolu SMB pomocí Azure CLI
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.subservice: files
ms.openlocfilehash: 0aa291c3334af35ec90648cfbcbb7de7015deb99
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731440"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Připojení Azure File storage s linuxem pomocí protokolu SMB

Tento článek popisuje, jak používat službu Azure File storage na virtuální počítač s Linuxem pomocí připojení protokolu SMB pomocí Azure CLI. Azure File storage nabízí sdílené složky v cloudu s využitím standardního protokolu SMB. 

File storage nabízí sdílené složky v cloudu, které využívají standardní protokol SMB. V jakémkoli operačním systému, který podporuje protokol SMB 3.0 můžou připojit sdílenou složku. Když použijete připojení protokolu SMB v systému Linux, získáte snadné zálohování robustní a trvalý archivace umístění úložiště, která je podporovaná smlouvou SLA.

Přesunutí souborů z virtuálního počítače připojení protokolu SMB, která je hostována ve službě File storage je skvělý způsob, jak ladit protokoly. Stejné sdílené složce protokolu SMB je místně připojit do pracovní stanice se systémem Mac, Linux nebo Windows. SMB není nejlepším řešením pro streamování Linux nebo protokoly aplikací v reálném čase, protože protokol SMB není určený pro zpracování těchto funkcí náročné protokolování. Nástroje protokolování vyhrazené a Unifikovaný vrstvy, jako je Fluentd bude vhodnější než SMB k shromažďování údajů o Linuxu a aplikace protokolování výstupu.

Tato příručka vyžaduje, že používáte Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu **az --version**. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků s názvem *myResourceGroup* v *USA – východ* umístění.

```bash
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvořit nový účet úložiště, v rámci skupiny prostředků, kterou jste vytvořili pomocí [vytvořit účet úložiště az](/cli/azure/storage/account). Tento příklad vytvoří účet úložiště s názvem *mySTORAGEACCT<random number>*  a vloží název tohoto účtu úložiště do proměnné **STORAGEACCT**. Názvy účtů úložiště musí být jedinečný, pomocí `$RANDOM` přidá číslo a za účelem zajistit její jedinečnost.

```bash
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>Získání klíče úložiště

Když vytvoříte účet úložiště, klíče účtu se vytvoří v párech tak, aby se mohou otáčet bez přerušení služby. Když přepnete do druhého klíče dvojice, můžete vytvořit nový pár klíčů. Nové klíče účtu úložiště se vytváří vždy ve dvojicích, tak budete mít vždy alespoň jeden nevyužité klíč účtu úložiště můžete přepnout na.

Zobrazení klíče účtu úložiště pomocí [seznamu klíčů účtu úložiště az](/cli/azure/storage/account/keys). V tomto příkladu ukládají hodnotu klíč 1 **klíč úložiště** proměnné.

```bash
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Vytvoření sdílené složky

Vytvoření sdílené složky úložiště souboru pomocí [vytvořit sdílenou složku úložiště az](/cli/azure/storage/share). 

Názvy sdílených složek musí být malá písmena, číslice a spojovníky, ale nemůžou začínat pomlčkou. Kompletní informace o zadávání názvů sdílených složek a souborů najdete v tématu [Pojmenování a odkazování na sdílené složky, soubory a metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Tento příklad vytvoří sdílenou složku s názvem *myshare* s kvótou 10 GB. 

```bash
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Vytvořit bod připojení

Na počítači s Linuxem připojit sdílenou složku Azure, budete muset Ujistěte se, že máte **cifs utils** nainstalovaný balíček. Pokyny k instalaci, naleznete v tématu [nainstalovat balíček cifs utils pro vaši Linuxovou distribuci](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Služba soubory Azure používá protokol SMB, která komunikuje přes TCP port 445.  Pokud máte potíže s připojením sdílené složky Azure, ujistěte se, že brána firewall neblokuje TCP port 445.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Připojit sdílenou složku

Připojení sdílené složky Azure do místního adresáře. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

Výše uvedený příkaz používá [připojit](https://linux.die.net/man/8/mount) příkaz pro připojení sdílené složky Azure a možnosti, které jsou specifické pro [cifs](https://linux.die.net/man/8/mount.cifs). Konkrétně, file_mode a dir_mode možnosti sadu souborů a adresářů na oprávnění `0777`. `0777` Poskytuje oprávnění čtení, zápis a oprávnění pro všechny uživatele. Tato oprávnění můžete změnit tak, že nahradíte hodnoty s jinými [chmod oprávnění](https://en.wikipedia.org/wiki/Chmod). Můžete také využít další [cifs](https://linux.die.net/man/8/mount.cifs) možnosti, jako je ID skupiny nebo uid. 


## <a name="persist-the-mount"></a>Zachovat připojení

Po restartování virtuálního počítače s Linuxem, je při vypnutí odpojeny připojené sdílené složky SMB. Pro opětovné připojení sdílené složky protokolu SMB při spuštění, přidá řádek do Linux /etc/fstab. Linux používá soubor fstab zobrazte seznam systémů souborů, které jsou potřebné k připojení během spouštění. Přidání sdílené složky SMB zajišťuje, že sdílená složka File storage trvale připojeného souboru systému pro virtuální počítač s Linuxem. Přidání souboru úložiště sdílené složky SMB do nového virtuálního počítače je možné při použití cloud-init.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```
Pro zvýšení zabezpečení v produkčních prostředích byste měli uložit svoje přihlašovací údaje mimo fstab.

## <a name="next-steps"></a>Další postup

- [Použití cloud-init k přizpůsobení virtuálního počítače s Linuxem během vytváření](using-cloud-init.md)
- [Přidání disku do virtuálního počítače s Linuxem](add-disk.md)
- [Šifrování disků na virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure](encrypt-disks.md)

