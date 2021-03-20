---
title: Použití metody testování offline – přizpůsobení
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak pomocí offline testování změřit efektivitu vaší aplikace a analyzovat výukovou smyčku.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 627f511bb12c16c8f54935d1f782cb7c2c962163
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87132751"
---
# <a name="offline-evaluation"></a>Offline vyhodnocení

Testování offline je metoda, která umožňuje testovat a vyhodnocovat efektivitu služby přizpůsobeného pomocí nástroje, aniž by došlo ke změně kódu nebo ovlivnění uživatelského prostředí. Offline testování používá minulá data odesílaná z vaší aplikace do rozhraní API pro řazení a odměnu, aby bylo možné porovnat, jak se provedlo různé pořadí.

V období data se provádí zkušební verze offline. Rozsah může být dokončený s aktuálním časem jako pozdě. Začátek rozsahu nemůže být větší než počet dní zadaný pro [uchovávání dat](how-to-settings.md).

Testování offline vám může přispět k zodpovězení následujících otázek:

* Jak efektivní je přizpůsobování pro úspěšné přizpůsobení?
    * Jaké jsou průměrné ceny dosažené zásadami pro strojové učení v režimu online?
    * Jak se přizpůsobuje porovnávání s účinností, co by se aplikace ve výchozím nastavení provedla?
    * Jaká byla poměrná efektivita náhodné volby pro přizpůsobení?
    * Jaké by byly srovnávací efektivita různých zásad učení, které jsou zadané ručně?
* Které funkce kontextu přispívat více nebo méně pro úspěšné přizpůsobení?
* Které funkce akcí přispívají k úspěšnému přizpůsobení více nebo méně?

Kromě toho je možné pomocí offline testování zjistit více optimalizovaných výukových zásad, které přizpůsobené můžou využít ke zlepšení výsledků v budoucnu.

Offline vyhodnocení neposkytuje pokyny pro procento událostí, které se mají použít pro průzkum.

## <a name="prerequisites-for-offline-evaluation"></a>Předpoklady pro offline testování

Níže jsou uvedené důležité požadavky na zástupce pro offline testování:

* Má dostatek dat. Doporučené minimum je minimálně 50 000 událostí.
* Shromažďování dat z období pomocí reprezentativního chování a provozu uživatelů

## <a name="discovering-the-optimized-learning-policy"></a>Zjišťují se zásady optimalizovaného učení.

Přizpůsobený modul může k automatickému zjišťování lepších výukových zásad využít proces vyhodnocení offline.

Po provedení testování offline se v porovnání s aktuálními zásadami online zobrazí srovnávací efektivita přizpůsobení s touto novou zásadou. Tuto zásadu učení pak můžete použít, chcete-li ji uplatnit hned v přizpůsobeném, a to tak, že ji stáhnete a nahrajete na panelu modely a zásady. Můžete si ho také stáhnout pro účely budoucí analýzy nebo použití.

Aktuální zásady zahrnuté do hodnocení:

| Nastavení učení | Účel|
|--|--|
|**Online zásady**| Aktuální zásady učení použité v přizpůsobeném nástroji |
|**Standardní hodnoty**|Výchozí hodnota aplikace (určená první akcí odeslanou při voláních pořadí)|
|**Náhodné zásady**|Chování imaginárního pořadí, které vždy vrátí náhodný výběr akcí ze zadaných.|
|**Vlastní zásady**|Při spuštění vyhodnocení se nahrály další zásady učení.|
|**Optimalizované zásady**|Pokud bylo vyhodnocení spuštěno s možností zjišťování optimalizované zásady, bude porovnána a bude možné ji stáhnout, nebo ji můžete nastavit jako zásady učení online, přičemž nahradíte tu aktuální.|

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Princip závažnosti offline vyhodnocení výsledků

Při spuštění testování offline je velmi důležité analyzovat _meze spolehlivosti_ výsledků. Pokud jsou velké, znamená to, že vaše aplikace nedostala dostatek dat, aby odhady odměna byly přesné nebo významné. Jelikož systém shromažďuje více dat a spouštíte offline vyhodnocení v delších obdobích, dojde k zúžení intervalů spolehlivosti.

## <a name="how-offline-evaluations-are-done"></a>Jak se dokončí hodnocení offline

Hodnocení offline se provádí pomocí metody s názvem **Counterfactual Evaluation**.

Přizpůsobená aplikace je postavená na předpokladu, že chování uživatelů (a tedy i jejich využití) není nemožné předpovědět (přizpůsobení nemůže vědět, co by se stalo, pokud byl uživatel vidět jinak, než to uvidí), a jenom se dozvědět od měřené ceny.

Toto je koncepční proces, který se používá pro vyhodnocení:

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call

        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.

    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

Testování offline používá pouze pozorované chování uživatele. Tento proces zahodí velké objemy dat, zejména v případě, že vaše aplikace provádí pořadí volání s velkým počtem akcí.


## <a name="evaluation-of-features"></a>Vyhodnocení funkcí

Online vyhodnocení můžou poskytovat informace o tom, kolik konkrétních funkcí pro akce nebo kontext se má zvážit pro vyšší ceny. Tyto informace jsou vypočítány pomocí vyhodnocení za dané časové období a data a mohou se lišit v čase.

Doporučujeme, abyste provedli hodnocení funkcí a požádali:

* Další, další funkce, které může vaše aplikace nebo systém poskytovat na řádcích, které jsou efektivnější?
* Jaké funkce je možné odebrat z důvodu nízké efektivity? Funkce s nízkou efektivitou přidávají do strojového učení _šum_ .
* Jsou k dispozici nějaké funkce, které jsou omylem zahrnuty? Příklady těchto: uživatelsky identifikovatelné informace, duplicitní ID atd.
* Existují nějaké nežádoucí funkce, které by se neměly používat k přizpůsobení v důsledku regulativního nebo zodpovědného použití. Jsou k dispozici funkce, které by mohly být proxy serverem (to znamená, že se jedná o těsné zrcadlení nebo korelace)?


## <a name="next-steps"></a>Další kroky

Konfigurovat přizpůsobeného [přizpůsobování](how-to-settings.md) 
 [Spustit vyhodnocení offline](how-to-offline-evaluation.md) Princip fungování přizpůsobeného [přizpůsobování](how-personalizer-works.md)
