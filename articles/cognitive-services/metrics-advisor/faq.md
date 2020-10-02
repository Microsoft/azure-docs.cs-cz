---
title: Nejčastější dotazy k metrickým nástrojům
titleSuffix: Azure Cognitive Services
description: Nejčastější dotazy týkající se služby Advisor metrik.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: aahi
ms.openlocfilehash: e4a75bdd6147ee2189660c37062c5bec9d55d512
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631731"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>Nejčastější dotazy k metrickým nástrojům

### <a name="what-is-the-cost-of-my-instance"></a>Jaké jsou náklady na moji instanci?

V současné době není za použití vaší instance ve verzi Preview žádné náklady.

### <a name="why-is-the-demo-website-readonly"></a>Proč je ukázkový web jen pro čtení?

[Ukázkový web](https://anomaly-detector.azurewebsites.net/) je veřejně dostupný. Tato instance je určená jen pro čtení, aby nedocházelo k nechtěnému nahrávání dat.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>Proč nemůžu vytvořit prostředek? Cenová úroveň není k dispozici a uvádí "Už jste vytvořili 1 S0 pro toto předplatné"?

:::image type="content" source="media/pricing.png" alt-text="Zpráva, když už existuje prostředek F0":::

Během veřejné verze Preview se může v rámci předplatného v jedné oblasti vytvořit jenom jedna instance poradce metrik.

Pokud již máte vytvořenou instanci ve stejné oblasti, která používá stejné předplatné, můžete pro vytvoření nové instance použít jinou oblast nebo jiné předplatné. Můžete také odstranit existující instanci a vytvořit novou.

Pokud jste už odstranili existující instanci, ale pořád se zobrazí tato chyba, počkejte asi 20 minut po odstranění prostředku, než vytvoříte novou instanci.

## <a name="basic-concepts"></a>Základní koncepty

### <a name="what-is-multi-dimensional-time-series-data"></a>Co jsou multidimenzionální data časových řad?

Podívejte se na [multidimenzionální definici metriky](glossary.md#multi-dimensional-metric)  v glosáři.

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>Kolik dat je potřeba k tomu, aby Poradce pro metriky spouštěl detekci anomálií?

Minimálně jeden datový bod může aktivovat detekci anomálií. To ale nevede k dosažení nejlepší přesnosti. Služba bude předpokládat okno předchozích datových bodů s použitím hodnoty, kterou jste zadali jako pravidlo "Fill-mezera" při vytváření datového kanálu.

Doporučujeme mít některá data před časovým razítkem, na kterém chcete zjistit.
Na základě členitosti dat se doporučená hodnota dat liší podle následujících pokynů.

| Členitost | Doporučená velikost dat pro detekci |
| ----------- | ------------------------------------- |
| Méně než 5 minut | 4 dny dat |
| 5 minut až 1 den | 28 dní dat |
| Více než 1 den do 31 dnů | 4 roky dat |
| Více než 31 dní | 48 let dat |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>Proč Poradce pro metriky nedetekuje anomálie z historických dat?

Poradce metriky je určený k detekci živých streamování dat. Existuje omezení maximální délky historických dat, která bude služba zobrazovat zpátky a spustí detekci anomálií. Znamená to, že pouze datové body po některém z nejbližších časových razítek budou mít výsledky detekce anomálií. Nejbližší časové razítko závisí na členitosti dat.

Na základě členitosti dat jsou délky historických dat, která budou mít výsledky detekce anomálií, následující.

| Členitost | Maximální délka historických dat pro detekci anomálií |
| ----------- | ------------------------------------- |
| Méně než 5 minut | Doba zprovoznění – 13 hodin |
| 5 minut na méně než 1 hodinu | Doba zprovoznění – 4 dny  |
| 1 hodina do méně než 1 den | Doba zprovoznění – 14 dní  |
| 1 den | Doba zprovoznění – 28 dnů  |
| Více než 1 den, méně než 31 dní | Doba zprovoznění – 2 roky  |
| Více než 31 dní | Doba zprovoznění – 24 let   |

### <a name="more-concepts-and-technical-terms"></a>Další koncepty a technické výrazy

Další informace najdete také v [glosáři](glossary.md) .

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>Návody napsat platný dotaz pro ingestování mých dat?  

Aby mohl Poradce pro metriky přijímat data, budete muset vytvořit dotaz, který vrací dimenze vašich dat v jednom časovém razítku. Poradce metriky spustí tento dotaz několikrát, aby získal data z jednotlivých časových razítek. 

Všimněte si, že dotaz by měl vracet maximálně jeden záznam pro každou kombinaci dimenzí v daném časovém razítku. Všechny vrácené záznamy musí mít stejné časové razítko. Dotaz nevrátil žádné duplicitní záznamy.

Předpokládejme například, že vytvoříte následující dotaz pro denní metriku: 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

Nezapomeňte pro vaši časovou řadu použít správnou členitost. Pro hodinovou metriku byste použili: 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

Všimněte si, že tyto dotazy vrací data pouze v jednom časovém razítku a obsahují všechny kombinace dimenzí, které budou ingestovat pomocí Poradce pro metriky. 

:::image type="content" source="media/query-result.png" alt-text="Zpráva, když už existuje prostředek F0" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>Návody detekovat špičky & jako anomálie?

Pokud máte předdefinované pevné prahové hodnoty, můžete v [konfiguracích detekce anomálií](how-tos/configure-metrics.md#anomaly-detection-methods)ve skutečnosti ručně nastavit "tvrdé prahové hodnoty".
Pokud neexistují žádné prahové hodnoty, můžete použít inteligentní zjišťování, které používá AI. Podrobnosti najdete [v tématu ladění konfigurace zjišťování](how-tos/configure-metrics.md#tune-the-detecting-configuration) .

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>Návody detekci neshodě s běžnými (sezónními) vzorci jako anomálie?

Inteligentní zjišťování se může naučit vzor vašich dat, včetně sezónních vzorů. Pak detekuje tyto datové body, které neodpovídají běžným vzorům, jako anomálie. Podrobnosti najdete [v tématu ladění konfigurace zjišťování](how-tos/configure-metrics.md#tune-the-detecting-configuration) .

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>Návody detekovat ploché řádky jako anomálie?

Pokud jsou vaše data obvykle poměrně nestabilní a výkyvují velkou část a chcete, aby se zobrazila výstraha, když se změní příliš stabilní nebo se změní na plochý řádek, je možné nastavit prahovou hodnotu "Změna prahové hodnoty" na detekci takových datových bodů, když je změna příliš malá.
Podrobnosti najdete v tématu [konfigurace detekce anomálií](how-tos/configure-metrics.md#anomaly-detection-methods) .

## <a name="next-steps"></a>Další kroky
- [Přehled Poradce pro metriky](overview.md)
- [Vyzkoušet ukázkový web](quickstarts/explore-demo.md)
- [Použití webového portálu](quickstarts/web-portal.md)