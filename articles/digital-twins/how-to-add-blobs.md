---
title: Přidání objektů blob pro objekty v digitální dvojče Azure | Dokumentace Microsoftu
description: Pochopení způsobu, jak přidat objekty BLOB na objekty v digitální dvojče Azure
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adgera
ms.openlocfilehash: 0929a4a63eee35d21723c980887d6b4217898682
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688127"
---
# <a name="how-to-add-blobs-to-objects-in-azure-digital-twins"></a>Přidání objektů blob pro objekty v digitální dvojče Azure

Objekty BLOB jsou nestrukturovaných reprezentace běžných typů souborů (jako jsou obrázky a protokoly). Objekty BLOB udržovat přehled o jaký druh dat, které představují pomocí typu MIME (například: "image/jpeg") a metadata (název, popis, typ atd.).

Azure digitální dvojče podporuje objekty BLOB se připojuje k zařízení, mezery a uživatelů. Objekty BLOB mohou představovat profilový obrázek pro uživatele, zařízení fotografie, video, mapy nebo protokolu.

> [!NOTE]
> Tento článek předpokládá:
> * Zda je vaše instance správně nakonfigurován pro příjem požadavků rozhraní API pro správu.
> * Že jste správně ověřeny pomocí klienta REST podle vašeho výběru.

## <a name="uploading-blobs-an-overview"></a>Nahrání objektů blob: Přehled

S více částmi. požadavky se používají k nahrání objektů blob pro konkrétní koncové body a jejich příslušné funkce.

> [!IMPORTANT]
> Požadavky na vícedílné zprávy standardu vyžadují tři základní údaje. Pro Azure digitální dvojče:
> * A **Content-Type** hlavičky:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
> * A **Content-Disposition**: `form-data; name="metadata"`.
> * Obsah souboru k odeslání.
>
> Přesné **Content-Type** a **Content-Disposition** může lišit v závislosti na scénáři.

Každý s více částmi. požadavek je rozdělen do několika bloků. S více částmi. požadavky na rozhraní API Správce Dvojčat digitální Azure jsou rozdělené **dvě** (**2**) tyto části:

1. První část je povinný a obsahuje metadata objektu Blob, jako je například přidružený typ MIME za **Content-Type** a **Content-Disposition** výše.

1. Druhá část obsahuje skutečný obsah objektu Blob (nestrukturovaný obsah souboru).  

Ani jeden ze dvou částí jsou požadovány pro **oprava** požadavky. Jsou potřeba pro **příspěvek** nebo operace vytvoření.

### <a name="blob-metadata"></a>Metadata objektu blob

Kromě **Content-Type** a **Content-Disposition**, vícedílné zprávy standardu požadavků musíte zadat také správný text JSON. Subjekt JSON pro odeslání závisí na typu prováděné operace požadavku HTTP.

Čtyři hlavní schémat JSON použít jsou:

![Objekty BLOB místa][1]

Tato schémata modelu jsou podrobně popsány v úplné poskytnutá dokumentace Swagger.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Další informace o použití zadaný referenční dokumentaci najdete [použití Swagger](./how-to-use-swagger.md).

### <a name="examples"></a>Příklady

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Chcete-li požadavek POST, která nahrává do textového souboru jako objekt Blob a přidruží ji k mezery:

```plaintext
POST YOUR_MANAGEMENT_API_URL/spaces/blobs HTTP/1.1
Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"

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

| Hodnota parametru | Nahradit hodnotou |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | Název obsahu hranic s více částmi. |

.NET provádění stejné odeslání objektu Blob jsou uvedeny níže horizontálních oddílů pomocí třídy [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metaData in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

## <a name="api-endpoints"></a>Koncové body rozhraní API

Níže je k dispozici návod core koncové body a jejich konkrétní funkce.

### <a name="devices"></a>Zařízení

Objekty BLOB lze připojit k zařízení. Na obrázku níže (znázorňující referenční dokumentaci Swagger pro rozhraní API pro správu) určuje koncové body související s zařízení rozhraní API pro využití Blob a cesta k požadované parametry k předání do nich:

![Objekty BLOB zařízení][2]

Například pokud chcete aktualizovat nebo vytvoření objektu Blob a připojit objekt Blob do zařízení, opravy požadavku:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_BLOB_ID* | Požadované ID objektů Blob |

Úspěšné požadavky se vrátí objekt DeviceBlob JSON v odpovědi. DeviceBlobs odpovídají následujícím schématu JSON:

| Atribut | Typ | Popis | Příklady |
| --- | --- | --- | --- |
| **DeviceBlobType** | Řetězec | Kategorie objektu Blob, který lze připojit k zařízení | `Model` a `Specification` |
| **DeviceBlobSubtype*** | Řetězec | Podkategorii objektů Blob, který je podrobnější než DeviceBlobType | `PhysicalModel`, `LogicalModel`, `KitSpecification`, a `FunctionalSpecification` |

> [!TIP]
> Výše uvedené tabulce použijte ke zpracování dat úspěšně vrácený požadavek.

### <a name="spaces"></a>Mezery

Objekty BLOB lze také připojit na mezery. Na obrázku níže jsou uvedeny všechny místo koncových bodů rozhraní API zodpovědná za zpracování objektů BLOB spolu s parametry všechny cesty k předání do nich:

![Objekty BLOB místa][3]

Například vrátit objekt Blob připojené k mezeru, je požadavek GET na:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_BLOB_ID* | Požadované ID objektů Blob |

Provedení požadavku PATCH stejný koncový bod vám umožní aktualizovat popis metadat a vytvořit novou verzi objektu Blob. Požadavek HTTP je proveden pomocí metody PATCH spolu s všechny nezbytné meta a vícedílné zprávy standardu data formuláře.

Úspěšné operace vrátí SpaceBlob, který odpovídá následujícím schématu a je možné využívat vrácená data:

| Atribut | Typ | Popis | Příklady |
| --- | --- | --- | --- |
| **SpaceBlobType** | Řetězec | Kategorie objektu Blob, který lze připojit k mezeru | `Map` a `Image` |
| **SpaceBlobSubtype** | Řetězec | Podkategorii objektů Blob, který je podrobnější než SpaceBlobType | `GenericMap`, `ElectricalMap`, `SatelliteMap`, a `WayfindingMap` |

### <a name="users"></a>Uživatelé

Objekty BLOB je možné připojit k modely uživatelů (říct přidružit profilový obrázek). Následující obrázek znázorňuje relevantní koncových bodů rozhraní API uživatelé a všechny požadované cesty parametry, jako jsou `id`:

![Uživatelské objekty BLOB][4]

Například k načtení objektu Blob připojit na uživatele, provede požadavek GET s všechny požadované-data formuláře:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_BLOB_ID* | Požadované ID objektů Blob |

Vrácený JSON (UserBlobs) bude splňovat následující modely JSON:

| Atribut | Typ | Popis | Příklady |
| --- | --- | --- | --- |
| **UserBlobType** | Řetězec | Kategorie objektů Blob, který lze připojit k uživateli | `Image` a `Video` |
| **UserBlobSubtype** |  Řetězec | Podkategorii objektů Blob, který je podrobnější než UserBlobType | `ProfessionalImage`, `VacationImage`, a `CommercialVideo` |

## <a name="common-errors"></a>Běžné chyby

Nezahrnuje informace správné hlavičky:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>Další postup

Další informace o zadané Swagger Azure digitální dvojče referenční dokumentaci, [použití digitální dvojče Swagger](how-to-use-swagger.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
