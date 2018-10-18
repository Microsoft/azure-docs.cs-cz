---
title: Migrace z Azure Media Services v2 na v3 | Dokumentace Microsoftu
description: Tento článek popisuje změny, které byly zavedeny v Azure Media Services v3 a jsou uvedeny rozdíly mezi dvěma verzemi.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, všesměrové vysílání, live, režim offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: a17bad5beb651aaa085c626296c200a00c30647e
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376358"
---
# <a name="migrate-from-media-services-v2-to-v3"></a>Migrace ze služby Media Services v2 na v3

Tento článek popisuje změny, které byly zavedeny v Azure Media Services (AMS) v3 a jsou uvedeny rozdíly mezi dvěma verzemi.

## <a name="why-should-you-move-to-v3"></a>Proč byste je přesunout na v3?

### <a name="api-is-more-approachable"></a>Rozhraní API je více přístupné

*  V3 je založená na sjednocené rovinu rozhraní API, která poskytuje správu i provoz funkcí, které jsou integrované v Azure Resource Manageru. Šablony Azure Resource Manageru je možné vytvořit a nasadit transformací, koncové body streamování, LiveEvents a další.
* Otevřete rozhraní API (označuje se také jako Swagger) specifikace dokumentu.
* Sady SDK jsou dostupné pro .net,.Net Core, Node.js, Python, Java, Ruby.
* Integrace s Azure CLI.

### <a name="new-features"></a>Nové funkce

* Kódování nyní podporuje HTTPS ingestování (vstupu na základě adresy Url).
* Transformace jsou nové ve verzi 3. Transformace umožňuje sdílet konfigurace, vytváření šablon Azure Resource Manageru a izolovat nastavení kódování pro konkrétního zákazníka nebo klienta. 
* Prostředek může mít více StreamingLocators s různými nastaveními dynamického balení a dynamického šifrování.
* Content protection podporuje více klíčových funkcí. 
* Livestream ve verzi Preview podporuje dynamické balení a dynamického šifrování. To umožňuje ochranu obsahu pro balení ve verzi Preview a DASH nebo HLS.
* LiveOuput je snazší než starší Program entity. 
* Přidala se podpora RBAC u entit.

## <a name="changes-from-v2"></a>Změny z v2

* V Media Services v3 šifrování úložiště (šifrování AES-256) je jenom podporována pro zpětné kompatibility při vaše prostředky se vytvořily pomocí Media Services v2. To znamená v3 spolupracuje s existující úložiště šifrované prostředky, ale nebude umožňovat vytváření nových.

    Pro prostředky vytvořené pomocí v3, služba Media Services podporuje [služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) šifrování úložiště na straně serveru.
    
* Media Services SDK oddělený od SDK úložiště, který dává větší kontrolu nad sady SDK služby Storage použít a správa verzí – potíže se vyhnete. 
* Ve verzi 3 kódování přenosové rychlosti jsou všechny bity za sekundu. To se liší od v2 REST, které přednastavení kodéru Media Encoder Standard. Například s přenosovou rychlostí v v2 by byl zadán jako 128, ale ve verzi 3 by 128000. 
* IngestManifests AssetFiles AccessPolicies, neexistuje ve verzi 3.
* Klíčů ContentKeys už nejsou entity, vlastnost StreamingLocator.
* Event Grid podporu nahradí NotificationEndpoints.
* Některé entity byly přejmenovány.

  * JobOutput nahradí úlohy, teď právě součástí projektu.
  * Livestream nahradí kanálu.
  * LiveOutput nahradí programu.
  * StreamingLocator nahradí Lokátor.

## <a name="code-changes"></a>Změny kódu

### <a name="create-an-asset-and-upload-a-file"></a>Vytvoření assetu a nahrání souboru 

#### <a name="v2"></a>v2

```csharp
IAsset asset = context.Assets.Create(assetName, storageAccountName, options);

IAssetFile assetFile = asset.AssetFiles.Create(assetFileName);

assetFile.Upload(filePath);
```

#### <a name="v3"></a>v3

```csharp
Asset asset = client.Assets.CreateOrUpdate(resourceGroupName, accountName, assetName, new Asset());

var response = client.Assets.ListContainerSas(resourceGroupName, accountName, assetName, permissions: AssetContainerPermission.ReadWrite, expiryTime: DateTime.Now.AddHours(1));

var sasUri = new Uri(response.AssetContainerSasUrls.First());
CloudBlobContainer container = new CloudBlobContainer(sasUri);

var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));
blob.UploadFromFile(fileToUpload);
```

### <a name="submit-a-job"></a>Odeslání úlohy

#### <a name="v2"></a>v2

```csharp
IMediaProcessor processor = context.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

IJob job = jobs.Create($"Job for {inputAsset.Name}");

ITask task = job.Tasks.AddNew($"Task for {inputAsset.Name}", processor, taskConfiguration);

task.InputAssets.Add(inputAsset);

task.OutputAssets.AddNew(outputAssetName, outputAssetStorageAccountName, outputAssetOptions);

job.Submit();
```

#### <a name="v3"></a>v3

```csharp
client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, new Asset());

JobOutput[] jobOutputs = { new JobOutputAsset(outputAssetName)};

JobInput jobInput = JobInputAsset(assetName: assetName);

Job job = client.Jobs.Create(resourceGroupName,
accountName, transformName, jobName,
new Job {Input = jobInput, Outputs = jobOutputs});
```

### <a name="publish-an-asset-with-aes-encryption"></a>Publikování assetu pomocí šifrování AES 

#### <a name="v2"></a>v2

1. Vytvoření ContentKeyAuthorizationPolicyOption
2. Vytvoření ContentKeyAuthorizationPolicy
3. Vytvoření AssetDeliveryPolicy
4. Vytvoření prostředku a nahrát obsah nebo odeslat úlohu a použití prostředku výstupu
5. Přiřazení k AssetDeliveryPolicy Assetu
6. Vytvoření ContentKey
7. Připojit ContentKey k Assetu
8. Vytvoření AccessPolicy
9. Vytvoření lokátoru

#### <a name="v3"></a>v3

1. Vytvoření obsahu klíče zásad
2. Vytvoření prostředku
3. Nahrát obsah nebo Asset používat jako JobOutput
4. Vytvoření StreamingLocator

## <a name="next-steps"></a>Další postup

Projděte si článek o [streamování souborů](stream-files-dotnet-quickstart.md), kde se dozvíte, jak je snadné začít s kódováním a streamováním videosouborů. 

