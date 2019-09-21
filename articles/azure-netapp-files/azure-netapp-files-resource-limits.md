---
title: Omezení prostředků pro Azure NetApp Files | Microsoft Docs
description: Popisuje omezení pro Azure NetApp Files prostředky a způsob zvýšení limitu prostředků.
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
ms.date: 09/20/2019
ms.author: b-juche
ms.openlocfilehash: f7213ddee5d7bdfd41508f5fee66de63cde5b7c4
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170019"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Omezení prostředků pro Azure NetApp Files

Principy omezení prostředků pro Azure NetApp Files vám pomůžou spravovat svazky.

## <a name="resource-limits"></a>Omezení prostředků

Následující tabulka popisuje omezení prostředků pro Azure NetApp Files:

|  Resource  |  Výchozí limit  |  Nastavitelná prostřednictvím žádosti o podporu  |
|----------------|---------------------|--------------------------------------|
|  Počet účtů NetApp na předplatné Azure   |  10    |  Ano   |
|  Počet fondů kapacity na účet NetApp   |    25     |   Ano   |
|  Počet svazků na fond kapacity     |    500   |    Ano     |
|  Počet snímků na svazek       |    255     |    Ne        |
|  Počet podsítí delegovaných pro Azure NetApp Files (Microsoft. NetApp/svazky) na Azure Virtual Network    |   1   |    Ne    |
|  Počet IP adres ve virtuální síti (včetně partnerských virtuální sítě), které mají přístup k Azure NetApp Files   |    1000   |    Ano   |
|  Minimální velikost jednoho fondu kapacity   |  4 TiB     |    Ne  |
|  Maximální velikost jednoho fondu kapacity    |  500 TiB   |   Ne   |
|  Minimální velikost jednoho svazku    |    100 GiB    |    Ne    |
|  Maximální velikost jednoho svazku     |    100 TiB    |    Ne    |
|  Maximální počet souborů ([maxfiles](#maxfiles)) na jeden svazek     |    100 000 000    |    Ano    |    
|  Maximální velikost jednoho souboru     |    16 TiB    |    Ne    |    

## Omezení maxfiles<a name="maxfiles"></a> 

Azure NetApp Files svazky mají omezení s názvem *maxfiles*. Limit maxfiles je počet souborů, které může obsahovat svazek. Limit maxfiles pro Azure NetApp Files svazek je indexovaný na základě velikosti (kvóty) svazku. Limit maxfiles pro svazek se zvyšuje nebo snižuje rychlostí 20 000 000 souborů za TiB zřízené velikosti svazku. 

Služba dynamicky upravuje limit maxfiles pro svazek na základě jeho zřízené velikosti. Například svazek nakonfigurovaný původně s velikostí 1 TiB by měl maxfiles limit 20 000 000. Následné změny velikosti svazku mají za následek automatické přizpůsobení limitu maxfiles na základě následujících pravidel: 

|    Velikost svazku (kvóta)     |  Automatické přizpůsobení limitu maxfiles    |
|----------------------------|-------------------|
|    < 1 TiB                 |    20 000 000     |
|    > = 1 TiB, ale < 2 TiB    |    40 000 000     |
|    > = 2 TiB, ale < 3 TiB    |    60 000 000     |
|    > = 3 TiB, ale < 4 TiB    |    80 000 000     |
|    > = 4 TiB                |    100 000 000    |

U libovolné velikosti svazku můžete zahájit [žádost o podporu](#limit_increase) , aby se zvýšil limit maxfiles nad rámec 100 000 000.

## Zvýšení limitu požadavku<a name="limit_increase"></a> 

Můžete vytvořit žádost o podporu Azure, která zvýší nastavitelná omezení z výše uvedené tabulky. 

Z Azure Portal rovina navigace: 

1. Klikněte na tlačítko **help + podpora**.
2. Klikněte na **+ Nová žádost o podporu**.
3. Na kartě základy zadejte následující informace: 
    1. Typ problému: Vyberte **omezení služby a předplatné (kvóty)** .
    2. Odběru Vyberte předplatné pro prostředek, u kterého potřebujete kvótu zvýšit.
    3. Typ kvóty: Vyberte **úložiště: Omezení**Azure NetApp Files.
    4. Klikněte na **Další: Řešení**.
4. Na kartě Podrobnosti:
    1. V poli Popis zadejte pro odpovídající typ prostředku následující informace:

        |  Resource  |    Nadřazené prostředky      |    Požadovaná nová omezení     |    Důvod zvýšení kvóty       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Účet |  *ID předplatného*   |  *Požadované nové maximální číslo **účtu***    |  *Jaký scénář nebo případ použití se žádostí vyzve?*  |
        |  Fond    |  *ID předplatného, identifikátor URI účtu*  |  *Požadované nové maximální číslo **fondu***   |  *Jaký scénář nebo případ použití se žádostí vyzve?*  |
        |  Svazek  |  *ID předplatného, identifikátor URI účtu, identifikátor URI fondu*   |  *Požadované nové maximální číslo **svazku***     |  *Jaký scénář nebo případ použití se žádostí vyzve?*  |
        |  Maxfiles  |  *ID předplatného, identifikátor URI účtu, identifikátor URI fondu, identifikátor URI svazku*   |  *Požadované nové maximální číslo **maxfiles***     |  *Jaký scénář nebo případ použití se žádostí vyzve?*  |    

    2. Zadejte odpovídající způsob podpory a zadejte informace o kontraktu.

    3. Klikněte na **Další: K vytvoření žádosti** si Projděte + Create. 


## <a name="next-steps"></a>Další kroky  

- [Pochopení hierarchie úložiště Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Nákladový model pro Azure NetApp Files](azure-netapp-files-cost-model.md)
