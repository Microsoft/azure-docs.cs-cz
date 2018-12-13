---
title: Azure Video Indexer využít k přizpůsobení modelu značky
titlesuffix: Azure Media Services
description: Tento článek ukazuje, jak pomocí Azure Video Indexer lze přizpůsobit model značky.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/03/2018
ms.author: anzaman
ms.openlocfilehash: 5591c36a13291432f2d790b4c83e98270b4bc035
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284868"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Přizpůsobení modelu značky s rozhraním API pro Video Indexer

Video Indexer podporuje značky zjišťování z pro zpracování řeči a vizuální textu během indexování a přeindexování z obrazový a zvukový obsah. Funkce detekce značka identifikuje zmínky produktů, služeb, a společnosti navrhl Bingu značky databáze. Například pokud Microsoft je uveden v videa nebo zvukový obsah nebo pokud se zobrazí ve visual textu ve videu, Video Indexer rozpozná jako značku v obsahu. Vlastní značky model umožňuje vyloučit určité značky z zjištění a zahrnout značky, které by měly být součástí modelu, který nemusí být v databázi značky Bingu.

Podrobnější přehled najdete v části [přehled](customize-brands-model-overview.md).

Video Indexer API můžete vytvářet, používat a upravovat vlastní modely značky zjistil ve videu, jak je popsáno v tomto tématu. Můžete také web Video Indexer, jak je popsáno v [značky přizpůsobení modelu pomocí webu Video Indexer](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Vytvoření značky

Tím se vytvoří nová vlastní značky a přidá ho do vlastního modelu značky pro zadaný účet. 

### <a name="request-url"></a>Adresa URL požadavku

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```
 
[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand).

### <a name="request-parameters"></a>Parametry žádosti 

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|ID účtu|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|

### <a name="request-body"></a>Text požadavku

Kromě těchto parametrů je nutné zadat objektu JSON těla požadavku, který poskytuje informace o novou značku ve formátu v příkladu níže.

```json
{
  "name": "Example",
  "enabled": true,
  "tags": ["Tag1", "Tag2"],
  "description": "This is an example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example"
}
```

Nastavení **povolené** na hodnotu true umístí značku *zahrnout* seznam pro Video Indexer ke zjištění. Nastavení **povolené** na hodnotu false umístí značku *vyloučit* seznamu, aby Video Indexer nerozpozná.

**ReferenceUrl** hodnota může být žádným webům odkaz pro značku jít například o odkaz na stránku jeho encyklopedii Wikipedia.

**Značky** hodnota je seznam značek pro značku. Tím se zobrazí v značku *kategorie* pole na webu Video Indexer. Například značku "Azure" můžete příznakem nebo klasifikovány jako "Cloud".

### <a name="response"></a>Odpověď

Odpověď obsahuje informace o značku, které jste právě vytvořili následující formát v příkladu níže.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Odstranění značek

Odebere určitou značku z vlastní značky modelu pro zadaný účet. Účet je zadán v **accountId** parametru. Po úspěšném volání značka se už nebude v *zahrnout* nebo *vyloučit* opatří seznamy.

### <a name="request-url"></a>Adresa URL požadavku

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```
 
[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|ID účtu|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|id|integer|Ano|Id značky (generovány, pokud byl vytvořen značka)|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|

### <a name="request-body"></a>Text požadavku

Neexistuje žádné další tělo požadavku, vyžaduje se pro toto volání.

### <a name="response"></a>Odpověď

Není žádný vrácený obsah, pokud značka se úspěšně odstranil.

## <a name="get-a-specific-brand"></a>Získat značky

To umožňuje hledat podrobnosti značky v vlastní značky modelu pro zadaný účet pomocí id značky. 

### <a name="request-url"></a>Adresa URL požadavku

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```
 
[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?).

### <a name="request-parameters"></a>Parametry žádosti 

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|ID účtu|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|id|integer|Ano|ID značky (generovány, pokud byl vytvořen značka)|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|

### <a name="request-body"></a>Text požadavku

Neexistuje žádné další tělo požadavku, vyžaduje se pro toto volání.

### <a name="response"></a>Odpověď

Odpověď poskytuje informace o značce prohledávat (s použitím ID značky) ve formátu v příkladu níže.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> **povolené** nastavena na **true** znamená, že značka je v *zahrnout* seznamu pro Video Indexer ke zjištění, a **povolené** se false označuje, značku se *vyloučit* seznamu, aby Video Indexer nerozpozná.

## <a name="update-a-specific-brand"></a>Aktualizace značky

Díky tomu můžete vyhledat podrobnosti značky v vlastní značky modelu pro zadaný účet pomocí ID značky. 

### <a name="request-url"></a>Adresa URL požadavku

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```
 
[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|ID účtu|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|id|integer|Ano|ID značky (generovány, pokud byl vytvořen značka)|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|

### <a name="request-body"></a>Text požadavku

Kromě těchto parametrů je nutné zadat, že objektu JSON těla požadavku, která poskytuje aktualizované informace na značku, kterou chcete aktualizovat následující formát v příkladu níže.

```json
{
  "name": "Example",
  "enabled": false,
  "tags": ["Tag1", "NewTag2"],
  "description": "This is an update example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-28T15:52:22.3413983",
}
```

> [!NOTE]
> V tomto příkladu značky, který byl vytvořen v textu požadavku příklad v **vytvořit určitou značku** oddílu se aktualizuje tady s novou značku a nový popis. **Povolené** byla hodnota změněna na hodnotu false pro její umístění do také *vyloučit* seznamu.

### <a name="response"></a>Odpověď

Odpověď obsahuje aktualizované informace o značku, kterou jste aktualizovali následující formát v příkladu níže.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampeAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Získá všechny značky

Vrátí všechny značky v vlastní značky modelu pro zadaný účet bez ohledu na to, jestli je značka určená v *zahrnout* nebo *vyloučit* seznamu značek.

### <a name="request-url"></a>Adresa URL požadavku

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```
 
[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|ID účtu|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|

### <a name="request-body"></a>Text požadavku

Neexistuje žádné další tělo požadavku, vyžaduje se pro toto volání.

### <a name="response"></a>Odpověď

Odpověď obsahuje seznam všech značek ve vašem účtu a všech jejich podrobnosti ve formátu v příkladu níže.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
        {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> Značku s názvem *příklad* probíhá *zahrnout* seznam pro Video Indexer ke zjištění a značku s názvem *priklad2* je v *vyloučit* seznamu , abyste Video Indexer nerozpozná.

## <a name="get-brands-model-settings"></a>Získat nastavení modelu značky

Vrátí nastavení modelu značky v příslušném účtu. Nastavení modelu značky představují, zda je povoleno zjišťování z databáze značky Bing, nebo ne. Pokud nejsou povoleny značky Bing, Video Indexer zjistí značky z vlastního modelu značky zadaného účtu.

### <a name="request-url"></a>Adresa URL požadavku

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```
 
[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|ID účtu|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|

### <a name="request-body"></a>Text požadavku

Neexistuje žádné další tělo požadavku, vyžaduje se pro toto volání.

### <a name="response"></a>Odpověď

Odpověď ukazuje, jestli jsou povolené značky Bingu ve formátu v příkladu níže.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> **useBuiltIn** je nastavena na hodnotu true představuje tento Bingu značky jsou povolené. Pokud *useBuiltin* je hodnota false, Bingu značky jsou zakázané. **Stavu** hodnotu můžete ignorovat, protože se už nepoužívá.

## <a name="update-brands-model-settings"></a>Aktualizovat nastavení modelu značky

Tím se aktualizuje nastavení modelu značky v příslušném účtu. Nastavení modelu značky představují, zda je povoleno zjišťování z databáze značky Bing, nebo ne. Pokud nejsou povoleny značky Bing, Video Indexer zjistí značky z vlastního modelu značky zadaného účtu.

### <a name="request-url"></a>Adresa URL požadavku: 
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/BrandsModelSettings?accessToken={accessToken}
```
 
[Zobrazit požadované parametry a otestujte pomocí portálu pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?).

### <a name="request-parameters"></a>Parametry žádosti 

|**Název**|**Typ**|**Požadováno**|**Popis**|
|---|---|---|---|
|location|řetězec|Ano|Oblasti Azure, ke které se mají směrovat volání. Další informace najdete v tématu [oblastí Azure a Video Indexer](regions.md).|
|ID účtu|řetězec|Ano|Globálně jedinečný identifikátor účtu|
|accessToken|řetězec|Ano|Přístupový token (musí být z rozsahu [účet přístupový Token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) k ověřování na základě volání. Přístupové tokeny vyprší za 1 hodinu.|

### <a name="request-body"></a>Text požadavku

Kromě těchto parametrů je nutné zadat objektu JSON těla požadavku, který poskytuje informace o novou značku ve formátu v příkladu níže.

```json
{
    "useBuiltIn":true
}
```

> [!NOTE]
> **useBuiltIn** je nastavena na hodnotu true představuje tento Bingu značky jsou povolené. Pokud *useBuiltin* je hodnota false, Bingu značky jsou zakázané.

### <a name="response"></a>Odpověď

Po úspěšné aktualizaci nastavení modelu značky není žádný vrácený obsah.

## <a name="next-steps"></a>Další postup

[Přizpůsobení modelu značky pomocí webu](customize-brands-model-with-website.md)
