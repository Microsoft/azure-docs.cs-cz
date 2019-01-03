---
title: Service Fabric Cluster Resource Manager – spřažení | Dokumentace Microsoftu
description: Přehled konfigurace spřažení pro služeb Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 678073e1-d08d-46c4-a811-826e70aba6c4
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: bda70a6854dc6d94d3d4b37e6f587e4dcd045126
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543833"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Konfigurace a použití spřažení služeb v Service Fabric
Spřažení je ovládací prvek, který je k dispozici hlavně k usnadnění přechodu větší monolitické aplikace do cloudu a mikroslužby světa. Používá se také jako optimalizace pro zlepšení výkonu služeb, i když to může mít vedlejší účinky.

Řekněme, že jste spojili větší aplikace, nebo disk, který právě nebyl navržen s mikroslužeb na paměti, do Service Fabric (nebo v jakémkoli distribuované prostředí). Tento typ přechodu je běžné. Spuštění přenesením celé aplikace do prostředí, balení a zajistit jeho běží hladce. Potom spustíte, jeho rozdělení na různé menší služby všechny komunikovat mezi sebou.

Nakonec můžete zjistit, že aplikace dochází k nějakým potížím. Problémy se obvykle spadají do jedné z těchto kategorií:

1. Některé součásti X v monolitické aplikace měla nezdokumentovaný závislosti na komponentě Y a právě zapnuté těchto komponent do samostatných služeb. Protože tyto služby běží na různých uzlech v clusteru, jsou k přerušení.
2. Tyto součásti komunikovat přes (místní pojmenované kanály | sdílené paměti | soubory na disku) a opravdu potřebujete umožnit zápis do sdílené místního prostředku z důvodů výkonu hned teď. Že pevný závislost získá později odebral, možná.
3. Všechno, co je v pořádku, ale ukázalo se, že tyto dvě komponenty mají ve skutečnosti přetížení a výkonu citlivé. Při jejich přesunu do samostatných služeb celkový výkon aplikace tanked nebo latence zvýšit. Celkové aplikace v důsledku toho se podmínky očekávání.

V těchto případech jsme nechcete přijít o našich refaktoringu práci a nechcete, aby se vrátíte do monolitu. Poslední podmínka může být i jako obyčejný optimalizace žádoucí. Nicméně dokud jsme přepracovat součásti, které budou přirozeně fungují jako služby (nebo nám můžete vyřešit očekávaný výkon nějakým způsobem) budeme potřebovat určitou představu o umístění.

Co dělat? Dobře můžete zkusit zapnutí spřažení.

## <a name="how-to-configure-affinity"></a>Jak nakonfigurovat nastavení bránící spřažení
Nastavení spřažení, můžete definovat vztah přidružení mezi dvěma různými službami. Si můžete představit vztahů jako "odkazující" jedné služby v jiné a slavným "tuto službu spustit pouze ve kterém běží služba." Někdy se označují spřažení jako nadřazené a podřízené relace (kde je bod podřízené v nadřazené). Spřažení zajistí, že replik nebo instancí jedné služby se umístí na stejné uzly jako jiné služby.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Podřízené služby mohou účastnit jenom jeden spřažení relace. Pokud jste chtěli na podřízenou k spřažené s dvě nadřazené služby najednou máte několik možností:
> - Reverse vztahy (mají parentService1 a parentService2 odkazovaly na aktuální službu podřízené), nebo
> - Určí jeden z rodičů jako centrum s konvencí a mít všechny služby, přejděte na tuto službu. 
>
> Výsledné chování umístění v clusteru by měl být stejný.
>

## <a name="different-affinity-options"></a>Možnosti různých vztahů
Spřažení je reprezentován některou z několika korelace schémata a má dva různé režimy. Nejběžnější režim spřažení je, čemu říkáme NonAlignedAffinity. V NonAlignedAffinity replik nebo instancí jiné služby jsou umístěny na stejné uzly. V režimu je AlignedAffinity. Zarovnané spřažení je užitečný jenom v případě stavové služby. Konfigurace dvě stavové služby, které mají zarovnané spřažení zajistí, že primárek tyto služby jsou umístěny na stejné uzly navzájem. Navíc způsobí, že každý pár sekundární databáze pro služby využívané umístit na stejné uzly. Také je možné (ale méně běžné) ke konfiguraci NonAlignedAffinity pro stavové služby. Pro NonAlignedAffinity různých replik dva stavové služby běžet na stejné uzly, ale jejich primárek můžou být nakonec na různých uzlech.

<center>
![Režimy vztahů a jejich vliv][Image1]
</center>

### <a name="best-effort-desired-state"></a>Nejlepší úsilí požadovaného stavu
Vztah spřažení je nejlepší úsilí. Neposkytuje stejné záruky kolokaci nebo spolehlivost spuštěným ve stejném spustitelný proces. Služby ve vztahu k přidružení jsou v podstatě různé entity, které může selhat a přesunout nezávisle na sobě. Vztah spřažení může také rozdělit, i když tyto konce jsou dočasné. Například omezení kapacity může to znamenat, že pouze některé objekty služby ve vztahu spřažení vejde na daný uzel. V těchto případech i když existuje vztah přidružení na místě, se nedají vynutit z důvodu dalších omezení. Pokud je to možné udělat, porušení zásad je automaticky opraven později.

### <a name="chains-vs-stars"></a>Řetězy vs. hvězdiček
Ještě dnes Cluster Resource Manager nemá přístup k modelu řetězů, spřažení relace. Co to znamená, že služba, která je podřízená položka v jednom vztahu spřažení nemůže být nadřazená jiné spřažení relace. Pokud chcete tento typ relace model, máte efektivně modelu jako hvězdičku, spíše než řetězce. Pokud chcete přesunout z řetězce na hvězdičku, nejnižší podřízený by být prvek první podřízený, nadřazené místo. V závislosti na uspořádání vašich služeb budete muset provést několikrát. Pokud není žádná služba přirozené nadřazené, budete muset vytvořit nového, který slouží jako zástupný symbol. V závislosti na požadavcích, můžete také chtít prozkoumat [skupin aplikací](service-fabric-cluster-resource-manager-application-groups.md).

<center>
![Řetězy vs. Počet hvězdiček v kontextu spřažení relace][Image2]
</center>

Další věc ještě dnes si o spřažení relací je, že jsou směrové ve výchozím nastavení. To znamená, že pravidlo spřažení pouze vynutí, že podřízené umístěné s nadřazenými. Takže není jisté, že nadřazený se nachází na podřízené. Proto pokud dojde k narušení vztahů a chcete-li opravit porušení zásad z nějakého důvodu není možné přesunout podřízené do nadřazeného uzlu, pak – i v případě přesunutí nadřazené podřízeného uzlu by opravíte porušení – nadřazená nebude přesunuta do th elektronické podřízený uzel. Nastavení souboru config [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) na hodnotu true by odstranila směrovou orientaci. Je také důležité si uvědomit, že vztah spřažení nelze vynikající nebo okamžitě vynutit, protože různé služby s jinou životní cykly a může selhat a přesunout nezávisle na sobě. Řekněme například, že nadřazený náhle převezme služby při selhání do jiného uzlu vzhledem k tomu, že ho došlo k chybě. Cluster Resource Manageru a převzetí služeb při selhání Manageru zpracovávat převzetí služeb při selhání nejprve od udržování služby, v souladu, a k dispozici je priorita. Po dokončení převzetí služeb vztah spřažení je nefunkční, ale Cluster Resource Manager předpokládá, že všechno je v pořádku dokud ho oznámení, že podřízené není umístěn v nadřazené. Tyto druhy kontroly jsou prováděny pravidelně. Další informace o tom, jak Cluster Resource Manager vyhodnotí omezení je k dispozici v [v tomto článku](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), a [tohohle](service-fabric-cluster-resource-manager-balancing.md) hovoří o další informace o tom, jak nakonfigurovat tempo, na kterém jsou tato omezení vyhodnotit.   


### <a name="partitioning-support"></a>Dělení podpory
Všimněte si, že o spřažení je posledním krokem je tento spřažení relace nejsou podporovány, kde je nadřazený rozdělit na oddíly. Nakonec může podporovány dělené nadřazené služby, ale ještě dnes není povolený.

## <a name="next-steps"></a>Další postup
- Další informace o konfiguraci služby [informace o konfiguraci služby](service-fabric-cluster-resource-manager-configure-services.md)
- Omezení služby pro malou skupinu počítačů nebo agregaci zatížení služeb, použijte [skupiny aplikací](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png