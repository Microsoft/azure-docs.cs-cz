---
title: Přehled služby analýzy chyb
description: Tento článek popisuje službu analýzy chyb v Service Fabric pro vyvolání chyb a spouštění testovacích scénářů pro vaše služby.
ms.topic: conceptual
ms.date: 06/15/2017
ms.openlocfilehash: 4e879b0b39d58f115b4661d47cf2ae564ba14e5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91531373"
---
# <a name="introduction-to-the-fault-analysis-service"></a>Seznámení se službou analýzy chyb
Služba analýzy chyb je navržena pro testování služeb, které jsou postaveny na Microsoft Azure Service Fabric. Pomocí služby analýzy chyb můžete navolávat smysluplné chyby a spouštět kompletní testovací scénáře pro vaše aplikace. Tyto chyby a scénáře cvičení a ověřují množství stavů a přechodů, ke kterým dojde v průběhu své životnosti, a to vše v kontrolovaném, bezpečném a konzistentním způsobem.

Akce jsou jednotlivé chyby, které cílí na službu pro její otestování. Vývojář služby může tyto prvky použít jako stavební bloky pro psaní složitých scénářů. Například:

* Restartujte uzel, aby se simuloval libovolný počet situací, kdy se počítač nebo virtuální počítač restartuje.
* Přesuňte repliku stavové služby pro simulaci vyrovnávání zatížení, převzetí služeb při selhání nebo upgradu aplikace.
* Vyvolat ztrátu kvora pro stavovou službu a vytvořit situaci, kdy operace zápisu nemohou pokračovat, protože pro příjem nových dat nejsou k dispozici dostatek záložních a sekundárních replik.
* Vyvolat ztrátu dat u stavové služby, která vytvoří situaci, kdy je stav veškerého stavu v paměti zcela vymazán.

Scénáře jsou komplexní operace složené z jedné nebo více akcí. Služba analýzy chyb nabízí dva předdefinované scénáře:

* Scénář chaos
* Scénář převzetí služeb při selhání

## <a name="testing-as-a-service"></a>Testování jako služba
Služba analýzy chyb je Service Fabric systémová služba, která se automaticky spustí s clusterem Service Fabric. Tato služba slouží jako hostitel pro vkládání chyb, provádění testovacího scénáře a analýzu stavu. 

![Služba analýzy chyb][0]

Při spuštění akce selhání nebo testovacího scénáře se do služby analýzy chyb odešle příkaz, který spustí akci selhání nebo scénář testu. Služba analýzy chyb je stavová, aby mohla spolehlivě spustit chyby a scénáře a ověřit výsledky. Například dlouhotrvající testovací scénář může být spolehlivě proveden službou analýzy chyb. A vzhledem k tomu, že testy jsou spouštěny v rámci clusteru, služba může prošetřit stav clusteru a vaše služby a poskytnout tak podrobné informace o selhání.

## <a name="testing-distributed-systems"></a>Testování distribuovaných systémů
Service Fabric usnadňuje práci s psaním a správou distribuovaně škálovatelných aplikací. Služba analýzy chyb provádí testování distribuované aplikace podobně jako jednodušší. Existují tři hlavní problémy, které je potřeba vyřešit při testování:

1. Simulace a generování chyb, ke kterým může dojít v reálných scénářích: jedním z důležitých aspektů Service Fabric je to, že umožňuje distribuovaným aplikacím obnovení z různých selhání. Abychom ale mohli otestovat, jestli je aplikace schopná zotavit z těchto selhání, potřebujeme mechanismus, který simuluje nebo generuje tyto chyby v kontrolovaném testovacím prostředí.
1. Možnost generovat korelační selhání: základní selhání v systému, například selhání sítě a selhání počítače, se dají snadno vytvářet jednotlivě. Generování významného počtu scénářů, které se mohou vyskytnout v reálném světě v důsledku interakcí těchto jednotlivých selhání není triviální.
1. Jednotné prostředí napříč různými úrovněmi vývoje a nasazení: existuje řada systémů vkládání chyb, které mohou provádět různé typy selhání. Při přechodu ze scénářů pro vývojáře na jednom místě je však nedostatečné prostředí pro spouštění stejných testů ve velkých testovacích prostředích, aby je bylo možné použít pro testy v produkčním prostředí.

I když existuje celá řada mechanismů, jak tyto problémy vyřešit, má systém, který je stejný s požadovanými zárukami – pro testování v produkčních clusterech nefunguje žádný způsob, jak v prostředí pro vývojáře s jedním polem testovat. Služba analýzy chyb pomáhá vývojářům aplikací soustředit se na testování obchodní logiky. Služba analýzy chyb poskytuje všechny schopnosti potřebné k otestování interakce služby s podkladovým distribuovaným systémem.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Simulace a generování reálných scénářů selhání
Abychom mohli testovat odolnost distribuovaného systému před chybami, potřebujeme mechanismus pro generování selhání. V teoretickém stavu je vygenerování selhání, jako je například uzel dolů, snadné, začíná se stejnou sadou problémů s konzistencí, kterou Service Fabric snaží vyřešit. Pokud třeba chcete vypnout uzel, bude požadovaný pracovní postup následující:

1. Z klienta vydejte požadavek na vypnutí uzlu.
1. Odešle požadavek do pravého uzlu.
   
    a. Pokud uzel nebyl nalezen, měla by být neúspěšná.
   
    b. Pokud je uzel nalezen, měl by vracet pouze v případě, že je uzel vypnut.

Chcete-li ověřit selhání z perspektivy testu, je nutné, aby při vyzkoušení tohoto selhání bylo známo, že při vyvolání tohoto selhání dojde k selhání. Záruka, že Service Fabric poskytuje, je, že uzel bude mimo provoz nebo byl již vypnutý, když příkaz dosáhl uzlu. V obou případech by test měl být schopný správně považovat stav za úspěšný nebo neúspěšný, při ověření. Systém implementovaný mimo Service Fabric k provedení stejné sady selhání může způsobit mnoho problémů se sítí, hardwarem a softwarem, což by mu zabránilo v poskytování výše uvedených záruk. V případě problémů uvedených dříve bude Service Fabric překonfigurovat stav clusteru, aby se mohly problémy vyřešit, a proto bude služba analýzy chyb stále schopna poskytovat správnou sadu záruk.

### <a name="generating-required-events-and-scenarios"></a>Generování požadovaných událostí a scénářů
Při současném simulaci reálného selhání je obtížné začít s, schopnost generovat korelační chyby je ještě obtížné. Například ztráta dat nastane ve stavové trvalé trvalé službě, když dojde k následujícím akcím:

1. Replikace je zachytila pouze pro kvorum zápisu replik. Všechny sekundární repliky jsou zpožděny za primárním serverem.
1. Kvorum zápisu vychází z důvodu nefunkčních replik (kvůli balíčku kódu nebo rozbalení uzlu).
1. Kvorum zápisu nejde zálohovat, protože data pro repliky jsou ztracena (kvůli poškození disku nebo obnovení bitové kopie počítače).

K těmto korelačním selháním dochází v reálném světě, ale ne tak často jako jednotlivé chyby. Možnost testování těchto scénářů dřív, než k nim dojde v produkčním prostředí, je kritická. Ještě důležitější je možnost simulovat tyto scénáře s provozními úlohami za kontrolovaných okolností (uprostřed dne se všemi inženýry na palubě). To je mnohem lepší, než když se poprvé vykoná v produkčním prostředí v 2:00 ráno.

### <a name="unified-experience-across-different-environments"></a>Jednotné prostředí napříč různými prostředími
Zvyklosti tradičně bylo vytvořit tři různé sady prostředí, jednu pro vývojové prostředí, jednu pro testy a jednu pro produkci. Model byl:

1. Ve vývojovém prostředí vyprodukuje přechody stavu, které umožňují testy jednotek jednotlivých metod.
1. V testovacím prostředí vzniká selhání umožňující ucelené testy, které vykonávají různé scénáře selhání.
1. Udržujte provozní prostředí pristine, aby se předešlo jakýmkoli nepřirozeným chybám a bylo zajištěno, že dojde k selhání extrémně rychlá odpověď.

V Service Fabric se prostřednictvím služby analýzy chyb navrhuji tuto funkci rozpracovat a používat stejnou metodologii z vývojářského prostředí do produkčního prostředí. Toho můžete dosáhnout dvěma způsoby:

1. K navýšení neřízených selhání použijte rozhraní API služby analýzy chyb z prostředí s jedním box, které je pro produkční clustery.
1. Pokud chcete dát tomuto clusteru horečku, která způsobí automatické odpočty selhání, vygenerujte automatické chyby pomocí služby analýza chyb. Řízení četnosti selhání prostřednictvím konfigurace umožňuje, aby byla stejná služba v různých prostředích testována jinak.

U Service Fabric, i když by se škálování selhání v různých prostředích lišilo, skutečné mechanismy budou stejné. To umožňuje mnohem rychlejší kanál pro nasazení a možnost testovat služby v rámci reálných zátěží.

## <a name="using-the-fault-analysis-service"></a>Používání služby analýzy chyb
**C#**

Funkce služby analýza chyb jsou v oboru názvů System. Fabric v balíčku NuGet Microsoft. ServiceFabric. Chcete-li používat funkce služby analýza chyb, přidejte do projektu balíček NuGet jako referenci.

**PowerShell**

Pokud chcete používat PowerShell, musíte nainstalovat sadu Service Fabric SDK. Po nainstalování sady SDK se modul ServiceFabric PowerShellu automaticky načte, abyste ho mohli použít.

## <a name="next-steps"></a>Další kroky
Aby bylo možné vytvořit vysoce škálovatelné služby, je nutné před i po nasazení zajistit, aby tyto služby mohly vydržet chyby reálného světa. V dnešní době dnes možnost inovovat rychle a rychle přesunout kód do provozu je velmi důležité. Služba analýzy chyb pomáhá vývojářům služeb přesně to dělat.

Zahajte testování aplikací a služeb pomocí integrovaných [scénářů testování](service-fabric-testability-scenarios.md)nebo vytvořte vlastní testovací scénáře pomocí [akcí selhání](service-fabric-testability-actions.md) poskytovaných službou selhání Analysis Service.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
