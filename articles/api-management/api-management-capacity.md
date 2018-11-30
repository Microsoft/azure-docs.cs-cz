---
title: Kapacita instance Azure API managementu | Dokumentace Microsoftu
description: Tento článek vysvětluje, co je metriku kapacity a jak se informovaně rozhodnout, jestli se má změnit velikost instance Azure API Management.
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
ms.openlocfilehash: 31959cc1bef6b6434f6d3f586052a845837aa438
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442587"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Kapacita instance Azure API Management

**Kapacita** je jednoduchá nejdůležitější [metrik Azure monitoru](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) umožňující informovaná rozhodnutí, jestli se má škálování instance služby API Management tak, aby vyhovovaly větší zatížení. Konstrukce je složité a ukládá určité chování.

Tento článek vysvětluje, co **kapacity** je a jak se chová. Ukazuje, jak přistupovat k **kapacity** metrik na webu Azure Portal a navrhne, při které byste měli zvážit vertikální nebo upgradu vaší instance služby API Management.

## <a name="prerequisites"></a>Požadavky

Chcete-li postupujte podle kroků v tomto článku, budete potřebovat:

+ Aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Instanci služby APIM. Další informace najdete v tématu [vytvoření instance Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Co je to kapacita

![Metrika kapacity](./media/api-management-capacity/capacity-ingredients.png)

**Kapacita** je indikátor zatížení na instanci služby APIM. Odráží využití prostředků (procesor, paměť) a délky front sítě. Využití procesoru a paměti odhalí spotřeba prostředků:

+ Služby APIM, jako je akce nebo abychom si vyžádali správu zpracování, který může obsahovat předává požadavky nebo spuštění zásady
+ Vybrat procesy operačního systému, včetně procesů, které zahrnují náklady na počet metod handshake SSL u nového připojení.

Celkový počet **kapacity** hodnota průměrem těchto vlastních hodnot z každé jednotky instance služby API Management.

## <a name="capacity-metric-behavior"></a>Kapacitní metriky chování

Z důvodu jeho vytváření v reálném životě **kapacity** mohou ovlivňovat mnoho proměnných, například:

+ modely připojení (nové připojení na žádost o vs opětovné použití existujícího připojení)
+ velikost požadavku a odpovědi
+ zásady nakonfigurované na každé rozhraní API nebo v některých klientech odesílání požadavků.

Složitějších operací na požadavky jsou, tím vyšší **kapacity** za spotřebu. Například zásady komplexní transformace využívat mnohem více procesorů než jednoduchý požadavek přesměrování. Pomalá back-end služba odpovědi zvýší jeho příliš.

> [!IMPORTANT]
> **Kapacita** není s přímým přístupem měřítko počet zpracovávaných požadavků.

![Kapacitní metriky špičky](./media/api-management-capacity/capacity-spikes.png)

**Kapacita** můžete také vystoupat přerušovaně nebo být větší než nula, i v případě, že neexistují žádné požadavky na zpracování. Dochází z důvodu akcí systému nebo platformu a by se neměl brát v úvahu při rozhodování, jestli se má změnit velikost instance.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Zkontrolujte kapacitu pomocí webu Azure Portal
  
![Metrika kapacity](./media/api-management-capacity/capacity-metric.png)  

1. Přejděte k vaší instanci APIM v [webu Azure portal](https://portal.azure.com/).
2. Vyberte **metriky (preview)**.
3. Z části fialové vyberte **kapacity** metriky z dostupných metrik a nechte výchozí **Avg** agregace.

    > [!TIP]
    > Vždy byste se podívat na **kapacity** metriky rozpis na umístění, aby se zabránilo chybě interpretace.

4. Vyberte z části zelené **umístění** pro rozdělení metriku podle dimenzí.
5. Můžete si vyberte požadovaný časový rámec z panelu horní části.

    Můžete nastavit upozornění na metriku dali vám vědět, kdy se děje něco neočekávaného. Třeba získáte oznámení po překročil vaší instanci APIM jeho očekávané maximální vytížení kapacitu pro více než 20 minut.

    >[!TIP]
    > Můžete nakonfigurovat výstrahy, které vám umožní vědět, že vaše služba není dostatek kapacity, nebo pomocí funkce automatického škálování Azure Monitor automaticky přidat jednotku Azure API Management. Škálování operace může trvat přibližně 30 minut, takže byste měli odpovídajícím způsobem naplánovat pravidla.  
    > Je povolen pouze škálování hlavního umístění.

## <a name="use-capacity-for-scaling-decisions"></a>Použitá kapacita pro rozhodnutí o škálování

**Kapacita** je metrika pro rozhodování, jestli se má škálování instance služby API Management tak, aby vyhovovaly větší zatížení. Vezměte v úvahu:

+ Prohlížení dlouhodobé trendů a průměr.
+ Ignorování nečekaným špičkám, které budou pravděpodobně nesouvisí žádné nárůst zatížení (viz oddíl "Chování metrik kapacity" vysvětlení).
+ Upgrade nebo změna velikosti instance, když **kapacity**hodnota překročí 60 % nebo 70 % po dobu delší čas (například 30 minut). Jiné hodnoty může fungují lépe, služby nebo scénáři.

>[!TIP]  
> Pokud budete moct předem odhadnout provozu, otestujte vaší instanci APIM na úlohy, které jste očekávali. Můžete zvýšit zatížení ve svém tenantovi postupně a sledovat, jaká hodnota metriky kapacity odpovídá zatížení ve špičce. Postupujte podle kroků v předchozí části, chcete-li pochopit, jakou kapacitu se používá v daném okamžiku pomocí webu Azure portal.

## <a name="next-steps"></a>Další postup

[Jak škálovat nebo upgradujte instanci služby Azure API Management](upgrade-and-scale.md)