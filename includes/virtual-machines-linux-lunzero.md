---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09fa612e7e5c681da16bf19e94c626ae14a3b8a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590718"
---
Při přidávání datových disků do virtuálního počítače s Linuxem může dojít k chybám, pokud disk neexistuje na logické jednotce 0. Pokud přidáváte disk ručně pomocí `az vm disk attach -new` příkazu a zadáte`--lun`logickou jednotku ( ) namísto povolení platformě Azure k určení příslušné logické jednotky, dbejte na to, aby disk již existoval nebo bude existovat na logické jednotce 0. 

Vezměme si následující příklad zobrazující úryvek výstupu z `lsscsi`:

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

Dva datové disky existují na LOGICKÉ jednotce 0 `lsscsi` a `[host:channel:target:lun]`LOGICKÉ jednotce 1 (první sloupec ve výstupních podrobnostech). Oba disky by měly být přístupné z virtuálního počítače. Pokud jste ručně zadali první disk, který má být přidán na LUN 1 a druhý disk na LOGICKÉ jednotce 2, nemusí se disky zobrazit správně z virtuálního počítače.

> [!NOTE]
> Hodnota `host` Azure je 5 v těchto příkladech, ale to se může lišit v závislosti na typu úložiště, které vyberete.
> 
> 

Toto chování disku není problém Azure, ale způsob, jakým linuxové jádro sleduje specifikace SCSI. Když linuxové jádro prohledá sběrnici SCSI pro připojená zařízení, musí být zařízení nalezeno na adrese LUN 0, aby systém mohl pokračovat v hledání dalších zařízení. Jako takové:

* Zkontrolujte výstup `lsscsi` po přidání datového disku a ověřte, zda máte disk na logické jednotce 0.
* Pokud se disk ve virtuálním počítači nezobrazuje správně, ověřte, zda disk existuje na logické jednotce 0.

