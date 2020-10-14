---
title: Konfigurace instance poradce metriky pomocí webového portálu
titleSuffix: Azure Cognitive Services
description: Jak nakonfigurovat instanci poradce metrik a vyladit výsledky detekce anomálií.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 7923dad3d47122c0ceb04d1240736e2b66a0dd64
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048249"
---
# <a name="how-to-configure-metrics-and-fine-tune-detecting-configuration"></a>Postupy: Konfigurace metrik a doladění konfigurace zjišťování

Pomocí tohoto článku můžete začít s konfigurací instance Advisoru metrik pomocí webového portálu. Pokud chcete vyhledat metriky pro konkrétní datový kanál, přejděte na stránku **datové kanály** a vyberte jeden z informačních kanálů. Zobrazí se seznam metrik, které jsou k němu přidružené.

:::image type="content" source="../media/metrics/select-metric.png" alt-text="Vyberte metriku" lightbox="../media/metrics/select-metric.png":::

Kliknutím na jeden z názvů metrik zobrazíte jeho podrobnosti. V tomto podrobném zobrazení můžete přepnout na jinou metriku ve stejném datovém kanálu pomocí rozevíracího seznamu v pravém horním rohu obrazovky.

Když si poprvé zobrazíte podrobnosti metrik, můžete načíst časovou řadu tím, že pro vás povolíte poradce metriky, nebo zadáním hodnot, které se mají zahrnout do jednotlivých dimenzí. 

Můžete také vybrat rozsahy času a změnit rozložení stránky.

> [!NOTE]
> - Čas spuštění je včetně.
> - Čas ukončení je exkluzivní. 

Kliknutím na kartu **incidenty** můžete zobrazit anomálie a vyhledat odkaz na [centrum incidentů](diagnose-incident.md).

## <a name="tune-the-detecting-configuration"></a>Vyladění konfigurace zjišťování

Metrika může použít jednu nebo více konfigurací detekce. Pro každou metriku existuje výchozí konfigurace, kterou můžete upravit nebo přidat do, podle svých potřeb monitorování.

### <a name="tune-the-configuration-for-all-series-in-current-metric"></a>Ladit konfiguraci pro všechny řady v aktuální metrikě

Tato konfigurace se použije na všechny řady v této metrikě, kromě těch, které mají samostatnou konfiguraci. Konfigurace úrovně metriky se ve výchozím nastavení aplikují, když jsou data připojená a zobrazují se na levém panelu. Uživatelé můžou na stránce metriky přímo upravovat konfiguraci na úrovni metrik. 

Existují další parametry, jako je **směr**a **platná anomálie** , které lze použít k další optimalizaci konfigurace. Můžete kombinovat i různé metody detekce. 

:::image type="content" source="../media/configuration-combination.png" alt-text="Vyberte metriku" lightbox="../media/configuration-combination.png":::

### <a name="tune-the-configuration-for-a-specific-series-or-group"></a>Vyladění konfigurace konkrétní řady nebo skupiny

Kliknutím na **Upřesnit konfiguraci** pod možností konfigurace úrovně metriky zobrazte konfiguraci na úrovni skupiny. Kliknutím na **+** ikonu v tomto okně můžete přidat konfiguraci pro jednotlivé řady nebo skupinu řad. Parametry jsou podobné parametrům konfigurace na úrovni metrik, ale možná budete muset zadat alespoň jednu hodnotu dimenze pro konfiguraci na úrovni skupiny pro identifikaci skupiny řad. A zadejte všechny hodnoty dimenzí pro konfiguraci na úrovni řady a Identifikujte konkrétní řadu. 

Tato konfigurace se použije pro skupinu řad nebo konkrétní řady namísto konfigurace úrovně metriky. Po nastavení podmínek pro tuto skupinu ji uložte.

:::image type="content" source="../media/advanced-configuration.png" alt-text="Vyberte metriku" lightbox="../media/advanced-configuration.png":::

### <a name="anomaly-detection-methods"></a>Metody detekce anomálií

Poradce metrik nabízí více metod detekce anomálií. Můžete použít jeden nebo zkombinovat pomocí logických operátorů kliknutím na **+** tlačítko. 

**Inteligentní zjišťování** 

Inteligentní zjišťování je založené na strojovém učení, které se učí vzory z historických dat a používá je k budoucí detekci. Při použití této metody je **citlivostí** nejdůležitější parametr pro optimalizaci výsledků zjišťování. Můžete ji přetáhnout na menší nebo větší hodnotu a ovlivnit tak vizualizaci na pravé straně stránky. Vyberte si ten, který odpovídá vašim datům, a uložte ho. 


V režimu Inteligentní detekce se pro vyladění výsledku detekce anomálií použijí parametry verze citlivost a hranice.

Citlivost může ovlivnit šířku rozsahu očekávané hodnoty každého bodu. Při zvýšení bude očekávaný rozsah hodnot těsný a budou hlášeny další anomálie:

:::image type="content" source="../media/metrics/smart-detection-high-sensitivity.png" alt-text="Vyberte metriku":::

Když je citlivost vypnutá, očekávaný rozsah hodnoty bude širší a bude Hlášeno méně anomálií:

:::image type="content" source="../media/metrics/smart-detection-low-sensitivity.png" alt-text="Vyberte metriku":::

**Změnit prahovou hodnotu** 

Mezní hodnota změny se obvykle používá, když se data metriky všeobecně nacházejí v určitém rozsahu. Prahová hodnota je nastavena podle **procenta změny**. Režim **změny prahové hodnoty** dokáže detekovat anomálie ve scénářích:

* Vaše data jsou normálně stabilní a hladké. Chcete být upozorněni, když dojde ke kolísání.
* Vaše data jsou normálně poměrně nestabilní a výkyvují velké množství. Chcete být upozorněni, když bude příliš stabilní nebo plochý.

Chcete-li použít tento režim, použijte následující postup:

1. Vyberte možnost **změnit prahovou hodnotu** jako metodu detekce anomálií při nastavování konfigurací detekce anomálií pro vaše metriky nebo časové řady.
    
    :::image type="content" source="../media/metrics/change-threshold.png" alt-text="Vyberte metriku":::

2. Vyberte **mimo rozsah** nebo **v parametru rozsah** podle vašeho scénáře.

    Pokud chcete detekovat kolísání, vyberte **mimo rozsah**. Například s níže uvedeným nastavením se jako izolované zjistí všechny datové body, které se v porovnání s předchozími změnami rovnají 10%.
    :::image type="content" source="../media/metrics/out-of-the-range.png" alt-text="Vyberte metriku":::

    Pokud chcete ve svých datech detekovat ploché řádky, vyberte **v tomto rozsahu**. Například s níže uvedeným nastavením se jako izolované detekuje jakýkoliv datový bod, který se mění v 0,01% oproti předchozímu. Vzhledem k tomu, že prahová hodnota je malá (0,01%), detekuje ploché řádky v datech jako odlehlé hodnoty.

    :::image type="content" source="../media/metrics/in-the-range.png" alt-text="Vyberte metriku":::

3. Nastavte procentuální hodnotu změny, která se bude počítat jako anomálie a k porovnání budou použity dříve zachycené datové body. Toto porovnání je vždy mezi aktuálním datovým bodem a jedním datovým bodem N bodů před ním.
    
    **Směr** je platný pouze v případě, že používáte režim **Rozsah** :
    
    * **Up** nakonfiguruje detekci tak, aby detekuje anomálie jenom v případě, že (aktuální datový bod) – (datový bod porovnání) > **+** prahovou hodnotou.
    * **Down** Nakonfiguruje detekci tak, aby detekuje anomálie jenom tehdy, když (aktuální datový bod) – (porovnávání datového bodu) < **-** prahovou hodnotou.
 
**Tvrdý práh**

 Tvrdý práh je základní metoda pro detekci anomálií. Můžete nastavit horní nebo dolní mez, abyste určili očekávaný rozsah hodnot. Všechny body, které se nacházejí mimo hranici, budou označeny jako anomálie. 


## <a name="preset-events"></a>Předdefinované události

V některých případech se může stát, že očekávané události a výskyty (například svátky) generují neobvyklé data. Pomocí přednastavených událostí můžete do výstupu detekce anomálií přidat příznaky během určených časů. Tato funkce by se měla nakonfigurovat po zprovoznění datového kanálu. Každá metrika může mít jenom jednu z přednastavených konfigurací události.

> [!Note]
> Přednastavená konfigurace události bude při detekci anomálií brát v úvahu svátky a může změnit výsledky. Použije se na datové body, které se ingestují po uložení konfigurace. 

Klikněte na tlačítko **Konfigurovat přednastavený událost** vedle rozevíracího seznamu metriky na každé stránce podrobností metriky.
 
:::image type="content" source="../media/metrics/preset-event-button.png" alt-text="Vyberte metriku":::

V zobrazeném okně nakonfigurujte možnosti podle vašeho využití. Ujistěte se, že je vybrána možnost **Povolit volnou událost** pro použití konfigurace. 

Část **události svátků** vám pomůže potlačit zbytečné anomálie zjištěné během dovolené. Existují dvě možnosti, jak můžete použít možnost **strategie** :

* **Potlačit svátek**: potlačí všechny anomálie a výstrahy v důsledku zjištění anomálií během doby svátků.
* **Svátek jako víkend**: vypočítá průměrné očekávané hodnoty několika odpovídajících víkendů před svátkem a založí stav anomálií z těchto hodnot.

Existuje několik dalších hodnot, které můžete nakonfigurovat:

|Možnost  |Popis  |
|---------|---------|
|**Vyberte jednu dimenzi jako zemi.**     | Vyberte dimenzi, která obsahuje informace o zemi. Například kód země.         |
|**Mapování kódu země**     | Mapování mezi standardním [kódem země](https://wikipedia.org/wiki/ISO_3166-1_alpha-2)a údaji země zvolené dimenze.        |
|**Možnosti svátků**    | Bez ohledu na to, jestli se mají brát v úvahu všechny svátky, jenom PTO (placené volno) nebo jenom svátky PTO.         |
|**Dny k rozšíření**    |  Ovlivněné dny před a po svátek.        |


Část **události cyklu** se dá v některých scénářích použít k omezení zbytečných upozornění pomocí cyklických vzorů v datech. Příklad: 

- Metriky, které mají více vzorů nebo cyklů, jako je například týdenní i měsíční vzor. 
- Metriky, které nemají jasný vzor, ale data jsou srovnatelné roky za rok (YoY), měsíc za měsíc (MoM), týden v týdnu (WoW) nebo den za den (DoD).
 
Ne všechny možnosti lze vybrat pro každou členitost. K dispozici jsou následující možnosti na členitost:

| Členitost | YoY | MoM | WoW | DoD |
|:-|:-|:-|:-|:-|
| Ročně | X | X | X | X |
| Měsíčně | X | X | X | X |
| Každý týden | ✔ | X | X | X |
| Každý den | ✔ | ✔ | ✔ | × |
| Hodinově | ✔ | ✔ | ✔ | ✔ |
| Po minutách | X | X | X | X |
| Za druhé | X | X | X | X |
| Uživatelská | ✔ | ✔ | ✔ | ✔ |

X – není k dispozici.  
✔ – K dispozici.
  
\* Pokud používáte vlastní členitost v sekundách, je k dispozici pouze v případě, že je metrika delší než jedna hodina a méně než jeden den.

Událost cyklu se používá ke snížení anomálií, pokud následují cyklický vzor, ale vyhlásí anomálii, pokud více datových bodů nedodržuje vzor. **Striktní režim** slouží k povolení vytváření sestav anomálií, pokud ani jeden datový bod nedodržuje vzor. 

:::image type="content" source="../media/metrics/preset-events.png" alt-text="Vyberte metriku":::

## <a name="view-recent-incidents"></a>Zobrazit nedávné incidenty

Poradce pro metriky detekuje anomálie na všech vašich datech časových řad při jejich ingestování. Ale ne všechny anomálie musí být eskalacované, protože nemusí mít velký dopad. Agregace se provede na anomáliích pro seskupení souvisejících skupin na incidenty. Tyto incidenty můžete zobrazit na kartě **incident** na stránce s podrobnostmi metriky. 

Kliknutím na incident přejděte na stránku **analýzy incidentů** , kde můžete zobrazit další podrobnosti. Klikněte na **Spravovat incidenty v novém centru incidentů**, abyste našli stránku [centra incidentů](diagnose-incident.md) , kde můžete najít všechny incidenty v rámci konkrétní metriky. 

## <a name="subscribe-anomalies-for-notification"></a>Odebírat anomálie pro oznámení

Pokud se chcete dostat oznámení, když se zjistí anomálii, můžete se přihlásit k odběru upozornění pro tuto metriku pomocí zavěšení. Další informace najdete v tématu [Konfigurace výstrah a získání oznámení pomocí zavěšení](alerts.md) .


## <a name="next-steps"></a>Další kroky 
- [Konfigurace výstrah a získání oznámení pomocí háku](alerts.md)
- [Úprava detekce anomálií pomocí zpětné vazby](anomaly-feedback.md)
- [Diagnostikujte incident](diagnose-incident.md).

