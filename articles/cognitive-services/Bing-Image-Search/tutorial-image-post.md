---
title: Odesílání bitové kopie Bingu přehledů | Microsoft Docs
description: Konzolová aplikace, který používá rozhraní API služby Bing Image Search přehledné bitové kopie a nahrajte image.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 12/07/2017
ms.author: v-gedod
ms.openlocfilehash: f0bf32a9951527a072fffe464f6b5f50d0f237a2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342390"
---
# <a name="tutorial-bing-image-upload-for-insights"></a>Kurz: Bitové kopie Bingu nahrát přehledů

Poskytuje rozhraní API služby Bing Image Search `POST` možnost odeslat bitové kopie a hledat informace, které jsou relevantní pro bitovou kopii. Tato Konzolová aplikace C# odešle obrázek používá koncový bod hledání bitovou kopii k získání podrobností o bitové kopii.
Ve výsledcích stručně řečeno, jsou objekty JSON například následující:

![[Výsledky JSON]](media/cognitive-services-bing-images-api/jsonResult.jpg)

Tento kurz vysvětluje následující postupy:

> [!div class="checklist"]
> * Použijte funkci vyhledávání Image `/details` koncového bodu v `POST` požadavku
> * Zadejte hlavičky pro dané žádosti
> * Použijte parametry adresy URL k určení výsledky
> * Odešle data bitové kopie a odeslat `POST` požadavku
> * Tisk výsledků JSON do konzoly

## <a name="app-components"></a>Součásti aplikace

Kurz aplikace obsahuje tři části:

> [!div class="checklist"]
> * Konfigurace koncového bodu a zadat koncový bod hledání bitové kopie Bingu a požadované hlavičky
> * Odeslání souboru bitové kopie pro `POST` požadavek na koncový bod
> * Analýza výsledky JSON, které jsou uvedeny podrobnosti vrácená z `POST` požadavku

## <a name="scenario-overview"></a>Přehled scénáře
Existují [tři koncové body Image vyhledávání](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint). `/details` Koncového bodu můžete použít `POST` žádost s bitové kopie dat v textu požadavku.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
`modules` Následující parametr URL `/details?` Určuje, jaký druh podrobnosti výsledky obsahují:
* `modules=All`
* `modules=RecognizedEntities` (osoby nebo místech viditelné v bitové kopii)

Zadejte `modules=All` v `POST` požadavek na načtení text JSON, který obsahuje v následujícím seznamu:
* `bestRepresentativeQuery` -Bing dotaz, který vrátí podobné nahraný obrázek bitové kopie
* `detectedObjects` například ohraničující pole nebo aktivní body v bitové kopii
* `image` metadata
* `imageInsightsToken` -token pro následné `GET` požadavek, který získá `RecognizedEntities` (osoby nebo místech viditelné v bitové kopii) 
* `imageTags`
* `pagesIncluding` -Webové stránky, které zahrnují bitovou kopii
* `relatedSearches`
* `visuallySimilarImages`

Zadejte `modules=RecognizedEntities` v `POST` požadavek na načtení pouze `imageInsightsToken`, který se používá v následné `GET` požadavku. Ji identifikuje osoby nebo umístí viditelné v bitové kopii.

## <a name="webclient-and-headers-for-the-post-request"></a>Webový klient a hlavičky pro v příspěvku požadavků
Vytvoření `WebClient` objektu a nastavit hlavičky. Všechny požadavky na rozhraní API služby Bing Search vyžadují `Ocp-Apim-Subscription-Key`. A `POST` žádost odeslat bitovou kopii musíte zadat také `ContentType: multipart/form-data`.

```
            WebClient client = new WebClient();
            client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            client.Headers["ContentType"] = "multipart/form-data"; 
```

## <a name="upload-the-image-and-get-results"></a>Odeslat bitovou kopii a získat výsledky

`WebClient` Třída zahrnuje `UpLoadFile` metoda, která formáty dat pro `POST` požadavku. Formátuje `RequestStream` a volání `HttpWebRequest`, zabraňující spoustu složitost.
Volání `WebClient.UpLoadFile` s `/details` koncový bod a soubor bitové kopie k odeslání. Odpověď je binární data, která je snadno převést na JSON. 

Použijte JSON text k chybě při inicializaci instance `SearchResult` struktury (najdete v článku [zdrojovému kódu aplikace](tutorial-image-post-source.md) pro kontext).
```        
         const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";

        // The image to upload. Replace with your file and path.
        const string imageFile = "ansel-adams-tetons-snake-river.jpg";
            
        byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
        var json = System.Text.Encoding.Default.GetString(resp);

        // Create result object for return
        var searchResult = new SearchResult()
        {
            jsonResult = json,
            relevantHeaders = new Dictionary<String, String>()
        };
```

## <a name="print-the-results"></a>Tisk výsledků
Zbytek kód analyzuje výsledku JSON a vytiskne ji do konzoly.

```
        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
```
## <a name="get-request-using-the-imageinsightstoken"></a>ZÍSKAT žádost o použití ImageInsightsToken
Použít `ImageInsightsToken` vrátí s výsledky `POST`, vytvoření `GET` požadavek takto:
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```
Pokud existují osobní osoby nebo místech bitovou kopii, tento požadavek vrátí informace o nich.
[– Elementy QuickStart](https://docs.microsoft.com/azure/cognitive-services/bing-image-search) obsahovat mnoho příklady kódu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Referenční dokumentace rozhraní API vyhledávání bitové kopie Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

