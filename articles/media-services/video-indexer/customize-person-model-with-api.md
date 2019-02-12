---
title: Použití rozhraní Video Indexer API k přizpůsobení modelu osobě – Azure
titlesuffix: Azure Media Services
description: Tento článek ukazuje, jak upravit model osoba s rozhraním API pro Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 6fe4f391b5337929efc3f6190a925fd1e967cf1c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55999347"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Přizpůsobení modelu osoba s rozhraním API pro Video Indexer

Video Indexer podporuje rozpoznávání tváří a rozpoznávání celebrit v případě video obsahu. Funkce rozpoznávání celebrit pokrývá přibližně jednoho milionu tváří, které jsou založené na zdroji dat běžně požadovaných například IMDB Wikipedia a horní vlivné osoby LinkedIn. Zjištění tváří, které nejsou rozpoznány aplikací funkce rozpoznávání celebrit; Nicméně jsou ponechána bez názvu. Po nahrání videa můžete do nástroje Video Indexer a získat výsledky zpět, můžete přejít zpět a pojmenujte tváří, které nebyly rozpoznány. Jakmile je označování plochy s názvem pro rozpoznávání tváře a název nechejte se přidat do vašeho účtu osoba modelu. Video Indexer pak zjistit tento tvář budoucí videa a posledních videa.

Video Indexer API můžete použít k úpravě tváří, které byly zjištěny ve videu, jak je popsáno v tomto tématu. Můžete také web Video Indexer, jak je popsáno v [osoba přizpůsobení modelu pomocí webu Video Indexer](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Správa více modelů osoby 

Video Indexer podporuje několik modelů osoba na jeden účet. Tato funkce je aktuálně k dispozici pouze prostřednictvím rozhraní API pro Video Indexer.

Pokud váš účet určeného pro různé scénáře použití, můžete chtít vytvořit více modelů osoba na jeden účet. Například pokud je váš obsah související s sportu, můžete pak vytvořit samostatný model osoba pro každý sport (fotbalu, Basketbalový, soccer atd.). 

Po vytvoření modelu, můžete zadáním ID modelu konkrétní osobě modelu při nahrávání/indexování nebo Reindexace videa. Školení nové tváře video aktualizuje konkrétní vlastního modelu, který byl přidružený videa.

Každý účet může obsahovat maximálně 50 modelů osoby. Pokud nepotřebujete podpora více modelů osoby, nepřiřazujte osoba ID modelu videa při nahrávání/indexování nebo přeindexování. V takovém případě Video Indexer využívá výchozí vlastní osoba model ve vašem účtu.

## <a name="create-a-new-person-model"></a>Vytvořit nový model osoby

Vytvořte nový model osoby v příslušném účtu. 

### <a name="request-url"></a>Adresa URL požadavku

To je požadavek POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Níže je požadavek Curl.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}"
```

[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-PersonModel?).

### <a name="request-parameters"></a>Parametry žádosti 

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|accountId|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|jméno|řetězec|Ano|Název pro model osoby|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|

### <a name="request-body"></a>Text požadavku

Neexistuje žádné další tělo požadavku, vyžaduje se pro toto volání.

### <a name="response"></a>Odpověď

Odpověď obsahuje že název a ID vytvořený model osoby, modelovat, kterou jste právě vytvořili ve formátu v příkladu níže.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Potom byste měli použít **id** hodnota **personModelId** parametr při [nahrání videí do indexu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) nebo [přeindexování video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Odstranit model osoby

Odstraňte vlastní model osoby ze zadaného účtu. 

Jakmile model osoba byl úspěšně odstraněn, index aktuální videa, které dřív používali odstraněné modelu zůstane beze změny, dokud je přeindexování. Při Reindexace, tváří, které byly v odstraněné modelu pojmenované nebude v aktuální videa, které nebyly indexovány pomocí tohoto modelu; rozpoznán modulem Video Indexer ale bude stále rozpoznán tyto plochy. Aktuální videa, které nebyly indexovány pomocí modelu odstraněné nyní používají váš účet výchozí osoba model. Pokud tváří z odstraněné modelu jsou také s názvem vašeho účtu výchozí model, budou tyto plochy rozpoznat ve videích.

### <a name="request-url"></a>Adresa URL požadavku

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}
```

Níže je požadavek Curl.
```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}"
```

[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-PersonModel?).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|accountId|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|id|řetězec|Ano|Id modelu osoby (vygeneruje, když se vytvoří model osoba)|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|

### <a name="request-body"></a>Text požadavku

Neexistuje žádné další tělo požadavku, vyžaduje se pro toto volání.

### <a name="response"></a>Odpověď

Není žádný vrácený obsah, když osoba model byl úspěšně odstraněn.

## <a name="get-all-person-models"></a>Získat všechny modely osoby

Získáte všechny modely osoby v příslušném účtu. 

### <a name="request-call"></a>Žádost o hovor

To je požadavek GET.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Níže je požadavek Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}"
```

[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-PersonModels?).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|accountId|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|

### <a name="request-body"></a>Text požadavku

Neexistuje žádné další tělo požadavku, vyžaduje se pro toto volání.

### <a name="response"></a>Odpověď

Odpověď obsahuje seznam všechny osoby modely ve vašem účtu (včetně výchozí osoba model v příslušném účtu) a všechny jejich názvy a ID ve formátu v příkladu níže.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

Můžete také kterém modelu chcete použít pro videa s využitím **id** hodnota modelu osoby pro **personModelId** parametr při [nahrání videí do indexu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) nebo [přeindexování video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Aktualizace rozpoznávání tváře

Tento příkaz umožňuje aktualizovat rozpoznávání tváře ve videu s názvem pomocí ID videa a ID typ písma. To pak aktualizuje osoba model, který byl přidružen k nahrání/indexování nebo Reindexace videa. Pokud žádný model osoba byla přiřazena, aktualizuje účtu výchozí osoba model. 

Jakmile k tomu dojde, rozpozná výskyty stejného tvář další aktuální videa, které sdílejí stejný model osoby. Rozpoznávání tváře ve vaší aktuální videa může trvat nějakou dobu projevily, je to dávkového procesu.

Můžete aktualizovat řez, který Video Indexer rozpoznán jako celebrit s novým názvem. Název, kterým dáváte přednost rozpoznávání celebrit integrované.

### <a name="request-call"></a>Žádost o hovor

To je požadavek POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}
```

Níže je požadavek Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}"
```

[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-face?).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|accountId|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|videoId|řetězec|Ano|ID videa, ve kterém se zobrazí plošku, kterou chcete aktualizovat. Tím se vytvoří při video je nahraný a indexovat.|
|funkci faceId|integer|Ano|ID pro rozpoznávání tváře, která bude aktualizována. Můžete získat funkci faceId rejstřík videí|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|
|jméno|řetězec|Ano|Nový název se aktualizovat typ písma s.|

Názvy musí být jedinečné pro osobu modely, tak Pokud dáte dva různé tváří na stejné osobě model stejné **název** hodnotu parametru, Video Indexer zobrazení plochy jako stejné osobě a sladila je po přeindexování vaše video. 

### <a name="request-body"></a>Text požadavku

Neexistuje žádné další tělo požadavku, vyžaduje se pro toto volání.

### <a name="response"></a>Odpověď

Není žádný vrácený obsah, když typ písma se úspěšně aktualizoval.

## <a name="next-steps"></a>Další postup

[Přizpůsobení modelu osoba na webu Video Indexer](customize-person-model-with-website.md)
