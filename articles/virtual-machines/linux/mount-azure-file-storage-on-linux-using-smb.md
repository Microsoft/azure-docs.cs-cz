---
title: Připojení úložiště souborů Azure na virtuálních počítačích SMB s Operačním systémem Linux
description: Jak připojit úložiště souborů Azure na virtuálnípočítače SMB s pomocí azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: 0314095a053087a7d490926c41c6ae386c304919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066648"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Připojení úložiště souborů Azure na virtuálních počítačích SMB s Operačním systémem Linux

Tento článek ukazuje, jak používat službu úložiště souborů Azure na virtuálním počítači SM pomocí připojení SMB s Azure CLI. Azure File Storage nabízí sdílené složky v cloudu pomocí standardního protokolu SMB. 

Úložiště souborů nabízí sdílené složky v cloudu, které používají standardní protokol SMB. Sdílenou složku můžete připojit z libovolného operačního systému, který podporuje smb 3.0. Při použití připojení SMB na Linuxu získáte snadné zálohování do robustního a trvalého umístění úložiště archivace, které je podporováno službou SLA.

Přesunutí souborů z virtuálního počítače do připojení SMB, které je hostované v úložišti souborů, je skvělý způsob, jak ladit protokoly. Stejnou sdílenou složku SMB lze připojit místně k pracovní stanici Mac, Linux nebo Windows. SMB není nejlepším řešením pro streamování Linuxu nebo protokolů aplikací v reálném čase, protože protokol SMB není vytvořen tak, aby zvládl tak těžké povinnosti protokolování. Vyhrazený, jednotný nástroj pro protokolování vrstvy, jako je Fluentd by bylo lepší volbou než SMB pro sběr Linux a aplikace protokolování výstupu.

Tato příručka vyžaduje, abyste spouštěli Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu **az --version**. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků s názvem *myResourceGroup* v umístění *V USA ve východě.*

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvořte nový účet úložiště v rámci skupiny prostředků, kterou jste vytvořili pomocí [vytvoření účtu úložiště az](/cli/azure/storage/account). Tento příklad vytvoří účet úložiště s názvem *mySTORAGEACCT\<náhodné číslo>* a umístí název tohoto účtu úložiště do proměnné **STORAGEACCT**. Názvy účtů úložiště musí `$RANDOM` být jedinečné, pomocí připojí číslo na konec, aby bylo jedinečné.

```azurecli
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>Získání klíče úložiště

Když vytvoříte účet úložiště, klíče účtu se vytvoří ve dvojicích, takže je lze otáčet bez přerušení služby. Když přepnete na druhý klíč v páru, vytvoříte nový pár klíčů. Nové klíče účtů úložiště se vždy vytvářejí ve dvojicích, takže máte vždy připravený alespoň jeden nepoužívaný klíč účtu úložiště k přepnutí.

Zobrazení klíčů účtu úložiště pomocí [seznamu klíčů účtu úložiště az](/cli/azure/storage/account/keys). Tento příklad ukládá hodnotu klíče 1 v proměnné **STORAGEKEY.**

```azurecli
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Vytvoření sdílené složky

Vytvořte sdílenou složku úložiště souborů pomocí [vytvoření sdílené složky úložiště az](/cli/azure/storage/share). 

Názvy sdílení musí být všechna malá písmena, čísla a jednotlivé spojovníky, ale nemohou začínat pomlčkou. Kompletní informace o zadávání názvů sdílených složek a souborů najdete v tématu [Pojmenování a odkazování na sdílené složky, soubory a metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Tento příklad vytvoří sdílenou složku s názvem *myshare* s kvótou 10 GiB. 

```azurecli
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Vytvoření přípojného bodu

Chcete-li připojit sdílenou složku Azure v počítači s Linuxem, musíte se ujistit, že máte nainstalovaný balíček **CIFS-utils.** Pokyny k instalaci [najdete v tématu Instalace balíčku cifs-utils pro distribuci Linuxu](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Azure Files používá protokol SMB, který komunikuje přes port TCP 445.  Pokud máte potíže s montáží sdílené složky Azure, ujistěte se, že brána firewall neblokuje port TCP 445.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Připojení sdílené složky

Připojte sdílenou složku Azure do místního adresáře. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

Výše uvedený příkaz používá příkaz [mount](https://linux.die.net/man/8/mount) k připojení sdílené složky Azure a možnosti specifické pro [cIFs](https://linux.die.net/man/8/mount.cifs). Konkrétně možnosti file_mode a dir_mode nastavují `0777`soubory a adresáře na oprávnění . Oprávnění `0777` poskytuje oprávnění ke čtení, zápisu a spouštění všem uživatelům. Tato oprávnění můžete změnit nahrazením hodnot jinými [oprávněními chmod](https://en.wikipedia.org/wiki/Chmod). Můžete také použít jiné možnosti [cifs,](https://linux.die.net/man/8/mount.cifs) jako je gid nebo uid. 


## <a name="persist-the-mount"></a>Zachovat držák

Při restartování virtuálního počítače s Linuxem je připojená sdílená smb Protokolu SMB během vypnutí odpojena. Chcete-li znovu připojit sdílenou složku SMB při startu, přidejte řádek do Linuxu /etc/fstab. Linux používá soubor fstab k seznamu souborových systémů, které potřebuje připojit během spouštěcího procesu. Přidání sdílené složky SMB zajistí, že sdílená složka úložiště souborů je trvale připojený souborový systém pro virtuální počítač s Linuxem. Přidání sdílené složky SMB úložiště souborů do nového virtuálního provozu je možné při použití cloud-init.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Pro zvýšení zabezpečení v produkčním prostředí byste měli ukládat pověření mimo fstab.

## <a name="next-steps"></a>Další kroky

- [Přizpůsobení virtuálního počítače s Linuxem při vytváření pomocí cloud-initu](using-cloud-init.md)
- [Přidání disku do virtuálního počítače s Linuxem](add-disk.md)
- [Šifrování disku Azure pro virtuální počítače s Linuxem](disk-encryption-overview.md)

