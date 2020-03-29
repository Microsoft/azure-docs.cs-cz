---
title: Pokyny pro přepisy označené člověkem - Služba řeči
titleSuffix: Azure Cognitive Services
description: Chcete-li zlepšit přesnost rozpoznávání řeči, například když jsou slova odstraněna nebo nesprávně nahrazena, můžete spolu se zvukovými daty použít přepisy označené člověkem. Přepisy označené člověkem jsou slovo za slovem, doslovné přepisy zvukového souboru.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 81b4ffc8f77673e52bb78f891e3de618b67e0d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806058"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Jak vytvořit přepisy označené člověkem

Pokud chcete zlepšit přesnost rozpoznávání, zejména problémy, které jsou způsobeny odstraněním nebo nesprávným nahrazením slov, budete chtít spolu se zvukovými daty použít přepisy označené člověkem. Co jsou přepisy označené člověkem? To je snadné, jsou to slovo-by-slovo, doslovný přepis zvukového souboru.

Pro zlepšení rozpoznávání je zapotřebí velký vzorek dat o přepisu, doporučujeme poskytnout 10 až 1 000 hodin dat o přepisu. Na této stránce si prověříme pokyny, které vám pomohou vytvářet vysoce kvalitní přepisy. Tato příručka je rozdělena podle národního prostředí s oddíly pro americkou angličtinu, mandarínskou čínštinu a němčinu.

## <a name="us-english-en-us"></a>Americká angličtina (en-US)

Přepisy pro anglický zvuk označené člověkem musí být poskytovány jako prostý text, pouze pomocí znaků ASCII. Vyhněte se použití interpunkčních znaků latinky 1 nebo Unicode. Tyto znaky jsou často neúmyslně přidány při kopírování textu z textového editoru nebo škrábání dat z webových stránek. Pokud jsou tyto znaky k dispozici, nezapomeňte je aktualizovat příslušným nahrazením ASCII.

Tady je pár příkladů:

| Znaky, kterým je třeba se vyhnout | Substituce | Poznámky |
| ------------------- | ------------ | ----- |
| "Dobrý svět" | "Dobrý svět" | Počáteční a uzavírací uvozovky byly nahrazeny příslušnými znaky ASCII. |
| Johnův den | Johnův den | Apostrof byl nahrazen příslušným znakem ASCII. |
| bylo to dobré - ne, bylo to skvělé! | bylo to dobré - ne, bylo to skvělé! | Pomlčka em byla nahrazena dvěma spojovníky. |

### <a name="text-normalization-for-us-english"></a>Normalizace textu pro americkou angličtinu

Normalizace textu je transformace slov do konzistentního formátu používaného při trénování modelu. Některá pravidla normalizace se používají na text automaticky, doporučujeme však používat tyto pokyny při přípravě dat přepisu s lidským označením:

- Napište zkratky slovy.
- Zapište nestandardní číselné řetězce slovy (například účetními termíny).
- Neabecední znaky nebo smíšené alfanumerické znaky by měly být přepsány jako vyslovované.
- Zkratky, které jsou vyslovovány jako slova, by neměly být upravovány (například "radar", "laser", "RAM" nebo "NATO").
- Zapište zkratky, které jsou vyslovovány jako samostatná písmena s každým písmenem odděleným mezerou.

Zde je několik příkladů normalizace, které byste měli provést na přepisu:

| Původní text               | Text po normalizaci              |
| --------------------------- | ------------------------------------- |
| Dr. Bruce Banner            | Doktor Bruce Banner                   |
| James Bond, 007             | James Bond, dvojitý oh sedm           |
| Ke$ha                       | Kesha                                 |
| Jak dlouhá je 2x4         | Jak dlouho je dva o čtyři           |
| Setkání probíhá od 13:00 do 15:00 | Schůzka se probíhá od jedné do tří hodin. |
| Moje krevní skupina je O+         | Moje krevní skupina je 0 pozitivní           |
| Voda je H20                | Voda je H 2 O                        |
| Hrát OU812 od Van Halen     | Hrát O U 8 1 2 od Van Halen           |
| UTF-8 s BOM              | U T F 8 s kusovníkem                      |

Na přepisy se automaticky použijí následující normalizační pravidla:

- Používejte malá písmena.
- Odstraňte všechny interpunkce kromě apostrofů ve slovech.
- Rozbalte čísla do slova / mluvené formě, například částky v dolarech.

Zde je několik příkladů normalizace automaticky provádí na transkripci:

| Původní text                          | Text po normalizaci          |
| -------------------------------------- | --------------------------------- |
| "Svatá krávo!" řekl Batman.               | svatá kráva řekla batman              |
| "Cože?" řekl Batmanův parťák, Robin. | co řekl Batman ův parťák robin |
| Jdi pro-em!                            | jít si em                         |
| Mám dvojité klouby.                     | Jsem dvojkloubová.                |
| 104 Jilmská ulice                         | jedna oh čtyři Elm ulice            |
| Nalaďte na 102,7                          | naladit na jeden oh dva bod sedm    |
| Pí je asi 3,14                       | pi je asi tři bod jeden čtyři  |
| Stojí \$3,14                        | stojí tři čtrnáct           |

## <a name="mandarin-chinese-zh-cn"></a>Mandarínská čínština (zh-CN)

Přepisy pro mandarínskou čínštinu označené člověkem musí být kódovány značkou pořadí bajtů. Vyhněte se použití interpunkčních znaků s poloviční šířkou. Tyto znaky mohou být zahrnuty neúmyslně při přípravě dat v textovém editoru nebo škrábání dat z webových stránek. Pokud jsou tyto znaky k dispozici, nezapomeňte je aktualizovat s příslušnou náhradou celé šířky.

Tady je pár příkladů:

| Znaky, kterým je třeba se vyhnout | Substituce   | Poznámky |
| ------------------- | -------------- | ----- |
| ""?" | ""?" | Počáteční a uzavírací uvozovky byly nahrazeny příslušnými znaky. |
| V. | V.| Otazník byl nahrazen vhodnou povahou. |

### <a name="text-normalization-for-mandarin-chinese"></a>Normalizace textu pro mandarínskou čínštinu

Normalizace textu je transformace slov do konzistentního formátu používaného při trénování modelu. Některá pravidla normalizace se používají na text automaticky, doporučujeme však používat tyto pokyny při přípravě dat přepisu s lidským označením:

- Napište zkratky slovy.
- Zapište číselné řetězce v mluvené podobě.

Zde je několik příkladů normalizace, které byste měli provést na přepisu:

| Původní text | Text po normalizaci |
| ------------- | ------------------------ |
| 21. | 中中中中中中 |
| 3 504. | 三号 楼 五 零 四 |

Na přepisy se automaticky použijí následující normalizační pravidla:

- Odebrat všechny interpunkce
- Rozšíření čísel do mluveného formuláře
- Převod písmen s plnou šířkou na písmena s poloviční šířkou
- Použití velkých písmen pro všechna anglická slova

Zde je několik příkladů normalizace automaticky provádí na transkripci:

| Původní text | Text po normalizaci |
| ------------- | ------------------------ |
| 3.1415 | V ěn |
| ¥ 3,5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992 中 8 中 8 中 | V ěn |
| ??? | V ěn |
| V 5:00 | 中中中 中中 中中 |
| 21. | 中中中中中中中中 |

## <a name="german-de-de-and-other-languages"></a>Němčina (de-DE) a další jazyky

Přepisy označené člověkem pro německý zvuk (a jiné neanglické nebo mandarínské čínské jazyky) musí být kódovány značkou pořadí bajtů. Pro každý zvukový soubor by měl být poskytnut jeden přepis s lidským označením.

### <a name="text-normalization-for-german"></a>Normalizace textu pro němčinu

Normalizace textu je transformace slov do konzistentního formátu používaného při trénování modelu. Některá pravidla normalizace se používají na text automaticky, doporučujeme však používat tyto pokyny při přípravě dat přepisu s lidským označením:

- Napište desetinné čárky jako "", a ne ".".
- Napište oddělovače času jako ":" a ne "." (například: 12:00 Uhr).
- Zkratky jako "ca". nejsou nahrazeny. Doporučujeme použít celý mluvený formulář.
- Čtyři hlavní matematické operátory (+, -, \*, a /) jsou odebrány. Doporučujeme je nahradit písemnou formou: "plus", "minus", "mal" a "geteilt".
- Operátory porovnání jsou odebrány (=, < a >). Doporučujeme je nahradit "gleich", "kleiner als" a "grösser als".
- Napište zlomky, například 3/4, v písemné formě (například" drei viertel" místo 3/4).
- Nahraďte symbol "€" jeho psanou formou "Euro".

Zde je několik příkladů normalizace, které byste měli provést na přepisu:

| Původní text    | Text po normalizaci uživatele | Text po normalizaci systému       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12,23 Uhr | Es ist 12:23 Uhr              | es ist zwölf uhr drei und zwanzig uhr |
| {12.45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3 - 4        | 2 plus 3 mínus 4              | zwei plus drei mínus vier             |

Na přepisy se automaticky použijí následující normalizační pravidla:

- Pro veškerý text použijte malá písmena.
- Odstraňte všechny interpunkce, včetně různých typů uvozovek ("test", "test", "test" a «test» jsou v pořádku).
- Zlikvidujte řádky s libovolnými speciálními znaky z této sady: ¢ ¤ ¥ ¦ § © ¬ ® ° ± ² μ × ÿ Ø ¬¬.
- Rozbalte čísla na mluvenou formu, včetně částek v dolarech nebo eurech.
- Přijměte přehlásky pouze pro a, o a vy. Ostatní budou nahrazeny "th" nebo budou zlikvidovány.

Zde je několik příkladů normalizace automaticky provádí na transkripci:

| Původní text    | Text po normalizaci |
| ---------------- | ------------------------ |
| Frankfurtský prsten | frankfurtský prsten         |
| Eine Frage!     | eine fráž               |
| wir, haben       | wir haben                |

## <a name="next-steps"></a>Další kroky

- [Příprava a testování dat](how-to-custom-speech-test-data.md)
- [Kontrola dat](how-to-custom-speech-inspect-data.md)
- [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)
- [Trénování vašeho modelu](how-to-custom-speech-train-model.md)
- [Nasazení modelu](how-to-custom-speech-deploy-model.md)
