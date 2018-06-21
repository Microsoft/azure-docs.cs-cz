---
title: Kapacity instance Azure API Management | Microsoft Docs
description: Tento článek vysvětluje, co je metrika kapacitu a jak provádět informovaně rozhodnout, zda se má velikost instance služby Azure API Management.
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
ms.openlocfilehash: 4983854a14a6efe9214692dc677dedeada73933b
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296103"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Kapacita instance služby Azure API Management

**Kapacita** jeden, nejdůležitější [Azure monitorování metrika](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) pro přijímání informované rozhodnutí, zda se má velikost instance služby API Management, aby dokázala pojmout větší zatížení. Konstrukce je složitý a ukládá určité chování.

Tento článek vysvětluje, co **kapacity** je a jak se chová. Ukazuje, jak pro přístup k **kapacity** metriky na portálu Azure a navrhne, kdy se mají vzít v úvahu škálování nebo upgradu vaší instance služby API Management.

## <a name="prerequisites"></a>Požadavky

Chcete-li postupujte podle kroků v tomto článku, budete potřebovat:

+ Aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM instance. Další informace najdete v tématu [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).

## <a name="what-is-capacity"></a>Jaká je kapacita

![Metrika kapacity](./media/api-management-capacity/capacity-ingredients.png)

**Kapacita** zatížení v instanci APIM slouží jako ukazatel. Zobrazí využití prostředků (procesor, paměť) a délky fronty sítě. Využití procesoru a paměti zjistí spotřeba prostředků:

+ APIM služby, jako je akce nebo žádostí o správu zpracování, který může obsahovat přesměrováním požadavků nebo spuštění zásad
+ Vybrat procesy operačního systému, včetně procesy, které zahrnují náklady na metodou handshake SSL na nová připojení.

Celkový počet **kapacity** je průměrem z každé jednotky instance služby API Management své vlastní hodnoty.

## <a name="capacity-metric-behavior"></a>Kapacity metriky chování

Z důvodu jeho vytváření v reálného života **kapacity** může být ovlivněn mnoha proměnných, například:

+ vzory připojení (nové připojení na žádost vs, opětovné použití existující připojení)
+ velikost požadavku a odpovědi
+ zásady nakonfigurované na každé rozhraní API nebo počet klientů odesílání požadavků.

Jsou složitější operace na žádosti, tím vyšší **kapacity** bude spotřeba. Například zásad komplexní transformace využívat mnohem víc procesoru než jednoduché žádost o přesměrování. Pomalé back-end služby odpovědí zvýší jeho příliš.

> [!IMPORTANT]
> **Kapacita** není přímé míru počet zpracovávaných požadavků.

![Kapacity metriky špičky](./media/api-management-capacity/capacity-spikes.png)

**Kapacita** můžete také občas špiček nebo být větší než nula, i když nejsou žádné zpracovávaných požadavků. Ho dochází z důvodu systému nebo platformy specifické akce a by neměly být brány v úvahu při rozhodování, zda se škálovat instance.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Zkontrolujte kapacitu pomocí portálu Azure
  
![Metrika kapacity](./media/api-management-capacity/capacity-metric.png)  

1. Přejděte k vaší instanci APIM v [portál Azure](https://portal.azure.com/).
2. Vyberte **metriky (preview)**.
3. Z části fialové vyberte **kapacity** metriky z k dostupným metrikám a ponechejte výchozí **průměr** agregace.

    > [!TIP]
    > Vždy měli podívat **kapacity** metriky rozpis podle umístění, aby se zabránilo chybě interpretace.

4. Z části zelená vyberte **umístění** k rozdělení metriku dimenzí.
5. Vyberte požadovanou časový rámec z horním panelu oddílu.

    Můžete nastavit upozornění pokaždé, když se něco neočekávaného děje na metriky. Například dostávat oznámení při instanci APIM byl exceededing své očekávané maximální kapacity pro více než 20 minut.

    >[!TIP]
    > Můžete nakonfigurovat výstrahy, které umožňují vědět, pokud se vaše služba má nedostatek kapacity nebo funkci Automatické škálování Azure monitorování automaticky přidáte jednotku Azure API Management. Škálování operace může trvat přibližně 30 minut, takže byste měli naplánovat na odpovídajícím způsobem pravidel.  
    > Je povolen pouze škálování hlavního umístění.

## <a name="use-capacity-for-scaling-decisions"></a>Použít kapacity pro škálování rozhodnutí

**Kapacita** je metrika pro přijímání rozhodnutí, zda se má velikost instance služby API Management, aby dokázala pojmout větší zatížení. Vezměte v úvahu:

+ Prohlížení dlouhodobé trendu a průměr.
+ Ignoruje nečekané špičky, které budou pravděpodobně není související s každé zvýšení zatížení (viz část "Chování metriky kapacity" vysvětlení).
+ Upgrade nebo škálování vaší instance při **kapacity**je hodnota přesahuje 60 % nebo 70 % delší dobu (například 30 minut). Jiné hodnoty může fungovat lépe pro službu nebo scénář.

>[!TIP]  
> Pokud budete moci Odhad provozu předem, testovací instanci APIM úloh očekáváte. Můžete zvýšit zatížení na vašeho klienta postupně a sledovat, jaká hodnota metriky kapacity odpovídá zatížení ve špičce. Postupujte podle kroků z předchozí části, abyste pochopili, jakou kapacitu se používá v každém okamžiku používat portál Azure.

## <a name="next-steps"></a>Další postup

[Postup škálování nebo upgradujte instanci služby Azure API Management](upgrade-and-scale.md)