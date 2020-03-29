---
title: Vykazování kolaborativního překladatelského rámce (CTF) – překladač textovéapi
titleSuffix: Azure Cognitive Services
description: Jak používat sestavy collaborative translation framework (CTF).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 6a197095d97e67f7548e60375148cff57e47b797
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595934"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Jak použít vytváření sestav CTF (Collaborative Translation Framework)

> [!NOTE]
> Tato metoda je zastaralé. Není k dispozici ve V3.0 překladače text API.
> 
> Kolaborativní překlady Framework (CTF), dříve k dispozici pro V2.0 translator text API, byl zastaralá od února 1, 2018. Funkce AddTranslation a AddTranslationArray umožňují uživatelům povolit opravy prostřednictvím rámce kolaborativního překladu. ledna 2018 tyto dvě funkce nepřijaly nové odeslání věty a uživatelé obdrží chybovou zprávu. Tyto funkce byly vyřazeny a nebudou nahrazeny.

Rozhraní API pro vytváření přehledů (CTF) pro spolupráci (CTF) vrátí statistiky a skutečný obsah v úložišti CTF. Toto rozhraní API se liší od Metody GetTranslations(), protože:
* Vrátí přeložený obsah a jeho celkový počet jenom z vašeho účtu (appId nebo účet Azure Marketplace).
* Vrátí přeložený obsah a jeho celkový počet bez nutnosti shody zdrojové věty.
* Nevrací automatický překlad (strojový překlad).

## <a name="endpoint"></a>Koncový bod
Koncový bod rozhraní CTF Reporting API jehttps://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Metody
| Name (Název) |    Popis|
|:---|:---|
| Metoda GetUserTranslationCounts | Získejte počty překladů, které jsou vytvořeny uživatelem. |
| Metoda GetUserTranslations | Načte překlady, které jsou vytvořeny uživatelem. |

Tyto metody umožňují:
* Načtěte kompletní sadu uživatelských překladů a oprav pod ID účtu ke stažení.
* Získejte seznam častých přispěvatelů. Ujistěte se, že správné uživatelské jméno je uvedeno v AddTranslation().
* Vytvořte uživatelské rozhraní ( UI), které umožňuje důvěryhodným uživatelům zobrazit všechny dostupné kandidáty, v případě potřeby omezené na část webu, na základě předpony URI.

> [!NOTE]
> Obě metody jsou poměrně pomalé a drahé. Doporučuje se je používat střídmě.

## <a name="getusertranslationcounts-method"></a>Metoda GetUserTranslationCounts

Tato metoda získá počet překladů, které jsou vytvořeny uživatelem. Poskytuje seznam počty překladů seskupené podle uriPrefix, z, uživatele, minRating a maxRating parametry požadavku.

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
| appId | **Povinné** Pokud je použita hlavička Autorizace, ponechte pole appid prázdné, jiné, zadejte řetězec obsahující "Nosič" + " " + přístupový token.|
| uriPrefix | **Nepovinné** Řetězec obsahující předponu identifikátoru URI překladu.|
| Z | **Nepovinné** Řetězec představující kód jazyka překladového textu. |
| na | **Nepovinné** Řetězec představující kód jazyka přeložit text do.|
| minrating| **Nepovinné** Celá hodnota představující minimální hodnocení kvality přeloženého textu. Platná hodnota je mezi -10 a 10. Výchozí hodnota je 1.|
| maxRating| **Nepovinné** Celá hodnota představující maximální hodnocení kvality přeloženého textu. Platná hodnota je mezi -10 a 10. Výchozí hodnota je 1.|
| uživatel | **Nepovinné** Řetězec, který se používá k filtrování výsledku na základě původce odeslání. |
| category| **Nepovinné** Řetězec obsahující kategorii nebo doménu překladu. Tento parametr podporuje pouze výchozí obecnou možnost.|
| minDateUtc| **Nepovinné** Datum od data, od kdy chcete načíst překlady. Datum musí být ve formátu UTC. |
| maxDateUtc| **Nepovinné** Datum, do kdy chcete načíst překlady. Datum musí být ve formátu UTC. |
| Přeskočit| **Nepovinné** Počet výsledků, které chcete přeskočit na stránce. Chcete-li například přeskočit prvních 20 řádků výsledků a zobrazení ze záznamu 21. Výchozí hodnota pro tento parametr je 0.|
| vzít | **Nepovinné** Počet výsledků, které chcete načíst. Maximální počet jednotlivých požadavků je 100. Výchozí hodnota je 100.|

> [!NOTE]
> Parametry skip a take request umožňují stránkování pro velký počet záznamů výsledků.

**Vrácená hodnota**

Sada výsledků obsahuje pole **UserTranslationCount**. Každý UserTranslationCount má následující prvky:

| Pole | Popis |
|:---|:---|
| Počet| Počet výsledků, které jsou načteny|
| From | Zdrojový jazyk|
| Rating| Hodnocení, které je použito předkladatelem v volání metody AddTranslation()|
| Akce| Cílový jazyk|
| Uri| Identifikátor URI použitý v volání metody AddTranslation()|
| Uživatel| Uživatelské jméno|

**Výjimky**

| Výjimka | Zpráva | Podmínky |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametr '**maxDateUtc**' musí být větší nebo roven '**minDateUtc**'.| Hodnota parametru **maxDateUtc** je menší než hodnota parametru **minDateUtc**.|
| PřeložitVýjimka | IP je nad kvótou.| <ul><li>Je dosaženo limitu počtu požadavků za minutu.</li><li>Velikost požadavku zůstává omezena na 10000 znaků.</li><li>Hodinová a denní kvóta omezují počet znaků, které bude rozhraní API Microsoft Translator API přijímat.</li></ul>|
| PřeložitVýjimka | AppId je nad kvótu.| ID aplikace překročilo hodinovou nebo denní kvótu.|

> [!NOTE]
> Kvóta se přizpůsobí tak, aby byla zajištěna spravedlnost mezi všemi uživateli služby.

**Zobrazit příklady kódu na GitHib**
* [C #](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>Metoda GetUserTranslations

Tato metoda načte překlady, které jsou vytvořeny uživatelem. Poskytuje překlady seskupené podle uriPrefix, z, do uživatele a minRating a maxRating parametry požadavku.

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
| appId | **Povinné** Pokud je použita hlavička Autorizace, ponechte pole appid prázdné, jiné, zadejte řetězec obsahující "Nosič" + " " + přístupový token.|
| uriPrefix| **Nepovinné** Řetězec obsahující předponu identifikátoru URI překladu.|
| Z| **Nepovinné** Řetězec představující kód jazyka překladového textu.|
| na| **Nepovinné** Řetězec představující kód jazyka přeložit text do.|
| minrating| **Nepovinné** Celá hodnota představující minimální hodnocení kvality přeloženého textu. Platná hodnota je mezi -10 a 10. Výchozí hodnota je 1.|
| maxRating| **Nepovinné** Celá hodnota představující maximální hodnocení kvality přeloženého textu. Platná hodnota je mezi -10 a 10. Výchozí hodnota je 1.|
| uživatel| **Volitelné. Řetězec, který se používá k filtrování výsledku na základě původce podání**|
| category| **Nepovinné** Řetězec obsahující kategorii nebo doménu překladu. Tento parametr podporuje pouze výchozí obecnou možnost.|
| minDateUtc| **Nepovinné** Datum od data, od kdy chcete načíst překlady. Datum musí být ve formátu UTC.|
| maxDateUtc| **Nepovinné** Datum, do kdy chcete načíst překlady. Datum musí být ve formátu UTC.|
| Přeskočit| **Nepovinné** Počet výsledků, které chcete přeskočit na stránce. Chcete-li například přeskočit prvních 20 řádků výsledků a zobrazení ze záznamu 21. Výchozí hodnota pro tento parametr je 0.|
| vzít| **Nepovinné** Počet výsledků, které chcete načíst. Maximální počet jednotlivých požadavků je 100. Výchozí hodnota je 50.|

> [!NOTE]
> Parametry skip a take request umožňují stránkování pro velký počet záznamů výsledků.

**Vrácená hodnota**

Sada výsledků obsahuje pole **UserTranslation**. Každý UserTranslation má následující prvky:

| Pole | Popis |
|:---|:---|
| CreatedDateUtc| Datum vytvoření položky pomocí AddTranslation()|
| From| Zdrojový jazyk|
| OriginalText| Text zdrojového jazyka, který se používá při odesílání žádosti|
|Rating |Hodnocení, které je použito předkladatelem v volání metody AddTranslation()|
|Akce|    Cílový jazyk|
|Přeložený text|    Překlad, jak byl odeslán v volání metody AddTranslation()|
|Uri|   Identifikátor URI použitý v volání metody AddTranslation()|
|Uživatel   |Uživatelské jméno|

**Výjimky**

| Výjimka | Zpráva | Podmínky |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametr '**maxDateUtc**' musí být větší nebo roven '**minDateUtc**'.| Hodnota parametru **maxDateUtc** je menší než hodnota parametru **minDateUtc**.|
| PřeložitVýjimka | IP je nad kvótou.| <ul><li>Je dosaženo limitu počtu požadavků za minutu.</li><li>Velikost požadavku zůstává omezena na 10000 znaků.</li><li>Hodinová a denní kvóta omezují počet znaků, které bude rozhraní API Microsoft Translator API přijímat.</li></ul>|
| PřeložitVýjimka | AppId je nad kvótu.| ID aplikace překročilo hodinovou nebo denní kvótu.|

> [!NOTE]
> Kvóta se přizpůsobí tak, aby byla zajištěna spravedlnost mezi všemi uživateli služby.

**Zobrazit příklady kódu na GitHib**
* [C #](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
