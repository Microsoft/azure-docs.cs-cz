---
title: Jak kódovat datový zdroj Azure pomocí standardu Media Encoder Standard | Dokumenty společnosti Microsoft
description: Přečtěte si, jak pomocí standardu Media Encoder Standard kódovat mediální obsah ve službě Azure Media Services. Ukázky kódu používají rozhraní REST API.
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 6854400f2152a5952a7b24dbd860d7ad4bfc943d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774918"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Jak kódovat datový zdroj pomocí standardu media encoderstandardu
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [Odpočinku](media-services-rest-encode-asset.md)
> * [Portál](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Přehled

Chcete-li dodávat digitální video přes Internet, je nutné médium komprimovat. Digitální videosoubory jsou velké a mohou být příliš velké pro doručování přes internet nebo pro zařízení zákazníků, aby se správně zobrazovala. Kódování je proces komprese videa a zvuku, aby si zákazníci mohli prohlížet vaše média.

Úlohy kódování jsou jednou z nejběžnějších operací zpracování ve službě Azure Media Services. Vytvoření úloh kódování pro převod mediálních souborů z jednoho kódování do druhého. Při kódování můžete použít vestavěný kodér Media Services (Standard kodéru médií). Můžete také použít kodér poskytovaný partnerem mediálních služeb. Kodéry třetích stran jsou k dispozici na Azure Marketplace. Podrobnosti úloh kódování můžete určit pomocí přednastavených řetězců definovaných pro kodéru nebo pomocí přednastavených konfiguračních souborů. Typy přednastavení, které jsou k dispozici, naleznete v [tématu Přednastavení úloh pro standard kodéru médií](https://msdn.microsoft.com/library/mt269960).

Každá úloha může mít jeden nebo více úkolů v závislosti na typu zpracování, které chcete provést. Pomocí rozhraní REST API můžete vytvářet úlohy a související úkoly jedním ze dvou způsobů:

* Úkoly lze definovat vřádí prostřednictvím vlastnosti Navigace úkoly na entitách úlohy.
* Použijte dávkové zpracování OData.

Doporučujeme vždy zakódovat zdrojové soubory do sady MP4 s adaptivní množí datový tok a potom ji převést na požadovaný formát pomocí [dynamického balení](media-services-dynamic-packaging-overview.md).

Pokud je váš výstupní prostředek zašifrován úložištěm, musíte nakonfigurovat zásady poskytování prostředků. Další informace naleznete [v tématu Konfigurace zásad doručování majetku](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Požadavky

Při přístupu k entitám ve službě Media Services je nutné nastavit konkrétní pole záhlaví a hodnoty v požadavcích HTTP. Další informace naleznete [v tématu Setup for Media Services REST API Development](media-services-rest-how-to-use.md).

Než začnete odkazovat na mediální procesory, ověřte, zda máte správné ID mediálního procesoru. Další informace naleznete v tématu [Získání mediálních procesorů](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní AMS API, najdete [v tématu Přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-job-with-a-single-encoding-task"></a>Vytvoření úlohy s jednou úlohou kódování

> [!NOTE]
> Při práci s rozhraním REST API mediálních služeb platí následující důležité informace:
>
> Při přístupu k entitám ve službě Media Services je nutné nastavit konkrétní pole záhlaví a hodnoty v požadavcích HTTP. Další informace naleznete [v tématu Setup for Media Services REST API development](media-services-rest-how-to-use.md).
>
> Při použití JSON a určení použití **klíčového** slova __metadata v požadavku (například odkaz na propojený objekt), musíte nastavit **accept** header na [formát JSON Verbose](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Accept: application/json;odata=verbose.
>
>

Následující příklad ukazuje, jak vytvořit a zaúčtovat úlohu s jednou sadou úloh pro kódování videa v určitém rozlišení a kvalitě. Při kódování pomocí standardu kodéru médií můžete použít přednastavení konfigurace úloh, která jsou zde [zadána](https://msdn.microsoft.com/library/mt269960).

Požadavek:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
        Authorization: Bearer <ENCODED JWT TOKEN> 
        x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
        Host: media.windows.net

    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Odpověď:

    HTTP/1.1 201 Created

    . . .

### <a name="set-the-output-assets-name"></a>Nastavení názvu výstupního datového zdroje
Následující příklad ukazuje, jak nastavit atribut assetName:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>Požadavky
* Vlastnosti TaskBody musí používat literál XML k definování počtu vstupních nebo výstupních datových zdrojů, které jsou úkolem používány. Článek úlohy obsahuje definici schématu XML pro XML.
* V definici TaskBody musí `<inputAsset>` být `<outputAsset>` každá vnitřní hodnota pro a musí být nastavena jako JobInputAsset(hodnota) nebo JobOutputAsset(hodnota).
* Úloha může mít více výstupních datových zdrojů. Jeden JobOutputAsset(x) lze použít pouze jednou jako výstup úkolu v úloze.
* Jako vstupní majetek úkolu můžete zadat JobInputAsset nebo JobOutputAsset.
* Úkoly nesmí tvořit cyklus.
* Parametr hodnoty, který předáte JobInputAsset nebo JobOutputAsset představuje hodnotu indexu pro majetek. Skutečné datové zdroje jsou definovány v navigačních vlastnostech InputMediaAssets a OutputMediaAssets v definici entity úlohy.
* Vzhledem k tomu, že media services je postaven na OData v3, jednotlivé prostředky v InputMediaAssets a OutputMediaAssets kolekce navigační vlastnosti jsou odkazovány prostřednictvím "__metadata: uri" název-hodnota dvojice.
* InputMediaAssets se mapuje na jeden nebo více datových zdrojů vytvořených ve službě Media Services. OutputMediaAssets jsou vytvořeny systémem. Neodkazují na existující majetek.
* OutputMediaAssets lze pojmenovat pomocí atributu assetName. Pokud tento atribut není k dispozici, pak název OutputMediaAsset je `<outputAsset>` bez ohledu na vnitřní textovou hodnotu prvku je s příponou buď název úlohy hodnotu nebo Id úlohy (v případě, kdy Name vlastnost není definována). Pokud například nastavíte hodnotu assetName na "Ukázka", bude vlastnost OutputMediaAsset Name nastavena na hodnotu Ukázka. Pokud jste však nenastavili hodnotu pro assetName, ale nastavili název úlohy na "NewJob", pak by název OutputMediaAsset byl "JobOutputAsset(hodnota)_NewJob."

## <a name="create-a-job-with-chained-tasks"></a>Vytvoření úlohy s zřetězenými úkoly
V mnoha scénářích aplikace vývojáři chtějí vytvořit řadu úloh zpracování. Ve službě Media Services můžete vytvořit řadu zřetězených úloh. Každá úloha provádí různé kroky zpracování a může používat různé mediální procesory. Zřetězené úkoly mohou předávat datový zdroj z jednoho úkolu do druhého a provádět lineární posloupnost úkolů na datovém zdroji. Úkoly prováděné v úloze však nemusí být v pořadí. Při vytváření zřetězené úlohy jsou zřetězené objekty **ITask** vytvořeny v jednom objektu **IJob.**

> [!NOTE]
> V současné době existuje limit 30 úkolů na úlohu. Pokud potřebujete zřetězit více než 30 úkolů, vytvořte více než jednu úlohu, která bude úkoly obsahovat.
>
>

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
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
Povolení řetězení úloh:

* Úloha musí mít alespoň dva úkoly.
* Musí existovat alespoň jeden úkol, jehož vstup je výstupem jinéúlohy v úloze.

## <a name="use-odata-batch-processing"></a>Použití dávkového zpracování OData
Následující příklad ukazuje, jak pomocí dávkového zpracování OData vytvořit úlohu a úkoly. Informace o dávkovém zpracování naleznete v tématu [Open Data Protocol (OData) Batch Processing](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
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
    x-ms-version: 2.19
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
    x-ms-version: 2.19
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--



## <a name="create-a-job-by-using-a-jobtemplate"></a>Vytvoření úlohy pomocí šablony úlohy
Při zpracování více datových zdrojů pomocí společné sady úkolů použijte šablonu úlohy k určení výchozích přednastavení úloh nebo k nastavení pořadí úkolů.

Následující příklad ukazuje, jak vytvořit šablonu úlohy s tasktemplate, který je definován v řádku. TaskTemplate používá standard kodéru médií jako mediaprocessor ke kódování souboru datového zdroje. Lze však použít i jiné mediaprocesory.

    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }


> [!NOTE]
> Na rozdíl od jiných entit Media Services je nutné definovat nový identifikátor GUID pro každou šablonu úlohy a umístit ji do vlastnosti taskTemplateId a Id v těle požadavku. Schéma identifikace obsahu musí následovat schéma popsané v části Identifikace entit Mediálních služeb Azure. Šablony jobů také nelze aktualizovat. Místo toho je nutné vytvořit nový s aktualizovanými změnami.
>
>

Pokud je úspěšná, je vrácena následující odpověď:

    HTTP/1.1 201 Created

    . . .


Následující příklad ukazuje, jak vytvořit úlohu, která odkazuje na ID šablony úlohy:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}


Pokud je úspěšná, je vrácena následující odpověď:

    HTTP/1.1 201 Created

    . . .


## <a name="advanced-encoding-features-to-explore"></a>Pokročilé funkce kódování k prozkoumání
* [Jak generovat miniatury](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Generování miniatur během kódování](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Oříznutí videí během kódování](media-services-crop-video.md)
* [Přizpůsobení přednastavení kódování](media-services-custom-mes-presets-with-dotnet.md)
* [Překrytí nebo vodoznak videa s obrázkem](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky
Nyní, když víte, jak vytvořit úlohu pro kódování datového zdroje, naleznete v tématu [Jak zkontrolovat průběh úlohy pomocí služby Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Viz také
[Získat mediální procesory](media-services-rest-get-media-processor.md)
