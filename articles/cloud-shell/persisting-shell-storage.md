---
title: Zachovejte soubory pro Bash ve službě Azure Cloud Shell | Dokumentace Microsoftu
description: Návod, jak prostředí Bash ve službě Azure Cloud Shell nevyřeší soubory.
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: juluk
ms.openlocfilehash: 606b36be4a2bbeff8dd226f41341d60e23f0d988
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163273"
---
[!INCLUDE [PersistingStorage-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>Jak funguje Cloud Shell úložiště 
Cloud Shell nevyřeší soubory prostřednictvím obě z následujících metod: 
* Vytvoření bitové kopie disku vaše `$Home` adresáře a zachová veškerý obsah v rámci adresáře. Image disku je uložen v zadané sdílené složce jako `acc_<User>.img` na `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`, a automaticky synchronizuje změny. 
* Připojení jako zadané sdílené složce `clouddrive` ve vaší `$Home` adresáře pro interakci s přímým přístupem sdílenou složku. `/Home/<User>/clouddrive` je namapovaný na `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Všechny soubory ve vašich `$Home` adresář, jako jsou klíče SSH, zůstávají ve vaší imagi disku uživatele, který je uložen v adresáři připojené sdílené složky. Použít osvědčené postupy při uchovávání informací ve vaší `$Home` adresáře a připojené sdílené složky.

## <a name="bash-specific-commands"></a>Příkazy bash konkrétní

### <a name="use-the-clouddrive-command"></a>Použití `clouddrive` příkaz
Bash ve službě Cloud Shell, můžete spustit příkaz, volána `clouddrive`, což vám umožní ručně aktualizovat sdílené složce, která se připojí k službě Cloud Shell.
![Spuštěním příkazu "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

### <a name="mount-a-new-clouddrive"></a>Připojte nový clouddrive

#### <a name="prerequisites-for-manual-mounting"></a>Požadavky pro ruční připojení
Sdílené složce, která je přidružená Cloud Shell pomocí můžete aktualizovat `clouddrive mount` příkazu.

Pokud existující sdílenou složku připojíte, účty úložiště musí být umístěné ve vaší oblasti vyberte Cloud Shell. Načíst umístění spuštěním `env` z prostředí Bash a kontrolu `ACC_LOCATION`.

#### <a name="the-clouddrive-mount-command"></a>`clouddrive mount` Příkaz

> [!NOTE]
> Pokud jste připojení nové sdílené složky, vytvoří se nové uživatelské image pro vaše `$Home` adresáře. Vaše předchozí `$Home` image se ukládají v předchozí sdílené složce.

Spustit `clouddrive mount` příkaz s následujícími parametry:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Chcete-li zobrazit další podrobnosti, spusťte `clouddrive mount -h`, jak je znázorněno zde:

![Spuštění "clouddrive mount'command](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Odpojte clouddrive
Můžete odpojit sdílenou složku, která je připojený k službě Cloud Shell v každém okamžiku. Protože Cloud Shell vyžaduje připojené sdílené složky pro použití, zobrazí výzva k vytvoření a připojení jiné sdílené složky v příští relaci.

1. Spusťte `clouddrive unmount`.
2. Berete na vědomí a potvrďte výzvy.

Sdílené složky budou nadále existovat, dokud je neodstraníte ručně. Cloud Shell vyhledá již pro tuto sdílenou složku v dalších relacích. Chcete-li zobrazit další podrobnosti, spusťte `clouddrive unmount -h`, jak je znázorněno zde:

![Spuštění "clouddrive unmount'command](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> I když spustíte tento příkaz neodstraní všechny prostředky, ručně odstranit skupinu prostředků, účet úložiště nebo sdílenou složku, která je namapovaná na službě Cloud Shell vymaže váš `$Home` image disku adresář a všechny soubory ve sdílené složce. Tato akce je nevratná

### <a name="list-clouddrive"></a>Seznam `clouddrive`
Chcete-li zjistit, které sdílenou složku je připojený jako `clouddrive`, spusťte `df` příkaz. 

Cesta k souboru na clouddrive ukazuje, že váš název účtu úložiště a sdílených složek v adrese URL. Například `//storageaccountname.file.core.windows.net/filesharename`.

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```
## <a name="powershell-specific-commands"></a>Příkazů specifických pro prostředí PowerShell

### <a name="list-clouddrive-azure-file-shares"></a>Seznam `clouddrive` sdílených složek Azure
`Get-CloudDrive` Rutina načte informace sdílené složky Azure file aktuálně připojit `clouddrive` ve službě Cloud Shell. <br>
![Spuštění Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Odpojení `clouddrive`
Sdílené složky Azure, který je připojený k službě Cloud Shell v každém okamžiku může odpojit. Pokud sdílená složka Azure se odebral, budete vyzváni k vytvořit a připojit novou sdílenou složku Azure file v příští relaci.

`Dismount-CloudDrive` Rutina odpojí z aktuálního účtu úložiště sdílené složky Azure. Odpojení `clouddrive` ukončí aktuální relaci. Uživatel se vyzve k vytvořit a připojit novou sdílenou složku Azure file během příští relaci.
![Spuštění Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Další postup
[Bash v Cloud Shellu Quickstart](quickstart.md) <br>
[Prostředí PowerShell ve službě Cloud Shell Quickstart](quickstart-powershell.md) <br>
[Další informace o Microsoft Azure Files storage](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Další informace o značkách úložiště](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
