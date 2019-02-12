---
title: Kódování Azure prostředku pomocí kodéru Media Encoder Standard | Dokumentace Microsoftu
description: Naučte se kódovat mediálního obsahu Azure Media Services pomocí kodéru Media Encoder Standard. Ukázky kódu pomocí rozhraní REST API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: e51fa086b26e5692d8dd572654b7c1ec50c641c5
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005144"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Kódování prostředku pomocí kodéru Media Encoder Standard
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Azure Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Přehled

Pokud chcete poskytovat digitální video přes Internet, je nutné médium komprimovat. Digitální video soubory jsou velké a může být příliš velký, aby poskytování přes Internet nebo pro zařízení vašich zákazníků a zobrazeny správně. Kódování je proces komprese videa a zvuku, takže vaši zákazníci mohou zobrazit médií.

Kódování úloh je jednou z nejběžnějších operací zpracování ve službě Azure Media Services. K převodu mediálních souborů z jednoho kódování do druhého se využívají kódovací úlohy. Při kódování, můžete použít předdefinované kodér Media Services (Media Encoder Standard). Můžete také použít kodér poskytovanými partnerem Media Services. Kodérů třetích stran jsou k dispozici prostřednictvím Azure Marketplace. Můžete zadat podrobnosti úlohy kódování pomocí přednastavení, které jsou definovány pro váš kodér, nebo pomocí přednastavených konfiguračních souborů. Typy předvolby, které jsou k dispozici, najdete v sekci [předvolby úloh pro Media Encoder Standard](https://msdn.microsoft.com/library/mt269960).

Každá úloha může mít jednu nebo více úloh v závislosti na typu zpracování, které chcete dosáhnout. Prostřednictvím rozhraní REST API můžete vytvořit úloh a jejich související úlohy v jedné ze dvou způsobů:

* Úkoly mohou být definována vložením prostřednictvím vlastnosti navigace úkoly u úlohy entit.
* Použijte dávkové zpracování protokolu OData.

Doporučujeme vždy zakódovat do sady MP4 adaptivní přenosové rychlosti zdrojové soubory a pak sadu převést na požadovaný formát s použitím [dynamické balení](media-services-dynamic-packaging-overview.md).

Pokud výstupní asset používá šifrování úložiště, musíte nakonfigurovat zásady doručení assetu. Další informace najdete v tématu [konfigurace zásad doručení assetu](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Požadavky

Při přístupu k entity ve službě Media Services, musíte nastavit specifická pole hlaviček a hodnoty v požadavcích HTTP. Další informace najdete v tématu [instalace pro vývoj pro Media Services REST API](media-services-rest-how-to-use.md).

Než začnete, odkazující na procesory médií, ověřte, že máte správná média ID procesoru. Další informace najdete v tématu [získat procesory médií](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní API pro AMS, naleznete v tématu [přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-job-with-a-single-encoding-task"></a>Vytvoření úlohy pomocí jediné úlohy kódování

> [!NOTE]
> Při práci s rozhraní REST API pro Media Services, platí následující aspekty:
>
> Při přístupu k entity ve službě Media Services, musíte nastavit specifická pole hlaviček a hodnoty v požadavcích HTTP. Další informace najdete v tématu [instalace pro vývoj rozhraní REST API pro Media Services](media-services-rest-how-to-use.md).
>
> Při pomocí formátu JSON a určení pro použití **__metadata** – klíčové slovo v požadavku (například k odkazu na odkazovaný objekt), je nutné nastavit **přijmout** záhlaví [JSON podrobný formát](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Přijměte: application/json; odata = verbose.
>
>

Následující příklad ukazuje, jak vytvořit a odeslat úlohu u jedné úlohy nastavit ke kódování videa na konkrétní řešení a kvalitu. Při kódování pomocí Media Encoder Standard, můžete použít přednastavení úloh konfigurace zadaná [tady](https://msdn.microsoft.com/library/mt269960).

Požadavek:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
        Authorization: Bearer <ENCODED JWT TOKEN> 
        x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
        Host: media.windows.net

    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Odpověď:

    HTTP/1.1 201 Created

    . . .

### <a name="set-the-output-assets-name"></a>Nastavte název výstupního prostředku
Následující příklad ukazuje, jak nastavit atribut assetName:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>Požadavky
* Taskbody – vlastnosti používaly literál XML definovat počet vstupních nebo výstupních prostředky, které jsou používány úkolu. Tento článek úloh obsahuje definici schématu XML pro XML.
* V definici taskbody – každé vnitřní hodnota <inputAsset> a <outputAsset> musí být nastavena jako JobInputAsset(value) nebo JobOutputAsset(value).
* Úkol může mít více výstupní assety. Jeden JobOutputAsset(x) jde použít jenom jednou jako výstup úlohy v rámci úlohy.
* Můžete zadat JobInputAsset nebo JobOutputAsset jako vstupní asset úlohy.
* Úlohy nesmí tvoří cyklus.
* Hodnota parametru, který můžete předat JobInputAsset nebo JobOutputAsset představuje hodnotu indexu pro určitý prostředek. Skutečné prostředky jsou definovány v navigační vlastnosti InputMediaAssets a OutputMediaAssets na definici entit úlohy.
* Protože Media Services je založená na protokolu OData v3, jednotlivé prostředky v InputMediaAssets a OutputMediaAssets navigační vlastnost kolekce odkazují "__metadata: identifikátor uri" dvojice název hodnota.
* InputMediaAssets mapuje na jeden nebo více prostředků, které jste vytvořili ve službě Media Services. OutputMediaAssets jsou vytvořeny v systému. Jejich neodkazují na existující prostředek.
* Pomocí atributu assetName může mít název OutputMediaAssets. Pokud tento atribut není k dispozici, pak OutputMediaAsset jmenuje bez ohledu na hodnotu vnitřní text z <outputAsset> element je s příponou hodnotu název úlohy nebo úlohu s Id hodnoty (v případě, kdy není definována vlastnost Name). Například pokud nastavíte hodnotu assetName na "Ukázkový", klikněte název OutputMediaAsset je nastavena na "Ukázkový". Nicméně, pokud jste nenastavili hodnotu assetName, ale nastaven název úlohy, který "NewJob", pak OutputMediaAsset název by měl být "_NewJob JobOutputAsset (hodnota)."

## <a name="create-a-job-with-chained-tasks"></a>Vytvoření úlohy zřetězené úkoly
V mnoha scénářích aplikací vývojáři chtějí vytvořit řadu úloh zpracování. Ve službě Media Services můžete vytvořit řadu zřetězené úkoly. Každý úkol provádí jiné zpracování kroky a může používat procesory architektury různá média. Zřetězené úkoly můžete předat prostředek z jednoho úkolu do druhého, provádění lineární posloupnost úloh na prostředku. Úlohy prováděné v rámci úlohy, ale nemusejí být v sekvenci. Když vytvoříte úlohu zřetězené, zřetězené **ITask** objekty vytvořené v jednom **IJob** objektu.

> [!NOTE]
> Není aktuálně maximálně 30 úkoly na jednu úlohu. Pokud je nutné zřetězit více než 30 úloh, vytvořte více než jednu úlohu tak, aby obsahovala úkoly.
>
>

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          },
          {  
             "Configuration":"H264 Smooth Streaming 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
          }
       ]
    }


### <a name="considerations"></a>Požadavky
Pokud chcete povolit řetězení úloh:

* Úloha musí mít alespoň dvě úlohy.
* Musí existovat alespoň jeden úkol, jejichž vstup je výstup jiný úkol v úloze.

## <a name="use-odata-batch-processing"></a>Použít dávkovým zpracováním OData
Následující příklad ukazuje způsob použití dávkovým zpracováním OData k vytvoření úlohy a úkolů. Informace o zpracování služby batch najdete v tématu [Open Data Protocol (OData), dávkové zpracování](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net


    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-ID: 1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.windows.net/api/$1/Tasks HTTP/1.1
    Content-ID: 2
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--



## <a name="create-a-job-by-using-a-jobtemplate"></a>Vytvoření úlohy s využitím JobTemplate
Při zpracování více prostředků s použitím společnou sadu úkolů, použijte JobTemplate zadejte výchozí přednastavení úloh nebo nastavit pořadí úkolů.

Následující příklad ukazuje, jak vytvořit JobTemplate s TaskTemplate, která je definována vložením. TaskTemplate používá Media Encoder Standard jako MediaProcessor ke kódování souboru prostředku. Další MediaProcessors však lze použít také.

    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }


> [!NOTE]
> Na rozdíl od dalších subjektů Media Services, musíte definovat nový identifikátor GUID pro každou TaskTemplate a umístěte ho do taskTemplateId a vlastnost Id v textu vašeho požadavku. Schéma obsahu identifikace musí následovat schéma popsaných v identifikovat Azure Media Services entity. Navíc JobTemplates se nedá aktualizovat. Místo toho musíte vytvořit nový s aktualizované změny.
>
>

V případě úspěchu se vrátí následující odpověď:

    HTTP/1.1 201 Created

    . . .


Následující příklad ukazuje, jak vytvořit úlohu, která odkazuje na identifikátor JobTemplate:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}


V případě úspěchu se vrátí následující odpověď:

    HTTP/1.1 201 Created

    . . .


## <a name="advanced-encoding-features-to-explore"></a>Pokročilé funkce Encoding prozkoumat
* [Postup generování miniatur](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Generování miniatur při kódování](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Oříznutí videa při kódování](media-services-crop-video.md)
* [Přizpůsobení kódovací předvolby](media-services-custom-mes-presets-with-dotnet.md)
* [Překryv nebo vodoznak videa s obrázkem](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další postup
Teď, když víte, jak vytvořit úlohu kódování prostředku, přečtěte si téma [jak zkontrolovat průběh úlohy pomocí služby Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Další informace najdete v tématech
[Získat procesory médií](media-services-rest-get-media-processor.md)
