---
title: Zachovat soubory v Azure Cloud Shell | Microsoft Docs
description: Návod, jak Azure Cloud Shell přetrvává soubory
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: f60125123d019cbfa93bfc1b06da7ac90b54e311
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742040"
---
[!INCLUDE [PersistingStorage-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>Jak funguje úložiště Cloud Shell 
Cloud Shell uchovává soubory prostřednictvím obou následujících metod: 
* Vytvořením bitové kopie `$Home` disku v adresáři zachovejte veškerý obsah v adresáři. Bitová kopie disku je uložena v zadané sdílené složce jako `acc_<User>.img` at `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`a automaticky synchronizuje změny. 
* Vaše Zadaná sdílená složka se `clouddrive` připojuje jako `$Home` v adresáři pro přímou interakci sdílení souborů. `/Home/<User>/clouddrive`je namapována na `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Všechny soubory ve vašem `$Home` adresáři, jako jsou klíče SSH, jsou trvalé na uživatelském disku, který je uložený v připojené sdílené složce souborů. Pokud uchováváte informace ve vašem `$Home` adresáři a připojené sdílené složce, použijte doporučené postupy.

## <a name="bash-specific-commands"></a>Příkazy specifické pro bash

### <a name="use-the-clouddrive-command"></a>`clouddrive` Použití příkazu
Pomocí bash v Cloud Shell můžete spustit příkaz s názvem `clouddrive`, který umožňuje ručně aktualizovat sdílenou složku, která je připojená k Cloud Shell.
![Spuštění příkazu "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

### <a name="mount-a-new-clouddrive"></a>Připojit nový clouddrive

#### <a name="prerequisites-for-manual-mounting"></a>Předpoklady ručního připojení
Sdílenou složku, která je přidružená k Cloud Shell, můžete aktualizovat pomocí `clouddrive mount` příkazu.

Pokud připojíte existující sdílenou složku, musí být účty úložiště umístěny ve vaší vybrané Cloud Shell oblasti. Načtěte umístění spuštěním `env` z bash a `ACC_LOCATION`kontrolou.

#### <a name="the-clouddrive-mount-command"></a>`clouddrive mount` Příkaz

> [!NOTE]
> Pokud připojujete novou sdílenou složku, vytvoří se pro váš `$Home` adresář nová uživatelská image. Předchozí `$Home` obrázek je uložený v předchozí sdílené složce.

`clouddrive mount` Spusťte příkaz s následujícími parametry:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Pokud chcete zobrazit další podrobnosti, `clouddrive mount -h`spusťte, jak je znázorněno zde:

![Spuštění clouddrive mount'command](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Odpojit clouddrive
Sdílenou složku, která je připojená k Cloud Shell, můžete kdykoli odpojit. Vzhledem k tomu, že Cloud Shell vyžaduje, aby se použila připojená sdílená složka, budete při další relaci vyzváni k vytvoření a připojení další sdílené složky.

1. Spusťte `clouddrive unmount`.
2. Potvrďte a potvrďte výzvy.

Sdílená složka bude i nadále existovat, dokud ji neodstraníte ručně. Cloud Shell už po dalších relacích tuto sdílenou složku nebude hledat. Pokud chcete zobrazit další podrobnosti, `clouddrive unmount -h`spusťte, jak je znázorněno zde:

![Spuštění clouddrive unmount'command](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> I když se spustí tento příkaz, neodstraní žádné prostředky, ručně odstraní skupinu prostředků, účet úložiště nebo sdílenou složku, která je namapovaná na Cloud Shell `$Home` vymaže vaši image disku adresáře a všechny soubory ve sdílené složce. Tato akce je nevratná

### <a name="list-clouddrive"></a>Seznamu`clouddrive`
Pokud chcete zjistit `clouddrive`, která sdílená složka je připojená `df` , spusťte příkaz. 

Cesta k souboru clouddrive zobrazuje název vašeho účtu úložiště a sdílenou složku v adrese URL. Například `//storageaccountname.file.core.windows.net/filesharename`.

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
## <a name="powershell-specific-commands"></a>Příkazy specifické pro PowerShell

### <a name="list-clouddrive-azure-file-shares"></a>Vypsat `clouddrive` sdílené složky Azure
Rutina načte informace o sdílené složce Azure, které jsou `clouddrive` aktuálně připojené v Cloud Shell. `Get-CloudDrive` <br>
![Spuštění Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Odpojte`clouddrive`
Sdílenou složku Azure, která je připojená k Cloud Shell, můžete kdykoli odpojit. Pokud se sdílená složka Azure odebrala, budete při další relaci vyzváni k vytvoření nové sdílené složky Azure a připojení k ní.

`Dismount-CloudDrive` Rutina odpojí sdílenou složku Azure od aktuálního účtu úložiště. Odpojení `clouddrive` ukončí aktuální relaci. Uživatel bude při další relaci vyzván k vytvoření a připojení nové sdílené složky Azure.
![Spuštění odpojení – CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Poznámka: Pokud potřebujete definovat funkci v souboru a zavolat ji z rutin PowerShellu, musí být zahrnut operátor tečka. Například:. .\MyFunctions.ps1

## <a name="next-steps"></a>Další postup
[Bash v rychlém startu Cloud Shell](quickstart.md) <br>
[PowerShell v Cloud Shell rychlý Start](quickstart-powershell.md) <br>
[Informace o úložišti Microsoft Azurech souborů](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[Další informace o značkách úložiště](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
