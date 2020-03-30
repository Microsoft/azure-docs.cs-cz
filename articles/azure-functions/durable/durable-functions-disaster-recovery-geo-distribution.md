---
title: Zotavení po havárii a geografická distribuce Azure Durable Functions
description: Další informace o zotavení po havárii a geografické distribuci v trvanlivé funkce.
author: MS-Santi
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 7951f216143bef0d48a6b751beff3f8f4316b9bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433336"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Zotavení po havárii a geografická distribuce v trvalých funkcích Azure

V trvalé funkce, veškerý stav je trvalé ve službě Azure Storage. [Centrum úloh](durable-functions-task-hubs.md) je logický kontejner pro prostředky Azure Storage, které se používají pro orchestrations. Funkce Orchestrator a aktivity mohou vzájemně komunikovat pouze v případě, že patří do stejného centra úloh.
Popsané scénáře navrhnou možnosti nasazení pro zvýšení dostupnosti a minimalizaci prostojů během činností zotavení po havárii.

Je důležité si všimnout, že tyto scénáře jsou založeny na konfigurace active pasivní, protože se řídí využití azure storage. Tento vzor se skládá z nasazení aplikace funkce zálohování (pasivní) do jiné oblasti. Traffic Manager bude sledovat dostupnost primární (aktivní) aplikace funkce. Převezme služby při selhání aplikace pro funkci zálohování, pokud primární selže. Další informace naleznete v tématu [Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)'s [Priority Traffic-Routing Method.](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)

>[!NOTE]
>
> - Navrhovaná konfigurace Aktivní pasivní zajišťuje, že klient je vždy schopen aktivovat nové orchestrace přes HTTP. V důsledku toho, že dvě aplikace funkcí sdílejí stejné úložiště, bude mezi nimi distribuováno zpracování na pozadí, které soutěží o zprávy ve stejných frontách. Tato konfigurace vznikne v přidané výstupní náklady pro sekundární funkce aplikace.
> - Základní účet úložiště a centrum úloh jsou vytvořeny v primární oblasti a jsou sdíleny oběma aplikacemi funkcí.
> - Všechny aplikace funkcí, které jsou redundantně nasazeny, musí sdílet stejné přístupové klávesy funkce v případě, že jsou aktivovány prostřednictvím protokolu HTTP. Prostředí Functions Runtime poskytuje [rozhraní API pro správu,](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) které umožňuje spotřebitelům programově přidávat, odstraňovat a aktualizovat funkční klávesy.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scénář 1 – výpočetní prostředky s vyrovnáváním zatížení se sdíleným úložištěm

Pokud výpočetní infrastruktura v Azure selže, aplikace funkce může být nedostupná. Chcete-li minimalizovat možnost takové prostoje, tento scénář používá dvě aplikace funkce nasazené do různých oblastí.
Traffic Manager je nakonfigurován tak, aby detekoval problémy v aplikaci primární funkce a automaticky přesměroval provoz do aplikace funkcí v sekundární oblasti. Tato aplikace funkce sdílí stejný účet Azure Storage a Centrum úloh. Proto není ztracen stav aplikací funkce a práce může pokračovat normálně. Po obnovení stavu do primární oblasti Azure Traffic Manager spustí směrování požadavků na tuto aplikaci funkce automaticky.

![Diagram znázorňující scénář 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Při použití tohoto scénáře nasazení existuje několik výhod:

- Pokud výpočetní infrastruktura selže, práce můžete pokračovat v oblasti převzetí služeb při selhání bez ztráty stavu.
- Traffic Manager se automaticky postará o automatické převzetí služeb při selhání aplikace funkce v pořádku.
- Traffic Manager automaticky obnoví provoz do aplikace primární funkce po výpadku byl opraven.

Však pomocí tohoto scénáře zvažte:

- Pokud se aplikace funkce nasadí pomocí vyhrazeného plánu služby App Service, replikace výpočetní infrastruktury v datovém centru převzetí služeb při selhání zvyšuje náklady.
- Tento scénář zahrnuje výpadky na výpočetní infrastruktury, ale účet úložiště i nadále jediný bod selhání pro funkci App. Pokud dojde k výpadku úložiště, aplikace trpí výpadky.
- Pokud je aplikace funkce převzetí mů e-la, bude zvýšená latence, protože bude přistupovat ke svému účtu úložiště napříč oblastmi.
- Přístup ke službě úložiště z jiné oblasti, kde se nachází, způsobuje vyšší náklady z důvodu přenosu odchozího přenosu sítě.
- Tento scénář závisí na Traffic Manager. Vzhledem k tomu, [jak Traffic Manager funguje](../../traffic-manager/traffic-manager-how-it-works.md), může být nějakou dobu, dokud klientská aplikace, která spotřebovává trvalé funkce potřebuje dotaz znovu adresu aplikace funkce z Traffic Manager.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scénář 2 – výpočetní prostředky s vyrovnáváním zatížení s regionálním úložištěm

Předchozí scénář zahrnuje pouze případ selhání ve výpočetní infrastruktuře. Pokud služba úložiště selže, bude mít za následek výpadek aplikace funkce.
K zajištění nepřetržitého provozu trvalých funkcí tento scénář používá účet místního úložiště v každé oblasti, do které jsou nasazené aplikace funkcí.

![Diagram znázorňující scénář 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Tento přístup přidává vylepšení předchozího scénáře:

- Pokud aplikace funkce selže, Traffic Manager se postará o převzetí služeb při selhání do sekundární oblasti. Protože však aplikace funkce spoléhá na svůj vlastní účet úložiště, trvalé funkce nadále fungují.
- Během převzetí služeb při selhání neexistuje žádná další latence v oblasti převzetí služeb při selhání, protože aplikace funkce a účet úložiště jsou umístěny společně.
- Selhání vrstvy úložiště způsobí selhání v trvanlivé funkce, které zase spustí přesměrování na oblast převzetí služeb při selhání. Opět platí, že vzhledem k tomu, že aplikace funkce a úložiště jsou izolované pro oblast, trvalé funkce budou i nadále fungovat.

Důležité důležité informace pro tento scénář:

- Pokud se aplikace funkce nasadí pomocí vyhrazeného plánu AppService, replikace výpočetní infrastruktury v datovém centru převzetí služeb při selhání zvyšuje náklady.
- Aktuální stav není převzetí služeb při selhání, což znamená, že provádění a kontrolní ch odsávaných funkcí se nezdaří. Je na klientské aplikaci, aby zopakovat nebo restartovat práci.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scénář 3 – výpočetní prostředky s vyrovnáváním zatížení se sdíleným úložištěm GRS

Tento scénář je změna v prvním scénáři, implementace účtu sdíleného úložiště. Hlavní rozdíl, že účet úložiště je vytvořen s povolenou geografickou replikací.
Funkčně tento scénář poskytuje stejné výhody jako scénář 1, ale umožňuje další výhody obnovení dat:

- Geograficky redundantní úložiště (GRS) a GRS pro čtení (RA-GRS) maximalizují dostupnost pro váš účet úložiště.
- Pokud je výpadek oblasti služby úložiště, jednou z možností je, že operace datového centra určují, že úložiště musí být převzetí služeb při selhání do sekundární oblasti. V takovém případě bude přístup k účtu úložiště přesměrován transparentně na geograficky replikovanou kopii účtu úložiště bez zásahu uživatele.
- V takovém případě bude zachován stav trvalých funkcí až do poslední replikace účtu úložiště, ke kterému dochází každých několik minut.

Stejně jako u jiných scénářů, existují důležité aspekty:

- Převzetí služeb při selhání repliky provádí operátory datového centra a může to nějakou dobu trvat. Do té doby bude aplikace funkce trpět výpadkem.
- Za použití geograficky replikovaných účtů úložiště se zvyšují náklady.
- GRS dochází asynchronně. Některé z nejnovějších transakcí může dojít ke ztrátě z důvodu latence procesu replikace.

![Diagram znázorňující scénář 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Další kroky

Můžete si přečíst více o [navrhování vysoce dostupných aplikací pomocí RA-GRS](../../storage/common/storage-designing-ha-apps-with-ragrs.md)
