---
title: Omezení prostředků pro soubory Azure NetApp | Dokumentace Microsoftu
description: Popisuje omezení pro souborů NetApp Azure prostředky, včetně omezení pro účty NetApp, fondy kapacitu, svazky, snímky a delegované podsítě.
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
ms.openlocfilehash: b55467d77beb8f97b8e392b72682268ae0407e54
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826374"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Omezení prostředků pro Azure NetApp Files

Principy omezení prostředků pro soubory Azure NetApp umožňuje správu svazků.

## <a name="resource-limits"></a>Omezení prostředků

Následující tabulka popisuje omezení prostředků pro soubory Azure NetApp:

|  Resource  |  Výchozí omezení  |  Nastavitelné prostřednictvím žádosti o podporu  |
|----------------|---------------------|--------------------------------------|
|  Počet účtů NetApp za předplatné Azure   |  10    |  Ano   |
|  Počet kapacity fondů na účet NetApp   |    25     |   Ano   |
|  Počet svazků na kapacitu fondu     |    500   |    Ano     |
|  Počet snímků na jeden svazek       |    255     |    Ne        |
|  Počet podsítí delegovat do Azure NetApp souborů (Microsoft.NetApp/volumes) na Azure Virtual Network    |   1   |    Ne    |
|  Maximální počet virtuálních počítačů (včetně partnerských virtuálních sítích), který může připojit ke svazku     |    1000   |    Ne   |
|  Minimální velikost fondu jednoho kapacity   |  4 TiB     |    Ne  |
|  Maximální velikost fondu jednoho kapacity    |  500 TiB   |   Ne   |
|  Minimální velikost jednoho svazku    |    100 GiB    |    Ne    |
|  Maximální kvóty z jednoho svazku * přiřazené   |   92 TiB   |    Ne   |
|  Maximální velikost jednoho svazku *     |    100 TiB    |    Ne       |

* Svazek můžete ručně vytvořit nebo změně velikosti lze uchovávat 92 TiB. Svazek však může růst až 100 TB za Nadlimitní využití scénář. Zobrazit [modelu nákladů pro soubory Azure NetApp](azure-netapp-files-cost-model.md) podrobnosti o překročení kapacity. 

## <a name="request-limit-increase"></a>Požádat o zvýšení limitu 

Můžete vytvořit žádost o podporu Azure o navýšení limitů měnitelné z výše uvedené tabulky. 

Z plochy Azure portal navigace: 

1. Klikněte na tlačítko **Nápověda a podpora**.
2. Klikněte na tlačítko **+ nová žádost o podporu**.
3. Na kartě základy zadejte následující informace: 
    1. Typ problému: Vyberte **limity služby a předplatného (kvóty)**.
    2. Předplatná: Vyberte předplatné pro prostředek, který je třeba zvýšit kvótu.
    3. Typ kvóty: Vyberte **úložiště: Služba soubory Azure NetApp omezuje**.
    4. Klikněte na tlačítko **Další: Řešení**.
4. Na kartě Podrobnosti:
    1. V poli Popis zadejte následující informace pro odpovídající typ prostředku:

        |  Resource  |    Nadřazené prostředky      |    Požadovaná nová omezení     |    Důvod pro zvýšení kvóty       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Účet |  *ID předplatného*   |  *Maximální požadovaný nové **účet** číslo*    |  *Jaké scénáře nebo použití případu výzva požadavek?*  |
        |  Fond    |  *ID předplatného, identifikátor URI účtu*  |  *Maximální požadovaný nové **fondu** číslo*   |  *Jaké scénáře nebo použití případu výzva požadavek?*  |
        |  Svazek  |  *ID předplatného, identifikátor URI, účtu fondu identifikátorů URI*   |  *Maximální požadovaný nové **svazku** číslo*     |  *Jaké scénáře nebo použití případu výzva požadavek?*  |

    2. Zadejte odpovídající metoda podpory a poskytnout informace o smlouvě.

    3. Klikněte na tlačítko **Další: Zkontrolovat a vytvořit** k vytvoření požadavku. 


## <a name="next-steps"></a>Další postup  

- [Vysvětlení hierarchii úložiště souborů Azure NetApp](azure-netapp-files-understand-storage-hierarchy.md)
- [Model nákladů pro soubory Azure NetApp](azure-netapp-files-cost-model.md)
