---
title: Vyhodnocení funkcí - Personalizátor
titleSuffix: Azure Cognitive Services
description: Když spustíte hodnocení v prostředku personalizace z webu Azure Portal, personalizace poskytuje informace o tom, jaké funkce kontextu a akce ovlivňují model.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 31243b5e9da55aafbc376fa416c1b00a4499c116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "70242415"
---
# <a name="feature-evaluation"></a>Vyhodnocení funkcí

Když spustíte hodnocení v prostředku personalizace z [webu Azure Portal](https://portal.azure.com), personalizace poskytuje informace o tom, jaké funkce kontextu a akce ovlivňují model. 

To je užitečné pro:

* Představte si další funkce, které byste mohli použít, získat inspiraci z toho, co funkce jsou důležitější v modelu.
* Podívejte se, jaké funkce nejsou důležité, a potenciálně je odeberte nebo dále analyzujte, co může mít vliv na používání.
* Poskytněte redakčním nebo kuračním týmům pokyny ohledně nového obsahu nebo produktů, které stojí za to do katalogu přinést.
* Poradce při potížích a chybách, ke kterým dochází při odesílání funkcí personalistovi.

Důležitější rysy mají silnější váhy v modelu. Vzhledem k tomu, že tyto funkce mají silnější váhu, mají tendenci být přítomny, když personalizátor získá vyšší odměny.

## <a name="getting-feature-importance-evaluation"></a>Získání hodnocení důležitosti funkce

Chcete-li zobrazit výsledky důležitosti funkce, je nutné spustit vyhodnocení. Hodnocení vytvoří popisky prvků čitelné pro člověka na základě názvů funkcí pozorovaných během období hodnocení.

Výsledné informace o důležitosti funkce představují aktuální model online personalisty. Hodnocení analyzuje význam modelu uloženého v koncovém datu období hodnocení poté, co prošel všemi školeními provedenými během hodnocení, se současnou politikou online učení. 

Výsledky důležitosti funkce nepředstavují jiné zásady a modely testované nebo vytvořené během hodnocení.  Hodnocení nebude obsahovat funkce odeslané personalistovi po skončení zkušebního období.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>Jak interpretovat hodnocení důležitosti funkce

Personalizákátor vyhodnocuje funkce vytvořením "skupin" funkcí, které mají podobný význam. Jedna skupina může být řekl, že mají celkově větší význam než ostatní, ale v rámci skupiny, řazení funkcí je abecedně.

Informace o jednotlivých funkcích zahrnují:

* Zda funkce pochází z kontextu nebo akce.
* Klíč a hodnota funkce.

Například aplikace pro objednávání zmrzliny může jako velmi důležitou funkci vidět "Context.Weather:Hot".

Personalizák zobrazuje korelace funkcí, které, když se berou v úvahu společně, produkují vyšší odměny.

Například se může zobrazit "Context.Weather:Hot *with* Action.MenuItem:IceCream" a "Context.Weather:Cold *with* Action.MenuItem:WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>Akce, které můžete provést na základě vyhodnocení funkcí

### <a name="imagine-additional-features-you-could-use"></a>Představte si další funkce, které byste mohli použít

Nechte se inspirovat důležitějšími funkcemi modelu. Pokud se například v mobilní aplikaci pro videozobrazuje "Context.MobileBattery:Low", může se vám zobrazit tento typ připojení, který může způsobit, že se zákazníci rozhodnou zobrazit jeden videoklip přes jiný, a pak do aplikace přidat funkce o typu připojení a šířce pásma.

### <a name="see-what-features-are-not-important"></a>Podívejte se, jaké funkce nejsou důležité

Potenciálně odstranit nedůležité funkce nebo dále analyzovat, co může ovlivnit používání. Funkce se mohou z mnoha důvodů řadit nízko. Jedním z nich by mohlo být, že skutečně funkce nemá vliv na chování uživatelů. Ale to by mohlo také znamenat, že funkce není zřejmé, pro uživatele. 

Například video stránky by mohly vidět, že "Action.VideoResolution = 4k" je funkce s nízkou důležitostí, v rozporu s uživatelským výzkumem. Příčinou může být, že aplikace ani nezmiňuje nebo nezobrazuje rozlišení videa, takže uživatelé by na něm nezměnili své chování.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>Poskytněte pokyny redakčním nebo kuračním týmům

Poskytněte pokyny k novému obsahu nebo produktům, které stojí za to do katalogu uvést. Personalizátor je navržen tak, aby byl nástrojem, který rozšiřuje lidské vhledy a týmy. Jedním ze způsobů, jak to dělá, je tím, že poskytuje informace redakční skupiny o tom, co je to o produktech, článcích nebo obsahu, který řídí chování. Scénář aplikace videa může například ukázat, že existuje důležitá funkce nazvaná "Action.VideoEntities.Cat:true", což vede k tomu, že redakční tým přinese další videa koček.

### <a name="troubleshoot-common-problems-and-mistakes"></a>Poradce při potížích a chybách

Běžné problémy a chyby lze opravit změnou kódu aplikace tak, aby neodesílal nevhodné nebo nesprávně formátované funkce personalistovi. 

Běžné chyby při odesílání funkcí zahrnují následující:

* Zasílání osobně identifikovatelných informací (PII). PiI specifické pro jednu osobu (jako je jméno, telefonní číslo, čísla kreditních karet, IP adresy) by neměly být používány s Personalizákem. Pokud vaše aplikace potřebuje sledovat uživatele, použijte neidentifikující UUID nebo jiné číslo UserID. Ve většině případů je to také problematické.
* U velkého počtu uživatelů je nepravděpodobné, že interakce každého uživatele bude vážit více než interakce všech obyvatel, takže odesílání ID uživatelů (i když bez PII) pravděpodobně přidá modelu více šumu než hodnota.
* Odeslání polí data a času jako přesných časových razítek namísto hodnot času featurized. S funkcemi, jako je Context.TimeStamp.Day=Monday nebo "Context.TimeStamp.Hour"="13" je užitečnější. Pro každou z nich bude k dispozici maximálně 7 nebo 24 hodnot prvků. Ale "Context.TimeStamp":"1985-04-12T23:20:50.52Z" je tak přesný, že nebude žádný způsob, jak se z toho poučit, protože se to už nikdy nestane.

## <a name="next-steps"></a>Další kroky

Pochopte [škálovatelnost a výkon](concepts-scalability-performance.md) pomocí personalistu.

