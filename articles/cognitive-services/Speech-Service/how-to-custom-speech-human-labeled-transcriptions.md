---
title: Pokyny pro lidské s názvem bez přípony přepisů – hlasové služby
titlesuffix: Azure Cognitive Services
description: Pokud chcete zlepšit přesnost rozpoznávání, zejména problémy, které jsou způsobeny, když slova jsou odstraněny nebo nesprávně nahrazena, budete chtít použít lidských s názvem bez přípony přepisů spolu s zvuková data. Co jsou označené jako lidských přepisů? Je to jednoduché, jsou aplikace word ve Wordu, verbatim přepisů zvukový soubor.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7f0b467284872f3d936984741c6d092705008a5a
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025919"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Jak vytvořit lidských s názvem bez přípony přepisů

Pokud chcete zlepšit přesnost rozpoznávání, zejména problémy, které jsou způsobeny, když slova jsou odstraněny nebo nesprávně nahrazena, budete chtít použít lidských s názvem bez přípony přepisů spolu s zvuková data. Co jsou označené jako lidských přepisů? Je to jednoduché, jsou aplikace word ve Wordu, verbatim přepisů zvukový soubor.

Ukázku velkých dat určené k transkripci je vyžadováno pro zlepšení rozpoznávání, doporučujeme poskytuje rozmezí 10 až 1 000 hodin dat určené k transkripci. Na této stránce si probereme pokyny, které jsou navržené tak, aby vám pomůžou vytvořit vysoce kvalitní přepisů. Tato příručka je rozdělený podle národního prostředí a s oddíly pro Americkou angličtinu, Mandarínština čínštiny a němčina.

## <a name="us-english-en-us"></a>Jazykovou verzi US English (en US)

Lidské s názvem bez přípony přepisy pro anglickou zvuk musí být uvedeny jako prostý text, použití pouze znaky ASCII. Nepoužívejte Latin-1 nebo Unicode znaky interpunkce. Tyto znaky jsou často nechtěně přidány po zkopírování textu z textového procesoru nebo automatizované získávání dat data z webové stránky. Pokud tyto znaky jsou k dispozici, ujistěte se, že aktualizace pomocí odpovídající nahrazení ASCII.

Tady je pár příkladů:

| Aby se zabránilo znaků | Nahrazení | Poznámky |
|---------------------|--------------|-------|
| "Hello world" | "Hello world" | Uvozovky otevírací a zavírací byla nahrazena s odpovídající znaky ASCII. |
| John's na den | John's na den | Apostrof má byla nahrazena příslušný znak ASCII. |
| bylo dobré – Ne, bylo vynikající! | bylo dobré – Ne, bylo vynikající! | Dlouhé pomlčky byla nahrazena dva spojovníky. |

### <a name="text-normalization-for-us-english"></a>Text normalizace pro Americkou angličtinu

Normalizace text je transformace slova do konzistentní formát používaný při cvičení modelu. Některá pravidla normalizace se použijí na text automaticky, ale doporučujeme pomocí těchto pokynů, jak připravit data přepis lidských s názvem bez přípony:

* Vypsat zkratky ve slovech.
* Vypsat nestandardní číselných řetězců v slova (jako jsou podmínky monitorování účtů).
* Jako vyslovováno by měla být přepsána interpunkční znaky nebo smíšené alfanumerické znaky.
* Zkratky, které jsou vyslovuje se jako slova by neměla být upravována (například "této možnosti taky přemýšlíte", "laserové", "RAM" nebo "NATO").
* Zápis si zkratek, které jsou vyslovuje se jako samostatné písmena každé písmeno oddělené mezerou.

Tady je pár příkladů normalizace, který by měl provádět na přepis:

| Původní text | Text za normalizace |
|---------------|--------------------------|
| Dr. Bruce Banner | Banner Bruce lékař |
| James Bond, 007 | James Bond dvakrát ale sedm |
| Ke$ha | Kesha |
| Jak dlouhé je 2 × 4 | Jak dlouhé je pomocí čtyř |
| Schůzky přejde z 1 – 15: 00 | Schůzky přechází z jedné do tří hodin |
| Můj typ krve je O + | Můj typ krve je O pozitivní |
| hodnota je H20 | hodnota je H 2 O |
| Přehrát OU812 podle Van Halen | Přehrát U O 8 1 2 Van Halen |
| UTF-8 s BOM | U T F 8 s BOM |

Přepisy se automaticky použijí následující pravidla normalizace:

* Používejte malá písmena.
* Odeberte všechny interpunkční znaménka s výjimkou apostrofy v rámci slova.
* Do formuláře slyšet/slova, jako je například korunách rozbalte čísla.

Tady je pár příkladů normalizace automaticky provedeno přepis:

| Původní text | Text za normalizace |
|---------------|--------------------------|
| "Tichá krávy!" uvedené Batman. | tichá krávy říká, že batman |
| "Co?" uvedené Batman sidekick dotazování. | jaké uvedené batman sidekick dotazování |
| Go get - em! | em přejděte get |
| Já jsem double-jointed | Já jsem double jointed |
| 104 Elm Street | jeden ale čtyři jilm ulice |
| Vyladění 102.7 | ladění na jednu ale dvě sedm |
| Pi je asi 3.14 | Pi je přibližně tři čtyři jeden bod |
To stojí $3.14| to stojí tři čtrnáct |

## <a name="mandarin-chinese-zh-cn"></a>Mandarínština čínština (zh-cn)

Lidské s názvem bez přípony přepisy pro čínské zvuk Mandarínština musí být kódovaný pomocí značky pořadí bajtů kódování UTF-8. Nepoužívejte poloviční šířku interpunkční znaménka. Tyto znaky může být zahrnuta neúmyslně při přípravě dat v textovém editoru nebo scrape data z webové stránky. Pokud tyto znaky jsou k dispozici, ujistěte se, že aktualizace pomocí odpovídající nahrazení plnou šířkou.

Tady je pár příkladů:

| Aby se zabránilo znaků | Nahrazení | Poznámky |
|---------------------|--------------|-------|
| "你好" | "你好" | Uvozovky otevírací a zavírací byla nahrazena odpovídající znaky. |
| 需要什么帮助? | 需要什么帮助？ | Otazník má byla nahrazena odpovídající znak. |

### <a name="text-normalization-for-mandarin-chinese"></a>Text normalizace pro Mandarínština čínštinu

Normalizace text je transformace slova do konzistentní formát používaný při cvičení modelu. Některá pravidla normalizace se použijí na text automaticky, ale doporučujeme pomocí těchto pokynů, jak připravit data přepis lidských s názvem bez přípony:

* Vypsat zkratky ve slovech.
* Vypsat číselných řetězců v podobě mluvené slovo.

Tady je pár příkladů normalizace, který by měl provádět na přepis:

| Původní text | Text za normalizace |
|---------------|--------------------------|
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

Přepisy se automaticky použijí následující pravidla normalizace:

* Odebrat všechny interpunkční znaménka
* Rozbalte čísla do mluvené řeči formuláře
* Převést na poloviční šířku písmena plnou šířkou
* Použití velkých písmen pro všechny anglická slova

Tady je pár příkladů normalizace automaticky provedeno přepis:

| Původní text | Text za normalizace |
|---------------|--------------------------|
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z |W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Němčina (de-DE) a další jazyky

Lidské s názvem bez přípony přepisů německé zvuku (a ostatní jiné než anglické jazykové nebo Mandarínština čínskými jazyky) musí být kódovaný pomocí značky pořadí bajtů kódování UTF-8. Pro každý zvukový soubor musí být zadána jedna přepisu lidských s názvem bez přípony.

### <a name="text-normalization-for-german"></a>Text normalizace pro němčinu

Normalizace text je transformace slova do konzistentní formát používaný při cvičení modelu. Některá pravidla normalizace se použijí na text automaticky, ale doporučujeme pomocí těchto pokynů, jak připravit data přepis lidských s názvem bez přípony:

*   Zápis desetinné čárky jako ","a".".
*   Zápis oddělovače času jako ":"a"." (například: 12:00 Uhr).
*   Zkratky jako "ca". nejsou nahrazeny. Doporučujeme použít úplný formát mluvené slovo.
*   Čtyři hlavní matematické operátory (+, -, \*, a /) se odeberou. Doporučujeme nahradit psané formy: "plus," "minus," "zjišťování" a "geteilt."
*   Operátory porovnání jsou odebrány. (=, <, a >). Doporučujeme nahradit "gleich", "kleiner als," a "grösser als."
*   Zápis zlomky, jako je například 3/4 v písemné formě (například: "drei viertel" namísto 3/4).
*   Symbol "do" nahraďte její psané formy "Euro."

Tady je pár příkladů normalizace, který by měl provádět na přepis:

| Původní text | Text za normalizace uživatele | Text za normalizace systému |
|---------------|-------------------------------|---------------------------------|
| Uhr TIS 12.23 ES | ES TIS 12:23 Uhr | ES TIS zwölf uhr drei a zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier |

Přepisy se automaticky použijí následující pravidla normalizace:

* Použijte pro veškerý text malými písmeny.
* Odebrat všechny interpunkční znaménka, včetně různých typů uvozovky ("test", "test", "test" a "test" jsou OK).
* Zahodit řádky s žádné speciální znaky z této sady: z ¤ y ¦ smyslu © ª ¬® ° rozmezí ² µ × ÿ Ø¬¬.
* Rozbalte čísla do mluvené řeči formuláře, včetně dolar nebo eurech.
* Přijměte samohlásky pouze u, e a vy. Ostatní se nahradí "th" nebo zahozeny.

Tady je pár příkladů normalizace automaticky provedeno přepis:

| Původní text | Text za normalizace |
|---------------|--------------------------|
| Aktualizační kanál Frankfurter | aktualizační kanál frankfurter |
| ¡Eine Frage! | eine frage |
| WIR haben | WIR haben |

## <a name="next-steps"></a>Další kroky

* [Připravit a otestovat vašich dat](how-to-custom-speech-test-data.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnocení vašich dat](how-to-custom-speech-evaluate-data.md)
* [Vyzkoušejte svůj model](how-to-custom-speech-train-model.md)
* [Nasazení modelu](how-to-custom-speech-deploy-model.md)
