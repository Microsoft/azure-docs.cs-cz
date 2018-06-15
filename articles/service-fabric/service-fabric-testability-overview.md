---
title: Přehled služby Analysis Service selhání | Microsoft Docs
description: Tento článek popisuje službu analýzy selhání v Service Fabric vyvolat chyb a spuštěným scénáře testování pro vaše služby.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: timlt
editor: vturecek
ms.assetid: 1f064276-293a-4989-a513-e0d0b9fdf703
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: 807e4588e23ea01c5ce435282d7af59bb108e6c6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209680"
---
# <a name="introduction-to-the-fault-analysis-service"></a>Úvod ke službě analýza selhání
Služba Analysis Service selhání je určená pro testování služby, které jsou postaveny na Microsoft Azure Service Fabric. Se službou Analysis selhání můžete vyvolat smysluplný chyb a spouštění scénáře dokončení testování vaší aplikace. Tyto scénáře a chyb vykonává a ověřit množství stavy a přechody, které služba bude mít v průběhu své životnosti, všechny řízené, bezpečné a konzistentním způsobem.

Akce jsou jednotlivé chyb cílení na služby pro testování ho. Vývojář služby můžete použít jako stavební bloky k zápisu složité scénáře. Příklad:

* Restartujte uzel k simulaci libovolný počet situace, kdy počítač nebo virtuální počítač restartovat.
* Přesunutí repliky stavové služby k simulaci Vyrovnávání zatížení a převzetí služeb při selhání nebo upgradu aplikace.
* Vyvolání ztrátě kvora na stavové služby, chcete-li vytvořit situaci, kdy operace zápisu nemůže pokračovat, protože nejsou k dispozici dostatek "zálohování" nebo "sekundární" repliky tak, aby přijímal nová data.
* Vyvolání ztrátě dat na stavové služby pro vytvoření situaci, kde všechny stavy v paměti je zcela k vymazání.

Scénáře jsou komplexní operace skládá z jedné nebo více akcí. Služba Analysis odolnost poskytuje dva předdefinované kompletní scénáře:

* Scénář Chaos
* Scénář převzetí služeb při selhání

## <a name="testing-as-a-service"></a>Testování jako služby
Služba Analysis selhání je systémová služba Service Fabric, která se automaticky spustí s cluster Service Fabric. Tato služba funguje jako hostitel pro pravděpodobnost vkládání, spuštění testu scénáři a analýz stavu. 

![Služba analýza selhání][0]

Při selhání akce nebo testovací scénář se zahájí, je odeslán příkaz ke službě analýza selhání spuštění akce nebo testovací scénář selhání. Služba Analysis selhání je stavová, aby mohli spolehlivě spuštění chyb a scénáře a ověření výsledky. Například scénář testovací dlouho běžící lze spolehlivě provést pomocí služby analýzy selhání. A protože testy se spouštějí v clusteru, služba můžete zkontrolovat stav clusteru a vaše služby, aby poskytují podrobnější informace o selhání.

## <a name="testing-distributed-systems"></a>Testování distribuovaných systémů
Service Fabric usnadňuje práci zápis a správu distribuovaných aplikací škálovatelný výrazně jednodušší. Služba Analysis Service selhání tomu bude testování podobně jednodušší distribuované aplikace. Existují tři hlavní problémy, které je třeba vyřešit při testování:

1. Simulaci nebo generování chyb, které můžou nastat ve scénářích reálného světa: jeden z důležité aspekty Service Fabric je, že umožňuje distribuované aplikace pro zotavení při různých selháních. Ale otestujete tak, že aplikace je možné obnovit z těchto chyb, potřebujeme mechanismus pro simulaci nebo generování reálného selhání v řízené testovacím prostředí.
2. Možnost generování korelační selhání: základní selhání v systému, jako je například selhání sítě a selhání počítače, je snadné vytvoření jednotlivě. Generování velký počet scénáře, které může dojít ve skutečnosti v důsledku interakce jednotlivých selhání je netriviální.
3. Jednotné rozhraní mezi různé úrovně vývoj a nasazení: existuje mnoho selhání vkládání systémy, které můžete provést různých typů chyb. Prostředí ve všech těchto je však nízký při přesunu z jedné pole Vývojářské scénáře, na stejnou testů v prostředích s velkými testovacími k jejich používání pro testy v produkčním prostředí.

Existuje mnoho mechanismy k řešení těchto problémů, systém, který nemá stejné s požadované záruky – úplně z jedné pole vývojářského prostředí, otestovat v produkčních clusterů – chybí. Služba Analysis Service selhání pomáhá vývojáři aplikace soustředit se jenom na své obchodní logiky testování. Služba Analysis Service selhání nabízí všechny funkce, které jsou potřebné k testování služby interakci s základní distribuovaného systému.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Simulaci generování scénářích reálného selhání
K testování odolnosti proti selhání distribuovaného systému, potřebujeme mechanismus ke generování chyb. Zatímco teoreticky generování selhání jako vypnutý uzel zdá se, že snadno, začne stiskne stejnou sadu konzistence problémy, které Service Fabric se snaží vyřešit. Například pokud nám chcete vypnout uzlu, požadované pracovní postup je následující:

1. Z klienta vydejte žádost o vypnutí uzlu.
2. Odešlete požadavek správný uzel.
   
    a. Pokud není nalezen uzel, má selhat.
   
    b. Pokud je nalezen uzel, má být vrácen pouze pokud je vypnutý uzel.

Ověření chyby z hlediska testu, test musí vědět, že pokud je toto selhání vyvolané, selhání ve skutečnosti se stane. Záruka, že Service Fabric nabízí je, že buď uzel přejde nebo již byla dolů, když příkaz dosaženo uzlu. V obou případech bude test schopen správně důvodu týkající se stavu a úspěch nebo neúspěch správně v jeho ověření. Implementována mimo Service Fabric udělat stejnou sadu selhání systému může dosáhl mnoho sítě, hardwaru a softwaru problémy, které by jinak znemožňovaly v poskytování předchozí záruky. V případě problémů uvádí před Service Fabric bude překonfigurována, je stav clusteru, chcete-li vyřešit problémy, a proto služba selhání Analysis Service bude nadále umožnit správnou sadu záruky.

### <a name="generating-required-events-and-scenarios"></a>Generování požadované události a scénáře
Při simulaci selhání reálného konzistentně robustním začínat, je možnost k vygenerování korelační selhání i tougher. Například ke ztrátě dat probíhá stavové služby trvalou při dojít v následujících akcí:

1. Pouze zápisu kvora repliky jsou zachycena na replikaci. Za primární funkce lag všechny sekundární repliky.
2. Zápis kvora ocitne mimo provoz z důvodu repliky směrem dolů (z důvodu balíček kódu nebo uzel směrem dolů).
3. Zápis kvora nelze vraťte vzhledem k tomu, že data pro repliky dojde ke ztrátě (z důvodu poškozeného disku nebo obnovování počítače).

Korelační selhání dojít v reálném světě, ale ne tak často jednotlivých selhání. Umožňuje otestovat pro tyto scénáře před dějí v produkčním prostředí je důležité. Ještě důležitější je schopnost simulovat tyto scénáře s produkčním prostředí v řízené případech (uprostřed den s všechny techniky podlaží). To je mnohem lepší, než s jeho dojít poprvé v produkčním prostředí ve 2:00

### <a name="unified-experience-across-different-environments"></a>Jednotném rozhraní různých prostředích
Praxi tradičně byl vytvořit tři různé sady prostředí, jeden pro vývojové prostředí, jeden pro testy a jednu pro produkční prostředí. Model byl:

1. Ve vývojovém prostředí vytvářet přechodů mezi stavy, které umožňují testování částí jednotlivých metod.
2. V testovacím prostředí vytvářet selhání umožňující začátku do konce testy, které vykonává různé scénáře selhání.
3. Udržování produkčního prostředí pristine zabránit jeho selhání bez přirozený a zkontrolujte, zda je velmi rychlý lidského odpovědi k chybě.

V Service Fabric, prostřednictvím služby Analysis Service selhání jsme hodlají zapněte to a používat stejnou metodu z vývojářského prostředí do produkčního prostředí. Chcete-li dosáhnout dvěma způsoby:

1. K způsobit selhání řízené, použijte selhání API služby Analysis z prostředí jeden pole úplně do produkčních clusterů.
2. Cluster prasat, který způsobuje, že automatické indukční chyb, použijte službu analýzy selhání generovat automatické selhání. Řízení frekvence selhání prostřednictvím konfigurace umožňuje stejnou službu, která má být testována jinak v různých prostředích.

Pomocí Service Fabric i když rozsahu selhání se liší v různých prostředích, skutečné mechanismy by identické. To umožňuje mnohem rychlejší kódu na deployment kanálu a schopnost testování služeb v rámci reálného zatížení.

## <a name="using-the-fault-analysis-service"></a>Pomocí služby analýza selhání
**C#**

Funkce Analysis Service odolnosti jsou v oboru názvů System.Fabric v balíčku Microsoft.ServiceFabric NuGet. Pokud chcete používat funkce selhání Analysis Service, obsahovat balíček nuget jako odkaz ve vašem projektu.

**PowerShell**

Pomocí prostředí PowerShell, je nutné nainstalovat Service Fabric SDK. Po instalaci sady SDK je modul ServiceFabric PowerShell automaticky načíst budete muset používat.

## <a name="next-steps"></a>Další postup
Pokud chcete vytvořit skutečně cloudové služby, je důležité zajistit, před i po nasazení, aby služby můžete odolat selhání skutečných. Ve světě služby v současné době možnost inovacemi. Zajistěte snadno a rychle přesouvat kódu do produkčního prostředí je velmi důležité. Služba Analysis Service selhání pomáhá vývojářům služby k tomu přesněji.

Zahájit testování vaší aplikace a služby pomocí integrovaných [scénáře otestovat](service-fabric-testability-scenarios.md), nebo vytvořit vlastní scénáře testování pomocí [poruch akce](service-fabric-testability-actions.md) poskytovaný službou Analysis selhání.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
