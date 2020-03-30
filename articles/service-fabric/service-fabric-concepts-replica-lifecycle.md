---
title: Repliky a instance ve službě Azure Fabric
description: Další informace o replikách a instancích ve službě Service Fabric, včetně přehledu jejich životního cyklu a funkcí.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: cf21af43de553a2802289e44eaece12952d077d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258561"
---
# <a name="replicas-and-instances"></a>Repliky a instance 
Tento článek poskytuje přehled životního cyklu replikstavové služby a instance bezstavové služby.

## <a name="instances-of-stateless-services"></a>Instance služeb bez státní příslušnosti
Instance bezstavové služby je kopií logiky služby, která běží na jednom z uzlů clusteru. Instance v rámci oddílu je jednoznačně identifikována jeho **InstanceId**. Životní cyklus instance je modelován v následujícím diagramu:

![Životní cyklus instance](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>Vbudova (IB)
Poté, co Správce prostředků clusteru určí umístění pro instanci, vstoupí do tohoto stavu životního cyklu. Instance je spuštěna v uzlu. Hostitel aplikace je spuštěn, instance je vytvořena a poté otevřena. Po dokončení spuštění instance přejde do stavu připraven. 

Pokud se dojde k chybě hostitele aplikace nebo uzlu pro tuto instanci, přejde do vynechaný ho stavu.

### <a name="ready-rd"></a>Připraveno (RD)
Ve stavu ready instance je spuštěna na uzlu. Pokud je tato instance spolehlivé služby, **RunAsync** byl vyvolán. 

Pokud se dojde k chybě hostitele aplikace nebo uzlu pro tuto instanci, přejde do vynechaný ho stavu.

### <a name="closing-cl"></a>Uzávěrka (CL)
Ve stavu uzávěrky Azure Service Fabric je v procesu vypnutí instance v tomto uzlu. Toto vypnutí může být z mnoha důvodů – například upgrade aplikace, vyrovnávání zatížení nebo služba, která je odstraněna. Po dokončení vypnutí se přejde do vynechaný stavu.

### <a name="dropped-dd"></a>Vynecháno (DD)
Ve stavu vynechání instance již není spuštěna na uzlu. V tomto okamžiku Service Fabric udržuje metadata o této instanci, která je nakonec odstraněn také.

> [!NOTE]
> Je možné přejít z libovolného stavu do vynechaný stavu pomocí **ForceRemove** možnost na `Remove-ServiceFabricReplica`.
>

## <a name="replicas-of-stateful-services"></a>Repliky stavových služeb
Replika stavové služby je kopií logiky služby spuštěné na jednom z uzlů clusteru. Kromě toho replika udržuje kopii stavu této služby. Dva související koncepty popisují životní cyklus a chování stavových replik:
- Životní cyklus repliky
- Role repliky

Následující diskuse popisuje trvalé stavové služby. Pro nestálé (nebo v paměti) stavové služby dolů a vyřazené stavy jsou ekvivalentní.

![Životní cyklus repliky](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>Vbudova (IB)
Replika InBuild je replika, která je vytvořena nebo připravena pro připojení k sadě replik. V závislosti na roli repliky IB má různé sémantiky. 

Pokud se zhroutí hostitel aplikace nebo uzel pro repliku InBuild, přejde do stavu dolů.

   - **Primární repliky InBuild**: Primární InBuild jsou první repliky pro oddíl. Tato replika se obvykle stane při vytváření oddílu. Primární Repliky InBuild také vznikají, když jsou všechny repliky restartování oddílu nebo jsou vynechány.

   - **IdleSecondary InBuild repliky**: Jedná se buď nové repliky, které jsou vytvořeny Správce prostředků clusteru nebo existující repliky, které šly dolů a je třeba přidat zpět do sady. Tyto repliky jsou nasazeny nebo sestaveny primární před jejich připojení množiny replik jako ActiveSecondary a podílet se na potvrzení kvora operací.

   - **ActiveSecondary InBuild repliky**: Tento stav je pozorován v některých dotazech. Jedná se o optimalizaci, kde se sada replik nemění, ale replika musí být vytvořena. Replika sama sleduje přechody normálního stavu počítače (jak je popsáno v části o rolích repliky).

### <a name="ready-rd"></a>Připraveno (RD)
Replika připravená je replika, která se účastní replikace a potvrzení kvora operací. Stav připravenosti je použitelný pro primární a aktivní sekundární repliky.

Pokud se dojde k chybě hostitele aplikace nebo uzlu pro připravenou repliku, přejde do stavu dolů.

### <a name="closing-cl"></a>Uzávěrka (CL)
Replika zadá uzávěrkový stav v následujících scénářích:

- **Vypnutí kódu pro repliku**: Service Fabric může být nutné vypnout spuštěný kód pro repliku. Toto vypnutí může být z mnoha důvodů. Například může dojít z důvodu upgradu aplikace, prostředků nebo infrastruktury nebo z důvodu chyby hlášené replikou. Po dokončení repliky repliky přechody do stavu dolů. Trvalý stav přidružený k této replice, která je uložena na disku, není vyčištěn.

- **Odebrání repliky z clusteru**: Service Fabric může být nutné odebrat trvalý stav a vypnout spuštěný kód pro repliku. Toto vypnutí může být z mnoha důvodů, například vyrovnávání zatížení.

### <a name="dropped-dd"></a>Vynecháno (DD)
Ve stavu vynechání instance již není spuštěna na uzlu. V uzlu také nezůstal žádný stav. V tomto okamžiku Service Fabric udržuje metadata o této instanci, která je nakonec odstraněn také.

### <a name="down-d"></a>Dolů (D)
Ve stavu dolů není kód repliky spuštěn, ale trvalý stav pro tuto repliku existuje v tomto uzlu. Replika může být z mnoha důvodů, například uzel je dolů, selhání v kódu repliky, upgrade aplikace nebo chyby repliky.

Dolů replika je otevřena Service Fabric podle potřeby, například po dokončení upgradu na uzlu.

Role repliky není relevantní ve stavu dolů.

### <a name="opening-op"></a>Otevření (OP)
Dolů replika přejde do stavu otevření, když Service Fabric potřebuje převést repliku zpět nahoru. Tento stav může být například po dokončení upgradu kódu pro aplikaci na uzlu. 

Pokud se dojde k chybě hostitele aplikace nebo uzlu pro úvodní repliku, přejde do stavu dolů.

Role repliky není relevantní ve stavu otevření.

### <a name="standby-sb"></a>Pohotovostní režim (SB)
Replika pohotovostního režimu je replika trvalé služby, která přešla dolů a byla otevřena. Tato replika může být použita Service Fabric, pokud potřebuje přidat další repliku do sady replik (protože replika již má určitou část stavu a proces sestavení je rychlejší). Po vypršení platnosti pohotovostního režimu Bude zahozena replika pohotovostního režimu.

Pokud se dojde k chybě hostitele aplikace nebo uzlu pro pohotovostní repliku, přejde do stavu dolů.

Role repliky není relevantní v pohotovostním stavu.

> [!NOTE]
> Každá replika, která není dolů nebo klesla je považován za *nahoru*.
>

> [!NOTE]
> Je možné přejít z libovolného stavu do vynechaný stavu pomocí **Možnost IForceRemove** na `Remove-ServiceFabricReplica`.
>

## <a name="replica-role"></a>Role repliky 
Role repliky určuje její funkci v sadě replik:

- **Primární (P):** V sadě replik je jedna primární sada, která je zodpovědná za provádění operací čtení a zápisu. 
- **ActiveSecondary (S)**: Jedná se o repliky, které přijímají aktualizace stavu z primární, aplikujte je a potom odešlete zpět potvrzení. V sadě replik je více aktivních sekundárních zařízení. Počet těchto aktivních sekundárních položek určuje počet chyb, které může služba zpracovat.
- **IdleSecondary (I):** Tyto repliky jsou právě sestaveny primární. Jsou příjem stavu z primární před tím, než mohou být povýšeni na aktivní sekundární. 
- **Žádný (N):** Tyto repliky nemají odpovědnost v sadě replik.
- **Neznámý (U):** Toto je počáteční role repliky před tím, než obdrží jakékoli volání rozhraní **ChangeRole** API z Service Fabric.

Následující diagram znázorňuje přechody rolí repliky a některé ukázkové scénáře, ve kterých mohou nastat:

![Role repliky](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> P: Vytvoření nové primární repliky.
- U -> I: Vytvoření nové nečinné repliky.
- U -> N: Odstranění repliky v pohotovostním režimu.
- I -> S: Podpora nečinnosti sekundární k aktivní sekundární tak, aby jeho uznání přispět k usnášeníschopnosti.
- I -> P: Propagace nečinnosti sekundární k primární. K tomu může dojít při zvláštní rekonfigurace při nečinnosti sekundární je správný kandidát být primární.
- I -> N: Odstranění nečinné sekundární repliky.
- S -> P: Propagace aktivní sekundární k primární. To může být způsobeno převzetím služeb při selhání primární nebo primární přesun iniciovaný Správcem prostředků clusteru. Může být například v reakci na upgrade aplikace nebo vyrovnávání zatížení.
- S -> N: Odstranění aktivní sekundární repliky.
- P -> S: Snížení úrovně primární repliky. To může být způsobeno primárním pohybem iniciovanýsprávcem prostředků clusteru. Může být například v reakci na upgrade aplikace nebo vyrovnávání zatížení.
- P -> N: Odstranění primární repliky.

> [!NOTE]
> Programyovací modely vyšší úrovně, jako jsou [spolehlivé objekty actor](service-fabric-reliable-actors-introduction.md) a [spolehlivé služby](service-fabric-reliable-services-introduction.md), skrýt koncept rolí repliky od vývojáře. V herci, pojem role je zbytečné. Ve službách je to z velké části zjednodušené pro většinu scénářů.
>

## <a name="next-steps"></a>Další kroky
Další informace o konceptech service fabric naleznete v následujícím článku:

[Životní cyklus Reliable Services – C#](service-fabric-reliable-services-lifecycle.md)

