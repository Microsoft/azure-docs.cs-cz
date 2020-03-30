---
title: Poradce při potížích s chybou přidělení cloudové služby | Dokumenty společnosti Microsoft
description: Řešení potíží s přidělením při nasazení Cloud Services v Azure
services: azure-service-management, cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 470778e5c441bb05ffc7c5e1c5ef97a6c30d3359
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247537"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Řešení potíží s přidělením při nasazení Cloud Services v Azure
## <a name="summary"></a>Souhrn
Když nasadíte instance do cloudové služby nebo přidáte nové instance webových nebo pracovních rolí, Microsoft Azure přidělí výpočetní prostředky. Občas se mohou zobrazit chyby při provádění těchto operací ještě před dosažením omezení předplatného Azure. Tento článek vysvětluje příčiny některých běžných selhání přidělení a navrhuje možné nápravy. Tyto informace mohou být také užitečné při plánování nasazení služeb.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Souvislosti – jak funguje přidělování
Servery v datových centrech Azure jsou rozdělené do clusterů. Nový požadavek na přidělení cloudové služby je pokus ve více clusterech. Když se první instance nasadí do cloudové služby (v pracovní nebo produkční), tato cloudová služba se připne do clusteru. Jakékoli další nasazení pro cloudovou službu se stane ve stejném clusteru. V tomto článku budeme odkazovat na to jako "připnul ke clusteru". Diagram 1 níže znázorňuje případ normální přidělení, které je pokus ve více clusterech; Diagram 2 znázorňuje případ přidělení, které je připnuté ke clusteru 2, protože to je místo, kde je hostována stávající cloudová služba CS_1.

![Diagram přidělení](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Proč dochází k selhání přidělení
Pokud je požadavek na přidělení připnutý ke clusteru, je vyšší pravděpodobnost, že se nepodaří najít volné prostředky, protože dostupný fond prostředků je omezen na cluster. Pokud je váš požadavek na přidělení připnutý ke clusteru, ale požadovaný typ prostředku není tímto clusterem podporován, váš požadavek se nezdaří, i když má cluster volný prostředek. Diagram 3 níže znázorňuje případ, kdy se nezdaří připnuté přidělení, protože jediný kandidátský cluster nemá volné prostředky. Diagram 4 znázorňuje případ, kdy se nezdaří připnuté přidělení, protože jediný kandidátský cluster nepodporuje požadovanou velikost virtuálního počítače, i když má cluster volné prostředky.

![Selhání připnutého přidělení](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Poradce při potížích s chybou přidělení pro cloudové služby
### <a name="error-message"></a>Chybová zpráva
Může se zobrazit následující chybová zpráva:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Běžné problémy
Zde jsou běžné scénáře přidělení, které způsobují, že požadavek na přidělení má být připnut do jednoho clusteru.

* Nasazení do pracovního slotu – pokud cloudová služba má nasazení v obou slotech, pak celá cloudová služba je připnutá k určitému clusteru.  To znamená, že pokud už v produkčním slotu existuje nasazení, dá se nové pracovní nasazení ve stejném clusteru přidělit jenom do tohoto produkčního slotu. Pokud se cluster blíží kapacitě, požadavek může selhat.
* Škálování – při přidávání nových instancí do existující cloudové služby musí přidělování probíhat ve stejném clusteru.  Malé požadavky na škálování se většinou podaří přidělit, ale neplatí to vždy. Pokud se cluster blíží kapacitě, požadavek může selhat.
* Skupina spřažení – nové nasazení do prázdné cloudové služby může být přiděleno prostředkům infrastruktury v libovolném clusteru v této oblasti, pokud cloudová služba není připnutá ke skupině spřažení. Nasazení do stejné skupiny spřažení bude pokus o stejný cluster. Pokud se cluster blíží kapacitě, požadavek může selhat.
* Virtuální síť skupiny spřažení – starší virtuální sítě byly vázány na skupiny spřažení namísto oblastí a cloudové služby v těchto virtuálních sítích by byly připnuté ke clusteru skupiny spřažení. Nasazení pro tento typ virtuální sítě se pokusí na připnutý clusteru. Pokud se cluster blíží kapacitě, požadavek může selhat.

## <a name="solutions"></a>Řešení
1. Znovu nasadit do nové cloudové služby – Toto řešení je pravděpodobně nejúspěšnější, protože umožňuje platformě vybrat ze všech clusterů v této oblasti.

   * Nasazení úlohy do nové cloudové služby  
   * Aktualizace záznamu CNAME nebo A tak, aby ukazovaly provoz na novou cloudovou službu.
   * Jakmile se nulový provoz bude na staré stránky, můžete odstranit starou cloudovou službu. Toto řešení by mělo mít nulové prostoje.
2. Odstraňte produkční i pracovní sloty – toto řešení zachová váš stávající název DNS, ale způsobí prostoje vaší aplikace.

   * Odstraňte produkční a pracovní sloty existující cloudové služby, aby cloudová služba byla prázdná, a pak
   * Vytvořte nové nasazení ve stávající cloudové službě. To se znovu pokusí o přidělení na všechny clustery v oblasti. Ujistěte se, že cloudová služba není vázána na skupinu spřažení.
3. Vyhrazená ADRESA IP – Toto řešení zachová vaši stávající ADRESU IP, ale způsobí prostoje vaší aplikace.  

   * Vytvoření vyhrazené adresy IP pro stávající nasazení pomocí prostředí Powershell

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Postupujte #2 shora a ujistěte se, že zadáte nový ReservedIP v CSCFG služby.
4. Odebrat skupinu spřažení pro nová nasazení – skupiny spřažení se již nedoporučují. Postupujte podle pokynů v kroku 1 výše a nasaďte novou cloudovou službu. Ujistěte se, že cloudová služba není ve skupině spřažení.
5. Převést na místní virtuální síť – viz [postup migrace ze skupin spřažení do místní virtuální sítě (VNet).](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)
