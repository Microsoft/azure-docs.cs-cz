---
title: Přepis pokyny pro rozpoznávání řeči školení | Microsoft Docs
description: Zjistěte, jak připravit text pro přizpůsobení akustickými a modely jazyk a hlas písem pro službu řeči.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 93ab7c81a773f692b2b970bb1901d82b7aceb5a2
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "35343936"
---
# <a name="transcription-guidelines-for-using-speech-service"></a>Přepis pokyny k používání služby řeči

Chcete-li přizpůsobit **řeči na Text** nebo **převod textu na řeč**, je nutné zadat text společně s řeči. Každý řádek v textu odpovídá jedné utterance. Text se nazývá *přepis*, a musíte ji vytvořit v konkrétním formátu.

Služba rozpoznávání řeči provádí některé normalizations vám tak, aby text v souladu. Před odesláním textu pro školení, je nutné provést další úlohy normalizace. 

Tento článek popisuje oba typy normalizations. Podle pokynů mírně lišit pro různé jazyky.

## <a name="us-english-en-us"></a>Čeština (cs cz)

Textová data do této služby nahrán by měly být zapsány ve formátu prostého textu pomocí pouze ASCII znaková sada. Každý řádek souboru by mělo obsahovat text pro jeden utterance.

Je důležité nepoužívejte rozšířené (Latinská-1) nebo interpunkční znaky kódování Unicode. Tyto znaky může být zahrnuta nechtěně při přípravě dat v textovém editoru nebo oškrabávání data z webové stránky. Tyto znaky nahraďte příslušné náhrady ASCII. Příklad:

| Aby se zabránilo znaků | Nahrazení |
|----- | ----- |
| "Hello, world" (otevírají a zavírají dvojité uvozovky) | "Hello, world" (dvojité uvozovky) |
| Jan pro den (vpravo jednoduché uvozovky) | Jan pro den (apostrof) |
| byl dobrý – Ne, bylo vynikající! (dlouhé pomlčky) | bylo dobrý – Ne, bylo vynikající! (pomlčky) |

### <a name="text-normalization-performed-by-the-service"></a>Text normalizaci provádí služba

Služba rozpoznávání řeči provádí následující normalizaci text na text přepisy.

*   Nižší-velká a malá písmena veškerého textu
*   Odebrání veškerou interpunkci s výjimkou Wordu interní apostrofy
*   Rozšíření čísel na mluvené formulář, včetně objemy dolar

Zde jsou některé příklady

| Původní Text | Po normalizaci |
|----- | ----- |
| Starbucks kávy | starbucks kávy |
| "Tichá kráva!" uvedené Batman. | tichá kráva uvedená batman |
| "Co?" uvedené Batman sidekick, každý s každým. | jaké uvedené batman sidekick dotazování |
| Přejděte get - em! | em přejděte get |
| Jsem double-jointed | i mě double otočně připevněna |
| 104 hlavní třída | jeden jejda čtyři hlavní ulice |
| Naladění 102.7 | vyladění na jednu jejda dva bodu sedm |
| Pi je o 3.14 | Pi je přibližně tři čtyři jeden bod |
| Náklady na něj $3.14 | náklady na něj tři čtrnáct |

### <a name="text-normalization-you-must-perform"></a>Normalizaci textu, které musíte provést

Následující normalizaci se vztahují na vaši přepisy text.

*   Zkratky by měl být napsána v slova, aby odrážela mluvené formuláře
*   Nestandardní číselných řetězců (například některé datum nebo forms monitorování účtů) by měla uvádět slova
*   By měla být přepsána slova s neabecedními znaky nebo smíšeném alfanumerické znaky, jako vyslovováno
*   Ponechte zkratky vyslovováno jako slova nezměněný. Například paprskového, laserové, paměti RAM, NATO a Mr.
*   Zápis zkratky vyslovováno jako samostatné písmena, s písmena oddělené mezerami. Například IBM, procesoru, FBI, bude Doplněn, NaN. 

Zde je několik příkladů:

| Původní Text | Po normalizaci |
|----- | ----- |
| 3. NE 14 zotavení po havárii. | čtrnáct severovýchod třetí jednotky |
| Dr. Strangelove | Doctor Strangelove |
| James dokumentových 007 | James dokumentových dvakrát jejda sedm |
| Nastavit jako$ ha | Kesha |
| Jak dlouho je 2 x 4 | Jak dlouho je pomocí čtyř |
| Schůzka přejde z – 3: 00 | Přejde schůzku od jedné do tří pm |
| Moje krve typ je O + | Moje krve typ je O kladné |
| horních je H20 | horních je H 2 O |
| přehrání OU812 podle Van Halen | přehrání U O 2 1 8 podle Van Halen |
| Znakové sady UTF-8 s BOM | U T F 8 s BOM |

## <a name="chinese-zh-cn"></a>Čínština (zh-CN)

Textová data odešlou do služby řeči vlastní měli používat s značky pořadí bajtů kódování UTF-8. Každý řádek souboru by mělo obsahovat text pro jeden utterance.

Je důležité k zamezení znaky interpunkce poloviční šířky. Tyto znaky může být zahrnuta nechtěně při přípravě dat v textovém editoru nebo oškrabávání data z webové stránky. Nahraďte je vhodné náhrady plnou šířkou. Příklad:

| Aby se zabránilo znaků | Nahrazení |
|----- | ----- |
| "你好" (otevření a zavření dvojité uvozovky) | "你好" (dvojité uvozovky) |
| 需要什么帮助? (otazník) | 需要什么帮助? |

### <a name="text-normalization-performed-by-the-service"></a>Text normalizaci provádí služba

Služba rozpoznávání řeči provádí následující normalizaci text na text přepisy.

*   Odebrání všech interpunkce
*   Rozšiřování čísla mluvené formuláře
*   Převod plnou šířkou písmena poloviční šířkou
*   Všechny horní-velká a malá písmena anglické slova

Zde je několik příkladů:

| Původní Text | Po normalizaci |
|----- | ----- |
| 3,1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

### <a name="text-normalization-you-must-perform"></a>Normalizaci textu, které musíte provést

Před importem se vztahují následující normalizaci na text.

*   Zkratky by měl být napsána v slova, aby odrážela mluvené formuláře
*   Tuto službu nezahrnuje všechny číselné počty. Je spolehlivější k zapsání číselných řetězců v mluvené formuláře.

Zde je několik příkladů:

| Původní Text | Po normalizaci |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Ostatní jazyky

Textová data nahrán do **řeči na Text** služby musíte použít s značky pořadí bajtů kódování UTF-8. Každý řádek souboru by mělo obsahovat text pro jeden utterance.

> [!NOTE]
> Tyto příklady použití němčina. Ale tyto pokyny platí pro všechny jazyky, které nejsou angličtinu nebo čínské.

### <a name="text-normalization-performed-by-the-service"></a>Text normalizaci provádí služba

Služba rozpoznávání řeči provádí následující normalizaci text na text přepisy.

*   Nižší-velká a malá písmena veškerého textu
*   Odebrání veškerou interpunkci, včetně různých typů uvozovky ("test", "test", "test" nebo "test" jsou v pořádku)
*   Zahození libovolný řádek obsahující zvláštních znaků ze sady ^ hodnot l ¤ y ¦ smyslu © ª ¬® ° rozmezí ² µ × ÿ Ø¬¬
*   Rozšíření čísla do aplikace word formuláře, včetně dolar nebo euro objemy
*   Samohlásky přijímají pouze pro a, e, u; ostatní budou nahrazeny "th" nebo zahozeny

Zde jsou některé příklady

| Původní Text | Po normalizaci |
|----- | ----- |
| Frankfurter prstenec | frankfurter prstenec |
| "Hallo, Mama!" sagt die Tochter. | hallo mama sagt die tochter |
| ¡Eine Frage! | eine frage |
| WIR haben | WIR haben |
| DAS macht 10 | DAS macht zehn odběru |

### <a name="text-normalization-you-must-perform"></a>Normalizaci textu, které musíte provést

Před importem se vztahují následující normalizaci na text.

*   Desetinné čárky by měl být ","a není".": 2,3 % a není 2.3 %
*   Oddělovač času mezi hodin a minut musí být ":"a není".": 12:00 Uhr
*   Zkratky, jako je například "certifikační autority.", "bzw." nejsou nahradit. Doporučujeme, abyste že použijte úplný formát.
*   Odebrání pět hlavní matematické operátory: +, -, \*, /. Doporučujeme, abyste je nahradíte jejich literálu formuláře: plus minus špatný, geteilt.
*   Totéž platí i pro operátory porovnání (=, <>,) - gleich, kleiner als, grösser als
*   Použít zlomků, jako je například 3 nebo 4, v aplikaci word formátu (například "viertel drei' místo ¾)
*   € Symbol nahraďte formuláře slovo "Euro"

Zde je několik příkladů:

| Původní Text | Po normalizaci uživatele | Po normalizaci systému
|--------  | ----- | -------- |
| ES ist 12.23Uhr | ES ist 12:23Uhr | ES ist zwölf uhr drei a zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 a 3 4 | 2 a 3 minus 4 | zwei plus drei minus vier|
| DAS macht 12€ | DAS macht 12 EUR | DAS macht zwölf EUR |

## <a name="next-steps"></a>Další postup

- [Získat zkušební verze předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznávat řeč v jazyce C#](quickstart-csharp-windows.md)
