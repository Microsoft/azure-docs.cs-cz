---
title: Delegování podsítě na soubory Azure NetApp | Dokumenty společnosti Microsoft
description: Popisuje, jak delegovat podsíť na soubory Azure NetApp.
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
ms.date: 03/19/2020
ms.author: b-juche
ms.openlocfilehash: b83f530549ffa43789963fd0c95b4982f5289356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054465"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegování podsítě do Azure NetApp Files 

Musíte delegovat podsíť na soubory Azure NetApp.   Při vytváření svazku je třeba zadat delegovanou podsíť.

## <a name="considerations"></a>Požadavky
* Průvodce vytvořením nové podsítě je výchozí pro masku sítě /24, která poskytuje 251 dostupných adres IP. Použití masky sítě /28, která poskytuje 16 použitelných ADRES IP, je pro službu dostatečná.
* V každé virtuální síti Azure (VNet) lze delegovat jenom jednu podsíť na soubory Azure NetApp.   
   Azure umožňuje vytvářet více delegovaných podsítí ve virtuální síti.  Všechny pokusy o vytvoření nového svazku se však nezdaří, pokud použijete více než jednu delegovanou podsíť.
* V delegované podsíti nelze určit skupinu zabezpečení sítě nebo koncový bod služby. To způsobí, že delegování podsítě nezdaří.
* Přístup ke svazku z globálně partnerské virtuální sítě není aktuálně podporován.
* Vytváření [vlastních tras definovaných uživatelem](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) v podsítích virtuálních zařízení s předponou adresy (cíl) do podsítě delegované na soubory Azure NetApp není podporováno. To bude mít vliv na připojení virtuálních zařízení.

## <a name="steps"></a>Kroky 
1.  Přejděte na okno **Virtuální sítě** z portálu Azure a vyberte virtuální síť, kterou chcete použít pro soubory Azure NetApp.    

1. Vyberte **podsítě** z okna Virtuální síť a klepněte na tlačítko **+Podsíť.** 

1. Vytvořte novou podsíť, která se použije pro soubory Azure NetApp, vyplněním následujících povinných polí na stránce Přidat podsíť:
    * **Název**: Zadejte název podsítě.
    * **Rozsah adres**: Zadejte rozsah IP adres.
    * **Delegování podsítě**: Vyberte **Microsoft.NetApp/svazky**. 

      ![Delegování podsítě](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Můžete také vytvořit a delegovat podsíť při [vytváření svazku pro soubory Azure NetApp](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Další kroky  
* [Vytvoření svazku pro Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Informace o integraci virtuálních sítí pro služby Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


