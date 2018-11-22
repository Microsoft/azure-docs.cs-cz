---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 87dd3680aae3e87f78ab2dbe70c44b2008706747
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279530"
---
Při přidávání datové disky na virtuální počítač s Linuxem, chyby můžete narazit, pokud neexistuje disku na logické jednotce 0. Pokud přidáváte ručně pomocí disku `azure vm disk attach-new` příkaz a zadejte logickou jednotku (`--lun`) namísto povolení platformy Azure k určení příslušné logické jednotky, zajistíme, že disk již existuje nebo bude existovat na logické jednotce 0. 

Podívejte se na následující příklad zobrazuje fragment výstup z `lsscsi`:

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

Existují dva datové disky na logická jednotka LUN 0 a 1 logická jednotka (v prvním sloupci `lsscsi` výstupu ukazuje podrobnosti o `[host:channel:target:lun]`). Oba disky by měly být přístupné z v rámci virtuálního počítače. Pokud jste měli ručně zadali první disk má být přidán na logické jednotce 1 a druhý disk na logické jednotce 2, nemusíte vidět disky správně z ve vašem virtuálním počítači.

> [!NOTE]
> Azure `host` hodnota je 5 v těchto příkladech, ale to se může lišit v závislosti na typu úložiště vyberete.
> 
> 

Toto chování disku není problém s Azure, ale způsob, ve kterém linuxového jádra následuje specifikace SCSI. Prohledávání linuxového jádra sběrnice SCSI, hledá připojená zařízení, zařízení musí být nalezen na logická jednotka LUN 0 v pořadí pro systém pokračujte skenování pro další zařízení. Takto:

* Prohlédněte si výstup `lsscsi` po přidání datového disku k ověření, že máte disk na logické jednotce 0.
* Pokud disk není uveden správně ve vašem virtuálním počítači, ověřte, zda že existuje disku na logické jednotce 0.

