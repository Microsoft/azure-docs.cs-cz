---
title: Nejčastější dotazy k metrickým nástrojům
titleSuffix: Azure Cognitive Services
description: Nejčastější dotazy týkající se služby Advisor metrik.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: mbullwin
ms.openlocfilehash: 0c4c296cb1454ed89eef102732533589b1c8ca0d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420953"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>Nejčastější dotazy k metrickým nástrojům

### <a name="what-is-the-cost-of-my-instance"></a>Jaké jsou náklady na moji instanci?

V současné době není za použití vaší instance ve verzi Preview žádné náklady.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>Proč nemůžu vytvořit prostředek? Cenová úroveň není k dispozici a uvádí "Už jste vytvořili 1 S0 pro toto předplatné"?

:::image type="content" source="media/pricing.png" alt-text="Zpráva, když už existuje prostředek F0":::

Během veřejné verze Preview se dá v rámci předplatného vytvořit jenom jedna instance Advisoru metriky pro jednotlivé oblasti.

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

:::image type="content" source="media/query-result.png" alt-text="Výsledek dotazu s jedním časovým razítkem" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>Návody detekovat špičky & jako anomálie?

Pokud máte předdefinované pevné prahové hodnoty, můžete v [konfiguracích detekce anomálií](how-tos/configure-metrics.md#anomaly-detection-methods)ve skutečnosti ručně nastavit "tvrdé prahové hodnoty".
Pokud neexistují žádné prahové hodnoty, můžete použít inteligentní zjišťování, které používá AI. Podrobnosti najdete [v tématu ladění konfigurace zjišťování](how-tos/configure-metrics.md#tune-the-detecting-configuration) .

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>Návody detekci neshodě s běžnými (sezónními) vzorci jako anomálie?

Inteligentní zjišťování se může naučit vzor vašich dat, včetně sezónních vzorů. Pak detekuje tyto datové body, které neodpovídají běžným vzorům, jako anomálie. Podrobnosti najdete [v tématu ladění konfigurace zjišťování](how-tos/configure-metrics.md#tune-the-detecting-configuration) .

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>Návody detekovat ploché řádky jako anomálie?

Pokud jsou vaše data obvykle poměrně nestabilní a výkyvují velkou část a chcete, aby se zobrazila výstraha, když se změní příliš stabilní nebo se změní na plochý řádek, je možné nastavit prahovou hodnotu "Změna prahové hodnoty" na detekci takových datových bodů, když je změna příliš malá.
Podrobnosti najdete v tématu [konfigurace detekce anomálií](how-tos/configure-metrics.md#anomaly-detection-methods) .

### <a name="how-to-set-up-email-settings-and-enable-alerting-by-email"></a>Jak nastavit nastavení e-mailu a povolit upozorňování e-mailem?

1.  Uživatel s oprávněním správce předplatného nebo správce skupiny prostředků musí přejít na prostředek poradce metriky, který se vytvořil v Azure Portal, a vybrat kartu **řízení přístupu (IAM)** . 
2.  Vyberte **Přidat přiřazení rolí** .
3.  Vyberte roli **správce Poradce pro Cognitive Services metriky**, vyberte svůj účet jako na obrázku níže.
4.  Klikněte na tlačítko **Uložit** a pak jste úspěšně přidali jako správce prostředku Advisory metrik. Všechny výše uvedené akce musíte provést správcem předplatného nebo správcem skupiny prostředků. 

:::image type="content" source="media/access-control.png" alt-text="Na stránce nabídky pro řízení přístupu (IAM) s vybraným přidáním přiřazení role následovaný polem s přiřazením přístupu k vybranému uživateli, který je zobrazený s rolí přístupu Cognitive Services správce Poradce pro metriky, a následným tlačítkem uložit v uživatelském rozhraní, které se vybere k ilustraci kroků hledání uživatele a přidání určité úrovně oprávnění k přístupu." lightbox="media/access-control.png":::


5.  Může trvat až jednu minutu, než se oprávnění rozšíří. Pak vyberte svůj pracovní prostor Poradce pro metriky a v levém navigačním panelu vyberte možnost **Nastavení e-mailu** . Vyplňte požadované položky, zejména informace týkající se protokolu SMTP. 
6.  Vyberte **Save (Uložit**) a pak se nastaví Konfigurace e-mailu. U upozornění téměř v reálném čase můžete vytvořit nové háky a přihlásit se k odběru metrik metriky. 

## <a name="advanced-concepts"></a>Pokročilé koncepty

### <a name="how-does-metric-advisor-build-an-incident-tree-for-multi-dimensional-metrics"></a>Jak metrika metriky sestaví strom incidentů pro multidimenzionální metriky?

Metriky je možné rozdělit do několika časových řad podle dimenzí. Například metrika `Response latency` je monitorována pro všechny služby vlastněné týmem. `Service`Kategorie se dá použít jako dimenze k obohacení metriky, takže se budeme `Response latency` dělit od `Service1` , `Service2` a tak dále. Každou službu je možné nasadit na různých počítačích v několika datových centrech, takže tuto metriku můžete dále rozdělit pomocí `Machine` a `Data center` .

|Služba| Datové centrum| Počítač  | 
|----|------|----------------   |
| S1 |  DC1 |   Kategorie |
| S1 |  DC1 |   Kategorie |
| S1 |  DC2 |   Chladírenské |
| S1 |  DC2 |   M4 |
| S2 |  DC1 |   Kategorie |
| S2 |  DC1 |   Kategorie |
| S2 |  DC2 |   M5 |
| S2 |  DC2 |   M6 |
| ...|      |      |

Od celku `Response latency` můžeme přejít k podrobnostem metriky podle `Service` `Data center` a `Machine` . Je ale možné, že vlastníci služeb mají k používání cesty lepší význam `Service`  ->  `Data center`  ->  `Machine` , nebo je možné, že technici infrastruktury mají přístup k používání cesty `Data Center`  ->  `Machine`  ->  `Service` . Vše závisí na individuálních podnikových požadavcích vašich uživatelů. 

V rámci metrického poradce můžou uživatelé zadat libovolnou cestu, kterou chtějí přejít k podrobnostem nebo souhrnům z jednoho uzlu hierarchické topologie. Hierarchická topologie je přesnější a je to místo stromové struktury směrná acyklického graf. Existuje úplná hierarchická topologie, která se skládá ze všech potenciálních kombinací dimenzí, například takto: 

:::image type="content" source="media/dimension-combinations-view.png" alt-text="Diagram hierarchické topologie skládající se z několika rozpojování vrcholů a hran s více dimenzemi označenými S, DC a M s odpovídajícími čísly od 1 do 6" lightbox="media/dimension-combinations-view.png":::

Teoreticky platí, že pokud má dimenze odlišné hodnoty, `Service` `Ls` `Data center` má dimenze `Ldc` odlišné hodnoty a dimenze `Machine` má `Lm` odlišné hodnoty, pak existují `(Ls + 1) * (Ldc + 1) * (Lm + 1)` kombinace dimenzí v hierarchické topologii. 

Ale obvykle ne všechny kombinace dimenzí jsou platné, což může výrazně snížit složitost. V současné době neomezujeme počet dimenzí, pokud uživatelé tuto metriku agreguje sami. Pokud potřebujete použít kumulativní funkci poskytovanou poradcem metriky, počet dimenzí by neměl být větší než 6. Ale omezujeme počet časových řad, které jsou rozšířené o dimenzi, aby byla metrika menší než 10 000.

Nástroj **strom incidentů** na stránce Diagnostika zobrazuje pouze uzly, kde byla zjištěna anomálie, nikoli celá topologie. To vám umožní soustředit se na aktuální problém. Také nemusí zobrazovat všechny anomálie v rámci metrik a místo toho zobrazí horní anomálie na základě příspěvku. Tímto způsobem můžeme rychle zjistit dopad, rozsah a cestu k dvojstránce neobvyklých dat. Což významně snižuje počet anomálií, se kterými se musíme soustředit, a pomáhá uživatelům pochopit a vyhledat jejich klíčové problémy. 
 
Například při výskytu anomálie se `Service = S2 | Data Center = DC2 | Machine = M5` Odchylka anomálie týká nadřazeného uzlu `Service= S2` , který taky zjistil anomálii, ale anomálie neovlivňuje celé datové centrum `DC2` a všechny služby, na kterých se nachází `M5` . Strom incidentu by byl sestaven jako na následujícím snímku obrazovky, na nejvyšší anomálii je zachycena `Service = S2` a původní příčina by mohla být analyzována ve dvou cestách, pro které obě povedou `Service = S2 | Data Center = DC2 | Machine = M5` .

 :::image type="content" source="media/root-cause-paths.png" alt-text="5 označený vrcholy se dvěma odlišnými cestami, které jsou propojeny okraji s běžným uzlem označeným S2. Nejvyšší anomálie je zachycena u služby = S2 a hlavní příčina může být analyzována pomocí dvou cest, které obě tyto možnosti vedou ke službě = S2 | Datové centrum = DC2 | Machine = M5" lightbox="media/root-cause-paths.png":::

## <a name="next-steps"></a>Další kroky
- [Přehled Poradce pro metriky](overview.md)
- [Použití webového portálu](quickstarts/web-portal.md)