---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 01/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 5658b68081fae8dab528030f7fc1cd6fe4935730
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "102178318"
---
- Pro disk SSD úrovně Premium nelze povolit rozšíření na vyžádání, které má méně než 512 GiB. SSD úrovně Premium, která je nižší než 512 GiB, bude vždycky používat shlukování na základě kreditu.
- Shlukování na vyžádání se podporuje jenom na Premium SSD. Pokud je jednotka SSD úrovně Premium s povoleným roztržením na vyžádání přepnuta na jiný typ disku, bude shlukování disku zakázané.
- Rozpínání na vyžádání se při změně úrovně výkonu automaticky nevypne. Pokud chcete změnit úroveň výkonu, ale nechcete zachovat shlukování disku, je nutné ji zakázat.
- Rozpínání na vyžádání se dá povolit jenom v případě, že se disk odpojí z virtuálního počítače nebo když je virtuální počítač zastavený. Vypínání na vyžádání se dá po uplynutí 12 hodin po povolení vypnout.