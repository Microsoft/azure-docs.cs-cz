---
title: Pokyny pro přepis ve službě vlastní řeči na Azure | Microsoft Docs
description: Zjistěte, jak připravit data pro vlastní službu řeči v kognitivní služby.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 2785a35ac7583ac3d9503cb721d10078d86aa365
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342514"
---
# <a name="transcription-guidelines"></a>Pokyny pro přepis
K zajištění nejlepší využití vašich dat text pro přizpůsobení modelu acoustic a jazyk, vhodné dodržovat následující pokyny přepis. Tyto pokyny jsou jenom určitých jazyků.

## <a name="text-normalization"></a>Normalizaci textu

Pro optimální použití v modelu přizpůsobení acoustic nebo jazyk textová data musí být normalizovány, což znamená, že převede na standardní, jednoznačným formuláře čitelný v systému. Tato část popisuje text normalizaci zajišťuje služba rozpoznávání řeči vlastní při importu dat a normalizaci text, který musí uživatel provést před import dat.

## <a name="inverse-text-normalization"></a>Inverzní text normalizaci

Proces převodu "nezpracovaných" neformátovaný text zpět na formátovaný text, tj. s malá a velká písmena nebo interpunkční znaménka, se nazývá normalizace inverzní text (ITN). ITN se provádí na výsledky vrácené rozhraním API pro rozpoznávání řeči kognitivní služby. Koncový bod vlastní nasazení pomocí vlastní řeči služba používá stejný ITN jako API pro rozpoznávání řeči kognitivní služby. Ale tato služba nepodporuje aktuálně vlastní ITN, takže nové podmínky zaváděné model vlastní jazyk nebude naformátovaný ve výsledcích rozpoznávání.

## <a name="transcription-guidelines-for-en-us"></a>Přepis pokyny pro en US

Textová data do této služby nahrán by měly být zapsány ve formátu prostého textu pomocí pouze ASCII tisknutelná znaková sada. Každý řádek souboru by mělo obsahovat text pro pouze jeden utterance.

Je důležité k zamezení interpunkční znaky kódování Unicode. To může nastat nechtěně když přípravu dat v textovém editoru nebo oškrabávání data z webové stránky. Tyto znaky nahraďte příslušné náhrady ASCII. Příklad:

| Znakové sady Unicode, aby se zabránilo | Nahrazení ASCII |
|----- | ----- |
| "Hello, world" (otevírají a zavírají dvojité uvozovky) | "Hello, world" (dvojité uvozovky) |
| Jan pro den (vpravo jednoduché uvozovky) | Jan pro den (apostrof) |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Text normalizaci zajišťuje služba rozpoznávání řeči vlastní

Tato služba bude provádět následující text normalizaci dat importován jako jazyk datové sady nebo transcriptions pro akustickými datovou sadu. To zahrnuje

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

### <a name="text-normalization-required-by-users"></a>Text normalizaci požadovaných uživateli

Pro zajištění nejlepšího využití vašich dat, bude použito ke svým datům před jeho import následující pravidla normalizace.

*   Zkratky by měl být napsána v slova, aby odrážela mluvené formuláře
*   Nestandardní číselných řetězců by měla být napsána v slova
*   By měla být přepsána slova s neabecedními znaky nebo smíšeném alfanumerické znaky, jako vyslovováno
*   Běžné režim může být ponecháno jako jedna entita bez tečky nebo mezery mezi písmena, ale všechny ostatní režim by měly být zapsány v samostatných písmena s každou písmeno oddělených a jedna mezera

Zde jsou některé příklady

| Původní Text | Po normalizaci |
|----- | ----- |
| 3. NE 14 zotavení po havárii. | čtrnáct severovýchod třetí jednotky |
| Dr. Strangelove | Doctor Strangelove |
| James dokumentových 007 | James vytvořit vazbu mezi double jejda sedm |
| Nastavit jako$ ha | Kesha |
| Jak dlouho je 2 x 4 | Jak dlouho je pomocí čtyř |
| Schůzka přejde z – 3: 00 | Přejde schůzku od jedné do tří pm |
| Moje krve typ je O + | Moje krve typ je O kladné |
| horních je H20 | horních je H 2 O |
| přehrání OU812 podle Van Halen | přehrání U O 2 1 8 podle Van Halen |

## <a name="transcription-guidelines-for-zh-cn"></a>Přepis pokyny pro zh-CN

Textová data odešlou do služby řeči vlastní využít **kódování UTF-8 (včetně BOM)**. Každý řádek souboru by mělo obsahovat text pro pouze jeden utterance.

Je důležité k zamezení znaky interpunkce poloviční šířky. To může nastat nechtěně když přípravu dat v textovém editoru nebo oškrabávání data z webové stránky. Tyto znaky nahraďte příslušné náhrady plnou šířkou. Příklad:

| Znakové sady Unicode, aby se zabránilo | Nahrazení ASCII |
|----- | ----- |
| "你好" (otevření a zavření dvojité uvozovky) | "你好" (dvojité uvozovky) |
| 需要什么帮助? (otazník) | 需要什么帮助? |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Text normalizaci zajišťuje služba rozpoznávání řeči vlastní

Tato služba rozpoznávání řeči provede normalizaci následující text pro data importován jako jazyk datové sady nebo transcriptions pro akustickými datovou sadu. To zahrnuje

*   Odebrání všech interpunkce
*   Rozšíření čísel na mluvené formulář
*   Převeďte plnou šířkou písmena poloviční šířky.
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

### <a name="text-normalization-required-by-users"></a>Text normalizaci požadovaných uživateli

Pro zajištění nejlepšího využití vašich dat, bude použito následující pravidla normalizace ke svým datům _předchozí_ k importu.

*   Zkratky by měl být napsána v slova, aby odrážela mluvené formuláře
*   Tuto službu nezahrnuje všechny číselné počty. Je spolehlivější k zapsání číselných řetězců v mluvené formuláře

Zde jsou některé příklady

| Původní Text | Po normalizaci |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="transcription-guidelines-for-de-de"></a>Přepis pokyny pro de-DE

Textová data odešlou do služby řeči vlastní měli používat jenom **kódování UTF-8 (včetně BOM)**. Každý řádek souboru by mělo obsahovat text pro pouze jeden utterance.

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Text normalizaci zajišťuje služba rozpoznávání řeči vlastní

Tato služba bude provádět následující text normalizaci dat importován jako jazyk datové sady nebo transcriptions pro akustickými datovou sadu. To zahrnuje

*   Nižší-velká a malá písmena veškerého textu
*   Odebrání veškerou interpunkci, včetně angličtině nebo němčině uvozovky ("test", "test", "test" nebo "test" jsou v pořádku)
*   Zahodit všechny řádky, obsahující všechny včetně speciální znak: ^ hodnot l ¤ y ¦ smyslu © ª ¬® ° rozmezí ² µ × ÿ Ø¬¬
*   Rozšíření čísla do aplikace word formuláře, včetně dolar nebo euro objemy
*   Můžeme přijmout jenom samohlásky pro a, e, u; ostatní bude nahrazena "th" nebo zrušených

Zde jsou některé příklady

| Původní Text | Po normalizaci |
|----- | ----- |
| Frankfurter prstenec | frankfurter prstenec |
| "Hallo, Mama!" sagt die Tochter. | hallo mama sagt die tochter |
| ¡Eine Frage! | eine frage |
| WIR haben | WIR haben |
| DAS macht 10 | DAS macht zehn odběru |


### <a name="text-normalization-required-by-users"></a>Text normalizaci požadovaných uživateli

Pro zajištění nejlepšího využití vašich dat, bude použito ke svým datům před jeho import následující pravidla normalizace.

*   Musí být desetinné čárky a ne. například 2,3 % a není 2.3 %
*   Oddělovač času mezi hodin a minut musí být: a not., například 12:00 Uhr
*   Zkratky, jako je například "certifikační autority.", "bzw." nejsou nahradit. Doporučujeme používat úplný formát, aby bylo možné používat správnou výslovnost.
*   Odebrání pět hlavní matematické operátory: +, -, \*, /.
 Doporučujeme, abyste je Pokud chcete nahradit jejich literálu formuláři plus minus špatný, geteilt.
*   Totéž platí i pro komparátorů (=, <>,) - gleich, kleiner als, grösser als
*   Použít zlomek například 3 nebo 4 v podobě word drei viertel místo ¾
*   € Symbol nahraďte formuláře slovo "Euro"


Zde jsou některé příklady

| Původní Text | Po normalizaci uživatele | Po normalizaci systému
|--------  | ----- | -------- |
| ES ist 12.23Uhr | ES ist 12:23Uhr | ES ist zwölf uhr drei a zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 a 3 4 | 2 a 3 minus 4 | zwei plus drei minus vier|
| DAS macht 12€ | DAS macht 12 EUR | DAS macht zwölf EUR |



## <a name="next-steps"></a>Další postup
* [Jak používat vlastní koncový bod řeči na text](cognitive-services-custom-speech-create-endpoint.md)
* Zvyšte přesnost s vaší [vlastní akustickými modelu](cognitive-services-custom-speech-create-acoustic-model.md)
* Zvyšte přesnost s [vlastní jazyk modelu](cognitive-services-custom-speech-create-language-model.md)
