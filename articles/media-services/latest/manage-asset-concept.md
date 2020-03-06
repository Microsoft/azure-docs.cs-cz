---
title: Správa prostředků v Azure Media Services
titleSuffix: Azure Media Services
description: Prostředek, ke kterému zadáte médium (například prostřednictvím upload nebo Live ingest), výstupní média (z výstupu úlohy) a publikování médií z (pro streamování). Toto téma poskytuje přehled o tom, jak vytvořit nový Asset a nahrát soubory.
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
ms.openlocfilehash: b1ec4ee3d7a51c2a21a5bbd8888ea4662cf78bf5
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304153"
---
# <a name="manage-assets"></a>Správa prostředků

V Azure Media Services [Asset](https://docs.microsoft.com/rest/api/media/assets) je místo, kde jste 

* nahrávání mediálních souborů do assetu,
* ingestování a archivace živých streamů do assetu
* výstup výsledků kódování úlohy analýzy do assetu
* publikování médií pro streamování, 
* Stažení souborů z assetu.

Toto téma poskytuje přehled o tom, jak nahrát soubory do assetu a provádět některé další běžné operace. Obsahuje také odkazy na ukázky kódu a související témata.

## <a name="prerequisite"></a>Požadavek 

Než začnete s vývojem, přečtěte si:

* [Koncepty](concepts-overview.md)
* [Vývoj s využitím rozhraní Media Services V3 API](media-services-apis-overview.md) (zahrnuje informace o přístupu k rozhraním API, konvencím pojmenování atd.) 

## <a name="upload-media-files-into-an-asset"></a>Nahrání mediálních souborů do assetu

Až se digitální soubory nahrají do úložiště a přidruží se k Assetu, dají se použít v Media Services kódování, streamování a analýze pracovních postupů obsahu. Jedním ze běžných Media Services pracovních postupů je nahrání, kódování a streamování souboru. Tato část popisuje obecné kroky.

1. Použijte rozhraní API Media Services verze 3 k vytvoření nového vstupního aktiva. Tato operace vytvoří kontejner v účtu úložiště přidruženém k vašemu účtu Media Services. Rozhraní API vrátí název kontejneru (například `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).

    Pokud již máte kontejner objektů blob, který chcete přidružit k Assetu, můžete zadat název kontejneru při vytváření Assetu. Platforma Media Services momentálně podporuje jenom objekty blob v kořenové složce kontejneru, a ne s cestou v názvu souboru. Proto bude kontejner s názvem souboru „input.mp4“ fungovat. Kontejner s názvem souboru video/vstupy/Input. mp4 ale nebude fungovat.

    K nahrání přímo do jakékoli účtu úložiště a kontejneru, ke kterým máte oprávnění v rámci vašeho předplatného, můžete použít rozhraní Azure CLI.

    Název kontejneru musí být jedinečný a splňovat pokyny pro pojmenování úložiště. Název nemusí splňovat formátování názvů kontejnerů aktiv Media Services (Asset-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Získejte adresu URL SAS s oprávněními pro čtení i zápis, která se použije k nahrání digitálních souborů do kontejneru aktiva. K [vypsání adres URL kontejnerů aktiv](https://docs.microsoft.com/rest/api/media/assets/listcontainersas) můžete použít rozhraní API Media Services.
3. Použijte rozhraní API Azure Storage nebo sady SDK (například [úložiště REST API](../../storage/common/storage-rest-api-auth.md) nebo [sadu .NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) k nahrání souborů do kontejneru assetů.
4. Pomocí rozhraní API služby Media Services verze 3 můžete vytvořit transformaci a úlohu ke zpracování vstupního aktiva. Další informace najdete v tématu [Transformace a úlohy](transform-concept.md).
5. Streamování obsahu z prostředku "Output".

### <a name="create-a-new-asset"></a>Vytvořit nový prostředek

> [!NOTE]
> Vlastnosti Assetu typu DateTime jsou vždycky ve formátu UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Příklad REST najdete v tématu [Vytvoření assetu pomocí REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) .

V tomto příkladu se dozvíte, jak vytvořit **Text žádosti** , kde můžete zadat popis, název kontejneru, účet úložiště a další užitečné informace.

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

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Ingestování a archivace živých streamů do assetu

V Media Services je objekt [živého výstupu](https://docs.microsoft.com/rest/api/media/liveoutputs) podobný digitálnímu záznamu videa, který zachytí a zaznamená svůj živý stream do assetu v účtu Media Services. Zaznamenaný obsah je trvale uložen do kontejneru definovaného prostředkem [assetu](https://docs.microsoft.com/rest/api/media/assets) .

Další informace naleznete v tématu:

* [Použití cloudového videorekordéru](live-event-cloud-dvr.md)
* [Kurz živého streamování](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>Výstup výsledků úlohy do assetu

V Media Services při zpracování videí (například kódování nebo analýzy) potřebujete vytvořit výstupní [Asset](assets-concept.md) pro uložení výsledku vaší [úlohy](transforms-jobs-concept.md).

Další informace naleznete v tématu:

* [Kódování videa](encoding-concept.md)
* [Vytvoření vstupu úlohy z místního souboru](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Publikování assetu pro streamování

Chcete-li publikovat Asset pro streamování, je nutné vytvořit [Lokátor streamování](streaming-locators-concept.md). Lokátor streamování musí znát název assetu, který chcete publikovat. 

Další informace naleznete v tématu:

[Kurz: nahrávání, kódování a streamování videí pomocí Media Services V3](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Stažení výsledků úlohy z výstupního prostředku

Tyto výsledky vaší úlohy pak můžete stáhnout do místní složky pomocí služby Media Service a rozhraní API pro úložiště. 

Podívejte se na příklad [Stažení souborů](download-results-howto.md) .

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Viz téma [filtrování, řazení, stránkování Media Services entit](entities-overview.md).

## <a name="next-steps"></a>Další kroky

Podívejte se na kompletní příklady kódu, které ukazují, jak nahrávat, kódovat, analyzovat, streamovat živě a na vyžádání: 

* [Java](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.NET](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [REST](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).
