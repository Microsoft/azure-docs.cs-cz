---
title: Připojení Azure File storage na virtuální počítače s Linuxem pomocí protokolu SMB | Microsoft Docs
description: Tom, jak připojit Azure File storage na virtuální počítače s Linuxem pomocí protokolu SMB pomocí Azure CLI
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
ms.openlocfilehash: 2019324030b2e4c469d0b9ba937fb40a9d0675f1
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099707"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Připojení Azure File storage na virtuální počítače s Linuxem pomocí protokolu SMB


Tento článek ukazuje, jak používat službu Azure File storage na virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure připojení protokolu SMB. Azure File storage nabízí sdílené složky v cloudu přes standardní protokol SMB. 

File storage nabízí sdílené složky v cloudu, které používají standardní protokol SMB. Sdílené složky můžete připojit z jakékoli operační systém, který podporuje protokol SMB 3.0. Pokud používáte připojení protokolu SMB v systému Linux, získáte snadno zálohy robustní, trvalé archivováním umístění úložiště podporovaný SLA.

Přesunutí souborů z virtuálního počítače připojení protokolu SMB, který je hostován úložiště souborů je skvělým způsobem, jak ladit protokoly. Stejné sdílené složce protokolu SMB může být místně připojen do pracovní stanice se systémem Mac, Linux nebo Windows. SMB není nejlepší řešení pro streamování Linux nebo aplikace protokolů v reálném čase, protože není protokol SMB vytvořené pro zpracování těchto funkcí velkou protokolování. Nástroje protokolování vyhrazené, jednotná vrstvy, jako je Fluentd bude vhodnější než SMB pro shromažďování Linux a aplikace protokolování výstupu.

Tato příručka vyžaduje, že používáte Azure CLI verze verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu **az --version**. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků s názvem *myResourceGroup* v *východní USA* umístění.

```bash
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvořit nový účet úložiště, v rámci skupiny prostředků, kterou jste vytvořili, pomocí [vytvořit účet úložiště az](/cli/azure/storage/account#create). Tento příklad vytvoří účet úložiště s názvem *mySTORAGEACCT<random number>*  a vloží název tohoto účtu úložiště v proměnné **STORAGEACCT**. Názvy účtů úložiště musí být jedinečný, pomocí `$RANDOM` připojí číslo na konec zajistit její jedinečnost.

```bash
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>Získat klíč úložiště

Když vytvoříte účet úložiště, klíče účtu jsou vytvořeny v párech, aby se mohou otáčet bez výpadku služby. Když přepnete na druhý klíč v páru, můžete vytvořit nový pár klíčů. Nových klíčů účtu úložiště se vytváří vždy v párech, takže máte vždy alespoň jeden nepoužívané klíč účtu úložiště připravené přepnout do.

Zobrazit klíče účtu úložiště pomocí [seznam klíčů účtu úložiště az](/cli/azure/storage/account/keys#list). Uloží hodnotu klíč 1 v tomto příkladu **klíč úložiště** proměnné.

```bash
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Vytvoření sdílené složky

Vytvoření sdílené složky úložiště souboru pomocí [vytvořit sdílenou složku úložiště az](/cli/azure/storage/share#create). 

Názvy složek musí být malá písmena, číslice a pomlčky jeden ale nemůže začínat pomlčkou. Kompletní informace o zadávání názvů sdílených složek a souborů najdete v tématu [Pojmenování a odkazování na sdílené složky, soubory a metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Tento příklad vytvoří sdílenou složku s názvem *název_sdílené_položky* s kvótou 10 GiB. 

```bash
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Vytvoření přípojný bod

Chcete-li připojení Azure sdílené složky v počítači Linux, je potřeba zajistěte, aby byla **cifs utils** balíček nainstalován. Pokyny k instalaci naleznete v tématu [nainstalovat balíček cifs utils pro Linux distribuční](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Soubory Azure používá protokol SMB, která komunikuje přes TCP port 445.  Pokud máte problémy se připojení Azure sdílené složky, ujistěte se, že brána firewall neblokuje TCP port 445.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Připojení sdílené složky

Připojení Azure sdílené složky do místního adresáře. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```



## <a name="persist-the-mount"></a>Zachovat připojení

Po restartování virtuálního počítače s Linuxem, je při vypnutí nepřipojené připojené sdílenou složku SMB. Pro opětovné připojení do sdílené složky protokolu SMB na spuštění, přidejte řádek na Linux /etc/fstab. Linux používá soubor fstab zobrazte seznam systémů souborů, které je potřeba připojit během spouštění. Přidání sdílené složky SMB zajistí, že sdílené složky úložiště bude trvale připojeného souboru systém pro virtuální počítač s Linuxem. Přidání úložiště File sdílená složka SMB na nový virtuální počítač je možné, pokud používáte cloudové init.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```
Pro zvýšení zabezpečení v produkčním prostředí měli byste uložit přihlašovací údaje mimo fstab.

## <a name="next-steps"></a>Další postup

- [Přizpůsobení virtuálního počítače s Linuxem během vytváření pomocí init cloudu](using-cloud-init.md)
- [Přidání disku do virtuálního počítače s Linuxem](add-disk.md)
- [Šifrování disky na virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure](encrypt-disks.md)

