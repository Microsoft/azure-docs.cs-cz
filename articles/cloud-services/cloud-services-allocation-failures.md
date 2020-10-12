---
title: Řešení potíží s přidělením cloudové služby | Microsoft Docs
description: Řešení potíží s přidělením při nasazení služby Azure Cloud Services. Přečtěte si, jak přidělování funguje a proč může přidělení selhat.
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
ms.openlocfilehash: cba3f47015072f16112ef981d2f59d0c73cb69c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88142481"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Řešení potíží s přidělením při nasazení Cloud Services v Azure
## <a name="summary"></a>Shrnutí
Když nasadíte instance do cloudové služby nebo přidáte nové instance webových nebo pracovních rolí, Microsoft Azure přiděluje výpočetní prostředky. Při provádění těchto operací může občas docházet k chybám, a to ještě před dosažením limitů předplatného Azure. Tento článek vysvětluje příčiny některých běžných selhání přidělení a navrhuje možnou nápravu. Tyto informace mohou být užitečné také při plánování nasazení služeb.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Pozadí – jak funguje přidělování
Servery v datových centrech Azure jsou rozdělené do clusterů. V několika clusterech se zkouší nový požadavek na přidělení cloudové služby. Při nasazení první instance do cloudové služby (v přípravné nebo produkční) se tato cloudová služba připnula ke clusteru. Všechna další nasazení pro cloudovou službu budou provedena ve stejném clusteru. V tomto článku odkazujeme na to, že je "připnuté do clusteru". Obrázek 1 níže znázorňuje případ normálního přidělení, které se pokouší o více clusterů. Diagram 2 znázorňuje případ přidělení připnuté ke clusteru 2, protože to je místo, kde je stávající cloudová služba CS_1 hostovaná.

![Diagram přidělení](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Proč dochází k selhání přidělení
Když je požadavek na přidělení připnutý ke clusteru, existuje větší šance na selhání při hledání volných prostředků, protože dostupný fond zdrojů je omezený na cluster. Pokud je váš požadavek na přidělení připnutý ke clusteru, ale požadovaný typ prostředku není tímto clusterem podporován, požadavek selže, i když má cluster volné prostředky. Diagram 3 níže znázorňuje případ, kdy připnuté přidělení selhává, protože jediný cluster kandidátů nemá volné prostředky. Diagram 4 znázorňuje případ, kdy připnuté přidělení selhává, protože jediný kandidát clusteru nepodporuje požadovanou velikost virtuálního počítače, i když má cluster bezplatné prostředky.

![Připnuté selhání přidělení](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Řešení potíží s přidělováním pro cloudové služby
### <a name="error-message"></a>Chybová zpráva
Může se zobrazit následující chybová zpráva:

> Operace "Azure Operation ID}" se nezdařila s kódem Compute. ConstrainedAllocationFailed. Podrobnosti: přidělení nebylo úspěšné; v požadavku nelze naplnit omezení. Požadované nasazení nové služby je spojené se skupinou vztahů nebo je jeho cílem virtuální síť, anebo je v této hostované službě nějaké nasazení. Kterákoli z těchto podmínek omezuje nové nasazení na konkrétní prostředky Azure. Zkuste to znovu později nebo zkuste zmenšit velikost virtuálního počítače nebo počet instancí role. Případně, pokud je to možné, odeberte uvedená omezení nebo se pokuste nasadit do jiné oblasti. "

### <a name="common-issues"></a>Běžné problémy
Tady jsou společné scénáře přidělování, které způsobují připnutí žádosti o přidělení k jednomu clusteru.

* Nasazení do přípravného slotu – Pokud cloudová služba má nasazení v obou slotech, bude celá cloudová služba připnuté na konkrétní cluster.  To znamená, že pokud už v produkčním slotu existuje nasazení, dá se nové pracovní nasazení ve stejném clusteru přidělit jenom do tohoto produkčního slotu. Pokud se cluster blíží kapacitě, požadavek může selhat.
* Škálování – při přidávání nových instancí do existující cloudové služby musí přidělování probíhat ve stejném clusteru.  Malé požadavky na škálování se většinou podaří přidělit, ale neplatí to vždy. Pokud se cluster blíží kapacitě, požadavek může selhat.
* Skupina vztahů – nové nasazení do prázdné cloudové služby může být přiděleno prostředky infrastruktury v jakémkoli clusteru v této oblasti, pokud není cloudová služba připnuté ke skupině vztahů. Nasazení do stejné skupiny vztahů se pokusí o stejný cluster. Pokud se cluster blíží kapacitě, požadavek může selhat.
* Skupina vztahů vNet – starší virtuální sítě byly svázané se skupinami vztahů namísto oblastí a cloudové služby v těchto virtuálních sítích by se připnuly k clusteru skupiny vztahů. V připnutém clusteru se pokusí nasazení na tento typ virtuální sítě. Pokud se cluster blíží kapacitě, požadavek může selhat.

## <a name="solutions"></a>Řešení
1. Opětovné nasazení do nové cloudové služby – toto řešení je pravděpodobně nejvíce úspěšné, protože umožňuje platformě vybrat ze všech clusterů v této oblasti.

   * Nasazení úlohy do nové cloudové služby  
   * Aktualizace záznamu CNAME nebo záznamu pro přechod na provoz do nové cloudové služby
   * Po skončení provozu na starý Web můžete odstranit starou cloudovou službu. Toto řešení by mělo mít žádný výpadek.
2. Odstraňte produkční a přípravné sloty – toto řešení zachová stávající název DNS, ale způsobí výpadek vaší aplikace.

   * Odstraňte produkční a přípravné sloty existující cloudové služby, aby byla cloudová služba prázdná, a potom
   * Vytvořte nové nasazení v existující cloudové službě. Dojde k opakovanému pokusu o přidělení všech clusterů v této oblasti. Ujistěte se, že cloudová služba není vázaná na skupinu vztahů.
3. Vyhrazená IP adresa – toto řešení zachová vaši stávající IP adresu, ale způsobí výpadek vaší aplikace.  

   * Vytvoření vyhrazené IP adresy pro existující nasazení pomocí PowerShellu

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Sledujte #2 výše a nezapomeňte zadat novou vyhrazenou IP adresu v CSCFG služby.
4. Odebrat skupinu vztahů pro nová nasazení – skupiny vztahů už se nedoporučují. Postupujte podle pokynů v kroku 1 výše a nasaďte novou cloudovou službu. Ujistěte se, že cloudová služba není ve skupině vztahů.
5. Převod na místní Virtual Network – viz [Postup migrace ze skupin vztahů do místní Virtual Network (VNET)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
