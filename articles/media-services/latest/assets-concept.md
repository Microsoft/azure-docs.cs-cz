---
title: Prostředky v Azure Media Services | Microsoft Docs
description: Tento článek obsahuje vysvětlení, co jsou prostředky, a jak se používají Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 3dc1866a3c0339bca0c27fb53894a14581e88490
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390507"
---
# <a name="assets"></a>Prostředky

V Azure Media Services [Asset](https://docs.microsoft.com/rest/api/media/assets) obsahuje informace o digitálních souborech uložených v Azure Storage (včetně videa, zvuku, obrázků, kolekcí miniatur, textových stop a souborů titulků). 

Asset se namapuje na kontejner objektů BLOB v [účtu Azure Storage](storage-account-concept.md) a soubory v prostředku se ukládají jako objekty blob bloku v tomto kontejneru. Media Services podporuje úrovně objektů blob, pokud účet používá úložiště pro obecné účely v2 (GPv2). Pomocí GPv2 můžete přesunout soubory do [studeného nebo archivního úložiště](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **Archivní** úložiště je vhodné pro archivaci zdrojových souborů, když už nepotřebujete (například po kódování).

**Archivní** úroveň úložiště je doporučena pouze pro velmi velké zdrojové soubory, které již byly zakódovány a výstup úlohy kódování byl vložen do výstupního kontejneru objektů BLOB. Objekty blob ve výstupním kontejneru, které chcete přidružit k Assetu a používají se ke streamování nebo analýze obsahu, musí existovat v **horké** nebo **studené** úrovni úložiště.

### <a name="naming-blobs"></a>Pojmenování objektů BLOB

Názvy souborů nebo objektů BLOB v rámci assetu musí splňovat požadavky na [název objektu BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) a [požadavky na název systému souborů NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Důvodem těchto požadavků jsou soubory, které lze kopírovat z úložiště objektů blob na místní disk NTFS ke zpracování.

## <a name="upload-digital-files-into-assets"></a>Nahrávání digitálních souborů do assetů

Až se digitální soubory nahrají do úložiště a přidruží se k Assetu, dají se použít v Media Services kódování, streamování a analýze pracovních postupů obsahu. Jedním ze běžných Media Services pracovních postupů je nahrání, kódování a streamování souboru. Tato část popisuje obecné kroky.

> [!TIP]
> Než začnete s vývojem, přečtěte si téma [vývoj s Media Services V3 API](media-services-apis-overview.md) (obsahuje informace o přístupu k rozhraním API, konvencím pojmenování atd.

1. Použijte rozhraní API Media Services verze 3 k vytvoření nového vstupního aktiva. Tato operace vytvoří kontejner v účtu úložiště přidruženém k vašemu účtu Media Services. Rozhraní API vrátí název kontejneru (například `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    Pokud už máte kontejner objektů blob, který chcete přidružit aktivu, můžete při vytváření aktiva zadat název kontejneru. Platforma Media Services momentálně podporuje jenom objekty blob v kořenové složce kontejneru, a ne s cestou v názvu souboru. Proto bude kontejner s názvem souboru „input.mp4“ fungovat. Kontejner s názvem souboru „videos/inputs/input.mp4“ ale fungovat nebude.

    K nahrání přímo do jakékoli účtu úložiště a kontejneru, ke kterým máte oprávnění v rámci vašeho předplatného, můžete použít rozhraní Azure CLI. <br/>Název kontejneru musí být jedinečný a splňovat pokyny pro pojmenování úložiště. Název nemusí splňovat formátování názvů kontejnerů aktiv Media Services (Asset-GUID). 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Získejte adresu URL SAS s oprávněními pro čtení i zápis, která se použije k nahrání digitálních souborů do kontejneru aktiva. K [vypsání adres URL kontejnerů aktiv](https://docs.microsoft.com/rest/api/media/assets/listcontainersas) můžete použít rozhraní API Media Services.
3. Použijte rozhraní API Azure Storage nebo sady SDK (například [úložiště REST API](../../storage/common/storage-rest-api-auth.md) nebo [sadu .NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) k nahrání souborů do kontejneru assetů. 
4. Pomocí rozhraní API služby Media Services verze 3 můžete vytvořit transformaci a úlohu ke zpracování vstupního aktiva. Další informace najdete v tématu [Transformace a úlohy](transform-concept.md).
5. Streamování obsahu z prostředku "Output".

Úplný příklad rozhraní .NET, který ukazuje, jak vytvořit Asset, získat adresu URL SAS s možností zápisu do kontejneru assetů v úložišti, nahrát soubor do kontejneru v úložišti pomocí adresy URL SAS najdete v tématu [Vytvoření vstupu úlohy z místního souboru](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Vytvořit nový prostředek

> [!NOTE]
> Vlastnosti prostředku typu DateTime jsou vždycky ve formátu UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Příklad REST najdete v tématu [Vytvoření assetu pomocí REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) .

Tento příklad ukazuje, jak vytvořit **tělo požadavku**, kde můžete zadat užitečné informace, například popis, název kontejneru, účet úložiště a další informace.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

Úplný příklad najdete v tématu [Vytvoření vstupu úlohy z místního souboru](job-input-from-local-file-how-to.md). V Media Services V3 lze také vytvořit vstup úlohy z adres URL protokolu HTTPS (viz [Vytvoření vstupu úlohy z adresy URL protokolu HTTPS](job-input-from-http-how-to.md)).

## <a name="map-v3-asset-properties-to-v2"></a>Mapování vlastností assetu V3 na v2

Následující tabulka ukazuje, jak vlastnosti [assetu](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)v v3 jsou mapovány na vlastnosti assetu ve verzi v2.

|vlastnosti V3|vlastnosti v2|
|---|---|
|ID – (jedinečné) Úplná cesta Azure Resource Manager, viz příklady v [assetu](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|název – (jedinečný) viz [zásady vytváření názvů](media-services-apis-overview.md#naming-conventions) ||
|alternateId|AlternateId|
|assetId|Hodnota ID – (jedinečná) začíná `nb:cid:UUID:` předponou.|
|vytvořené|Vytvořeno|
|description|Name|
|lastModified|lastModified|
|storageAccountName|název_účtu_úložiště|
|storageEncryptionFormat| Možnosti (možnosti vytváření)|
|type||

## <a name="storage-side-encryption"></a>Šifrování na straně úložiště

K ochraně vašich prostředků v klidovém stavu, prostředky by se měla šifrovat pomocí šifrování na straně úložiště. Následující tabulka ukazuje, jak funguje šifrování na straně úložiště ve službě Media Services:

|Možnost šifrování|Popis|Media Services v2|Media Services v3|
|---|---|---|---|
|Šifrování úložiště služby Media Services|Šifrování AES-256, klíče spravované službou Media Services|Podporované<sup>(1)</sup>|Nepodporuje<sup>(2)</sup>|
|[Šifrování služby Storage pro neaktivní uložená Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Šifrování na straně serveru nabízených službou Azure Storage, klíče spravované službou Azure nebo podle zákazníka|Podporováno|Podporováno|
|[Šifrování na straně klienta úložiště](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Šifrování na straně klienta nabízené službou Azure storage, klíče spravované zákazníkem ve službě Key Vault|Nepodporuje se|Nepodporuje se|

<sup>1</sup> při Media Services podporuje zpracování obsahu v nezašifrované podobě nebo bez jakéhokoli typu šifrování, učiníte tak se nedoporučuje.

<sup>2</sup> v Media Services v3 šifrování úložiště (šifrování AES-256) je pouze podporována pro zpětné kompatibility při vaše prostředky se vytvořily pomocí Media Services v2. To znamená v3 spolupracuje s existující úložiště šifrované prostředky, ale nebude umožňovat vytváření nových.

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Viz téma [filtrování, řazení, stránkování Media Services entit](entities-overview.md).

## <a name="next-steps"></a>Další kroky

* [Streamování souboru](stream-files-dotnet-quickstart.md)
* [Použití cloudového videorekordéru](live-event-cloud-dvr.md)
* [Rozdíly mezi Media Services V2 a V3](migrate-from-v2-to-v3.md)
