---
title: Instance v Azure Service Fabric a repliky | Microsoft Docs
description: Pochopení repliky a instancí – jejich funkce a životní cykly
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
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211384"
---
# <a name="replicas-and-instances"></a>Repliky a instancí 
Tento článek nabízí přehled životního cyklu replik instancí služby bezstavové a stavové služby.

## <a name="instances-of-stateless-services"></a>Instance bezstavové služby
Instance bezstavové služby je kopii služby logiky, která běží na jednom z uzlů clusteru. Instance v rámci oddílu je jedinečně identifikovaný jeho **identifikátor InstanceId**. Životní cyklus instance je modelován v následujícím diagramu:

![Životní cyklus instance](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Po clusteru Resource Manager určuje umístění pro instanci, vstoupí do tohoto životního cyklu stavu. Instance je spuštěn na uzlu. Hostitele aplikací je spuštěná, je vytvořen a pak otevřít instanci. Po dokončení spuštění instance přechází do stavu Připraveno. 

Pokud hostitele aplikací nebo uzel pro tuto instanci dojde k chybě, přechází do stavu vynechaných.

### <a name="ready-rd"></a>Připraveno (RD)
Ve stavu Připraveno instance je spuštěná na uzlu. Pokud je tato instance spolehlivě, **RunAsync** metoda byla volána. 

Pokud hostitele aplikací nebo uzel pro tuto instanci dojde k chybě, přechází do stavu vynechaných.

### <a name="closing-cl"></a>Zavření (CL)
Ve stavu ukončovací Azure Service Fabric právě vypíná instance na tomto uzlu. Tento vypnout může být z mnoha důvodů – například upgradu aplikace, Vyrovnávání zatížení nebo služby právě odstraňuje. Po dokončení vypnutí přechází do stavu vynechaných.

### <a name="dropped-dd"></a>Vyřazené (DD)
V vynechaných stavu již není spuštěn instance na uzlu. V tomto okamžiku Service Fabric udržuje metadata o této instance, kterou nakonec je rovněž odstraněny.

> [!NOTE]
> Je možné na přechod z jakékoli stav vyřazené stavu pomocí **ForceRemove** možnost `Remove-ServiceFabricReplica`.
>

## <a name="replicas-of-stateful-services"></a>Repliky stavové služby
Replika stavové služby je kopii logice služby spuštěné v jednom z uzlů clusteru. Kromě toho repliky udržuje kopii stav této služby. Dva související koncepty popisují životní cyklus a chování stavová replik:
- Životní cyklus repliky
- Role repliky

Následující diskusi popisuje trvalou stavové služby. Pro stavové služby volatile (nebo v paměti) jsou stavy dolů a vynechaných ekvivalentní.

![Životní cyklus repliky](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Repliku InBuild je repliky, která se má vytvořit nebo připraven pro připojení k replice. V závislosti na roli repliky IB má jinou sémantiku. 

Pokud uzel pro repliku InBuild nebo hostitele aplikace spadne, přechází do stavu Dole.

   - **Primární replik InBuild**: primární InBuild jsou první replik pro jeden oddíl. Tato replika obvykle dojde, když se vytváří oddílu. Primární repliky také nastat, když restartujte všechny repliky oddílu InBuild nebo jsou vyřadit.

   - **Replik IdleSecondary InBuild**: Jedná se buď nové repliky, které jsou vytvořené pomocí Správce prostředků clusteru, nebo existující repliky, které se dolů a je třeba přidat zpět do sady. Tyto repliky jsou předvyplněných nebo vytvořené primární předtím, než můžete připojit k sadě replik jako ActiveSecondary a účastnit kvora potvrzení operace.

   - **Replik ActiveSecondary InBuild**: Tento stav se zjištěnými v některé dotazy. Je není změna optimalizace kde sady replik, ale je potřeba repliku má být sestaven. Replika samotné následuje přechody počítač normálním stavu (jak je popsáno v části na role repliky).

### <a name="ready-rd"></a>Připraveno (RD)
Připraveno replika je repliky, která se účastní replikace a kvora potvrzení operací. Stavu Připraveno se vztahuje na primární a aktivní sekundární repliky.

Pokud uzel pro repliku připraveno nebo hostitele aplikace spadne, přechází do stavu Dole.

### <a name="closing-cl"></a>Zavření (CL)
Repliku vstupuje do stavu zavření v následujících scénářích:

- **Vypínání kód pro repliku**: Service Fabric může být nutné vypnout spuštěním kódu pro repliku. Z mnoha důvodů může být tento vypnout. Například může dojít z důvodu aplikace, prostředků infrastruktury nebo upgrade infrastruktury nebo z důvodu chybu hlášené repliky. Pokud replika zavřít dokončení, replika přechází do dolů stavu. Setrvalý stav přidružené k této repliky, která je uložená na disku není vyčistit.

- **Odstranění repliky z clusteru**: Service Fabric může být nutné odebrat trvalého stavu a vypnout spuštěním kódu pro repliku. Tento vypnout může být z mnoha důvodů, například Vyrovnávání zatížení.

### <a name="dropped-dd"></a>Vyřazené (DD)
V vynechaných stavu již není spuštěn instance na uzlu. Je také bez stavu na uzlu vlevo. V tomto okamžiku Service Fabric udržuje metadata o této instance, kterou nakonec je rovněž odstraněny.

### <a name="down-d"></a>Dolů (D)
V dolní stav kód repliky není spuštěná, ale trvalého stavu pro tuto repliku existuje v tomto uzlu. Repliku můžete být mimo provoz z mnoha důvodů – například uzlu se dolů, havárie v kód repliky, upgradu aplikace nebo repliky chyb.

Repliku dolů otevře Service Fabric podle potřeby, třeba po dokončení upgradu na uzlu.

Role repliky není ve stavu dolů relevantní.

### <a name="opening-op"></a>Otevírání (OP)
Repliku dolů vstupuje do stavu otevírání, pokud Service Fabric je potřeba uvést repliky zálohovat znovu. Tento stav může být například po dokončení upgradu kódu pro aplikaci na uzlu. 

Pokud uzel pro repliku otevírání nebo hostitele aplikace spadne, přechází do stavu Dole.

Role repliky není ve stavu otevírání relevantní.

### <a name="standby-sb"></a>Pohotovostní režim (SB)
Pohotovostní replika je replika trvalou služba, která se a poté otevřené. Tato replika by mohly používat Service Fabric, pokud je potřeba přidat další repliky na repliku, (protože repliky už je některá část stavu a proces je rychlejší). Po vypršení platnosti StandByReplicaKeepDuration pohotovostní repliky se zahodí.

Pokud uzel pro pohotovostní repliku nebo hostitele aplikace spadne, přechází do stavu Dole.

Role repliky není relevantní v pohotovostním stavu.

> [!NOTE]
> Všechny repliky, která je mimo provoz nebo vyřadit se považuje za *až*.
>

> [!NOTE]
> Je možné na přechod z jakékoli stav vyřazené stavu pomocí **ForceRemove** možnost `Remove-ServiceFabricReplica`.
>

## <a name="replica-role"></a>Role repliky 
Role repliky určuje jeho funkce sady replik:

- **Primární (P)**: je jedna primární sady replik, která je odpovědná za provádění operací čtení a zápisu. 
- **ActiveSecondary (S)**: Toto jsou repliky, které přijímat aktualizace stavu z primární, je použít a potom odešlete back potvrzení. V sadě replik nejsou více aktivní sekundární databáze. Počet tyto aktivní sekundární databáze určuje počet závad, které služba dokáže zpracovat.
- **IdleSecondary (I)**: tyto repliky se právě vytvořené primární. Předtím, než může být povýšen na sekundární aktivní, že je mu stavu z primární. 
- **Žádná (N)**: tyto repliky nemají odpovědnost sady replik.
- **Neznámý (U)**: Toto je počáteční role repliky před obdrží žádné **ChangeRole** volání rozhraní API z Service Fabric.

Následující diagram znázorňuje přechody role repliky a několik ukázkových scénářů, ve kterých může dojít:

![Role repliky](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> P: vytvoření nové primární replice.
- U -> I: vytvoření nové repliky nečinnosti.
- U -> N: odstranění pohotovostní repliky.
- I -> S: povýšení nečinnosti sekundární aktivní sekundární tak, aby jeho potvrzení přispívat k kvora.
- I -> P: povýšení nečinnosti sekundární pro primární. To může nastat v části speciální že změny konfigurace, při nečinnosti sekundární je na správné candidate jako primární.
- I -> N: odstranění nečinnosti sekundární repliky.
- S -> P: povýšení active sekundární pro primární. Může to být kvůli převzetí služeb při selhání primární nebo primární přesun iniciované správcem prostředků clusteru. Například může být v reakci na upgradu aplikace nebo služby Vyrovnávání zatížení.
- S -> N: odstranění aktivní sekundární repliky.
- P -> S: degradace primární repliky. Může to být kvůli primární přesun iniciované správcem prostředků clusteru. Například může být v reakci na upgradu aplikace nebo služby Vyrovnávání zatížení.
- P -> Odstranění N: primární repliky.

> [!NOTE]
> Modely programování vyšší úrovně, jako například [Reliable Actors](service-fabric-reliable-actors-introduction.md) a [spolehlivé služby](service-fabric-reliable-services-introduction.md), skrýt konceptu repliky rolí od vývojáře. V aktéři není nutné představu o roli. V dialogovém okně služby je z velké části zjednodušený pro většinu scénářů.
>

## <a name="next-steps"></a>Další postup
Další informace o konceptech Service Fabric najdete v následujícím článku:

[Životní cyklus Reliable Services – C#](service-fabric-reliable-services-lifecycle.md)

