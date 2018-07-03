---
title: Pokyny určené k transkripci řeči | Dokumentace Microsoftu
description: Zjistěte, jak připravit text pro přizpůsobení akustických a jazykových modelů a hlasová písma pro službu rozpoznávání řeči.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: PanosPeriorellis
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/01/2018
ms.author: panosper
ms.openlocfilehash: acca6f4cd2f4e7b452f5a70457d3d034e4d4aa7e
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345181"
---
# <a name="transcription-guidelines-for-using-speech-service"></a>Přepis pokyny k používání Speech service

Chcete-li přizpůsobit **převod řeči na Text** nebo **převod textu na řeč**, je nutné zadat text spolu s řeči. Každý řádek v textu odpovídá jedné utterance. Text by měl odpovídat řeč jako přesně nejvíce. Text je volána *přepisu*, a je třeba jej vytvořit v určitém formátu.

Speech service normalizuje vstup pro zachování konzistence text. 

Tento článek popisuje oba typy normalizations. Pravidla se mírně liší pro jednotlivé jazyky.

## <a name="us-english-en-us"></a>Jazykovou verzi US English (en US)

Textová data, by měly být zapsány utterance jeden na řádek, ve formátu prostého textu, pomocí pouze znaková sada ASCII.

Vyhněte se použití rozšířené (Latin-1) nebo interpunkční znaky znakové sady Unicode. Tyto znaky může být zahrnuta neúmyslně při přípravě dat v textovém editoru nebo automatizované získávání dat data z webové stránky. Nahraďte tyto znaky náhrady odpovídající ASCII. Příklad:

| Aby se zabránilo znaků | Nahrazení |
|----- | ----- |
| "Hello world" (otevírají a zavírají dvojité uvozovky) | "Hello world" (dvojité uvozovky) |
| John's na den (vpravo jednoduché uvozovky) | John's na den (apostrof) |
| bylo dobré – Ne, bylo vynikající! (pomlčku) | bylo dobré – Ne, bylo vynikající! (pomlčky) |

### <a name="text-normalization-rules-for-english"></a>Text pravidel normalizace pro angličtinu

Speech service provádí následující pravidla normalizace.

*   Malá písmena veškerý text
*   Odebrat všechny interpunkční znaménka s výjimkou Wordu interní apostrofy
*   Rozšíření čísel do mluvené řeči formuláře, včetně korunách

Tady je několik příkladů

| Původní Text | Po normalizace |
|----- | ----- |
| "Tichá krávy!" uvedené Batman. | tichá krávy říká, že batman |
| "Co?" uvedené Batman sidekick dotazování. | jaké uvedené batman sidekick dotazování |
| Go get - em! | em přejděte get |
| Já jsem double-jointed | Já jsem double jointed |
| Ulice jilm 104 | jeden ale čtyři jilm ulice |
| Vyladění 102.7 | ladění na jednu ale dvě sedm |
| Pi je asi 3.14 | Pi je přibližně tři čtyři jeden bod |
| To stojí $3.14 | to stojí tři čtrnáct |

Platí následující normalizace pro vaše záznamy o studiu text.

*   Zkratky by měl být napsána v slova
*   Nestandardní číselné řetězce (například některé datum nebo formuláře monitorování účtů) by měl uvádět slova
*   Slov s neabecedními znaky nebo smíšené alfanumerické znaky by měla být přepsána, jak vyslovováno
*   Ponechte zkratky vyslovuje se jako slova beze změny. Například této možnosti taky přemýšlíte, laserové, paměti RAM, NATO.
*   Zápis zkratky vyslovuje se jako samostatné písmena s písmeny, oddělené mezerami. Například, IBM, procesoru, úřadu FBI, TBD, NaN. 

Zde je několik příkladů:

| Původní Text | Po normalizace |
|----- | ----- |
| 14 NE 3rd zotavení po havárii. | čtrnáct severovýchod třetí disk |
| Dr. Bruce Banner | Banner Bruce lékař |
| James Bond, 007 | James Bond dvakrát ale sedm |
| Ke$ ha | Kesha |
| Jak dlouhé je 2 × 4 | Jak dlouhé je pomocí čtyř |
| Schůzky přejde z 1 – 15: 00 | Schůzky přechází z jedné do tří hodin |
| Můj typ krve je O + | Můj typ krve je O pozitivní |
| hodnota je H20 | hodnota je H 2 O |
| Přehrát OU812 podle Van Halen | Přehrát U O 8 1 2 Van Halen |
| UTF-8 s BOM | U T F 8 s BOM |

## <a name="chinese-zh-cn"></a>Čínština (zh-CN)

Textová data nahrát do služby Custom Speech Service používali s značky pořadí bajtů kódování UTF-8. Soubor by měly být zapsány jeden utterance každý řádek.

Nepoužívejte poloviční šířku interpunkční znaménka. Tyto znaky může být zahrnuta neúmyslně při přípravě dat v textovém editoru nebo automatizované získávání dat data z webové stránky. Nahraďte je vhodné náhrady plnou šířkou. Příklad:

| Aby se zabránilo znaků | Nahrazení |
|----- | ----- |
| "你好" (otevření a zavření dvojité uvozovky) | "你好" (dvojité uvozovky) |
| 需要什么帮助? (otazník) | 需要什么帮助? |

### <a name="text-normalization-rules-for-chinese"></a>Text pravidel normalizace pro čínštinu

Speech service provádí následující pravidla normalizace.

*   Odebrat všechny interpunkční znaménka
*   Rozbalení čísla do mluvené řeči formuláře
*   Převod na poloviční šířku písmena plnou šířkou
*   Velká písmena všech slov v angličtině

Zde je několik příkladů:

| Původní Text | Po normalizace |
|----- | ----- |
| 3,1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

Platí následující normalizace pro text před importem.

*   Zkratky by měl být napsána v slova (stejně jako v mluvené slovo formuláře)
*   Vypsat číselných řetězců v podobě mluvené slovo.

Zde je několik příkladů:

| Původní Text | Po normalizace |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Jiné jazyky

Textová data nahrát do **převod řeči na Text** služba musí použít s značky pořadí bajtů kódování UTF-8. Soubor by měly být zapsány jeden utterance každý řádek.

> [!NOTE]
> Tyto příklady používají němčina. Ale tyto pokyny platí pro všechny jazyky, které nejsou Angličtina (USA) nebo čínština.

### <a name="text-normalization-rules-for-german"></a>Text pravidel normalizace pro němčinu

Speech service provádí následující pravidla normalizace.

*   Malá písmena veškerý text
*   Odebírá všechny interpunkční znaménka, včetně různých typů uvozovky ("test", "test", "test" nebo "test" jsou v pořádku)
*   Rušení řádků pomocí libovolného speciálního znaku ze sady z ¤ y ¦ smyslu © ª ¬® ° rozmezí ² µ × ÿ Ø¬¬
*   Rozšíření čísel, tvaru, včetně dolar nebo euro objemy
*   Samohlásky jsou přijaty pouze u, e, u; ostatní se nahradí "th" nebo být zahozeny

Tady je několik příkladů

| Původní Text | Po normalizace |
|----- | ----- |
| Aktualizační kanál Frankfurter | aktualizační kanál frankfurter |
| ¡Eine Frage! | eine frage |
| WIR haben | WIR haben |

Platí následující normalizace pro text před importem.

*   Desetinné čárky by měl být ","a"."
*   Oddělovač času mezi hodiny a minuty musí být ":"a".": 12:00 Uhr
*   Zkratky jako "certifikační autorita." nejsou nahrazeny. Doporučujeme, abyste že použijte úplný formát.
*   Pět hlavních matematické operátory jsou odebrány: +, -, \*, /. Doporučujeme nahradit jejich literálu formuláře: plus/minus mal geteilt.
*   To samé platí pro operátory porovnání (=, <>,) - gleich kleiner als, grösser als
*   Použití zlomky, jako je například 3/4 ve tvaru (například "drei viertel" namísto ¾)
*   Nahraďte formulářem, slovo "Euro" € symbol

Zde je několik příkladů:

| Původní Text | Po normalizace uživatele | Po normalizace systému
|--------  | ----- | -------- |
| ES TIS 12.23Uhr | 12:23Uhr TIS ES | ES TIS zwölf uhr drei a zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf ||
| 2 + 3-4 | 2 a 3 minus 4 | zwei plus drei minus vier|

## <a name="next-steps"></a>Další postup

- [Získejte zkušební verzi předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznávání řeči v jazyce C#](quickstart-csharp-windows.md)
