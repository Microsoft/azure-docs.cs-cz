---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4d2c0a02a48c0e04b501f136f66c28b3f532e2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486023"
---
Přírůstkové snímky jsou zálohy bodu v čase pro spravované disky, které se při pořízení skládají pouze ze všech změn od posledního snímku. Při pokusu o stažení nebo jiné použití přírůstkového snímku se použije celý virtuální pevný disk. Tato nová funkce pro snímky spravovaného disku potenciálně umožňuje, aby byly nákladově efektivnější, protože pokud se rozhodnete, není třeba ukládat celý disk s každým jednotlivým snímek. Stejně jako běžné snímky, přírůstkové snímky lze použít k vytvoření úplného spravovaného disku nebo k vytvoření pravidelného snímku.

Existuje několik rozdílů mezi přírůstkový snímek a pravidelný snímek. Přírůstkové snímky budou vždy používat standardní úložiště pevných disků, bez ohledu na typ úložiště disku, zatímco běžné snímky mohou používat prémiové disky SSD. Pokud používáte běžné snímky v úložišti Premium k škálování nasazení virtuálních počítačů, doporučujeme použít vlastní image ve standardním úložišti v [Galerii sdílených obrázků](../articles/virtual-machines/linux/shared-image-galleries.md). To vám pomůže dosáhnout masivnější stupnice s nižšími náklady. Přírůstkové snímky navíc potenciálně nabízejí lepší spolehlivost s [zónově redundantním úložištěm](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Pokud ZRS je k dispozici ve vybrané oblasti, přírůstkové snímek bude používat ZRS automaticky. Pokud ZRS není k dispozici v oblasti, pak snímek bude výchozí [místně redundantní úložiště](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Toto chování můžete přepsat a vybrat ručně, ale nedoporučujeme to.

Přírůstkové snímky také nabízejí možnost rozdílu, k dispozici pouze spravované disky. Umožňují získat změny mezi dvěma přírůstkovými snímky stejných spravovaných disků až na úroveň bloku. Tuto možnost můžete použít ke snížení nároku na data při kopírování snímků napříč oblastmi.  Můžete například stáhnout první přírůstkový snímek jako základní objekt blob v jiné oblasti. Pro následné přírůstkové snímky můžete zkopírovat pouze změny od posledního snímku do základního objektu blob. Po zkopírování změn můžete pořizovat snímky na základní objekt blob, které představují vaše bod v čase zálohování disku v jiné oblasti. Disk můžete obnovit buď ze základního objektu blob, nebo ze snímku na základním objektu blob v jiné oblasti.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="Diagram znázorňující přírůstkové snímky zkopírované napříč oblastmi. Snímky provádět různá volání rozhraní API, dokud nakonec tvoří objekty BLOB stránky na každý snímek.":::