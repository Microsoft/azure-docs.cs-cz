---
title: Poskytněte zpětnou vazbu o anomálii službě Advisory metrik.
titleSuffix: Azure Cognitive Services
description: Naučte se, jak odeslat zpětnou vazbu na anomálie nalezené instancí poradce metriky a vyladit výsledky.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbullwin
ms.openlocfilehash: 96a6dfebdd0047d5a6f4ff3e295ab96dd8c7226a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184853"
---
# <a name="provide-anomaly-feedback"></a>Poskytnutí zpětné vazby anomálií

Zpětná vazba uživatele je jednou z nejdůležitějších metod zjišťování vad v systému detekce anomálií. Tady poskytujeme způsob, jak uživatelům označit nesprávné výsledky detekce přímo v časové řadě a okamžitě použít tuto zpětnou vazbu. Tímto způsobem může uživatel naučit systém detekce anomálií, jak provádět detekci anomálií pro konkrétní časovou řadu prostřednictvím aktivních interakcí. 

> [!NOTE]
> V současné době zpětná vazba ovlivní pouze výsledky detekce anomálií, a to pomocí **inteligentní detekce** , ale i **prahové** **hodnoty pro** změnu

## <a name="how-to-give-time-series-feedback"></a>Jak poskytnout zpětnou vazbu časové řady

Zpětnou vazbu můžete poskytnout na stránce s podrobnostmi metriky na libovolné řadě. Stačí vybrat libovolný bod a zobrazí se dialogové okno zpětné vazby. Zobrazuje rozměry řady, kterou jste si zvolili. Můžete znovu vybrat hodnoty dimenzí nebo dokonce odebrat některé z nich, abyste získali dávku dat časových řad. Po výběru možnosti časová řada vyberte tlačítko **Přidat** , abyste mohli přidat zpětnou vazbu, a máte čtyři druhy zpětné vazby, které byste mohli poskytnout. Pokud chcete připojit více položek zpětné vazby, po dokončení svých poznámek vyberte tlačítko **Uložit** .

:::image type="content" source="../media/feedback/click-on-any-point.png" alt-text="Graf s daty časových řad s modrou čárou a červenými tečkami v různých bodech. Červený rámeček kolem jednoho bodu s textem: vybrat libovolný bod":::

:::image type="content" source="../media/feedback/select-or-remove.png" alt-text="Dialogové okno Přidat zpětnou vazbu se dvěma dimenzemi a možností pro výběr nebo odebrání dimenzí a přidání zpětné vazby.":::

### <a name="mark-the-anomaly-point-type"></a>Označit typ bodu anomálií

Jak je znázorněno na obrázku níže, dialogové okno pro zpětnou vazbu vyplní časové razítko zvoleného bodu automaticky, i když můžete tuto hodnotu upravit. Pak vyberte, zda chcete tuto položku identifikovat jako `Anomaly` , `NotAnomaly` nebo `AutoDetect` .

:::image type="content" source="../media/feedback/anomaly-value.png" alt-text="Rozevírací nabídka s možnostmi anomálií, NotAnomaly a AutoDetect":::

Výběr bude používat váš názor na budoucí zpracování detekce anomálií stejné řady. Zpracované body nebudou přepočítány. To znamená, že pokud jste označili anomálii jako NotAnomaly, potlačíme vám podobné anomálie v budoucnu, a pokud jste označili `NotAnomaly` bod jako `Anomaly` , budeme v budoucnu rozpoznávat podobné body `Anomaly` . Pokud `AutoDetect` je tato možnost zvolená, všechny předchozí zpětné vazby ve stejném bodě budou v budoucnu ignorovány.

## <a name="provide-feedback-for-multiple-continuous-points"></a>Poskytnutí zpětné vazby pro několik souvislých bodů 

Chcete-li poskytnout zpětnou vazbu anomálií pro několik souvislých bodů současně, vyberte skupinu bodů, které chcete opatřit poznámkami. Vybraný časový rozsah se zobrazí automaticky vyplněný, když zadáte zpětnou vazbu na anomálii.

:::image type="content" source="../media/feedback/continuous-anomaly-feedback.png" alt-text="Nabídka zpětné vazby z anomálií s vybraným anomálií a konkrétním časovým rozsahem":::

Chcete-li zobrazit, zda je individuálním bodem ovlivněna vaše zpětná vazba při procházení časových řad, vyberte jeden bod. Pokud byl výsledek detekce anomálií změněn o zpětnou vazbu, zobrazí se na ovládacím prvku popisek, který má **vliv na zpětnou vazbu: true**. Pokud se to **projeví na základě zpětné vazby: false**, znamená to, že pro tento bod byl proveden výpočet zpětné vazby anomálií, ale výsledek detekce anomálií by se neměl měnit.

:::image type="content" source="../media/feedback/affected-by-feedback.png" alt-text="Popisek se zobrazí se slovy: má vliv na zpětnou vazbu: true, zvýrazněný červeně.":::

Existují situace, kdy Nenavrhujeme poskytovat zpětnou vazbu:

- Anomálie je způsobená svátkem. Pro vyřešení tohoto druhu falešného alarmu doporučujeme použít přednastavenou událost, protože bude přesnější.
- Anomálie je způsobena změnou známého zdroje dat. V tuto chvíli nastala například změna v nadřazeném systému. V takovém případě se očekává, že se mu pošle upozornění na anomálii, protože náš systém nevěděl, co způsobilo změnu hodnoty, a když dojde k opakování podobných změn hodnot. Proto nenavrhneme tento druh problému jako `NotAnomaly` .

## <a name="change-points"></a>Body změny

Někdy změna trendu dat ovlivní výsledky detekce anomálií. Pokud se rozhodnete, jestli je bod anomálií, nebo ne, vezme se v úvahu nejnovější okno dat historie. Pokud vaše časová řada má změnu trendu, můžete označit přesný bod změny, což vám pomůže s detekcí anomálií v budoucí analýze.

Jak ukazuje následující obrázek, můžete vybrat `ChangePoint` typ zpětné vazby a vybrat `ChangePoint` , `NotChangePoint` nebo `AutoDetect` v seznamu pro příjem dat.

:::image type="content" source="../media/feedback/changepoint.png" alt-text="Nabídka bodu změny s rozevíracím seznamem obsahujícím možnosti pro ChangePoint, NotChangePoint a AutoDetect":::

> [!NOTE]
> Pokud se vaše data mění, stačí označit jeden bod jako `ChangePoint` , takže pokud jste označili a `timerange` , vyplníme automaticky časové razítko a čas posledního bodu. V takovém případě bude mít vaše Poznámka vliv jenom na výsledky detekce anomálií po 12 bodech.

## <a name="seasonality"></a>Sezónnost

V případě sezónních dat se při zjišťování anomálií v jednom kroku odhaduje období (sezónnost) časové řady a použije se ve fázi detekce anomálií. V některých případech je obtížné identifikovat přesné období a může také dojít ke změně období. Nesprávně definované období může mít vedlejší účinky na výsledky detekce anomálií. Aktuální období můžete najít pomocí popisku, jeho název je `Min Period` .

:::image type="content" source="../media/feedback/min-period.png" alt-text="Popis tlačítka se překrývá s tečkami a číslem 7 zvýrazněným červenou barvou.":::

Můžete poskytnout zpětnou vazbu k období, abyste opravili tento druh chyby detekce anomálií. Jak ukazuje obrázek, můžete nastavit hodnotu období. Jednotka `interval` znamená jednu členitost. V tomto případě se v tomto případě nejedná o data, která nejsou sezónní. Můžete také vybrat, `AutoDetect` jestli chcete zrušit předchozí zpětnou vazbu a automaticky nechat zjistit dobu trvání kanálu. 
 
> [!NOTE]
> Při nastavování období nemusíte přiřazovat časové razítko nebo timerange, toto období bude mít vliv na budoucí detekci anomálií u celých časové řady od chvíle, kdy poskytnete svůj názor.


:::image type="content" source="../media/feedback/period-feedback.png" alt-text="Nabídka s obdobím automatického rozpoznávání nastaveným na hodnotu 28 a interval nastaven na nulu, což znamená, že se nejedná o sezónní.":::

## <a name="provide-comment-feedback"></a>Poskytnutí zpětné vazby k komentářům

Můžete také přidat komentáře, které vám přiřadí poznámky a poskytnou kontext pro vaše data. Chcete-li přidat komentáře, vyberte časový rozsah a přidejte text pro komentář.

:::image type="content" source="../media/feedback/feedback-comment.png" alt-text="Nabídka s možností nastavit časový rozsah a pole pro přidání textového komentáře":::

## <a name="time-series-batch-feedback"></a>Zpětná vazba dávky časové řady

Jak je popsáno výše, vám vaše názory umožňuje znovu vybrat nebo odebrat hodnoty dimenzí a získat tak dávku časových řad definované filtrem dimenzí. Tento modální panel můžete otevřít také tak, že kliknete na tlačítko "+" pro zpětnou vazbu z levého panelu a vyberete dimenze a hodnoty dimenzí.

:::image type="content" source="../media/feedback/feedback-time-series.png" alt-text="Nabídka s modrým symbolem plus zvýrazněným červeně vedle zpětné vazby slov":::

:::image type="content" source="../media/feedback/feedback-dimension.png" alt-text="Přidat nabídku zpětné vazby se dvěma dimenzemi, které jsou označeny dim1 a Dim2":::

## <a name="how-to-view-feedback-history"></a>Jak zobrazit historii zpětné vazby

Existují dva způsoby, jak zobrazit historii zpětné vazby. Na levém panelu můžete vybrat tlačítko Historie zpětné vazby a uvidíte modální seznam zpětných připomínek. Obsahuje seznam všech zpětných připomínek, které jste zadali před jednotlivými nebo filtry dimenzí.

:::image type="content" source="../media/feedback/feedback-history-options.png" alt-text="Nabídka seznam zpětné vazby":::

Další možností zobrazení historie zpětné vazby je z řady. V pravém horním rohu každé řady se zobrazí několik tlačítek. Vyberte tlačítko Zobrazit zpětnou vazbu a řádek se přepne z zobrazení bodů anomálií na zobrazení položek zpětné vazby. Zelený příznak představuje bod změny a modré body jsou jiné body zpětné vazby. Můžete je také vybrat a získat modální seznam zpětných připomínek, který obsahuje podrobné informace o zpětné vazbě uvedené pro tento bod.

:::image type="content" source="../media/feedback/feedback-history-graph.png" alt-text="Graf historie zpětné vazby":::

:::image type="content" source="../media/feedback/feedback-list.png" alt-text="Nabídka seznamu zpětné vazby se dvěma dimenzemi":::

> [!NOTE]
> Kdokoli, kdo má přístup ke službě metriky, může poskytnout zpětnou vazbu, takže se můžete podívat na zpětnou vazbu, kterou poskytli jiní vlastníci datového kanálu. Pokud upravíte stejný bod jako někdo jiný, vaše zpětná vazba přepíše předchozí položku zpětné vazby.       

## <a name="next-steps"></a>Další kroky
- [Diagnostikujte incident](diagnose-incident.md).
- [Konfigurace metrik a doladění konfigurace zjišťování](configure-metrics.md)
- [Konfigurace výstrah a získání oznámení pomocí háku](../how-tos/alerts.md)