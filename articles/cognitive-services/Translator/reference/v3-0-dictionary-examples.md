---
title: V případě metody textová Microsoft překladač API slovník příklady | Microsoft Docs
description: Pomocí metody ukázky slovníků Microsoft překladač Text rozhraní API.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 9960f3be42090edaec1df935d70e4c1a0d25b691
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343563"
---
# <a name="text-api-30-dictionary-examples"></a>Text API 3.0: Ukázky slovníků

Obsahuje příklady, které ukazují, jak ve slovníku pojmů v kontextu. Tato operace se používá v kombinaci s [slovníku vyhledávání](.\v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Adresa URL požadavku

Odesílání `POST` požadavek na:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parametry žádosti

Žádosti jsou parametry předané na řetězec dotazu:

<table width="100%">
  <th width="20%">Parametr dotazu</th>
  <th>Popis</th>
  <tr>
    <td>verze rozhraní API.</td>
    <td>*Požadovaný parametr*.<br/>Verze rozhraní API požadovaná klientem. Hodnota musí být `3.0`.</td>
  </tr>
  <tr>
    <td>od</td>
    <td>*Požadovaný parametr*.<br/>Určuje jazyk, ze vstupního textu. Jazyk zdrojového musí mít jednu z [podporované jazyky](.\v3-0-languages.md) součástí `dictionary` oboru.</td>
  </tr>
  <tr>
    <td>na</td>
    <td>*Požadovaný parametr*.<br/>Určuje jazyk text výstupu. Cílový jazyk musí mít jednu z [podporované jazyky](.\v3-0-languages.md) součástí `dictionary` oboru.</td>
  </tr>
</table>

Hlavičky požadavku zahrnují:

<table width="100%">
  <th width="20%">Záhlaví</th>
  <th>Popis</th>
  <tr>
    <td>_Jedním autorizačním_<br/>_Záhlaví_</td>
    <td>*Hlavička požadavku požadované*.<br/>V tématu [dostupné možnosti pro ověřování](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Typ obsahu</td>
    <td>*Hlavička požadavku požadované*.<br/>Určuje typ obsahu datové části. Možné hodnoty jsou: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Hlavička požadavku požadované*.<br/>Délka textu požadavku.</td>
  </tr>
  <tr>
    <td>X ClientTraceId</td>
    <td>*Volitelné*.<br/>GUID klientem generovaná k jednoznačné identifikaci požadavku. Tuto hlavičku můžete vynechat, pokud zahrnují ID trasování v řetězci dotazu pomocí parametru dotazu s názvem `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Tělo požadavku

Text žádosti je pole JSON. Každý element pole je objekt JSON s následujícími vlastnostmi:

  * `Text`: Řetězec určující termín k vyhledávání. Měl by být hodnota `normalizedText` pole z back překlady předchozí [slovníku vyhledávání](.\v3-0-dictionary-lookup.md) požadavku. Hodnota může být také `normalizedSource` pole.

  * `Translation`: Řetězec určující přeložený text předtím vrátila rutina [slovníku vyhledávání](.\v3-0-dictionary-lookup.md) operaci. To musí být rozsahu od `normalizedTarget` pole `translations` seznam [slovníku vyhledávání](.\v3-0-dictionary-lookup.md) odpovědi. Příklady pro konkrétní cíl zdroj word dvojici vrátí službu.

Příkladem je:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Platí následující omezení:

* Pole může obsahovat maximálně 10 elementy.
* Hodnota textového elementu pole nesmí překročit 100 znaků včetně mezer.

## <a name="response-body"></a>Text odpovědi

Úspěšná odpověď je pole JSON s jeden výsledek pro každý řetězec v vstupního pole. Výsledný objekt zahrnuje následující vlastnosti:

  * `normalizedSource`: Řetězec poskytnutí normalizovaný formu termín zdroje. Obecně platí, měl by být stejný jako hodnota `Text` pole v indexu odpovídající seznamu v textu požadavku.
    
  * `normalizedTarget`: Řetězec poskytnutí normalizovaný formu cílového termínu. Obecně platí, měl by být stejný jako hodnota `Translation` pole v indexu odpovídající seznamu v textu požadavku.
  
  * `examples`: Seznam příkladů pro (zdroj termín, termín cíl) pár. Každý element seznamu je objekt s následujícími vlastnostmi:

    * `sourcePrefix`: Řetězec, který má řetězení _před_ hodnotu `sourceTerm` k vytvoření kompletní příklad. Nepřidávejte znak mezery, protože je již nainstalován při by měl být. Tato hodnota může být prázdný řetězec.

    * `sourceTerm`Vyhledávat rovno skutečný výraz: řetězec. Řetězec se přidá s `sourcePrefix` a `sourceSuffix` k vytvoření kompletní příklad. Její hodnota je oddělená, nemůže být označen v uživatelském rozhraní, například pomocí tučné ho.

    * `sourceSuffix`: Řetězec, který má řetězení _po_ hodnotu `sourceTerm` k vytvoření kompletní příklad. Nepřidávejte znak mezery, protože je již nainstalován při by měl být. Tato hodnota může být prázdný řetězec.

    * `targetPrefix`: Řetězec podobný řetězci `sourcePrefix` , ale pro cíl.

    * `targetTerm`: Řetězec podobný řetězci `sourceTerm` , ale pro cíl.

    * `targetSuffix`: Řetězec podobný řetězci `sourceSuffix` , ale pro cíl.

    > [!NOTE]
    > Pokud nejsou žádné příklady ve slovníku, odpověď je 200 (OK) ale `examples` seznam je prázdný seznam.

## <a name="examples"></a>Příklady

Tento příklad ukazuje, jak vyhledat příklady pro dvojici skládá anglické podmínek `fly` a jeho španělské překlad `volar`.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

---

Text odpovědi (zkratka pro přehlednost) je:

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```
