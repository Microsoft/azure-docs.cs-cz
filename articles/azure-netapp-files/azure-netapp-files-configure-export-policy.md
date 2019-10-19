---
title: Konfigurace zásad exportu pro svazek NFS pomocí Azure NetApp Files | Microsoft Docs
description: Popisuje postup konfigurace zásad exportu pro řízení přístupu ke svazku systému souborů NFS pomocí Azure NetApp Files
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
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: d73ac199df03f4d789db0634be1e926afc77e623
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597561"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Konfigurace zásad exportu pro svazek NFS

Můžete volitelně nakonfigurovat zásady exportu pro řízení přístupu ke svazku Azure NetApp Files. Zásada exportu Azure NetApp Files podporuje pouze svazky systému souborů NFS.  Podporují se NFSv3 i názvů NFSv4. 

## <a name="steps"></a>Kroky 

1.  Klikněte na okno **Vytvořit zásady exportu** z okna Správa svazku. 

2.  Zadejte do následujících polí informace pro vytvoření pravidla zásad exportu:   
    *  **Index**   
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

    ![Zásada exportu](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Další kroky 
* [Správa svazků](azure-netapp-files-manage-volumes.md)
* [Připojování nebo odpojování svazku pro virtuální počítače](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Správa snímků](azure-netapp-files-manage-snapshots.md)
