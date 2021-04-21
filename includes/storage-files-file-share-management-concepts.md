---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6c54bdc2738f9455926882c439ff8942c1c0a5a6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759755"
---
Sdílené složky Azure se nasazují do *účtů úložiště*, což jsou objekty nejvyšší úrovně, které představují sdílený fond úložiště. Tento fond úložiště se dá použít k nasazení několika sdílených složek a dalších prostředků úložiště, jako jsou kontejnery objektů blob, fronty nebo tabulky. Všechny prostředky úložiště, které jsou nasazené do účtu úložiště, sdílejí omezení vztahující se na tento účet úložiště. Omezení pro aktuální účet úložiště najdete v tématu [škálovatelnost a cíle výkonnosti souborů Azure](../articles/storage/files/storage-files-scale-targets.md).

Existují dva hlavní typy účtů úložiště, které budete používat pro nasazení souborů Azure: 
- **Účty úložiště pro obecné účely verze 2 (GPv2)**: účty úložiště GPv2 umožňují nasadit sdílené složky Azure na hardwaru založeném na standardu a na pevných discích (HDD). Kromě ukládání sdílených složek Azure můžou účty úložiště GPv2 ukládat i další prostředky úložiště, jako jsou kontejnery objektů blob, fronty nebo tabulky. 
- **Účty úložiště** úložiště: účty úložiště úložiště umožňují nasadit sdílené složky Azure na hardware Premium/Solid-State (SSD) na disku (SSD). Účty úložiště souborů se dají použít jenom k ukládání sdílených složek Azure. v účtu úložiště úložiště se nedají nasadit žádné další prostředky úložiště (kontejnery objektů blob, fronty, tabulky atd.). Sdílené složky SMB i NFS můžou nasadit jenom účty úložiště souborů.

V Azure Portal, PowerShellu nebo rozhraní příkazového řádku se může nacházet několik dalších typů účtů úložiště. Dva typy účtů úložiště, účty úložiště BlockBlobStorage a BlobStorage, nemůžou obsahovat sdílené složky Azure. Další dva typy účtů úložiště, které se můžou zobrazovat, jsou obecné účely verze 1 (GPv1) a klasické účty úložiště, z nichž obě můžou obsahovat sdílené složky Azure. I když účty úložiště GPv1 a Classic můžou obsahovat sdílené složky Azure, jsou většina nových funkcí služby Azure Files k dispozici pouze v účtech úložiště GPv2 a úložiště souborů. Proto doporučujeme, abyste pro nová nasazení používali jenom účty úložiště GPv2 a Storage a k upgradu GPv1 a klasických účtů úložiště, pokud už ve svém prostředí existují.  