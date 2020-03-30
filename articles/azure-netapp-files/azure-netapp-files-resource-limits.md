---
title: Omezení prostředků pro soubory Azure NetApp | Dokumenty společnosti Microsoft
description: Popisuje omezení pro prostředky Azure NetApp Files a jak požádat o zvýšení limitu prostředků.
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
ms.date: 3/13/2020
ms.author: b-juche
ms.openlocfilehash: 36b2d50722a1840e461d6907f440d859c7c82117
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408823"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Omezení prostředků pro Azure NetApp Files

Principy omezení prostředků pro soubory Azure NetApp vám pomůže spravovat svazky.

## <a name="resource-limits"></a>Omezení prostředků

Následující tabulka popisuje omezení prostředků pro soubory Azure NetApp:

|  Prostředek  |  Výchozí omezení  |  Nastavitelná prostřednictvím žádosti o podporu  |
|----------------|---------------------|--------------------------------------|
|  Počet účtů NetApp na oblast Azure   |  10    |  Ano   |
|  Počet fondů kapacity na účet NetApp   |    25     |   Ano   |
|  Počet svazků na kapacitní fond     |    500   |    Ano     |
|  Počet snímků na svazek       |    255     |    Ne        |
|  Počet podsítí delegovaných na soubory Azure NetApp (Microsoft.NetApp/svazky) na virtuální síť Azure    |   1   |    Ne    |
|  Počet použitých IP adresy ve virtuální síti (včetně okamžitě partnerských virtuálních sítí) se soubory Azure NetApp   |    1000   |    Ano   |
|  Minimální velikost jednoho kapacitního fondu   |  4 TiB     |    Ne  |
|  Maximální velikost jednoho fondu kapacity    |  500 TiB   |   Ne   |
|  Minimální velikost jednoho svazku    |    100 GiB    |    Ne    |
|  Maximální velikost jednoho svazku     |    100 TiB    |    Ne    |
|  Maximální velikost jednoho souboru     |    16 TiB    |    Ne    |    
|  Maximální velikost jednoho adresáře      |    320 MB    |    Ne    |    
|  Maximální počet souborů ([maxfiles](#maxfiles)) na svazek     |    100 milionů    |    Ano    |    

Další informace naleznete v [tématu Nejčastější dotazy ke správě kapacity](azure-netapp-files-faqs.md#capacity-management-faqs).

## <a name="maxfiles-limits"></a>Omezení maxsouborů<a name="maxfiles"></a> 

Svazky souborů Azure NetApp mají limit nazývaný *maxfiles*. Limit maxsouborů je počet souborů, které může svazek obsahovat. Limit maximálních souborů pro svazek souborů Azure NetApp se indexuje na základě velikosti (kvóty) svazku. Limit maxfiles pro svazek se zvyšuje nebo snižuje rychlostí 20 milionů souborů na TiB rezervované velikosti svazku. 

Služba dynamicky upravuje limit maxfiles pro svazek na základě jeho zřízená velikost. Například svazek nakonfigurovaný původně s velikostí 1 TiB by měl maximální limit souborů 20 milionů. Následné změny velikosti svazku by vedly k automatické muzice limitu maxfiles na základě následujících pravidel: 

|    Velikost svazku (kvóta)     |  Automatické seřízení limitu maxfiles    |
|----------------------------|-------------------|
|    < 1 TiB                 |    20 milionů     |
|    >= 1 TiB, ale < 2 TiB    |    40 milionů     |
|    >= 2 TiB, ale < 3 TiB    |    60 milionů     |
|    >= 3 TiB, ale < 4 TiB    |    80 milionů     |
|    >= 4 TiB                |    100 milionů    |

Pro libovolnou velikost svazku můžete zahájit [žádost o podporu](#limit_increase) a zvýšit limit maximálního počtu souborů nad 100 milionů.

## <a name="request-limit-increase"></a>Zvýšení limitu požadavku<a name="limit_increase"></a> 

Můžete vytvořit žádost o podporu Azure pro zvýšení nastavitelných limitů z výše uvedené tabulky. 

Z navigační roviny portálu Azure: 

1. Klepněte na **tlačítko Nápověda + podpora**.
2. Klikněte na **+ Nová žádost o podporu**.
3. Na kartě Základy zadejte následující informace: 
    1. Typ problému: Vyberte **omezení služby a předplatného (kvóty).**
    2. Odběry: Vyberte předplatné pro prostředek, který potřebujete zvýšit kvótu.
    3. Typ kvóty: Vyberte **úložiště: Limity souborů Azure NetApp**.
    4. Klikněte na **další: Řešení**.
4. Na kartě Podrobnosti:
    1. V poli Popis zadejte následující informace pro odpovídající typ prostředku:

        |  Prostředek  |    Nadřazené zdroje      |    Požadované nové limity     |    Důvod zvýšení kvóty       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Účet |  *ID předplatného*   |  *Požadované nové číslo **maximálního účtu***    |  *Jaký scénář nebo případ použití vyvolal požadavek?*  |
        |  Fond    |  *ID předplatného, identifikátor URI účtu*  |  *Požadované nové maximální číslo **fondu***   |  *Jaký scénář nebo případ použití vyvolal požadavek?*  |
        |  Svazek  |  *ID předplatného, identifikátor URI účtu, identifikátor URI fondu*   |  *Požadované nové maximální **číslo svazku***     |  *Jaký scénář nebo případ použití vyvolal požadavek?*  |
        |  Maximální počet souborů  |  *ID předplatného, identifikátor URI účtu, identifikátor URI fondu, identifikátor URI svazku*   |  *Požadované nové maximální **číslo maxsouborů***     |  *Jaký scénář nebo případ použití vyvolal požadavek?*  |    

    2. Zadejte vhodnou metodu podpory a zadejte informace o smlouvě.

    3. Klikněte na **Další: Kontrola + vytvořit** pro vytvoření požadavku. 


## <a name="next-steps"></a>Další kroky  

- [Vysvětlení hierarchie úložiště služby Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Model nákladů pro Azure NetApp Files](azure-netapp-files-cost-model.md)
