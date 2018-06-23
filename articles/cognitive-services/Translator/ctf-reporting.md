---
title: Spolupráce překlad překladač Microsoft Reporting Framework (CTF)
description: Jak používat reporting spolupráce překlad Framework (CTF).
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: cefc630a82a56703ba4942bcad18f6e0a38b1ee5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342772"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Jak používat reporting spolupráce překlad Framework (CTF)

> [!NOTE]
> Tato metoda je zastaralá. Není k dispozici v V3.0 rozhraní API Text překladač.

> Spolupráci překlady Framework (CTF), dříve k dispozici pro V2.0 rozhraní API překladač Text, se považovat za zastaralou od 1. února 2018. Funkce AddTranslation a AddTranslationArray uživatelům povolit opravy prostřednictvím rozhraní spolupráce překlad. Po 31. ledna 2018 tyto dvě funkce nebyl přijat nový větu odesílání a uživatelům se zobrazí chybová zpráva. Tyto funkce se postupně vyřazuje z provozu a nebude nahrazen. 

>Podobné funkce je dostupná v rozhraní API překladač rozbočovače, umožňuje vytvářet vlastní překlad systému se terminologie a styl, a můžete ji pomocí ID kategorií v rozhraní API Text překladač použít. Překladač rozbočovače: [ https://hub.microsofttranslator.com ](https://hub.microsofttranslator.com). Překladač rozbočovače rozhraní API: [ https://hub.microsofttranslator.com/swagger ](https://hub.microsofttranslator.com/swagger).

Rozhraní API Reporting spolupráce překlad Framework (CTF) vrátí statistiky a skutečný obsah v úložišti CTF. Toto rozhraní API se liší od metody GetTranslations() protože ji:
* Vrátí přeložený obsahu a jeho celkový počet pouze z vašeho účtu (appId nebo účtu Azure Marketplace).
* Vrátí přeložený obsahu a jeho celkový počet bez nutnosti shoda věty zdroje.
* Nevrací automatické překlad (strojový překlad).

## <a name="endpoint"></a>Koncový bod
Koncový bod rozhraní API CTF vytváření sestav http://api.microsofttranslator.com/v2/beta/ctfreporting.svc
                        

## <a name="methods"></a>Metody
| Název |    Popis|
|:---|:---|
| GetUserTranslationCounts – metoda | Získáte překladů, které jsou vytvořené uživatelem. |
| GetUserTranslations – metoda | Načte překladů, které jsou vytvořené uživatelem. |

Tyto metody umožňují:
* Načte kompletní sadu překlady uživatele a opravy pod ID vašeho účtu ke stažení.
* Získejte seznam častých přispěvatele. Ujistěte se, že je zadané správné uživatelské jméno součástí AddTranslation().
* Vytvořte uživatelské rozhraní (UI), které umožňuje vaší důvěryhodné uživatelům zobrazit všechny dostupné kandidáty, v případě potřeby omezen na část vašeho webu, na základě předpony identifikátoru URI.

> [!NOTE]
> Obě metody jsou relativně pomalé a nákladné. Doporučujeme používat pouze.

## <a name="getusertranslationcounts-method"></a>GetUserTranslationCounts – metoda

Tato metoda získá počet překladů, které jsou vytvořené uživatelem. Poskytuje seznam překlad počty seskupené podle uriPrefix, od, pro uživatele, minRating a maxRating parametry žádosti.

**Syntaxe**

> [!div class="tabbedCodeSnippets"]
```cs
UserTranslationCount[]GetUserTranslationCounts(
           string appId,
           string uriPrefix,
           string from,
           string to,
           int? minRating,
           int? maxRating,
           string user, 
           string category
           DateTime? minDateUtc,
           DateTime? maxDateUtc,
           int? skip,
           int? take);
```

**Parametry**

| Parametr | Popis |
|:---|:---|
| appId | **Požadované** Pokud se používá k hlavičce autorizace, ponechejte pole appid prázdný jinak zadejte řetězec obsahující "Nosiče" + "" + přístupový token.|
| uriPrefix | **Volitelné** řetězec obsahující předpony identifikátoru URI překladu.|
| od | **Volitelné** řetězec představující kód jazyka překlad textu. |
| na | **Volitelné** řetězec představující kód jazyka přeložit text do.|
| minRating| **Volitelné** celočíselnou hodnotu představující minimální kvalita hodnocení pro přeložený text. Platná hodnota je mezi -10 a 10. Výchozí hodnota je 1.|
| maxRating| **Volitelné** celočíselnou hodnotu představující maximální kvality hodnocení pro přeložený text. Platná hodnota je mezi -10 a 10. Výchozí hodnota je 1.|
| uživatel | **Volitelné** řetězec, který se používá k filtrování výsledku podle odesílatel odesílání. |
| category| **Volitelné** řetězec obsahující kategorie nebo domény překladu. Tento parametr podporuje pouze výchozí možnost Obecné.|
| minDateUtc| **Volitelné** datum z Pokud chcete načíst překlady. Datum musí být ve formátu UTC. |
| maxDateUtc| **Volitelné** datum do kdy budete chtít načíst překlady. Datum musí být ve formátu UTC. |
| Přeskočit| **Volitelné** počet výsledků, které chcete nechat přeskočit na stránce. Pokud chcete možnost přeskočení prvních 20 řádky výsledků a zobrazení z 21 záznamu výsledku, můžete například zadejte 20 tohoto parametru. Výchozí hodnota tohoto parametru je 0.|
| proveďte | **Volitelné** počet výsledků, které chcete načíst. Maximální počet každého požadavku je 100. Výchozí hodnota je 100.|

> [!NOTE]
> Přeskočit a proveďte parametrů žádosti povolit stránkování pro velký počet výsledků.

**Návratová hodnota**

Výsledná sada obsahuje pole **UserTranslationCount**. Každý UserTranslationCount má následující prvky:

| Pole | Popis |
|:---|:---|
| Počet| Počet výsledků, které se načítají|
| Od | Jazyk zdrojového|
| hodnocení| Hodnocení, který je používán jako odesílatel při volání metody AddTranslation()|
| Akce| Cílový jazyk|
| URI| Identifikátor URI použít při volání metody AddTranslation()|
| Uživatel| Uživatelské jméno|

**Výjimky**

| Výjimka | Zpráva | Podmínky |
|:---|:---|:---|
| Výjimka ArgumentOutOfRangeException | Parametr '**maxDateUtc**'musí být větší než nebo rovna hodnotě'**minDateUtc**'.| Hodnota parametru **maxDateUtc** je menší než hodnota parametru **minDateUtc**.|
| TranslateApiException | IP je nad kvótu.| <ul><li>Dosažení limitu pro počet požadavků za minutu.</li><li>Velikost žádosti zůstane omezené 10000 znaků.</li><li>Každou hodinu a denní kvóta omezit počet znaků, které bude přijímat rozhraní API služby Microsoft překladač.</li></ul>|
| TranslateApiException | AppId je nad kvótu.| ID aplikace překročila kvótu pro hodinových nebo denních.|

> [!NOTE]
> Kvóta upraví zajistit rovné zacházení s všechny uživatele služby.

**Příklady kódu pro zobrazení na GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>GetUserTranslations – metoda

Tato metoda načte překladů, které jsou vytvořené uživatelem. Nabízí že překlady seskupené podle uriPrefix, od, položky, uživatele a minRating a maxRating parametry žádosti.

**Syntaxe**

> [!div class="tabbedCodeSnippets"]
```cs
UserTranslation[] GetUserTranslations (
            string appId,
            string uriPrefix,
            string from,
            string to,
            int? minRating,
            int? maxRating,
            string user, 
            string category
            DateTime? minDateUtc,
            DateTime? maxDateUtc,
            int? skip,
            int? take); 
```

**Parametry**

| Parametr | Popis |
|:---|:---|
| appId | **Požadované** Pokud se používá k hlavičce autorizace, ponechejte pole appid prázdný jinak zadejte řetězec obsahující "Nosiče" + "" + přístupový token.|
| uriPrefix| **Volitelné** řetězec obsahující předpony identifikátoru URI překladu.|
| od| **Volitelné** řetězec představující kód jazyka překlad textu.|
| na| **Volitelné** řetězec představující kód jazyka přeložit text do.|
| minRating| **Volitelné** celočíselnou hodnotu představující minimální kvalita hodnocení pro přeložený text. Platná hodnota je mezi -10 a 10. Výchozí hodnota je 1.|
| maxRating| **Volitelné** celočíselnou hodnotu představující maximální kvality hodnocení pro přeložený text. Platná hodnota je mezi -10 a 10. Výchozí hodnota je 1.|
| uživatel| **Volitelný parametr. Řetězec, který se používá k filtrování výsledků podle odesílatel odesílání**|
| category| **Volitelné** řetězec obsahující kategorie nebo domény překladu. Tento parametr podporuje pouze výchozí možnost Obecné.| 
| minDateUtc| **Volitelné** datum z Pokud chcete načíst překlady. Datum musí být ve formátu UTC.| 
| maxDateUtc| **Volitelné** datum do kdy budete chtít načíst překlady. Datum musí být ve formátu UTC.|
| Přeskočit| **Volitelné** počet výsledků, které chcete nechat přeskočit na stránce. Pokud chcete možnost přeskočení prvních 20 řádky výsledků a zobrazení z 21 záznamu výsledku, můžete například zadejte 20 tohoto parametru. Výchozí hodnota tohoto parametru je 0.|
| proveďte| **Volitelné** počet výsledků, které chcete načíst. Maximální počet každého požadavku je 100. Výchozí hodnota je 50.|

> [!NOTE]
> Přeskočit a proveďte parametrů žádosti povolit stránkování pro velký počet výsledků.

**Návratová hodnota**

Výsledná sada obsahuje pole **UserTranslation**. Každý UserTranslation má následující prvky:

| Pole | Popis |
|:---|:---|
| CreatedDateUtc| Datum vytvoření položky pomocí AddTranslation()|
| Od| Jazyk zdrojového|
| originalText| Jazyk zdrojového text, který se používá při odesílání žádosti|
|hodnocení |Hodnocení, který je používán jako odesílatel při volání metody AddTranslation()|
|Akce|    Cílový jazyk|
|translatedText|    Překlad jako ve volání metody AddTranslation()|
|URI|   Identifikátor URI použít při volání metody AddTranslation()|
|Uživatel   |Uživatelské jméno|

**Výjimky**

| Výjimka | Zpráva | Podmínky |
|:---|:---|:---|
| Výjimka ArgumentOutOfRangeException | Parametr '**maxDateUtc**'musí být větší než nebo rovna hodnotě'**minDateUtc**'.| Hodnota parametru **maxDateUtc** je menší než hodnota parametru **minDateUtc**.|
| TranslateApiException | IP je nad kvótu.| <ul><li>Dosažení limitu pro počet požadavků za minutu.</li><li>Velikost žádosti zůstane omezené 10000 znaků.</li><li>Každou hodinu a denní kvóta omezit počet znaků, které bude přijímat rozhraní API služby Microsoft překladač.</li></ul>|
| TranslateApiException | AppId je nad kvótu.| ID aplikace překročila kvótu pro hodinových nebo denních.|

> [!NOTE]
> Kvóta upraví zajistit rovné zacházení s všechny uživatele služby.

**Příklady kódu pro zobrazení na GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)


















