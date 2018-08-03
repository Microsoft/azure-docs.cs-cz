---
title: Přehled služby analýzy selhání | Dokumentace Microsoftu
description: Tento článek popisuje Fault Analysis Service ve službě Service Fabric pro vyvolat chyby a spouštění testovacích scénářů vašich služeb.
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
ms.openlocfilehash: a4ddfc17a81a6816bc797bab4c3b5a8b2fc4334e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425234"
---
# <a name="introduction-to-the-fault-analysis-service"></a>Úvod do Fault Analysis Service
Fault Analysis Service je určená pro testování služeb, které jsou postavené na Microsoft Azure Service Fabric. Fault Analysis Service můžete zahájit smysluplné chyb a spouštějte scénáře dokončení testování vašich aplikací. Tyto chyby a scénáře výkon a ověřit řadu stavy a přechody, která službu budou moct používat v průběhu svého životního cyklu, všechny řízené, bezpečné a konzistentním způsobem.

Akce jsou jednotlivé chyby cílit na službu pro testování. Služba vývojáři mohou využít jako stavební bloky pro zápis složité scénáře. Příklad:

* Restartujte uzel pro simulaci libovolný počet situacích, kdy po restartování počítače nebo virtuálního počítače.
* Přesunutí repliky stavové služby pro simulaci Vyrovnávání zatížení a převzetí služeb při selhání nebo upgrade aplikace.
* Vyvolání ztráty kvora na stavové služby k nastat situace, kdy operace zápisu nemůže pokračovat, protože nejsou k dispozici dostatek "zálohování" nebo "sekundární" repliky tak, aby přijímal nová data.
* Vyvolání ztráty dat na stavové služby k nastat situace, kdy všechny stavy v paměti je zcela dojde k vymazání.

Scénáře jsou složité operace skládá z jedné nebo více akcí. Fault Analysis Service poskytuje dvě předdefinované kompletní scénáře:

* Scénář chaosu
* Scénáře převzetí služeb při selhání

## <a name="testing-as-a-service"></a>Testování jako služba
Fault Analysis Service je služba systému Service Fabric, která se automaticky spustí s clusterem Service Fabric. Tato služba funguje jako hostitel pro vkládání chyb, scénář provádění testů a analýzu stavu. 

![Fault Analysis Service][0]

Když je zahájeno selhání akce nebo testovací scénář, příkaz je odesílat Fault Analysis Service spustit selhání akce nebo testovací scénář. Fault Analysis Service je stavový, takže můžete spolehlivě spouštět scénáře a chyby a ověřte výsledky. Například scénář dlouho běžící test můžete spolehlivě provést Fault Analysis Service. A vzhledem k tomu, že testy se spouštějí v clusteru, služba můžete zkontrolovat stav clusteru a poskytují podrobnější informace o selhání vaší služby.

## <a name="testing-distributed-systems"></a>Testování distribuovaných systémů
Service Fabric zajišťuje práce pro zápis a správa škálovatelných aplikací distribuovaných výrazně usnadňuje. Fault Analysis Service je testování jednodušší podobně distribuované aplikace. Existují tři hlavní problémy, které je potřeba vyřešit při testování:

1. Simulace a generování chyb, které mohou nastat situace ze skutečného světa: jedním z důležitých aspektů Service Fabric je, že umožňuje distribuované aplikace k zotavení z různých chyb. Pokud chcete otestovat, že aplikace je možné obnovit z těchto chyb, ještě ale zbývá mechanismus pro simulaci/generovat skutečná selhání v řízeném testovacím prostředí.
1. Generovat korelační selhání: základní selhání v systému, jako je například selhání sítě a selhání v počítači, je snadné je vytvořit samostatně. Generování velký počet scénářů, které může dojít v reálném světě v důsledku interakce jednotlivá selhání je netriviální.
1. Jednotné rozhraní pro vývoj a nasazení od různých úrovních: existují mnoho selhání vkládání systémů, které můžete dělat různé typy chyb. Prostředí pro všechny z nich ale nízký při přesunu z jedné integrované Vývojářské scénáře ke spuštěnému stejné testy v prostředích s velkými testovacími je může používat pro testování v produkčním prostředí.

I když existují mnoho mechanismů k řešení těchto problémů, systém, který dělá totéž s požadované záruky – od jedné integrované vývojářské prostředí, otestovat v produkčních clusterech – chybí. Fault Analysis Service pomáhá vývojářům aplikací soustředit na své obchodní logiky testování. Fault Analysis Service nabízí všechny funkce potřebné pro testování interakce službu pomocí základního distribuovaného systému.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Simulace/generování selhání reálné scénáře
Pokud chcete testovat odolnost distribuovaného systému proti chybám, potřebujeme mechanismus k vygenerování chyby. Zatímco teoreticky generování selhání, jako je výpadek uzlu zdá se, že jednoduché, začne dosažení stejnou sadu konzistence problémy, které Service Fabric se pokouší vyřešit. Jako příklad Pokud chcete vypnout uzlu, vyžaduje pracovní postup je následující:

1. Z klienta vydejte žádost o vypnutí uzlu.
1. Odešlete požadavek na správný uzel.
   
    a. Pokud uzel není nalezen, mělo by selhat.
   
    b. Pokud je nalezen uzel, měla by vrátit pouze pokud je vypnutý uzel.

Ověřit chyby z hlediska test, je potřeba vědět, že při této chyby je způsobený, skutečně dojde k selhání testu. Záruka, že Service Fabric nabízí, je, že buď uzel se vypne nebo byl již dolů po příkazu dosažení uzlu. V obou případech test by měl být moct správně z důvodu informace o stavu a úspěch nebo neúspěch správně v jeho ověření. Systém implementována mimo Service Fabric provést stejnou sadu selhání může přístupů mnoho sítě, hardwaru a softwaru problémy, které by jinak znemožňovaly zadáním předchozí záruky. Za přítomnosti problémy bylo uvedeno dříve, Service Fabric překonfiguruje stav clusteru, který má tyto problémy obejít, a proto Fault Analysis Service budou mít pořád povolený poskytnout správnou sadu záruky.

### <a name="generating-required-events-and-scenarios"></a>Generování požadované události a scénáře
Při simulaci reálné selhání konzistentně je složité začít, je i tougher generovat korelační selhání. Například dojde ke ztrátě dat stavové trvalé služby při následujících akcí:

1. Jenom zápis kvorum replik jsou zachycena na replikaci. Všechny sekundární repliky bude tak hrozit primární.
1. Zápis kvora ocitne mimo provoz kvůli repliky směrem dolů (z důvodu balíček kódu nebo uzel počítače).
1. Zápis kvora nelze vrátit protože data pro repliky (kvůli poškození disku nebo obnovování z Image počítače).

Korelační selhání dojde v reálném světě, ale ne jako často jednotlivá selhání. Umožňuje otestovat pro tyto scénáře předtím, než k nim dojde v produkčním prostředí je velmi důležité. Ještě důležitější je schopnost simulovat scénářů produkční úlohy v řízeném okolností (uprostřed den s všechny, kteří v balíčku). To je mnohem lepší, než když to uděláme poprvé v produkčním prostředí ve 2:00:00

### <a name="unified-experience-across-different-environments"></a>Unifikovaného prostředí napříč různými prostředími
Praxi tradičně bylo vytvořit tři různé sady prostředí, jeden pro vývojové prostředí, jeden pro testy a jeden pro produkční prostředí. Model byl:

1. Ve vývojovém prostředí vytvářet přechodů mezi stavy, které umožňují testování částí jednotlivých metod.
1. V testovacím prostředí vytvořit povolených – celkové testy, které vykonávají různé scénáře selhání neúspěšných.
1. Zachovejte pristine, aby se zabránilo chybám bez přirozeného a ujistěte se, že je velmi rychlý lidské odpovědi na chybu v provozním prostředí.

V Service Fabric prostřednictvím Fault Analysis Service jsme hodlají to požadujeme neexkluzivní a použijte stejnou metodu z vývojářského prostředí do produkčního prostředí. Existují dva způsoby, jak toho dosáhnout:

1. Pokud chcete zahájit řízeného selhání, použijte selhání API služby analýzy z jedné integrované prostředí až po produkční clustery.
1. Cluster prasat, který způsobí, že automatické indukční selhání, použijte Fault Analysis Service k vygenerování automatické selhání. Řízení množství chyb prostřednictvím konfigurace umožňuje stejnou službu, která má být testována odlišně v různých prostředích.

S využitím Service Fabric i když bude selhání stupnice odlišná v různých prostředích, skutečné mechanismy by být identické. To umožňuje mnohem rychleji kód nasazení kanálu a schopnost testování služeb v rámci reálné zátěže.

## <a name="using-the-fault-analysis-service"></a>Pomocí Fault Analysis Service
**C#**

Funkce odolnosti službu Analysis Services jsou v oboru názvů System.Fabric – v balíčku Microsoft.ServiceFabric NuGet. Pokud chcete používat funkce Fault Analysis Service, zahrnují balíček nuget jako odkaz v projektu.

**PowerShell**

Jak pomocí prostředí PowerShell, je nutné nainstalovat sadu Service Fabric SDK. Po dokončení instalace sady SDK modulu ServiceFabric PowerShell se automaticky načtena pro použití.

## <a name="next-steps"></a>Další postup
K vytvoření skutečně cloudových služeb, je důležité zajistit, před i po nasazení se, že služby můžete odolat selhání reálného světa. Ve světě služby v současné době umožňuje rychle inovovat a rychle přesunout kód do produkčního prostředí je velmi důležité. Fault Analysis Service pomáhá vývojářům služby udělat přesně tohle zjistíte.

Začít testovat svoje aplikace a služby pomocí integrovaných [otestování scénářů](service-fabric-testability-scenarios.md), nebo vytvořte vlastní testovací scénáře použití [selhání akce](service-fabric-testability-actions.md) poskytované Fault Analysis Service.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
