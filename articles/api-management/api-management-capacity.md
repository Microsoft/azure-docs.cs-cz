---
title: Kapacita instance služby Azure API Management | Microsoft Docs
description: V tomto článku se dozvíte, co je metrika kapacity a jak dělat informovaná rozhodnutí o tom, jestli se má škálovat instance služby Azure API Management.
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
ms.openlocfilehash: f4eb90cc0e8867230c7fb1a2a169b5ba0126e46a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86205890"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Kapacita instance služby Azure API Management

**Kapacita** je nejdůležitější [Azure monitor metrika](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) pro rozhodování o tom, jestli se má škálovat API Management instance, aby se vešlo více zátěže. Jeho konstrukce je složitá a ukládá určité chování.

Tento článek vysvětluje, co je **kapacita** a jak se chová. Ukazuje, jak získat přístup k metrikám **kapacity** v Azure Portal a navrhuje, kdy zvažte škálování nebo upgrade instance API Management.

> [!IMPORTANT]
> Tento článek popisuje, jak můžete na základě metriky kapacity monitorovat a škálovat svou instanci Azure API Management. Je ale stejně důležité pochopit, co se stane, když konkrétní instance API Management skutečně *dosáhla* své kapacity. Azure API Management nepoužije žádné omezování na úrovni služby, aby se zabránilo fyzickému přetížení instancí. Když instance dosáhne své fyzické kapacity, bude se chovat podobně jako jakýkoli přetížený webový server, který nedokáže zpracovat příchozí požadavky: latence se zvýší, připojení se zahozena a dojde k chybám časového limitu atd. To znamená, že klienti rozhraní API by měli být připravení pracovat s touto možností podobně jako u jakékoli jiné externí služby (např. použitím zásad opakování).

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle kroků v tomto článku, musíte mít:

+ Musíte mít aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Instance APIM Další informace najdete v tématu [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Co je kapacita

![Metrika kapacity](./media/api-management-capacity/capacity-ingredients.png)

**Kapacita** je indikátorem zatížení instance API Management. Odráží využití prostředků (CPU, paměť) a délky fronty sítě. Využití CPU a paměti odhalí spotřebu prostředků pomocí:

+ API Management služby datové roviny, jako je například zpracování požadavků, které můžou zahrnovat předávací požadavky nebo spustit zásadu.
+ API Management služby roviny správy, jako jsou akce správy použité prostřednictvím webu Azure Portal nebo ARM, nebo načtení z [portálu pro vývojáře](api-management-howto-developer-portal.md).
+ Vybrané procesy operačního systému včetně procesů, které zahrnují náklady na handshake TLS u nových připojení.

Celková **kapacita** je průměrem vlastních hodnot z každé jednotky instance API Management.

I když je **metrika kapacity** navržená tak, aby surfoval problémy s instancí API Management, existují případy, kdy se problémy neprojeví ve změnách **metriky kapacity**.

## <a name="capacity-metric-behavior"></a>Chování metriky kapacity

Z důvodu jeho konstrukce může být v reálné **kapacitě** ovlivněno mnoha proměnnými, například:

+ vzorce připojení (nové připojení k žádosti a opětovné použití existujícího připojení)
+ velikost žádosti a odpovědi
+ zásady nakonfigurované pro každé rozhraní API nebo počet klientů, kteří odesílají požadavky.

Složitější operace s požadavky jsou, čím vyšší bude spotřeba **kapacity** . Komplexní zásady transformace například využívají mnohem více PROCESORů než jednoduché předávání požadavků. Pomalé odpovědi služby back-endu se zvýší.

> [!IMPORTANT]
> **Kapacita** není přímá míra počtu zpracovávaných požadavků.

![Špičky metrik kapacity](./media/api-management-capacity/capacity-spikes.png)

**Kapacita** může být také přerušovaná nebo je větší než nula, i když nejsou zpracovávány žádné požadavky. Důvodem je, že se jedná o akce specifické pro systém nebo platformu a nemělo by se vzít v úvahu při rozhodování, zda chcete škálovat instanci.

**Metrika s nízkou kapacitou** nemusí nutně znamenat, že vaše instance API Management nemá žádné problémy.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Použití webu Azure Portal k prohlédnutí kapacity
  
![Metrika kapacity](./media/api-management-capacity/capacity-metric.png)  

1. V [Azure Portal](https://portal.azure.com/)přejděte na svou instanci APIM.
2. Vyberte **Metriky**.
3. V části fialová vyberte metrika **kapacity** z dostupných metrik a nechte výchozí **průměrnou** agregaci.

    > [!TIP]
    > Vždy byste se měli podívat na rozpis metrik **kapacity** na umístění, abyste se vyhnuli špatným interpretům.

4. V zelené části vyberte **umístění** pro rozdělení metriky podle dimenze.
5. Z horního panelu oddílu vyberte požadovaný časový rámec.

    Můžete nastavit výstrahu metriky, abyste měli jistotu, že se děje něco neočekávaného. Můžete například dostávat oznámení, když vaše instance APIM překročila očekávanou kapacitu špičky na více než 20 minut.

    >[!TIP]
    > Můžete nakonfigurovat výstrahy, které vám pomůžou zjistit, kdy vaše služba má nedostatečnou kapacitu, nebo použít funkci Azure Monitor automatického škálování, která automaticky přidá jednotku Azure API Management. Operace škálování může trvat přibližně 30 minut, takže pravidla byste měli naplánovat odpovídajícím způsobem.  
    > Je povoleno pouze škálování hlavního umístění.

## <a name="use-capacity-for-scaling-decisions"></a>Využijte kapacitu pro rozhodování o škálování

**Kapacita** je metrika pro rozhodování o tom, jestli se má škálovat instance API Management, aby se vešlo více zátěže. Rozmyslete si:

+ Podívejte se na dlouhodobý trend a průměr.
+ Ignorují se náhlé špičky, které s největší pravděpodobně nesouvisejí se zvýšením zátěže (vysvětlení najdete v části "chování metriky kapacity").
+ Upgrade nebo škálování instance, pokud hodnota **kapacity**přesáhne 60% nebo 70% po delší dobu (například 30 minut). Pro vaši službu nebo scénář můžou fungovat lepší i jiné hodnoty.

>[!TIP]  
> Pokud máte v úmyslu odhadnout svůj provoz předem, otestujte instanci APIM na úlohách, které očekáváte. Zatížení žádosti v tenantovi můžete postupně zvyšovat a monitorovat, jakou hodnotu metriky kapacity odpovídají zatížení vaší špičky. Postupujte podle kroků v předchozí části a použijte Azure Portal k pochopení, kolik kapacity se v daném okamžiku používá.

## <a name="next-steps"></a>Další kroky

- [Jak škálovat nebo upgradovat instanci služby Azure API Management](upgrade-and-scale.md)
- [Optimalizace a ukládání na cloudové útratě](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)