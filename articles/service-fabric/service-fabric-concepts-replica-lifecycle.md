---
title: Repliky a instance v Azure Service Fabric
description: Seznamte se s replikami a instancemi v Service Fabric, včetně přehledu jejich životního cyklu a funkcí.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: cf21af43de553a2802289e44eaece12952d077d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84710741"
---
# <a name="replicas-and-instances"></a>Repliky a instance 
Tento článek obsahuje přehled životního cyklu replik stavových služeb a instancí bezstavových služeb.

## <a name="instances-of-stateless-services"></a>Instance bezstavových služeb
Instance bezstavové služby je kopií logiky služby, která běží na jednom z uzlů clusteru. Instance v rámci oddílu je jednoznačně identifikována jeho **InstanceId**. Životní cyklus instance je modelován v následujícím diagramu:

![Životní cyklus instance](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Po Správce prostředků clusteru určí umístění pro instanci, vstoupí v tomto stavu životního cyklu. Instance je spuštěna na uzlu. Hostitel aplikace je spuštěn, instance je vytvořena a poté otevřena. Po dokončení spuštění instance přejde do stavu připraveno. 

Pokud hostitel aplikace nebo uzel této instance selže, přejde do stavu zrušeno.

### <a name="ready-rd"></a>Připraveno (RD)
V připraveném stavu je instance v uzlu funkční a spuštěná. Pokud je tato instance spolehlivou službou, vyvolal se **RunAsync** . 

Pokud hostitel aplikace nebo uzel této instance selže, přejde do stavu zrušeno.

### <a name="closing-cl"></a>Zavírání (CL)
Ve stavu ukončení Azure Service Fabric v procesu vypínání instance v tomto uzlu. Toto vypnutí může být z mnoha důvodů – například upgrade aplikace, Vyrovnávání zatížení nebo odstranění služby. Po skončení vypnutí přejdete do stavu zrušeno.

### <a name="dropped-dd"></a>Vyřazeno (DD)
Ve stavu zrušeno, instance již na uzlu neběží. V tomto okamžiku Service Fabric udržuje metadata o této instanci, která je nakonec také odstraněna.

> [!NOTE]
> Můžete přejít ze všech stavů na vyřazený stav pomocí možnosti **ForceRemove** na `Remove-ServiceFabricReplica` .
>

## <a name="replicas-of-stateful-services"></a>Repliky stavových služeb
Replika stavové služby je kopie logiky služby spuštěné na jednom z uzlů clusteru. Kromě toho replika udržuje kopii stavu této služby. V souvislosti s životním cyklem a chováním stavových replik se popisují dvě související koncepce:
- Životní cyklus repliky
- Role repliky

Následující diskuze popisuje trvalé stavové služby. Pro nestálé stavové služby (nebo v paměti) jsou stavy dolů a odložené.

![Životní cyklus repliky](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Replika inbuildu je replika, která je vytvořená nebo připravená pro připojení k sadě replik. V závislosti na roli repliky má IB odlišnou sémantiku. 

Pokud hostitel aplikace nebo uzel pro repliku inbuildu selže, přejde do stavu mimo provoz.

   - **Primární repliky Inbuildu**: primární sestavení inbuildu jsou prvními replikami pro oddíl. K této replice obvykle dochází při vytváření oddílu. Primární repliky inbuildu se také vyskytnou, když se všechny repliky oddílu restartují nebo jsou vyřazeny.

   - **IdleSecondary repliky InBuild**: Jedná se o nové repliky, které jsou vytvořené správce prostředků clusteru, nebo existující repliky, které se vypnuly a které je potřeba do sady přidat zpátky. Tyto repliky jsou osazené nebo vytvořené primárním serverem dřív, než se můžou připojit k sadě replik jako ActiveSecondary a zúčastnit se potvrzení operací pomocí kvora.

   - **Repliky ActiveSecondary InBuild**: Tento stav je v některých dotazech pozorován. Je to optimalizace, kde se sada repliky nemění, ale je třeba vytvořit repliku. Replika sama sleduje přechod normálního stavového počítače (jak je popsáno v části v tématu role repliky).

### <a name="ready-rd"></a>Připraveno (RD)
Připravená replika je replika, která se účastní replikace a potvrzení operací kvora. Stav připraveno se vztahuje na primární a aktivní sekundární repliky.

Pokud hostitel aplikace nebo uzel pro připravenou repliku selže, přejde do stavu v pořádku.

### <a name="closing-cl"></a>Zavírání (CL)
Replika vstoupí do stavu ukončení v následujících scénářích:

- **Vypínání kódu pro repliku**: Service Fabric může vyžadovat vypnutí spuštěného kódu repliky. Toto vypnutí může být z mnoha důvodů. K tomu může dojít například kvůli selhání aplikace, infrastruktury nebo upgradu infrastruktury nebo z důvodu chyby hlášené replikou. Po dokončení zavření repliky přejde replika do stavu mimo provoz. Trvalý stav přidružený k této replice, který je uložený na disku, se nevyčistí.

- **Odebírá se replika z clusteru**: Service Fabric může vyžadovat odebrání trvalého stavu a vypnutí spuštěného kódu pro repliku. Toto vypnutí může být z mnoha důvodů, například vyrovnávání zatížení.

### <a name="dropped-dd"></a>Vyřazeno (DD)
Ve stavu zrušeno, instance již na uzlu neběží. V uzlu není také žádný stav. V tomto okamžiku Service Fabric udržuje metadata o této instanci, která je nakonec také odstraněna.

### <a name="down-d"></a>Dolů (D)
Ve vypnutém stavu není kód repliky spuštěný, ale v tomto uzlu existuje trvalý stav pro tuto repliku. Replika může být mimo provoz z mnoha důvodů – například uzel, který je mimo provoz, chybu v kódu repliky, upgrade aplikace nebo chyby repliky.

Povinná replika je otevřená Service Fabric podle potřeby, například když se upgrade dokončí na uzlu.

Role repliky není relevantní v nefunkčním stavu.

### <a name="opening-op"></a>Otevírání (OP)
Když Service Fabric potřebuje znovu vytvořit repliku, vstoupí do stavu otevření. Tento stav může být například po dokončení upgradu kódu aplikace na uzlu. 

Pokud hostitel aplikace nebo uzel pro otevření repliky selže, přejde do stavu v pořádku.

Role repliky není relevantní v otevřeném stavu.

### <a name="standby-sb"></a>Pohotovostní (SB)
Pohotovostní replika je replikou trvalé služby, která se ukončí a pak se otevřela. Tuto repliku může použít Service Fabric, pokud je potřeba přidat další repliku do sady replik (protože replika již obsahuje část stavu a proces sestavení je rychlejší). Po vypršení platnosti StandByReplicaKeepDuration je záložní replika zahozena.

Pokud hostitel aplikace nebo uzel selže v pohotovostní replice, přejde do stavu mimo provoz.

Role repliky není relevantní v pohotovostním stavu.

> [!NOTE]
> Všechny repliky, které nejsou vypnuté ani vyřazené, *se* považují za nefunkční.
>

> [!NOTE]
> Je možné přejít ze všech stavů do stavu zrušeno pomocí možnosti **ForceRemove** na `Remove-ServiceFabricReplica` .
>

## <a name="replica-role"></a>Role repliky 
Role repliky určuje její funkci v sadě replik:

- **Primární (P)**: v sadě replik je jedna primární verze, která je zodpovědná za provádění operací čtení a zápisu. 
- **ActiveSecondary (e)**: Jedná se o repliky, které přijímají aktualizace stavu z primární databáze, aplikují je a pak odešlou potvrzení zpět. Sada replik obsahuje několik aktivních sekundárních prostředí. Počet aktivních sekundárních sekundárních hodnot určuje počet chyb, které služba může zpracovat.
- **IdleSecondary (I)**: tyto repliky jsou vytvářeny primárním serverem. Jejich příjem z primární úrovně předtím, než se dají zvýšit na aktivní sekundární. 
- **Žádné (N)**: tyto repliky nemají zodpovědnost v sadě replik.
- **Neznámé (U)**: Jedná se o počáteční roli repliky předtím, než přijme jakékoli volání rozhraní API **ChangeRole** z Service Fabric.

Následující diagram znázorňuje přechody role repliky a příklady scénářů, ve kterých mohou nastat:

![Role repliky](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U-> P: vytváření nové primární repliky.
- U-> I: vytvoření nové nečinné repliky.
- U-> N: odstraňování pohotovostní repliky.
- I-> S: povýšení sekundární nečinné na aktivní sekundární, aby jeho potvrzení přispělo k kvoru.
- I-> P: propagace sekundární nečinné na primární. K tomu může dojít v případě speciálního překonfigurování, pokud je sekundárním kandidátem na primární počítač.
- I-> N: odstraňování sekundární repliky po nečinnosti.
- S-> P: povýšení aktivní sekundární na primární. Důvodem může být převzetí služeb při selhání primárního nebo primárního přesunu iniciované Správce prostředků clusteru. Například může být v reakci na upgrade aplikace nebo vyrovnávání zatížení.
- S-> N: odstranění aktivní sekundární repliky.
- P-> S: snížení úrovně primární repliky. Důvodem může být primární přesun iniciované Správce prostředků clusteru. Například může být v reakci na upgrade aplikace nebo vyrovnávání zatížení.
- P-> N: odstranění primární repliky.

> [!NOTE]
> Modely programování vyšší úrovně, například [Reliable Actors](service-fabric-reliable-actors-introduction.md) a [Reliable Services](service-fabric-reliable-services-introduction.md), skrývají koncept rolí repliky od vývojáře. V části Actors není pojem role zbytečný. Ve službách se ve většině scénářů zjednodušilo hlavně.
>

## <a name="next-steps"></a>Další kroky
Další informace o Service Fabric konceptech najdete v následujícím článku:

[Životní cyklus Reliable Services – C#](service-fabric-reliable-services-lifecycle.md)

