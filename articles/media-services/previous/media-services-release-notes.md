---
title: Poznámky k verzi Azure Media Services | Microsoft Docs
description: Tento článek pojednává o poznámkách k verzi Microsoft Azure Media Services V2.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 019f0bc98de45fa1fe6f9b8c72ef74beb50ea2c2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017354"
---
# <a name="azure-media-services-release-notes"></a>Poznámky k verzi Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Tyto poznámky k verzi pro Azure Media Services shrnují změny z předchozích verzí a známých problémů.

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

Chceme od našich zákazníků slyšet, abychom se mohli zaměřit na opravy problémů, které vás zaovlivňují. Chcete-li nahlásit problém nebo položit otázky, odešlete příspěvek na webu [Azure Media Services Fórum MSDN]. 

## <a name="known-issues"></a><a name="issues"></a>Známé problémy
### <a name="media-services-general-issues"></a><a name="general_issues"></a>Media Services Obecné problémy

| Problém | Description |
| --- | --- |
| V REST API není k dispozici několik běžných hlaviček protokolu HTTP. |Pokud vyvíjíte Media Services aplikace pomocí REST API, zjistíte, že některá společná pole hlaviček protokolu HTTP (včetně klienta-požadavek-ID, požadavek-ID a RETURN-CLIENT-REQUEST-ID) se nepodporují. Hlavičky budou přidány v budoucí aktualizaci. |
| Procento – kódování není povoleno. |Media Services používá hodnotu vlastnosti IAssetFile.Name při vytváření adres URL pro obsah streamování (například `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters` ). Z tohoto důvodu není povolena procentuální kódování. Hodnota vlastnosti Name nemůže obsahovat žádný z následujících [znaků rezervovaných v procentech](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! * ' ();: @ &= + $,/?% # [] ". Přípona názvu souboru může taky obsahovat jenom jeden znak ".". |
| Metoda ListBlobs, která je součástí sady Azure Storage SDK verze 3. x, se nezdařila. |Media Services generuje adresy URL SAS na základě verze [2012-02-12](/rest/api/storageservices/version-2012-02-12) . Pokud chcete použít sadu SDK úložiště k vypsání objektů BLOB v kontejneru objektů blob, použijte metodu [CloudBlobContainer. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) , která je součástí sady Storage SDK verze 2. x. |
| Mechanismus omezování Media Services omezuje využití prostředků u aplikací, které provedou nadměrné požadavky na službu. Služba může vrátit stav "služba není k dispozici" 503 HTTP kód stavu. |Další informace najdete v popisu stavového kódu HTTP 503 v tématu [Media Services kódy chyb](media-services-encoding-error-codes.md). |
| Při dotazování entit se vrátí limit 1 000 entit najednou, protože veřejná verze REST 2 omezuje výsledky dotazu na 1 000 výsledků. |Použijte Skip a/Top (rozhraní .NET) (REST), jak je popsáno v [tomto příkladu .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) a v [tomto příkladu REST API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Někteří klienti můžou přijít do problému se značkou opakování v manifestu Smooth Streaming. |Další informace najdete v [této části](media-services-deliver-content-overview.md#known-issues). |
| Objekty sady .NET SDK Media Services nejde serializovat a v důsledku toho nefungují s Azure cache pro Redis. |Pokud se pokusíte serializovat objekt sady SDK Assetcollection a přidat ho do mezipaměti Azure pro Redis, je vyvolána výjimka. |
|REST API odpoví chybovou zprávou, že při pokusu o získání filtru na úrovni prostředku nebo účtu není v této verzi rozhraní REST API přístup k filtru.|Filtr byl vytvořen nebo upraven novější verzí rozhraní API, než se používá k pokusu o získání filtru. K tomu může dojít, pokud jsou dvě verze rozhraní API používány kódem nebo nástroji používanými zákazníkem.  Nejlepším řešením je upgradovat kód nebo nástroje na použití novějších nebo těchto dvou verzí rozhraní API.|

## <a name="rest-api-version-history"></a><a name="rest_version_history"></a>Historie verze REST API
Informace o historii verze REST API Media Services najdete v části [Azure Media Services REST API referenci].

## <a name="february-2021"></a>Únor 2021

### <a name="azure-media-services-v2-api-and-sdks-deprecation-announcement"></a>Oznámení o zastaralosti rozhraní API pro Azure Media Services V2 a sady SDK

#### <a name="update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024"></a>Aktualizace Azure Media Services REST API a sad SDK na V3 od 29. února 2024

Vzhledem k tomu, že verze 3 Azure Media Services REST API a klientské sady SDK pro .NET a Java nabízí více funkcí než verze 2, vyvyřazujeme verze 2 Azure Media Services REST API a klientské sady SDK pro .NET a Java. Doporučujeme, abyste tento přepínač provedli dřív, abyste získali bohatší výhody verze 3 Azure Media Services REST API a klientské sady SDK pro .NET a Java.
Verze 3 poskytuje:
 
- nepřetržitá podpora živé události
- Rozhraní REST API pro platformu ARM, klientské sady SDK pro .NET Core, Node.js, Python, Java, přejít a Ruby.
- Spravované klíče zákazníka, integrace důvěryhodných úložišť, podpora privátních odkazů a [Další](https://review.docs.microsoft.com/en-us/azure/media-services/latest/migrate-v-2-v-3-migration-benefits)

#### <a name="action-required"></a>Požaduje se akce:

Chcete-li minimalizovat přerušení vašich úloh, přečtěte si [Průvodce migrací](https://go.microsoft.com/fwlink/?linkid=2149150&clcid=0x409) a převeďte svůj kód z rozhraní API verze 2 a sady SDK na verzi 3 API a SDK před 29. února 2024.
**Po 29. února 2024** přestane Azure Media Services nadále přijímat provoz ve verzi 2 REST API, rozhraní API pro správu účtů ARM verze 2015-10-01 nebo z SDK klienta .NET verze 2. To zahrnuje všechny klientské sady SDK Open Source třetích stran, které mohou volat rozhraní API verze 2.  

Podívejte se na oficiální [oznámení o aktualizacích Azure](https://azure.microsoft.com/updates/update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024/).

## <a name="september-2020"></a>Září 2020

Následující vlastnosti v2 již nebudou naplněny pomocí historických dat o průběhu úlohy:

* [HistoricalEvents](/dotnet/api/microsoft.windowsazure.mediaservices.client.itask.historicalevents)
* [PerfMessage](/dotnet/api/microsoft.windowsazure.mediaservices.client.itask.perfmessage)

Chcete-li získat historii úlohy, měli byste použít oznámení úlohy v2 prostřednictvím webhooků nebo zpráv fronty pomocí koncových bodů oznámení. Další informace naleznete v tématu:

* [Monitorování oznámení úloh Media Services pomocí Azure Queue Storage](media-services-dotnet-check-job-progress-with-queues.md)
* [Monitorování oznámení úloh Media Services pomocí webhooků Azure](media-services-dotnet-check-job-progress-with-webhooks.md)

## <a name="february-2020"></a>Únor 2020

Některé procesory pro analýzu médií budou vyřazeny. Data o vyřazení najdete v tématu [starší verze součástí](legacy-components.md) .

## <a name="september-2019"></a>Září 2019

### <a name="deprecation-of-media-processors"></a>Vyřazení procesorů médií

Oznamujeme vyřazení *Azure Media Indexer* a *Azure Media Indexer 2 ve verzi Preview*. [Azure Media Services video indexer](../video-indexer/index.yml) nahrazuje tyto starší verze procesorů médií.

Informace o datech vyřazení najdete v tématu tyto [starší součásti](legacy-components.md) .

Přečtěte si také téma [migrace z Azure Media Indexer a Azure Media Indexer 2 na Azure Media Services video indexer](migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Srpen 2019

### <a name="deprecation-of-media-processors"></a>Vyřazení procesorů médií

Oznamujeme vyřazení *Windows Azure Media Encoder* (WAME) a *Azure Media Encoderch* (ázev) mediálních procesorů. Informace o datech vyřazení najdete v tématu tyto [starší součásti](legacy-components.md) .

Podrobnosti najdete v článku [migrace WAME do Media Encoder Standard](./migrate-windows-azure-media-encoder.md) a [migrace do Media Encoder Standard](./migrate-azure-media-encoder.md).

## <a name="march-2019"></a>Březen 2019

Funkce Media s náhledem datapit v Azure Media Services je zastaralá.

## <a name="december-2018"></a>Prosinec 2018

Funkce Media s náhledem, která je v Azure Media Services, bude brzy vyřazena. Od 19. prosince 2018 se Media Services už nemění ani nevylepšení multimédií. 29. března 2019 bude vyřazeno a již nebude k dispozici.

## <a name="october-2018"></a>Říjen 2018

### <a name="cmaf-support"></a>Podpora CMAF

CMAF a podpora šifrování "cbcs" pro Apple HLS (iOS 11 +) a přehrávače MPEG-SPOJOVNÍKů, které podporují CMAF.

### <a name="web-vtt-thumbnail-sprites"></a>Pohyblivé miniatury webových VTT

Nyní můžete použít Media Services k vygenerování pohyblivých souborů miniatur webových VTT pomocí našich rozhraní API v2. Další informace najdete v tématu [generování Sprite miniatury](generate-thumbnail-sprite.md).

## <a name="july-2018"></a>Červenec 2018

S nejnovější verzí služby jsou drobné změny v chybových zprávách, které služba vrátila, v případě, že úloha selže, s ohledem na to, jak je rozděleno na dva nebo více řádků.

## <a name="may-2018"></a>Květen 2018 

Od 12. května 2018 už živé kanály nebudou podporovat protokol ingestování přenosového streamu RTP/MPEG-2. Migrujte prosím z protokolu RTP/MPEG-2 na RTMP nebo fragmentujte protokoly ingesta MP4 (Smooth Streaming).

## <a name="october-2017-release"></a>Verze z října 2017
> [!IMPORTANT] 
> Media Services je zastaralá podpora klíčů pro ověřování Azure Access Control Service. 22. června 2018 již nebudete moci provádět ověřování pomocí Media Services back-endu prostřednictvím kódu pomocí Access Control Service klíčů. Musíte aktualizovat svůj kód, aby používal Azure Active Directory (Azure AD) pro [ověřování založené na Azure AD](media-services-use-aad-auth-to-access-ams-api.md). Sledujte upozornění na tuto změnu v Azure Portal.

### <a name="updates-for-october-2017"></a>Aktualizace pro říjen 2017
#### <a name="sdks"></a>Sady SDK
* Sada .NET SDK se aktualizovala tak, aby podporovala ověřování Azure AD. Z nejnovější sady .NET SDK na Nuget.org se odebrala podpora pro ověřování Access Control Service, aby se podporovala rychlejší migrace do Azure AD. 
* Sada JAVA SDK byla aktualizována tak, aby podporovala ověřování Azure AD. Do sady Java SDK se přidala podpora pro ověřování Azure AD. Informace o použití sady Java SDK s Media Services naleznete v tématu Začínáme [s klientskou sadou SDK pro Java pro Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Kódování založené na souborech
* Nyní můžete použít kodér Premium ke kódování vašeho obsahu do fotovizuálního kodeku H. 265 videa s vysokým efektivitou (HEVC). Pokud zvolíte H. 265 přes ostatní kodeky, například H. 264, nebudete mít žádný vliv na ceny. Informace o HEVC patentových licencích najdete v tématu [online služby – požadavky](https://azure.microsoft.com/support/legal/).
* Pro zdrojové video, které je kódované pomocí kodeku videa H. 265 (HEVC), jako je video zachycené pomocí iOS11 nebo GoPro Hero 6, můžete teď k kódování těchto videí použít kodér Premium nebo standardní kodér. Informace o licencích pro patenty najdete v tématu [online služby – požadavky](https://azure.microsoft.com/support/legal/).
* U obsahu, který obsahuje zvukové stopy ve více jazycích, musí být hodnoty jazyka správně označeny podle odpovídající specifikace formátu souboru (například ISO MP4). Pak můžete použít standardní kodér ke kódování obsahu pro streamování. Seznam výsledných audio streamování obsahuje dostupné zvukové jazyky.
* Kodér Standard teď podporuje dvě nové předvolby systému jenom pro zvuk, "AAC zvuk" a "AAC dobré kvality zvuku". V uvedeném pořadí vyprodukuje výstup ve formátu AAC (Advanced Audio kódovací) v stereofonních rychlostech 128 kb/s a 192 KB/s.
* Kodér Premium teď jako vstup podporuje formáty souborů QuickTime/MOV. Kodek videa musí být jedním z [typů Apple ProRes uvedených v tomto článku na GitHubu](./media-services-media-encoder-standard-formats.md). Zvuk musí být buď AAC, nebo modul pro kódování Pulse (PCM). Kodér úrovně Premium nepodporuje, například video SOUBĚŽNÁ/DVCPro zabalené do souborů QuickTime/MOV jako vstup. Standardní kodér podporuje tyto Videokodeky.
* V kodérech byly provedeny následující opravy chyb:

    * Nyní můžete odesílat úlohy pomocí vstupního assetu. Po dokončení těchto úloh můžete prostředek upravit (například přidat, odstranit nebo přejmenovat soubory v rámci assetu) a odeslat další úlohy.
    * Zlepšuje se kvalita miniatur snímků JPEG vytvořených nástrojem Standard Encoder.
    * Standard Encoder zpracovává vstupní metadata a generování miniatur lépe ve velmi krátkém videu.
    * Vylepšení dekodéru H. 264 používaného ve standardním kodéru eliminují některé vzácné artefakty. 

#### <a name="media-analytics"></a>Media Analytics
Obecná dostupnost Azure Media Redactor: Tento multimediální procesor provádí anonymitu tím, že rozostří plošky vybraných jednotlivců a je ideální pro použití ve scénářích veřejného zabezpečení a sdělovacích médií. 

Přehled tohoto nového procesoru najdete v [tomto blogovém příspěvku](https://azure.microsoft.com/blog/azure-media-redactor/). Informace o dokumentaci a nastavení najdete v tématu [redigování se Azure Media Analytics](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Verze z června 2017

Media Services teď podporuje [ověřování pomocí Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> V současné době Media Services podporuje model ověřování Access Control Service. Access Control Service autorizaci budou zastaralá od 1. června 2018. Doporučujeme, abyste na model ověřování Azure AD migrovali co nejdříve.

## <a name="march-2017-release"></a>Verze z března 2017

Nyní můžete použít standardní kodér k [automatickému generování žebříku přenosové rychlosti](media-services-autogen-bitrate-ladder-with-mes.md) zadáním přednastaveného řetězce "adaptivního streamování" při vytváření úlohy kódování. Pokud chcete zakódovat video pro streamování s Media Services, použijte přednastavení adaptivního streamování. K přizpůsobení předvolby kódování pro konkrétní scénář můžete začít s [těmito přednastaveními](media-services-mes-presets-overview.md).

Nyní můžete pomocí Media Encoder Standard nebo Media Encoder Premium Workflow [vytvořit úlohu kódování, která generuje bloky dat fMP4](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Verze z února 2017

Od 1. dubna 2017 se automaticky odstraní libovolný záznam úlohy ve vašem účtu, který je starší než 90 dní, spolu s přidruženými záznamy úloh. Odstranění proběhne i v případě, že celkový počet záznamů je nižší než maximální kvóta. K archivaci informací o úloze nebo úkolu můžete použít kód popsaný v tématu [Správa prostředků a souvisejících entit pomocí sady Media Services .NET SDK](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Verze z ledna 2017

V Media Services koncový bod streamování představuje službu streamování, která může doručovat obsah přímo do aplikace klienta v přehrávači nebo do sítě pro doručování obsahu (CDN) pro další distribuci. Media Services taky poskytuje bezproblémovou integraci Azure Content Delivery Network. Odchozí datový proud ze služby StreamingEndpoint může být živý datový proud, video na vyžádání nebo progresivní stažení assetu v účtu Media Services. Každý účet Media Services obsahuje výchozí koncový bod streamování. V rámci účtu můžete vytvořit další koncové body streamování. 

Existují dvě verze koncových bodů streamování, 1,0 a 2,0. Od 10. ledna 2017 budou všechny nově vytvořené Media Services účty zahrnovat výchozí koncový bod streamování verze 2,0. Další koncové body streamování, které přidáte do tohoto účtu, jsou také verze 2,0. Tato změna neovlivní stávající účty. Stávající koncové body streamování jsou verze 1,0 a je možné je upgradovat na verzi 2,0. Tato změna obsahuje chování, účtování a změny funkcí. Další informace najdete v [přehledu koncových bodů streamování](media-services-streaming-endpoints-overview.md).

Počínaje verzí 2,15 Media Services přidali následující vlastnosti entitě koncového bodu streamování:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

Další informace o těchto vlastnostech naleznete v tématu [StreamingEndpoint](/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Verze z prosince 2016

 Nyní můžete použít Media Services pro přístup k datům telemetrie/metrik pro své služby. Pomocí aktuální verze Media Services můžete shromažďovat data telemetrie pro entity streamování, streamování a archivace za provozu. Další informace najdete v tématu [Media Services telemetrie](media-services-telemetry-overview.md).

## <a name="july-2016-release"></a><a name="july_changes16"></a>Verze z července 2016
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Aktualizuje soubor manifestu (*. ISM) generovaných úlohami kódování
Když je odeslána úloha kódování do Media Encoder Standard nebo kodéru Media Encoder Premium, úloha kódování vygeneruje [soubor manifestu streamování](media-services-deliver-content-overview.md) (*. ISM) ve výstupním prostředku. V nejnovější verzi služby se aktualizovala syntaxe tohoto souboru manifestu streamování.

> [!NOTE]
> Syntaxe souboru manifestu streamování (. ISM) je rezervovaná pro interní použití. V budoucích verzích se může změnit. Neupravujte ani nepracuje s obsahem tohoto souboru.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Nový manifest klienta (*. ISMC) se generuje do výstupního prostředku, když úloha kódování provede výstup jednoho nebo více souborů MP4.
Od nejnovější verze služby se po dokončení úlohy kódování, která generuje jeden nebo více souborů MP4, výstupní prostředek obsahuje také soubor manifestu klienta streamování (*. ISMC). Soubor. ISMC pomáhá zlepšit výkon dynamického streamování. 

> [!NOTE]
> Syntaxe souboru manifestu klienta (. ISMC) je vyhrazena pro interní použití. V budoucích verzích se může změnit. Neupravujte ani nepracuje s obsahem tohoto souboru.
> 
> 

Další informace najdete v [tomto blogu](/archive/blogs/randomnumber/encoder-changes-within-azure-media-services-now-create-ismc-file).

### <a name="known-issues"></a>Známé problémy
Někteří klienti můžou přijít do problému se značkou opakování v manifestu Smooth Streaming. Další informace najdete v [této části](media-services-deliver-content-overview.md#known-issues).

## <a name="april-2016-release"></a><a id="apr_changes16"></a>Verze z dubna 2016
### <a name="media-analytics"></a>Media Analytics
 Media Services představena Media Analytics pro výkonnou analýzu videa. Další informace najdete v tématu [Přehled analýzy Media Services](./legacy-components.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (Preview)
Nyní můžete použít Media Services k dynamickému šifrování obsahu HTTP Live Streaming (HLS) pomocí Apple FairPlay. K doručování licencí FairPlay klientům můžete také použít službu doručování licencí Media Services. Další informace najdete v části "použití Azure Media Services k streamování obsahu HLS chráněného technologií Apple FairPlay".

## <a name="february-2016-release"></a><a id="feb_changes16"></a>Verze z února 2016
Nejnovější verze sady Media Services SDK pro .NET (3.5.3) obsahuje opravu chyb týkající se Google Widevine. Nebylo možné znovu použít AssetDeliveryPolicy pro více assetů šifrovaných pomocí Widevine. V rámci této opravy chyby byla do sady SDK přidána následující vlastnost: WidevineBaseLicenseAcquisitionUrl.

```csharp
Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
    new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
{
    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

};
```

## <a name="january-2016-release"></a><a id="jan_changes_16"></a>Verze z ledna 2016
Jednotky rezervované pro kódování byly přejmenovány, aby se zkrátily názvy kodérů.

Jednotky rezervované pro kódování Basic, Standard a Premium se přejmenovaly na rezervované jednotky S1, S2 a S3. Zákazníci, kteří používají jednotky rezervované pro kódování v dnešní době, uvidí jako popisek v Azure Portal (a ve vyúčtování) S1. Zákazníci, kteří používají standard a Premium, uvidí popisky S2 a S3 v uvedeném pořadí. 

## <a name="december-2015-release"></a><a id="dec_changes_15"></a>Verze z prosince 2015

### <a name="media-encoder-deprecation-announcement"></a>Oznámení o zastarání kodéru Media Encoder

 Kodér médií bude zastaralý od verze Media Encoder Standard přibližně o 12 měsíců.

### <a name="azure-sdk-for-php"></a>Azure SDK pro PHP
Tým Azure SDK publikoval novou verzi balíčku [Azure SDK pro php](https://github.com/Azure/azure-sdk-for-php) , který obsahuje aktualizace a nové funkce pro Media Services. Konkrétně sada Media Services SDK for PHP nyní podporuje nejnovější funkce [ochrany obsahu](media-services-content-protection-overview.md) . Tyto funkce jsou dynamické šifrování pomocí AES a DRM (PlayReady a Widevine) s omezeními tokenu a bez něj. Podporuje také škálování [jednotek kódování](media-services-dotnet-encoding-units.md).

Další informace naleznete v tématu:

* Následující [ukázky kódu](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) vám pomůžou rychle začít:
  * **vodworkflow_aes. php**: Tento soubor php ukazuje, jak používat dynamické šifrování aes-128 a službu pro doručování klíčů. Je založený na ukázce .NET, která je vysvětlena v tématu [použití dynamického šifrování AES-128 a služby doručování klíčů](media-services-protect-with-aes128.md).
  * **vodworkflow_aes. php**: Tento soubor php ukazuje, jak používat dynamické šifrování PlayReady a službu doručování licencí. Je založený na ukázce .NET, která je vysvětlena v tématu [použití dynamického společného šifrování PlayReady nebo Widevine](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units. php**: Tento soubor php ukazuje, jak škálovat rezervované jednotky kódování.

## <a name="november-2015-release"></a><a id="nov_changes_15"></a>Verze z listopadu 2015
 Media Services teď nabízí službu pro doručování licencí Widevine v cloudu. Další informace najdete v [tomto blogu](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Podívejte se také na [Tento kurz](media-services-protect-with-playready-widevine.md) a [úložiště GitHub](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Služby Widevine License Delivery Services, které poskytuje Media Services, jsou ve verzi Preview. Další informace najdete v [tomto blogu](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a name="october-2015-release"></a><a id="oct_changes_15"></a>Verze z října 2015
Media Services je teď živý v následujících datových centrech: Brazílie – jih, Indie – západ, Indie – jih a Indie – střed. Nyní můžete použít Azure Portal k [Vytvoření účtů mediálních služeb](media-services-portal-create-account.md) a provádění různých úloh popsaných na [webové stránce dokumentace Media Services](https://azure.microsoft.com/documentation/services/media-services/). Live Encoding v těchto datových centrech není povolená. Dále nejsou v těchto datových centrech k dispozici všechny typy jednotek rezervovaných pro kódování.

* Brazílie – jih: k dispozici jsou jenom jednotky rezervované pro kódování úrovně Standard a Basic.
* Indie – západ, Indie – jih a Indie – střed: k dispozici jsou jenom základní jednotky rezervované pro kódování.

## <a name="september-2015-release"></a><a id="september_changes_15"></a>Verze září 2015
Media Services teď nabízí možnost chránit jak video na vyžádání, tak živé streamy pomocí modulární technologie DRM pro Widevine. Pomocí následujících partnerů pro doručování služeb vám pomůžete doručovat licence Widevine:
* [Axinom](https://www.axinom.com) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Další informace najdete v [tomto blogu](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Konfiguraci zásady AssetDeliveryConfiguration na používání technologie Widevine můžete provést pomocí sady [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (počínaje verzí 3.5.1) nebo rozhraní REST API. 
* Media Services přidat podporu pro videa Apple ProRes. Teď můžete nahrávat své zdrojové videosoubory QuickTime, které používají Apple ProRes nebo jiné kodeky. Další informace najdete v [tomto blogu](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Nyní můžete použít Media Encoder Standard k provedení extrakce dílčího oříznutí a archivu za provozu. Další informace najdete v [tomto blogu](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Byly provedeny následující aktualizace filtrování: 
  
  * Ve formátu Apple HLS teď můžete použít filtr jenom pro zvuk. Pomocí této aktualizace můžete v adrese URL odebrat pouze zvukové stopy zadáním (pouze zvuk = false).
  * Když definujete filtry pro své prostředky, můžete v jedné adrese URL zkombinovat více filtrů (až tří).
    
    Další informace najdete v [tomto blogu](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Media Services teď podporuje I-snímky ve verzi HLS 4. Podpora I-snímků optimalizuje operace rychlého převíjení vpřed a vzad. Ve výchozím nastavení obsahují všechny výstupy HLS verze 4 I seznam testů v rámci rámce (EXT-X-I-FRAME-STREAM-INF).
Další informace najdete v [tomto blogu](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="august-2015-release"></a><a id="august_changes_15"></a>Verze z srpna 2015
* K dispozici je teď sada Media Services SDK pro 0.8.0 verze Java a nové ukázky. Další informace naleznete v tématu:
    
* Azure Media Player byla aktualizována s podporou více audio streamu. Další informace najdete v [tomto blogovém příspěvku](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a name="july-2015-release"></a><a id="july_changes_15"></a>Verze z července 2015
* Byla oznámena Obecná dostupnost Media Encoder Standard. Další informace najdete v [tomto blogovém příspěvku](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard používá přednastavení, jak je popsáno v [této části](./media-services-mes-presets-overview.md). Pokud použijete přednastavení pro kódování 4K, Získejte typ rezervované jednotky Premium. Další informace najdete v tématu [škálování v kódování](media-services-scale-media-processing-overview.md).
* Živé titulky v reálném čase byly použity s Media Services a Media Player. Další informace najdete v [tomto blogovém příspěvku](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Aktualizace Media Services .NET SDK
Sada Media Services .NET SDK je teď verze 3.4.0.0. Byly provedeny následující aktualizace: 

* Pro živý archiv se implementovala podpora. Nemůžete stáhnout Asset, který obsahuje živý archiv.
* Pro dynamické filtry se implementovala podpora.
* Je implementovaná funkčnost, aby si uživatelé mohli při odstraňování assetu zachovat kontejner úložiště.
* Byly provedeny opravy chyb související se zásadami opakování v kanálech.
* Media Encoder Premium Workflow byla povolena.

## <a name="june-2015-release"></a><a id="june_changes_15"></a>Verze z června 2015
### <a name="media-services-net-sdk-updates"></a>Aktualizace Media Services .NET SDK
Sada Media Services .NET SDK je teď verze 3.3.0.0. Byly provedeny následující aktualizace: 

* Byla přidána podpora specifikace zjišťování OpenId Connect.
* Byla přidána podpora pro zpracování klíčů na straně poskytovatele identity.

Pokud používáte poskytovatele identity, který zveřejňuje dokument zjišťování OpenID Connect (jako Azure AD, Google a Salesforce), můžete dát Media Services k získání podpisových klíčů pro ověření webových tokenů JSON (JWTs) ze specifikace zjišťování OpenID Connect. 

Další informace najdete v tématu [použití webových klíčů JSON ze specifikace zjišťování OpenID Connect pro práci s ověřováním JWT v Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a name="may-2015-release"></a><a id="may_changes_15"></a>Vydání verze květen 2015
Byly oznámeny následující nové funkce:

* [Náhled živého kódování pomocí Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Dynamický manifest](media-services-dynamic-manifest-overview.md)

## <a name="april-2015-release"></a><a id="april_changes_15"></a>Verze z dubna 2015
### <a name="general-media-services-updates"></a>Obecné aktualizace Media Services
* [Media Playera](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) byla oznámena.
* Od Media Services REST 2,10 jsou kanály, které jsou nakonfigurované tak, aby ingestování protokolu Real-Time Messaging Protocol (RTMP) vytvořené pomocí primárních a sekundárních adres URL pro ingestování. Další informace najdete v tématu [konfigurace kanálů](media-services-live-streaming-with-onprem-encoders.md#channel_input)pro ingestování.
* Azure Media Indexer byla aktualizována.
* Byla přidána podpora španělského jazyka.
* Byla přidána nová konfigurace pro formát XML.

Další informace najdete v [tomto blogu](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Aktualizace Media Services .NET SDK
Sada Media Services .NET SDK je teď verze 3.2.0.0. Byly provedeny následující aktualizace:

* Zásadní změna: TokenRestrictionTemplate. Issuer a TokenRestrictionTemplate. cílová skupina byla změněna tak, aby byla typu String.
* V souvislosti s vytvářením vlastních zásad opakování byly provedeny aktualizace.
* Byly provedeny opravy chyb související s nahráváním a stahováním souborů.
* Třída MediaServicesCredentials nyní přijímá primární a sekundární koncové body řízení přístupu k ověřování proti.

## <a name="march-2015-release"></a><a id="march_changes_15"></a>Verze z března 2015
### <a name="general-media-services-updates"></a>Obecné aktualizace Media Services
* Media Services nyní poskytuje Content Delivery Network integraci. Pro podporu integrace byla do StreamingEndpoint přidána vlastnost CdnEnabled. CdnEnabled se dá použít s rozhraními REST API počínaje verzí 2,9. Další informace najdete v tématu [StreamingEndpoint](/rest/api/media/operations/streamingendpoint). CdnEnabled se dá použít se sadou .NET SDK počínaje verzí 3.1.0.2. Další informace najdete v tématu [StreamingEndpoint](/archive/blogs/randomnumber/encoder-changes-within-azure-media-services-now-create-ismc-file).
* Media Encoder Premium Workflow byla oznámena. Další informace najdete v tématu [představení služby Premium Encoding v Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a name="february-2015-release"></a><a id="february_changes_15"></a>Verze z února 2015
### <a name="general-media-services-updates"></a>Obecné aktualizace Media Services
REST API Media Services je teď verze 2,9. Od této verze můžete povolit integraci Content Delivery Network s koncovými body streamování. Další informace najdete v tématu [StreamingEndpoint](/rest/api/media/operations/streamingendpoint).

## <a name="january-2015-release"></a><a id="january_changes_15"></a>Verze z ledna 2015
### <a name="general-media-services-updates"></a>Obecné aktualizace Media Services
Byla oznámena Obecná dostupnost ochrany obsahu s dynamickým šifrováním. Další informace najdete v tématu [Media Services vylepšuje zabezpečení streamování pomocí všeobecné dostupnosti technologie DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Aktualizace Media Services .NET SDK
Sada Media Services .NET SDK je teď verze 3.1.0.1.

Tato verze označila výchozí konstruktor Microsoft. WindowsAzure. MediaServices. Client. ContentKeyAuthorization. TokenRestrictionTemplate jako zastaralou. Nový konstruktor přijímá jako argument typ TokenType.

```csharp
TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
```


## <a name="december-2014-release"></a><a id="december_changes_14"></a>Verze z prosince 2014
### <a name="general-media-services-updates"></a>Obecné aktualizace Media Services
* Některé aktualizace a nové funkce byly přidány do Media Indexer. Další informace najdete v [poznámkách k verzi Azure Media Indexer verze 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Byl přidán nový REST API, který můžete použít k aktualizaci jednotek rezervovaných pro kódování. Další informace najdete v tématu [EncodingReservedUnitType with REST](/rest/api/media/operations/encodingreservedunittype).
* Byla přidána podpora CORS pro službu doručování klíčů.
* Bylo provedeno vylepšení výkonu pro dotazování na možnosti zásad autorizace.
* V čínském datovém centru je [Adresa URL pro doručení klíčů](/rest/api/media/operations/contentkey#get_delivery_service_url) teď vázaná na zákazníka (stejně jako v jiných datových centrech).
* Byla přidána doba trvání automatického cíle HLS. Při živém streamování je HLS vždy zabalen dynamicky. Ve výchozím nastavení Media Services automaticky vypočítá poměr segmentace segmentu HLS (FragmentsPerSegment) na základě intervalu klíčových snímků (KeyFrameInterval). Tato metoda je také označována jako skupina obrázků (skupinu GOP), která je přijímána z kodéru Live Encoder. Další informace najdete v tématu [práce s Media Services živým streamem](/previous-versions/azure/dn783466(v=azure.100)).

### <a name="media-services-net-sdk-updates"></a>Aktualizace Media Services .NET SDK
[Sada Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) je teď verze 3.1.0.0. Byly provedeny následující aktualizace:

* Závislost rozhraní .NET SDK byla upgradována na rozhraní .NET 4,5.
* Bylo přidáno nové rozhraní API, které můžete použít k aktualizaci rezervovaných jednotek kódování. Další informace najdete v tématu [aktualizace typu rezervované jednotky a zvětšení jednotek rezervovaných pro kódování pomocí .NET](media-services-dotnet-encoding-units.md).
* Přidala se podpora JWT pro ověřování tokenu. Další informace najdete v tématu [ověřování tokenu JWT v Media Services a dynamické šifrování](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* V šabloně licence PlayReady se přidaly relativní posuny pro BeginDate a ExpirationDate.

## <a name="november-2014-release"></a><a id="november_changes_14"></a>Verze z listopadu 2014
* Nyní můžete použít Media Services k ingestování živého obsahu Smooth Streaming (fMP4) prostřednictvím připojení TLS. Pokud chcete ingestovat přes TLS, nezapomeňte aktualizovat adresu URL ingestování na HTTPS. V současné době Media Services nepodporuje TLS s vlastními doménami. Další informace o živém streamování najdete v tématu [práce s Azure Media Services živým streamem](/previous-versions/azure/dn783466(v=azure.100)).
* V současné době nemůžete ingestovat živý stream RTMP přes připojení TLS.
* Přes protokol TLS můžete streamovat pouze v případě, že koncový bod streamování, ze kterého dodáváte obsah, byl vytvořen po 10. září 2014. Pokud jsou vaše adresy URL streamování založené na koncových bodech streamování vytvořených po 10. září 2014, adresa URL obsahuje "streaming.mediaservices.windows.net" (nový formát). Adresy URL streamování obsahující "origin.mediaservices.windows.net" (starý formát) nepodporují protokol TLS. Pokud má vaše adresa URL ve starém formátu a chcete streamovat přes TLS, [vytvořte nový koncový bod streamování](media-services-portal-manage-streaming-endpoints.md). Pokud chcete streamovat obsah přes TLS, použijte adresy URL na základě nového koncového bodu streamování.

### <a name="media-services-net-sdk"></a><a id="oct_sdk"></a>Sada Media Services .NET SDK
Rozhraní Media Services SDK for .NET Extensions je teď verze 2.0.0.3.

Sada Media Services SDK pro .NET je teď verze 3.0.0.8. Byly provedeny následující aktualizace:

* Refaktoring byl implementován v třídách zásad opakování.
* Do hlaviček požadavku HTTP se přidal řetězec uživatelského agenta.
* Byl přidán krok sestavení NuGet pro obnovení.
* Testy scénáře byly vyřešeny, aby používaly certifikát x509 z úložiště.
* Nastavení ověřování bylo přidáno pro čas, kdy se kanál a konec aktualizace streamování ukončí.

### <a name="new-github-repository-to-host-media-services-samples"></a>Nové úložiště GitHubu pro hostování ukázek Media Services
Ukázky jsou k [disMedia Services v úložišti GitHub Samples](https://github.com/Azure/Azure-Media-Services-Samples).

## <a name="september-2014-release"></a><a id="september_changes_14"></a>Verze září 2014
Metadata Media Services REST jsou nyní verze 2,7. Další informace o nejnovějších aktualizacích REST najdete v referenčních informacích k [Media Services REST API](/rest/api/media/operations/azure-media-services-rest-api-reference).

Sada Media Services SDK pro .NET je teď verze 3.0.0.7

### <a name="breaking-changes"></a><a id="sept_14_breaking_changes"></a>Změny způsobující chyby
* Počátek byl přejmenován na [StreamingEndpoint].
* Při použití Azure Portal ke kódování a publikování souborů MP4 byla provedena změna ve výchozím chování.

### <a name="new-featuresscenarios-that-are-part-of-the-general-availability-release"></a><a id="sept_14_GA_changes"></a>Nové funkce/scénáře, které jsou součástí vydání obecné dostupnosti
* Byl zaveden procesor Media Indexer Media. Další informace najdete v tématu [indexování mediálních souborů pomocí Media Indexer](/previous-versions/azure/dn783455(v=azure.100)).
* Pomocí entity [StreamingEndpoint] můžete přidat vlastní názvy domén (hostitelů).
  
    Pokud chcete jako název koncového bodu streamování Media Services použít vlastní název domény, přidejte vlastní názvy hostitelů do svého koncového bodu streamování. K přidání vlastních názvů hostitelů použijte Media Services rozhraní REST API nebo sadu .NET SDK.
  
    Platí následující důležité informace:
  
  * Musíte mít vlastnictví vlastního názvu domény.
  * Vlastnictví názvu domény musí být ověřeno pomocí Media Services. Pokud chcete ověřit doménu, vytvořte záznam CName, který mapuje nadřazenou doménu MediaServicesAccountId a ověří DNS MediaServices-DNS-Zone.
  * Je nutné vytvořit jiný záznam CName, který mapuje název vlastního hostitele (například sports.contoso.com) na název hostitele StreamingEndpoint pro Media Services (například amstest.streaming.mediaservices.windows.net).

    Další informace naleznete v tématu vlastnost CustomHostNames v článku [StreamingEndpoint](/rest/api/media/operations/streamingendpoint) .

### <a name="new-featuresscenarios-that-are-part-of-the-public-preview-release"></a><a id="sept_14_preview_changes"></a>Nové funkce/scénáře, které jsou součástí verze Public Preview
* Živý stream ve verzi Preview. Další informace najdete v tématu [práce s Media Services živým streamem](/previous-versions/azure/dn783466(v=azure.100)).
* Služba doručení klíčů. Další informace najdete v tématu [použití dynamického šifrování AES-128 a služby doručování klíčů](/previous-versions/azure/dn783457(v=azure.100)).
* Dynamické šifrování AES Další informace najdete v tématu [použití dynamického šifrování AES-128 a služby doručování klíčů](/previous-versions/azure/dn783457(v=azure.100)).
* Služba pro doručování licencí PlayReady. 
* Dynamické šifrování PlayReady 
* Media Services šablonou licence PlayReady. Další informace najdete v tématu [Přehled šablon licencí PlayReady pro Media Services].
* Streamování – šifrované prostředky Další informace najdete v článku o [obsahu zašifrovaném úložištěm streamování](/previous-versions/azure/dn783451(v=azure.100)).

## <a name="august-2014-release"></a><a id="august_changes_14"></a>Verze z srpna 2014
Při kódování assetu se vytvoří výstupní Asset, když se úloha kódování dokončí. Do této verze Media Services kodér vytvořil metadata o výstupních prostředcích. Od této verze kodér také generuje metadata o vstupních prostředcích. Další informace najdete v tématu [vstupní metadata] a [výstupní metadata].

## <a name="july-2014-release"></a><a id="july_changes_14"></a>Verze z července 2014
Pro balíček Azure Media Services a modul pro šifrování byly provedeny následující opravy chyb:

* Po přenosu živého archivu do HLS se přehrává jenom zvuk: Tento problém byl opravený a zvuk i video můžou přehrát.
* Když se Asset zabalí do HLS a AES 128-bit obálky, nezabalené proudy se na zařízení s Androidem Nepřehrávat: Tato chyba byla opravena a zabalený datový proud přehrává na zařízeních s Androidem, která podporují HLS.

## <a name="may-2014-release"></a><a id="may_changes_14"></a>Vydání verze květen 2014
### <a name="general-media-services-updates"></a><a id="may_14_changes"></a>Obecné aktualizace Media Services
Nyní můžete použít [dynamické balení] ke streamování HLS verze 3. Pokud chcete streamovat HLS verze 3, přidejte následující formát do cesty k umístění zdroje: *. ISM/manifest (Format = M3U8-AAPL-V3). Další informace najdete v [tomto fóru](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

Dynamické balení teď také podporuje doručování HLS (verze 3 a verze 4) zašifrované pomocí PlayReady na základě Smooth Streaming staticky šifrovaných pomocí PlayReady. Informace o tom, jak šifrovat Smooth Streaming pomocí PlayReady, najdete v tématu [ochrana Smooth Streaming pomocí PlayReady](/previous-versions/azure/dn189154(v=azure.100)).

### <a name="media-services-net-sdk-updates"></a><a name="may_14_donnet_changes"></a>Aktualizace Media Services .NET SDK
Sada Media Services .NET SDK je teď verze 3.0.0.5. Byly provedeny následující aktualizace:

* Rychlost a odolnost jsou lepší při nahrávání a stahování mediálních prostředků.
* Byla provedena vylepšení logiky opakování a přechodného zpracování výjimek: 
  
  * Byla vylepšena detekce přechodných chyb a logika opakování pro výjimky, které jsou způsobeny při dotazování, ukládání změn a nahrávání nebo stahování souborů. 
  * Když získáte výjimky webu (například během žádosti o Access Control Service tokenu), závažné chyby se teď rychleji nezdařily.

Další informace najdete v tématu [logika opakování v sadě Media Services SDK pro .NET].

## <a name="januaryfebruary-2014-releases"></a><a id="jan_feb_changes_14"></a>Verze z ledna 2014. února
### <a name="media-services-net-sdk-3001-3002-and-3003"></a><a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1, 3.0.0.2 a 3.0.0.3
Změny v 3.0.0.1 a 3.0.0.2 zahrnují:

* Byly opraveny problémy související s používáním dotazů LINQ s příkazy OrderBy.
* Testovací řešení v [GitHubu] byla rozdělena na testy jednotek a testy založené na scénářích.

Další informace o těchto změnách naleznete v tématu [verze Media Services .NET SDK 3.0.0.1 a 3.0.0.2](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html).

Ve verzi 3.0.0.3 byly provedeny následující změny:

* Závislosti Azure Storage se upgradují na použití verze 3.0.3.0.
* Pro 3,0 byl opraven problém zpětné kompatibility. *.* Tool.

## <a name="december-2013-release"></a><a id="december_changes_13"></a>Verze z prosince 2013
### <a name="media-services-net-sdk-3000"></a><a name="dec_13_donnet_changes"></a>Sada Media Services .NET SDK 3.0.0.0
> [!NOTE]
> Verze 3.0. x. x nejsou zpětně kompatibilní s verzemi 2.4. x. x.
> 
> 

Nejnovější verze sady Media Services SDK je teď 3.0.0.0. Nejnovější balíček si můžete stáhnout z NuGet nebo získat bity z [GitHubu].

Počínaje verzí Media Services SDK 3.0.0.0 můžete znovu použít tokeny [Access Control Service Azure AD](/previous-versions/azure/azure-services/hh147631(v=azure.100)) . Další informace najdete v části "opakované použití Access Control Service tokeny" v tématu [připojení k Media Services pomocí sady Media Services SDK pro .NET](/previous-versions/azure/jj129571(v=azure.100)).

### <a name="media-services-net-sdk-extensions-2000"></a><a name="dec_13_donnet_ext_changes"></a>Rozšíření Media Services .NET SDK 2.0.0.0
 Rozšíření Media Services .NET SDK jsou sada rozšiřujících metod a pomocných funkcí, které zjednodušují váš kód a usnadňují vývoj pomocí Media Services. Můžete získat nejnovější bity z [rozšíření Media Services .NET SDK](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a name="november-2013-release"></a><a id="november_changes_13"></a>Verze z listopadu 2013
### <a name="media-services-net-sdk-changes"></a><a name="nov_13_donnet_changes"></a>Změny Media Services .NET SDK
Od této verze sada Media Services SDK pro .NET zpracovává přechodné chyby při volání, které mohou nastat při volání Media Services REST API vrstvě.

## <a name="august-2013-release"></a><a id="august_changes_13"></a>Verze z srpna 2013
### <a name="media-services-powershell-cmdlets-included-in-azure-sdk-tools"></a><a name="aug_13_powershell_changes"></a>Rutiny Media Services PowerShellu zahrnuté v nástrojích Azure SDK
V [nástrojích Azure SDK](https://github.com/Azure/azure-sdk-tools)jsou teď zahrnuté následující Media Services rutiny PowerShellu:

* Get-AzureMediaServices 

    Příklad: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Příklad: `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`
* New-AzureMediaServicesKey 
  
    Příklad: `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Příklad: `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`

## <a name="june-2013-release"></a><a id="june_changes_13"></a>Verze z června 2013
### <a name="media-services-changes"></a><a name="june_13_general_changes"></a>Media Services změny
V této části jsou uvedené aktualizace, které jsou zahrnuté v 2013 Media Services verzích od června:

* Možnost propojit více účtů úložiště s účtem služby Media Service. 
    * StorageAccount
    * Asset. StorageAccountName a Asset. StorageAccount
* Možnost aktualizace úlohy. Priorita 
* Entity a vlastnosti související s oznámením: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Úloha
* Asset. URI 
* Locator.Name 

### <a name="media-services-net-sdk-changes"></a><a name="june_13_dotnet_changes"></a>Změny Media Services .NET SDK
V červnu 2013 Media Services vydání sady SDK jsou zahrnuty následující změny. Nejnovější Sada Media Services SDK je k dispozici na GitHubu.

* Počínaje verzí 2.3.0.0 podporuje Media Services SDK propojení několika účtů úložiště k účtu Media Services. Tuto funkci podporují následující rozhraní API:
  
    * Typ IStorageAccount
    * Vlastnost Microsoft. WindowsAzure. MediaServices. Client. CloudMediaContext. StorageAccounts
    * Vlastnost StorageAccount
    * Vlastnost StorageAccountName
  
      Další informace najdete v tématu [správa Media Servicesch prostředků napříč několika účty úložiště](/previous-versions/azure/dn271889(v=azure.100)).
* Rozhraní API související s oznámeními. Počínaje verzí 2.2.0.0 můžete naslouchat oznámením ve službě Azure Queue Storage. Další informace najdete v tématu [zpracování oznámení úloh Media Services](/previous-versions/azure/dn261241(v=azure.100)).
  
    * Vlastnost Microsoft. WindowsAzure. MediaServices. Client. IJob. JobNotificationSubscriptions
    * Typ Microsoft. WindowsAzure. MediaServices. Client. INotificationEndPoint
    * Typ Microsoft. WindowsAzure. MediaServices. Client. IJobNotificationSubscription
    * Typ Microsoft. WindowsAzure. MediaServices. Client. NotificationEndPointCollection
    * Typ Microsoft. WindowsAzure. MediaServices. Client. NotificationEndPointType
* Závislost na sadě SDK klienta úložiště 2,0 (Microsoft.WindowsAzure.StorageClient.dll)
* Závislost na OData 5,5 (Microsoft.Data.OData.dll)

## <a name="december-2012-release"></a><a id="december_changes_12"></a>Verze z prosince 2012
### <a name="media-services-net-sdk-changes"></a><a name="dec_12_dotnet_changes"></a>Změny Media Services .NET SDK
* IntelliSense: nebyla přidána dokumentace technologie IntelliSense pro mnoho typů.
* Microsoft. Practices. TransientFaultHandling. Core: byl opraven problém, kde sada SDK stále obsahuje závislost na starou verzi tohoto sestavení. SDK teď odkazuje na verzi 5.1.1209.1 tohoto sestavení.

Opravy pro problémy nalezené v listopadu 2012 SDK:

* IAsset. Locators. Count: Tento počet je nyní správně nahlášen na nových rozhraních IAsset po odstranění všech lokátorů.
* IAssetFile. ContentFileSize: Tato hodnota je nyní správně nastavena po odeslání pomocí IAssetFile. upload (FilePath).
* IAssetFile. ContentFileSize: Tato vlastnost se teď dá nastavit při vytváření souboru prostředků. Bylo dřív jen pro čtení.
* IAssetFile. upload (FilePath): byl opraven problém, kdy byla tato synchronní metoda nahrána při odeslání více souborů do assetu aktivována následující chyba. Došlo k chybě: Server nemohl ověřit požadavek. Ujistěte se, že hodnota hlavičky autorizace je správně vytvořená, včetně signatury. "
* IAssetFile. UploadAsync: byl opraven problém, který omezil současné nahrávání souborů na pět souborů.
* IAssetFile. UploadProgressChanged: tuto událost teď poskytuje sada SDK.
* IAssetFile. DownloadAsync (String, BlobTransferClient, ILocator, CancellationToken): Toto přetížení metody je nyní k dispozici.
* IAssetFile. DownloadAsync: byl opraven problém, který omezil současné stahování souborů na pět souborů.
* IAssetFile. Delete (): byl opraven problém, kde volání Delete může vyvolat výjimku, pokud nebyl nahrán žádný soubor pro IAssetFile.
* Úlohy: byl opraven problém, který řetězuje úlohu MP4 k vyhlazení datových proudů s "úlohou ochrany pomocí úlohy PlayReady" pomocí šablony úlohy, ve které se nevytvořily žádné úkoly.
* EncryptionUtils. GetCertificateFromStore (): Tato metoda již nevyvolává výjimku odkazu s hodnotou null z důvodu chyby při hledání certifikátu na základě potíží s konfigurací certifikátů.

## <a name="november-2012-release"></a><a id="november_changes_12"></a>Verze z listopadu 2012
Změny uvedené v této části byly aktualizace obsažené v sadě SDK listopadu 2012 (verze 2.0.0.0). Tyto změny mohou vyžadovat úpravu nebo přepsání verze napsané pro vydání sady SDK z června 2012 Preview.

* Prostředky
  
    * IAsset. Create (Asset) je *jediná* funkce vytváření assetů. IAsset. Create již neodesílá soubory jako součást volání metody. Použijte IAssetFile pro nahrávání.
    * Metoda IAsset. Publish a hodnota výčtu AssetState. Publish se odebrala ze sady SDK služeb. Veškerý kód, který spoléhá na tuto hodnotu, musí být přepsán.
* FileInfo
  
    * Tato třída byla odebrána a nahrazena třídou IAssetFile.
  
* IAssetFiles
  
    * IAssetFile nahrazuje FileInfo a má jiné chování. Pokud ho chcete použít, vytvořte instanci objektu IAssetFiles následovaný nahráním souboru, a to pomocí sady SDK Media Services nebo sady SDK pro úložiště. Je možné použít následující přetížení IAssetFile. upload:
  
        * IAssetFile. upload (filePath): Tato synchronní metoda blokuje vlákno a doporučujeme ji použít pouze při nahrávání jediného souboru.
        * IAssetFile. UploadAsync (filePath, blobTransferClient, lokátor, cancellationToken): Tato asynchronní metoda je preferovaným mechanismem nahrávání. 
    
            Známá chyba: Pokud použijete token zrušení, nahrávání se zruší. Úlohy mohou mít mnoho stavů zrušení. Je nutné správně zachytit a zpracovat výjimky.
* Lokátory
  
    * Verze specifické pro původní původce byly odebrány. Kontext specifický pro SAS. Lokátory. CreateSasLocator (Asset, accessPolicy) bude označena jako zastaralá nebo odebraná obecnou dostupností. Aktualizované chování najdete v části "Lokátory" v části "nové funkce".

## <a name="june-2012-preview-release"></a><a id="june_changes_12"></a>Vydání z června 2012 Preview
Následující funkce byly v listopadu vydání sady SDK nové:

* Odstraňují se entity
  
    * Objekty IAsset, IAssetFile, ILocator, IAccessPolicy a IContentKey jsou nyní odstraněny na úrovni objektu, to znamená IObject. Delete () namísto vyžadování odstranění v kolekci, to znamená cloudMediaContext. ObjCollection. Delete (objInstance).
* Lokátory
  
    * Lokátory je teď potřeba vytvořit pomocí metody CreateLocator. Musí používat hodnoty výčtu LocatorType. SAS nebo LocatorType. OnDemandOrigin jako argument pro konkrétní typ lokátoru, který chcete vytvořit.
    * Do lokátorů se přidaly nové vlastnosti, které usnadňují získání použitelných identifikátorů URI pro váš obsah. Tato změna návrhu lokátorů poskytuje větší flexibilitu pro budoucí rozšiřitelnost třetí strany a zvyšuje snadné použití pro klientské aplikace pro média.
* Podpora asynchronních metod
  
    * Do všech metod se přidala asynchronní podpora.

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Microsoft Q&A question page for Azure Media Services]: /answers/topics/azure-media-services.html
[Odkaz na Azure Media Services REST API]: /rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[Vstupní metadata]: ./media-services-input-metadata-schema.md
[Výstupní metadata]: ./media-services-output-metadata-schema.md
[Deliver content]: /previous-versions/azure/hh973618(v=azure.100)
[Index media files with the Azure Media Indexer]: /previous-versions/azure/dn783455(v=azure.100)
[StreamingEndpoint]: /rest/api/media/operations/streamingendpoint
[Work with Media Services live streaming]: /previous-versions/azure/dn783466(v=azure.100)
[Use AES-128 dynamic encryption and the key delivery service]: /previous-versions/azure/dn783457(v=azure.100)
[Use PlayReady dynamic encryption and the license delivery service]: /previous-versions/azure/dn783467(v=azure.100)
[Preview features]: https://azure.microsoft.com/services/preview/
[Přehled šablon licencí PlayReady Media Services]: /previous-versions/azure/dn783459(v=azure.100)
[Stream storage-encrypted content]: /previous-versions/azure/dn783451(v=azure.100)
[Azure portal]: https://portal.azure.com
[Dynamické balení]: /previous-versions/azure/jj889436(v=azure.100)
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: /previous-versions/azure/dn189154(v=azure.100)
[Logika opakování v sadě Media Services SDK pro .NET]: ./media-services-retry-logic-in-dotnet-sdk.md
[Grass Valley announces EDIUS 7 streaming through the cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: /previous-versions/azure/dn303341(v=azure.100)
[Create overlays]: /previous-versions/azure/dn640496(v=azure.100)
[Stitch video segments]: /previous-versions/azure/dn640504(v=azure.100)
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: /previous-versions/azure/azure-services/hh147631(v=azure.100)
[Connect to Media Services with the Media Services SDK for .NET]: /previous-versions/azure/jj129571(v=azure.100)
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: /previous-versions/azure/dn271889(v=azure.100)
[Handle Media Services job notifications]: /previous-versions/azure/dn261241(v=azure.100)