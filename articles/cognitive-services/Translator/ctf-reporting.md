---
title: Vytváření sestav architektury CTF (spolupráce s překlady) – překladatel
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 68b0de40940fa75dd4eb4e1572405f31ce1c22b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934374"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Jak použít vytváření sestav CTF (Collaborative Translation Framework)

> [!NOTE]
> Tato metoda je zastaralá. Není k dispozici v v 3.0 překladatele.
> 
> Rozhraní CTF (Týmová překlady), dříve dostupné v 2.0 translatoru, bylo Zastaralé od 1. února 2018. Funkce AddTranslation a AddTranslationArray umožňují uživatelům povolit opravy prostřednictvím architektury pro překlady pro spolupráci. Po 31. ledna 2018 tyto dvě funkce nepřijaly nové zprávy o větě a uživatelé obdrží chybovou zprávu. Tyto funkce byly vyřazeny a nebudou nahrazeny.

Rozhraní API pro vytváření sestav (CTF) pro vytváření sestav vrátí statistické údaje a skutečný obsah v úložišti CTF. Toto rozhraní API se liší od metody gettranslations (), protože:
* Vrátí přeložený obsah a celkový počet z vašeho účtu (appId nebo Azure Marketplace účet).
* Vrátí přeložený obsah a celkový počet bez nutnosti shody zdrojové věty.
* Nevrátí automatický překlad (strojový překlad).

## <a name="endpoint"></a>Koncový bod
Koncový bod rozhraní API pro vytváření sestav CTF je https://api.microsofttranslator.com/v2/beta/ctfreporting.svc .

## <a name="methods"></a>Metody
| Název | Popis|
|:---|:---|
| Metoda GetUserTranslationCounts | Získejte počty překladů, které jsou vytvořené uživatelem. |
| Metoda GetUserTranslations | Načte překlady, které jsou vytvořeny uživatelem. |

Tyto metody umožňují:
* Načte úplnou sadu uživatelských překladů a oprav v rámci ID účtu ke stažení.
* Získejte seznam častých přispěvatelů. Ujistěte se, že v AddTranslation () je zadáno správné uživatelské jméno.
* Sestavte uživatelské rozhraní (UI), které umožňuje důvěryhodným uživatelům zobrazit všechny dostupné kandidáty, pokud je to nutné omezit na část vaší lokality na základě předpony identifikátoru URI.

> [!NOTE]
> Obě metody jsou poměrně pomalé a nákladné. Doporučuje se je používat zřídka.

## <a name="getusertranslationcounts-method"></a>Metoda GetUserTranslationCounts

Tato metoda získá počet překladů, které jsou vytvořeny uživatelem. Poskytuje seznam počtů překladů seskupených podle parametrů uriPrefix, from, to, User, minRating a maxRating.

**Syntax**

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
| Výsledkem | **Volitelné** Řetězec představující kód jazyka textu překladu. |
| na | **Volitelné** Řetězec představující kód jazyka, do kterého se má text překládat.|
| minRating| **Volitelné** Celočíselná hodnota představující minimální hodnocení kvality pro přeložený text. Platná hodnota je mezi-10 a 10. Výchozí hodnota je 1.|
| maxRating| **Volitelné** Celočíselná hodnota představující maximální hodnocení kvality pro přeložený text. Platná hodnota je mezi-10 a 10. Výchozí hodnota je 1.|
| uživatel | **Volitelné** Řetězec, který se používá k filtrování výsledku na základě původce odeslání. |
| category| **Volitelné** Řetězec obsahující kategorii nebo doménu překladu. Tento parametr podporuje pouze výchozí možnost Obecné.|
| minDateUtc| **Volitelné** Datum, od kterého chcete načíst překlady. Datum musí být ve formátu UTC. |
| maxDateUtc| **Volitelné** Datum, do kterého chcete načíst překlady. Datum musí být ve formátu UTC. |
| Přeskočit| **Volitelné** Počet výsledků, které mají být na stránce přeskočeny. Například pokud chcete přeskočit prvních 20 řádků výsledků a zobrazit z 21 záznamu výsledků, zadejte pro tento parametr 20. Výchozí hodnota pro tento parametr je 0.|
| take | **Volitelné** Počet výsledků, které chcete načíst. Maximální počet jednotlivých požadavků je 100. Výchozí hodnota je 100.|

> [!NOTE]
> Parametry vynechání a přijetí žádosti umožňují stránkování pro velký počet výsledných záznamů.

**Návratová hodnota**

Sada výsledků obsahuje pole **UserTranslationCount**. Každý UserTranslationCount má následující prvky:

| Pole | Description |
|:---|:---|
| Počet| Počet načtených výsledků|
| Z | Zdrojový jazyk|
| Rating| Hodnocení, které je použito odesílatelem ve volání metody AddTranslation ()|
| Záměr| Cílový jazyk|
| Identifikátor URI| Identifikátor URI, který se použije ve volání metody AddTranslation ()|
| Uživatel| Uživatelské jméno|

**Výjimky**

| Výjimka | Zpráva | Podmínky |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametr**maxDateUtc**musí být větší nebo roven hodnotě "**minDateUtc**".| Hodnota parametru **maxDateUtc** je menší než hodnota parametru **minDateUtc**.|
| TranslateApiException | IP adresa překračuje kvótu.| <ul><li>Dosáhlo se limitu počtu požadavků za minutu.</li><li>Velikost žádosti zůstane omezená na 10000 znaků.</li><li>Hodinová a denní kvóta omezuje počet znaků, které bude Překladatel akceptovat.</li></ul>|
| TranslateApiException | AppId je nad kvótou.| ID aplikace překročilo hodinovou nebo denní kvótu.|

> [!NOTE]
> Kvóta se upraví, aby se zajistilo spravedlivé mezi všemi uživateli služby.

**Zobrazit příklady kódu na GitHubu**
* [C#](https://github.com/MicrosoftTranslator/CustomTranslator-API-CSharp)
* [PHP](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-PHP)

## <a name="getusertranslations-method"></a>Metoda GetUserTranslations

Tato metoda načte překlady, které jsou vytvořeny uživatelem. Poskytuje překlady seskupené podle parametrů uriPrefix, from, to, User a minRating a maxRating.

**Syntax**

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
| Výsledkem| **Volitelné** Řetězec představující kód jazyka textu překladu.|
| na| **Volitelné** Řetězec představující kód jazyka, do kterého se má text překládat.|
| minRating| **Volitelné** Celočíselná hodnota představující minimální hodnocení kvality pro přeložený text. Platná hodnota je mezi-10 a 10. Výchozí hodnota je 1.|
| maxRating| **Volitelné** Celočíselná hodnota představující maximální hodnocení kvality pro přeložený text. Platná hodnota je mezi-10 a 10. Výchozí hodnota je 1.|
| uživatel| **Volitelné. Řetězec, který se používá k filtrování výsledku na základě původce odeslání**|
| category| **Volitelné** Řetězec obsahující kategorii nebo doménu překladu. Tento parametr podporuje pouze výchozí možnost Obecné.|
| minDateUtc| **Volitelné** Datum, od kterého chcete načíst překlady. Datum musí být ve formátu UTC.|
| maxDateUtc| **Volitelné** Datum, do kterého chcete načíst překlady. Datum musí být ve formátu UTC.|
| Přeskočit| **Volitelné** Počet výsledků, které mají být na stránce přeskočeny. Například pokud chcete přeskočit prvních 20 řádků výsledků a zobrazit z 21 záznamu výsledků, zadejte pro tento parametr 20. Výchozí hodnota pro tento parametr je 0.|
| take| **Volitelné** Počet výsledků, které chcete načíst. Maximální počet jednotlivých požadavků je 100. Výchozí hodnota je 50.|

> [!NOTE]
> Parametry vynechání a přijetí žádosti umožňují stránkování pro velký počet výsledných záznamů.

**Návratová hodnota**

Sada výsledků obsahuje pole **UserTranslation**. Každý UserTranslation má následující prvky:

| Pole | Description |
|:---|:---|
| CreatedDateUtc| Datum vytvoření položky pomocí AddTranslation ()|
| Z| Zdrojový jazyk|
| OriginalText| Text zdrojového jazyka, který se používá při odesílání žádosti|
|Rating |Hodnocení, které je použito odesílatelem ve volání metody AddTranslation ()|
|Záměr|    Cílový jazyk|
|TranslatedText|    Překlad jako odeslaný ve volání metody AddTranslation ()|
|Identifikátor URI|   Identifikátor URI, který se použije ve volání metody AddTranslation ()|
|Uživatel   |Uživatelské jméno|

**Výjimky**

| Výjimka | Zpráva | Podmínky |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametr**maxDateUtc**musí být větší nebo roven hodnotě "**minDateUtc**".| Hodnota parametru **maxDateUtc** je menší než hodnota parametru **minDateUtc**.|
| TranslateApiException | IP adresa překračuje kvótu.| <ul><li>Dosáhlo se limitu počtu požadavků za minutu.</li><li>Velikost žádosti zůstane omezená na 10000 znaků.</li><li>Hodinová a denní kvóta omezuje počet znaků, které bude Překladatel akceptovat.</li></ul>|
| TranslateApiException | AppId je nad kvótou.| ID aplikace překročilo hodinovou nebo denní kvótu.|

> [!NOTE]
> Kvóta se upraví, aby se zajistilo spravedlivé mezi všemi uživateli služby.

**Zobrazit příklady kódu na GitHib**
* [C#](https://github.com/MicrosoftTranslator/CustomTranslator-API-CSharp)
* [PHP](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-PHP)
