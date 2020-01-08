---
title: Zotavení po havárii a geografická distribuce Azure Durable Functions
description: Přečtěte si o zotavení po havárii a geografické distribuci v Durable Functions.
author: MS-Santi
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 7951f216143bef0d48a6b751beff3f8f4316b9bd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433336"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Zotavení po havárii a geografická distribuce v Azure Durable Functions

V Durable Functions je veškerý stav trvale v Azure Storage. [Centrum úloh](durable-functions-task-hubs.md) je logický kontejner pro Azure Storage prostředky, které se používají pro orchestrace. Funkce Orchestrator a Activity můžou vzájemně fungovat, když patří do stejného centra úloh.
Popsané scénáře navrhují možnosti nasazení a zvyšují dostupnost a minimalizují prostoje během aktivit zotavení po havárii.

Je důležité si všimnout, že tyto scénáře jsou založené na konfiguracích aktivní – pasivní, protože se řídí využitím Azure Storage. Tento model se skládá z nasazení zálohovací (pasivní) aplikace Function App do jiné oblasti. Traffic Manager bude sledovat dostupnost primární (aktivní) funkce aplikace. Dojde k převzetí služeb při selhání v aplikaci Function App, pokud primární selže. Další informace najdete v tématu věnovaném [metodě směrování s prioritou](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method) [Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).

>[!NOTE]
>
> - Navrhovaná konfigurace aktivní – pasivní zajišťuje, že klient bude vždycky schopný aktivovat nové orchestrace přes HTTP. V důsledku toho, že mají dvě aplikace Function App sdílející stejné úložiště, bude zpracování na pozadí distribuováno mezi obě z nich a bude se přijímají pro zprávy ve stejných frontách. Tato konfigurace se vyskytne v přidaných nákladech na výstup pro sekundární aplikaci Function App.
> - Základní účet úložiště a centrum úloh se vytvoří v primární oblasti a sdílí se s oběma aplikacemi funkcí.
> - Všechny aplikace Function App, které jsou redundantním nasazením, musí v případě aktivace pomocí protokolu HTTP sdílet stejné přístupové klíče funkce. Modul runtime Functions zveřejňuje [rozhraní API pro správu](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) , které umožňuje uživatelům programově přidávat, odstraňovat a aktualizovat klíče funkcí.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scénář 1 – COMPUTE s vyrovnáváním zatížení se sdíleným úložištěm

Pokud se výpočetní infrastruktura v Azure nezdařila, může dojít k nedostupnosti aplikace Function App. K minimalizaci možnosti takového výpadku používá tento scénář dvě aplikace Function App nasazené do různých oblastí.
Traffic Manager je nakonfigurovaná tak, aby zjistila problémy v aplikaci s primární funkcí a automaticky přesměruje provoz do aplikace Function App v sekundární oblasti. Tato aplikace Function App sdílí stejný účet Azure Storage a centrum úloh. Proto se stav aplikací Function App neztratí a práce může normálně pokračovat. Jakmile se stav obnoví do primární oblasti, Azure Traffic Manager zahájí žádosti o směrování do této aplikace Function App automaticky.

![Diagram znázorňující scénář 1](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Při použití tohoto scénáře nasazení je k dispozici několik výhod:

- Pokud dojde k selhání výpočetní infrastruktury, můžete pokračovat v práci v oblasti převzetí služeb při selhání bez ztráty stavu.
- Traffic Manager se postará o automatické převzetí služeb při selhání do funkční aplikace v pořádku.
- Po opravě výpadku Traffic Manager automaticky znovu navázat provoz do primární aplikace Function App.

V tomto scénáři ale zvažte následující:

- Pokud je aplikace Function App nasazená pomocí vyhrazeného App Service plánu, bude se při replikaci výpočetní infrastruktury v datovém centru převzetí služeb při selhání zvyšovat náklady.
- Tento scénář pokrývá výpadky výpočetní infrastruktury, ale účet úložiště i nadále je jediným bodem selhání aplikace Function App. Pokud dojde k výpadku úložiště, aplikace utrpí výpadek.
- Pokud dojde k převzetí služeb při selhání aplikace Function App, dojde ke zvýšení latence, protože budou mít přístup k účtu úložiště napříč různými oblastmi.
- Přístup ke službě úložiště z jiné oblasti, ve které se nachází, se s vyššími náklady vyplatí kvůli přenosům na výstup sítě.
- Tento scénář závisí na Traffic Manager. S ohledem na to, [jak Traffic Manager funguje](../../traffic-manager/traffic-manager-how-it-works.md), může trvat nějakou dobu, než klientská aplikace, která spotřebovává trvalou funkci, musí znovu zadat dotaz na adresu aplikace Function app z Traffic Manager.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scénář 2 – výpočetní výkon s vyrovnáváním zatížení s místním úložištěm

Předchozí scénář pokrývá jenom případ selhání výpočetní infrastruktury. Pokud služba úložiště selže, způsobí to výpadek aplikace Function App.
Pro zajištění nepřetržitého provozu trvalých funkcí používá tento scénář místní účet úložiště v každé oblasti, do které jsou nasazené aplikace Function App.

![Diagram znázorňující scénář 2](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Tento přístup přináší vylepšení v předchozím scénáři:

- Pokud aplikace Function App selže, Traffic Manager se postará o selhání do sekundární oblasti. Vzhledem k tomu, že aplikace Function App spoléhá na vlastní účet úložiště, jsou trvalé funkce nadále funkční.
- Během převzetí služeb při selhání nedochází k žádné další latenci v oblasti převzetí služeb při selhání, protože aplikace Function App a účet úložiště se nacházejí společně.
- Selhání vrstvy úložiště způsobí selhání u trvalých funkcí, které zase spustí přesměrování na oblast převzetí služeb při selhání. Vzhledem k tomu, že aplikace Function App a úložiště jsou izolované podle oblasti, budou trvalé funkce i nadále fungovat.

Důležité informace pro tento scénář:

- Pokud je aplikace Function App nasazená pomocí vyhrazeného plánu AppService, replikace výpočetní infrastruktury v datacentru při selhání zvyšuje náklady.
- Aktuální stav není při selhání, což znamená, že spuštění a kontrolní body se nezdaří. Chcete-li akci opakovat nebo restartovat, je to klientská aplikace.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scénář 3 – výpočty vyrovnaných k vyrovnávání zatížení pomocí sdíleného úložiště GRS

Tento scénář je úpravou v prvním scénáři, implementace sdíleného účtu úložiště. Hlavní rozdíl v tom, že je účet úložiště vytvořený s povolenou geografickou replikací.
V takovém případě přináší tento scénář stejné výhody jako scénář 1, ale umožňuje další výhody obnovení dat:

- Geograficky redundantní úložiště (GRS) a GRS pro čtení (RA-GRS) maximalizuje dostupnost vašeho účtu úložiště.
- Pokud dojde k výpadku služby úložiště v oblasti, jednou z možností je to, že operace Datacenter určují, že se úložiště musí převzít do sekundární oblasti. V tomto případě se přístup k účtu úložiště transparentně přesměruje na geograficky replikovanou kopii účtu úložiště bez zásahu uživatele.
- V takovém případě se stav trvalých funkcí zachová až na poslední replikaci účtu úložiště, ke kterému dochází každých pár minut.

Stejně jako u ostatních scénářů jsou důležité důležité informace:

- Převzetí služeb při selhání replikou provádí operátoři Datacenter a může to chvíli trvat. Do té doby dojde k výpadku aplikace Function App.
- Existují zvýšené náklady na použití geograficky replikovaných účtů úložiště.
- K GRS dochází asynchronně. Z důvodu latence procesu replikace může dojít ke ztrátě některých z posledních transakcí.

![Diagram znázorňující scénář 3](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>Další kroky

Další informace o [navrhování aplikací s vysokou dostupností najdete v článku RA-GRS](../../storage/common/storage-designing-ha-apps-with-ragrs.md) .
