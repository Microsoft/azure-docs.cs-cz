---
title: Použití rozhraní API pro Video Indexer k přizpůsobení modelu osoby – Azure
titleSuffix: Azure Media Services
description: Tento článek popisuje, jak přizpůsobit model osoby pomocí rozhraní Video Indexer API.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 44f97e3d9af9daac8d62ae42be76bd73dedbd453
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838270"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Přizpůsobení modelu osoby pomocí rozhraní Video Indexer API

Video Indexer podporuje rozpoznávání tváře a rozpoznávání celebrit pro obsah videa. Funkce rozpoznávání celebrit se zabývá přibližně 1 000 000 obličejemi na základě běžně vyžádaného zdroje dat, jako jsou IMDB, Wikipedii a nejvyšší vlivy na LinkedIn. Jsou zjištěny plošky, které funkce rozpoznávání celebrit nerozpoznala; zůstanou ale bez názvu. Po nahrání videa do Video Indexer a získání výsledků zpět můžete přejít zpět a pojmenovat nerozpoznané obličeje. Po označení obličeje názvem se vytvoří obličej a název, který se přidá do modelu osoby vašeho účtu. Video Indexer pak tuto plošku rozpozná v budoucích videích a minulých videích.

Pomocí rozhraní Video Indexer API můžete upravit plošky zjištěné ve videu, jak je popsáno v tomto tématu. Můžete také použít Video Indexer web, jak je popsáno v tématu [Přizpůsobení modelu osoby pomocí webu video indexer](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Správa modelů s více osobami 

Video Indexer podporuje různé modely osob na účet. Tato funkce je aktuálně dostupná jenom prostřednictvím rozhraní API pro Video Indexer.

Pokud vaše účty využívají různé scénáře použití, můžete chtít vytvořit pro každý účet více modelů osob. Pokud je váš obsah například v souvislosti s sportem, můžete vytvořit samostatný pracovní model pro každého sportu (fotbal, basketbalový, fotbal atd.). 

Po vytvoření modelu je můžete použít k poskytnutí ID modelu konkrétního modelu osoby při nahrávání/indexování nebo přeindexování videa. Školení nové plochy pro video aktualizuje konkrétní vlastní model, ke kterému byl video přidruženo.

Každý účet má omezení 50 modelů osob. Pokud nepotřebujete podporu modelu více osob, při nahrávání/indexování nebo přeindexování nepřiřazujte své video ID modelu osoby. V takovém případě používá Video Indexer ve vašem účtu výchozí model vlastní osoby.

## <a name="create-a-new-person-model"></a>Vytvoření nového modelu osoby

Vytvoří nový model osoby v zadaném účtu. 

### <a name="request-url"></a>Adresa URL požadavku

Toto je požadavek POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Níže je požadavek v kudrlinkou.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}"
```

[Podívejte se na požadované parametry a otestujte je pomocí portálu pro vývojáře video indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?).

### <a name="request-parameters"></a>Parametry žádosti 

|**Název**|**Typ**|**Požadovanou**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblast Azure, do které má být hovor směrován. Další informace najdete v tématu [oblasti a video indexer Azure](regions.md).|
|ID|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|jméno|řetězec|Ano|Název pro model osoby|
|accessToken|řetězec|Ano|Přístupový token (musí být v oboru [přístupového tokenu účtu](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pro ověřování proti volání. Přístupové tokeny vyprší do 1 hodiny.|

### <a name="request-body"></a>Text požadavku

Pro toto volání není vyžadováno žádné další tělo žádosti.

### <a name="response"></a>Odpověď

Odpověď poskytuje název a generovaný identifikátor ID modelu osoby, který jste právě vytvořili, podle formátu níže uvedeného příkladu.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Při [nahrávání videa k indexování](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) nebo [přeindexování videa](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)byste pak měli použít hodnotu **ID** parametru **personModelId** .

## <a name="delete-a-person-model"></a>Odstranění modelu osoby

Odstraní vlastní model osoby ze zadaného účtu. 

Po úspěšném odstranění modelu osoby se index vašich aktuálních videí, která používala odstraněný model, zůstane beze změny, dokud je znovu neindexujte. Po přeindexování se Video Indexer ve vašich současných videích, která byla indexována pomocí tohoto modelu, nerozpoznala. Tyto plošky se ale ještě budou detekovat. Vaše aktuální videa, která byla indexována pomocí odstraněného modelu, teď budou používat výchozí pracovní model vašeho účtu. Pokud se plošky z odstraněného modelu také pojmenují ve výchozím modelu vašeho účtu, budou tyto plošky i nadále rozpoznány ve videích.

### <a name="request-url"></a>Adresa URL požadavku

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}
```

Níže je požadavek v kudrlinkou.
```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}"
```

[Podívejte se na požadované parametry a otestujte je pomocí portálu pro vývojáře video indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Požadovanou**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblast Azure, do které má být hovor směrován. Další informace najdete v tématu [oblasti a video indexer Azure](regions.md).|
|ID|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|id|řetězec|Ano|ID modelu osoby (vygenerované při vytvoření modelu osoby)|
|accessToken|řetězec|Ano|Přístupový token (musí být v oboru [přístupového tokenu účtu](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pro ověřování proti volání. Přístupové tokeny vyprší do 1 hodiny.|

### <a name="request-body"></a>Text požadavku

Pro toto volání není vyžadováno žádné další tělo žádosti.

### <a name="response"></a>Odpověď

Po úspěšném odstranění modelu osoby se nevrátí žádný obsah.

## <a name="get-all-person-models"></a>Získat modely všech osob

Získá všechny modely osob v zadaném účtu. 

### <a name="request-call"></a>Žádost o volání

Toto je požadavek GET.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Níže je požadavek v kudrlinkou.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}"
```

[Podívejte se na požadované parametry a otestujte je pomocí portálu pro vývojáře video indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Požadovanou**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblast Azure, do které má být hovor směrován. Další informace najdete v tématu [oblasti a video indexer Azure](regions.md).|
|ID|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|accessToken|řetězec|Ano|Přístupový token (musí být v oboru [přístupového tokenu účtu](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pro ověřování proti volání. Přístupové tokeny vyprší do 1 hodiny.|

### <a name="request-body"></a>Text požadavku

Pro toto volání není vyžadováno žádné další tělo žádosti.

### <a name="response"></a>Odpověď

Odpověď obsahuje seznam všech obchodních modelů v účtu (včetně výchozího modelu osoby v zadaném účtu) a každé z jejich názvů a ID podle formátu níže uvedeného příkladu.

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

Můžete zvolit model, který chcete použít pro video, a to pomocí hodnoty **ID** modelu osoby pro parametr **personModelId** při [nahrávání videa do indexu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) nebo při [přeindexování videa](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Aktualizovat obličej

Tento příkaz umožňuje aktualizovat obličej ve videu s názvem s použitím ID videa a ID obličeje. Tím se pak aktualizuje model osoby, ke kterému se video přidružil při nahrávání, indexování nebo přeindexování. Pokud nebyl přiřazen žádný model osoby, aktualizuje se výchozí model osoby účtu. 

Jakmile k tomu dojde, rozpozná výskyty stejné plošky ve vašich dalších současných videích, které sdílejí stejný model Person. Rozpoznávání obličeje v ostatních aktuálních videích může nějakou dobu trvat, protože se jedná o dávkový proces.

Můžete aktualizovat obličej, který Video Indexer rozpoznat jako celebrit s novým názvem. Nový název, který udělíte, bude mít přednost před integrovaným rozpoznáváním celebrit.

### <a name="request-call"></a>Žádost o volání

Toto je požadavek POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}
```

Níže je požadavek v kudrlinkou.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}"
```

[Podívejte se na požadované parametry a otestujte je pomocí portálu pro vývojáře video indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Požadovanou**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblast Azure, do které má být hovor směrován. Další informace najdete v tématu [oblasti a video indexer Azure](regions.md).|
|ID|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|videoId|řetězec|Ano|ID videa, ve kterém se zobrazí plocha, kterou chcete aktualizovat Tato se vytvoří, když se video nahraje a naindexuje.|
|faceId|celé číslo|Ano|ID pro plochu, která se aktualizuje FaceId můžete získat z indexu videa.|
|accessToken|řetězec|Ano|Přístupový token (musí být v oboru [přístupového tokenu účtu](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pro ověřování proti volání. Přístupové tokeny vyprší do 1 hodiny.|
|jméno|řetězec|Ano|Nový název, ve kterém se má obličej aktualizovat|

Názvy jsou pro pracovní modely jedinečné, takže pokud poskytnete dvěma různým plochám stejného modelu stejný název, jako je stejná hodnota parametru **Name** , video indexer zobrazení obličeje jako stejnou osobu a po přeindexaci videa je konverguje. 

### <a name="request-body"></a>Text požadavku

Pro toto volání není vyžadováno žádné další tělo žádosti.

### <a name="response"></a>Odpověď

V případě, že se obličej úspěšně aktualizoval, nevrátí se žádný obsah.

## <a name="next-steps"></a>Další kroky

[Přizpůsobení modelu osoby pomocí Video Indexer webu](customize-person-model-with-website.md)
