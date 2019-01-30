---
title: Pokyny pro přepis pro vzdělávání Speech Service
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak připravit text pro přizpůsobení akustických a jazykových modelů a hlasová písma pro službu rozpoznávání řeči.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: panosper
ms.openlocfilehash: 44f2cb9e95a9a79c85c23b0eebd919851b94c1f8
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228130"
---
# <a name="transcription-guidelines-for-using-the-speech-service"></a>Přepis pokyny k používání Speech Service

Chcete-li přizpůsobit **převod řeči na Text** nebo **převod textu na řeč**, je nutné zadat text spolu s řeči. Každý řádek v textu odpovídá jedné utterance. Text by měl odpovídat řeč co nejpřesněji. Text je volána *přepisu*, a je třeba jej vytvořit v určitém formátu.

Speech Service normalizuje vstup pro zachování konzistence text. 

Tento článek popisuje oba typy normalizations. Pravidla se mírně liší pro jednotlivé jazyky.

## <a name="us-english-en-us"></a>Jazykovou verzi US English (en-us)

Textová data, by měly být zapsány utterance jeden na řádek, ve formátu prostého textu, pomocí pouze znaková sada ASCII.

Vyhněte se použití rozšířené (Latin-1) nebo interpunkční znaky znakové sady Unicode. Tyto znaky může být zahrnuta neúmyslně při přípravě dat v textovém editoru nebo scrape data z webové stránky. Nahraďte příslušné náhrady ASCII znaky. Příklad:

| Aby se zabránilo znaků | Nahrazení |
|----- | ----- |
| "Hello world" (levá a pravá dvojité uvozovky) | "Hello world" (dvojité uvozovky) |
| John's na den (vpravo jednoduché uvozovky) | John's na den (apostrof) |
| bylo dobré – Ne, bylo vynikající! (pomlčku) | bylo dobré – Ne, bylo vynikající! (pomlčky) |

### <a name="text-normalization-rules-for-english"></a>Text pravidel normalizace pro angličtinu

Speech Service provádí následující normalizace pravidla:

* Použití malých písmen pro veškerý text
* Odebrat všechny interpunkční znaménka s výjimkou Wordu interní apostrofy
* Rozbalení čísla do mluvené řeči formuláře, včetně korunách

Zde je několik příkladů:

| Původní text | Po normalizace |
|----- | ----- |
| "Tichá krávy!" uvedené Batman. | tichá krávy říká, že batman |
| "Co?" uvedené Batman sidekick dotazování. | jaké uvedené batman sidekick dotazování |
| Go get - em! | em přejděte get |
| Já jsem double-jointed | Já jsem double jointed |
| 104 Elm Street | jeden ale čtyři jilm ulice |
| Vyladění 102.7 | ladění na jednu ale dvě sedm |
| Pi je asi 3.14 | Pi je přibližně tři čtyři jeden bod |
| To stojí $3.14 | to stojí tři čtrnáct |

Platí následující normalizace pro vaše záznamy o studiu text:

* Zkratky by měl být zapsaná ve slovech.
* Nestandardní číselné řetězce (například některé datum nebo formuláře monitorování účtů) by měl zapsaná ve slovech.
* Slov s neabecedními znaky nebo smíšené alfanumerické znaky by měla být přepsána jako výraznější.
* Ponechte zkratek, které jsou vyslovuje se jako slova beze změny (například "této možnosti taky přemýšlíte," "laserové," "RAM" nebo "NATO").
* Zápis zkratek, které jsou vyslovováno jako samostatné písmena s písmeny oddělených mezerami (třeba "IBM," "CPU", "FBI", "TBD" nebo "Není číslo"). 

Zde je několik příkladů:

| Původní text | Po normalizace |
|----- | ----- |
| 14 NE 3rd Dr. | čtrnáct severovýchod třetí disk |
| Dr. Bruce Banner | Banner Bruce lékař |
| James Bond, 007 | James Bond dvakrát ale sedm |
| Ke$ha | Kesha |
| Jak dlouhé je 2 × 4 | Jak dlouhé je pomocí čtyř |
| Schůzky přejde z 1 – 15: 00 | Schůzky přechází z jedné do tří hodin |
| Můj typ krve je O + | Můj typ krve je O pozitivní |
| hodnota je H20 | hodnota je H 2 O |
| Přehrát OU812 podle Van Halen | Přehrát U O 8 1 2 Van Halen |
| UTF-8 s BOM | U T F 8 s BOM |

## <a name="chinese-zh-cn"></a>Čínština (zh-cn)

Textová data, která se nahraje do služby Custom Speech Service používali s značky pořadí bajtů kódování UTF-8. Soubor by měly být zapsány jeden utterance každý řádek.

Nepoužívejte poloviční šířku interpunkční znaménka. Tyto znaky může být zahrnuta neúmyslně při přípravě dat v textovém editoru nebo scrape data z webové stránky. Nahraďte je vhodné náhrady plnou šířkou. Příklad:

| Aby se zabránilo znaků | Nahrazení |
|----- | ----- |
| "你好" (levá a pravá dvojité uvozovky) | "你好" (dvojité uvozovky) |
| 需要什么帮助? (otazník) | 需要什么帮助？ |

### <a name="text-normalization-rules-for-chinese"></a>Text pravidel normalizace pro čínštinu

Speech Service provádí následující normalizace pravidla:

* Odebrat všechny interpunkční znaménka
* Rozbalení čísla do mluvené řeči formuláře
* Převod na poloviční šířku písmena plnou šířkou
* Použití velkých písmen pro všechny anglická slova

Zde je několik příkladů:

| Původní text | Po normalizace |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

Předtím, než importujete textu, aby u ní použil normalizace následující:

* Zkratky by měl být napsána v slova (stejně jako v mluvené slovo formulář).
* Vypsat číselných řetězců v podobě mluvené slovo.

Zde je několik příkladů:

| Původní text | Po normalizace |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Jiné jazyky

Textová data nahrát do **převod řeči na Text** služba musí použít s značky pořadí bajtů kódování UTF-8. Soubor by měly být zapsány jeden utterance každý řádek.

> [!NOTE]
> Následující příklady používají němčina. Ale pokyny platí pro všechny jazyky, které nejsou Angličtina (USA) nebo čínština.

### <a name="text-normalization-rules-for-german"></a>Text pravidel normalizace pro němčinu

Speech Service provádí následující normalizace pravidla:

* Použití malých písmen pro veškerý text
* Odebrat všechny interpunkční znaménka, včetně různých typů uvozovky ("test", "test", "test" a "test" jsou OK)
* Rušení řádků pomocí libovolného speciálního znaku ze sady z ¤ y ¦ smyslu © ª ¬® ° rozmezí ² µ × ÿ Ø¬¬
* Rozšíření čísla do tvaru, včetně dolar nebo eurech
* Přijímá samohlásky pouze u, e a vy; ostatní se nahradí "th" nebo být zahozeny

Zde je několik příkladů:

| Původní text | Po normalizace |
|----- | ----- |
| Aktualizační kanál Frankfurter | aktualizační kanál frankfurter |
| ¡Eine Frage! | eine frage |
| WIR haben | WIR haben |

Předtím, než importujete textu, aby u ní použil normalizace následující:

* Desetinné čárky by měl být ","a".".
* Oddělovače času mezi hodinách a minutách by měl být ":"a"." (například 12:00 Uhr).
* Zkratky jako "ca". nejsou nahrazeny. Doporučujeme použít úplný formát.
* Čtyři hlavní matematické operátory (+, -, \*, a /) se odeberou. Doporučujeme nahradit jejich literálu formuláře: "plus," "minus," "zjišťování" a "geteilt."
* Stejné pravidlo platí pro operátory porovnání (=, <, a >). Doporučujeme nahradit "gleich", "kleiner als," a "grösser als."
* Použijte zlomky, jako je například 3/4 ve tvaru (například "drei viertel" namísto ¾).
* € Symbol nahraďte formulářem, slovo "Euro."

Zde je několik příkladů:

| Původní text | Po normalizace uživatele | Po normalizace systému
|--------  | ----- | -------- |
| Uhr TIS 12.23 ES | ES TIS 12:23 Uhr | ES TIS zwölf uhr drei a zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf ||
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|

## <a name="next-steps"></a>Další postup

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznávání řeči v jazyce C#](quickstart-csharp-dotnet-windows.md)
