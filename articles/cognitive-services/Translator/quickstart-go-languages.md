---
title: 'Rychlý start: Získat podporované jazyky, přejděte – Translator Text API'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu získáte seznam jazyků podporovaných pro překlad, transkripci a vyhledávání ve slovníku a příklady s použitím služby Translator Text API s Go.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: b6c932d6c6f3f8ce5d75f87ab86ae6bf38b0b03a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705608"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-go"></a>Rychlý start: Získání seznamu podporovaných jazycích pomocí jazyka Go pomocí rozhraní Translator Text API

V tomto rychlém startu zjistíte jak požadavek GET, který vrátí seznam hodnot pomocí Go a Translator Text REST API podporované jazyky.

>[!TIP]
> Pokud chcete zobrazit veškerý kód najednou, zdrojový kód pro tuto ukázku je k dispozici na [Githubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Go](https://golang.org/doc/install)

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

Vytvoření nového projektu přejít pomocí vašeho oblíbeného prostředí IDE nebo editoru nebo nové složky na vašem počítači. Tento fragment kódu zkopírujte do složky vašeho projektu nebo do souboru s názvem `get-languages.go`.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)
```

## <a name="create-the-main-function"></a>Vytvoření funkce main

Pojďme vytvořit hlavní funkci pro naši aplikaci. Můžete si všimnout, že se že jedná jediný řádek kódu. Důvodem je, vytváříme jednu funkci, kterou chcete získat a tisknout seznam podporovaných jazyků pro Translator Text.

Zkopírujte do svého projektu tento kód:

```go
func main() {
    /*
     * This calls our getLanguages function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    getLanguages()
}
```

## <a name="create-a-function-to-get-a-list-of-supported-languages"></a>Vytvořit funkci zobrazíte seznam podporovaných jazyků

Pojďme vytvořit funkci, kterou chcete získat seznam podporovaných jazyků.

```go
func getLanguages() {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Dále můžeme se vytvoří adresa URL. Adresa URL se vytvořil pomocí `Parse()` a `Query()` metody.

Zkopírujte tento kód do `getLanguages` funkce.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/languages")
q := u.Query()
q.Add("api-version", "3.0")
u.RawQuery = q.Encode()
```

>[!NOTE]
> Další informace o koncových bodech, cesty a parametry požadavku najdete v tématu [Translator Text API 3.0: Jazyky](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

## <a name="build-the-request"></a>Žádost o sestavení

Teď, když jste kódování textu žádosti jako dokumenty JSON, můžete vytvořit váš požadavek POST a volání rozhraní Translator Text API.

```go
// Build the HTTP GET request
req, err := http.NewRequest("GET", u.String(), nil)
if err != nil {
    log.Fatal(err)
}
// Add required headers
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

Pokud používáte víc služeb předplatného služeb Cognitive Services, musíte taky zahrnout `Ocp-Apim-Subscription-Region` v parametry požadavku. [Další informace o dvojúrovňovém víc služeb předplatného](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="handle-and-print-the-response"></a>Zpracování a tisku odpověď

Přidejte tento kód `getLanguages` funkce, která se dekódovat odpověď JSON a poté ho naformátujte a vytiskne výsledek.

```go
// Decode the JSON response
var result interface{}
if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
    log.Fatal(err)
}
// Format and print the response to terminal
prettyJSON, _ := json.MarshalIndent(result, "", "  ")
fmt.Printf("%s\n", prettyJSON)
```

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

To je vše, sestavili jste jednoduchý program, který zavolá službu Translator Text API a vrátí odpověď JSON. Teď je čas program spustit:

```console
go run get-languages.go
```

Pokud chcete porovnat svůj kód s naším, kompletní ukázka je k dispozici na [GitHubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Ukázková odpověď

Najít v této zemi/oblast – zkratka [seznam jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu:

```json
{
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  },
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>Další postup

Podívejte se na referenční dokumentace rozhraní API o všechno, co můžete dělat s rozhraním Translator Text API.

> [!div class="nextstepaction"]
> [Referenční materiály k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="see-also"></a>Další informace najdete v tématech

Další informace o použití rozhraní Translator Text API na:

* [Překlad textu](quickstart-go-translate.md)
* [Transliterace textu](quickstart-go-transliterate.md)
* [Identifikace jazyka podle vstupu](quickstart-go-detect.md)
* [Získání alternativních překladů](quickstart-go-dictionary.md)
* [Určení délky věty ze vstupu](quickstart-go-sentences.md)
