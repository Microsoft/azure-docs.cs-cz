---
title: Správce prostředků clusteru Service Fabric - spřažení | Microsoft Docs
description: Přehled konfigurace spřažení pro služby Service Fabric
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
ms.openlocfilehash: 57abea79a620aa83e16ad4cc2fd78a4294f2b278
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Konfigurace a použití spřažení služby v Service Fabric
Spřažení je ovládací prvek, který je k dispozici především ke zjednodušení přechodu větší monolitický aplikací do cloudu a mikroslužeb world. Používá se také jako optimalizace pro zlepšení výkonu služeb, i když tak může mít vedlejší účinky.

Řekněme, že jste přináší větší aplikace, nebo ten, který právě nebyl navržen s mikroslužeb pamatovat na Service Fabric (nebo všechny distribuovaném prostředí). Tento typ přechodu je běžné. Můžete začít zrušení celá aplikace do prostředí, balení a zkontrolování, zda je spuštěna bez problémů. Potom spusťte jej rozdělit do různých menší služby všechny komunikovat navzájem.

Nakonec můžete zjistit, že aplikace dochází k problémům. Problémy se obvykle spadají do jedné z těchto kategorií:

1. Některé součásti X monolitický aplikace měla nedokumentovanými závislost na součásti Y a právě převedena tyto součásti na samostatné služby. Vzhledem k tomu, že tyto služby běží v rozdílných uzlech v clusteru, jsou poškozená.
2. Tyto součásti komunikovat přes (místní pojmenované kanály | sdílené paměti | soubory na disku) a skutečně potřebují k dát zapisovat do sdíleného místního prostředku z důvodů výkonu hned teď. Že pevné závislostí získá později odebral, možná.
3. Všechno, co je v pořádku, ale ukazuje se, že tyto dvě součásti jsou ve skutečnosti chatty a výkonu citlivé. Při jejich přesunu do samostatné služby celkového výkonu aplikace tanked nebo latenci vyšší. V důsledku toho není celkové aplikace splnění očekávání.

V těchto případech jsme nechcete ztratit naše refaktoringu pracovní a nechcete, aby se vrátíte k monolitu. Poslední stav může být i žádoucí jako prostý optimalizace. Ale dokud jsme můžete změnit návrh součásti, které budou fungovat přirozeně jako služby (nebo dokud jsme může pomoct vyřešit očekávaný výkon nějakým způsobem) vytvoříme potřebovat některé smysl polohu.

Co dělat? Dobře mohou zkuste zapnete spřažení.

## <a name="how-to-configure-affinity"></a>Postup konfigurace spřažení
Pokud chcete nastavit spřažení, definovat relaci spřažení mezi dva různé služby. Si můžete představit spřažení jako "ukazující" jedné služby v jiné a informacemi o tom, "tuto službu spustit pouze kde běží služba." Někdy označujeme spřažení relaci nadřazený podřízený (kde je bod podřízených v nadřazené). Spřažení zajišťuje, že replik nebo instancí jedné služby budou umístěny v uzlu stejné jako jiné služby.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Služba podřízené jenom účastnit vztahu jeden spřažení. Pokud byste chtěli podřízené spřáhnout s dvě nadřazené služby najednou máte několik možností:
> - Reverse vztahy (mají parentService1 a přejděte na aktuální službu podřízené parentService2), nebo
> - Určete jeden z rodičů jako rozbočovač podle konvence a mít všechny služby, přejděte na tuto službu. 
>
> Výsledné chování umístění v clusteru musí být stejná.
>

## <a name="different-affinity-options"></a>Možnosti různých spřažení
Spřažení je reprezentována prostřednictvím jedním z několika korelace schémata a má dva různé režimy. Nejběžnější režim spřažení nazýváme NonAlignedAffinity. V NonAlignedAffinity replik nebo instancí různé služby jsou umístěny na stejném uzlu. Další režim je AlignedAffinity. Zarovnaný spřažení je užitečný jenom v případě stavové služby. Konfigurace dvě stavové služby do mají zarovnán spřažení zajišťuje, že základní barvy těchto služeb budou umístěny v uzlu stejné jako navzájem. Také způsobuje, že každý pár sekundárních databází pro tyto služby má být umístěn do stejným uzlům. Také je možné (i když méně běžné) ke konfiguraci NonAlignedAffinity pro stavové služby. Pro NonAlignedAffinity jiné repliky dvě stavové služby běžet na stejné uzly, ale jejich základní barvy, může dojít v různých uzlech.

<center>
![Režimy spřažení a jejich důsledky][Image1]
</center>

### <a name="best-effort-desired-state"></a>Nejlepší stav úsilí potřeby
Relaci spřažení je nejlepší úsilí. Neposkytuje stejné záruky společné umístění nebo spolehlivosti spuštěná ve stejném nemá spustitelný proces. Služeb v relaci spřažení se podstatně liší entit, které můžou selhat a přesunování nezávisle. Relaci spřažení může také zalomit, i když jsou tyto konce dočasné. Například omezení kapacity může znamenat, že jenom některé objekty služby v relaci spřažení vejde na daný uzel. V těchto případech i v případě, že je na místě, vztah spřažení ji nelze vynutit z důvodu další omezení. Pokud je možné provést, porušení automaticky opraven později.

### <a name="chains-vs-stars"></a>Řetězy oproti hvězdičkami
Správce prostředků clusteru není ještě dnes moct modelu řetězy spřažení relací. Co to znamená, který je služba, která je podřízená v jednom vztahu spřažení nemůže být nadřízený prvek v jiné relace spřažení. Pokud chcete tento typ vztahu modelu, máte efektivně model jako hvězdu, nikoli řetězec. Pokud chcete přesunout z řetěz hvězdu, by být nadřazena nejspodnějších podřízené prvním podřízeným objektem nadřazené místo. V závislosti na uspořádání vašim službám budete muset provést několikrát. Pokud není žádná služba přirozené nadřazené, máte může vytvořit jeden, který slouží jako zástupný znak. V závislosti na požadavcích, můžete také chtít viděl [skupin aplikací](service-fabric-cluster-resource-manager-application-groups.md).

<center>
![Řetězy vs. Počet hvězdiček v kontextu relace spřažení][Image2]
</center>

Další fakt, který ještě dnes Poznámka o spřažení relace je, že jsou směrovou. To znamená, že pravidlo spřažení pouze vynucuje, že podřízená umístěn nadřazený. Ji není zajistí, že k nadřazené umístěné se podřízené. Je také důležité si uvědomit, že vztah spřažení nelze vynikající nebo okamžitě vynutit, protože různé služby s jinou životní cykly a může selhat a přesunout nezávisle. Řekněme například, že nadřazený najednou převezme jiný uzel vzhledem k tomu, že ho došlo k chybě. Správce prostředků clusteru a převzetí služeb při selhání správce zpracování převzetí služeb při selhání nejprve od udržování služeb, konzistentní, a k dispozici je priorita. Po dokončení převzetí vztah spřažení je poškozený, ale správce prostředků clusteru předpokládá, že všechno, co je vhodná, dokud se oznámení, že podřízená není umístěný se nadřazenou položkou. Tyto řazení kontrol se provádí pravidelně. Další informace o tom, jak správce prostředků clusteru vyhodnotí omezení je k dispozici v [v tomto článku](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), a [tato](service-fabric-cluster-resource-manager-balancing.md) komunikuje Další informace o tom, jak nakonfigurovat cadence, na které se vyhodnocují těchto omezení.   


### <a name="partitioning-support"></a>Vytváření oddílů podpory
Poslední věcí Všimněte o spřažení je tento spřažení relace nejsou podporované, kde je nadřazená rozdělena na oddíly. Nakonec může podporovány oddílů nadřazené služby, ale ještě dnes není povoleno.

## <a name="next-steps"></a>Další postup
- Další informace o konfiguraci služby [Další informace o konfiguraci služby](service-fabric-cluster-resource-manager-configure-services.md)
- Omezení služby pro malou skupinu počítačů nebo agregování zatížení služeb, použijte [skupin aplikací](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png