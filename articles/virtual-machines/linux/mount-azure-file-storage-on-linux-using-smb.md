---
title: Připojení služby Azure File Storage na virtuálních počítačích se systémem Linux pomocí protokolu SMB
description: Postup připojení služby Azure File Storage na virtuálních počítačích se systémem Linux pomocí protokolu SMB pomocí rozhraní příkazového řádku Azure
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: 6a5d4f388d9e5f47a13812687489525590c520c3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016093"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Připojení služby Azure File Storage na virtuálních počítačích se systémem Linux pomocí protokolu SMB

V tomto článku se dozvíte, jak používat službu Azure File Storage na virtuálním počítači se systémem Linux pomocí připojení SMB pomocí Azure CLI. Služba Azure File Storage nabízí sdílené složky v cloudu pomocí standardního protokolu SMB. 

File Storage nabízí sdílené složky v cloudu, které používají standardní protokol SMB. Sdílenou složku můžete připojit z libovolného operačního systému, který podporuje SMB 3,0. Při použití připojení SMB v systému Linux získáte snadné zálohování robustního a trvalého úložiště archivace, které je podporováno smlouvou SLA.

Přesunutí souborů z virtuálního počítače do připojení SMB hostovaného v úložišti souborů je skvělým způsobem, jak ladit protokoly. Stejnou sdílenou složku protokolu SMB lze místně připojit k pracovní stanici Mac, Linux nebo Windows. SMB není nejlepším řešením pro streamování protokolů Linux nebo aplikací v reálném čase, protože protokol SMB není sestavený tak, aby zpracovávala takové těžké protokolovací funkce. Vyhrazený Nástroj pro vrstvu jednotného protokolování, jako je například Fluent, je lepší volbou než protokol SMB pro shromažďování výstupu protokolování pro Linux a aplikace.

Tato příručka vyžaduje, abyste spustili Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu **az --version**. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte v umístění *východní USA* skupinu prostředků s názvem *myResourceGroup* .

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

V rámci vytvořené skupiny prostředků vytvořte nový účet úložiště pomocí [AZ Storage Account Create](/cli/azure/storage/account). Tento příklad vytvoří účet úložiště s názvem *mySTORAGEACCT \<random number>* a vloží název tohoto účtu úložiště do proměnné **STORAGEACCT**. Názvy účtů úložiště musí být jedinečné, použití `$RANDOM` čísla ke konci připojovat k jedinečnému názvu.

```azurecli
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>Získat klíč úložiště

Když vytvoříte účet úložiště, klíče účtu se vytvoří ve dvojicích, aby se mohly otáčet bez přerušení služby. Když přepnete na druhý klíč ve dvojici, vytvoříte nový pár klíčů. Nové klíče účtu úložiště se vždycky vytvářejí ve dvojicích, takže vždycky budete mít aspoň jeden nepoužívaný klíč účtu úložiště připravený k přepnutí na.

Zobrazte klíče účtu úložiště pomocí funkce [AZ Storage Account Keys list](/cli/azure/storage/account/keys). Tento příklad uloží hodnotu klíče 1 do proměnné **STORAGEKEY** .

```azurecli
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Vytvoření sdílené složky

Pomocí [AZ Storage Share Create](/cli/azure/storage/share)vytvořte sdílenou složku úložiště souborů. 

Názvy sdílených složek musí obsahovat malá písmena, číslice a spojovníky, ale nemohou začínat spojovníkem. Kompletní informace o zadávání názvů sdílených složek a souborů najdete v tématu [Pojmenování a odkazování na sdílené složky, soubory a metadata](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata).

Tento příklad vytvoří sdílenou složku s názvem *myshare* s kvótou 10-GIB. 

```azurecli
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Vytvořit přípojný bod

Pokud chcete sdílenou složku Azure připojit k počítači se systémem Linux, musíte se ujistit, že máte nainstalovaný balíček **CIFS-util** . Pokyny k instalaci najdete v tématu [instalace balíčku CIFS-utils pro distribuci systému Linux](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Azure Files používá protokol SMB, který komunikuje přes port TCP 445.  Pokud máte potíže s připojením sdílené složky Azure, ujistěte se, že brána firewall neblokuje port TCP 445.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Připojení sdílené složky

Připojte sdílenou složku Azure k místnímu adresáři. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

Výše uvedený příkaz používá příkaz [Mount](https://linux.die.net/man/8/mount) k připojení sdílené složky Azure a možností specifických pro [CIFS](https://linux.die.net/man/8/mount.cifs). Konkrétně možnosti file_mode a dir_mode nastaví soubory a adresáře na oprávnění `0777` . `0777`Oprávnění poskytuje oprávnění ke čtení, zápisu a spouštění pro všechny uživatele. Tato oprávnění můžete změnit nahrazením hodnot pomocí dalších [oprávnění chmod](https://en.wikipedia.org/wiki/Chmod). Můžete také použít další možnosti [CIFS](https://linux.die.net/man/8/mount.cifs) , například GID nebo UID. 


## <a name="persist-the-mount"></a>Zachovat připojení

Po restartování virtuálního počítače se systémem Linux bude připojená sdílená složka SMB odpojena během vypínání. Pokud chcete po spuštění znovu připojit sdílenou složku SMB, přidejte řádek do adresáři/etc/fstab. pro Linux. Linux používá soubor fstab k vypsání systémů souborů, které musí připojit během procesu spouštění. Přidání sdílené složky SMB zajistí, že sdílená složka úložiště je trvale připojeným systémem souborů pro virtuální počítač se systémem Linux. Přidání sdílené složky SMB úložiště souborů do nového virtuálního počítače je možné, když použijete Cloud-init.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Pro zvýšení zabezpečení v produkčních prostředích byste měli přihlašovací údaje ukládat mimo fstab.

## <a name="next-steps"></a>Další kroky

- [Přizpůsobení virtuálního počítače se systémem Linux během vytváření pomocí Cloud-init](using-cloud-init.md)
- [Přidání disku do virtuálního počítače s Linuxem](add-disk.md)
- [Azure Disk Encryption pro virtuální počítače s Linuxem](disk-encryption-overview.md)
