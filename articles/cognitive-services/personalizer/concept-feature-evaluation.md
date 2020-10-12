---
title: Vyhodnocení funkcí – přizpůsobení
titleSuffix: Azure Cognitive Services
description: Když spustíte vyhodnocení prostředku přizpůsobeného modulem na Azure Portal, přizpůsobený modul poskytuje informace o tom, jaké funkce kontextu a akcí mají vliv na model.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: c0e47a2943cf8c934d201f76aefc41868adf0b25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87127719"
---
# <a name="feature-evaluation"></a>Vyhodnocení funkcí

Když spustíte vyhodnocení prostředku přizpůsobeného modulem na [Azure Portal](https://portal.azure.com), přizpůsobený modul poskytuje informace o tom, jaké funkce kontextu a akcí mají vliv na model. 

To je užitečné v zájmu:

* Představte si další funkce, které byste mohli použít, abyste inspiracii, z jakých funkcí jsou v modelu důležitější.
* Podívejte se, jaké funkce nejsou důležité, a potenciálně je odebrat nebo analyzovat, co může ovlivnit využití.
* Poskytněte vám pokyny k redakčním nebo léčebným týmům o novém obsahu nebo produktech, které přináší do katalogu.
* Řešení běžných problémů a chyb, ke kterým dochází při posílání funkcí do přizpůsobení.

Důležitější funkce mají v modelu silnější váhy. Vzhledem k tomu, že tyto funkce mají silnější váhu, jsou obvykle přítomné, když přizpůsobování získá vyšší ceny.

## <a name="getting-feature-importance-evaluation"></a>Získávání hodnocení důležitosti funkce

Chcete-li zobrazit výsledky důležitosti funkcí, je nutné spustit vyhodnocení. Vyhodnocení vytváří uživatelsky čitelné popisky funkcí na základě názvů funkcí zjištěných během zkušebního období.

Výsledné informace o funkci důležitost představují aktuální model online přizpůsobování. Vyhodnocení analyzuje důležitost funkcí modelu uloženého v konečném datu zkušebního období, a to po absolvování všech školení provedených během hodnocení s aktuálními zásadami online učení. 

Výsledky důležitosti funkcí neodpovídají jiným zásadám a modelům testovaným nebo vytvořeným během hodnocení.  Vyhodnocení nebude zahrnovat funkce odeslané do přizpůsobení po konci zkušebního období.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>Jak interpretovat vyhodnocení důležitosti funkcí

Přizpůsobení vyhodnotí funkce tím, že vytvoří skupiny funkcí, které mají podobnou důležitost. Jedna skupina může být označena tak, aby měla celkově silnější důležitost než jiné, ale v rámci skupiny je pořadí funkcí seřazeno podle abecedy.

Jednotlivé funkce obsahují tyto informace:

* Určuje, zda funkce pochází z kontextu nebo akcí.
* Klíč a hodnota funkce

Například aplikace pro řazení dílny zmrzliny může zobrazit "Context. počasí: Hot" jako velmi důležitou funkci.

Přizpůsobování zobrazuje korelace funkcí, které při zohlednění dohromady poskytují vyšší ceny.

Například se může zobrazit "Context. počasí: Hot *with* Action. MenuItem: IceCream;" a také "Context. počasí: studená *s* akcí. MenuItem: WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>Akce, které můžete provést na základě vyhodnocení funkcí

### <a name="imagine-additional-features-you-could-use"></a>Představte si další funkce, které byste mohli použít

Získejte inspiraci z důležitějších funkcí v modelu. Pokud například v mobilní aplikaci pro video vidíte "Context. MobileBattery: nízká", můžete si všimnout, že typ připojení může také určit, že se zákazníci budou moci podívat na jeden videoklip a pak do aplikace přidat funkce typu připojení a šířky pásma.

### <a name="see-what-features-are-not-important"></a>Podívejte se, jaké funkce nejsou důležité.

Potenciálně odebrat neimportované funkce nebo dále analyzovat, co může ovlivnit využití. Funkce můžou v mnoha případech vyhodnocena jako nízká. Jednou z nich může být to, že tato funkce nemá vliv na chování uživatele. Může to ale také znamenat, že uživatel nemá zřejmou funkci. 

Například web video může vidět, že "Action. VideoResolution = 4k" je funkce s nízkou důležitostí, která je v konfliktu s výzkumem uživatelů. Příčinou může být to, že aplikace ani nezmiňuje nebo nezobrazuje rozlišení videa, takže uživatelé nemůžou změnit jejich chování na základě IT.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>Poskytněte pokyny k redakčním nebo léčebným týmům.

Poskytněte dokumentaci k novému obsahu nebo produktům, které přináší do katalogu. Přidaný nástroj je navržený tak, aby byl nástrojem, který rozšiřuje lidský přehled a týmy. Jedním ze způsobů, jak to dělá, je poskytnout informace pro redakční skupiny v tom, co je o produktech, článcích nebo obsahu, které řídí chování. Například scénář aplikace video může ukázat, že je k dispozici důležitá funkce s názvem Action. VideoEntities. Cat: true, přičemž se zobrazí redakční tým, který se dotáže na další videa o Cat.

### <a name="troubleshoot-common-problems-and-mistakes"></a>Řešení běžných problémů a chyb

Běžné problémy a chyby mohou být opraveny změnou kódu aplikace, aby neodesílaly nevhodné nebo nesprávně naformátované funkce pro přizpůsobení. 

K běžným chybám při odesílání funkcí patří následující:

* Posílání identifikovatelných osobních údajů (PII). V přidaném programu by se neměly používat PII specifické pro jednu jednotlivou (například název, telefonní číslo, čísla kreditních karet, IP adresy). Pokud vaše aplikace potřebuje sledovat uživatele, použijte neidentifikační identifikátor UUID nebo jiné číslo ID uživatele. Ve většině scénářů to je také problematické.
* U velkého počtu uživatelů je pravděpodobné, že interakce jednotlivých uživatelů bude mít za to více než všechny interakce, takže odeslání ID uživatele (i v případě, že Nepii) bude pravděpodobně do modelu přidávat větší šum než hodnota.
* Posílání polí data a času jako přesná časová razítka místo hodnot natrénuje Time. Funkce, jako je například Context. TimeStamp. Day = pondělí nebo "Context. TimeStamp. hour" = "13", jsou užitečnější. Pro každý z nich bude k dispozici maximálně 7 nebo 24 hodnot funkcí. Ale "Context. TimeStamp": "1985-04-12T23:20:50.52 Z" je tak přesným způsobem, že se z něj nebudete moct učit, protože k tomu nikdy nedojde.

## <a name="next-steps"></a>Další kroky

Informace o [škálovatelnosti a výkonu](concepts-scalability-performance.md) pomocí přizpůsobení

