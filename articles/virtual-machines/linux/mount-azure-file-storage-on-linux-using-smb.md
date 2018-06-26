---
title: Připojení Azure File storage na virtuální počítače s Linuxem pomocí protokolu SMB | Microsoft Docs
description: Tom, jak připojit Azure File storage na virtuální počítače s Linuxem pomocí protokolu SMB 2.0 rozhraní příkazového řádku Azure
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2017
ms.author: iainfou
ms.openlocfilehash: 2255c8fd7cd873ae9b6511e1a7b9e2ac13f9fb66
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936764"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Připojení Azure File storage na virtuální počítače s Linuxem pomocí protokolu SMB

Tento článek ukazuje, jak využívat službu Azure File storage na virtuální počítač s Linuxem pomocí připojení protokolu SMB 2.0 rozhraní příkazového řádku Azure. Azure File storage nabízí sdílené složky v cloudu přes standardní protokol SMB. Požadavky:

- [Účet Azure](https://azure.microsoft.com/pricing/free-trial/)
- [Soubory veřejného a privátního klíče SSH](mac-create-ssh-keys.md)

## <a name="quick-commands"></a>Rychlé příkazy

* Skupinu prostředků.
* Virtuální síť Azure
* Skupina zabezpečení sítě pomocí SSH příchozí
* Podsíť
* Účet úložiště Azure
* Klíče účtu úložiště Azure
* Sdílenou složku Azure File storage
* Virtuální počítač s Linuxem

Nahradí všechny příklady s vlastním nastavením.

### <a name="create-a-directory-for-the-local-mount"></a>Vytvořte adresář pro místního připojení

```bash
mkdir -p /mnt/mymountpoint
```

### <a name="mount-the-file-storage-smb-share-to-the-mount-point"></a>Připojte soubor úložiště do přípojného bodu sdílená složka SMB

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>Zachovat připojení po restartu systému
Uděláte to tak, přidejte následující řádek na `/etc/fstab`:

```bash
//myaccountname.file.core.windows.net/mysharename /mnt/mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Podrobný postup

File storage nabízí sdílené složky v cloudu, které používají standardní protokol SMB. Nejnovější verze služby úložiště File můžete také připojit sdílenou složku z jakékoli operační systém, který podporuje protokol SMB 3.0. Pokud používáte připojení protokolu SMB v systému Linux, získáte snadno zálohy robustní, trvalé archivováním umístění úložiště podporovaný SLA.

Přesunutí souborů z virtuálního počítače připojení protokolu SMB, který je hostován úložiště souborů je skvělým způsobem, jak ladit protokoly. Je to způsobeno téže sdílené složky protokolu SMB může být připojen místně do pracovní stanice se systémem Mac, Linux nebo Windows. SMB není nejlepší řešení pro streamování Linux nebo aplikace protokolů v reálném čase, protože není protokol SMB vytvořené pro zpracování těchto funkcí velkou protokolování. Nástroje protokolování vyhrazené, jednotná vrstvy, jako je Fluentd bude vhodnější než SMB pro shromažďování Linux a aplikace protokolování výstupu.

Pro tento podrobný návod jsme vytvořte součásti potřebné nejprve vytvořit sdílenou složku úložiště a pak připojte prostřednictvím protokolu SMB na virtuální počítač s Linuxem.

1. Vytvořte skupinu prostředků s [vytvořit skupinu az](/cli/azure/group#az_group_create) pro sdílené složky.

    Chcete-li vytvořit skupinu prostředků s názvem `myResourceGroup` v umístění "Západní USA", pomocí následujícího příkladu:

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

2. Vytvoření účtu úložiště Azure s [vytvořit účet úložiště az](/cli/azure/storage/account#az_storage_account_create) ukládat soubory.

    Chcete-li vytvořit účet úložiště s názvem můj_účet_úložiště pomocí úložiště Standard_LRS SKU, použijte následující příklad:

    ```azurecli
    az storage account create --resource-group myResourceGroup \
        --name mystorageaccount \
        --location westus \
        --sku Standard_LRS
    ```

3. Zobrazit klíče účtu úložiště.

    Když vytvoříte účet úložiště, klíče účtu jsou vytvořeny v párech, aby se mohou otáčet bez výpadku služby. Když přepnete na druhý klíč v páru, můžete vytvořit nový pár klíčů. Nových klíčů účtu úložiště se vytváří vždy v párech, a že jste vždy k dispozici alespoň jeden nepoužívané klíč účtu úložiště připravené přepnout do.

    Zobrazit klíče účtu úložiště s [seznam klíčů účtu úložiště az](/cli/azure/storage/account/keys#az_storage_account_keys_list). Účet úložiště klíčů pro pojmenované `mystorageaccount` jsou uvedené v následujícím příkladu:

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount
    ```

    Chcete-li extrahovat jeden klíč, použijte `--query` příznak. Následující příklad extrahuje první klíč (`[0]`):

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount \
        --query '[0].{Key:value}' --output tsv
    ```

4. Vytvořte sdílenou složku úložiště.

    Sdílenou složku úložiště obsahuje sdílené složky SMB s [vytvořit sdílenou složku úložiště az](/cli/azure/storage/share#az_storage_share_create). Kvóta je vždy vyjádřené v gigabajtech (GB). Předejte jí jeden z klíčů z předchozí `az storage account keys list` příkaz. Vytvořte sdílenou složku s názvem mystorageshare s kvótou 10 GB s použitím v následujícím příkladu:

    ```azurecli
    az storage share create --name mystorageshare \
        --quota 10 \
        --account-name mystorageaccount \
        --account-key nPOgPR<--snip-->4Q==
    ```

5. Vytvořte adresář přípojného bodu.

    Vytvořte místní adresář v souborovém systému Linux připojit sdílenou složku SMB. Nic zapsané nebo čtení z adresáře místní připojení se předají do složky SMB, který je hostován na úložiště File. K vytvoření místního adresáře v /mnt/mymountdirectory, použijte následující příklad:

    ```bash
    sudo mkdir -p /mnt/mymountpoint
    ```

6. Připojení sdílené složky SMB do místního adresáře.

    Zadejte vlastní uživatelské jméno účtu úložiště a klíč účtu úložiště přihlašovacích údajů k připojení následujícím způsobem:

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountpoint -o vers=3.0,username=mystorageaccount,password=mystorageaccountkey,dir_mode=0777,file_mode=0777
    ```

7. Zachovat připojení SMB prostřednictvím restartování počítače.

    Po restartování virtuálního počítače s Linuxem, je při vypnutí nepřipojené připojené sdílenou složku SMB. Pro opětovné připojení do sdílené složky protokolu SMB na spuštění, přidejte řádek na Linux /etc/fstab. Linux používá soubor fstab zobrazte seznam systémů souborů, které je potřeba připojit během spouštění. Přidání sdílené složky SMB zajistí, že sdílené složky úložiště bude trvale připojeného souboru systém pro virtuální počítač s Linuxem. Přidání úložiště File sdílená složka SMB na nový virtuální počítač je možné, pokud používáte cloudové init.

    ```bash
    //myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>Další postup

- [Přizpůsobení virtuálního počítače s Linuxem během vytváření pomocí init cloudu](using-cloud-init.md)
- [Přidání disku do virtuálního počítače s Linuxem](add-disk.md)
- [Šifrování disky na virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure](encrypt-disks.md)
