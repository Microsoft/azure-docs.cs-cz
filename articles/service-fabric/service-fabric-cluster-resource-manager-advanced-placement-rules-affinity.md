---
title: Správce prostředků clusteru prostředků infrastruktury služby – spřažení
description: Přehled spřažení služeb pro služby Azure Service Fabric a pokyny pro konfiguraci spřažení služeb.
services: service-fabric
documentationcenter: .net
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7bfd261802fbf891b8f45079255783cb1e8ac7d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551739"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Konfigurace a používání spřažení služeb v service fabric
Spřažení je ovládací prvek, který je k dispozici především pomoci usnadnit přechod větších monolitických aplikací do cloudu a mikroslužeb světa. Používá se také jako optimalizace pro zlepšení výkonu služeb, i když to může mít vedlejší účinky.

Řekněme, že přinášíte větší aplikaci, nebo tu, která prostě nebyla navržena s ohledem na mikroslužby, do Service Fabric (nebo jakéhokoli distribuovaného prostředí). Tento typ přechodu je běžný. Začnete tím, že zvednete celou aplikaci do prostředí, zabalíte ji a ujistíte se, že běží hladce. Pak to začnete rozevírat na různé menší služby, které spolu všichni mluví.

Nakonec můžete zjistit, že aplikace dochází k některým problémům. Problémy obvykle spadají do jedné z těchto kategorií:

1. Některé součásti X v monolitické aplikaci měly nedokumentovanou závislost na součásti Y a právě jste tyto součásti přeměnili na samostatné služby. Vzhledem k tomu, že tyto služby jsou nyní spuštěny na různých uzlech v clusteru, jsou přerušené.
2. Tyto součásti komunikují prostřednictvím (místní pojmenované kanály | sdílené paměti | soubory na disku) a skutečně potřebují být schopny zapisovat do sdíleného místního prostředku z důvodů výkonu právě teď. Ta tvrdá závislost se odstraní později, možná.
3. Všechno je v pořádku, ale ukazuje se, že tyto dvě složky jsou ve skutečnosti chatty / výkon citlivý. Když je přesunuli do samostatných služeb, celkový výkon aplikace se zvýšil nebo se zvýšila latence. V důsledku toho celková aplikace nesplňuje očekávání.

V těchto případech nechceme přijít o refaktoringovou práci a nechceme se vrátit k monolitu. Poslední podmínka může být dokonce žádoucí jako prostý optimalizace. Nicméně, dokud nebudeme moci přepracovat komponenty tak, aby fungovaly přirozeně jako služby (nebo dokud nevyřešíme očekávání výkonu jiným způsobem), budeme potřebovat nějaký smysl pro lokalitu.

Co dělat? No, můžeš zkusit zapnout spřízněnost.

## <a name="how-to-configure-affinity"></a>Jak nakonfigurovat spřažení
Chcete-li nastavit spřažení, definujte vztah spřažení mezi dvěma různými službami. Spřažení si můžete myslet jako "ukázání" jedné služby na druhou a říká: "Tato služba může běžet pouze tam, kde je tato služba spuštěna." Někdy označujeme afinitu jako vztah rodič/podřízený vztah (kde namiřujete dítě na rodiče). Spřažení zajišťuje, že repliky nebo instance jedné služby jsou umístěny na stejných uzlech jako uzly jiné služby.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Dětská služba se může účastnit pouze jednoho vztahu spřažení. Pokud chcete, aby dítě bylo spřaženo se dvěma nadřazené služby najednou máte několik možností:
> - Stornovat vztahy (mají parentService1 a parentService2 bod v aktuální podřízené služby), nebo
> - Určete jednoho z rodičů jako rozbočovač podle konvence a mít všechny služby bod na této službě. 
>
> Výsledné chování umístění v clusteru by mělo být stejné.
>

## <a name="different-affinity-options"></a>Různé možnosti spřažení
Spřažení je reprezentováno prostřednictvím jednoho z několika korelace schémat a má dva různé režimy. Nejběžnější režim spřažení je to, co nazýváme NonAlignedAffinity. V NonAlignedAffinity repliky nebo instance různých služeb jsou umístěny na stejné uzly. Druhý režim je AlignedAffinity. Zarovnané spřažení je užitečné pouze se stavovými službami. Konfigurace dvou stavových služeb tak, aby byly zarovnány spřažení, zajistí, že primárky těchto služeb budou umístěny na stejných uzlech jako ostatní. Také způsobí, že každá dvojice sekundárních pro tyto služby, které mají být umístěny na stejné uzly. Je také možné (i když méně časté) ke konfiguraci NonAlignedAffinity pro stavové služby. Pro NonAlignedAffinity by různé repliky dvou stavových služeb běžely na stejných uzlech, ale jejich primárky by mohly skončit na různých uzlech.

<center>

![Režimy afinity a jejich účinky][Image1]
</center>

### <a name="best-effort-desired-state"></a>Nejlepší úsilí požadovaný stav
Vztah afinity je nejlepší úsilí. Neposkytuje stejné záruky společného umístění nebo spolehlivosti jako spuštění ve stejném spustitelném procesu. Služby ve vztahu spřažení jsou zásadně odlišné entity, které mohou selhat a přesunout nezávisle. Vztah spřažení by se také mohl zlomit, i když tyto přestávky jsou dočasné. Omezení kapacity může například znamenat, že pouze některé objekty služby ve vztahu spřažení se vejde do daného uzlu. V těchto případech i v případě, že je vztah spřažení na místě, nemůže být vynucena z důvodu jiných omezení. Pokud je to možné, porušení je automaticky opraveno později.

### <a name="chains-vs-stars"></a>Řetězy vs. hvězdy
Dnes Správce prostředků clusteru není schopen modelovat řetězce vztahů spřažení. To znamená, že služba, která je podřízeným dítětem v jednom vztahu spřažení, nemůže být rodičem v jiném vztahu spřažení. Pokud chcete modelovat tento typ vztahu, musíte jej efektivně modelovat jako hvězdu, nikoli jako řetězec. Chcete-li přesunout z řetězce na hvězdu, nejspodnější podřízená by být nadřazený k první dítě rodiče místo. V závislosti na uspořádání vašich služeb to možná budete muset udělat vícekrát. Pokud neexistuje žádná přirozená nadřazená služba, bude pravděpodobně muset vytvořit službu, která slouží jako zástupný symbol. V závislosti na vašich požadavcích můžete také nahlédnout do [skupin aplikací](service-fabric-cluster-resource-manager-application-groups.md).

<center>

![Řetězy vs. Hvězdy v kontextu vztahů s afinitou][Image2]
</center>

Další věc, kterou je třeba si uvědomit, o vztahů spřažení dnes je, že jsou směrové ve výchozím nastavení. To znamená, že pravidlo spřažení pouze vynucuje, že podřízené umístěné s nadřazeným objektem. Nezajišťuje, že rodič je umístěn s podřízeným. Proto pokud dojde k porušení afinity a opravit porušení z nějakého důvodu není možné přesunout dítě do uzlu rodiče, pak – i v případě, že přesunutí rodiče do uzlu dítěte by bylo opraveno porušení - rodič nebude přesunutdo uzlu rodiče uzlu dítěte. Nastavení konfigurace [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) na true by odebrat směrovost. Je také důležité si uvědomit, že vztah spřažení nemůže být dokonalý nebo okamžitě vynucený, protože různé služby mají různé životní cykly a mohou selhat a pohybovat se nezávisle. Řekněme například, že nadřazený objekt náhle převezme služby při selhání do jiného uzlu, protože došlo k chybě. Správce prostředků clusteru a správce převzetí služeb při selhání nejprve zpracovávají převzetí služeb při selhání, protože prioritou je udržování služeb, konzistentní a dostupné. Po dokončení převzetí služeb při selhání je vztah spřažení přerušen, ale Správce prostředků clusteru si myslí, že je vše v pořádku, dokud si nevšimne, že dítě není umístěno s nadřazeným objektem. Tyto druhy kontrol jsou prováděny pravidelně. Další informace o tom, jak Správce prostředků clusteru vyhodnocuje omezení je k dispozici v [tomto článku](service-fabric-cluster-resource-manager-management-integration.md#constraint-types)a [tento jeden](service-fabric-cluster-resource-manager-balancing.md) hovoří více o tom, jak nakonfigurovat kadence, na které jsou vyhodnocovány tato omezení.   


### <a name="partitioning-support"></a>Podpora dělení
Poslední věc, kterou si všimnete o spřažení je, že vztahy spřažení nejsou podporovány, kde je nadřazený oddíl. Dělené nadřazené služby mohou být nakonec podporovány, ale dnes není povoleno.

## <a name="next-steps"></a>Další kroky
- Další informace o konfiguraci služeb [naleznete v informacích o konfiguraci služeb](service-fabric-cluster-resource-manager-configure-services.md)
- Chcete-li omezit služby na malou sadu počítačů nebo agregovat zatížení služeb, použijte [skupiny aplikací](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png