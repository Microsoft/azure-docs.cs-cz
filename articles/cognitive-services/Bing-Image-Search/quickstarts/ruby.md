---
title: 'Rychlý start: Odeslání vyhledávací dotazy s využitím rozhraní REST API pro API pro vyhledávání obrázků Bingu a Ruby'
description: V tomto rychlém startu odesíláte vyhledávací dotazy do rozhraní API pro vyhledávání Bingu k získání seznamu určit prioritu relevantních obrázků pomocí Ruby.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 8/20/2018
ms.author: aahi
ms.openlocfilehash: fdc22971a369effbca31e23305ee57739852a50b
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578797"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-ruby"></a>Rychlý start: Odeslání vyhledávací dotazy s využitím rozhraní REST API a Ruby

V tomto rychlém startu můžete provést první volání do rozhraní API Bingu pro vyhledávání obrázků a získejte odpověď ve formátu JSON. Tato jednoduchá aplikace v Ruby odešle vyhledávací dotaz na rozhraní API a zobrazí nezpracované výsledky.

Zatímco tato aplikace je napsána v Ruby, je rozhraní API RESTful webová služba, která je kompatibilní s Většina programovacích jazyků.

Zdrojový kód pro tuto ukázku je k dispozici na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingImageSearchv7.rb).
## <a name="prerequisites"></a>Požadavky

* [Nejnovější verze Ruby](https://www.ruby-lang.org/en/downloads/).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytváření a inicializace aplikace

1. naimportujte následující balíčky do souboru s kódem.

    ```ruby
    require 'net/https'
    require 'uri'
    require 'json'
    ```

2. Vytváření proměnných pro koncový bod rozhraní API, cesty pro hledání obrázků rozhraní API, váš klíč předplatného a hledané výrazy.

    ```ruby
    uri  = "https://api.cognitive.microsoft.com"
    path = "/bing/v7.0/images/search"
    term = "puppies"
    ```

## <a name="format-and-make-an-api-request"></a>Formátování a proveďte požadavek rozhraní API

Použití proměnných v posledním kroku k formátování adresa URL pro hledání pro žádosti na rozhraní API. Odešlete žádost.

```ruby
uri = URI(uri + path + "?q=" + URI.escape(term))


request = Net::HTTP::Get.new(uri)
request['Ocp-Apim-Subscription-Key'] = accessKey

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end
```

## <a name="process-and-print-the-json"></a>Proces a tisk ve formátu JSON 

Po přijetí odpovědi můžete analyzovat ve formátu JSON a získat hodnoty z něj. Například miniatury vrátí adresa URL pro první výsledek a celkový počet imagí.

```ruby
response.each_header do |key, value|
    # header names are lowercased
    if key.start_with?("bingapis-") or key.start_with?("x-msedge-") then
        puts key + ": " + value
    end
end

parsed_json = JSON.parse(response.body)
total_returned_images = parsed_json["totalEstimatedMatches"]
first_result = parsed_json["value"][0]["thumbnailUrl"]

puts "total number of returned matches: #{total_returned_images}"
puts "Url to the thumbnail of the first returned search result: #{first_result}"
```

## <a name="sample-json-response"></a>Ukázková odpověď JSON

Odpovědi z rozhraní API Bingu pro vyhledávání obrázků se vrátí jako JSON. Tato ukázková odpověď byl zkrácen na zobrazit jeden výsledek.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }
}
```


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz jednostránkovou aplikaci pro vyhledávání obrázků Bingu](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech 

* [Co je pro vyhledávání obrázků Bingu?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Zkuste online interaktivní ukázka](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Získání bezplatné přístupového klíče služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Dokumentace ke službě Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Referenční dokumentace rozhraní API pro vyhledávání obrázků Bingu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)