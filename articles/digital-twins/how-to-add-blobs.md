---
title: Jak přidat objekty BLOB k objektům – Azure Digital Twins | Dokumenty společnosti Microsoft
description: Zjistěte, jak přidat objekty BLOB uživatelům, zařízením a prostorům v Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: c85db05e6feeea43023c2391998f837348caed4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75929656"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Přidání objektů BLOB k objektům v Azure Digital Twins

Objekty BLOB jsou nestrukturované reprezentace běžných typů souborů, jako jsou obrázky a protokoly. Objekty BLOB sledují, jaký druh dat představují pomocí typu MIME (například "image/jpeg") a metadat (název, popis, typ a tak dále).

Azure Digital Twins podporuje připojení objektů BLOB k zařízením, prostorům a uživatelům. Objekty BLOB mohou představovat profilový obrázek pro uživatele, fotografii zařízení, video, mapu, zip firmwaru, data JSON, protokol atd.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Přehled nahrávání objektů BLOB

Vícedílné požadavky můžete použít k nahrání objektů BLOB do konkrétních koncových bodů a jejich příslušných funkcí.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Metadata objektu blob

Kromě **typu obsahu** a **content-dispozice**, Azure Digital Twins blob vícedílné požadavky musí určit správné tělo JSON. Které tělo JSON odeslat závisí na druhu operace požadavku HTTP, která se provádí.

Čtyři hlavní schémata JSON jsou:

[![Schémata JSON](media/how-to-add-blobs/blob-models-swagger-img.png)](media/how-to-add-blobs/blob-models-swagger-img.png#lightbox)

Metadata objektů blob JSON odpovídají následujícímu modelu:

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
| **Parentid** | Řetězec | Nadřazená entita, ke které chcete přidružit objekt blob (mezery, zařízení nebo uživatele) |
| **Jméno** |Řetězec | Jméno pro blob vhodné pro člověka |
| **Typ** | Řetězec | Typ objektu blob - nelze použít *typ* a *typeId*  |
| **Typeid** | Integer | ID typu objektu blob - nelze použít *typ* a *id typu* |
| **Podtypu** | Řetězec | Podtyp objektu blob - nelze použít *podtyp* a *podtypId* |
| **podtypId** | Integer | ID podtypu pro objekt blob – nelze použít *podtyp* a *podtypId* |
| **Popis** | Řetězec | Přizpůsobený popis objektu blob |
| **Sdílení** | Řetězec | Zda lze objekt blob sdílet`None`– `Tree` `Global`výčt [ , ], ] |

Metadata objektů blob se vždy zadávají jako první `.json` blok s **typem obsahu** `application/json` nebo jako soubor. Data souboru jsou dodávána v druhém bloku dat a mohou být libovolného podporovaného typu MIME.

Swagger dokumentace popisuje tyto schémata modelu v plném rozsahu.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Informace o používání referenční dokumentace načtete [Jak používat Swagger](./how-to-use-swagger.md).

### <a name="blobs-response-data"></a>Data odpovědi objektů Blob

Jednotlivě vrácené objekty BLOB odpovídají následujícímu schématu JSON:

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
| **Id** | Řetězec | Jedinečný identifikátor objektu blob |
| **Jméno** |Řetězec | Jméno pro blob vhodné pro člověka |
| **Parentid** | Řetězec | Nadřazená entita, ke které chcete přidružit objekt blob (mezery, zařízení nebo uživatele) |
| **Typ** | Řetězec | Typ objektu blob - nelze použít *typ* a *typeId*  |
| **Typeid** | Integer | ID typu objektu blob - nelze použít *typ* a *id typu* |
| **Podtypu** | Řetězec | Podtyp objektu blob - nelze použít *podtyp* a *podtypId* |
| **podtypId** | Integer | ID podtypu pro objekt blob – nelze použít *podtyp* a *podtypId* |
| **Sdílení** | Řetězec | Zda lze objekt blob sdílet`None`– `Tree` `Global`výčt [ , ], ] |
| **Popis** | Řetězec | Přizpůsobený popis objektu blob |
| **contentInfos** | Pole | Určuje nestrukturované informace o metadatech včetně verze. |
| **Celéjméno** | Řetězec | Úplný název objektu blob |
| **spacePaths** | Řetězec | Cesta k prostoru |

Metadata objektů blob se vždy zadávají jako první `.json` blok s **typem obsahu** `application/json` nebo jako soubor. Data souboru jsou dodávána v druhém bloku dat a mohou být libovolného podporovaného typu MIME.

### <a name="blob-multipart-request-examples"></a>Příklady vícedílných požadavků objektů Blob

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Chcete-li nahrát textový soubor jako objekt blob a přidružit jej k prostoru, vytvořte ověřený požadavek HTTP POST na:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

S následujícím tělem:

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
| USER_DEFINED_BOUNDARY | Název hranice vícedílného obsahu |

Následující kód je implementace .NET stejného nahrávání objektů blob pomocí třídy [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

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

Nakonec mohou uživatelé [cURL](https://curl.haxx.se/) vytvářet vícedílné žádosti o formuláře stejným způsobem:

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
| YOUR_TOKEN | Váš platný token OAuth 2.0 |
| YOUR_SPACE_ID | ID prostoru pro přidružení objektu blob k objektu blob |
| PATH_TO_FILE | Cesta k textovému souboru |

[![příklad cURL](media/how-to-add-blobs/http-blob-post-through-curl-img.png)](media/how-to-add-blobs/http-blob-post-through-curl-img.png#lightbox)

Úspěšné POST vrátí ID nového objektu blob.

## <a name="api-endpoints"></a>Koncové body rozhraní API

Následující části popisují koncové body rozhraní API související s objektem blob a jejich funkce.

### <a name="devices"></a>Zařízení

K zařízením můžete připojit objekty BLOB. Následující obrázek znázorňuje referenční dokumentaci Swagger pro vaše nastavení API pro správu. Určuje koncové body rozhraní API související se zařízením pro spotřebu objektů blob a všechny parametry požadované cesty, které mají být do nich předávány.

[![Objekty BLOB zařízení](media/how-to-add-blobs/blobs-device-api-swagger-img.png)](media/how-to-add-blobs/blobs-device-api-swagger-img.png#lightbox)

Chcete-li například aktualizovat nebo vytvořit objekt blob a připojit objekt blob k zařízení, vytvořte ověřený požadavek HTTP PATCH na:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_BLOB_ID* | Požadované ID objektu blob |

Úspěšné požadavky vrátí objekt JSON, jak [je popsáno výše](#blobs-response-data).

### <a name="spaces"></a>Mezery

K mezerám můžete také připojit objekty BLOB. Na následujícím obrázku jsou uvedeny všechny koncové body rozhraní API místa odpovědné za zpracování objektů BLOB. Také uvádí všechny parametry cesty předat do těchto koncových bodů.

[![Prostor ové objekty BLOB](media/how-to-add-blobs/blobs-space-api-swagger-img.png)](media/how-to-add-blobs/blobs-space-api-swagger-img.png#lightbox)

Chcete-li například vrátit objekt blob připojený k prostoru, vytvořte ověřený požadavek HTTP GET na adresu:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_BLOB_ID* | Požadované ID objektu blob |

Úspěšné požadavky vrátí objekt JSON, jak [je popsáno výše](#blobs-response-data).

Požadavek PATCH na stejný koncový bod aktualizuje popisy metadat a vytvoří verze objektu blob. Požadavek HTTP se provádí prostřednictvím metody PATCH, spolu s všechny potřebné meta a vícedílné formuláře data.

### <a name="users"></a>Uživatelé

Objekty BLOB můžete připojit k uživatelským modelům (například pro přidružení profilového obrázku). Následující obrázek znázorňuje příslušné koncové body rozhraní `id`API uživatele a všechny požadované parametry cesty, například :

[![Objekty BLOB uživatele](media/how-to-add-blobs/blobs-users-api-swagger-img.png)](media/how-to-add-blobs/blobs-users-api-swagger-img.png#lightbox)

Chcete-li například načíst objekt blob připojený k uživateli, vytvořte ověřený požadavek HTTP GET s libovolnými požadovanými daty formuláře na adresu:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_BLOB_ID* | Požadované ID objektu blob |

Úspěšné požadavky vrátí objekt JSON, jak [je popsáno výše](#blobs-response-data).

## <a name="common-errors"></a>Běžné chyby

* Běžná chyba zahrnuje neposkytnutí správných informací záhlaví:

  ```JSON
  {
      "error": {
          "code": "400.600.000.000",
          "message": "Invalid media type in first section."
      }
  }
  ```

  Chcete-li tuto chybu vyřešit, ověřte, zda má celkový požadavek příslušnou hlavičku **typu obsahu:**

     * `multipart/mixed`
     * `multipart/form-data`

  Také ověřte, zda každý *vícedílný blok dat* má odpovídající **typ obsahu**.

* Druhá běžná chyba nastává, když je ke stejnému prostředku v [grafu prostorové inteligence](concepts-objectmodel-spatialgraph.md)přiřazeno více objektů BLOB :

  ```JSON
  {
      "error": {
          "code": "400.600.000.000",
          "message": "SpaceBlobMetadata already exists."
      }
  }
  ```

  > [!NOTE]
  > Atribut **zprávy** se bude lišit v závislosti na prostředku. 

  Ke každému prostředku v rámci prostorového grafu může být připojen pouze jeden objekt blob (každého druhu). 

  Chcete-li tuto chybu vyřešit, aktualizujte existující objekt blob pomocí příslušné operace ROZHRANÍ HTTP PATCH. Tím nahradíte existující data objektu blob požadovanými daty.

## <a name="next-steps"></a>Další kroky

- Další informace o referenční dokumentaci Swagger pro Azure Digital Twins najdete v [článek Použití Azure Digital Twins Swagger](how-to-use-swagger.md).

- Chcete-li nahrát objekty BLOB prostřednictvím Pošťáka, přečtěte [si článek Jak nakonfigurovat Pošťáka](./how-to-configure-postman.md).