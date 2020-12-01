---
title: Pravidla přepisu na základě popisku – služba řeči
titleSuffix: Azure Cognitive Services
description: Chcete-li zlepšit přesnost rozpoznávání řeči, například pokud jsou slova odstraněna nebo nesprávně nahrazena, můžete společně se zvukovými daty použít přepisy. Přepisy na základě popisku jsou slova na základě slova, doslovného přepisu zvukového souboru.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 4516bbddd3fda593021288a440e1b354d4d7f1e3
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340233"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Jak vytvořit přepisy s lidským popiskem

Pokud chcete zlepšit přesnost rozpoznávání, zejména problémy, které jsou způsobeny odstraněním nebo nesprávným nahrazením slov, budete chtít použít přepisy, které jsou pro vaše zvuková data k popisku. Co jsou Přepisy na základě popisku? To je snadné – jedná se o slovo do Wordu, doslovného přepisu zvukového souboru.

Pro zlepšení rozpoznávání je nutná rozsáhlá ukázka dat přepisu, což nabízíme mezi 10 a 1 000 hodinami přepisu dat. Na této stránce si ukážeme pokyny, které vám pomůžou vytvořit vysoce kvalitní přepisy. Tato příručka je rozdělená podle národního prostředí s oddíly pro AMERICKou angličtinu, Mandarinii, čínskou a Německo.

## <a name="us-english-en-us"></a>USA – angličtina (EN-US)

Přepisy v angličtině pro angličtinu musí být zadány jako prostý text, a to pouze pomocí znaků ASCII. Vyhněte se použití interpunkčních znamének s latinkou 1 nebo Unicode. Tyto znaky jsou často nechtěně přidány při kopírování textu z aplikace pro zpracování textu nebo z dat z webových stránek. Pokud jsou tyto znaky k dispozici, nezapomeňte je aktualizovat pomocí odpovídající náhrady ASCII.

Tady je pár příkladů:

| Znaky, které se mají zabránit | Substituce | Poznámky |
| ------------------- | ------------ | ----- |
| Hello World | Hello world | Levé a pravé uvozovky se nahradily odpovídajícími znaky ASCII. |
| Den Jan | Den Jan | Apostrof byl nahrazen příslušným znakem ASCII. |
| to bylo dobré – ne, bylo skvělé! | je dobrá – ne, bylo skvělé! | Em pomlčka byla nahrazena dvěma pomlčkami. |

### <a name="text-normalization-for-us-english"></a>Normalizace textu pro AMERICKou angličtinu

Normalizace textu je transformace slov do konzistentního formátu, který se používá při výuce modelu. Některá pravidla normalizace se aplikují na text automaticky, doporučujeme ale tyto pokyny použít při přípravě dat přepisu na základě popisku:

- Vypište zkratky v slovech.
- Vypište nestandardní číselné řetězce v slovech (například účetní termíny).
- Neabecední znaky nebo smíšené alfanumerické znaky by měly být přepisu jako vyslovované.
- Zkratky, které jsou vyslovované jako slova, by se neměly upravovat (například "paprsky", "Laser", "RAM" nebo "NATO").
- Odpište zkratky, které jsou vyslovované jako samostatná písmena s každým písmenem oddělené mezerou.

Tady je několik příkladů normalizace, které byste měli provést na přepisu:

| Původní text               | Text po normalizaci              |
| --------------------------- | ------------------------------------- |
| Banner Dr. Bruce            | Bruce banner pro lékaře                   |
| Jan dluhopis, 007             | Jan dluhopis, dvojitý Oh 7           |
| Ke $ ha                       | Kesha                                 |
| Jak dlouho je 2x4         | Jak dlouho jsou dva čtyři           |
| Schůzka směřuje z 1 – 3pm | Schůzka bude směrována z jedné na tři odpoledne. |
| Moje krevní typ je O +         | Můj typ krevního typu je O kladné           |
| Voda je H20                | Voda je H 2 O                        |
| Hraní OU812 po Van Halen     | Přehrát O U 8 1 2 od Van Halen           |
| UTF-8 s BOM              | U T F 8 pomocí kusovníku                      |

Následující pravidla normalizace se automaticky aplikují na Přepisy:

- Používejte malá písmena.
- Odebrat všechna interpunkční znaménka s výjimkou apostrofů v rámci slov.
- Rozbalí čísla do slov nebo mluveného formuláře, jako jsou třeba částky dolaru.

Tady je několik příkladů normalizace, které se automaticky provedou na přepisu:

| Původní text                          | Text po normalizaci          |
| -------------------------------------- | --------------------------------- |
| "Svatý kráva" zmíněné Batman.               | Svatý kráva, na kterou se říká Batman              |
| "Co?" v tomto Batman je to Sidekick, Robin. | Co říká dotaz na Batman – Sidekick |
| Získat – em!                            | získat em                         |
| Já jsem se zdvojnásobil                     | Jsem se zdvojnásobil                |
| ELM ulice 104                         | ELM ulice 1 0 4            |
| Vylaďte 102,7                          | vyladit až 1 0 2 bodů 7    |
| Pi má přibližně 3,14                       | Pi má přibližně tři body 1 4  |
| Náklady na IT \$ 3,14                        | náklady na IT 3 14           |

## <a name="mandarin-chinese-zh-cn"></a>Mandarin čínština (zh-CN)

Přepisy na základě popisku pro formát mandarinek pro lidskou čínštinu musí mít kódování UTF-8 s označením pořadí bajtů. Vyhněte se použití interpunkčních znamének s poloviční šířkou. Tyto znaky mohou být neúmyslně zahrnuty při přípravě dat v programu pro zpracování textu nebo při vynechání dat z webových stránek. Pokud jsou tyto znaky k dispozici, nezapomeňte je aktualizovat o příslušnou substituci s plnou šířkou.

Tady je pár příkladů:

| Znaky, které se mají zabránit | Substituce   | Poznámky |
| ------------------- | -------------- | ----- |
| "你好" | "你好" | Úvodní a koncové uvozovky byly nahrazeny příslušnými znaky. |
| 需要什么帮助? | 需要什么帮助？| Otazník byl nahrazen vhodným znakem. |

### <a name="text-normalization-for-mandarin-chinese"></a>Normalizace textu u Mandarinie v čínštině

Normalizace textu je transformace slov do konzistentního formátu, který se používá při výuce modelu. Některá pravidla normalizace se aplikují na text automaticky, doporučujeme ale tyto pokyny použít při přípravě dat přepisu na základě popisku:

- Vypište zkratky v slovech.
- Vypište číselné řetězce v mluvené formě.

Tady je několik příkladů normalizace, které byste měli provést na přepisu:

| Původní text | Text po normalizaci |
| ------------- | ------------------------ |
| 我今年 21 | 我今年二十一 |
| 3 号楼 504 | 三号 楼 五 零 四 |

Následující pravidla normalizace se automaticky aplikují na Přepisy:

- Odebrat všechna interpunkční znaménka
- Rozbalit čísla do mluveného formuláře
- Převést písmena s plnou šířkou na písmena s poloviční šířkou
- Použití velkých písmen pro všechna anglická slova

Tady je několik příkladů normalizace, které se automaticky provedou na přepisu:

| Původní text | Text po normalizaci |
| ------------- | ------------------------ |
| 3,1415 | 三 点 一 四 一 五 |
| ¥3,5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992 年 8 月 8 日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午 5:00 的航班 | 下午 五点 的 航班 |
| 我今年 21 岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Němčina (de-DE) a další jazyky

Přepisy pro německý zvuk (a jiné jazyky jiné než anglické nebo Mandarine) musí mít kódování UTF-8 s označením pořadí bajtů. Pro každý zvukový soubor je třeba zadat jeden přepis s názvem.

### <a name="text-normalization-for-german"></a>Normalizace textu pro němčinu

Normalizace textu je transformace slov do konzistentního formátu, který se používá při výuce modelu. Některá pravidla normalizace se aplikují na text automaticky, doporučujeme ale tyto pokyny použít při přípravě dat přepisu na základě popisku:

- Zápis desetinných míst jako "," a nikoli ".".
- Oddělovače času zápisu jako ":", nikoli "." (například: 12:00 Uhr).
- Zkratky jako "CA". nejsou nahrazeny. Doporučujeme, abyste použili úplný mluvený formulář.
- Čtyři hlavní matematické operátory (+,-, \* a/) se odeberou. Doporučujeme, abyste je nahradili pomocí vytvořeného formuláře: "plus", "mínus" "," "", "" "
- Operátory porovnání jsou odebrány (=, < a >). Doporučujeme, abyste je nahradili "Gleich", "Kleiner ALS" a "grösser ALS".
- Zapište zlomky, například 3/4, v písemné podobě (například "Drei Viertel" namísto 3/4).
- Nahraďte symbol "€" jeho napsaným formulářem "euro".

Tady je několik příkladů normalizace, které byste měli provést na přepisu:

| Původní text    | Text po normalizaci uživatele | Text po normalizaci systému       |
| ---------------- | ----------------------------- | ------------------------------------- |
| ES tis 12,23 Uhr | ES tis 12:23 Uhr              | ES tis Zwölf Uhr Drei und zwanzig Uhr |
| {12,45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3-4        | 2 plus 3 minus 4              | Zwei plus Drei mínus Vier             |

Následující pravidla normalizace se automaticky aplikují na Přepisy:

- Pro veškerý text použijte malá písmena.
- Odebrat veškerou interpunkci, včetně různých typů uvozovek ("test", "test", "test" a «test» "OK).
- Zahodí řádky se všemi speciálními znaky z této sady: ¢ ¤ y ¦ § © ª ¬® ° ± 20. až ¬ ¬.
- Rozbalíte čísla do mluveného formuláře, včetně částek dolaru nebo eura.
- Přijměte umlauts jenom pro a, o a vy. Ostatní budou nahrazeny "th" nebo budou zahozeny.

Tady je několik příkladů normalizace, které se automaticky provedou na přepisu:

| Původní text    | Text po normalizaci |
| ---------------- | ------------------------ |
| Frankfurter Ring | Frankfurter Ring         |
| ¡Eine Frage!     | eine frage               |
| wir, haben       | wir haben                |

## <a name="next-steps"></a>Další kroky

- [Příprava a testování dat](./how-to-custom-speech-test-and-train.md)
- [Kontrola dat](how-to-custom-speech-inspect-data.md)
- [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)
- [Trénování modelu](how-to-custom-speech-train-model.md)
- [Nasazení modelu](./how-to-custom-speech-train-model.md)