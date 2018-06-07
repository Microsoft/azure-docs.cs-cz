---
title: Migrace ze služby Azure Media Services v2 v3 | Microsoft Docs
description: Tento článek popisuje změny, které byly zavedeny v Azure Media Services v3 a jsou uvedeny rozdíly mezi dvěma verzemi.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: azure media services, stream, všesměrové vysílání, live, režim offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 05/22/2018
ms.author: juliako
ms.openlocfilehash: 4e644db12a74d6ef132a0c8d64ef517a0c2253cc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654860"
---
# <a name="migrate-from-media-services-v2-to-v3"></a>Migrace ze služby Media Services v2 v3

> [!NOTE]
> Nejnovější verze služby Azure Media Services je ve verzi Preview a může být označována jako v3.

Tento článek popisuje změny, které byly zavedeny v Azure Media Services (AMS) v3 a jsou uvedeny rozdíly mezi dvěma verzemi.

## <a name="why-should-you-move-to-v3"></a>Proč by měl přesunutí v3?

### <a name="api-is-more-approachable"></a>Rozhraní API je více přístupný

*  V3 je založena na jednotné rozhraní API prostor, který zveřejňuje operace a Správa funkce, které jsou integrované v Azure Resource Manager. Šablony Azure Resource Manager umožňuje vytvářet a nasazovat transformací, koncové body streamování, LiveEvents a další.
* Otevřete rozhraní API (neboli Swagger) dokumentu specifikací.
* Sady SDK k dispozici pro platformu .net, rozhraní .net Core, Node.js, Python, Javy, Ruby.
* Integrace rozhraní příkazového řádku Azure.

### <a name="new-features"></a>Nové funkce

* Kódování teď podporuje protokol HTTPS ingestování (vstupu na základě adresy Url).
* Transformace jsou novinkou v v3. Transformace se používá k sdílet konfigurace, vytváření šablon Azure Resource Manageru a izolovat kódování nastavení pro konkrétního zákazníka nebo klienta. 
* Prostředek může mít několik StreamingLocators s jiným nastavením dynamické balením a dynamickým šifrováním.
* Ochrana obsahu podporuje více klíčové funkce. 
* LiveEvent Preview podporuje dynamické balení a dynamické šifrování. To umožňuje ochranu obsahu pro balení Preview a také DASH a HLS.
* LiveOuput je jednodušší použít než starší Program entity. 
* Přidala se podpora RBAC na entity.

## <a name="changes-from-v2"></a>Změny z v2

* Media Services SDK odpojené od sady SDK úložiště, což dává větší kontrolu nad sady SDK úložiště používá a zabraňuje Správa verzí – potíže. 
* V v3 kódování přenosové rychlosti jsou všechny bity za sekundu. To se liší od v2 REST, který nastaví aplikace Media Encoder Standard. Například přenosovou rychlostí v v2 by byl zadán jako 128, ale v v3 by bylo 128000. 
* IngestManifests AssetFiles, AccessPolicies, nejsou k dispozici v v3.
* ContentKeys již nejsou entity, vlastnost StreamingLocator.
* Podpora mřížky událostí nahradí NotificationEndpoints.
* Byly přejmenovány některé entity

  * JobOutput nahradí úloh, teď právě část úlohy.
  * LiveEvent nahradí kanálu.
  * LiveOutput nahrazuje Program.
  * StreamingLocator nahradí lokátoru.

## <a name="code-changes"></a>Změny kódu

### <a name="create-an-asset-and-upload-a-file"></a>Vytvořte asset a nahrajte soubor 

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
4. Vytvoření prostředku a nahrát obsah nebo odeslat úlohu a použít výstupní asset
5. Přidružit Asset AssetDeliveryPolicy
6. Vytvoření ContentKey
7. Připojte ContentKey pro prostředek
8. Vytvoření AccessPolicy
9. Vytvořit lokátor

#### <a name="v3"></a>v3

1. Vytvoření zásad klíče k obsahu
2. Vytvoření prostředku
3. Nahrát obsah nebo jako JobOutput použít Asset
4. Vytvořit lokátor

## <a name="next-steps"></a>Další postup

Projděte si článek o [streamování souborů](stream-files-dotnet-quickstart.md), kde se dozvíte, jak je snadné začít s kódováním a streamováním videosouborů. 

