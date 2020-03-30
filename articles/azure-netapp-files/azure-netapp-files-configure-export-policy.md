---
title: Konfigurace zásad exportu pro svazek systému souborů NFS – soubory Azure NetApp
description: Popisuje, jak nakonfigurovat zásady exportu pro řízení přístupu ke svazku systému souborů nfs pomocí souborů Azure NetApp
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: b96fca3a5627a1c6c96c8db5c1c209a51c5e102a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551554"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Konfigurace zásad exportu pro svazek NFS

Můžete volitelně nakonfigurovat zásady exportu pro řízení přístupu ke svazku Azure NetApp Files. Zásady exportu souborů Azure NetApp podporují pouze svazky systému souborů NFS.  Jsou podporovány nfsv3 a NFSv4. 

## <a name="steps"></a>Kroky 

1.  Klikněte na **Zásady exportu** z navigačního podokna Soubory Azure NetApp. 

2.  Zadejte do následujících polí informace pro vytvoření pravidla zásad exportu:   
    *  **Index**   
        Určete číslo indexu pro pravidlo.  
        Zásady exportu můžou mít maximálně pět pravidel. Pravidla se vyhodnocují podle jejich pořadí v seznamu čísel indexu. Pravidla s nižším číslem indexu se vyhodnocují jako první. Například pravidlo s číslem indexu 1 je vyhodnoceno před pravidlem s číslem indexu 2. 

    * **Povolené klienty**   
        Zadejte hodnotu v jednom z následujících formátů:  
        * IPv4 adresa, například `10.1.12.24` 
        * IPv4 adresa s maskou podsítě, vyjádřená jako počet bitů, například `10.1.12.10/4`

    * **Přístup**  
        Vyberte jeden z následujících typů přístupu:  
        * Zakázaný přístup 
        * Čtení a zápis
        * Jen pro čtení

    ![Zásada exportu](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Další kroky 
* [Správa svazků](azure-netapp-files-manage-volumes.md)
* [Připojování nebo odpojování svazku pro virtuální počítače](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Správa snímků](azure-netapp-files-manage-snapshots.md)
