---
title: Delegování podsítě na Azure NetApp Files | Microsoft Docs
description: Naučte se delegovat podsíť na Azure NetApp Files. Při vytváření svazku zadejte delegovanou podsíť.
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
ms.topic: how-to
ms.date: 07/28/2020
ms.author: b-juche
ms.openlocfilehash: da7aa0889940c560df705e3c47f5ccb1960aee2c
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361020"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegování podsítě do Azure NetApp Files 

Je nutné delegovat podsíť na Azure NetApp Files.   Při vytváření svazku je nutné zadat delegovanou podsíť.

## <a name="considerations"></a>Požadavky

* Průvodce pro vytvoření nové podsítě je ve výchozím nastavení maska sítě/24, která poskytuje 251 dostupných IP adres. Použití masky sítě/28, která poskytuje pro 11 použitelné IP adresy, je pro službu dostačující.
* V každé službě Azure Virtual Network (VNet) je možné delegovat Azure NetApp Files jenom jednu podsíť.   
   Azure umožňuje vytvořit více delegovaných podsítí ve virtuální síti.  Pokud ale použijete víc než jednu delegovanou podsíť, všechny pokusy o vytvoření nového svazku selžou.  
   Ve virtuální síti může být jenom jedna delegovaná podsíť. Účet NetApp může nasazovat svazky do více virtuální sítě, z nichž každá má vlastní delegovanou podsíť.  
* V delegované podsíti nelze určit skupinu zabezpečení sítě ani koncový bod služby. Tím dojde k selhání delegování podsítě.
* Přístup k svazku z globálně partnerské virtuální sítě se momentálně nepodporuje.
* Vytváření [uživatelem definovaných vlastních tras](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) na podsítích virtuálních počítačů s předponou adresy (cíl) do podsítě delegované pro Azure NetApp Files není podporováno. Tím dojde k ovlivnění připojení virtuálního počítače. Azure NetApp Files vytvoří systémovou trasu k delegované podsíti. Trasa se zobrazí v **efektivních trasách** v tabulce směrování, pokud ji potřebujete k řešení potíží.

## <a name="steps"></a>Postup

1.  V Azure Portal otevřete okno **virtuální sítě** a vyberte virtuální síť, kterou chcete použít pro Azure NetApp Files.    

1. V okně virtuální síť vyberte **podsítě** a klikněte na tlačítko **+ podsíť** . 

1. Vytvořte novou podsíť, která se bude používat pro Azure NetApp Files, a to tak, že na stránce Přidat podsíť dokončíte následující povinná pole:
    * **Název**: zadejte název podsítě.
    * **Rozsah adres**: zadejte rozsah IP adres.
    * **Delegování podsítě**: vyberte **Microsoft. NetApp/svazky**. 

      ![Delegování podsítě](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Můžete také vytvořit a delegovat podsíť při [vytváření svazku pro Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Další kroky

* [Vytvoření svazku pro Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Informace o integraci virtuální sítě pro služby Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


