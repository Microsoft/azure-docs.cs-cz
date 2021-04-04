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
ms.date: 01/29/2021
ms.author: b-juche
ms.openlocfilehash: c82e834c0af3737c1e5ef19c7aa789b94d87f6d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99095387"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Omezení prostředků pro Azure NetApp Files

Principy omezení prostředků pro Azure NetApp Files vám pomůžou spravovat svazky.

## <a name="resource-limits"></a>Omezení prostředků

Následující tabulka popisuje omezení prostředků pro Azure NetApp Files:

|  Prostředek  |  Výchozí omezení  |  Nastavitelná prostřednictvím žádosti o podporu  |
|----------------|---------------------|--------------------------------------|
|  Počet účtů NetApp na oblast Azure na předplatné  |  10    |  Yes   |
|  Počet fondů kapacity na účet NetApp   |    25     |   Yes   |
|  Počet svazků na předplatné   |    500     |   Yes   |
|  Počet svazků na fond kapacity     |    500   |    Yes     |
|  Počet snímků na svazek       |    255     |    No        |
|  Počet podsítí delegovaných pro Azure NetApp Files (Microsoft. NetApp/svazky) na Azure Virtual Network    |   1   |    No    |
|  Počet využitých IP adres ve virtuální síti (včetně okamžitě virtuální sítě partnerských vztahů) s Azure NetApp Files   |    1000   |    No   |
|  Minimální velikost jednoho fondu kapacity   |  4 TiB     |    No  |
|  Maximální velikost jednoho fondu kapacity    |  500 TiB   |   No   |
|  Minimální velikost jednoho svazku    |    100 GiB    |    No    |
|  Maximální velikost jednoho svazku     |    100 TiB    |    No    |
|  Maximální velikost jednoho souboru     |    16 TiB    |    No    |    
|  Maximální velikost metadat adresáře v jednom adresáři      |    320 MB    |    No    |    
|  Maximální počet souborů ([maxfiles](#maxfiles)) na jeden svazek     |    100 000 000    |    Yes    |    
|  Minimální přiřazená propustnost pro ruční svazek technologie QoS     |    1 MiB/s   |    No    |    
|  Maximální přiřazená propustnost pro ruční svazek technologie QoS     |    soubory MiB 4 500/s    |    No    |    
|  Počet svazků ochrany dat replikace mezi oblastmi (cílové svazky)     |    5    |    Yes    |     

Pokud chcete zjistit, jestli se adresář blíží omezení maximální velikosti pro metadata adresáře (320 MB), přečtěte si téma [návody určení, jestli se adresář blíží velikosti limitu](azure-netapp-files-faqs.md#how-do-i-determine-if-a-directory-is-approaching-the-limit-size).   

Další informace najdete v tématu [Nejčastější dotazy ke správě kapacity](azure-netapp-files-faqs.md#capacity-management-faqs).

## <a name="maxfiles-limits"></a>Omezení maxfiles <a name="maxfiles"></a> 

Azure NetApp Files svazky mají omezení s názvem *maxfiles*. Limit maxfiles je počet souborů, které může obsahovat svazek. Limit maxfiles pro Azure NetApp Files svazek je indexovaný na základě velikosti (kvóty) svazku. Limit maxfiles pro svazek se zvyšuje nebo snižuje rychlostí 20 000 000 souborů za TiB zřízené velikosti svazku. 

Služba dynamicky upravuje limit maxfiles pro svazek na základě jeho zřízené velikosti. Například svazek nakonfigurovaný původně s velikostí 1 TiB by měl maxfiles limit 20 000 000. Následné změny velikosti svazku mají za následek automatické přizpůsobení limitu maxfiles na základě následujících pravidel: 

|    Velikost svazku (kvóta)     |  Automatické přizpůsobení limitu maxfiles    |
|----------------------------|-------------------|
|    <= 1 TiB                |    20 000 000     |
|    > 1 TiB, ale <= 2 TiB    |    40 000 000     |
|    > 2 TiB, ale <= 3 TiB    |    60 000 000     |
|    > 3 TiB, ale <= 4 TiB    |    80 000 000     |
|    > 4 TiB                 |    100 000 000    |

Pokud jste už pro svazek nastavili aspoň 4 TiB kvót, můžete [žádost o podporu](#limit_increase) iniciovat tak, že zvýšíte maxfiles limit nad 100 000 000. Pro každý 100 000 000 souborů, které narostete (nebo zlomku), potřebujete zvýšit odpovídající kvótu svazku o 4 TiB.  Pokud například zvýšíte limit maxfiles z 100 000 000 souborů na 200 000 000 souborů (nebo jakékoli číslo v rozsahu), musíte zvýšit kvótu svazku ze 4 TiB na 8 TiB.

## <a name="request-limit-increase"></a>Zvýšení limitu požadavku <a name="limit_increase"></a> 

Můžete vytvořit žádost o podporu Azure, která zvýší nastavitelná omezení z výše uvedené tabulky. 

Z Azure Portal rovina navigace: 

1. Klikněte na tlačítko **help + podpora**.
2. Klikněte na **+ Nová žádost o podporu**.
3. Na kartě základy zadejte následující informace: 
    1. Typ problému: vyberte **služby a omezení pro předplatné (kvóty)**.
    2. Předplatná: vyberte předplatné pro prostředek, pro který potřebujete zvýšit kvótu.
    3. Typ kvóty: vyberte **úložiště: omezení Azure NetApp Files**.
    4. Klikněte na **Další: řešení**.
4. Na kartě Podrobnosti:
    1. V poli Popis zadejte pro odpovídající typ prostředku následující informace:

        |  Prostředek  |    Nadřazené prostředky      |    Požadovaná nová omezení     |    Odůvodnění pro navýšení kvóty       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Účet |  *ID předplatného*   |  *Požadované nové maximální číslo **účtu***    |  *Jaký scénář nebo případ použití se žádostí vyzve?*  |
        |  Fond    |  *ID předplatného, identifikátor URI účtu NetApp*  |  *Požadované nové maximální číslo **fondu***   |  *Jaký scénář nebo případ použití se žádostí vyzve?*  |
        |  Svazek  |  *ID předplatného, identifikátor URI účtu NetApp, identifikátor URI fondu kapacity*   |  *Požadované nové maximální číslo **svazku***     |  *Jaký scénář nebo případ použití se žádostí vyzve?*  |
        |  Maxfiles  |  *ID předplatného, identifikátor URI účtu NetApp, identifikátor URI fondu kapacit, identifikátor URI svazku*   |  *Požadované nové maximální číslo **maxfiles***     |  *Jaký scénář nebo případ použití se žádostí vyzve?*  |    
        |  Svazky pro ochranu dat replikace mezi oblastmi  |  *ID předplatného, identifikátor URI cílového účtu NetApp, identifikátor URI cílového fondu kapacit, identifikátor URI zdrojového NetApp, identifikátor URI zdrojového zdroje, identifikátor URI zdrojového svazku*   |  * Požadovaný nový maximální počet **svazků pro ochranu dat replikace mezi oblastmi (cílové svazky)** _     |  _What scénář nebo se v případě případu použití zobrazí požadavek? *  |    

    2. Zadejte odpovídající způsob podpory a zadejte informace o kontraktu.

    3. Klikněte na **Další: zkontrolovat + vytvořit** a vytvořte žádost. 


## <a name="next-steps"></a>Další kroky  

- [Vysvětlení hierarchie úložiště služby Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Model nákladů pro Azure NetApp Files](azure-netapp-files-cost-model.md)
