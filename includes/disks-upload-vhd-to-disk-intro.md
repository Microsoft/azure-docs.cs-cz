---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420959"
---
Tento článek vysvětluje, jak buď nahrát virtuální pevný disk z místního počítače na spravovaný disk Azure nebo zkopírovat spravovaný disk do jiné oblasti pomocí AzCopy. Tento proces, přímé nahrávání, také umožňuje nahrát VHD až 32 TiB ve velikosti přímo do spravovaného disku. V současné době je přímé nahrávání podporováno pro standardní disky spravované pevným diskem, standardním SSD disky a prémiovými disky spravovanými ssd. To není podporováno pro ultra disky, zatím.

Pokud poskytujete řešení zálohování pro virtuální počítače IaaS v Azure, doporučujeme k obnovení záloh zákazníků na spravované disky použít přímé nahrávání. Při nahrávání virtuálního pevného disku ze zdroje externího do Azure by rychlost závisela na místní šířce pásma. Při nahrávání nebo kopírování z virtuálního počítače Azure by vaše šířka pásma byla stejná jako u standardních pevných disků.