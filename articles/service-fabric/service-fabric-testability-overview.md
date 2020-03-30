---
title: Přehled služby analýzy poruch
description: Tento článek popisuje službu analýzy chyb v service fabric pro vyvolání chyb a spuštění scénářů testů proti vašim službám.
author: anmolah
ms.topic: conceptual
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: d5c770a4d823ebe9b2700b081c407c54dd1d18a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465564"
---
# <a name="introduction-to-the-fault-analysis-service"></a>Úvod do služby analýzy poruch
Služba Fault Analysis Service je určena pro testování služeb, které jsou postavené na webu Microsoft Azure Service Fabric. Se službou Fault Analysis Service můžete vyvolat smysluplné chyby a spustit kompletní testovací scénáře proti vašim aplikacím. Tyto chyby a scénáře cvičení a ověřit četné stavy a přechody, které služba bude zažívat po celou dobu své životnosti, to vše řízeným, bezpečným a konzistentním způsobem.

Akce jsou jednotlivé chyby, které cílí na službu pro testování. Vývojář služby můžete použít jako stavební bloky psát složité scénáře. Například:

* Restartujte uzel, abyste simulovali libovolný počet situací, kdy je počítač nebo virtuální počítač restartován.
* Přesuňte repliku stavové služby a simulujte vyrovnávání zatížení, převzetí služeb při selhání nebo upgrade aplikace.
* Vyvolat ztrátu kvora na stavové služby k vytvoření situace, kdy operace zápisu nemůže pokračovat, protože není dostatek "zálohování" nebo "sekundární" repliky přijímat nová data.
* Vyvolat ztrátu dat na stavové služby k vytvoření situace, kdy je zcela vymazány všechny v paměti stavu.

Scénáře jsou složité operace složené z jedné nebo více akcí. Služba analýzy poruch poskytuje dva předdefinované úplné scénáře:

* Scénář chaosu
* Scénář převzetí služeb při selhání

## <a name="testing-as-a-service"></a>Testování jako služba
Služba Analýzy chyb je systémová služba Service Fabric, která je automaticky spuštěna pomocí clusteru Service Fabric. Tato služba funguje jako hostitel pro vkládání chyb, spuštění testovacího scénáře a analýzu stavu. 

![Služba analýzy poruch][0]

Při zahájení akce poruchy nebo testovacího scénáře je službě Fault Analysis Service odeslán příkaz ke spuštění akce poruchy nebo testovacího scénáře. Služba analýzy chyb je stavová, takže může spolehlivě spouštět chyby a scénáře a ověřovat výsledky. Například dlouhotrvající testovací scénář může být spolehlivě proveden službou Fault Analysis Service. A protože testy jsou prováděny uvnitř clusteru, služba můžete zkontrolovat stav clusteru a služby poskytnout podrobnější informace o selhání.

## <a name="testing-distributed-systems"></a>Testování distribuovaných systémů
Service Fabric výrazně usnadňuje psaní a správu distribuovaných škálovatelných aplikací. Služba Fault Analysis Service usnadňuje testování distribuované aplikace podobně. Existují tři hlavní problémy, které je třeba vyřešit při testování:

1. Simulace nebo generování chyb, ke kterým může dojít v reálných scénářích: Jedním z důležitých aspektů service fabric je, že umožňuje distribuovaným aplikacím zotavit se z různých selhání. Chcete-li však otestovat, že aplikace je schopna zotavit se z těchto selhání, potřebujeme mechanismus pro simulaci nebo generování těchto selhání reálného světa v řízeném testovacím prostředí.
1. Schopnost generovat korelované selhání: Základní selhání v systému, jako jsou selhání sítě a selhání počítače, lze snadno vytvářet jednotlivě. Generování značného počtu scénářů, které se mohou stát v reálném světě v důsledku interakcí těchto jednotlivých selhání, je netriviální.
1. Sjednocené prostředí na různých úrovních vývoje a nasazení: Existuje mnoho systémů vkládání chyb, které mohou dělat různé typy selhání. Zkušenosti ve všech těchto je však špatná při přechodu z scénáře pro vývojáře s jedním boxem na spuštění stejných testů ve velkých testovacích prostředích k jejich použití pro testy v produkčním prostředí.

I když existuje mnoho mechanismů k řešení těchto problémů, systém, který dělá totéž s požadovanými zárukami - celou cestu z prostředí pro vývojáře s jedním boxem k testování v produkčních clusterech - chybí. Služba Fault Analysis Service pomáhá vývojářům aplikací soustředit se na testování jejich obchodní logiky. Služba Fault Analysis Service poskytuje všechny funkce potřebné k testování interakce služby se základním distribuovaným systémem.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Simulace/generování reálných scénářů selhání
Chcete-li otestovat robustnost distribuovaného systému proti selhání, potřebujeme mechanismus pro generování selhání. Zatímco teoreticky generování selhání jako uzel dolů se zdá snadné, začne bít stejnou sadu konzistence problémy, které Service Fabric se snaží vyřešit. Například pokud chceme vypnout uzel, je povinný pracovní postup následující:

1. Z klienta vydejte požadavek uzlu vypnutí.
1. Odešlete požadavek do pravého uzlu.
   
    a. Pokud uzel nebyl nalezen, by měl selhat.
   
    b. Pokud je uzel nalezen, měl by se vrátit pouze v případě, že je uzel vypnut.

Chcete-li ověřit selhání z hlediska testu, test potřebuje vědět, že když je tato chyba vyvolána, selhání skutečně dojde. Záruka, že Service Fabric poskytuje je, že uzel přejde dolů nebo byl již dolů, když příkaz dosáhl uzlu. V obou případech by měl být test schopen správně uvažovat o stavu a úspěšné nebo správně selhat při jeho ověření. Systém implementovaný mimo service fabric k provedení stejné sady selhání by mohl zasáhnout mnoho problémů se sítí, hardwarem a softwarem, což by mu zabránilo v poskytování předchozích záruk. V případě problémů uvedených dříve Service Fabric překonfiguruje stav clusteru, aby problémy obešly, a proto služba analýzy chyb bude stále schopna poskytnout správnou sadu záruk.

### <a name="generating-required-events-and-scenarios"></a>Generování požadovaných událostí a scénářů
Zatímco simulace selhání reálného světa důsledně je těžké začít s, schopnost generovat korelované selhání je ještě těžší. Například ke ztrátě dat dochází ve stavové trvalé službě, když dojde k následujícím věcem:

1. Replikace je zachycena pouze kvorem zápisu replik. Všechny sekundární repliky zaostávají za primární.
1. Kvorum zápisu se vypne z důvodu repliky jít dolů (z důvodu balíček kódu nebo uzel bude dolů).
1. Kvorum zápisu se nemůže vrátit nahoru, protože data pro repliky jsou ztracena (z důvodu poškození disku nebo reimaging počítače).

K těmto korelačním selháním dochází v reálném světě, ale ne tak často jako k individuálním selháním. Schopnost testovat tyto scénáře dříve, než k nim dojde v produkčním prostředí, je kritická. Ještě důležitější je schopnost simulovat tyto scénáře s produkčními úlohami za kontrolovaných okolností (uprostřed dne se všemi inženýry na palubě). To je mnohem lepší, než mít to stalo poprvé ve výrobě ve 2:00 ráno.

### <a name="unified-experience-across-different-environments"></a>Jednotné prostředí v různých prostředích
Praxe tradičně byla vytvořit tři různé sady zkušeností, jeden pro vývojové prostředí, jeden pro testy, a jeden pro výrobu. Model byl:

1. Ve vývojovém prostředí vytvářejte přechody stavu, které umožňují testování částí jednotlivých metod.
1. V testovacím prostředí vytvořit selhání povolit end-to-end testy, které vykonávají různé scénáře selhání.
1. Udržujte produkční prostředí nedotčené, aby se zabránilo jakýmkoli nepřirozeným poruchám a zajistilo se, že na selhání dojde k extrémně rychlé lidské reakci.

V Service Fabric, prostřednictvím služby Fault Analysis Service, navrhujeme to toto pořadí a používat stejnou metodiku od vývojářského prostředí až po produkční prostředí. Existují dva způsoby, jak toho dosáhnout:

1. Chcete-li vyvolat řízené chyby, použijte api služby Analýzy chyb z prostředí s jedním boxem až po produkční clustery.
1. Chcete-li clusteru horečku, která způsobuje automatické indukci selhání, použijte Fault Analysis Service generovat automatické selhání. Řízení rychlosti selhání prostřednictvím konfigurace umožňuje stejnou službu, která má být testována odlišně v různých prostředích.

S Service Fabric, i když rozsah selhání by se liší v různých prostředích, skutečné mechanismy by být identické. To umožňuje mnohem rychlejší kanál pro nasazení a možnost testovat služby v rámci reálných zatížení.

## <a name="using-the-fault-analysis-service"></a>Použití služby analýzy poruch
**C #**

Funkce služby Analysis Service jsou v oboru názvů System.Fabric v balíčku Microsoft.ServiceFabric NuGet. Chcete-li použít funkce služby Fault Analysis Service, zahrňte balíček nuget jako referenci v projektu.

**PowerShell**

Chcete-li použít prostředí PowerShell, je nutné nainstalovat sadku Service Fabric SDK. Po instalaci sady SDK se modul ServiceFabric PowerShell automaticky načte, abyste jej mohl(a) použít.

## <a name="next-steps"></a>Další kroky
Chcete-li vytvořit skutečně cloudové služby, je důležité zajistit, před i po nasazení, že služby vydrží selhání reálného světa. V dnešním světě služeb je velmi důležitá schopnost rychle inovovat a rychle přesunout kód do výroby. Služba Fault Analysis Service pomáhá vývojářům služeb dělat přesně to.

Začněte testovat aplikace a služby pomocí předdefinovaných [testovacích scénářů](service-fabric-testability-scenarios.md)nebo vytvořte vlastní testovací scénáře pomocí [akcí chyb](service-fabric-testability-actions.md) poskytovaných službou Fault Analysis Service.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
