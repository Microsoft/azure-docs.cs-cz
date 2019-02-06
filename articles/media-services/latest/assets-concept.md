---
title: Prostředky ve službě Media Services – Azure | Dokumentace Microsoftu
description: Tento článek obsahuje vysvětlení, co jsou prostředky, a jak se používají Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 72229a723247d6f0d68341771b073d0626ab2edb
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745992"
---
# <a name="assets"></a>Prostředky

Ve službě Azure Media Services [Asset](https://docs.microsoft.com/rest/api/media/assets) obsahuje digitální soubory (včetně video, zvuk, obrázky, kolekci miniatur, textové stopy a soubory s titulky) a metadata o těchto souborech. Jakmile jsou digitální soubory nahrát do Assetu, můžete použít ve službě Media Services encoding, streamování, analýze obsahu pracovních postupů. Další informace najdete v tématu [digitální soubory nahrát do prostředků](#upload-digital-files-into-assets) níže v části.

Prostředek je namapována na kontejner objektů blob ve službě [účtu služby Azure Storage](storage-account-concept.md) a soubory v prostředku se ukládají jako objekty BLOB bloku v tomto kontejneru. Služba Media Services podporuje úrovně objektu Blob, pokud používá účet pro obecné účely v2 (GPv2) úložiště. S účty GPv2, můžete přesunout soubory do [studené nebo archivní úložiště](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **Archiv** úložiště je vhodný pro archivaci zdrojové soubory, pokud už nepotřebujete (například po jejich jsou kódovány).

**Archivu** vrstva úložiště se doporučuje jen pro velmi velké zdrojové soubory, které již byly zakódovány a kódování výstupu úlohy byl umístěn do kontejneru objektů blob výstup. Objekty BLOB ve výstupním kontejneru, který chcete přidružit k Asset a použít ke streamování a analyzovat obsah, musí existovat v **Hot** nebo **Cool** vrstvy úložiště.

> [!NOTE]
> Vlastnosti prostředku typu datum a čas jsou vždy ve formátu UTC.

## <a name="upload-digital-files-into-assets"></a>Digitální soubory nahrát do prostředků

Jedním z běžných pracovních Media Services je nahrávání, kódování a streamování do souboru. Tato část popisuje obecné kroky.

1. Použití Media Services v3 rozhraní API k vytvoření nového prostředku "vstupní". Tato operace vytvoří kontejner v účtu úložiště přidruženého k účtu Media Services. Rozhraní API vrátí název kontejneru (například `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    Pokud už máte kontejner objektů blob, který chcete přidružit k prostředku, můžete zadat název kontejneru, při vytváření prostředku. Media Services momentálně podporuje jenom objekty BLOB v kořenové složce kontejneru, nikoli zpětným cesty v názvu souboru. Proto bude fungovat kontejner s názvem souboru "input.mp4". Kontejner s názvem souboru "videos/inputs/input.mp4", ale nebudou fungovat.

    Rozhraní příkazového řádku Azure můžete použít k nahrání přímo do jakékoli účtu úložiště a kontejner, který máte oprávnění v rámci vašeho předplatného. <br/>Název kontejneru musí být jedinečný a postupujte podle úložiště pokyny pro pojmenování. Název nemá dodržovat formátování název kontejneru služby Asset média (identifikátor GUID prostředku). 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Získáte adresu URL SAS s oprávněními pro čtení i zápis, které se použijí k digitální soubory nahrát do Assetu kontejneru. Můžete použít rozhraní API služby Media Services k [seznam adres URL kontejneru asset](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Pomocí rozhraní API služby Azure Storage nebo sady SDK (například [REST API pro Storage](../../storage/common/storage-rest-api-auth.md), [sady JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), nebo [sady .NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) k nahrání souborů do kontejneru Asset. 
4. Pomocí rozhraní API služby Media Services v3 můžete vytvářet transformace a úlohy ke zpracování "vstupní" Asset. Další informace najdete v tématu [transformuje a úlohy](transform-concept.md).
5. Stream obsahu z "výstupní" asset.

> [!TIP]
> Úplný příklad .NET, který ukazuje, jak: vytvořit Asset, získání zapisovatelné adresy URL SAS prostředku kontejneru v úložišti, nahrajte soubor do kontejneru v úložišti pomocí adresy URL SAS, naleznete v tématu [vytvořit vstup úlohy z místního souboru](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Vytvoření nového prostředku

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Například REST, najdete v článku [vytvořit prostředek s využitím REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) příklad.

Tento příklad ukazuje, jak vytvořit **tělo požadavku** zadávají užitečné informace, například popis, název kontejneru, účet úložiště a další informace.

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

Úplný příklad naleznete v tématu [vytvořit vstup úlohy z místního souboru](job-input-from-local-file-how-to.md). V Media Services v3, mohou být také vytvořeny vstupu úlohy z adres URL protokolu HTTPS (viz [vytvořit vstup úlohy z adresy URL HTTPS](job-input-from-http-how-to.md)).

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Zobrazit [filtrování, řazení, stránkování, Media Services entit](entities-overview.md).

## <a name="storage-side-encryption"></a>Šifrování na straně úložiště

K ochraně vašich prostředků v klidovém stavu, prostředky by se měla šifrovat pomocí šifrování na straně úložiště. Následující tabulka ukazuje, jak funguje šifrování na straně úložiště ve službě Media Services:

|Možnost šifrování|Popis|Media Services v2|Media Services v3|
|---|---|---|---|
|Šifrování úložiště služby Media Services|Šifrování AES-256, klíče spravované službou Media Services|Podporované<sup>(1)</sup>|Nepodporuje<sup>(2)</sup>|
|[Šifrování služby Storage pro neaktivní uložená Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Šifrování na straně serveru nabízených službou Azure Storage, klíče spravované službou Azure nebo podle zákazníka|Podporováno|Podporováno|
|[Šifrování na straně klienta úložiště](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Šifrování na straně klienta nabízené službou Azure storage, klíče spravované zákazníkem ve službě Key Vault|Nepodporuje se|Nepodporuje se|

<sup>1</sup> při Media Services podporuje zpracování obsahu v nezašifrované podobě nebo bez jakéhokoli typu šifrování, učiníte tak se nedoporučuje.

<sup>2</sup> v Media Services v3 šifrování úložiště (šifrování AES-256) je pouze podporována pro zpětné kompatibility při vaše prostředky se vytvořily pomocí Media Services v2. To znamená v3 spolupracuje s existující úložiště šifrované prostředky, ale nebude umožňovat vytváření nových.

## <a name="next-steps"></a>Další postup

* [Streamování souboru](stream-files-dotnet-quickstart.md)
* [Použití cloudového DVR](live-event-cloud-dvr.md)
* [Rozdíly mezi Media Services v2 a v3](migrate-from-v2-to-v3.md)
