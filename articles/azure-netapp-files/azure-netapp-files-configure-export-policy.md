---
title: Konfigurace zásad exportu pro svazek NFS – Azure NetApp Files
description: Popisuje postup konfigurace zásad exportu pro řízení přístupu ke svazku systému souborů NFS pomocí Azure NetApp Files
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/24/2020
ms.openlocfilehash: 6d990b94210383ba4b30569693f4471f43306ed2
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87169837"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Konfigurace zásad exportu pro svazek NFS

Můžete nakonfigurovat zásady exportu pro řízení přístupu ke svazku Azure NetApp Files. Zásada exportu Azure NetApp Files podporuje svazky, které používají protokol NFS (NFSv3 a NFSv 4.1), a duální protokol (NFSv3 a SMB). 

Můžete vytvořit až pět pravidel zásad pro export.

## <a name="steps"></a>Kroky 

1.  Na stránce svazky vyberte svazek, pro který chcete nakonfigurovat zásadu exportu, a klikněte na **Exportovat zásadu**. 

    Při vytváření svazku můžete také nakonfigurovat zásady exportu.

2.  Zadejte do následujících polí informace pro vytvoření pravidla zásad exportu:   
    *  **Indexovacím**   
        Určete číslo indexu pro pravidlo.  
        Zásady exportu můžou mít maximálně pět pravidel. Pravidla se vyhodnocují podle jejich pořadí v seznamu čísel indexu. Pravidla s nižším číslem indexu se vyhodnocují jako první. Například pravidlo s číslem indexu 1 je vyhodnoceno před pravidlem s číslem indexu 2. 

    * **Povolení klienti**   
        Zadejte hodnotu v jednom z následujících formátů:  
        * IPv4 adresa, například `10.1.12.24` 
        * IPv4 adresa s maskou podsítě, vyjádřená jako počet bitů, například `10.1.12.10/4`

    * **Přístup**  
        Vyberte jeden z následujících typů přístupu:  
        * Zakázaný přístup 
        * Čtení a zápis
        * Jen pro čtení

    * **Přístup ke kořenu**  
        Určete, jestli má `root` účet přístup ke svazku.  Ve výchozím nastavení je přístup root nastavený na **zapnuto**a `root` účet má přístup ke svazku.

        ![Zásada exportu](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 



## <a name="next-steps"></a>Další kroky 
* [Správa svazků](azure-netapp-files-manage-volumes.md)
* [Připojování nebo odpojování svazku pro virtuální počítače](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Správa snímků](azure-netapp-files-manage-snapshots.md)
