---
title: Repliky a instance v Azure Service Fabric | Dokumentace Microsoftu
description: Vysvětlení replik a instancí – jejich funkce a životního cyklu
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: ''
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: 7f8638365b40395a5dd82457c40e5c15209ba1a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60882386"
---
# <a name="replicas-and-instances"></a>Replik a instancí 
Tento článek poskytuje přehled o životním cyklu repliky instancí bezstavové služby a stavové služby.

## <a name="instances-of-stateless-services"></a>Instance bezstavové služby
Instance bezstavové služby je kopie služby logiku, která běží na jednom z uzlů clusteru. Instance v rámci oddílu je jedinečně identifikovaný jeho **InstanceId**. V následujícím diagramu je modelovaná životního cyklu instance:

![Životní cyklus instance](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Jakmile je Cluster Resource Manager určuje umístění pro instanci, vstoupí do tohoto stavu životního cyklu. Instance je spuštěn na uzlu. Je spuštění aplikace hostitele, je vytvořen a pak otevřít instanci. Po dokončení spuštění instance přechází do stavu Připraveno. 

Pokud aplikace hostitele nebo uzel pro tuto instanci dojde k chybě, přejde do stavu zahozených.

### <a name="ready-rd"></a>Připraveno (RD)
Ve stavu Připraveno instance je spuštěná na uzlu. Pokud je tato instance spolehlivé služby **RunAsync** zavolání. 

Pokud aplikace hostitele nebo uzel pro tuto instanci dojde k chybě, přejde do stavu zahozených.

### <a name="closing-cl"></a>Pravá (+/ CL)
Ve stavu uzavírací Azure Service Fabric je probíhá proces jeho vypínání instance na tomto uzlu. Toto vypnutí může mít různé důvody – například upgrade aplikace, Vyrovnávání zatížení nebo služby se odstraní. Po dokončení vypnutí přechází do stavu vyřazené.

### <a name="dropped-dd"></a>Vynechané (DD)
Vynechané stavu instance již neběží na uzlu. Service Fabric v tomto okamžiku uchovává metadata o této instanci, která je nakonec rovněž odstraněna.

> [!NOTE]
> Je možné přejít z některému ze stavů do vynechané stavu pomocí **ForceRemove** možnost `Remove-ServiceFabricReplica`.
>

## <a name="replicas-of-stateful-services"></a>Repliky stavové služby
Replika stavové služby je kopie služby logiku běžící na jednom z uzlů clusteru. Kromě toho repliky neuchovává kopii stav této služby. Dvě koncepce související popisují životní cyklus a chování stavové repliky:
- Životní cyklus replik
- Role repliky

Následující diskuse popisuje stavové trvalé služby. Pro stavové služby volatile (nebo v paměti) jsou stavy dolů a vyřazené ekvivalentní.

![Životní cyklus replik](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Repliku InBuild je repliky, který vytvořil nebo připraven pro připojení k replice. V závislosti na roli repliky IB má odlišnou sémantiku. 

Pokud aplikace hostitele nebo uzel pro repliku InBuild dojde k chybě, přejde do stavu Dole.

   - **Primární replik InBuild**: Primární InBuild jsou první replik pro oddíl. Tato replika obvykle dojde, když se vytváří oddílu. Primární repliky vzniknout také při restartování všech replik oddílu InBuild nebo jsou vynechány.

   - **Replik IdleSecondary InBuild**: Tyto nové repliky, které jsou vytvořeny pomocí Cluster Resource Manager nebo existující repliky, které byly přidány dolů a je potřeba přidat zpět do sady. Tyto repliky jsou nasazený nebo vytvořil primární předtím, než může připojit k sadě replik jako ActiveSecondary a účastnit kvora potvrzení operace.

   - **Replik ActiveSecondary InBuild**: Tento stav se vyskytuje v některé dotazy. Je optimalizace kde sady replik není změněn, ale replika musí být sestaveny. Repliky, samotné se řídí normální přechodů počítače (jak je popsáno v části role repliky).

### <a name="ready-rd"></a>Připraveno (RD)
Připraveno replika je repliku, která je součástí replikace a kvora potvrzení operace. Připraven stát se vztahuje na primární a aktivní sekundární repliky.

Pokud aplikace hostitele nebo uzel pro repliku připravené dojde k chybě, přejde do dolní stav.

### <a name="closing-cl"></a>Pravá (+/ CL)
Replika přejde do stavu uzavírací v následujících scénářích:

- **Vypínání kód pro repliku**: Service Fabric může být nutné vypnout spuštěním kódu v případě repliky. Pro mnoho důvodů, proč může být toto vypnutí. Například může dojít z důvodu aplikace, prostředků infrastruktury nebo upgrade infrastruktury nebo z důvodu chyb hlášených repliky. Při zavření repliky dokončí, replika přechází do stavu Dole. Setrvalý stav přidružený k této replice, která je uložená na disku není vyčistit.

- **Odstranění repliky z clusteru**: Service Fabric může být nutné k odebrání trvalého stavu a vypnout spuštěním kódu v případě repliky. Toto vypnutí může být z mnoha důvodů, například Vyrovnávání zatížení.

### <a name="dropped-dd"></a>Vynechané (DD)
Vynechané stavu instance již neběží na uzlu. Neexistuje žádný stav na uzlu. Service Fabric v tomto okamžiku uchovává metadata o této instanci, která je nakonec rovněž odstraněna.

### <a name="down-d"></a>Dolů (D)
V dolů stavu repliky kód není spuštěný, ale existuje trvalého stavu pro tuto repliku v tomto uzlu. Replika můžou být mimo provoz pro mnoho důvodů, proč – například uzel se dolů, chyby v kódu repliky, upgrade aplikace nebo chyby repliky.

Repliku dolů je otevřít Service Fabric podle potřeby, například po dokončení upgradu na uzlu.

Role repliky není v dolní stav relevantní.

### <a name="opening-op"></a>Otevírání (OP)
Repliku dolů přejde do stavu otevírání, když Service Fabric potřebuje, abyste vyvolali repliky zpět znovu. Tento stav může být třeba po dokončení upgradu kódu pro aplikace na uzlu. 

Pokud aplikace hostitele nebo uzel pro repliku otevírání dojde k chybě, přejde do stavu Dole.

Role repliky není ve stavu otevírání relevantní.

### <a name="standby-sb"></a>Pohotovostní režim (SB)
Replik StandBy je replika trvalá služba, která klesl a pak se otevřel. Tato replika by mohly používat Service Fabric, pokud je potřeba přidat jiné repliky replik, (protože replika již obsahuje část stavu a procesu sestavení je rychlejší). Po vypršení platnosti StandByReplicaKeepDuration pohotovostní repliky se zahodí.

Pokud aplikace hostitele nebo uzel pro pohotovostní repliky dojde k chybě, přechází do stavu Dole.

Role repliky není v pohotovostním stavu relevantní.

> [!NOTE]
> Všechny repliky, který je mimo provoz nebo vyřadit se považuje za *nahoru*.
>

> [!NOTE]
> Je možné přejít z některému ze stavů do vynechané stavu pomocí **ForceRemove** možnost `Remove-ServiceFabricReplica`.
>

## <a name="replica-role"></a>Role repliky 
Role repliky určuje jeho funkci sady replik:

- **Primární (P)** : Existuje jedna primární sady replik, který zodpovídá za operace čtení a zápisu. 
- **ActiveSecondary (S)** : Jedná se o repliky, které přijímat aktualizace stavu z primárního serveru, aplikovat a pak odešle zpět potvrzení. Existují více aktivní sekundární databáze v replikách. Počet těchto aktivní sekundární databáze určuje počet chyb, které může služba zpracovat.
- **IdleSecondary (I)** : Tyto repliky byly sestaveny pomocí primární. Obdrželi stavu z primárního serveru, než mohou být přesunuty do aktivní sekundární. 
- **Žádný (N)** : Tyto repliky nemají odpovědnost sady replik.
- **Neznámý (U)** : Toto je počáteční role repliky předtím, než začne přijímat veškeré **ChangeRole** volání rozhraní API ze Service Fabric.

Následující diagram znázorňuje přechody role repliky a nějaké ukázkové scénáře, ve kterých může dojít:

![Role repliky](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> P: Vytvoření nové primární replice.
- U -> I: Vytvoření nové repliky nečinnosti.
- U -> N: Odstranění repliky pohotovostním režimu.
- I -> S: Podpora dobu nečinnosti sekundární do aktivní sekundární lokality tak, aby jeho potvrzení se využívají k dosažení kvora.
- I -> P: Podpora nečinná sekundární databáze na primární. V části speciální rekonfigurací tomu může dojít, když je nečinná sekundární správné kandidát na primární.
- I -> N: Odstranění nečinná sekundární repliky.
- S -> P: Podpora aktivní sekundární databáze na primární. Může to být z důvodu převzetí služeb při selhání z primární nebo primární přesun zahájené pomocí Cluster Resource Manager. Například může být v reakci na upgrade aplikace nebo služby Vyrovnávání zatížení.
- S -> N: Odstranění aktivní sekundární repliku.
- P -> S: Degradace primární repliky. To může být způsobeno primární přesun zahájené pomocí Cluster Resource Manager. Například může být v reakci na upgrade aplikace nebo služby Vyrovnávání zatížení.
- P -> N: Odstranění primární repliky.

> [!NOTE]
> Modely programování vyšší úrovně, jako například [Reliable Actors](service-fabric-reliable-actors-introduction.md) a [Reliable Services](service-fabric-reliable-services-introduction.md), skrýt koncept role repliky od vývojáře. V objektů actor pojem role je zbytečné. Služby je do značné míry zjednodušená pro většinu scénářů.
>

## <a name="next-steps"></a>Další postup
Další informace o konceptech Service Fabric najdete v následujícím článku:

[Životní cyklus Reliable Services – C#](service-fabric-reliable-services-lifecycle.md)

