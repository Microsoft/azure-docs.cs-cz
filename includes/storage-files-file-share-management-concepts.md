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
ms.openlocfilehash: a67ad4c5010cf93ff55123013a35c697ce5971f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597786"
---
Sdílené složky Azure se nasazují do *účtů úložiště*, což jsou objekty nejvyšší úrovně, které představují sdílený fond úložiště. Tento fond úložiště lze použít k nasazení více sdílených složek souborů, stejně jako další prostředky úložiště, jako jsou kontejnery objektů blob, fronty nebo tabulky. Všechny prostředky úložiště, které jsou nasazeny do účtu úložiště, sdílejí limity, které se vztahují k tomuto účtu úložiště. Aktuální limity pro účet úložiště najdete v tématu [Škálovatelnost a výkonnostní cíle Azure Files](../articles/storage/files/storage-files-scale-targets.md).

Existují dva hlavní typy účtů úložiště, které budete používat pro nasazení souborů Azure: 
- **Účty úložiště pro obecné účely verze 2 (GPv2):** Účty úložiště GPv2 umožňují nasazovat sdílené složky Azure na standardním hardwaru založeném na pevném disku (na pevném disku). Kromě ukládání sdílených složek Azure mohou účty úložiště GPv2 ukládat další prostředky úložiště, jako jsou kontejnery objektů blob, fronty nebo tabulky. 
- **Účty úložiště úložiště úložiště Úložiště**souborů : Účty úložiště Úložiště souborů umožňují nasazovat sdílené složky Azure na hardware založený na prémiovém nebo ssd disku (SSD). Účty FileStorage lze použít pouze k ukládání sdílených složek Azure; žádné jiné prostředky úložiště (kontejnery objektů blob, fronty, tabulky atd.) nelze nasadit v účtu FileStorage.

Existuje několik dalších typů účtů úložiště, se kterými se můžete sejít na webu Azure Portal, PowerShellu nebo CLI. Dva typy účtů úložiště, BlockBlobStorage a BlobStorage účty úložiště, nemůže obsahovat sdílené složky Azure. Další dva typy účtů úložiště, které se mohou zobrazit, jsou verze 1 pro obecné účely (GPv1) a klasické účty úložiště, které mohou obsahovat sdílené složky Azure. Přestože účty GPv1 a klasické úložiště mohou obsahovat sdílené složky Azure, většina nových funkcí souborů Azure je dostupná jenom v účtech úložiště GPv2 a FileStorage. Proto doporučujeme používat pouze účty úložiště GPv2 a FileStorage pro nová nasazení a upgradovat účty GPv1 a klasické úložiště, pokud již ve vašem prostředí existují.  