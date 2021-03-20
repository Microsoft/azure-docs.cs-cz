---
title: Zotavení po havárii a geografická distribuce Azure Durable Functions
description: Přečtěte si o zotavení po havárii a geografické distribuci v Durable Functions.
author: MS-Santi
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: azfuncdf
ms.openlocfilehash: 01c400f51cce85ef39e9d39bcad1221253c6942d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89071206"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Zotavení po havárii a geografická distribuce v Azure Durable Functions

Microsoft usiluje o to, aby byly služby Azure vždycky dostupné. Může ale dojít k neplánovaným výpadkům služby. Pokud vaše aplikace vyžaduje odolnost, společnost Microsoft doporučuje nakonfigurovat aplikaci pro geografickou redundanci. Kromě toho by zákazníci měli mít k dispozici plán zotavení po havárii pro zpracování oblasti výpadku regionální služby. Důležitou součástí plánu zotavení po havárii je příprava na převzetí služeb při selhání sekundární replikou vaší aplikace a úložiště v případě, že primární replika nebude k dispozici.

V Durable Functions je veškerý stav trvale v Azure Storage ve výchozím nastavení. [Centrum úloh](durable-functions-task-hubs.md) je logický kontejner pro Azure Storage prostředky, které se používají pro [orchestraci](durable-functions-types-features-overview.md#orchestrator-functions) a [entity](durable-functions-types-features-overview.md#entity-functions). Funkce Orchestrator, Activity a entity můžou vzájemně spolupracovat, když patří do stejného centra úloh. Tento dokument bude odkazovat na centra úloh při popisu scénářů pro zajištění vysoké dostupnosti těchto Azure Storagech prostředků.

Orchestrace a entity se můžou aktivovat pomocí [klientských funkcí](durable-functions-types-features-overview.md#client-functions) , které se SPOUŠTĚJÍ přes HTTP, nebo jedním z dalších podporovaných Azure Functions typů triggerů. Můžou se taky aktivovat pomocí [integrovaných rozhraní API http](durable-functions-http-features.md#built-in-http-apis). V zájmu zjednodušení se tento článek zaměřuje na scénáře zahrnující Azure Storage a triggery funkcí založené na protokolu HTTP a možnosti zvýšení dostupnosti a minimalizace výpadků během aktivit zotavení po havárii. Další typy triggerů, například triggery Service Bus nebo Cosmos DB, nebudou výslovně pokryty.

Následující scénáře jsou založené na konfiguracích Active-Passive, protože se řídí využitím Azure Storage. Tento model se skládá z nasazení zálohovací (pasivní) aplikace Function App do jiné oblasti. Traffic Manager bude monitorovat primární (aktivní) aplikaci funkcí pro dostupnost protokolu HTTP. Dojde k převzetí služeb při selhání v aplikaci Function App, pokud primární selže. Další informace najdete v tématu věnovaném [metodě Traffic-Routing priority](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method) v [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).

> [!NOTE]
> - Navrhovaná konfigurace Active-Passive zajišťuje, že klient bude vždycky schopný aktivovat nové orchestrace přes HTTP. V důsledku toho, že mají dvě aplikace Function App sdílí stejné centrum úloh v úložišti, budou některé transakce úložiště na pozadí distribuovány mezi oběma. Tato konfigurace proto u sekundární aplikace Function App naplní nějaké náklady na výstup.
> - Základní účet úložiště a centrum úloh se vytvoří v primární oblasti a sdílí se s oběma aplikacemi funkcí.
> - Všechny aplikace Function App, které jsou redundantním nasazením, musí v případě aktivace pomocí protokolu HTTP sdílet stejné přístupové klíče funkce. Modul runtime Functions zveřejňuje [rozhraní API pro správu](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) , které umožňuje uživatelům programově přidávat, odstraňovat a aktualizovat klíče funkcí. Správa klíčů je také možné použít [Azure Resource Manager rozhraní API](https://www.markheath.net/post/managing-azure-functions-keys-2).

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>Scénář 1 – COMPUTE s vyrovnáváním zatížení se sdíleným úložištěm

Pokud se výpočetní infrastruktura v Azure nezdařila, může dojít k nedostupnosti aplikace Function App. K minimalizaci možnosti takového výpadku používá tento scénář dvě aplikace Function App nasazené do různých oblastí.
Traffic Manager je nakonfigurovaná tak, aby zjistila problémy v aplikaci s primární funkcí a automaticky přesměruje provoz do aplikace Function App v sekundární oblasti. Tato aplikace Function App sdílí stejný účet Azure Storage a centrum úloh. Proto se stav aplikací Function App neztratí a práce může normálně pokračovat. Jakmile se stav obnoví do primární oblasti, Azure Traffic Manager zahájí žádosti o směrování do této aplikace Function App automaticky.

![Diagram znázorňující scénář 1](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Při použití tohoto scénáře nasazení je k dispozici několik výhod:

- Pokud dojde k selhání výpočetní infrastruktury, můžete pokračovat v práci v oblasti převzetí služeb při selhání bez ztráty dat.
- Traffic Manager se automatické převzetí služeb při selhání automaticky převezme do funkční aplikace.
- Po opravě výpadku Traffic Manager automaticky znovu navázat provoz do primární aplikace Function App.

V tomto scénáři ale zvažte následující:

- Pokud je aplikace Function App nasazená pomocí vyhrazeného App Service plánu, bude se při replikaci výpočetní infrastruktury v datacentru pro převzetí služeb při selhání zvyšovat náklady.
- Tento scénář pokrývá výpadky výpočetní infrastruktury, ale účet úložiště i nadále je jediným bodem selhání aplikace Function App. Pokud dojde k výpadku úložiště, aplikace utrpí výpadky.
- Pokud dojde k převzetí služeb při selhání aplikace Function App, dojde ke zvýšení latence, protože budou mít přístup k účtu úložiště napříč různými oblastmi.
- Přístup ke službě úložiště z jiné oblasti, ve které se nachází, se s vyššími náklady vyplatí kvůli přenosům na výstup sítě.
- Tento scénář závisí na Traffic Manager. S ohledem na to, [jak Traffic Manager funguje](../../traffic-manager/traffic-manager-how-it-works.md), může trvat nějakou dobu, než klientská aplikace, která spotřebovává trvalou funkci, musí znovu zadat dotaz na adresu aplikace Function app z Traffic Manager.

> [!NOTE]
> Počínaje v v **2.3.0** rozšíření Durable Functions můžou dvě aplikace Function App běžet bezpečně současně se stejným účtem úložiště a konfigurací centra úloh. První aplikace ke spuštění získá zapůjčení objektu BLOB na úrovni aplikace, které znemožní jiným aplikacím ukrást zprávy z front centra úloh. Pokud tato první aplikace přestane běžet, její zapůjčení vyprší a bude možné ji získat v druhé aplikaci, která pak bude pokračovat ve zpracování zpráv centra úloh.
> 
> Aplikace Function App, které jsou nakonfigurované tak, aby používaly stejný účet úložiště, budou před verzí v 2.3.0 zpracovávat zprávy a aktualizovat je souběžně, což vede k mnohem vyšším celkovým latencím a nákladům na výstup. V případě, že primární a replikované aplikace mají někdy jiný kód, který je pro ně nasazený, může být orchestrace také neúspěšná, protože funkce Orchestrator nekonzistence vykonává všechny tyto dvě aplikace. Proto se doporučuje, aby všechny aplikace, které vyžadují geografickou distribuci pro účely zotavení po havárii, používaly v 2.3.0 nebo vyšší z trvalého rozšíření.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>Scénář 2 – výpočetní výkon s vyrovnáváním zatížení s místním úložištěm

Předchozí scénář pokrývá jenom případ selhání výpočetní infrastruktury. Pokud služba úložiště selže, způsobí to výpadek aplikace Function App.
Pro zajištění nepřetržitého provozu trvalých funkcí používá tento scénář místní účet úložiště v každé oblasti, do které jsou nasazené aplikace Function App.

![Diagram znázorňující scénář 2](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Tento přístup přináší vylepšení v předchozím scénáři:

- Pokud aplikace Function App selže, Traffic Manager se postará o selhání do sekundární oblasti. Vzhledem k tomu, že aplikace Function App spoléhá na vlastní účet úložiště, jsou trvalé funkce nadále funkční.
- Během převzetí služeb při selhání nedochází k žádné další latenci v oblasti převzetí služeb při selhání, protože aplikace Function App a účet úložiště se společně nacházejí.
- Selhání vrstvy úložiště způsobí selhání trvalých funkcí, které zase spustí přesměrování do oblasti převzetí služeb při selhání. Vzhledem k tomu, že aplikace Function App a úložiště jsou izolované podle oblasti, budou trvalé funkce i nadále fungovat.

Důležité informace pro tento scénář:

- Pokud je aplikace Function App nasazená pomocí vyhrazeného App Service plánu, bude se při replikaci výpočetní infrastruktury v datacentru pro převzetí služeb při selhání zvyšovat náklady.
- Aktuální stav není při selhání, což znamená, že stávající Orchestrace a entity budou efektivně pozastaveny a nebudou dostupné, dokud se primární oblast neobnoví.

Pro shrnutí je kompromis mezi prvním a druhým scénářem tato latence zachována a náklady na výstup budou minimalizovány, ale existující Orchestrace a entity nebudou během výpadku k dispozici. Zda jsou tyto kompromisy přijatelné, závisí na požadavcích aplikace.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>Scénář 3 – výpočty vyrovnaných k vyrovnávání zatížení pomocí sdíleného úložiště GRS

Tento scénář je úpravou v prvním scénáři, implementace sdíleného účtu úložiště. Hlavní rozdíl spočívá v tom, že je účet úložiště vytvořený s povolenou geografickou replikací.
V takovém případě přináší tento scénář stejné výhody jako scénář 1, ale umožňuje další výhody obnovení dat:

- Geograficky redundantní úložiště (GRS) a GRS pro čtení (RA-GRS) maximalizuje dostupnost vašeho účtu úložiště.
- Pokud dojde k oblastnímu výpadku služby úložiště, můžete [ručně iniciovat převzetí služeb při selhání sekundární replikou](../../storage/common/storage-initiate-account-failover.md). V extrémních situacích, kdy dojde ke ztrátě oblasti z důvodu významné havárie, může společnost Microsoft zahájit místní převzetí služeb při selhání. V takovém případě není nutná žádná akce s vaší částí.
- Když dojde k převzetí služeb při selhání, stav trvalých funkcí se zachová až na poslední replikaci účtu úložiště, ke kterému obvykle dochází každých několik minut.

Stejně jako u ostatních scénářů jsou důležité důležité informace:

- Převzetí služeb při selhání repliky může nějakou dobu trvat. Dokud se převzetí služeb při selhání nedokončí a Azure Storage aktualizace záznamů DNS, dojde k výpadku aplikace Function App.
- Existují zvýšené náklady na použití geograficky replikovaných účtů úložiště.
- Replikace GRS kopíruje data asynchronně. Z důvodu latence procesu replikace může dojít ke ztrátě některých z posledních transakcí.

![Diagram znázorňující scénář 3](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

> [!NOTE]
> Jak je popsáno ve scénáři 1, důrazně doporučujeme, aby aplikace Function App nasazené pomocí této strategie používaly **v 2.3.0** nebo vyšších rozšíření Durable Functions.

Další informace najdete v dokumentaci [Azure Storage zotavení po havárii a v účtu úložiště pro převzetí služeb při selhání](../../storage/common/storage-disaster-recovery-guidance.md) .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o návrhu vysoce dostupných aplikací v Azure Storage](../../storage/common/geo-redundant-design.md)
