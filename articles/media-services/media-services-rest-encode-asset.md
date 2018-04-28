---
title: Postup kódování Azure assetu pomocí kodéru Media Encoder Standard | Microsoft Docs
description: Další informace o použití Media Encoder Standard ke kódování mediální obsah v Azure Media Services. Ukázky kódu pomocí rozhraní REST API.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: juliako
ms.openlocfilehash: efd4df24baaaf40ba90dd171d5227f61d1c0b36e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Postup kódování assetu pomocí kodéru Media Encoder Standard
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Azure Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Přehled
Chcete-li poskytovat digitální video přes Internet, je nutné médium komprimovat. Digitální video soubory jsou velké, může být příliš velký pro doručení přes Internet nebo pro zařízení vašich zákazníků a zobrazeny správně. Kódování je proces komprimace videa a zvuku, takže vaši zákazníci mohou zobrazit médiu.

Kódování úlohy jsou některé z nejběžnějších zpracování operací ve službě Azure Media Services. K převodu mediálních souborů z jednoho kódování do druhého se využívají kódovací úlohy. Při kódování, můžete použít předdefinované kodéru Media Services (Media Encoder Standard). Můžete také použít kodér poskytovanými partnerem Media Services. Třetí strany kodéry jsou k dispozici prostřednictvím Azure Marketplace. Můžete zadat podrobnosti úlohy kódování, pomocí přednastavení definované pro kodér nebo pomocí přednastavené konfigurační soubory. Typy přednastavení, které jsou k dispozici, najdete v sekci [přednastavení úloh pro Media Encoder Standard](http://msdn.microsoft.com/library/mt269960).

Každá úloha může mít jeden nebo více úloh, v závislosti na typu zpracování, který chcete provést. Přes rozhraní REST API můžete vytvořit úlohy a jejich související úlohy v jedné ze dvou způsobů:

* Úlohy může být definována vložením prostřednictvím úlohy navigační vlastnost u entity úlohy.
* Použijte dávkovým zpracováním OData.

Doporučujeme vždy zakódovat zdrojové soubory do sady souborů MP4 adaptivní přenosovou rychlostí a pak sadu převést na požadovaný formát pomocí [dynamické balení](media-services-dynamic-packaging-overview.md).

Pokud výstupní asset používá šifrování úložiště, musíte nakonfigurovat zásady doručení assetu. Další informace najdete v tématu [konfigurace zásad doručení assetu](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Požadavky

Při přístupu k entity ve službě Media Services, musíte nastavit specifická pole hlaviček a hodnoty ve své žádosti HTTP. Další informace najdete v tématu [instalační program pro Media Services REST API vývoj](media-services-rest-how-to-use.md).

Než začnete, odkazující na procesory médií, ověřte, zda máte správná média ID procesoru. Další informace najdete v tématu [získat procesory médií](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak připojit k rozhraní API pro AMS najdete v tématu [přístup k Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-job-with-a-single-encoding-task"></a>Vytvoření úlohy pomocí jednoho úkolu kódování
> [!NOTE]
> Při práci s Media Services REST API, platí následující aspekty:
>
> Při přístupu k entity ve službě Media Services, musíte nastavit specifická pole hlaviček a hodnoty ve své žádosti HTTP. Další informace najdete v tématu [nastavení pro vývoj pro Media Services REST API](media-services-rest-how-to-use.md).
>
> Při použití formátu JSON a určení pro použití **__metadata** – klíčové slovo v požadavku (například na referenční dokumentace propojeného objektu), musíte nastavit **přijmout** hlavičky k [JSON podrobný formát](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Přijměte: application/json; odata = verbose.
>
>

Následující příklad ukazuje, jak vytvořit a odeslat úlohu s jeden úkol nastavit ke kódování videa na konkrétní řešení a kvality. Při kódování pomocí procesoru Media Encoder Standard, můžete použít přednastavení úloh konfigurace zadané [zde](http://msdn.microsoft.com/library/mt269960).

Žádost:

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

### <a name="set-the-output-assets-name"></a>Nastavte název výstupního assetu
Následující příklad ukazuje, jak nastavit atribut assetName:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>Požadavky
* Taskbody – vlastnosti používaly literál XML Definujte počet vstup nebo výstup prostředky, které jsou používány úlohu. Úloha článek obsahuje definici schématu XML pro soubor XML.
* V definici taskbody – každý vnitřní hodnota <inputAsset> a <outputAsset> musí být nastavena jako JobInputAsset(value) nebo JobOutputAsset(value).
* Úloha může mít více prostředků výstup. Jeden JobOutputAsset(x) lze použít jako výstup úlohy pro úlohu pouze jednou.
* Můžete zadat JobInputAsset nebo JobOutputAsset jako vstupní datový zdroj úlohy.
* Úlohy nesmí tvoří cyklus.
* Hodnota parametru, který můžete předat JobInputAsset nebo JobOutputAsset představuje hodnotu indexu pro určitý prostředek. Skutečné prostředky jsou definovány v navigační vlastnosti InputMediaAssets a OutputMediaAssets v definici úlohy entity.
* Služba Media Services je integrovaná v OData v3, jsou jednotlivé prostředky v kolekcích navigační vlastnost InputMediaAssets a OutputMediaAssets odkazuje prostřednictvím "__metadata: identifikátor uri" dvojice název hodnota.
* InputMediaAssets se mapuje na jeden nebo více prostředků, které jste vytvořili ve službě Media Services. OutputMediaAssets jsou vytvořené v systému. Nemusíte se odkazovat na existující prostředek.
* Pomocí atributu assetName může mít název OutputMediaAssets. Pokud tento atribut není k dispozici, je název OutputMediaAsset bez ohledu na hodnotu vnitřní text z <outputAsset> element je s příponou název úlohy hodnota nebo hodnota Id úlohy (v případě, kde není definována vlastnost Name). Například pokud nastavíte hodnotu assetName "Ukázce", pak název OutputMediaAsset je nastavena na "Ukázka." Ale pokud nebylo nastavit hodnotu assetName, ale nastavena název úlohy na "NewJob", pak OutputMediaAsset název by měl být "_NewJob JobOutputAsset (hodnota)."

## <a name="create-a-job-with-chained-tasks"></a>Vytvořit úlohu zřetězené úlohy
V mnoha scénářích aplikace vývojáři chcete vytvořit řadu zpracování úlohy. Ve službě Media Services můžete vytvořit řadu zřetězené úlohy. Každý úkol provádí různé zpracování kroky a můžete použít různé média procesory. Zřetězené úlohy můžete předat prostředek od jednoho do jiného provádění lineární pořadí úloh na asset. Úlohy prováděné v rámci úlohy, ale nemusejí být v pořadí. Když vytvoříte úlohu zřetězené, zřetězené **ITask** objekty jsou vytvořené v jedné **IJob** objektu.

> [!NOTE]
> Je aktuálně omezeno na 30 úloh na úlohu. Pokud potřebujete zřetězit více než 30 úloh, vytvořte více než jednu úlohu tak, aby obsahovala úlohy.
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
Chcete-li povolit řetězení úloh:

* Úloha musí mít alespoň dvě úlohy.
* Musí existovat alespoň jeden úkol, jejichž vstup je výstup jiná úloha v úloze.

## <a name="use-odata-batch-processing"></a>Použít dávkovým zpracováním OData
Následující příklad ukazuje, jak používat dávkovým zpracováním OData k vytvoření úlohy a úlohy. Informace o zpracování dávky, najdete v části [Open Data Protocol (OData) dávkové zpracování](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

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



## <a name="create-a-job-by-using-a-jobtemplate"></a>Vytvořit úlohu pomocí JobTemplate
Když při zpracování více prostředků pomocí společnou sadu úloh, použijte JobTemplate zadat výchozí přednastavení úloh nebo nastavit pořadí úkolů.

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
> Na rozdíl od jinými entitami Media Services musíte definovat nový identifikátor GUID pro každou TaskTemplate a umístěte jej v taskTemplateId a vlastnost Id v textu vaší žádosti. Schéma obsahu identifikace musí následovat schéma popsaných v identifikovat Azure Media Services entity. Navíc JobTemplates nelze aktualizovat. Místo toho musíte vytvořit novou s aktualizované změny.
>
>

V případě úspěchu se vrátí následující odpověď:

    HTTP/1.1 201 Created

    . . .


Následující příklad ukazuje, jak vytvořit úlohu, která odkazuje na JobTemplate Id:

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


## <a name="advanced-encoding-features-to-explore"></a>Pokročilé funkce chcete prozkoumat kódování
* [Postup vytváření miniatur](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Vytváření miniatur během kódování](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Oříznutí videa během kódování](media-services-crop-video.md)
* [Přizpůsobení přednastavení kódování](media-services-custom-mes-presets-with-dotnet.md)
* [Překrytí nebo vodoznaku video s bitovou kopií](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další postup
Teď, když víte, jak vytvořit úlohu kódování assetu, najdete v článku [jak zkontrolovat průběh úlohy pomocí služby Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Další informace najdete v tématech
[Získat procesory médií](media-services-rest-get-media-processor.md)
