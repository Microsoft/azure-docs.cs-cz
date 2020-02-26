---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09fa612e7e5c681da16bf19e94c626ae14a3b8a1
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77590718"
---
Když přidáváte datové disky do virtuálního počítače se systémem Linux, může dojít k chybám, pokud disk neexistuje na logické jednotce (LUN) 0. Pokud přidáváte disk ručně pomocí příkazu `az vm disk attach -new` a zadáte LOGICKou hodnotu (`--lun`) místo toho, aby platforma Azure mohla určit příslušnou logickou jednotku (LUN), je nutné zajistit, aby disk již existoval, nebo bude existovat na logické jednotce (LUN) 0. 

Vezměte v úvahu následující příklad znázorňující fragment výstupu z `lsscsi`:

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

Dva datové disky existují na logických jednotkách 0 a LUN 1 (první sloupec v podrobnostech `lsscsi` výstup `[host:channel:target:lun]`). Oba disky by měly být přístupné z virtuálního počítače. Pokud jste ručně zadali první disk, který se má přidat na logickou jednotku 1 a druhý disk na logické jednotce 2, nemusíte na svém VIRTUÁLNÍm počítači správně vidět disky.

> [!NOTE]
> Hodnota `host` Azure je 5 v těchto příkladech, ale může se lišit v závislosti na zvoleném typu úložiště.
> 
> 

Toto chování disku není problém Azure, ale způsob, jakým jádro Linux sleduje specifikace SCSI. Když jádro systému Linux naskenuje sběrnici SCSI pro připojená zařízení, musí se zařízení najít na logické jednotce 0, aby systém pokračoval ve vyhledávání dalších zařízení. Jako takový:

* Po přidání datového disku zkontrolujte výstup `lsscsi`, abyste ověřili, že máte disk s logickou jednotkou 0.
* Pokud se disk na vašem VIRTUÁLNÍm počítači nezobrazuje správně, ověřte, že disk existuje na logické jednotce (LUN) 0.

