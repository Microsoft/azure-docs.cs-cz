---
title: Postup kódování prostředku Azure pomocí Media Encoder Standard | Microsoft Docs
description: Naučte se používat Media Encoder Standard ke kódování mediálního obsahu v Azure Media Services. Ukázky kódu používají REST API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d4b7a188a9509c3510de2843b232f57e3437177b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009823"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Postup při kódování assetu pomocí Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Azure Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Přehled

Aby bylo možné doručovat digitální video přes Internet, je nutné médium zkomprimovat. Digitální video soubory jsou velké a můžou být moc velké, aby je bylo možné doručit přes Internet, nebo aby se zařízení vašich zákazníků zobrazovalo správně. Kódování je proces komprimace videa a zvuku, aby vaši zákazníci mohli zobrazit vaše média.

Úlohy kódování jsou jednou z nejběžnějších operací zpracování v Azure Media Services. Vytvoříte úlohy kódování pro převod mediálních souborů z jednoho kódování na jiný. Při kódování můžete použít Media Services integrovaný kodér (Media Encoder Standard). Můžete také použít kodér poskytovaný Media Services partnerem. Kodéry třetích stran jsou k dispozici prostřednictvím Azure Marketplace. Můžete zadat podrobnosti o úlohách kódování pomocí přednastavených řetězců definovaných pro váš kodér nebo pomocí přednastavených konfiguračních souborů. Chcete-li zobrazit typy přednastavení, které jsou k dispozici, přečtěte si téma [Předvolby úloh pro Media Encoder Standard](./media-services-mes-presets-overview.md).

Každá úloha může mít jeden nebo více úloh v závislosti na typu zpracování, které chcete provést. Pomocí REST API můžete vytvořit úlohy a jejich související úkoly jedním ze dvou způsobů:

* Úkoly lze definovat prostřednictvím vlastnosti navigace úkoly v entitách úlohy.
* Použijte dávkové zpracování OData.

Doporučujeme, abyste zdrojové soubory vždycky zakódujíi do sady MP4 s adaptivní přenosovou rychlostí, a pak tuto sadu převede na požadovaný formát pomocí [dynamického balení](media-services-dynamic-packaging-overview.md).

Pokud je váš výstupní prostředek zašifrovaný z úložiště, musíte nakonfigurovat zásady doručení assetu. Další informace najdete v tématu [Konfigurace zásad doručení assetu](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Požadavky

Při přístupu k entitám v Media Services musíte nastavit konkrétní pole a hodnoty hlaviček v požadavcích HTTP. Další informace najdete v tématu [instalace Media Services REST APIm vývoji](media-services-rest-how-to-use.md).

Než začnete s odkazování na procesory médií, ověřte, že máte správné ID multimediálního procesoru. Další informace najdete v tématu [získání mediálních procesorů](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní API AMS, najdete v tématu [přístup k rozhraní Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-job-with-a-single-encoding-task"></a>Vytvoření úlohy s jednou úlohou kódování

> [!NOTE]
> Při práci s Media Services REST API platí následující požadavky:
>
> Při přístupu k entitám v Media Services musíte nastavit konkrétní pole a hodnoty hlaviček v požadavcích HTTP. Další informace najdete v tématu [instalace Media Services REST APIm vývoji](media-services-rest-how-to-use.md).
>
> Při použití JSON a určení použití klíčového slova **__metadata** v žádosti (například pro odkazování na propojený objekt) musíte nastavit [podrobný formát](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) **Accept** on JSON: přijmout: Application/JSON; OData = verbose.
>
>

Následující příklad ukazuje, jak vytvořit a publikovat úlohu s jednou nastavenou úlohou ke kódování videa v konkrétním rozlišení a kvalitě. Při kódování pomocí Media Encoder Standard můžete použít předvolby konfigurace úloh, které jsou [tady](./media-services-mes-presets-overview.md)uvedené.

Požadavek:

```console
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
```

Odpověď:

```console
HTTP/1.1 201 Created

. . .
```

### <a name="set-the-output-assets-name"></a>Nastavit název výstupního prostředku
Následující příklad ukazuje, jak nastavit atribut majetku:

```console
{ "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}`
```

### <a name="considerations"></a>Požadavky
* Vlastnosti TaskBody – musí použít literál XML pro definování počtu vstupních nebo výstupních prostředků, které úkol používá. Článek o úloze obsahuje definici schématu XML pro XML.
* V definici TaskBody – je nutné, aby každá vnitřní hodnota pro `<inputAsset>` a `<outputAsset>` byla nastavena jako JobInputAsset (hodnota) nebo JobOutputAsset (hodnota).
* Úloha může mít více výstupních prostředků. Jeden JobOutputAsset (x) lze použít pouze jednou jako výstup úkolu v úloze.
* Jako vstupní Asset úkolu můžete zadat JobInputAsset nebo JobOutputAsset.
* Úkoly nesmí tvořit cyklus.
* Parametr hodnoty, který předáte do JobInputAsset nebo JobOutputAsset, představuje hodnotu indexu pro Asset. Skutečné prostředky jsou definovány v navigačním vlastnosti InputMediaAssets a OutputMediaAssets v definici entity úlohy.
* Vzhledem k tomu, že Media Services je postavená na OData V3, jednotlivé prostředky v kolekcích vlastností navigace InputMediaAssets a OutputMediaAssets odkazují na dvojici název-hodnota __metadata: URI.
* InputMediaAssets se mapuje na jeden nebo více assetů, které jste vytvořili v Media Services. OutputMediaAssets jsou vytvořeny systémem. Neodkazují na stávající Asset.
* OutputMediaAssets lze pojmenovat pomocí atributu název prostředku. Pokud tento atribut není k dispozici, pak název OutputMediaAsset je bez ohledu na to, zda je vnitřní text hodnoty `<outputAsset>` prvku přípona hodnoty názvu úlohy nebo hodnota ID úlohy (v případě, že vlastnost Name není definována). Pokud například nastavíte hodnotu pro název prostředku na "Sample", vlastnost OutputMediaAsset Name je nastavena na "Sample". Pokud jste však nenastavili hodnotu pro název prostředku, ale nastavili jste název úlohy na "NewJob", pak bude název OutputMediaAsset "JobOutputAsset (hodnota) _NewJob."

## <a name="create-a-job-with-chained-tasks"></a>Vytvoření úlohy s zřetězenými úlohami
V mnoha scénářích aplikací chtějí vývojáři vytvořit řadu úloh zpracování. V Media Services můžete vytvořit řadu zřetězených úloh. Každý úkol provádí různé kroky zpracování a může používat různé procesory médií. Zřetězené úlohy mohou předání assetu z jednoho úkolu do druhého a provádění lineární posloupnosti úloh v rámci assetu. Úkoly prováděné v úloze ale nemusí být v sekvenci. Při vytváření zřetězené úlohy jsou zřetězené objekty **ITask** vytvořeny v jednom objektu **IJob** .

> [!NOTE]
> V současné době je povolený limit 30 úkolů na úlohu. Pokud potřebujete zřetězit více než 30 úloh, vytvořte více než jednu úlohu, která bude obsahovat úkoly.
>
>

```console
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
```

### <a name="considerations"></a>Požadavky
Povolení Řetězení úloh:

* Úloha musí obsahovat alespoň dvě úlohy.
* Musí existovat alespoň jeden úkol, jehož vstupem je výstup jiné úlohy v úloze.

## <a name="use-odata-batch-processing"></a>Použití dávkového zpracování OData
Následující příklad ukazuje, jak pomocí dávkového zpracování OData vytvořit úlohu a úkoly. Informace o dávkovém zpracování najdete v tématu [dávkové zpracování protokolu OData (Open Data Protocol)](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

```console
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
```


## <a name="create-a-job-by-using-a-jobtemplate"></a>Vytvoření úlohy pomocí JobTemplate
Když pracujete s více prostředky pomocí běžné sady úkolů, použijte JobTemplate k určení výchozích přednastavených úkolů nebo k nastavení pořadí úkolů.

Následující příklad ukazuje, jak vytvořit JobTemplate s TaskTemplate, která je definována jako inline. TaskTemplate používá Media Encoder Standard jako MediaProcessor ke kódování souboru prostředků. Můžete ale také použít jiné MediaProcessors.

```console
POST https://media.windows.net/API/JobTemplates HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net


{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
```

> [!NOTE]
> Na rozdíl od jiných entit Media Services musíte definovat nový identifikátor GUID pro každý TaskTemplate a umístit ho do vlastnosti taskTemplateId a ID v textu žádosti. Identifikační schéma obsahu musí odpovídat schématu popsanému v tématu identifikace Azure Media Services entit. Nelze také aktualizovat JobTemplates. Místo toho je nutné vytvořit novou pomocí aktualizovaných změn.
>
>

V případě úspěchu se vrátí následující odpověď:

```console
HTTP/1.1 201 Created

. . .
```

Následující příklad ukazuje, jak vytvořit úlohu, která odkazuje na ID JobTemplate:

```console
POST https://media.windows.net/API/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net


{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
```

V případě úspěchu se vrátí následující odpověď:

```console
HTTP/1.1 201 Created

. . .
```


## <a name="advanced-encoding-features-to-explore"></a>Pokročilé funkce kódování, které se mají prozkoumat
* [Jak generovat miniatury](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Generování miniatur během kódování](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Oříznout videa během kódování](media-services-crop-video.md)
* [Přizpůsobení předvoleb kódování](media-services-custom-mes-presets-with-dotnet.md)
* [Překrytí nebo zobrazení vodoznaku videa s obrázkem](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky
Když teď víte, jak vytvořit úlohu pro zakódování assetu, přečtěte si téma [Jak zkontrolovat průběh úlohy pomocí Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Viz také
[Získat procesory médií](media-services-rest-get-media-processor.md)