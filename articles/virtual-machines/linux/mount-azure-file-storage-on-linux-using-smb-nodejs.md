---
title: Připojení Azure File storage na virtuální počítače s Linuxem pomocí protokolu SMB 1.0 rozhraní příkazového řádku Azure | Microsoft Docs
description: Tom, jak připojit Azure File storage na virtuální počítače s Linuxem pomocí protokolu SMB
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/07/2016
ms.author: v-livech
ms.openlocfilehash: 442c08a03ff3eb8e4c86f8190e16b74744aa9dd3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="mount-azure-file-storage-on-linux-vms-by-using-smb-with-azure-cli-10"></a>Připojení Azure File storage na virtuální počítače s Linuxem pomocí protokolu SMB 1.0 rozhraní příkazového řádku Azure

Tento článek ukazuje, jak připojit Azure File storage na virtuální počítač s Linuxem pomocí protokolu Server Message Block (SMB). File storage nabízí sdílené složky v cloudu přes standardní protokol SMB. Požadavky:

* [Účet Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Secure Shell (SSH) soubory veřejného a privátního klíče](mac-create-ssh-keys.md)

## <a name="cli-versions-to-use"></a>Verze rozhraní příkazového řádku používat
Úlohu můžete dokončit pomocí jedné z následujících verzí rozhraní příkazového řádku (CLI):

- [Azure CLI 1.0](#quick-commands) – naše rozhraní příkazového řádku pro classic a resource správu modelech nasazení (v tomto článku)
- [Azure CLI 2.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)-naší nové generace rozhraní příkazového řádku pro model nasazení správy prostředků


## <a name="quick-commands"></a>Rychlé příkazy
K provedení úlohy rychle, postupujte podle kroků v této části. Podrobné informace a kontext, začít ve ["Podrobný návod"](mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough) části.

### <a name="prerequisites"></a>Požadavky
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
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>Zachovat připojení po restartu systému
Přidejte následující řádek na `/etc/fstab`:

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Podrobný postup

File storage nabízí sdílené složky v cloudu, které používají standardní protokol SMB. Nejnovější verze služby úložiště File můžete také připojit sdílenou složku z jakékoli operační systém, který podporuje protokol SMB 3.0. Pokud používáte připojení protokolu SMB v systému Linux, získáte snadno zálohy robustní, trvalé archivováním umístění úložiště podporovaný SLA.

Přesunutí souborů z virtuálního počítače připojení protokolu SMB, který je hostován úložiště souborů je skvělým způsobem, jak ladit protokoly. Je to způsobeno téže sdílené složky protokolu SMB může být připojen místně do pracovní stanice se systémem Mac, Linux nebo Windows. SMB není nejlepší řešení pro streamování Linux nebo aplikace protokolů v reálném čase, protože není protokol SMB vytvořené pro zpracování těchto funkcí velkou protokolování. Nástroje protokolování vyhrazené, jednotná vrstvy, jako je Fluentd bude vhodnější než SMB pro shromažďování Linux a aplikace protokolování výstupu.

Pro tento podrobný návod jsme vytvořte součásti potřebné nejprve vytvořit sdílenou složku úložiště a pak připojte prostřednictvím protokolu SMB na virtuální počítač s Linuxem.

1. Vytvořte účet úložiště Azure pomocí následujícího kódu:

    ```azurecli
    azure storage account create myStorageAccount \
    --sku-name lrs \
    --kind storage \
    -l westus \
    -g myResourceGroup
    ```

2. Zobrazit klíče účtu úložiště.

    Když vytvoříte účet úložiště, klíče účtu jsou vytvořeny v párech, aby se mohou otáčet bez výpadku služby. Když přepnete na druhý klíč v páru, můžete vytvořit nový pár klíčů. Nových klíčů účtu úložiště se vytváří vždy v párech, a že jste vždy k dispozici alespoň jeden klíč nepoužívané úložiště připravené přepnout do. Chcete-li zobrazit klíče účtu úložiště, použijte následující kód:

    ```azurecli
    azure storage account keys list myStorageAccount \
    --resource-group myResourceGroup
    ```
3. Vytvořte sdílenou složku úložiště.

    Sdílenou složku úložiště obsahuje sdílenou složku SMB. Kvóta je vždy vyjádřené v gigabajtech (GB). Pokud chcete vytvořit sdílenou složku úložiště, použijte následující kód:

    ```azurecli
    azure storage share create mystorageshare \
    --quota 10 \
    --account-name myStorageAccount \
    --account-key nPOgPR<--snip-->4Q==
    ```

4. Vytvořte adresář přípojného bodu.

    Je nutné vytvořit místní adresář v souborovém systému Linux připojit sdílenou složku SMB. Nic zapsané nebo čtení z adresáře místní připojení se předají do složky SMB, který je hostován na úložiště File. Chcete-li vytvořit adresář, použijte následující kód:

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

5. Připojte sdílenou složku SMB pomocí následujícího kódu:

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
    ```

6. Zachovat připojení SMB prostřednictvím restartování počítače.

    Po restartování virtuálního počítače s Linuxem, je při vypnutí nepřipojené připojené sdílenou složku SMB. Pro opětovné připojení do sdílené složky protokolu SMB na spouštění, musí přidá řádek do Linux /etc/fstab. Linux používá soubor fstab zobrazte seznam systémů souborů, které je potřeba připojit během spouštění. Přidání sdílené složky SMB zajistí, že sdílené složky úložiště bude trvale připojeného souboru systém pro virtuální počítač s Linuxem. Přidání úložiště File sdílená složka SMB na nový virtuální počítač je možné, pokud používáte cloudové init.

    ```bash
    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>Další postup

- [Přizpůsobení virtuálního počítače s Linuxem během vytváření pomocí init cloudu](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Přidání disku do virtuálního počítače s Linuxem](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Šifrování disky na virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
