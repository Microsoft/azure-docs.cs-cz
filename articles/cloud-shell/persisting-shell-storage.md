---
title: Zachovat soubory pro Bash v prostředí cloudu Azure | Microsoft Docs
description: Návod, jak Bash v prostředí cloudu Azure potrvají soubory.
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
ms.date: 01/30/2018
ms.author: juluk
ms.openlocfilehash: d8188634846a7ce75b5294cb3012069d9eafafc1
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
ms.locfileid: "28919538"
---
[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-bash-in-cloud-shell-storage-works"></a>Jak funguje Bash v prostředí cloudové úložiště 
Bash v prostředí cloudu potrvají soubory pomocí obě z následujících metod: 
* Vytvoření image disku vaší `$Home` adresáře se zachovat veškerý obsah v adresáři. Bitové kopie disku je uložen v zadané sdílené složky jako `acc_<User>.img` v `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`, a je automaticky synchronizuje změny. 
* Připojení zadané sdílené složky jako `clouddrive` ve vaší `$Home` adresář pro přímé sdílení souborů interakce. `/Home/<User>/clouddrive`se mapuje na `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Všechny soubory ve vašem `$Home` adresář, jako jsou klíče SSH, jsou nastavené jako trvalé v bitové kopii disku uživatele, který je uložený v připojené sdílené složky. Použít osvědčené postupy při zachování informace ve vaší `$Home` adresář a připojené sdílené složky.

## <a name="use-the-clouddrive-command"></a>Použití `clouddrive` příkaz
S Bash v prostředí cloudu, můžete spustit příkaz s názvem `clouddrive`, což umožňuje ručně aktualizovat sdílené složky, která je připojena k cloudové prostředí.
![Spuštěním příkazu "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>Připojit nové clouddrive

### <a name="prerequisites-for-manual-mounting"></a>Předpoklady pro ruční připojení
Sdílené složky, který je spojen s cloudové prostředí pomocí můžete aktualizovat `clouddrive mount` příkaz.

Pokud připojíte existující sdílené složky, musí být účty úložiště:
* Místně redundantní úložiště nebo geograficky redundantní úložiště pro podporu sdílených složek.
* Nachází se ve vašem regionu přiřazené. Po registraci oblasti jsou přiřazeny k je uvedena v názvu skupiny prostředků `cloud-shell-storage-<region>`.

### <a name="the-clouddrive-mount-command"></a>Příkaz clouddrive připojení

> [!NOTE]
> Pokud jste připojení nové sdílené složky, vytvoří se nové uživatelské image pro vaše `$Home` adresáře. Váš předchozí `$Home` bitové kopie je uložen v předchozí sdílené složky.

Spustit `clouddrive mount` příkazu s následujícími parametry:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Chcete-li zobrazit další podrobnosti, spusťte `clouddrive mount -h`, jak je vidět tady:

![Spuštění ' clouddrive mount'command](media/persisting-shell-storage/mount-h.png)

## <a name="unmount-clouddrive"></a>Odpojení clouddrive
Sdílení souborů, které je připojené k prostředí cloudu kdykoli můžete odpojit. Vzhledem k tomu, že cloudové prostředí vyžaduje připojené sdílené složky, který se má použít, budete vyzváni k vytvořit a připojit jiné sdílené složky na další relace.

1. Spusťte `clouddrive unmount`.
2. Na vědomí a potvrďte výzvy.

Sdílené složky budou nadále existovat, pokud chcete odstranit ručně. Cloudové prostředí už Hledat pro tuto sdílenou složku na následné relace. Chcete-li zobrazit další podrobnosti, spusťte `clouddrive unmount -h`, jak je vidět tady:

![Spuštění ' clouddrive unmount'command](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> I když se spustí tento příkaz nebude odstranit všechny prostředky, ručním odstranění skupiny prostředků, účet úložiště nebo sdílené složky, který je namapovaný na cloudové prostředí vymaže vaší `$Home` image disku adresáře a soubory ve sdílené složce. Tuto akci nelze vrátit zpět.

## <a name="list-clouddrive"></a>Seznam`clouddrive`
Chcete-li zjistit, které sdílené složky je připojit jako `clouddrive`spusťte `df` příkaz. 

Cestu k souboru na clouddrive ukazuje, že váš název účtu úložiště a sdílených složek v adrese URL. Například `//storageaccountname.file.core.windows.net/filesharename`.

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

[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Další postup
[Bash v prostředí cloudu rychlý start](quickstart.md) <br>
[Další informace o ukládání souborů aplikace Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Další informace o značkách úložiště](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
