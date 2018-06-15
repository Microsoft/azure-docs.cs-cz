---
title: Azure Service Fabric událostí Store | Microsoft Docs
description: Další informace o EventStore Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 1d235d5a75975c8d58cad1bbde0f16df2b1b3e7a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206512"
---
# <a name="eventstore-service-overview"></a>Přehled služby EventStore

>[!NOTE]
>Od verze Service Fabric verze 6.2. rozhraní API EventStore jsou aktuálně ve verzi preview pro clustery Windows jenom spuštěné v Azure. Pracujeme na portování tuto funkci do systému Linux a také naše samostatné clustery.

## <a name="overview"></a>Přehled

Počínaje verze 6.2, je služba EventStore možnosti monitorování v Service Fabric, která poskytuje způsob, jak můžete zjistit stav clusteru nebo úlohy k danému bodu v čase. Službu EventStore zpřístupní události Service Fabric prostřednictvím rozhraní API, které můžete provádět volání. Tato rozhraní API EventStore umožňují dotazování clusteru přímo k získání diagnostiky dat na všechny entity v clusteru a je využívat pro:
* Diagnostikovat problémy v vývoj nebo testování nebo kde může pomocí monitorování kanálu
* Potvrďte, že akce správy, které jsou s ohledem na váš cluster jsou zpracovávány správně clusteru

Pokud chcete zobrazit úplný seznam událostí, které jsou k dispozici v EventStore, najdete v části [události Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

Službu EventStore můžete položit dotaz na události, které jsou k dispozici pro každou entitu a typ entity v clusteru. To znamená, že mohou odesílat dotazy na události na následujících úrovních;
* Cluster: všechny clusteru úroveň události
* Uzly: všechny uzlu úroveň události
* Uzel: události specifické pro jeden uzel, na základě `nodeName`
* Aplikací: úroveň události všechny aplikace
* Aplikace: specifické pro jednu aplikaci události
* Služba: události ze všech služeb v clusterech služby
* Služba: události z konkrétní službu
* Oddíly: události ze všech oddílů
* Oddíl: události z konkrétního oddílu
* Repliky: události ze všech replik / instance
* Replika: události z konkrétní repliky / instance


Službu EventStore má také umožňuje korelovat události v clusteru. Pohledem na události, které byly napsané ve stejnou dobu z různých entit, které může mít dopad na sobě navzájem, služba EventStore je možné připojit tyto události, které usnadní identifikaci příčiny aktivity v clusteru. Například pokud jeden z vašich aplikací se stane se bez změny vyvolané není v pořádku, EventStore se taky podívejte se na další události vystavené platformy a to s může korelovat `NodeDown` událostí. To pomáhá s rychlejší detekce chyb a hlavní příčiny analýzy.

Abyste mohli začít používat službu EventStore, najdete v části [dotazu EventStore rozhraní API pro události clusteru](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Další postup
* Přehled monitorování a Diagnostika v Service Fabric - [monitorovací a diagnostické pro Service Fabric](service-fabric-diagnostics-overview.md)
* Další informace o monitorování cluster- [monitorování clusteru a platforma](service-fabric-diagnostics-event-generation-infra.md).