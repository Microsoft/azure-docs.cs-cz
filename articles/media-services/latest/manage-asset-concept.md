---
title: Správa datových zdrojů ve službě Azure Media Services
titleSuffix: Azure Media Services
description: Prostředek, ve kterém zadáváte média (například prostřednictvím nahrávání nebo živého ingestování), výstupní médium (z výstupu úlohy) a publikujete média z (pro streamování). Toto téma poskytuje přehled o tom, jak vytvořit nový datový zdroj a nahrát soubory.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9136fd702fad5c12a8ec97a68ff8a592a203d7d2
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582203"
---
# <a name="manage-assets"></a>Správa prostředků

Ve službě Azure Media Services je [datový majek](https://docs.microsoft.com/rest/api/media/assets) 

* nahrávat mediální soubory do datového zdroje,
* požívání a archivace živých přenosů do datového zdroje,
* výsledky kódování analytické úlohy k aktivu,
* publikovat média pro streamování, 
* stahovat soubory z datového zdroje.

Toto téma poskytuje přehled o tom, jak nahrát soubory do datového zdroje a provést některé další běžné operace. Poskytuje také odkazy na ukázky kódu a související témata.

## <a name="prerequisite"></a>Požadavek 

Než začnete vyvíjet, zkontrolujte:

* [Koncepty](concepts-overview.md)
* [Vývoj pomocí datových a 3 řešení api služby Media Services](media-services-apis-overview.md) (zahrnuje informace o přístupu k souborům API, konvencích pojmenování a tak dále) 

## <a name="upload-media-files-into-an-asset"></a>Nahrání mediálních souborů do datového zdroje

Po nahrání digitálních souborů do úložiště a přidružených k datovému zdroji je lze použít v kódování, streamování a analýze pracovních postupů obsahu služby Media Services. Jedním z běžných pracovních postupů mediálních služeb je nahrávání, kódování a streamování souboru. Tato část popisuje obecné kroky.

1. Použijte rozhraní API Media Services verze 3 k vytvoření nového vstupního aktiva. Tato operace vytvoří kontejner v účtu úložiště přidruženém k vašemu účtu Media Services. Rozhraní API vrátí název kontejneru `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`(například ).

    Pokud již máte kontejner objektů blob, který chcete přidružit k datovému zdroji, můžete při vytváření datového zdroje zadat název kontejneru. Platforma Media Services momentálně podporuje jenom objekty blob v kořenové složce kontejneru, a ne s cestou v názvu souboru. Proto bude kontejner s názvem souboru „input.mp4“ fungovat. Kontejner s názvem souboru "videa/vstupy/input.mp4" však nebude fungovat.

    K nahrání přímo do jakékoli účtu úložiště a kontejneru, ke kterým máte oprávnění v rámci vašeho předplatného, můžete použít rozhraní Azure CLI.

    Název kontejneru musí být jedinečný a splňovat pokyny pro pojmenování úložiště. Název nemusí splňovat formátování názvů kontejnerů aktiv Media Services (Asset-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Získejte adresu URL SAS s oprávněními pro čtení i zápis, která se použije k nahrání digitálních souborů do kontejneru aktiva.

    K [vypsání adres URL kontejnerů aktiv](https://docs.microsoft.com/rest/api/media/assets/listcontainersas) můžete použít rozhraní API Media Services.

    **AssetContainerSas.listContainerSas** přebírá parametr [ListContainerSasInput,](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) `expiryTime`na kterém jste nastavili . Čas by měl být nastaven na < 24 hodin.

    [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) vrátí více adres URL SAS, protože pro každý účet úložiště existují dva klíče účtu úložiště. Účet úložiště má dva klíče, protože pomáhá s převzetím služeb při selhání a bezproblémovou střídání klíčů účtu úložiště. První adresa URL SAS představuje první klíč účtu úložiště a druhá adresa URL SAS představuje druhý klíč.
3. Pomocí rozhraní API úložiště Azure nebo sad SDK (například [rozhraní REST API úložiště](../../storage/common/storage-rest-api-auth.md) nebo [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) nahrát soubory do kontejneru prostředků.
4. Pomocí rozhraní API služby Media Services verze 3 můžete vytvořit transformaci a úlohu ke zpracování vstupního aktiva. Další informace najdete v tématu [Transformace a úlohy](transform-concept.md).
5. Streamujte obsah z "výstupního" datového zdroje.

### <a name="create-a-new-asset"></a>Vytvoření nového aktiva

> [!NOTE]
> Vlastnosti datového zdroje typu Datetime jsou vždy ve formátu UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Příklad REST najdete v příkladu [Vytvoření datového zdroje s REST.](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples)

Příklad ukazuje, jak vytvořit **tělo požadavku,** kde můžete zadat popis, název kontejneru, účet úložiště a další užitečné informace.

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

### <a name="see-also"></a>Viz také

* [Vytvoření vstupu úlohy z místního souboru](job-input-from-local-file-how-to.md)
* [Vytvoření vstupu úlohy z adresy URL HTTPS](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Ingestování a archivace živých přenosů do datového zdroje

Ve službě Media Services je objekt [živého výstupu](https://docs.microsoft.com/rest/api/media/liveoutputs) jako digitální videorekordér, který zachytí a zaznamená živý přenos do datového zdroje ve vašem účtu Mediálních služeb. Zaznamenaný obsah je trvalý do kontejneru definovaného prostředek [Asset.](https://docs.microsoft.com/rest/api/media/assets)

Další informace naleznete v tématu:

* [Použití cloudového DVR](live-event-cloud-dvr.md)
* [Streamování živého kurzu](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>Výstup výsledků úlohy na aktivum

Ve službě Media Services je při zpracování videí (například kódování nebo analýzy) nutné vytvořit výstupní [datový zdroj](assets-concept.md) pro uložení výsledku [úlohy](transforms-jobs-concept.md).

Další informace naleznete v tématu:

* [Kódování videa](encoding-concept.md)
* [Vytvoření vstupu úlohy z místního souboru](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Publikování datového zdroje pro streamování

Chcete-li publikovat datový zdroj pro streamování, musíte vytvořit [lokátor streamování](streaming-locators-concept.md). Lokátor datových proudů potřebuje znát název datového zdroje, který chcete publikovat. 

Další informace naleznete v tématu:

[Kurz: Nahrávání, kódování a streamování videí pomocí Mediálních služeb v3](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Stažení výsledků úlohy z výstupního datového zdroje

Tyto výsledky úlohy pak můžete stáhnout do místní složky pomocí rozhraní API služby Media Service a úložiště. 

Podívejte se na příklad [stahování souborů.](download-results-howto.md)

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Viz [Filtrování, řazení, stránkování entit Mediálních služeb](entities-overview.md).

## <a name="next-steps"></a>Další kroky

Podívejte se na příklady úplného kódu, které ukazují, jak nahrát, zakódovat, analyzovat, streamovat živě a na vyžádání: 

* [Java](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.NET](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [ODPOČÍVEK](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).
