---
title: Konfigurace zásad exportu pro svazek Azure NetApp Files | Microsoft Docs
description: Popisuje způsob konfigurace zásad exportu pro řízení přístupu ke svazku Azure NetApp Files.
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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 115c6726ed150b9a76685bd5a558d547d215958f
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587731"
---
# <a name="configure-export-policy-for-a-volume"></a>Konfigurace zásad exportu pro svazek

Můžete volitelně nakonfigurovat zásady exportu pro řízení přístupu ke svazku Azure NetApp Files. 

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

    * **Protokoly**   
        Zadejte protokol, který se použije pro zásadu exportu.   
        Azure NetApp Files v současné době podporuje jenom NFSv3.

    ![Zásada exportu](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Další postup 
* [Správa svazků](azure-netapp-files-manage-volumes.md)
* [Připojování nebo odpojování svazku pro virtuální počítače](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Správa snímků](azure-netapp-files-manage-snapshots.md)
