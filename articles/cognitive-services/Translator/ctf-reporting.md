---
title: Collaborative Translation Framework (CTF) generování sestav – Translator Text API
titlesuffix: Azure Cognitive Services
description: Jak použít vytváření sestav Collaborative Translation Framework (CTF).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 79a645b0b41f200c384c165f244efa679be65171
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443447"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Jak použít vytváření sestav CTF (Collaborative Translation Framework)

> [!NOTE]
> Tato metoda je zastaralá. Není k dispozici v V3.0 Translator Text API.
> 
> Od 1. února 2018 se přestala nabízet spolupráci překlady Framework (CTF), dříve k dispozici pro V2.0 Translator Text API. Funkce AddTranslation a AddTranslationArray umožňují uživatelům povolit opravy prostřednictvím spolupráce Translation Framework. Po 31. ledna 2018 tyto dvě funkce nepřijímá nové příspěvky věty a uživatelé zobrazí chybová zpráva. Tyto funkce bylo vyřazeno a nebudou nahrazeny.

API pro generování sestav Collaborative Translation Framework (CTF) vrátí statistiku a skutečný obsah v úložišti CTF. Toto rozhraní API se liší od metody GetTranslations() protože ji:
* Vrátí přeloženého obsahu a jeho celkového počtu pouze z vašeho účtu (appId nebo účet Azure Marketplace).
* Vrátí přeloženého obsahu a jeho celkového počtu bez nutnosti shodu na konec věty zdroje.
* Nevrátí automatizovaný překlad (strojový překlad).

## <a name="endpoint"></a>Koncový bod
Koncový bod rozhraní API CTF generování sestav https://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Metody
| Název |    Popis|
|:---|:---|
| GetUserTranslationCounts Method | Získáte počet překlady, které jsou vytvořené tímto uživatelem. |
| GetUserTranslations Method | Načte překlady, které jsou vytvořené tímto uživatelem. |

Tyto metody umožňují:
* Načtení všech uživatelů překlady a opravy pod svým ID účtu ke stažení.
* Získáte seznam častí přispěvatelé. Ujistěte se, že je v AddTranslation() zadali správné uživatelské jméno.
* Vytváření uživatelského rozhraní (UI), který umožňuje vašim uživatelům důvěryhodné zobrazíte všechny dostupné kandidáty, v případě potřeby omezit na části webu, na základě předpony identifikátoru URI.

> [!NOTE]
> Obě metody jsou relativně pomalé a drahé. Doporučujeme používat opatrně.

## <a name="getusertranslationcounts-method"></a>GetUserTranslationCounts method

Tato metoda načte počet překlady, které jsou vytvořené tímto uživatelem. Poskytuje seznam seskupených podle uriPrefix, od uživatele, minRating a parametry požadavku maxRating počty překladu.

**Syntaxe**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslationCount[]GetUserTranslationCounts(
>            string appId,
>            string uriPrefix,
>            string from,
>            string to,
>            int? minRating,
>            int? maxRating,
>            string user,
>            string category
>            DateTime? minDateUtc,
>            DateTime? maxDateUtc,
>            int? skip,
>            int? take);
> ```

**Parametry**

| Parametr | Popis |
|:---|:---|
| appId | **Vyžaduje** Pokud hlavička autorizace se používá, ponechejte tuto položku appid pole prázdné jinak zadat řetězec obsahující "Nosiče" + "" + přístupový token.|
| uriPrefix | **Volitelné** řetězec obsahující předponu identifikátoru URI překladu.|
| from | **Volitelné** řetězec představující kód jazyka textu překlad. |
| na | **Volitelné** řetězec představující kód jazyka můžete přeložit text do.|
| minRating| **Volitelné** celočíselnou hodnotu představující hodnocení minimální kvalitu přeloženého textu. Platná hodnota je od -10 do 10. Výchozí hodnota je 1.|
| maxRating| **Volitelné** celočíselnou hodnotu představující hodnocení maximální kvalitu přeloženého textu. Platná hodnota je od -10 do 10. Výchozí hodnota je 1.|
| uživatel | **Volitelné** řetězec, který se používá k filtrování výsledků podle odesílatel požadavku dostane informaci odeslání. |
| category| **Volitelné** řetězec obsahující kategorie nebo domény překladu. Tento parametr podporuje pouze výchozí možnost Obecné.|
| minDateUtc| **Volitelné** datum, od kdy budete chtít načíst překlady. Datum musí být ve formátu UTC. |
| maxDateUtc| **Volitelné** datum, do kdy budete chtít načíst překlady. Datum musí být ve formátu UTC. |
| Přeskočit| **Volitelné** počet výsledků, které chcete nechat přeskočit na stránce. Například pokud chcete přeskočit prvních 20 řádků výsledků a zobrazení z 21. záznamu výsledku, zadejte 20 pro tento parametr. Výchozí hodnota tohoto parametru je 0.|
| Take | **Volitelné** počet výsledků, které chcete načíst. Maximální počet každý požadavek je 100. Výchozí hodnota je 100.|

> [!NOTE]
> Parametry požadavku skip a take povolit stránkování pro velký počet výsledků.

**Návratová hodnota**

Sada výsledků obsahuje pole **UserTranslationCount**. Každý UserTranslationCount obsahuje následující prvky:

| Pole | Popis |
|:---|:---|
| Count| Počet výsledků, který je načten|
| Z | Zdrojový jazyk|
| Hodnocení| Hodnocení, který je používán jako odesílatel ve volání metody AddTranslation()|
| Do| Cílový jazyk|
| Uri| Identifikátor URI použitý ve volání metody AddTranslation()|
| Uživatel| Uživatelské jméno|

**Výjimky**

| Výjimka | Message | Podmínky |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametr '**maxDateUtc**'musí být větší než nebo rovna hodnotě'**minDateUtc**".| Hodnota parametru **maxDateUtc** je menší než hodnota parametru **minDateUtc**.|
| TranslateApiException | IP adresa je přes kvótu.| <ul><li>Je dosažen limit pro počet požadavků za minutu.</li><li>Velikost požadavku zůstane omezené na znaky 10000.</li><li>Každou hodinu a denní kvótu omezit počet znaků, které Microsoft Translator API bude přijímat.</li></ul>|
| TranslateApiException | AppId je přes kvótu.| ID aplikace překročila kvótu hodinových nebo denních.|

> [!NOTE]
> Kvóta upraví zajistit rovnost mezi všemi uživateli služby.

**Příklady kódu zobrazení GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>GetUserTranslations – metoda

Tato metoda načte překlady, které jsou vytvořené tímto uživatelem. Nabízí že překlady seskupené podle uriPrefix, od, uživatele a minRating a maxRating parametry požadavku.

**Syntaxe**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslation[] GetUserTranslations (
>             string appId,
>             string uriPrefix,
>             string from,
>             string to,
>             int? minRating,
>             int? maxRating,
>             string user,
>             string category
>             DateTime? minDateUtc,
>             DateTime? maxDateUtc,
>             int? skip,
>             int? take);
> ```

**Parametry**

| Parametr | Popis |
|:---|:---|
| appId | **Vyžaduje** Pokud hlavička autorizace se používá, ponechejte tuto položku appid pole prázdné jinak zadat řetězec obsahující "Nosiče" + "" + přístupový token.|
| uriPrefix| **Volitelné** řetězec obsahující předponu identifikátoru URI překladu.|
| from| **Volitelné** řetězec představující kód jazyka textu překlad.|
| na| **Volitelné** řetězec představující kód jazyka můžete přeložit text do.|
| minRating| **Volitelné** celočíselnou hodnotu představující hodnocení minimální kvalitu přeloženého textu. Platná hodnota je od -10 do 10. Výchozí hodnota je 1.|
| maxRating| **Volitelné** celočíselnou hodnotu představující hodnocení maximální kvalitu přeloženého textu. Platná hodnota je od -10 do 10. Výchozí hodnota je 1.|
| uživatel| **Volitelné. Řetězec, který se používá k filtrování výsledků podle odesílatel požadavku dostane informaci odesílání**|
| category| **Volitelné** řetězec obsahující kategorie nebo domény překladu. Tento parametr podporuje pouze výchozí možnost Obecné.|
| minDateUtc| **Volitelné** datum, od kdy budete chtít načíst překlady. Datum musí být ve formátu UTC.|
| maxDateUtc| **Volitelné** datum, do kdy budete chtít načíst překlady. Datum musí být ve formátu UTC.|
| Přeskočit| **Volitelné** počet výsledků, které chcete nechat přeskočit na stránce. Například pokud chcete přeskočit prvních 20 řádků výsledků a zobrazení z 21. záznamu výsledku, zadejte 20 pro tento parametr. Výchozí hodnota tohoto parametru je 0.|
| Take| **Volitelné** počet výsledků, které chcete načíst. Maximální počet každý požadavek je 100. Výchozí hodnota je 50.|

> [!NOTE]
> Parametry požadavku skip a take povolit stránkování pro velký počet výsledků.

**Návratová hodnota**

Sada výsledků obsahuje pole **UserTranslation**. Každý UserTranslation obsahuje následující prvky:

| Pole | Popis |
|:---|:---|
| CreatedDateUtc| Datum vytvoření položky na základě AddTranslation()|
| Z| Zdrojový jazyk|
| OriginalText| Zdrojový jazyk textu, který se používá při odesílání žádosti|
|Hodnocení |Hodnocení, který je používán jako odesílatel ve volání metody AddTranslation()|
|Do|    Cílový jazyk|
|TranslatedText|    Překlad ve volání metody AddTranslation() odeslané podobě|
|Uri|   Identifikátor URI použitý ve volání metody AddTranslation()|
|Uživatel   |Uživatelské jméno|

**Výjimky**

| Výjimka | Message | Podmínky |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametr '**maxDateUtc**'musí být větší než nebo rovna hodnotě'**minDateUtc**".| Hodnota parametru **maxDateUtc** je menší než hodnota parametru **minDateUtc**.|
| TranslateApiException | IP adresa je přes kvótu.| <ul><li>Je dosažen limit pro počet požadavků za minutu.</li><li>Velikost požadavku zůstane omezené na znaky 10000.</li><li>Každou hodinu a denní kvótu omezit počet znaků, které Microsoft Translator API bude přijímat.</li></ul>|
| TranslateApiException | AppId je přes kvótu.| ID aplikace překročila kvótu hodinových nebo denních.|

> [!NOTE]
> Kvóta upraví zajistit rovnost mezi všemi uživateli služby.

**Příklady kódu zobrazení GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
