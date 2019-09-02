---
title: Jak přidat objekty blob do objektů v Azure Digital provlákna | Microsoft Docs
description: Naučte se přidávat objekty blob do objektů v digitálních proobjektech Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/09/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 61c09435606612377781fb382d2d31144e96b07b
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965933"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Přidávání objektů blob do objektů v digitálních proobjektech Azure

Objekty blob jsou nestrukturované reprezentace běžných typů souborů, jako jsou obrázky a protokoly. Objekty blob sledují druh dat, která představují, pomocí typu MIME (například image/jpeg) a metadata (název, popis, typ a tak dále).

Digitální vlákna Azure podporuje připojení objektů BLOB k zařízením, prostorům a uživatelům. Objekty blob mohou představovat profilový obrázek pro uživatele, fotografii zařízení, video, mapu, zip firmwaru, data JSON, protokol atd.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Přehled nahrávání objektů BLOB

Pro nahrání objektů blob do konkrétních koncových bodů a jejich příslušných funkcí můžete použít požadavky na více částí.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Metadata objektu blob

Kromě **obsahu Content-Type** a **Content-Disposition**musí požadavky na objekt BLOB s objekty BLOB v rámci služby Azure Digital requests zadat správný text JSON. Který text JSON pro odeslání závisí na typu operace požadavku HTTP, kterou provádíte.

Existují čtyři hlavní schémata JSON:

[![Schémata JSON](media/how-to-add-blobs/blob-models-img.png)](media/how-to-add-blobs/blob-models-img.png#lightbox)

Metadata objektu BLOB JSON odpovídají následujícímu modelu:

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

| Atribut | type | Popis |
| --- | --- | --- |
| **parentId** | String | Nadřazená entita k přidružení objektu BLOB k (prostorům, zařízením nebo uživatelům) |
| **name** |String | Uživatelsky přívětivý název objektu BLOB |
| **type** | String | Typ objektu BLOB – nejde použít *typ* a *typeId* .  |
| **typeId** | Integer | ID typu objektu BLOB – nelze použít *typ* a *typeId* |
| **subtype** | String | Podtyp objektu BLOB – nelze použít *podtyp* a *subtypeId* |
| **subtypeId** | Integer | ID podtypu objektu BLOB – nejde použít podtype a *subtypeId* . |
| **název** | String | Přizpůsobený popis objektu BLOB |
| **sharing** | String | Zda je možné objekt BLOB sdílet-enum [`None`, `Tree`, `Global`] |

Metadata objektů BLOB jsou vždycky dodávána jako první blok s **typem** `application/json` obsahu nebo jako `.json` soubor. Data souborů se dodávají ve druhém bloku a můžou být z libovolného podporovaného typu MIME.

Dokumentace Swagger popisuje tato schémata modelů podrobněji.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Přečtěte si, jak používat referenční dokumentaci, jak si můžete přečíst v tématu [použití Swagger](./how-to-use-swagger.md).

<div id="blobModel"></div>

### <a name="blobs-response-data"></a>Data odpovědí objektů BLOB

Objekty blob vrácené jednotlivě v souladu s následujícím schématem JSON:

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

| Atribut | type | Popis |
| --- | --- | --- |
| **id** | String | Jedinečný identifikátor objektu BLOB |
| **name** |String | Uživatelsky přívětivý název objektu BLOB |
| **parentId** | String | Nadřazená entita k přidružení objektu BLOB k (prostorům, zařízením nebo uživatelům) |
| **type** | String | Typ objektu BLOB – nejde použít *typ* a *typeId* .  |
| **typeId** | Integer | ID typu objektu BLOB – nelze použít *typ* a *typeId* |
| **subtype** | String | Podtyp objektu BLOB – nelze použít *podtyp* a *subtypeId* |
| **subtypeId** | Integer | ID podtypu objektu BLOB – nejde použít podtype a *subtypeId* . |
| **sharing** | String | Zda je možné objekt BLOB sdílet-enum [`None`, `Tree`, `Global`] |
| **název** | String | Přizpůsobený popis objektu BLOB |
| **contentInfos** | Array | Určuje informace o nestrukturovaných metadatech včetně verze |
| **fullName** | String | Úplný název objektu BLOB |
| **spacePaths** | String | Cesta k prostoru |

Metadata objektů BLOB jsou vždycky dodávána jako první blok s **typem** `application/json` obsahu nebo jako `.json` soubor. Data souborů se dodávají ve druhém bloku a můžou být z libovolného podporovaného typu MIME.

### <a name="blob-multipart-request-examples"></a>Příklady požadavků objektu BLOB multipart

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Pokud chcete nahrát textový soubor jako objekt BLOB a přidružit ho k prostoru, proveďte ověřený požadavek HTTP POST na:

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

| Value | Nahradit hodnotou |
| --- | --- |
| USER_DEFINED_BOUNDARY | Název hranice obsahu s více částmi |

Následující kód je implementace rozhraní .NET stejného nahrávání objektů BLOB pomocí třídy [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

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

Od sebe mohou [](https://curl.haxx.se/) vzdálení uživatelé provádět stejným způsobem jako požadavky na formulář v několika částech:

[![Objekty blob zařízení](media/how-to-add-blobs/curl-img.png)](media/how-to-add-blobs/curl-img.png#lightbox)

```bash
curl
 -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs"
 -H "Authorization: Bearer YOUR_TOKEN"
 -H "Accept: application/json"
 -H "Content-Type: multipart/form-data"
 -F "meta={\"ParentId\":\"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob\",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\":\"A well chosen description\",\"Sharing\":\"None\"};type=application/json"
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Value | Nahradit hodnotou |
| --- | --- |
| YOUR_TOKEN | Váš platný token OAuth 2,0 |
| YOUR_SPACE_ID | ID prostoru, ke kterému se má objekt BLOB přidružit |
| PATH_TO_FILE | Cesta k textovému souboru |

Úspěšný příspěvek vrátí ID nového objektu BLOB (zvýrazněný červenou barvou).

## <a name="api-endpoints"></a>Koncové body rozhraní API

Následující části popisují základní koncové body rozhraní API související s objekty BLOB a jejich funkce.

### <a name="devices"></a>Zařízení

Objekty blob můžete připojit k zařízením. Následující obrázek ukazuje referenční dokumentaci Swagger pro vaše rozhraní API pro správu. Určuje koncové body rozhraní API týkající se zařízení pro využití objektů BLOB a všechny požadované parametry cesty, které se jim budou předávat.

[![Objekty blob zařízení](media/how-to-add-blobs/blobs-device-api-img.png)](media/how-to-add-blobs/blobs-device-api-img.png#lightbox)

Pokud třeba chcete aktualizovat nebo vytvořit objekt BLOB a připojit objekt BLOB k zařízení, proveďte ověřený požadavek na opravu HTTP:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_BLOB_ID* | Požadované ID objektu BLOB |

Úspěšné požadavky vrátí objekt JSON, jak je [popsáno výše](#blobModel).

### <a name="spaces"></a>Spaces

Objekty blob můžete také připojit k prostorům. Následující obrázek uvádí všechny koncové body rozhraní API prostoru zodpovědné za zpracování objektů BLOB. Obsahuje také seznam parametrů cesty, které se mají předat do těchto koncových bodů.

[![Objekty blob prostoru](media/how-to-add-blobs/blobs-space-api-img.png)](media/how-to-add-blobs/blobs-space-api-img.png#lightbox)

Pokud například chcete vrátit objekt BLOB připojený k prostoru, proveďte ověřený požadavek HTTP GET na:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_BLOB_ID* | Požadované ID objektu BLOB |

Úspěšné požadavky vrátí objekt JSON, jak je [popsáno výše](#blobModel).

Požadavek PATCH na stejný koncový bod aktualizuje popisy metadat a vytvoří verze objektu BLOB. Požadavek HTTP se provede prostřednictvím metody PATCH, spolu s potřebnými daty meta a formuláře.

### <a name="users"></a>Uživatelé

Objekty blob můžete připojit k uživatelským modelům (například k přidružení obrázku profilu). Následující obrázek ukazuje relevantní koncové body rozhraní API pro uživatele a všechny požadované parametry cesty `id`, například:

[![Objekty blob uživatele](media/how-to-add-blobs/blobs-users-api-img.png)](media/how-to-add-blobs/blobs-users-api-img.png#lightbox)

Pokud například chcete načíst objekt BLOB připojený k uživateli, vytvořte ověřený požadavek HTTP GET s libovolnými požadovanými daty formuláře:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_BLOB_ID* | Požadované ID objektu BLOB |

Úspěšné požadavky vrátí objekt JSON, jak je [popsáno výše](#blobModel).

## <a name="common-errors"></a>Běžné chyby

Běžná chyba se týká neposkytnutí správné informace hlavičky:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

Chcete-li tuto chybu vyřešit, ověřte, zda má celková žádost odpovídající hlavičku **Content-Type** :

* `multipart/mixed`
* `multipart/form-data`

Ověřte také, že každý blok s více částmi má podle potřeby odpovídající **typ obsahu** .

## <a name="next-steps"></a>Další kroky

- Další informace o referenční dokumentaci k Swagger pro digitální vlákna Azure najdete v tématu [použití služby Swagger pro digitální vlákna Azure](how-to-use-swagger.md).

- Pokud chcete nahrát objekty blob prostřednictvím post, přečtěte si téma [Konfigurace post](./how-to-configure-postman.md).