---
title: Použití metody offline vyhodnocení – personalizace
titleSuffix: Azure Cognitive Services
description: Tento článek vám vysvětlí, jak pomocí offline hodnocení měřit efektivitu aplikace a analyzovat vaše učení smyčky.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: f8ceef5e80bf15f0ba52a9c289e617018febfb5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623589"
---
# <a name="offline-evaluation"></a>Offline vyhodnocení

Offline vyhodnocení je metoda, která umožňuje testovat a vyhodnocovat účinnost personalizované služby bez změny kódu nebo ovlivnění uživatelského prostředí. Offline vyhodnocení používá minulá data odeslaná z vaší aplikace do api pro hodnocení a odměny k porovnání toho, jak si různé hodnosti vedly.

Offline vyhodnocení se provádí v rozsahu dat. Rozsah může být dokončen až v aktuálním čase. Začátek rozsahu nesmí být větší než počet dní určený pro [uchovávání dat](how-to-settings.md).

Offline hodnocení vám pomůže odpovědět na následující otázky:

* Jak efektivní jsou personalizace řadí pro úspěšné personalizace?
    * Jaké jsou průměrné odměny dosažené zásadami online strojového učení personalisty?
    * Jak personalizace v porovnání s účinností toho, co by aplikace udělala ve výchozím nastavení?
    * Jaká by byla komparativní účinnost náhodné volby pro personalizaci?
    * Jaká by byla komparativní účinnost různých politik učení specifikovaných ručně?
* Které funkce kontextu přispívají víceméně k úspěšnému přizpůsobení?
* Které funkce akcí přispívají víceméně k úspěšnému přizpůsobení?

Kromě toho offline hodnocení lze použít k objevování optimalizovanější zásady učení, které personalizátor můžete použít ke zlepšení výsledků v budoucnu.

Offline hodnocení neposkytují pokyny, pokud jde o procento událostí, které mají být pro průzkum.

## <a name="prerequisites-for-offline-evaluation"></a>Předpoklady pro offline hodnocení

Níže jsou důležité aspekty pro hodnocení zástupce offline:

* Mít dostatek dat. Doporučené minimum je alespoň 50 000 událostí.
* Shromažďujte data z období s reprezentativním chováním uživatelů a provozem.

## <a name="discovering-the-optimized-learning-policy"></a>Objevování optimalizovaných zásad učení

Personalizátor může pomocí offline procesu hodnocení automaticky zjistit optimálnější zásady učení.

Po provedení offline hodnocení, můžete vidět komparativní účinnost Personalista s tímto novým zásadám ve srovnání s aktuální online zásady. Tuto zásadu učení pak můžete použít tak, aby byla účinná okamžitě v personalistovi, a to tak, že ji stáhnete a nahrajete do panelu Modely a zásady. Můžete si jej také stáhnout pro budoucí analýzu nebo použití.

Současné politiky zahrnuté do hodnocení:

| Nastavení výuky | Účel|
|--|--|
|**Online zásady**| Aktuální zásady učení používané v personalizaci |
|**Standardní hodnoty**|Výchozí nastavení aplikace (podle první akce odeslané v rank volání)|
|**Náhodné zásady**|Imaginární Rank chování, které vždy vrátí náhodný výběr akce z dodaných.|
|**Vlastní zásady**|Další zásady učení nahrané při spuštění hodnocení.|
|**Optimalizované zásady**|Pokud bylo hodnocení zahájeno s možností zjistit optimalizovanou zásadu, bude také porovnáno a budete jej moci stáhnout nebo z něj udělat zásadu online učení a nahradit ji současnou.|

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Pochopení relevance výsledků offline hodnocení

Při spuštění offline hodnocení, je velmi důležité analyzovat _hranice spolehlivosti_ výsledků. Pokud jsou široké, znamená to, že vaše aplikace neobdržela dostatek dat, aby odhady odměn byly přesné nebo významné. Vzhledem k tomu, že systém shromažďuje více dat a spouštějíte offline hodnocení po delší dobu, intervaly spolehlivosti se zužují.

## <a name="how-offline-evaluations-are-done"></a>Jak se provádí offline hodnocení

Offline hodnocení se provádí metodou nazvanou **Counterfactual Evaluation**.

Personalizákje je postaven na předpokladu, že chování uživatelů (a tedy odměny) je nemožné předvídat retrospektivně (Personalista nemůže vědět, co by se stalo, kdyby uživatel i ono ukázal něco jiného, než to, co viděli), a jen učit se od měřené odměny.

Jedná se o koncepční proces používaný pro hodnocení:

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

Vyhodnocení offline používá pouze pozorované chování uživatele. Tento proces zahodí velké objemy dat, zejména v případě, že vaše aplikace rank volání s velkým počtem akcí.


## <a name="evaluation-of-features"></a>Hodnocení funkcí

Offline hodnocení mohou poskytnout informace o tom, kolik konkrétních funkcí akcí nebo kontextu váží pro vyšší odměny. Informace se vypočítávají pomocí vyhodnocení proti danému časovému období a datům a mohou se lišit v závislosti na čase.

Doporučujeme podívat se na hodnocení funkcí a zeptat se:

* Jaké další, další, funkce by vaše aplikace nebo systém poskytnout v duchu těch, které jsou účinnější?
* Jaké funkce lze odstranit z důvodu nízké účinnosti? Funkce nízké účinnosti dodávají strojovému učení _šum._
* Existují nějaké funkce, které jsou náhodně zahrnuty? Příklady z nich jsou: uživateli identifikovatelné informace, duplicitní ID atd.
* Existují nějaké nežádoucí funkce, které by neměly být použity k přizpůsobení z důvodu regulačních nebo odpovědných použití úvahy? Existují funkce, které by mohly proxy (to znamená, že úzce zrcadlí nebo korelují s) nežádoucí funkce?


## <a name="next-steps"></a>Další kroky

[Konfigurace personalizace](how-to-settings.md)
[spustit offline hodnocení](how-to-offline-evaluation.md) pochopit, jak [personalizace funguje](how-personalizer-works.md)
