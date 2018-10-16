---
title: Pokyny pro přepis ve službě Custom Speech Service v Azure | Dokumentace Microsoftu
description: Zjistěte, jak připravit data pro Custom Speech Service ve službě Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 6c64b89c2eb933e9bcc24bdc3097e7a0b2aab368
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342462"
---
# <a name="transcription-guidelines"></a>Pokyny pro přepis

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Následující pokyny určené k transkripci byste měli dodržet, aby co nejlíp využít textových dat pro přizpůsobení akustických a jazykových modelu. Tyto pokyny jsou specifické pro konkrétní jazyk.

## <a name="text-normalization"></a>Normalizace text

Pro optimální využití v přizpůsobení akustických nebo jazyka modelu textová data musí být normalizovaná, což znamená, že transformována do čitelné podoby jednoznačnou, kompletní standardní systém. Tato část popisuje normalizace text prováděné Custom Speech Service při importu dat a normalizace text, který musí uživatel provést před importem dat.

## <a name="inverse-text-normalization"></a>Normalizace inverzní textu

Proces převodu "neupravené" neformátovaného textu zpět do formátovaného textu, tj. s malá a velká písmena a interpunkční znaménka, je nazvaný normalizace inverzní text (není). NENÍ se provádí na výsledky vrácená rozhraním API pro rozpoznávání řeči Cognitive Services. Vlastní koncový bod nasazeným v rámci Custom Speech Service používá není stejný jako Microsoft Cognitive Services Speech API. Ale tato služba nepodporuje aktuálně vlastní není tak nové podmínky zavedené vlastního jazykového modelu nebudou naformátovaná do výsledky rozpoznávání.

## <a name="transcription-guidelines-for-en-us"></a>Pokyny pro přepis pro en US

Text daty nahranými do této služby by měly být napsány v prostém textu pomocí pouze ASCII tisknutelný znakové sady. Každý řádek v souboru by měl obsahovat text pro pouze jednu utterance.

Je důležité se vyhněte použití interpunkční znaky znakové sady Unicode. To může nastat neúmyslně přípravě dat v textovém editoru nebo automatizované získávání dat data z webové stránky. Nahraďte tyto znaky náhrady odpovídající ASCII. Příklad:

| Kódování Unicode, aby se zabránilo | Nahrazení ASCII |
|----- | ----- |
| "Hello world" (otevírají a zavírají dvojité uvozovky) | "Hello world" (dvojité uvozovky) |
| John's na den (vpravo jednoduché uvozovky) | John's na den (apostrof) |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Text normalizace prováděné Custom Speech Service

Tato služba bude provádět následující text normalizaci dat importovat jako jazyk datové sady nebo přepisů pro akustický datové sady. Jedná se o

*   Malá písmena veškerý text
*   Odebrat všechny interpunkční znaménka s výjimkou Wordu interní apostrofy
*   Rozšíření čísel do mluvené řeči formuláře, včetně korunách

Tady je několik příkladů

| Původní Text | Po normalizace |
|----- | ----- |
| Starbucks kávy | starbucks kávy |
| "Tichá krávy!" uvedené Batman. | tichá krávy říká, že batman |
| "Co?" uvedené Batman sidekick dotazování. | jaké uvedené batman sidekick dotazování |
| Go get - em! | em přejděte get |
| Já jsem double-jointed | i teď double jointed |
| 104 hlavní ulice | jeden ale čtyři hlavní ulice |
| Vyladění 102.7 | ladění na jednu ale sedm dvě bodu |
| Pi je asi 3.14 | Pi je přibližně tři čtyři jeden bod |
| To stojí $3.14 | to stojí tři čtrnáct |

### <a name="text-normalization-required-by-users"></a>Text normalizace vyžadované uživatele

Aby bylo zajištěno co nejlíp využít vaše data, bude použito následující pravidla normalizace pro vaše data před importem.

*   Zkratky by měl uvádět tak, aby odrážely formuláře mluveného slova
*   Nestandardní číselných řetězců by měla být napsána v slova
*   Slov s neabecedními znaky nebo smíšené alfanumerické znaky by měla být přepsána, jak vyslovováno
*   Běžné zkratky ji lze ponechat jako jedna entita bez tečky ani mezery mezi písmena, ale další zkratky by měl zapíšou v samostatných písmena s každé písmeno oddělené jedna mezera

Tady je několik příkladů

| Původní Text | Po normalizace |
|----- | ----- |
| 14 NE 3rd zotavení po havárii. | čtrnáct severovýchod třetí disk |
| Dr. Strangelove | Lékař Strangelove |
| James Bond 007 | James vytvořit vazbu mezi double ale sedm |
| Ke$ ha | Kesha |
| Jak dlouhé je 2 × 4 | Jak dlouhé je pomocí čtyř |
| Schůzky přejde z 1 – 15: 00 | Schůzky přechází z jedné do tří hodin |
| Můj typ krve je O + | Můj typ krve je O pozitivní |
| hodnota je H20 | hodnota je H 2 O |
| Přehrát OU812 podle Van Halen | Přehrát U O 8 1 2 Van Halen |

## <a name="transcription-guidelines-for-zh-cn"></a>Pokyny pro přepis pro zh-CN

Textová data nahrát do služby Custom Speech Service používali **kódování UTF-8 (včetně BOM)**. Každý řádek v souboru by měl obsahovat text pro pouze jednu utterance.

Je důležité se vyhněte použití poloviční šířku interpunkční znaménka. To může nastat neúmyslně přípravě dat v textovém editoru nebo automatizované získávání dat data z webové stránky. Nahraďte tyto znaky náhrady odpovídající plnou šířkou. Příklad:

| Kódování Unicode, aby se zabránilo | Nahrazení ASCII |
|----- | ----- |
| "你好" (otevření a zavření dvojité uvozovky) | "你好" (dvojité uvozovky) |
| 需要什么帮助? (otazník) | 需要什么帮助? |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Text normalizace prováděné Custom Speech Service

Tato služba speech bude provádět následující text normalizaci dat importovat jako jazyk datové sady nebo přepisů pro akustický datové sady. Jedná se o

*   Odebrat všechny interpunkční znaménka
*   Rozšíření čísel do mluvené řeči formuláře
*   Převeďte na poloviční šířku písmena plnou šířkou.
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

### <a name="text-normalization-required-by-users"></a>Text normalizace vyžadované uživatele

Aby bylo zajištěno co nejlíp využít vaše data, bude použito následující pravidla normalizace ke svým datům _předchozí_ k importu.

*   Zkratky by měl uvádět tak, aby odrážely formuláře mluveného slova
*   Tuto službu nezahrnuje všechny číselné množství. Je spolehlivější vypsat číselných řetězců v podobě mluvené slovo

Tady je několik příkladů

| Původní Text | Po normalizace |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="transcription-guidelines-for-de-de"></a>Pokyny pro přepis pro de-DE

Používejte pouze textová data nahrát do služby Custom Speech Service **kódování UTF-8 (včetně BOM)**. Každý řádek v souboru by měl obsahovat text pro pouze jednu utterance.

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Text normalizace prováděné Custom Speech Service

Tato služba bude provádět následující text normalizaci dat importovat jako jazyk datové sady nebo přepisů pro akustický datové sady. Jedná se o

*   Malá písmena veškerý text
*   Odebrat všechny interpunkční znaménka, včetně anglické a německé uvozovky ("test", "test", "test" nebo "test" jsou v pořádku)
*   Zahodit všechny řádky obsahující všechny speciální znak včetně: ^ z l ¤ y ¦ smyslu © ª ¬® ° rozmezí ² µ × ÿ Ø¬¬
*   Rozšíření čísel, tvaru, včetně dolar nebo euro objemy
*   Můžeme přijmout jenom samohlásky pro a o u; ostatní bude nahrazen "th" nebo zahozeny

Tady je několik příkladů

| Původní Text | Po normalizace |
|----- | ----- |
| Aktualizační kanál Frankfurter | aktualizační kanál frankfurter |
| "Hallo, Mama!" sagt kostka Tochter. | hallo mama sagt kostka tochter |
| ¡Eine Frage! | eine frage |
| WIR haben | WIR haben |
| DAS macht 10 USD | DAS macht zehn dolarů |


### <a name="text-normalization-required-by-users"></a>Text normalizace vyžadované uživatele

Aby bylo zajištěno co nejlíp využít vaše data, bude použito následující pravidla normalizace pro vaše data před importem.

*   By měl být desetinné čárky a ne. například 2,3 % a ne % 2.3
*   Oddělovač času mezi hodiny a minuty musí být: a., například 12:00 Uhr
*   Zkratky jako "certifikační autorita.", "bzw." nejsou nahrazeny. Doporučujeme použít úplný formát, abyste měli správná výslovnost.
*   Pět hlavních matematické operátory jsou odebrány: +, -, \*, /.
 Doporučujeme nahradit je jejich literálu formulářem plus/minus mal geteilt.
*   To samé platí pro komparátorů (=, <>,) - gleich kleiner als, grösser als
*   Použijte ve tvaru drei viertel místo ¾ zlomek například 3/4
*   Nahraďte formulářem, slovo "Euro" € symbol


Tady je několik příkladů

| Původní Text | Po normalizace uživatele | Po normalizace systému
|--------  | ----- | -------- |
| ES TIS 12.23Uhr | 12:23Uhr TIS ES | ES TIS zwölf uhr drei a zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 + 3-4 | 2 a 3 minus 4 | zwei plus drei minus vier|
| DAS macht 12€ | DAS macht 12 eurech | eurech zwölf macht DAS |



## <a name="next-steps"></a>Další postup
* [Použití vlastního koncového bodu převodu řeči na text](cognitive-services-custom-speech-create-endpoint.md)
* Zvyšte přesnost s vaší [vlastního akustického modelu](cognitive-services-custom-speech-create-acoustic-model.md)
* Zlepšit přesnost [vlastních jazykových modelů](cognitive-services-custom-speech-create-language-model.md)
