---
title: Události Service Fabric Azure
description: Přečtěte si informace o Service Fabricch událostech, které jsou uvedené v poli, které vám pomůžou monitorovat cluster Azure Service Fabric.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 581bb3f16941fce660e0f464ed8ea9389b034b12
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628801"
---
# <a name="service-fabric-events"></a>Události služby Service Fabric 

Platforma Service Fabric zapisuje několik strukturovaných událostí pro klíčové provozní aktivity, které probíhají v rámci vašeho clusteru. Tento rozsah z upgrady clusteru na rozhodování o umístění repliky. Každá událost, která Service Fabric zpřístupňuje mapování jedné z následujících entit v clusteru:
* Cluster
* Aplikace
* Služba
* Oddíl
* Replika 
* Kontejner

Pokud chcete zobrazit úplný seznam událostí, které jsou vystavené platformou a [seznamem událostí Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

Tady je několik příkladů scénářů, ve kterých byste měli zobrazit události pro cluster. 
* Události životního cyklu uzlu: vzhledem k tomu, že se uzly dostanou, můžete je navýšit nebo škálovat, restartovat a aktivovat/deaktivovat. Tyto události budou vystaveny, což vám ukáže, co se stalo, a pomůže vám zjistit, jestli došlo k nějakému problému, nebo pokud se k úpravě stavu uzlu použilo rozhraní API, které bylo voláno prostřednictvím SF.
* Upgrade clusteru: při upgradu clusteru (SF verze nebo změna konfigurace) se zobrazí zahájení upgradu, Projděte si všechny vaše domény upgradu a dokončete (nebo vraťte zpět). 
* Upgrady aplikací: stejně jako u upgradů clusteru existuje komplexní sada událostí, na které se aktualizace provádí. Tyto události mohou být užitečné při plánování upgradu, aktuálního stavu upgradu a celkové posloupnosti událostí. To je užitečné v případě, že se chcete podívat, jaké upgrady byly úspěšně vyvolány, nebo zda bylo vrácení zpět aktivováno.
* Nasazení/odstranění aplikace/služby: pro každou aplikaci, službu a kontejner jsou k dispozici události, které se vytvářejí nebo odstraňují a jsou užitečné při škálování nebo oddálení, např. zvýšení počtu replik.
* Přesunutí oddílu (Změna konfigurace): pokaždé, když se stavový oddíl provede změnou konfigurace (změna v sadě replik), událost se zaprotokoluje. To je užitečné v případě, že se snažíte pochopit, jak často se sada replik oddílů mění nebo převezme služby při selhání, nebo sledovat, na kterých uzlech se v jakémkoli okamžiku spouští vaše primární replika.
* Události chaos: při použití služby [chaos](service-fabric-controlled-chaos.md) v Service Fabric se zobrazí události při každém spuštění nebo zastavení služby nebo když dojde k vložení chyby v systému.
* Události stavu: Service Fabric zveřejňuje události stavu pokaždé, když se vytvoří upozornění nebo zpráva o stavu chyby, nebo se entita vrátí do stavu OK nebo vyprší platnost sestavy stavu. Tyto události jsou velmi užitečné ke sledování historických statistik stavu pro entitu. 

## <a name="how-to-access-events"></a>Přístup k událostem

Existuje několik různých způsobů, kterými se Service Fabric k událostem, ke kterým lze přihlédnout:
* Události se protokolují prostřednictvím standardních kanálů, jako jsou ETW/protokoly událostí Windows, a dají se vizuálně vymezit libovolným monitorovacím nástrojem, který podporuje, jako jsou protokoly Azure Monitor. Ve výchozím nastavení mají clustery vytvořené na portálu zapnutou diagnostiku a mají agenta Windows Azure Diagnostics odesílající události do úložiště tabulek Azure, ale přesto je budete muset integrovat s vaším prostředkem Log Analytics. Přečtěte si další informace o konfiguraci [agenta Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) pro úpravu konfigurace diagnostiky clusteru, aby bylo možné vybrat další protokoly nebo čítače výkonu a [Azure monitor protokoly integrace](service-fabric-diagnostics-event-analysis-oms.md) .
* Rozhraní REST API služby Eventstoru, která umožňují dotazovat se na cluster přímo nebo prostřednictvím klientské knihovny Service Fabric. Události clusteru najdete v tématu věnovaném [rozhraním API pro Eventstoru dotazů](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Další kroky
* Další informace o monitorování clusteru – [monitorování clusteru a platformy](service-fabric-diagnostics-event-generation-infra.md)
* Další informace o službě Eventstoru Service – [Přehled služby eventstoru](service-fabric-diagnostics-eventstore.md)
