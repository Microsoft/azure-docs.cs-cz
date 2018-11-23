---
title: Azure Service Fabric události Store | Dokumentace Microsoftu
description: Další informace o Azure Service Fabric Eventstoru
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: cd58e24a51b153d6bf217f7d32a82e882183ed73
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290667"
---
# <a name="eventstore-service-overview"></a>Přehled služby Eventstoru

## <a name="overview"></a>Přehled

Počínaje verze 6.2, je služba Eventstoru možnosti monitorování v Service Fabric, která poskytuje způsob, jak můžete porozumět stavu clusteru nebo úloh v daném bodě v čase. Služba Eventstoru zpřístupňuje události Service Fabric prostřednictvím sady rozhraní API (přístupná prostřednictvím koncové body REST nebo Klientská knihovna). Tato rozhraní API Eventstoru umožňují dotazů clusteru přímo k získání diagnostických dat na entitu ve vašem clusteru a by měla sloužit ke:

* Diagnostikujte problémy ve vývoj a testování, nebo kde může pomocí monitorování kanálu
* Potvrďte, že akce správy, které je možné ve vašem clusteru jsou zpracovávány správně clusteru

Pokud chcete zobrazit úplný seznam událostí, které jsou k dispozici v Eventstoru, naleznete v tématu [události Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>Od verze Service Fabric verze 6.2. rozhraní API Eventstoru jsou aktuálně ve verzi preview pro clustery Windows pouze běžící v Azure. Pracujeme na přenos tuto funkci pro Linux, stejně jako naše samostatné clustery.

Pro události, které jsou k dispozici pro každou entitu a typ entity ve vašem clusteru může být dotazována Eventstoru služby. To znamená, že můžete zadat dotaz na události v případě následujících stupňů;
* Cluster: všechny clusteru událostí na úrovni
* Uzly: všechny uzel úroveň události
* Uzlu: specifické pro jeden uzel, na základě událostí `nodeName`
* Aplikace: všech událostí na úrovni aplikace
* Aplikace: specifické pro jednu aplikaci události
* Služby: události ze všech služeb ve vašich clusterů
* Služby: události z konkrétní služby
* Oddíly: události ze všech oddílů
* Oddíl: události z konkrétního oddílu
* Repliky: události ze všech replik nebo instancí
* Replika: události z konkrétní repliky / instance


Služba Eventstoru má také možnost ke korelaci událostí ve vašem clusteru. Zobrazením událostí, které byly vytvořeny ve stejnou dobu od různé entity, které může mít vliv na sobě navzájem, služba Eventstoru je moci připojit k těmto událostem a pomůže identifikovat příčiny pro aktivity ve vašem clusteru. Například pokud nastane jednou z vašich aplikací bez nutnosti jakkoli měnit vyvolané špatného, Eventstoru se také podívat na další události vystavené platformy a to s může korelovat `NodeDown` událostí. To pomáhá při rychlejší detekce chyb a hlavní příčiny potíží analýzy.

Doporučujeme pomocí Eventstoru pro rychlou analýzu a získat snímek představu, jak provozování clusteru a pokud se dějí věci jako očekává.

Abyste mohli začít s používáním služby Eventstoru, naleznete v tématu [rozhraní API Eventstoru dotazu pro události clusteru](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Další postup
* Přehled monitorování a diagnostiky v Service Fabric – [monitorování a Diagnostika pro Service Fabric](service-fabric-diagnostics-overview.md)
* Další informace o monitorování clusteru- [monitorování clusteru a platforma](service-fabric-diagnostics-event-generation-infra.md).