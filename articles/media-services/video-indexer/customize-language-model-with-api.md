---
title: Použití rozhraní Video Indexer API k přizpůsobení jazykového modelu – Azure
titlesuffix: Azure Media Services
description: Tento článek ukazuje, jak upravit jazykový model s rozhraními API pro Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/03/2018
ms.author: anzaman
ms.openlocfilehash: 95334ac326b346da23f17d3a9d494120235abace
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285365"
---
# <a name="customize-a-language-model-with-the-video-indexer-apis"></a>Upravit jazykový model s rozhraními API pro Video Indexer

Video Indexer umožňuje vytvářet vlastní jazykové modely přizpůsobit tak, že nahrajete přizpůsobení textu, a to textu z domény, jejichž slovní byste chtěli modul umožní reagovat na rozpoznávání řeči. Jakmile tréninku modelu, bude rozpoznán nová slova povolí, přizpůsobení textu. 

Podrobný přehled a osvědčené postupy pro vlastní jazykové modely, naleznete v tématu [upravit jazykový model s Video Indexer](customize-language-model-overview.md).

Rozhraní Video Indexer API můžete vytvářet a upravovat vlastní jazykové modely ve vašem účtu, jak je popsáno v tomto tématu. Můžete také použít web, jak je popsáno v [přizpůsobení jazykového modelu na webu Video Indexer](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Vytvořit jazykový model

Následující příkaz vytvoří novou vlastní jazykový model v příslušném účtu. Můžete nahrát soubory pro jazykový model v tomto volání. Alternativně můžete vytvořit jazykový model a nahrání souborů pro model později aktualizuje se jazykový model.

> [!NOTE]
> S jeho povolené soubory pro model další obsah svých souborů musí stále trénování modelu. V další části jsou pokyny na školení jazyk.

### <a name="request-url"></a>Adresa URL požadavku

To je požadavek POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Níže je požadavek Curl.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}&modelName={modelName}&language={language}"

--data-ascii "{body}" 
```

[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-PersonModel?).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|ID účtu|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|
|modelName|řetězec|Ano|Název jazykový model|
|language|řetězec|Ano|Jazyk jazykový model. <br/>**Jazyk** parametru se musí předávat jazyka ve formátu BCP-47 "jazyk oblasti značky" (např.: "en US"). Podporované jazyky jsou angličtina (en US), němčina (de-DE), španělština (es-SP), arabština (ar – třeba), francouzština (fr-FR), hindština (hi-Dobrý den), italština (it-IT), japonština (ja-JP), portugalština (pt-BR), ruština (ru-RU) a čínština (zh-CN).  |

### <a name="request-body"></a>Text požadavku

Pokud chcete nahrát soubory mají být přidány do jazykový model, musíte nahrát soubory v textu pomocí dat formuláře kromě zadání hodnot požadovaných parametrů výše. Chcete-li to provést dvěma způsoby: 

1. Klíč bude název souboru a souboru txt bude hodnota
2. Klíč bude název souboru a hodnota bude adresa URL k souboru txt

### <a name="response"></a>Odpověď

Odpověď obsahuje metadata na nově vytvořený jazykový model spolu s metadata ve všech souborech modelu formátu příklad výstupu JSON.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Trénování jazykový model

Následující příkaz trénovat vlastní jazykový model v příslušném účtu s obsahem v souborech, které byly odeslány a povolených jazykový model. 

> [!NOTE]
> Nejprve musíte vytvořit jazykový model a nahrát jeho soubory. Soubory můžete nahrát, buď při vytváření jazykového modelu nebo aktualizuje se jazykový model. 

### <a name="request-url"></a>Adresa URL požadavku

To je požadavek PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}
```

Níže je požadavek Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}"
```
 
[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5bac3cf761779a6c2ab27?).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|ID účtu|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|modelId|řetězec|Ano|Id jazyka modelu (vygeneruje, když se vytvoří jazykový model)|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|

### <a name="request-body"></a>Text požadavku

Neexistuje žádné další tělo požadavku, vyžaduje se pro toto volání.

### <a name="response"></a>Odpověď

Odpověď obsahuje metadata o nově trénovaného modelu jazyk spolu s metadata ve všech souborech modelu formátu příklad výstupu JSON.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Potom byste měli použít **id** hodnotu jazykový model pro **linguisticModelId** parametr při [nahrání videí do indexu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) a  **languageModelId** parametr při [přeindexování video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-language-model"></a>Odstranit jazykový model

Následující příkaz odstraní vlastního jazykového modelu ze zadaného účtu. Jakéhokoli videa, která se používala odstraněné jazykový model budete mít stejný index, dokud znovu Indexujte videa. Pokud znovu Indexujte videa, můžete přiřadit nového jazykového modelu na video. V opačném případě Video Indexer pomocí jeho výchozí model znovu Indexujte videa.

### <a name="request-url"></a>Adresa URL požadavku

To je požadavek DELETE.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Níže je požadavek Curl.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5ba53782606e91f65be9d?).

### <a name="request-parameters"></a>Parametry žádosti 

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|ID účtu|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|modelId|řetězec|Ano|Id jazyka modelu (vygeneruje, když se vytvoří jazykový model)|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|

### <a name="request-body"></a>Text požadavku

Neexistuje žádné další tělo požadavku, vyžaduje se pro toto volání.

### <a name="response"></a>Odpověď

Není žádný vrácený obsah, když jazykový model byl úspěšně odstraněn.

## <a name="update-a-language-model"></a>Aktualizovat jazykový model

Následující příkaz aktualizuje vlastní osoba jazykový model v příslušném účtu.

> [!NOTE]
> Musíte již jste vytvořili jazykový model. Toto volání můžete povolit nebo zakázat všechny soubory v rámci modelu, aktualizujte název jazykový model a nahrajte soubory mají být přidány do jazykového modelu.

### <a name="request-url"></a>Adresa URL požadavku

To je požadavek PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}[&modelName][&enable]
```

Níže je požadavek Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}?modelName={string}&enable={string}"

--data-ascii "{body}" 
```
 
[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b3ce85f4684240bdb78f?).

### <a name="request-parameters"></a>Parametry žádosti 

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|ID účtu|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|modelId|řetězec|Ano|Id jazyka modelu (vygeneruje, když se vytvoří jazykový model)|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|
|modelName|řetězec|Ne|Nový název, který přiřadíte k modelu|
|povolit|Boolean|Ne|Zvolte, zda jsou všechny soubory v rámci tohoto modelu (pravda) zapnutá nebo vypnutá (NEPRAVDA).|

### <a name="request-body"></a>Text požadavku

Pokud chcete nahrát soubory mají být přidány do jazykový model, musíte nahrát soubory v textu pomocí dat formuláře kromě zadání hodnot požadovaných parametrů výše. Chcete-li to provést dvěma způsoby: 

1. Klíč bude název souboru a souboru txt bude hodnota
2. Klíč bude název souboru a hodnota bude adresa URL k souboru txt

### <a name="response"></a>Odpověď

Odpověď obsahuje metadata o nově trénovaného modelu jazyk spolu s metadata ve všech souborech modelu formátu příklad výstupu JSON.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```
Můžete použít **id** soubory vrátit sem pro stažení obsahu souboru.

## <a name="update-a-file-from-a-language-model"></a>Aktualizace souboru z jazykový model

Následující příkaz umožňuje aktualizovat název a **povolit** stavu souboru ve vlastní jazykový model v příslušném účtu.

### <a name="request-url"></a>Adresa URL požadavku

To je požadavek PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}[&fileName][&enable]
```

Níže je požadavek Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}?fileName={string}&enable={string}"
```
 
[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b60547f33c1c2b2d1375?).

### <a name="request-parameters"></a>Parametry žádosti 

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|ID účtu|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|modelId|řetězec|Ano|ID jazykový model, který obsahuje soubor (vygeneruje, když se vytvoří jazykový model)|
|fileId|řetězec|Ano|ID souboru, který se právě aktualizuje (vygeneruje, když je soubor nahraných při vytváření nebo aktualizaci z jazykový model)|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|
|fileName|řetězec|Ne|Název aktualizace názvu souboru|
|povolit|Boolean|Ne|Aktualizace, jestli je tento soubor (true) zapnutá nebo vypnutá (NEPRAVDA) v jazykový model||

### <a name="request-body"></a>Text požadavku

Neexistuje žádné další tělo požadavku, vyžaduje se pro toto volání.

### <a name="response"></a>Odpověď

Odpověď obsahuje metadata souboru, který jste aktualizovali následující formát výstupu JSON příkladu níže.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```
Můžete použít **id** souboru vrátil tady ke stahování obsahu souboru.

## <a name="get-a-specific-language-model"></a>Získání konkrétní jazykový model

Následující příkaz vrátí informace o zadaný jazykový model v příslušném účtu, jako je například jazyk a soubory, které jsou v jazykový model. 

### <a name="request-url"></a>Adresa URL požadavku

To je požadavek GET.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Níže je požadavek Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b4fcbd9b437d27d53f16).

### <a name="request-parameters-and-request-body"></a>Parametry žádosti a text žádosti

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|ID účtu|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|modelId|řetězec|Ano|Id jazyka modelu (vygeneruje, když se vytvoří jazykový model)|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|

### <a name="request-body"></a>Text požadavku

Neexistuje žádné další tělo požadavku, vyžaduje se pro toto volání.

### <a name="response"></a>Odpověď

Odpověď obsahuje metadata zadané jazykový model spolu s metadat každého souboru modelu ve formátu JSON příklad následující výstup.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

Můžete použít **id** souboru vrátil tady ke stahování obsahu souboru.

## <a name="get-all-the-language-models"></a>Získání všech jazykových modelů

Následující příkaz vrátí všechny vlastní jazykové modely v příslušném účtu v seznamu.

### <a name="request-url"></a>Adresa URL požadavku

To je požadavek na získání.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}
```

Níže je požadavek Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}"
```
 
[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b4979e6ecbd30faa6f75?).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|ID účtu|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|

### <a name="request-body"></a>Text požadavku

Neexistuje žádné další tělo požadavku, vyžaduje se pro toto volání.

### <a name="response"></a>Odpověď

Odpověď obsahuje seznam všech jazykových modelů ve vašem účtu a všechny jejich metadata a soubory ve formátu JSON příklad následující výstup.

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Odstranění souboru z jazykový model

Následující příkaz odstraní zadaný soubor ze zadaného jazykový model v příslušném účtu. 

### <a name="request-url"></a>Adresa URL požadavku

To je požadavek DELETE.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}
```

Níže je požadavek Curl.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b57b5de51e64ee52242e).

### <a name="request-parameters"></a>Parametry žádosti 

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|ID účtu|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|modelId|řetězec|Ano|ID jazykový model, který obsahuje soubor (vygeneruje, když se vytvoří jazykový model)|
|fileId|řetězec|Ano|ID souboru, který se právě aktualizuje (vygeneruje, když je soubor nahraných při vytváření nebo aktualizaci z jazykový model)|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|

### <a name="request-body"></a>Text požadavku

Neexistuje žádné další tělo požadavku, vyžaduje se pro toto volání.

### <a name="response"></a>Odpověď

Není žádný vrácený obsah, když soubor byl úspěšně odstraněn z jazykový model.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Získat metadata souboru z jazykový model

Vrátí obsah a metadata o zadaný soubor ze zvoleného jazyka modelu v váš účet.

### <a name="request-url"></a>Adresa URL požadavku

To je požadavek GET.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Níže je požadavek Curl.
```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b73f9e6416d7a9965b42).

### <a name="request-parameters"></a>Parametry žádosti 

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|ID účtu|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|modelId|řetězec|Ano|ID jazykový model, který obsahuje soubor (vygeneruje, když se vytvoří jazykový model)|
|fileId|řetězec|Ano|ID souboru, který se právě aktualizuje (vygeneruje, když je soubor nahraných při vytváření nebo aktualizaci z jazykový model)|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|

### <a name="request-body"></a>Text požadavku

Neexistuje žádné další tělo požadavku, vyžaduje se pro toto volání.

### <a name="response"></a>Odpověď

Odpověď obsahuje obsah a metadata souboru ve formátu JSON, podobně jako tato:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> Obsah tohoto souboru příklad se slova "hello" a world"v dva samostatné řádky.

## <a name="download-a-file-from-a-language-model"></a>Stáhnout soubor ze jazykový model

Následující příkaz načte textový soubor s obsahem zadaného souboru ze zadaný jazykový model v příslušném účtu. Tento textový soubor by měl odpovídat obsah textového souboru, který byl původně nahrán.

### <a name="request-url"></a>Adresa URL požadavku
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}
```

Níže je požadavek Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}"
```
 
[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b99e522cb47bd9679122).

### <a name="request-parameters"></a>Parametry žádosti 

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|ID účtu|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|modelId|řetězec|Ano|ID jazykový model, který obsahuje soubor (vygeneruje, když se vytvoří jazykový model)|
|fileId|řetězec|Ano|ID souboru, který se právě aktualizuje (vygeneruje, když je soubor nahraných při vytváření nebo aktualizaci z jazykový model)|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|

### <a name="request-body"></a>Text požadavku 

Neexistuje žádné další tělo požadavku, vyžaduje se pro toto volání.

### <a name="response"></a>Odpověď

Odpověď bude stažení textový soubor s obsahem souboru ve formátu JSON. 

## <a name="next-steps"></a>Další postup

[Přizpůsobení jazykového modelu pomocí webu](customize-language-model-with-website.md)
