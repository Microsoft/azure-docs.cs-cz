---
title: Jak přidat objekty blob do objektů – digitální vlákna Azure | Microsoft Docs
description: Naučte se přidávat objekty blob do uživatelů, zařízení a mezer do digitálních vláken Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2019
ms.custom: seodec18
ms.openlocfilehash: 6ab9d0ae07978e69bebb0fc24c8965cce971cfd5
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082335"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Přidávání objektů blob do objektů v digitálních proobjektech Azure

Objekty blob jsou nestrukturované reprezentace běžných typů souborů, jako jsou obrázky a protokoly. Objekty blob sledují druh dat, která představují, pomocí typu MIME (například image/jpeg) a metadata (název, popis, typ a tak dále).

Digitální vlákna Azure podporuje připojení objektů BLOB k zařízením, prostorům a uživatelům. Objekty blob mohou představovat profilový obrázek pro uživatele, fotografii zařízení, video, mapu, zip firmwaru, data JSON, protokol atd.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Přehled nahrávání objektů BLOB

Pro nahrání objektů blob do konkrétních koncových bodů a jejich příslušných funkcí můžete použít požadavky na více částí.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Metadata objektu BLOB

Kromě **obsahu Content-Type** a **Content-Disposition**musí požadavky na objekt BLOB s objekty BLOB v rámci služby Azure Digital requests zadat správný text JSON. Který text JSON pro odeslání závisí na typu operace požadavku HTTP, kterou provádíte.

Existují čtyři hlavní schémata JSON:

[![schémat JSON](media/how-to-add-blobs/blob-models-img.png)](media/how-to-add-blobs/blob-models-img.png#lightbox)

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

| Atribut | Typ | Popis |
| --- | --- | --- |
| **parentId** | Řetězec | Nadřazená entita k přidružení objektu BLOB k (prostorům, zařízením nebo uživatelům) |
| **name** |Řetězec | Uživatelsky přívětivý název objektu BLOB |
| **type** | Řetězec | Typ objektu BLOB – nejde použít *typ* a *typeId* .  |
| **typeId** | Integer | ID typu objektu BLOB – nelze použít *typ* a *typeId* |
| **subtype** | Řetězec | Podtyp objektu BLOB – nelze použít *podtyp* a *subtypeId* |
| **subtypeId** | Integer | ID podtypu objektu BLOB – nejde použít *podtype* a *subtypeId* . |
| **název** | Řetězec | Přizpůsobený popis objektu BLOB |
| **sharing** | Řetězec | Zda je možné objekt BLOB sdílet – výčet [`None`, `Tree`, `Global`] |

Metadata objektu BLOB se vždycky dodávají jako první blok s **typem obsahu** `application/json` nebo jako `.json` soubor. Data souborů se dodávají ve druhém bloku a můžou být z libovolného podporovaného typu MIME.

Dokumentace Swagger popisuje tato schémata modelů podrobněji.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Přečtěte si, jak používat referenční dokumentaci, jak si můžete přečíst v tématu [použití Swagger](./how-to-use-swagger.md).

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

| Atribut | Typ | Popis |
| --- | --- | --- |
| **id** | Řetězec | Jedinečný identifikátor objektu BLOB |
| **name** |Řetězec | Uživatelsky přívětivý název objektu BLOB |
| **parentId** | Řetězec | Nadřazená entita k přidružení objektu BLOB k (prostorům, zařízením nebo uživatelům) |
| **type** | Řetězec | Typ objektu BLOB – nejde použít *typ* a *typeId* .  |
| **typeId** | Integer | ID typu objektu BLOB – nelze použít *typ* a *typeId* |
| **subtype** | Řetězec | Podtyp objektu BLOB – nelze použít *podtyp* a *subtypeId* |
| **subtypeId** | Integer | ID podtypu objektu BLOB – nejde použít *podtype* a *subtypeId* . |
| **sharing** | Řetězec | Zda je možné objekt BLOB sdílet – výčet [`None`, `Tree`, `Global`] |
| **název** | Řetězec | Přizpůsobený popis objektu BLOB |
| **contentInfos** | Pole | Určuje informace o nestrukturovaných metadatech včetně verze |
| **fullName** | Řetězec | Úplný název objektu BLOB |
| **spacePaths** | Řetězec | Cesta k prostoru |

Metadata objektu BLOB se vždycky dodávají jako první blok s **typem obsahu** `application/json` nebo jako `.json` soubor. Data souborů se dodávají ve druhém bloku a můžou být z libovolného podporovaného typu MIME.

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

| Hodnota | Nahradit hodnotou |
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

Od [sebe mohou vzdálení uživatelé provádět](https://curl.haxx.se/) stejným způsobem jako požadavky na formulář v několika částech:

```bash
curl -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs" \
 -H "Authorization: Bearer YOUR_TOKEN" \
 -H "Accept: application/json" \
 -H "Content-Type: multipart/form-data" \
 -F "meta={\"ParentId\":\"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob\",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\":\"A well chosen description\",\"Sharing\":\"None\"};type=application/json" \
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Hodnota | Nahradit hodnotou |
| --- | --- |
| YOUR_TOKEN | Váš platný token OAuth 2,0 |
| YOUR_SPACE_ID | ID prostoru, ke kterému se má objekt BLOB přidružit |
| PATH_TO_FILE | Cesta k textovému souboru |

[Příklad ![kudrlinkou](media/how-to-add-blobs/curl-img.png)](media/how-to-add-blobs/curl-img.png#lightbox)

Úspěšný příspěvek vrátí ID nového objektu BLOB.

## <a name="api-endpoints"></a>Koncové body rozhraní API

Následující části popisují základní koncové body rozhraní API související s objekty BLOB a jejich funkce.

### <a name="devices"></a>Zařízení

Objekty blob můžete připojit k zařízením. Následující obrázek ukazuje referenční dokumentaci Swagger pro vaše rozhraní API pro správu. Určuje koncové body rozhraní API týkající se zařízení pro využití objektů BLOB a všechny požadované parametry cesty, které se jim budou předávat.

[![objekty blob zařízení](media/how-to-add-blobs/blobs-device-api-img.png)](media/how-to-add-blobs/blobs-device-api-img.png#lightbox)

Pokud třeba chcete aktualizovat nebo vytvořit objekt BLOB a připojit objekt BLOB k zařízení, proveďte ověřený požadavek na opravu HTTP:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_BLOB_ID* | Požadované ID objektu BLOB |

Úspěšné požadavky vrátí objekt JSON, jak je [popsáno výše](#blobs-response-data).

### <a name="spaces"></a>Live

Objekty blob můžete také připojit k prostorům. Následující obrázek uvádí všechny koncové body rozhraní API prostoru zodpovědné za zpracování objektů BLOB. Obsahuje také seznam parametrů cesty, které se mají předat do těchto koncových bodů.

[objekty blob prostoru ![](media/how-to-add-blobs/blobs-space-api-img.png)](media/how-to-add-blobs/blobs-space-api-img.png#lightbox)

Pokud například chcete vrátit objekt BLOB připojený k prostoru, proveďte ověřený požadavek HTTP GET na:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_BLOB_ID* | Požadované ID objektu BLOB |

Úspěšné požadavky vrátí objekt JSON, jak je [popsáno výše](#blobs-response-data).

Požadavek PATCH na stejný koncový bod aktualizuje popisy metadat a vytvoří verze objektu BLOB. Požadavek HTTP se provede prostřednictvím metody PATCH, spolu s potřebnými daty meta a formuláře.

### <a name="users"></a>Uživatelé

Objekty blob můžete připojit k uživatelským modelům (například k přidružení obrázku profilu). Následující obrázek ukazuje relevantní koncové body rozhraní API pro uživatele a všechny požadované parametry cesty, například `id`:

[![objekty blob uživatele](media/how-to-add-blobs/blobs-users-api-img.png)](media/how-to-add-blobs/blobs-users-api-img.png#lightbox)

Pokud například chcete načíst objekt BLOB připojený k uživateli, vytvořte ověřený požadavek HTTP GET s libovolnými požadovanými daty formuláře:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_BLOB_ID* | Požadované ID objektu BLOB |

Úspěšné požadavky vrátí objekt JSON, jak je [popsáno výše](#blobs-response-data).

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