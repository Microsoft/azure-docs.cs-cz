---
title: Testování offline – Personalizer
titleSuffix: Azure Cognitive Services
description: Vytvořit smyčku zpětné vazby v tomto C# rychlý start ke službě Personalizer.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 3fdedd1af9b683b221dfa4aebad7a30559b7abff
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722485"
---
# <a name="offline-evaluation"></a>Offline vyhodnocení

Metoda, která umožňuje testování a vyhodnocení účinnosti Personalizer služby bez změny kódu nebo by to ovlivnilo činnost koncového uživatele se vyžaduje vyhodnocení za offline. Offline vyhodnocení používá dříve, které jste provedli data odeslaná z aplikace API pořadí pro porovnání jak různé rozměry.

Podle rozsahu dat se provádí v režimu offline hodnocení. Rozsah můžete dokončit tak pozdě, jak aktuální čas. Začátek rozsahu nemůže být delší než počet dní, zadaná pro [uchovávání dat](how-to-settings.md).

Offline hodnocení vám můžou dát odpověď na následující otázky:

* Jak efektivní jsou Personalizer pořadí pro přizpůsobení úspěšné?
    * Co jsou průměrné odměny dosahuje Personalizer online strojového učení zásady?
    * Jak Personalizer porovnání efektivitu jaké aplikace byste udělali ve výchozím nastavení?
    * Co by byl srovnávací efektivitu náhodné volby pro přizpůsobení?
    * Co by byl srovnávací účinnost zásad různých learning ručně zadáno?
* Které funkce kontextu víc nebo míň přispívají k přizpůsobení úspěšné?
* Které funkce akce víc nebo míň přispívají k úspěšné přizpůsobení?

Kromě toho Offline hodnocení je možné zjistit více optimalizované učení zásady, které Personalizer můžete použít ke zlepšení výsledků v budoucnu.

Offline hodnocení se neposkytuje pokyny ohledně procento událostí pro účely zkoumání.

## <a name="prerequisites-for-offline-evaluation"></a>Předpoklady pro offline hodnocení

Tady jsou důležité pro vyhodnocení reprezentativních offline:

* K dispozici dostatek dat. Doporučená minimální hodnota je nejméně 50 000 událostí.
* Shromažďovat data z období s chování reprezentativní uživatelů a provozu.

## <a name="discovering-the-optimized-learning-policy"></a>Odhalování optimalizovaného learning zásad

Personalizer můžete offline zkušebního automaticky zjistit více optimální learning zásad.

Po provedení offline hodnocení, uvidíte srovnávací efektivitu Personalizer s touto novou zásadou ve srovnání s aktuální zásady pro online. Můžete použít tyto zásady učení k němu platit okamžitě v Personalizer nebo si ho stáhnout pro pozdější analýzu nebo použití.

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Principy relevance výsledků vyhodnocení offline

Při spuštění v režimu offline hodnocení je velmi důležité pro analýzu _spolehlivosti hranice_ výsledků. Pokud jsou široké, znamená to, že vaše aplikace neobdržel dostatek dat pro odhady reward být přesné nebo významné. Systém shromažďuje více dat a spustit v režimu offline hodnocení časových obdobích, se stanou užší intervalu spolehlivosti.

## <a name="how-offline-evaluations-are-done"></a>Jak v režimu offline provést vyhodnocení

Dokončení offline hodnocení pomocí jiné metody volá **Counterfactual vyhodnocení**. 

Personalizer vychází z předpokladu, že jsou možné předpovědět zpětně uživatelů chování (a tedy odměny) (Personalizer nemůže vědět, co bude mít se stalo, pokud uživatel měl ukázalo, že něco jiný než co uvedena) a jenom na Učte se od měřené odměny. 

To je koncepční proces pro vyhodnocení:

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

Offline vyhodnocení využívat jenom takové chování zjištěných uživatelů. Tento proces zahodí velké objemy dat, zejména v případě, že vaše aplikace pořadí volání s velkým počtem akce.


## <a name="evaluation-of-features"></a>Vyhodnocení funkce

Offline vyhodnocení může poskytnout informace o kolik specifické funkce pro akce nebo kontext jsou na miskách vah: pro vyšší odměny. Informace je vypočítán s použitím vyhodnocení proti dané časové období a data a času se může lišit.

Doporučujeme, abyste ve vyhodnocení funkce a s dotazem:

* Jaké funkce, které další, může vaše aplikace nebo systému poskytuje podle ty, které jsou efektivnější?
* Jaké funkce můžete odebrat z důvodu efektivity s nízkou? Přidání funkce nízké efektivitu _šumu_ machine Learning.
* Existují všechny funkce, které jsou neúmyslně zahrnuty? Příklady: identifikovatelné osobní údaje (PII), duplicitní ID atd.
* Existují všechny nežádoucí funkce, které nesmějí být používány pro přizpůsobení z důvodu dodržování legislativních nebo zodpovědná použít požadavky? Jsou nějaké funkce, které by mohly proxy (to znamená, úzce zrcadlení nebo je možné korelovat s) nežádoucí funkce?


## <a name="next-steps"></a>Další postup

[Konfigurace Personalizer](how-to-settings.md)
