---
title: Přidání objektů blob pro objekty v digitální dvojče Azure | Dokumentace Microsoftu
description: Zjistěte, jak přidat objekty BLOB na objekty v digitální dvojče Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/28/2018
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 604093dcec048b0991bbc9beac3ef998cc47e351
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974505"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Přidat objekty BLOB na objekty v digitální dvojče Azure

Objekty BLOB jsou nestrukturovaných reprezentace běžných typů souborů, jako jsou obrázky a protokoly. Objekty BLOB, udržovat přehled o jaký druh dat, které představují pomocí typu MIME (například: "image/jpeg") a metadata (název, popis, typ a tak dále).

Azure podporuje digitální dvojče objekty BLOB se připojuje k zařízení, mezery a uživatelů. Objekty BLOB mohou představovat profilový obrázek pro uživatele, zařízení fotografie, video, mapy nebo protokolu.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-an-overview"></a>Nahrání objektů blob: Přehled

S více částmi. požadavky můžete použít k nahrání objektů blob pro konkrétní koncové body a jejich příslušné funkce.

> [!IMPORTANT]
> Požadavky na vícedílné zprávy standardu vyžadují tři údaje:
> * A **Content-Type** hlavičky:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * A **Content-Disposition**: `form-data; name="metadata"`
> * Obsah souboru k odeslání
>
> **Content-Type** a **Content-Disposition** informace se můžou lišit v závislosti na scénáři použití.

S více částmi. požadavky na rozhraní API Správce Dvojčat digitální Azure mají dvě části:

* Objekt BLOB metadat – například přidružený typ MIME, jak je znázorněno **Content-Type** a **Content-Disposition** informace

* Obsah objektu BLOB (nestrukturovaný obsah souboru)  

Ani jeden ze dvou částí, je třeba **oprava** požadavky. Jsou potřeba pro **příspěvek** nebo operace vytvoření.

### <a name="blob-metadata"></a>Metadata objektu blob

Kromě **Content-Type** a **Content-Disposition**, vícedílné zprávy standardu požadavků musíte zadat správný text JSON. Které text JSON pro odeslání závisí na druh operace požadavku HTTP, který provádí.

Čtyři hlavní schémat JSON jsou:

![Schémat JSON][1]

Swagger dokumentace popisuje tato schémata modelu ve všech podrobností.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Další informace o použití najdete v referenční dokumentaci [použití Swagger](./how-to-use-swagger.md).

### <a name="examples"></a>Příklady

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Chcete-li **příspěvek** požadavek, který nahraje do textového souboru jako objekt blob a přidruží ji k mezeru:

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

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

## <a name="api-endpoints"></a>Koncové body rozhraní API

Následující části popisují koncových bodů core týkajících se objektu blob rozhraní API a jejich funkce.

### <a name="devices"></a>Zařízení

Objekty BLOB můžete připojit k zařízení. Následující obrázek znázorňuje referenční dokumentaci Swagger pro rozhraní API pro správu. Určuje zařízení koncových bodů rozhraní API pro využití blob a cesta k požadované parametry k předání do nich.

![Objekty BLOB zařízení][2]

Například pokud chcete aktualizovat nebo vytvoření objektu blob a připojit objekt blob do zařízení, je **oprava** požadavek na:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_BLOB_ID* | ID požadované objektů blob |

Úspěšné požadavky vracet **DeviceBlob** objektu JSON v odpovědi. **DeviceBlob** objekty v souladu s následující schéma JSON:

| Atribut | Typ | Popis | Příklady |
| --- | --- | --- | --- |
| **DeviceBlobType** | Řetězec | Kategorie objektu blob, který lze připojit k zařízení | `Model` a `Specification` |
| **DeviceBlobSubtype** | Řetězec | Podkategorii objektů blob, který je specifičtější než **DeviceBlobType** | `PhysicalModel`, `LogicalModel`, `KitSpecification`, a `FunctionalSpecification` |

> [!TIP]
> V předchozí tabulce použijte ke zpracování dat úspěšně vrácený požadavek.

### <a name="spaces"></a>Spaces

Objekty BLOB můžete také připojit na mezery. Na následujícím obrázku jsou uvedeny všechny místo koncových bodů rozhraní API zodpovědná za zpracování objektů BLOB. Obsahuje také seznam všechny parametry cesty k předání do těchto koncových bodů.

![Objekty BLOB místa][3]

Například vrátit objekt blob připojené k mezeru, je **získat** požadavek na:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_BLOB_ID* | ID požadované objektů blob |

Vytváření **oprava** požadavek na stejný koncový bod můžete aktualizovat popis metadat a vytvořit novou verzi objektu blob. Požadavek HTTP je proveden prostřednictvím **oprava** metoda spolu s všechny nezbytné metadata a data vícedílného formuláře.

Úspěšné operace vrátit **SpaceBlob** objekt, který odpovídá následujícím schématu. Můžete ho využívat vrácená data.

| Atribut | Typ | Popis | Příklady |
| --- | --- | --- | --- |
| **SpaceBlobType** | Řetězec | Kategorie objektu blob, který lze připojit k mezeru | `Map` a `Image` |
| **SpaceBlobSubtype** | Řetězec | Podkategorii objektů blob, který je specifičtější než **SpaceBlobType** | `GenericMap`, `ElectricalMap`, `SatelliteMap`, a `WayfindingMap` |

### <a name="users"></a>Uživatelé

Objekty BLOB můžete připojit k modelům uživatele (například přidružit profilový obrázek). Následující obrázek ukazuje koncové body příslušné uživatelské rozhraní API a cesta k požadované parametry, jako je `id`:

![Uživatelské objekty BLOB][4]

Například k načtení objektu blob připojit na uživatele, ujistěte se, **získat** požadavek s daty z jakékoli požadované formuláře:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_BLOB_ID* | ID požadované objektů blob |

Vrácený JSON (**UserBlob** objekty) odpovídají následující modely JSON:

| Atribut | Typ | Popis | Příklady |
| --- | --- | --- | --- |
| **UserBlobType** | Řetězec | Kategorie objektů blob, který lze připojit k uživateli | `Image` a `Video` |
| **UserBlobSubtype** |  Řetězec | Podkategorii objektů blob, který je specifičtější než **UserBlobType** | `ProfessionalImage`, `VacationImage`, a `CommercialVideo` |

## <a name="common-errors"></a>Běžné chyby

Běžnou chybou je tak, aby nezahrnovala informace správné hlavičky:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>Další postup

Další informace o Swagger referenční dokumentaci pro Azure digitální dvojče, [použití Azure digitální dvojče Swagger](how-to-use-swagger.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
