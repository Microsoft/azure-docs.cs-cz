---
title: Přidání objektů blob pro objekty v digitální dvojče Azure | Dokumentace Microsoftu
description: Zjistěte, jak přidat objekty BLOB na objekty v digitální dvojče Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: ffd7d71c33b569b396b9f8babf8105968ee525b9
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263063"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Přidat objekty BLOB na objekty v digitální dvojče Azure

Objekty BLOB jsou nestrukturovaných reprezentace běžných typů souborů, jako jsou obrázky a protokoly. Objekty BLOB sledovat, jaký druh dat, které představují pomocí typu MIME (například: "image/jpeg") a metadata (název, popis, typ a tak dále).

Azure podporuje digitální dvojče objekty BLOB se připojuje k zařízení, mezery a uživatelů. Objekty BLOB mohou představovat profilový obrázek uživatele, zařízení fotografie, video, mapy, firmwaru zip, JSON data, protokolu atd.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Nahrává se objekty BLOB – přehled

S více částmi. požadavky můžete použít k nahrání objektů blob pro konkrétní koncové body a jejich příslušné funkce.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Metadata objektu blob

Kromě **Content-Type** a **Content-Disposition**, digitální dvojče Azure blob s více částmi. požadavky nutné zadat správný text JSON. Které text JSON pro odeslání závisí na druh operace požadavku HTTP, který provádí.

Čtyři hlavní schémat JSON jsou:

![Schémat JSON][1]

Metadata objektu blob JSON odpovídá následující model:

```JSON
{
    "parentId": "00000000-0000-0000-0000-000000000000",
    "name": "My First Blob",
    "type": "Map",
    "subtype": "GenericMap",
    "description": "A well chosen description",
    "sharing": "None"
  }
```

| Atribut | Typ | Popis |
| --- | --- | --- |
| **parentId** | Řetězec | Nadřazená entita přidružení objektu blob k (mezery, zařízení nebo uživatelů) |
| **Jméno** |Řetězec | Lidské – popisný název pro objekt blob |
| **type** | Řetězec | Nelze použít typ objektu blob - *typ* a *identifikátor typeId.*  |
| **typeId** | Integer | ID typu objektu blob - nelze použít *typ* a *identifikátor typeId.* |
| **Podtyp** | Řetězec | Nelze použít podtypu objektu blob - *podtyp* a *subtypeId* |
| **subtypeId** | Integer | Podtyp ID pro tento objekt blob - nemůžou používat *podtyp* a *subtypeId* |
| **description** | Řetězec | Vlastní popis objektu blob |
| **sdílení** | Řetězec | Určuje, zda objekt blob může být na úrovni shared – výčet [`None`, `Tree`, `Global`] |

Metadata objektu BLOB se vždy uvedené jako první blok s **Content-Type** `application/json` nebo stejně jako `.json` souboru. Data souborů zadaný v druhém bloků dat a může být libovolný podporovaný typ MIME.

Swagger dokumentace popisuje tato schémata modelu ve všech podrobností.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Další informace o použití najdete v referenční dokumentaci [použití Swagger](./how-to-use-swagger.md).

<div id="blobModel"></div>

### <a name="blobs-response-data"></a>Data odpovědi objekty BLOB

Jednotlivě vrácené objekty BLOB splňovat následující schéma JSON:

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "parentId": "00000000-0000-0000-0000-000000000000",
  "type": "string",
  "subtype": "string",
  "typeId": 0,
  "subtypeId": 0,
  "sharing": "None",
  "description": "string",
  "contentInfos": [
    {
      "type": "string",
      "sizeBytes": 0,
      "mD5": "string",
      "version": "string",
      "lastModifiedUtc": "2019-01-12T00:58:08.689Z",
      "metadata": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    }
  ],
  "fullName": "string",
  "spacePaths": [
    "string"
  ]
}
```

| Atribut | Typ | Popis |
| --- | --- | --- |
| **id** | Řetězec | Jedinečný identifikátor pro objekt blob |
| **Jméno** |Řetězec | Lidské – popisný název pro objekt blob |
| **parentId** | Řetězec | Nadřazená entita přidružení objektu blob k (mezery, zařízení nebo uživatelů) |
| **type** | Řetězec | Nelze použít typ objektu blob - *typ* a *identifikátor typeId.*  |
| **typeId** | Integer | ID typu objektu blob - nelze použít *typ* a *identifikátor typeId.* |
| **Podtyp** | Řetězec | Nelze použít podtypu objektu blob - *podtyp* a *subtypeId* |
| **subtypeId** | Integer | Podtyp ID pro tento objekt blob - nemůžou používat *podtyp* a *subtypeId* |
| **sdílení** | Řetězec | Určuje, zda objekt blob může být na úrovni shared – výčet [`None`, `Tree`, `Global`] |
| **description** | Řetězec | Vlastní popis objektu blob |
| **contentInfos** | Pole | Určuje informace nestrukturovaných metadata, včetně verze |
| **fullName** | Řetězec | Úplný název objektu blob |
| **spacePaths** | Řetězec | Místo cesty |

Metadata objektu BLOB se vždy uvedené jako první blok s **Content-Type** `application/json` nebo stejně jako `.json` souboru. Data souborů zadaný v druhém bloků dat a může být libovolný podporovaný typ MIME.

### <a name="blob-multipart-request-examples"></a>Příklady s více částmi. žádost o objekt BLOB

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Nahrát textový soubor jako objekt blob a přidružte jej k mezeru, ujistěte se, ověřeného požadavku HTTP POST do:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

Spolu s následujícím textem:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| Hodnota | Nahradit hodnotou |
| --- | --- |
| USER_DEFINED_BOUNDARY | Název obsahu hranic s více částmi. |

Následující kód je implementace .NET stejné odeslání objektu blob horizontálních oddílů pomocí třídy [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

//MY_BLOB.txt is the String representation of your text file
var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

A konečně [cURL](https://curl.haxx.se/) stejným způsobem mohou uživatelé provádět požadavky vícedílného formuláře:

![Objekty BLOB zařízení][5]

```bash
curl
 -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs"
 -H "Authorization: Bearer YOUR_TOKEN"
 -H "Accept: application/json"
 -H "Content-Type: multipart/form-data"
 -F "meta={\"ParentId\": \"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\": \"A well chosen description\", \"Sharing\": \"None\"};type=application/json"
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Hodnota | Nahradit hodnotou |
| --- | --- |
| YOUR_TOKEN | Neplatný token OAuth 2.0 |
| YOUR_SPACE_ID | ID místo pro objekt blob se přidružení |
| PATH_TO_FILE | Cesta k souboru text |

Úspěšné příspěvek vrátí ID nového objektu blob (zvýrazněný červenou barvou dříve).

## <a name="api-endpoints"></a>Koncové body rozhraní API

Následující části popisují koncových bodů core týkajících se objektu blob rozhraní API a jejich funkce.

### <a name="devices"></a>Zařízení

Objekty BLOB můžete připojit k zařízení. Následující obrázek znázorňuje referenční dokumentaci Swagger pro rozhraní API pro správu. Určuje zařízení koncových bodů rozhraní API pro využití blob a cesta k požadované parametry k předání do nich.

![Objekty BLOB zařízení][2]

Například pokud chcete aktualizovat nebo vytvoření objektu blob a připojit objekt blob do zařízení, je ověřený HTTP PATCH požadavek na:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_BLOB_ID* | ID požadované objektů blob |

Úspěšné požadavky vrácení objektu JSON jako [bylo popsáno dříve](#blobModel).

### <a name="spaces"></a>Spaces

Objekty BLOB můžete také připojit na mezery. Na následujícím obrázku jsou uvedeny všechny místo koncových bodů rozhraní API zodpovědná za zpracování objektů BLOB. Obsahuje také seznam všechny parametry cesty k předání do těchto koncových bodů.

![Objekty BLOB místa][3]

Například vrátit objekt blob připojené k mezeru, je ověřený požadavek HTTP GET na:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_BLOB_ID* | ID požadované objektů blob |

Úspěšné požadavky vrácení objektu JSON jako [bylo popsáno dříve](#blobModel).

Požadavek PATCH na stejný koncový bod aktualizuje popisů metadat a vytvoří verzí objektu blob. Se požadavek HTTP pomocí metody PATCH, spolu s všechny nezbytné metadat a dat vícedílného formuláře.

### <a name="users"></a>Uživatelé

Objekty BLOB můžete připojit k modelům uživatele (například přidružit profilový obrázek). Následující obrázek ukazuje koncové body příslušné uživatelské rozhraní API a cesta k požadované parametry, jako je `id`:

![Uživatelské objekty BLOB][4]

Například k načtení objektu blob připojit na uživatele, ujistěte se, ověřeného požadavku HTTP GET s daty z jakékoli požadované formuláře:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_BLOB_ID* | ID požadované objektů blob |

Úspěšné požadavky vrácení objektu JSON jako [bylo popsáno dříve](#blobModel).

## <a name="common-errors"></a>Běžné chyby

Běžnou chybou zahrnuje není poskytující informace správné hlavičky:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

Chcete-li tuto chybu vyřešit, ověřte, zda celkový požadavek má odpovídající **Content-Type** hlavičky:

* `multipart/mixed`
* `multipart/form-data`

Také ověřte, zda každý s více částmi. blok má odpovídající **Content-Type** podle potřeby.

## <a name="next-steps"></a>Další postup

- Další informace o Swagger referenční dokumentaci pro Azure digitální dvojče, [použití Azure digitální dvojče Swagger](how-to-use-swagger.md).

- Nahrání objektů BLOB pomocí Postman, přečtěte si téma [konfigurace nástroje Postman](./how-to-configure-postman.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
[5]: media/how-to-add-blobs/curl.PNG