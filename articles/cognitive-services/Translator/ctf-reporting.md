---
title: Vytváření sestav architektury CTF (spolupráce s překlady) – Translator Text API
titleSuffix: Azure Cognitive Services
description: Jak používat vytváření sestav architektury CTF (spolupráce s překlady).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 6a197095d97e67f7548e60375148cff57e47b797
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595934"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Jak použít vytváření sestav CTF (Collaborative Translation Framework)

> [!NOTE]
> Tato metoda je zastaralá. Není k dispozici v v 3.0 Translator Text API.
> 
> Rozhraní CTF (Týmová překlady), dříve dostupné v verzi 2.0 Translator Text API, bylo Zastaralé od 1. února 2018. Funkce AddTranslation a AddTranslationArray umožňují uživatelům povolit opravy prostřednictvím architektury pro překlady pro spolupráci. Po 31. ledna 2018 tyto dvě funkce nepřijaly nové zprávy o větě a uživatelé obdrží chybovou zprávu. Tyto funkce byly vyřazeny a nebudou nahrazeny.

Rozhraní API pro vytváření sestav (CTF) pro vytváření sestav vrátí statistické údaje a skutečný obsah v úložišti CTF. Toto rozhraní API se liší od metody gettranslations (), protože:
* Vrátí přeložený obsah a celkový počet z vašeho účtu (appId nebo Azure Marketplace účet).
* Vrátí přeložený obsah a celkový počet bez nutnosti shody zdrojové věty.
* Nevrátí automatický překlad (strojový překlad).

## <a name="endpoint"></a>Koncový bod
Koncový bod rozhraní API pro vytváření sestav CTF je https://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Metody
| Name |    Popis|
|:---|:---|
| GetUserTranslationCounts Method | Získejte počty překladů, které jsou vytvořené uživatelem. |
| GetUserTranslations Method | Načte překlady, které jsou vytvořeny uživatelem. |

Tyto metody umožňují:
* Načte úplnou sadu uživatelských překladů a oprav v rámci ID účtu ke stažení.
* Získejte seznam častých přispěvatelů. Ujistěte se, že v AddTranslation () je zadáno správné uživatelské jméno.
* Sestavte uživatelské rozhraní (UI), které umožňuje důvěryhodným uživatelům zobrazit všechny dostupné kandidáty, pokud je to nutné omezit na část vaší lokality na základě předpony identifikátoru URI.

> [!NOTE]
> Obě metody jsou poměrně pomalé a nákladné. Doporučuje se je používat zřídka.

## <a name="getusertranslationcounts-method"></a>Metoda GetUserTranslationCounts

Tato metoda získá počet překladů, které jsou vytvořeny uživatelem. Poskytuje seznam počtů překladů seskupených podle parametrů uriPrefix, from, to, User, minRating a maxRating.

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
| appId | **Vyžaduje** se Pokud se používá autorizační hlavička, nechte pole AppID prázdné jinak zadejte řetězec, který obsahuje řetězec "nosič" + "" a přístupový token.|
| uriPrefix | **Volitelné** Řetězec obsahující předponu identifikátoru URI překladu.|
| from | **Volitelné** Řetězec představující kód jazyka textu překladu. |
| na | **Volitelné** Řetězec představující kód jazyka, do kterého se má text překládat.|
| minRating| **Volitelné** Celočíselná hodnota představující minimální hodnocení kvality pro přeložený text. Platná hodnota je mezi-10 a 10. Výchozí hodnota je 1.|
| maxRating| **Volitelné** Celočíselná hodnota představující maximální hodnocení kvality pro přeložený text. Platná hodnota je mezi-10 a 10. Výchozí hodnota je 1.|
| uživatel | **Volitelné** Řetězec, který se používá k filtrování výsledku na základě původce odeslání. |
| category| **Volitelné** Řetězec obsahující kategorii nebo doménu překladu. Tento parametr podporuje pouze výchozí možnost Obecné.|
| minDateUtc| **Volitelné** Datum, od kterého chcete načíst překlady. Datum musí být ve formátu UTC. |
| maxDateUtc| **Volitelné** Datum, do kterého chcete načíst překlady. Datum musí být ve formátu UTC. |
| přeskočit| **Volitelné** Počet výsledků, které mají být na stránce přeskočeny. Například pokud chcete přeskočit prvních 20 řádků výsledků a zobrazit z 21 záznamu výsledků, zadejte pro tento parametr 20. Výchozí hodnota pro tento parametr je 0.|
| nezbytná | **Volitelné** Počet výsledků, které chcete načíst. Maximální počet jednotlivých požadavků je 100. Výchozí hodnota je 100.|

> [!NOTE]
> Parametry vynechání a přijetí žádosti umožňují stránkování pro velký počet výsledných záznamů.

**Návratová hodnota**

Sada výsledků obsahuje pole **UserTranslationCount**. Každý UserTranslationCount má následující prvky:

| Pole | Popis |
|:---|:---|
| Count| Počet načtených výsledků|
| Z | Zdrojový jazyk|
| Hodnocení| Hodnocení, které je použito odesílatelem ve volání metody AddTranslation ()|
| Do| Cílový jazyk|
| Uri| Identifikátor URI, který se použije ve volání metody AddTranslation ()|
| Uživatel| Uživatelské jméno|

**Výjimky**

| Výjimka | Message | Podmínky |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametr**maxDateUtc**musí být větší nebo roven hodnotě "**minDateUtc**".| Hodnota parametru **maxDateUtc** je menší než hodnota parametru **minDateUtc**.|
| TranslateApiException | IP adresa překračuje kvótu.| <ul><li>Dosáhlo se limitu počtu požadavků za minutu.</li><li>Velikost žádosti zůstane omezená na 10000 znaků.</li><li>Hodinová a denní kvóta omezuje počet znaků, které bude rozhraní API služby Microsoft Translator akceptovat.</li></ul>|
| TranslateApiException | AppId je nad kvótou.| ID aplikace překročilo hodinovou nebo denní kvótu.|

> [!NOTE]
> Kvóta se upraví, aby se zajistilo spravedlivé mezi všemi uživateli služby.

**Zobrazit příklady kódu na GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>Metoda GetUserTranslations

Tato metoda načte překlady, které jsou vytvořeny uživatelem. Poskytuje překlady seskupené podle parametrů uriPrefix, from, to, User a minRating a maxRating.

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
| appId | **Vyžaduje** se Pokud se používá autorizační hlavička, nechte pole AppID prázdné jinak zadejte řetězec, který obsahuje řetězec "nosič" + "" a přístupový token.|
| uriPrefix| **Volitelné** Řetězec obsahující předponu identifikátoru URI překladu.|
| from| **Volitelné** Řetězec představující kód jazyka textu překladu.|
| na| **Volitelné** Řetězec představující kód jazyka, do kterého se má text překládat.|
| minRating| **Volitelné** Celočíselná hodnota představující minimální hodnocení kvality pro přeložený text. Platná hodnota je mezi-10 a 10. Výchozí hodnota je 1.|
| maxRating| **Volitelné** Celočíselná hodnota představující maximální hodnocení kvality pro přeložený text. Platná hodnota je mezi-10 a 10. Výchozí hodnota je 1.|
| uživatel| **Volitelné. Řetězec, který se používá k filtrování výsledku na základě původce odeslání**|
| category| **Volitelné** Řetězec obsahující kategorii nebo doménu překladu. Tento parametr podporuje pouze výchozí možnost Obecné.|
| minDateUtc| **Volitelné** Datum, od kterého chcete načíst překlady. Datum musí být ve formátu UTC.|
| maxDateUtc| **Volitelné** Datum, do kterého chcete načíst překlady. Datum musí být ve formátu UTC.|
| přeskočit| **Volitelné** Počet výsledků, které mají být na stránce přeskočeny. Například pokud chcete přeskočit prvních 20 řádků výsledků a zobrazit z 21 záznamu výsledků, zadejte pro tento parametr 20. Výchozí hodnota pro tento parametr je 0.|
| nezbytná| **Volitelné** Počet výsledků, které chcete načíst. Maximální počet jednotlivých požadavků je 100. Výchozí hodnota je 50.|

> [!NOTE]
> Parametry vynechání a přijetí žádosti umožňují stránkování pro velký počet výsledných záznamů.

**Návratová hodnota**

Sada výsledků obsahuje pole **UserTranslation**. Každý UserTranslation má následující prvky:

| Pole | Popis |
|:---|:---|
| CreatedDateUtc| Datum vytvoření položky pomocí AddTranslation ()|
| Z| Zdrojový jazyk|
| OriginalText| Text zdrojového jazyka, který se používá při odesílání žádosti|
|Hodnocení |Hodnocení, které je použito odesílatelem ve volání metody AddTranslation ()|
|Do|    Cílový jazyk|
|TranslatedText|    Překlad jako odeslaný ve volání metody AddTranslation ()|
|Uri|   Identifikátor URI, který se použije ve volání metody AddTranslation ()|
|Uživatel   |Uživatelské jméno|

**Výjimky**

| Výjimka | Message | Podmínky |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametr**maxDateUtc**musí být větší nebo roven hodnotě "**minDateUtc**".| Hodnota parametru **maxDateUtc** je menší než hodnota parametru **minDateUtc**.|
| TranslateApiException | IP adresa překračuje kvótu.| <ul><li>Dosáhlo se limitu počtu požadavků za minutu.</li><li>Velikost žádosti zůstane omezená na 10000 znaků.</li><li>Hodinová a denní kvóta omezuje počet znaků, které bude rozhraní API služby Microsoft Translator akceptovat.</li></ul>|
| TranslateApiException | AppId je nad kvótou.| ID aplikace překročilo hodinovou nebo denní kvótu.|

> [!NOTE]
> Kvóta se upraví, aby se zajistilo spravedlivé mezi všemi uživateli služby.

**Zobrazit příklady kódu na GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
