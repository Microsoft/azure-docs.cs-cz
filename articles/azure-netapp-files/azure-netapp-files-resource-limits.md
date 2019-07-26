---
title: Omezení prostředků pro Azure NetApp Files | Microsoft Docs
description: Popisuje omezení pro Azure NetApp Files prostředky, včetně omezení pro účty NetApp, fondy kapacit, svazky, snímky a delegovanou podsíť.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: b-juche
ms.openlocfilehash: fe8d46c897ef68563f2e0e5a1da106174ae504c5
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424086"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Omezení prostředků pro Azure NetApp Files

Principy omezení prostředků pro Azure NetApp Files vám pomůžou spravovat svazky.

## <a name="resource-limits"></a>Omezení prostředků

Následující tabulka popisuje omezení prostředků pro Azure NetApp Files:

|  Resource  |  Výchozí omezení  |  Nastavitelná prostřednictvím žádosti o podporu  |
|----------------|---------------------|--------------------------------------|
|  Počet účtů NetApp na předplatné Azure   |  10    |  Ano   |
|  Počet fondů kapacity na účet NetApp   |    25     |   Ano   |
|  Počet svazků na fond kapacity     |    500   |    Ano     |
|  Počet snímků na svazek       |    255     |    Ne        |
|  Počet podsítí delegovaných pro Azure NetApp Files (Microsoft. NetApp/svazky) na Azure Virtual Network    |   1   |    Ne    |
|  Maximální počet IP adres používaných ve virtuální síti (včetně partnerských virtuální sítě) s Azure NetApp Files    |    1000   |    Ne   |
|  Minimální velikost jednoho fondu kapacity   |  4 TiB     |    Ne  |
|  Maximální velikost jednoho fondu kapacity    |  500 TiB   |   Ne   |
|  Minimální velikost jednoho svazku    |    100 GiB    |    Ne    |
|  Maximální přiřazená kvóta jednoho svazku *   |   92 TiB   |    Ne   |
|  Maximální velikost jednoho svazku *     |    100 TiB    |    Ne       |

\* Svazek se dá ručně vytvořit nebo změnit jeho velikost na maximální 92 TiB. Svazek ale může růst až 100 TiB ve scénáři překročení. Podrobnosti o překročení kapacity najdete v tématu [cost model pro Azure NetApp Files](azure-netapp-files-cost-model.md) . 

## <a name="request-limit-increase"></a>Zvýšení limitu požadavku 

Můžete vytvořit žádost o podporu Azure, která zvýší nastavitelná omezení z výše uvedené tabulky. 

Z Azure Portal rovina navigace: 

1. Klikněte na tlačítko **help + podpora**.
2. Klikněte na **+ Nová žádost o podporu**.
3. Na kartě základy zadejte následující informace: 
    1. Typ problému: Vyberte **omezení služby a předplatné (kvóty)** .
    2. Předplatná: Vyberte předplatné pro prostředek, u kterého potřebujete kvótu zvýšit.
    3. Typ kvóty: Vyberte **úložiště: Omezení**Azure NetApp Files.
    4. Klikněte **na další: Řešení**.
4. Na kartě Podrobnosti:
    1. V poli Popis zadejte pro odpovídající typ prostředku následující informace:

        |  Resource  |    Nadřazené prostředky      |    Požadovaná nová omezení     |    Důvod zvýšení kvóty       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Účet |  *ID předplatného*   |  *Požadované nové maximální číslo **účtu***    |  *Jaký scénář nebo případ použití se žádostí vyzve?*  |
        |  Fond    |  *ID předplatného, identifikátor URI účtu*  |  *Požadované nové maximální číslo **fondu***   |  *Jaký scénář nebo případ použití se žádostí vyzve?*  |
        |  Svazek  |  *ID předplatného, identifikátor URI účtu, identifikátor URI fondu*   |  *Požadované nové maximální číslo **svazku***     |  *Jaký scénář nebo případ použití se žádostí vyzve?*  |

    2. Zadejte odpovídající způsob podpory a zadejte informace o kontraktu.

    3. Klikněte **na další: K vytvoření žádosti** si Projděte + Create. 


## <a name="next-steps"></a>Další postup  

- [Pochopení hierarchie úložiště Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Nákladový model pro Azure NetApp Files](azure-netapp-files-cost-model.md)
