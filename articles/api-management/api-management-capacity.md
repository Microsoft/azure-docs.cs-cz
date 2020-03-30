---
title: Kapacita instance Azure API Management | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, co je metrika kapacity a jak činit informovaná rozhodnutí, zda škálovat instanci Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.custom: fasttrack-edit
ms.openlocfilehash: b6d949b50be348e72cedfc3710383308b04de106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336011"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Kapacita instance služby Azure API Management

**Kapacita** je nejdůležitější [metrika Azure Monitor](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) u provádění informovaných rozhodnutí, zda škálovat instanci správy rozhraní API tak, aby vyhovovala většímu zatížení. Jeho konstrukce je složitá a ukládá určité chování.

Tento článek vysvětluje, co je **kapacita** a jak se chová. Ukazuje, jak získat přístup k metrikám **kapacity** na webu Azure Portal a navrhuje, kdy zvážit škálování nebo upgrade instance správy rozhraní API.

> [!IMPORTANT]
> Tento článek popisuje, jak můžete sledovat a škálovat instanci Azure API Management na základě metriky kapacity. Je však stejně důležité pochopit, co se stane, když jednotlivé instance správy rozhraní API skutečně *dosáhla* své kapacity. Azure API Management nebude používat žádné omezení na úrovni služby, aby se zabránilo fyzické přetížení instancí. Když instance dosáhne své fyzické kapacity, bude se chovat podobně jako jakýkoli přetížený webový server, který není schopen zpracovat příchozí požadavky: latence se zvýší, připojení se vypustí, dojde k chybám časového rozlišení atd. To znamená, že klienti rozhraní API by měli být připraveni řešit tuto možnost podobně jako u jiných externích služeb (např. použitím zásad opakování).

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle kroků z tohoto článku, musíte mít:

+ Aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Instance APIM. Další informace najdete [v tématu Vytvoření instance Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Co je kapacita

![Metrika kapacity](./media/api-management-capacity/capacity-ingredients.png)

**Kapacita** je indikátor zatížení instance správy rozhraní API. Odráží využití prostředků (CPU, paměť) a délky fronty sítě. Využití procesoru a paměti odhaluje spotřebu prostředků:

+ Služby roviny dat správy rozhraní API, jako je například zpracování požadavků, které mohou zahrnovat předávání požadavků nebo spuštění zásad.
+ Služby rozhraní API Management Management Plane, jako jsou akce správy použité prostřednictvím portálu Azure Portal nebo ARM, nebo zatížení pocházející z [portálu pro vývojáře](api-management-howto-developer-portal.md).
+ Vybrané procesy operačního systému, včetně procesů, které zahrnují náklady na handshake s tls na nových připojeních.

Celková **kapacita** je průměr vlastních hodnot z každé jednotky instance správy rozhraní API.

Přestože **metrika kapacity** je navržena tak, aby odhalila problémy s instancí Správy rozhraní API, existují případy, kdy se problémy neprojeví ve změnách **metriky kapacity**.

## <a name="capacity-metric-behavior"></a>Chování metriky kapacity

Vzhledem k jeho konstrukci může být v reálném životě **kapacita** ovlivněna mnoha proměnnými, například:

+ vzory připojení (nové připojení na požadavek vs opětovné použití existujícího připojení)
+ velikost žádosti a odpovědi
+ zásady nakonfigurované v každém rozhraní API nebo počet klientů odesílajících požadavky.

Čím složitější jsou operace na požadavky, tím vyšší bude spotřeba **kapacity.** Například komplexní zásady transformace spotřebovávají mnohem více procesoru než jednoduché předávání požadavků. Pomalé back-endové odpovědi služby zvýší také.

> [!IMPORTANT]
> **Kapacita** není přímým měřítkem počtu zpracovávaných požadavků.

![Špičky metrik kapacity](./media/api-management-capacity/capacity-spikes.png)

**Kapacita** může také špička přerušovaně nebo větší než nula i v případě, že neexistují žádné požadavky jsou zpracovávány. Stává se to z důvodu akce specifické pro systém nebo platformu a neměly by být brány v úvahu při rozhodování, zda škálovat instance.

Metrika nízké **kapacity** nemusí nutně znamenat, že vaše instance správy rozhraní API nedochází k žádným problémům.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Využití portálu Azure ke kontrole kapacity
  
![Metrika kapacity](./media/api-management-capacity/capacity-metric.png)  

1. Přejděte na instanci APIM na [webu Azure Portal](https://portal.azure.com/).
2. Vyberte **Metriky**.
3. V části fialová vyberte **metriku Kapacita** z dostupných metrik a ponechte výchozí agregaci **Avg.**

    > [!TIP]
    > Vždy byste se měli podívat na rozdělení **metriky kapacity** podle lokality, abyste se vyhnuli nesprávným interpretacím.

4. V zelené části vyberte **Umístění** pro rozdělení metriky podle dimenze.
5. Z horního pruhu oddílu vyberte požadovaný časový rámec.

    Můžete nastavit upozornění na metriku, abyste věděli, kdy se děje něco neočekávaného. Například dostávat oznámení, když vaše instance APIM byla vyšší než jeho očekávané maximální kapacity pro více než 20 minut.

    >[!TIP]
    > Můžete nakonfigurovat výstrahy, abyste věděli, kdy je vaše služba nedostatek kapacity, nebo pomocí funkce automatického škálování Azure Monitoru k automatickému přidání jednotky správy rozhraní API Azure. Škálování operace může trvat přibližně 30 minut, takže byste měli naplánovat pravidla odpovídajícím způsobem.  
    > Je povoleno pouze škálování hlavního umístění.

## <a name="use-capacity-for-scaling-decisions"></a>Využití kapacity pro rozhodování o změně měřítka

**Kapacita** je metrika pro rozhodování o tom, zda škálovat instanci správy rozhraní API tak, aby vyhovovala většímu zatížení. Rozmyslete si:

+ Podíváme-li se na dlouhodobý trend a průměr.
+ Ignorování náhlé špičky, které s největší pravděpodobností nesouvisí s žádným zvýšením zatížení (viz "Metrika kapacity chování" pro vysvětlení).
+ Upgrade nebo škálování instance, když hodnota **kapacity**překročí 60 % nebo 70 % po delší časové období (například 30 minut). Různé hodnoty mohou fungovat lépe pro vaši službu nebo scénář.

>[!TIP]  
> Pokud jste schopni odhadnout provoz předem, otestujte instanci APIM na úlohy, které očekáváte. Můžete zvýšit zatížení požadavku na vašeho klienta postupně a sledovat, jaká hodnota metriky kapacity odpovídá zatížení ve špičce. Podle pokynů z předchozí části pomocí portálu Azure, abyste pochopili, kolik kapacity se v daném okamžiku používá.

## <a name="next-steps"></a>Další kroky

[Jak škálovat nebo upgradovat instanci služby Azure API Management](upgrade-and-scale.md)