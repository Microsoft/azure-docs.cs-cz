---
title: Zotavení po havárii a geografická distribuce v trvanlivý funkce – Azure
description: Další informace o zotavení po havárii a geografická distribuce v trvanlivý funkce.
services: functions
author: MS-Santi
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 8eb42a60045304416ec6aa1099a84b1e264c692d
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="disaster-recovery-and-geo-distribution"></a>Zotavení po havárii a geografická distribuce

## <a name="overview"></a>Přehled
V Azure Functions odolné je stav všech uchovávané v úložišti Azure. A [úloh centra](durable-functions-task-hubs.md) je logický kontejner pro prostředky úložiště Azure, které se používají pro orchestrations. Funkce produktu Orchestrator a aktivity můžete vzájemné interakce pouze při náleží do stejné úlohy rozbočovače.
Popisuje scénáře navrhnout možnosti nasazení pro zvýšení dostupnosti a minimalizovat prostoje během aktivity obnovení po havárii.
Je důležité si všimněte si, že tyto scénáře jsou založeny na aktivní – pasivní konfigurace, vzhledem k tomu, že jsou provést podle využití úložiště Azure. Tento vzor spočívá v nasazení aplikace na zálohování (pasivní) funkce v jiné oblasti. Správce provozu se monitorovat aplikaci primární (aktivní) funkce dostupnosti. Ho bude převzetí služeb při selhání aplikace zálohovací funkce Pokud selže primární. Další informace najdete v tématu [Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)na [metodu směrování provozu s prioritou.](../traffic-manager/traffic-manager-routing-methods.md#a-name--priorityapriority-traffic-routing-method)


>[!NOTE]
>- Navrhovaná konfigurace aktivní-pasivní zajišťuje, že klient vždycky moct aktivovat novou orchestrations prostřednictvím protokolu HTTP. Ale vzhledem s dvě funkce aplikace pro sdílení stejné úložiště, zpracování na pozadí budou distribuována mezi oba dva neslučitelných pro zprávy na stejné fronty. Tato konfigurace způsobuje v přidaném odchozí náklady na aplikaci sekundární funkce.
>- Základní rozbočovače účet a úloh úložiště jsou vytvořené v primární oblasti a jsou sdíleny obě funkce aplikace.
>- Všechny funkce aplikace, které jsou nasazeny redundantně, musejí sdílet stejné funkce přístupové klíče v případě aktivace probíhá prostřednictvím protokolu HTTP. Zpřístupní Functions Runtime [rozhraní API pro správu](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) umožňující příjemcům prostřednictvím kódu programu Přidání, odstranění a aktualizovat funkční klávesy.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scénář 1 - výpočetní se sdíleným úložištěm s vyrovnáváním zatížení
Pokud se nezdaří výpočetní infrastrukturu v Azure, funkce aplikace mohly být nedostupné. Aby se minimalizovala možnost takové výpadku, tento scénář používá dvě funkce aplikace nasazené na různých oblastech. Traffic Manager nakonfigurován pro zjišťování problémů v aplikaci primární funkce a automaticky přesměrování provozu na aplikaci funkce v sekundární oblasti. Tato funkce aplikace sdílí stejný účet úložiště Azure a úloh rozbočovače. Proto stav aplikace pro funkce není ztraceny a práci můžete pokračovat normálně. Po obnovení stavu na primární oblasti Azure Traffic Manager se automaticky spustí směrování žádosti o tuto aplikaci funkce.


![Diagram znázorňující scénář 1.](media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Při použití tohoto scénáře nasazení existuje několik výhod:
- V případě selhání na výpočetní infrastruktuře pracovní může pokračovat v převzetí přes oblast bez ztráty stavu.
- Správce provozu se stará o automatické převzetí služeb při selhání do pořádku funkce aplikace automaticky.
- Traffic Manager automaticky znovu naváže provoz do primární funkce aplikace po se výpadek byl opraven.

Ale, zvažte použití tohoto scénáře:
- Pokud funkce aplikace se nasazuje pomocí vyhrazené plán služby App Service, se replikuje na výpočetní infrastruktuře v služby při selhání přes datacenter zvyšují i náklady.
- Tento scénář popisuje výpadky na výpočetní infrastruktuře, ale účet úložiště je stále jediný bod selhání pro funkce aplikace. Pokud dojde k výpadku úložiště, vyskytne se v aplikaci s prodlevou.
- Pokud je funkce aplikace při selhání, budou existovat zvýší latence od jeho účtu úložiště bude přistupovat přes oblasti.
- Přístup k službě úložiště z jiné oblasti, kde je umístěn způsobuje v vyšší náklady z důvodu síťový odchozí provoz.
- Tento scénář závisí na Traffic Manager. Vzhledem k tomu, [fungování Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), může být nějakou dobu klientskou aplikaci, která využívá trvanlivý funkce potřebuje dotazovat znovu adresu funkce aplikace z Traffic Manager. 


## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scénář 2 - výpočetní s místní úložiště s vyrovnáváním zatížení
Předchozí scénář popisuje pouze v případě selhání v na výpočetní infrastruktuře. Pokud službu úložiště selže, bude výsledkem výpadku funkce aplikace.
Aby se zajistilo průběžné operace trvanlivý funkcí, tento scénář používá účet místní úložiště na každou oblast, do které funkce aplikace nasazená.

![Diagram znázorňující scénář 2.](media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Tento postup přidá vylepšení v předchozím scénáři:
- Pokud aplikaci funkce selže, má na starosti Traffic Manager převzetí služeb při selhání pro sekundární oblast. Ale vzhledem k aplikaci funkce závisí na svůj vlastní účet úložiště, trvanlivý funkce pokračovat v práci.
- Během převzetí služeb při selhání, další latence neexistuje v služby při selhání přes oblast, protože funkce aplikace a účet úložiště se společně umístěné.
- Selhání vrstvy úložiště způsobí selhání trvanlivý funkcí, které se pak spustí přesměrování na služby při selhání přes oblast. Znovu protože funkce aplikace a úložiště jsou izolované každou oblast, trvanlivý funkce budou nadále fungovat.
 
Důležité informace pro tento scénář:
- Pokud funkce aplikace se nasazuje pomocí vyhrazené plán aplikační služby, se replikuje na výpočetní infrastruktuře v služby při selhání přes datacenter zvyšují i náklady.
- Aktuální stav není převzetí služeb při selhání, což znamená, že provádění a kontrolní bod funkce se nezdaří. Je to na klientskou aplikaci pro opakování nebo restartování práce.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scénář 3 - výpočetní s GRS sdíleného úložiště s vyrovnáváním zatížení
Tento scénář je změna přes prvního scénáře, implementace účet sdílené úložiště. Hlavní rozdíl vytvořený účet úložiště s povolenou geografickou replikací.
Funkčně tento scénář poskytuje stejné výhody jako scénář 1, ale umožňuje obnovení výhody další data:
- Geograficky redundantní úložiště (GRS) a maximalizovat dostupnost pro váš účet úložiště GRS přístup pro čtení (RA-GRS).
- Pokud je oblast výpadek služby úložiště, je jednou z možností, operací datového centra určit, že úložiště musí být přebrány do sekundární oblasti. V takovém případě bude přístup k účtu úložiště přesměrován transparentně na kopii geograficky replikované účet úložiště, bez zásahu uživatele.
- V tomto případě stav trvanlivý funkce zachovají až poslední replikace účtu úložiště, který probíhá každých několik minut.
Stejně jako u jiných scénáře jsou důležité informace:
- Převzetí služeb při selhání v replice se provádí operátorů datacenter a může chvíli trvat. Do té doby bude funkce aplikace dochází k výpadku.
- Není vyšší náklady na používání geograficky replikované úložiště účtů.
- GRS nastane asynchronně. Některé z nejnovější transakce mohou být ztraceny z důvodu latence v procesu replikace.

![Diagram znázorňující scénář 3.](media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)


## <a name="next-steps"></a>Další postup

Další informace o [navrhování vysoce dostupné aplikace pomocí RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)
