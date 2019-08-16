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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 4ce40fdf36f7d66e60e15955318e43f1f24f275f
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515846"
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
|  Počet souběžných IP adres ve virtuální síti (včetně partnerských virtuální sítě), které mají přístup k Azure NetApp Files   |    1000   |    Ne   |
|  Minimální velikost jednoho fondu kapacity   |  4 TiB     |    Ne  |
|  Maximální velikost jednoho fondu kapacity    |  500 TiB   |   Ne   |
|  Minimální velikost jednoho svazku    |    100 GiB    |    Ne    |
|  Maximální velikost jednoho svazku     |    100 TiB    |    Ne       |
|  Maximální počet souborů (uzlů inode) na jeden svazek     |    50 000 000    |    Ne    |    

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


## <a name="next-steps"></a>Další kroky  

- [Pochopení hierarchie úložiště Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Nákladový model pro Azure NetApp Files](azure-netapp-files-cost-model.md)
