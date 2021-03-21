---
title: Monitorování metrik pro Azure front-end Standard/Premium
description: Tento článek popisuje metriky monitorování služby Azure front-Premium Standard/Premium.
services: frontdoor
author: duau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: bb10fb337972db2696960b530f2d7538bd36a2fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101098848"
---
# <a name="real-time-monitoring-in-azure-front-door-standardpremium"></a>Sledování v reálném čase ve frontě Azure na úrovni Standard/Premium

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

Přední dveře Azure (Standard/Premium) jsou integrované s Azure Monitor a mají 11 metrik, které vám pomůžou monitorovat, řešit a řešit problémy v reálném čase přes službu Azure front-Time Standard/Premium.  

Přední jednotky Azure front-Premium Standard/Premium a odesílají své metriky v intervalech 60 – sekund. Zobrazení metrik na portálu může trvat až 3 minuty. Metriky se dají zobrazit v grafech nebo v mřížce podle vašeho výběru a jsou dostupné přes portál, PowerShell, CLI a rozhraní API. Další informace najdete v tématu [Azure monitor metriky](../../azure-monitor/platform/data-platform-metrics.md).  

Výchozí metriky jsou zdarma. Další metriky můžete povolit pro další náklady. 

U každé metriky můžete nakonfigurovat výstrahy, jako je například prahová hodnota pro 4XXErrorRate nebo 5XXErrorRate. Pokud míra chyb překračuje prahovou hodnotu, aktivuje se výstraha jako nakonfigurovaná. Další informace najdete v tématu [Vytvoření, zobrazení a správa výstrah metrik pomocí Azure monitor](../../azure-monitor/platform/alerts-metric.md). 

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="metrics-supported-in-azure-front-door-standardpremium"></a>Metriky podporované v frontě Azure na úrovni Standard/Premium

| Metriky  | Description | Dimenze |
| ------------- | ------------- | ------------- |
| Poměr přístupů do bajtů | Procentuální hodnota výstupu z AFD cache vypočtená na celkový výstup. </br> **Poměr přístupů bajtů** = (výstup z Edge-výstup od počátku)/Egress z Edge. </br> **Počet vyloučených scénářů v bajtech poměru přístupů**:</br> 1. explicitně nakonfigurujete žádnou mezipaměť buď prostřednictvím stroje pravidel, nebo v chování v případě ukládání řetězce dotazu do mezipaměti. </br> 2. explicitně nakonfigurujte direktivu řízení mezipaměti s úložištěm bez uložení nebo soukromou mezipamětí. </br>3. bajt počtu přístupů může být nízký, pokud je většina přenosů předávána do zdroje, nikoli z ukládání do mezipaměti na základě vašich konfigurací nebo scénářů. | Koncový bod |
| RequestCount | Počet požadavků klientů poskytovaných službou CDN. | Koncový bod, země klienta, oblast klienta, stav HTTP, skupina stavů HTTP |
| ResponseSize | Počet požadavků klientů poskytovaných službou AFD. |Koncový bod, země klienta, oblast klienta, stav HTTP, skupina stavů HTTP |
| TotalLatency | Celková doba od žádosti klienta přijatá službou CDN, **dokud se nepošle poslední bajt odpovědi z CDN klientovi**. |Koncový bod, země klienta, oblast klienta, stav HTTP, skupina stavů HTTP |
| RequestSize | Počet bajtů odeslaných jako požadavek od klientů do AFD. | Koncový bod, země klienta, oblast klienta, stav HTTP, skupina stavů HTTP |
| 4XX% ErrorRate | Procento všech požadavků klienta, pro které je kód stavu odpovědi 4XX. | Koncový bod, země klienta, oblast klienta |
| 5XX% ErrorRate | Procento všech požadavků klienta, pro které je kód stavu odpovědi 5XX. | Koncový bod, země klienta, oblast klienta |
| OriginRequestCount  | Počet požadavků odeslaných z AFDu na počátek | Koncový bod, počátek, stav HTTP, skupina stavů HTTP |
| OriginLatency | Čas vypočítaný z doby, kdy byl požadavek odeslán přes AFD Edge do back-endu, dokud AFD nepřijal poslední bajt odpovědi z back-endu. | Koncový bod, počátek |
| OriginHealth% | Procento úspěšných sond stavu z AFD do počátku.| Původ, skupina původu |
| Počet požadavků WAF | Odpovídající WAF požadavek. | Akce, název pravidla, název zásady |

## <a name="access-metrics-in-azure-portal"></a>Přístup k metrikám v Azure Portal

1. V nabídce Azure Portal vyberte **všechny prostředky**  >>  **\<your-AFD Standard/Premium (Preview) -profile>** .

2. V části **monitorování** vyberte **metriky**:

3. V části **metriky** vyberte metriku, kterou chcete přidat:

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-1.png" alt-text="Snímek stránky s metrikami" lightbox="../media/how-to-monitoring-metrics/front-door-metrics-1-expanded.png":::

4. Vyberte **Přidat filtr** a přidejte filtr:

    :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-2.png" alt-text="Snímek obrazovky s přidáním filtrů do metrik" lightbox="../media/how-to-monitoring-metrics/front-door-metrics-2-expanded.png":::
    
5. Pokud chcete rozdělit data podle různých dimenzí, vyberte **použít rozdělení** :

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-4.png" alt-text="Snímek obrazovky s přidáním dimenzí do metrik" lightbox="../media/how-to-monitoring-metrics/front-door-metrics-4-expanded.png":::

6. Vyberte **Nový graf** a přidejte nový graf:

## <a name="configure-alerts-in-azure-portal"></a>Konfigurace výstrah v Azure Portal

1. Nastavte výstrahy na Azure front-up Standard/Premium (Preview) tak, že vyberete  >>  **výstrahy** monitorování.

1. Vyberte **nové pravidlo výstrahy** pro metriky uvedené v části metriky.

Výstraha se bude účtovat na základě Azure Monitor. Další informace o výstrahách naleznete v tématu [Azure monitor Alerts](../../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [sestavách Standard a Premium pro Azure front](how-to-reports.md).
- Přečtěte si o [protokolech Standard a Premium pro Azure front-end](how-to-logs.md).
