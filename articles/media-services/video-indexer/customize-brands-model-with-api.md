---
title: Použití Azure Video Indexer k přizpůsobení modelu značek
titleSuffix: Azure Media Services
description: Tento článek ukazuje, jak použít Azure Video Indexer k přizpůsobení modelu značek.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 804cc7b2b0b51312ed756723fff5150b02f324cc
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892800"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Přizpůsobení modelu značek pomocí rozhraní Video Indexer API

Video Indexer podporuje rozpoznávání značek od mluvené řeči a vizuálního textu během indexování a přeindexování videa a zvukového obsahu. Funkce detekce značky identifikuje zmínky o produktech, službách a společnostech, které navrhla databáze značek Bingu. Například pokud se Microsoft zmiňuje v obrazovém nebo zvukovém obsahu nebo pokud se zobrazuje v vizuálním textu ve videu, Video Indexer ho detekuje jako značku v obsahu. Vlastní model značek umožňuje vyloučit z detekce některé značky a zahrnout značky, které by měly být součástí modelu, který nemusí být v databázi značek Bingu.

Podrobný přehled najdete v tématu [Přehled](customize-brands-model-overview.md).

Rozhraní Video Indexer API můžete použít k vytváření, používání a úpravám vlastních značek, které byly zjištěny ve videu, jak je popsáno v tomto tématu. Můžete také použít Video Indexer web, jak je popsáno v tématu [Přizpůsobení modelu značek pomocí webu video indexer](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Vytvoření značky

Tím se vytvoří nová vlastní značka a přidá se k modelu vlastních značek pro zadaný účet.

### <a name="request-url"></a>Adresa URL požadavku

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Podívejte se na požadované parametry a otestujte je pomocí portálu pro vývojáře video indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Vyžaduje**|**Popis**|
|---|---|---|---|
|location|string|Ano|Oblast Azure, do které má být hovor směrován. Další informace najdete v tématu [oblasti a video indexer Azure](regions.md).|
|accountId|string|Ano|Globálně jedinečný identifikátor účtu|
|accessToken|string|Ano|Přístupový token (musí být v oboru [přístupového tokenu účtu](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pro ověřování proti volání. Přístupové tokeny vyprší do 1 hodiny.|

### <a name="request-body"></a>Text požadavku

Kromě těchto parametrů je nutné zadat objekt JSON pro tělo požadavku, který poskytuje informace o nové značce následující po formátu níže uvedeného příkladu.

```json
{
  "name": "Example",
  "enabled": true,
  "tags": ["Tag1", "Tag2"],
  "description": "This is an example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example"
}
```

Nastavením vlastnosti **Enabled** na hodnotu true se zobrazí značka v seznamu *vložených* video indexer pro detekci. Nastavením **Enabled** na false (NEPRAVDA) zůstane značka v seznamu *vyloučení* , takže ji video indexer nezjistí.

Hodnota **referenceUrl** může být libovolnými referenčními weby pro značku, jako je například odkaz na její stránku Wikipedii.

Hodnota **značky** je seznam značek pro značku. To se zobrazuje v poli *kategorie* značky na webu video indexer. Například značka "Azure" může být označená nebo Kategorizovaná jako "Cloud".

### <a name="response"></a>Odpověď

Odpověď poskytuje informace o značce, kterou jste právě vytvořili, podle formátu níže uvedeného příkladu.

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

## <a name="delete-a-brand"></a>Odstranění značky

Odebere značku z modelu vlastních značek pro zadaný účet. Účet je zadaný v parametru **accountId** . Po úspěšném volání už nebude značka v seznamech *zahrnutí* nebo *vyloučení* značek.

### <a name="request-url"></a>Adresa URL požadavku

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Podívejte se na požadované parametry a otestujte je pomocí portálu pro vývojáře video indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Vyžaduje**|**Popis**|
|---|---|---|---|
|location|string|Ano|Oblast Azure, do které má být hovor směrován. Další informace najdete v tématu [oblasti a video indexer Azure](regions.md).|
|accountId|string|Ano|Globálně jedinečný identifikátor účtu|
|id|celé číslo|Ano|ID značky (vygenerované při vytvoření značky)|
|accessToken|string|Ano|Přístupový token (musí být v oboru [přístupového tokenu účtu](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pro ověřování proti volání. Přístupové tokeny vyprší do 1 hodiny.|

### <a name="request-body"></a>Text požadavku

Pro toto volání není vyžadováno žádné další tělo žádosti.

### <a name="response"></a>Odpověď

Po úspěšném odstranění značky se nevrátí žádný obsah.

## <a name="get-a-specific-brand"></a>Získat konkrétní značku

To vám umožní vyhledat podrobnosti o značce v modelu vlastních značek pro zadaný účet pomocí ID značky.

### <a name="request-url"></a>Adresa URL požadavku

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Podívejte se na požadované parametry a otestujte je pomocí portálu pro vývojáře video indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Vyžaduje**|**Popis**|
|---|---|---|---|
|location|string|Ano|Oblast Azure, do které má být hovor směrován. Další informace najdete v tématu [oblasti a video indexer Azure](regions.md).|
|accountId|string|Ano|Globálně jedinečný identifikátor účtu|
|id|celé číslo|Ano|ID značky (vygenerované při vytvoření značky)|
|accessToken|string|Ano|Přístupový token (musí být v oboru [přístupového tokenu účtu](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pro ověřování proti volání. Přístupové tokeny vyprší do 1 hodiny.|

### <a name="request-body"></a>Text požadavku

Pro toto volání není vyžadováno žádné další tělo žádosti.

### <a name="response"></a>Odpověď

Odpověď poskytuje informace o značce, kterou jste hledali (pomocí ID značky), a to podle formátu níže uvedeného příkladu.

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
> možnost nastavená na **hodnotu true** znamená, že značka je v seznamu *vložených* hodnot video indexer ke zjištění a **povolená** hodnota false znamená, že se značka nachází v seznamu *vyloučení* , takže video indexer ji nerozpozná.

## <a name="update-a-specific-brand"></a>Aktualizace konkrétní značky

To vám umožní vyhledat podrobnosti o značce v modelu vlastních značek pro zadaný účet pomocí ID značky.

### <a name="request-url"></a>Adresa URL požadavku

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Podívejte se na požadované parametry a otestujte je pomocí portálu pro vývojáře video indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Vyžaduje**|**Popis**|
|---|---|---|---|
|location|string|Ano|Oblast Azure, do které má být hovor směrován. Další informace najdete v tématu [oblasti a video indexer Azure](regions.md).|
|accountId|string|Ano|Globálně jedinečný identifikátor účtu|
|id|celé číslo|Ano|ID značky (vygenerované při vytvoření značky)|
|accessToken|string|Ano|Přístupový token (musí být v oboru [přístupového tokenu účtu](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pro ověřování proti volání. Přístupové tokeny vyprší do 1 hodiny.|

### <a name="request-body"></a>Text požadavku

Kromě těchto parametrů je nutné zadat objekt JSON pro tělo požadavku, který poskytuje aktualizované informace o značce, kterou chcete aktualizovat, podle formátu níže uvedeného příkladu.

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
> V tomto příkladu se tady aktualizuje značka, která byla vytvořena v části příklad těla žádosti v části **vytvoření značky** , a nové označení a nový popis. Hodnota **Enabled** byla také změněna na hodnotu false, aby byla vložena do seznamu *vyloučení* .

### <a name="response"></a>Odpověď

Odpověď poskytuje aktualizované informace o značce, kterou jste aktualizovali podle formátu níže uvedeného příkladu.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
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

## <a name="get-all-of-the-brands"></a>Získání všech značek

Vrátí všechny značky v modelu vlastních značek pro zadaný účet bez ohledu na to, zda je značka v seznamu *zahrnutí* nebo *vyloučení* značek.

### <a name="request-url"></a>Adresa URL požadavku

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Podívejte se na požadované parametry a otestujte je pomocí portálu pro vývojáře video indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Vyžaduje**|**Popis**|
|---|---|---|---|
|location|string|Ano|Oblast Azure, do které má být hovor směrován. Další informace najdete v tématu [oblasti a video indexer Azure](regions.md).|
|accountId|string|Ano|Globálně jedinečný identifikátor účtu|
|accessToken|string|Ano|Přístupový token (musí být v oboru [přístupového tokenu účtu](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pro ověřování proti volání. Přístupové tokeny vyprší do 1 hodiny.|

### <a name="request-body"></a>Text požadavku

Pro toto volání není vyžadováno žádné další tělo žádosti.

### <a name="response"></a>Odpověď

Odpověď poskytuje seznam všech značek ve vašem účtu a každé z jejich podrobností podle formátu níže uvedeného příkladu.

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
> Značka s názvem *example* je v seznamu *include* pro video indexer k detekci a značka s názvem *example2* je v seznamu *vyloučení* , takže video indexer ji nerozpozná.

## <a name="get-brands-model-settings"></a>Získat nastavení modelu značek

Tím se vrátí nastavení modelu značky v zadaném účtu. Nastavení modelu značek představuje, zda je povoleno zjišťování z databáze značek Bing. Pokud nejsou značky Bingu povolené, Video Indexer detekuje pouze značky z vlastního modelu značek zadaného účtu.

### <a name="request-url"></a>Adresa URL požadavku

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Podívejte se na požadované parametry a otestujte je pomocí portálu pro vývojáře video indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Vyžaduje**|**Popis**|
|---|---|---|---|
|location|string|Ano|Oblast Azure, do které má být hovor směrován. Další informace najdete v tématu [oblasti a video indexer Azure](regions.md).|
|accountId|string|Ano|Globálně jedinečný identifikátor účtu|
|accessToken|string|Ano|Přístupový token (musí být v oboru [přístupového tokenu účtu](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pro ověřování proti volání. Přístupové tokeny vyprší do 1 hodiny.|

### <a name="request-body"></a>Text požadavku

Pro toto volání není vyžadováno žádné další tělo žádosti.

### <a name="response"></a>Odpověď

Odpověď ukazuje, jestli jsou značky Bingu povolené, a to podle formátu níže uvedeného příkladu.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> **useBuiltIn** nastavenou na hodnotu true představuje, že jsou značky Bingu povolené. Pokud je *useBuiltin* false, značky Bingu jsou zakázané. Hodnota **stavu** se může ignorovat, protože je zastaralá.

## <a name="update-brands-model-settings"></a>Aktualizace nastavení modelu značek

Tím se aktualizuje nastavení modelu značky v zadaném účtu. Nastavení modelu značek představuje, zda je povoleno zjišťování z databáze značek Bing. Pokud nejsou značky Bingu povolené, Video Indexer detekuje pouze značky z vlastního modelu značek zadaného účtu.

### <a name="request-url"></a>Adresa URL požadavku:
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/BrandsModelSettings?accessToken={accessToken}
```

[Podívejte se na požadované parametry a otestujte je pomocí portálu pro vývojáře video indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?).

### <a name="request-parameters"></a>Parametry žádosti

|**Název**|**Typ**|**Vyžaduje**|**Popis**|
|---|---|---|---|
|location|string|Ano|Oblast Azure, do které má být hovor směrován. Další informace najdete v tématu [oblasti a video indexer Azure](regions.md).|
|accountId|string|Ano|Globálně jedinečný identifikátor účtu|
|accessToken|string|Ano|Přístupový token (musí být v oboru [přístupového tokenu účtu](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) pro ověřování proti volání. Přístupové tokeny vyprší do 1 hodiny.|

### <a name="request-body"></a>Text požadavku

Kromě těchto parametrů je nutné zadat objekt JSON pro tělo požadavku, který poskytuje informace o nové značce následující po formátu níže uvedeného příkladu.

```json
{
    "useBuiltIn":true
}
```

> [!NOTE]
> **useBuiltIn** nastavenou na hodnotu true představuje, že jsou značky Bingu povolené. Pokud je *useBuiltin* false, značky Bingu jsou zakázané.

### <a name="response"></a>Odpověď

Po úspěšné aktualizaci nastavení modelu značek se nevrátí žádný obsah.

## <a name="next-steps"></a>Další kroky

[Přizpůsobení modelu značek pomocí webu](customize-brands-model-with-website.md)
