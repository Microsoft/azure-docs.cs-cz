---
title: Signály popisné zvukové stopy s Media Services V3
description: Podle kroků v tomto kurzu nahrajte soubor, zakódovat video, přidejte popisné zvukové stopy a Streamujte obsah pomocí Media Services V3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 01854e7636f53ec1faab157b51cc84f3539582b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104596718"
---
# <a name="signal-descriptive-audio-tracks"></a>Signály popisné zvukové stopy

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Do svého videa můžete přidat záznam mluveného komentáře, který umožní vizuálně postiženým klientům sledovat záznam videa tím, že naslouchá mluveným komentářům. V Media Services V3 budete signalizovat popisné zvukové stopy tím, že budete opatřovat zvukovou stopu v souboru manifestu.

Tento článek ukazuje, jak zakódovat video, nahrát zvukový soubor MP4 (kodek AAC) obsahující popisný zvuk do výstupního prostředku a upravit soubor. ISM tak, aby obsahoval popisný zvuk.

## <a name="prerequisites"></a>Požadavky

- [Vytvořte účet Media Services](./create-account-howto.md).
- Postupujte podle kroků v [části přístup k rozhraní API Azure Media Services pomocí Azure CLI](./access-api-howto.md) a přihlašovací údaje uložte. Budete je muset použít pro přístup k rozhraní API.
- Zkontrolujte [dynamické balení](dynamic-packaging-overview.md).
- Projděte si kurz [nahrávání, kódování a streamování videí](stream-files-tutorial-with-api.md) .

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Vytvoření vstupního prostředku a nahrání místního souboru do tohoto prostředku 

Funkce **CreateInputAsset** vytvoří nový vstupní [Asset](/rest/api/media/assets) a nahraje zadaný místní videosoubor do souboru. Tento **prostředek** se používá jako vstup do vaší úlohy kódování. V Media Services V3 může být vstupem do **úlohy** buď **Asset**, nebo může to být obsah, který zpřístupníte pro váš Media Services účet prostřednictvím adres URL protokolu HTTPS. 

Pokud se chcete dozvědět, jak kódovat z adresy URL HTTPS, přečtěte si [Tento článek](job-input-from-http-how-to.md) .  

Ve službě Media Services v3 slouží k nahrání souborů rozhraní API služby Azure Storage. Následující fragment kódu .NET vám ukáže, jak na to.

Uvedená funkce provede následující akce:

* Vytvoří **Asset** . 
* Získá zapisovatelnou [adresu URL SAS](../../storage/common/storage-sas-overview.md) kontejneru assetu [v úložišti](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container) .
* Přes adresu SAS odešle soubor do kontejneru v úložišti.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Pokud potřebujete předat název vytvořeného vstupního prostředku jiným metodám, nezapomeňte použít `Name` vlastnost u objektu assetu vráceného z `CreateInputAssetAsync` , například inputAsset.Name. 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>Vytvořte výstupní prostředek pro uložení výsledku úlohy kódování.

Výstupní [Asset](/rest/api/media/assets) ukládá výsledek vaší úlohy kódování. Následující funkce ukazuje, jak vytvořit výstupní Asset.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Pokud potřebujete předat název vytvořeného výstupního prostředku jiným metodám, nezapomeňte použít `Name` vlastnost u objektu assetu vráceného z `CreateIOutputAssetAsync` , například outputAsset.Name. 

V případě tohoto článku předejte `outputAsset.Name` hodnotu `SubmitJobAsync` `UploadAudioIntoOutputAsset` funkcím a.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Vytvořit transformaci a úlohu, která zakóduje nahraný soubor

Když kódujete nebo zpracováváte obsah v Media Services, kódování se obvykle nastaví jako předpis. Potom stačí odeslat **Úlohu**, která tento předpis použije pro video. Odesláním nových úloh pro každé nové video použijete tento recept na všechna videa v knihovně. V Media Services se pro předpis používá označení **transformace**. Další informace najdete v tématu [Transformace a úlohy](./transforms-jobs-concept.md). Ukázka popsaná v tomto kurzu definuje předpis, který zakóduje video tak, aby se dalo streamovat na nejrůznějších zařízeních s iOSem a Androidem. 

Následující příklad vytvoří transformaci (Pokud neexistuje).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

Následující funkce odešle úlohu.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Čekání na dokončení úlohy

Úloze chvíli trvá, než se dokončí, a když k tomu dojde, budete na to pravděpodobně chtít upozornit. Pro čekání na dokončení úlohy doporučujeme použít Event Grid.

Úloha obvykle prochází následujícími stavy: **naplánované**, **zařazeno do fronty**, **zpracování**, **dokončeno** (konečný stav). Pokud během provádění úlohy dojde k chybě, přejde úloha do stavu **Chyba**. Když úlohu zrušíte, změní se její stav na **Rušení** a potom na **Zrušeno**.

Další informace najdete v tématu [zpracování událostí Event Grid](monitoring/reacting-to-media-services-events.md).

## <a name="upload-the-audio-only-mp4-file"></a>Nahrání souboru MP4 jenom pro zvuk

Nahrajte do výstupního prostředku další soubor MP4 jenom pro zvuk (kodek AAC) obsahující popisný zvuk.  

```csharp
private static async Task UpoadAudioIntoOutputAsset(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string outputAssetName,
    string fileToUpload)
{
    // Use the Assets.Get method to get the existing asset. 
    // In Media Services v3, the Get method on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).

    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = await client.Assets.GetAsync(resourceGroupName, accountName, outputAssetName);
    
    if (asset != null)
    {
      // Use Media Services API to get back a response that contains
      // SAS URL for the Asset container into which to upload blobs.
      // That is where you would specify read-write permissions 
      // and the exparation time for the SAS URL.
      var response = await client.Assets.ListContainerSasAsync(
          resourceGroupName,
          accountName,
          outputAssetName,
          permissions: AssetContainerPermission.ReadWrite,
          expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime());

      var sasUri = new Uri(response.AssetContainerSasUrls.First());

      // Use Storage API to get a reference to the Asset container
      // that was created by calling Asset's CreateOrUpdate method.  
      CloudBlobContainer container = new CloudBlobContainer(sasUri);
      var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));

      // Use Strorage API to upload the file into the container in storage.
      await blob.UploadFromFileAsync(fileToUpload);
    }
}
```

Tady je příklad volání `UpoadAudioIntoOutputAsset` funkce:

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>Úprava souboru. ISM

Po dokončení úlohy kódování budou výstupní Asset obsahovat soubory generované úlohou kódování. 

1. V Azure Portal přejděte do účtu úložiště přidruženého k vašemu účtu Media Services. 
1. Najděte kontejner s názvem svého výstupního prostředku. 
1. V kontejneru Najděte soubor. ISM a klikněte na **Upravit objekt BLOB** (v pravém okně). 
1. Upravte soubor. ISM přidáním informací o nahraném souboru MP4 jenom pro zvuk (kodek AAC), který obsahuje popisný zvuk, a po dokončení klikněte na **Uložit** .

    Chcete-li signalizovat popisné zvukové stopy, je třeba do souboru. ISM přidat parametry "usnadnění" a "role". Vaše zodpovědnost za správné nastavení těchto parametrů k signalizaci zvukové stopy jako zvukového popisu. Například přidejte `<param name="accessibility" value="description" />` a `<param name="role" value="alternate" />` do souboru. ISM pro konkrétní zvukovou stopu, jak je znázorněno v následujícím příkladu.
 
```xml
<?xml version="1.0" encoding="utf-8"?>
<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
  <head>
    <meta name="clientManifestRelativePath" content="ignite.ismc" />
    <meta name="formats" content="mp4-v3" />
  </head>
  <body>
    <switch>
      <audio src="ignite_320x180_AACAudio_381.mp4" systemBitrate="128041" systemLanguage="eng">
        <param name="systemBitrate" value="128041" valuetype="data" />
        <param name="trackID" value="2" valuetype="data" />
        <param name="trackName" value="aac_eng_2_128041_2_1" valuetype="data" />
        <param name="systemLanguage" value="eng" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_2.mpi" valuetype="data" />
      </audio>
      <audio src="audio_description.m4a" systemBitrate="194000" systemLanguage="eng">
        <param name="trackName" value="aac_eng_audio_description" />
        <param name="accessibility" value="description" />
        <param name="role" value="alternate" />     
      </audio>          
      <video src="ignite_1280x720_AACAudio_3549.mp4" systemBitrate="3549855">
        <param name="systemBitrate" value="3549855" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_1280x720_AACAudio_3549_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_960x540_AACAudio_2216.mp4" systemBitrate="2216764">
        <param name="systemBitrate" value="2216764" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_960x540_AACAudio_2216_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_640x360_AACAudio_1154.mp4" systemBitrate="1154569">
        <param name="systemBitrate" value="1154569" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_640x360_AACAudio_1154_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_480x270_AACAudio_721.mp4" systemBitrate="721893">
        <param name="systemBitrate" value="721893" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_480x270_AACAudio_721_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_320x180_AACAudio_381.mp4" systemBitrate="381027">
        <param name="systemBitrate" value="381027" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_1.mpi" valuetype="data" />
      </video>
    </switch>
  </body>
</smil>
```

## <a name="get-a-streaming-locator"></a>Získání lokátoru streamování

Po dokončení kódování následuje zpřístupnění videa ve výstupním prostředku, kde je k dispozici klientům pro přehrávání. To můžete provést ve dvou krocích: Nejdřív vytvořte [Lokátor streamování](/rest/api/media/streaminglocators)a druhý, sestavte adresy URL streamování, které můžou klienti používat. 

Proces vytvoření **lokátoru streamování** se nazývá publikování. Ve výchozím nastavení je **Lokátor streamování** platný hned po volání rozhraní API a trvá až do odstranění, pokud nenastavíte volitelné počáteční a koncové časy. 

Když vytváříte [streamovací lokátor](/rest/api/media/streaminglocators), je potřeba zadat požadovaný název zásad streamování (**StreamingPolicyName**). V tomto příkladu budete zasílat streamování (nebo nešifrovaný obsah), aby se použily předdefinované zásady zrušení streamování (**PredefinedStreamingPolicy. ClearStreamingOnly**).

> [!IMPORTANT]
> Pokud používáte vlastní [zásady streamování](/rest/api/media/streamingpolicies), měli byste navrhnout určitou sadu takových zásad pro svůj účet Media Service a znovu je použít pro své StreamingLocators, kdykoli budete potřebovat stejné možnosti šifrování a protokoly. Váš účet Media Service má kvótu pro počet položek zásad streamování. Pro každý Lokátor streamování byste neměli vytvářet nové zásady streamování.

Následující kód předpokládá, že funkci voláte s jedinečným názvem lokátoru.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

I když ukázka v tomto tématu popisuje streamování, můžete použít stejné volání k vytvoření lokátoru streamování pro doručování videa prostřednictvím progresivního stahování.

### <a name="get-streaming-urls"></a>Vytvoření adres URL pro streamování

Teď, když se vytvořil [Lokátor streamování](/rest/api/media/streaminglocators) , můžete získat adresy URL streamování, jak je znázorněno v **GetStreamingURLs**. Pokud chcete vytvořit adresu URL, musíte zřetězit název hostitele [koncového bodu streamování](/rest/api/media/streamingendpoints) a cestu k **lokátoru streamování** . V této ukázce se používá *výchozí* **koncový bod streamování** . Při prvním vytvoření účtu služby Media Service bude tento *výchozí* **koncový bod streamování** v zastaveném stavu, takže je potřeba zavolat **Start**.

> [!NOTE]
> V této metodě budete potřebovat lokátor, který se použil při vytváření **lokátoru streamování** pro výstupní prostředek.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Testování s Azure Media Playerem

Tento článek používá k otestování streamu přehrávač Azure Media Player. 

> [!NOTE]
> Pokud se přehrávač hostuje na webu HTTPS, nezapomeňte adresu URL aktualizovat tak, aby obsahovala „https“. 

1. Otevřete webový prohlížeč a přejděte na [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. Do pole **Adresa URL:** vložte jednu z hodnot adresy URL streamování, které jste získali z aplikace. 
 
     Můžete vložit adresu URL ve formátu HLS, pomlčka nebo vyhlazení a Azure Media Player přepnout na příslušný protokol pro streamování pro přehrávání na zařízení automaticky.
3. Stiskněte **Update Player** (Aktualizovat přehrávač).

Azure Media Player můžete použít pro účely testování, nesmí se ale používat v produkčním prostředí. 

## <a name="next-steps"></a>Další kroky

[Analýza videí](analyze-videos-tutorial-with-api.md)
