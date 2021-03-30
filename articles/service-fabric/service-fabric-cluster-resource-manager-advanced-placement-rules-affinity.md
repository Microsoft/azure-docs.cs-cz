---
title: Service Fabric Správce prostředků clusteru – spřažení
description: Přehled spřažení služeb pro služby Azure Service Fabric Services a pokyny k konfiguraci spřažení služeb
services: service-fabric
documentationcenter: .net
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c141cd96877fd140b858d0aaed9197f2de80eca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89005730"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Konfigurace a použití spřažení služby v Service Fabric
Spřažení je ovládací prvek, který je k dispozici hlavně k usnadnění přechodu větších aplikací monolitické do cloudu a mikroslužeb na světě. Používá se také jako optimalizace pro zlepšení výkonu služeb, i když to může mít vedlejší účinky.

Řekněme, že přinášíte větší aplikaci, nebo tu, která se na mikroslužby nenavrhla, aby se mohla Service Fabric (nebo jakékoli distribuované prostředí). Tento typ přechodu je běžný. Začnete tím, že celou aplikaci vyvoláte do prostředí, zabalíte je a zajistěte, aby běžela bez problémů. Pak můžete začít s rozdělením na jiné menší služby, které vzájemně komunikují.

Nakonec se můžete setkat s tím, že u aplikace dochází k nějakým problémům. Problémy obvykle spadají do jedné z těchto kategorií:

1. Některá součást X v aplikaci monolitické měla nedokumentovaný závislost na ose Y a tyto součásti jste právě přepnuli na samostatné služby. Vzhledem k tomu, že tyto služby jsou teď spuštěné v různých uzlech clusteru, jsou poškozené.
2. Tyto komponenty komunikují přes (místní pojmenované kanály | sdílená paměť | soubory na disku) a ve skutečnosti musí být schopné zapisovat do sdíleného místního prostředku z důvodů výkonu. Tato pevná závislost bude později odebrána.
3. Všechno je v pořádku, ale dojde k tomu, že tyto dvě komponenty jsou ve skutečnosti závislé na konverzaci a výkonu. Když je přesunula do samostatných služeb, celkový výkon aplikace v cisternách nebo latenci se zvýšil. V důsledku toho celková aplikace nesplňuje očekávání.

V těchto případech Nechceme přijít o naši práci s refaktoringem a nechci se vrátit k monolitu. Poslední podmínka může být dokonce vhodná jako jednoduchá optimalizace. Dokud však můžeme změnit návrh komponent, aby fungovaly přirozeně jako služby (nebo až do toho, abychom mohli vyřešit očekávání jiným způsobem), budeme potřebovat nějaký smysl.

Co dělat? Můžete se také pokusit zapnout spřažení.

## <a name="how-to-configure-affinity"></a>Jak nakonfigurovat spřažení
Chcete-li nastavit spřažení, definujte vztah spřažení mezi dvěma různými službami. Spřažení si můžete představit jako "ukazující" jednu službu na jiném a vyslovením "Tato služba se dá spustit jenom tam, kde je tato služba spuštěná." Někdy odkazujeme na spřažení jako na vztah nadřazenosti/podřízenosti (kde navedete podřízenou položku na nadřazeném objektu). Spřažení zajišťuje, že repliky nebo instance jedné služby jsou umístěné na stejných uzlech jako u jiné služby.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Podřízená služba se může zúčastnit jenom jedné relace spřažení. Pokud jste chtěli, aby se podřízená položka spřažené ke dvěma nadřazeným službám najednou, máte několik možností:
> - Obrátíte vztahy (parentService1 a parentService2 Point v aktuální podřízené službě), nebo
> - Jednu z rodičů určete jako centrum podle konvence a všechny služby v této službě. 
>
> Výsledný způsob umístění v clusteru by měl být stejný.
>

## <a name="different-affinity-options"></a>Různé možnosti spřažení
Spřažení je reprezentované prostřednictvím jednoho z několika relačních schémat a má dva různé režimy. Nejběžnějším režimem spřažení je to, co voláme NonAlignedAffinity. V NonAlignedAffinity jsou repliky nebo instance různých služeb umístěné na stejných uzlech. Druhý režim je AlignedAffinity. Zarovnaná spřažení je užitečné jenom u stavových služeb. Konfigurace dvou stavových služeb pro zarovnávání spřažení zajišťuje, že primární prvky těchto služeb jsou umístěné na stejných uzlech. Také způsobí, že všechny dvojice sekundárních pro tyto služby budou umístěny na stejné uzly. K nakonfigurování NonAlignedAffinity pro stavové služby je také možné (i méně časté). V případě NonAlignedAffinity se na stejných uzlech spustí různé repliky dvou stavových služeb, ale jejich primární počítače můžou končit na různých uzlech.

<center>

![Režimy spřažení a jejich účinky][Image1]
</center>

### <a name="best-effort-desired-state"></a>Požadovaný stav nejlepšího úsilí
Vztah spřažení je nejlepší úsilí. Neposkytuje stejné záruky na společné umísťování nebo spolehlivost, které běží ve stejném spustitelném procesu. Služby v relaci spřažení jsou zásadními různými entitami, které mohou selhat a přesunout nezávisle. Relace spřažení by mohla také přerušit, i když jsou tyto přerušení dočasné. Například omezení kapacity může znamenat, že se na daný uzel mohou vejít pouze některé objekty služby ve vztahu spřažení. V takových případech, i když existuje vztah spřažení, nejde vynutit z důvodu dalších omezení. Pokud je to možné, je porušení automaticky opraveno později.

### <a name="chains-vs-stars"></a>Řetězce vs. hvězdičky
V současné době Správce prostředků clusteru nedokázal modelovat řetězy vztahů spřažení. To znamená, že služba, která je podřízená v jedné relaci spřažení, nemůže být nadřazená v jiné relaci spřažení. Pokud chcete modelovat tento typ relace, je efektivně nutné modelovat ho jako hvězdičku, nikoli jako řetězec. Chcete-li přejít z řetězce na hvězdičku, pak bude podřízený objekt podřízenosti nadřazené prvnímu podřízenému prvku. V závislosti na tom, jak se vaše služby týkají, může být nutné provést tento postup několikrát. Pokud není k dispozici žádná přirozená nadřazená služba, může být nutné ji vytvořit, která slouží jako zástupný symbol. V závislosti na vašich požadavcích můžete chtít také vyhledat [skupiny aplikací](service-fabric-cluster-resource-manager-application-groups.md).

<center>

![Řetězení vs. hvězdičky v kontextu vztahů spřažení][Image2]
</center>

Další věcí, které se týkají vztahů spřažení, je současné, že jsou ve výchozím nastavení směrované. To znamená, že pravidlo spřažení vynutilo pouze podřízený objekt umístěný s nadřazeným objektem. Nezajišťuje, aby se nadřazený objekt nacházel s podřízeným objektem. Proto pokud dojde k porušení spřažení a k nápravě porušení z nějakého důvodu není možné přesunout podřízenou položku na uzel nadřazené položky, pak--i když přesunete nadřazený uzel do uzlu podřízeného, by došlo k nápravě porušení – nadřazená položka nebude přesunuta do uzlu podřízeného. Nastavení [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) konfigurace na hodnotu true by vedlo k odebrání směru. Je také důležité si uvědomit, že vztah spřažení nelze perfektní ani okamžitě vykonat, protože různé služby mají různé životní cykly a mohou selhat a přesouvat se nezávisle. Řekněme například, že nadřazený objekt se při převzetí služeb při selhání převezme na jiný uzel, protože došlo k chybě. Cluster Správce prostředků a Správce převzetí služeb při selhání předá převzetí služeb při selhání, protože udržuje služby, které jsou v souladu s prioritou a jsou dostupné. Po dokončení převzetí služeb při selhání se relace spřažení poruší, ale cluster Správce prostředků považuje všechno za dostupné, dokud nezjistí, že podřízený objekt není umístěný s nadřazeným objektem. Tyto typy kontrol jsou pravidelně prováděny. Další informace o tom, jak Správce prostředků vyhodnotit omezení v [tomto článku](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), se dozvíte [o tom,](service-fabric-cluster-resource-manager-balancing.md) jak nakonfigurovat tempo, na kterých jsou tato omezení vyhodnocována.   


### <a name="partitioning-support"></a>Podpora dělení
Poslední aspekt oznámení o spřažení je, že vztahy spřažení nejsou podporované, pokud je nadřazený objekt rozdělený na oddíly. Je možné, že se v daném oddílu můžou podporovat i podřízené nadřazené služby, ale v současné době to není povolené.

## <a name="next-steps"></a>Další kroky
- Další informace o konfiguraci služeb najdete v informacích [o konfiguraci služeb](service-fabric-cluster-resource-manager-configure-services.md) .
- Chcete-li omezit služby na malou skupinu počítačů nebo pro agregaci zatížení služeb, použijte [skupiny aplikací](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png