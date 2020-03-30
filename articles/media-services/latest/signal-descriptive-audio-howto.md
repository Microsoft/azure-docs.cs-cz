---
title: Popisné zvukové stopy signálu pomocí Azure Media Services v3 | Dokumenty společnosti Microsoft
description: Podle pokynů tohoto kurzu nahrajte soubor, zakódujte video, přidejte popisné zvukové stopy a streamujte obsah pomocí služby Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 09/25/2019
ms.author: juliako
ms.openlocfilehash: 0d8f88e6c2fe273efa969278146de67ba18eaecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72392186"
---
# <a name="signal-descriptive-audio-tracks"></a>Popisné zvukové stopy signálu

Do videa můžete přidat skladbu mluveného komentáře, která pomůže zrakově postiženým klientům sledovat záznam videa poslechem mluveného komentáře. Ve službě Media Services v3 signalizujete popisné zvukové stopy anotací zvukové stopy v souboru manifestu.

Tento článek ukazuje, jak zakódovat video, nahrát soubor MP4 pouze pro zvuk (kodek AAC) obsahující popisný zvuk do výstupního datového zdroje a upravit soubor ISM tak, aby zahrnoval popisný zvuk.

## <a name="prerequisites"></a>Požadavky

- [Vytvořte účet mediálních služeb](create-account-cli-how-to.md).
- Postupujte podle kroků v [rozhraní Access Azure Media Services API s rozhraním příkazového příkazového příkazu k Síti Azure](access-api-cli-how-to.md) a uložte přihlašovací údaje. Budete je muset použít pro přístup k rozhraní API.
- Zkontrolujte [dynamické balení](dynamic-packaging-overview.md).
- Projděte si kurz [nahrávání, kódování a streamování videí.](stream-files-tutorial-with-api.md)

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Vytvoření vstupního prostředku a nahrání místního souboru do tohoto prostředku 

Funkce **CreateInputAsset** vytvoří nový vstupní [datový zdroj](https://docs.microsoft.com/rest/api/media/assets) a nahraje do něj zadaný místní video soubor. Tento **prostředek se** používá jako vstup do úlohy kódování. Ve službě Media Services v3 může být vstupem do **úlohy** **datový zdroj**nebo obsahem, který zpřístupníte účtu Mediálních služeb prostřednictvím adres URL HTTPS. 

Pokud se chcete dozvědět, jak kódovat z adresy URL HTTPS, přečtěte [si tento článek](job-input-from-http-how-to.md) .  

Ve službě Media Services v3 slouží k nahrání souborů rozhraní API služby Azure Storage. Následující fragment kódu .NET vám ukáže, jak na to.

Uvedená funkce provede následující akce:

* Vytvoří **datový zdroj** 
* Získá zapisovatelnou [adresu URL SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) do kontejneru datového zdroje [v úložišti.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)
* Přes adresu SAS odešle soubor do kontejneru v úložišti.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Pokud potřebujete předat název vytvořeného vstupního datového zdroje jiným `Name` metodám, nezapomeňte použít `CreateInputAssetAsync`vlastnost objektu datového zdroje vráceného z , například inputAsset.Name. 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>Vytvoření výstupního datového zdroje pro uložení výsledku úlohy kódování

Výstup [Asset](https://docs.microsoft.com/rest/api/media/assets) ukládá výsledek úlohy kódování. Následující funkce ukazuje, jak vytvořit výstupní datový zdroj.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Pokud potřebujete předat název vytvořeného výstupního majetku jiným metodám, nezapomeňte použít `Name` vlastnost `CreateIOutputAssetAsync`na objektu datového zdroje vráceném z , například outputAsset.Name. 

V případě tohoto článku předavěte `SubmitJobAsync` `UploadAudioIntoOutputAsset` hodnotu `outputAsset.Name` funkcím a.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Vytvoření transformace a úlohy, která zakóduje nahraný soubor

Když kódujete nebo zpracováváte obsah v Media Services, kódování se obvykle nastaví jako předpis. Potom stačí odeslat **Úlohu**, která tento předpis použije pro video. Odesláním nových pracovních míst pro každé nové video použijete tento recept na všechna videa v knihovně. V Media Services se pro předpis používá označení **transformace**. Další informace najdete v tématu [Transformace a úlohy](transform-concept.md). Ukázka popsaná v tomto kurzu definuje předpis, který zakóduje video tak, aby se dalo streamovat na nejrůznějších zařízeních s iOSem a Androidem. 

Následující příklad vytvoří transformaci (pokud neexistuje).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

Následující funkce odešle úlohu.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Čekání na dokončení úlohy

Úloze chvíli trvá, než se dokončí, a když k tomu dojde, budete na to pravděpodobně chtít upozornit. Doporučujeme použít Event Grid čekat na dokončení úlohy.

Úloha obvykle prochází následujícími stavy: **Naplánováno**, **Zařazeno do fronty**, **Zpracování**, **Dokončeno** (konečný stav). Pokud během provádění úlohy dojde k chybě, přejde úloha do stavu **Chyba**. Když úlohu zrušíte, změní se její stav na **Rušení** a potom na **Zrušeno**.

Další informace naleznete v [tématu Zpracování událostí mřížky událostí](reacting-to-media-services-events.md).

## <a name="upload-the-audio-only-mp4-file"></a>Nahrání zvukového souboru MP4

Nahrajte do výstupního datového zdroje další zvukový soubor MP4 (kodek AAC) obsahující popisný zvuk.  

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

Zde je příklad volání `UpoadAudioIntoOutputAsset` funkce:

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>Úprava souboru ISM

Po dokončení úlohy kódování bude výstupní datový zdroj obsahovat soubory generované úlohou kódování. 

1. Na webu Azure Portal přejděte na účet úložiště přidružený k vašemu účtu Mediálních služeb. 
1. Najděte kontejner s názvem výstupního datového zdroje. 
1. V kontejneru najděte soubor .ism a klikněte na **Upravit objekt blob** (v pravém okně). 
1. Upravte soubor .ism přidáním informací o nahraném souboru MP4 (kodek Pouze AAC) obsahujícím popisný zvuk a stiskněte **tlačítko Uložit** po dokončení.

    Chcete-li signalizovat popisné zvukové stopy, musíte do souboru .ism přidat parametry "usnadnění" a "role". Je vaší odpovědností správně nastavit tyto parametry tak, aby signalizovaly zvukovou stopu jako zvukový popis. Můžete například `<param name="accessibility" value="description" />` `<param name="role" value="alternate" />` přidat a do souboru ISM pro určitou zvukovou stopu, jak je znázorněno v následujícím příkladu.
 
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

## <a name="get-a-streaming-locator"></a>Získejte vyhledávač datových proudů

Po dokončení kódování následuje zpřístupnění videa ve výstupním prostředku, kde je k dispozici klientům pro přehrávání. Toho lze dosáhnout ve dvou krocích: nejprve [vytvořte lokátor streamování](https://docs.microsoft.com/rest/api/media/streaminglocators)a za druhé vytvořte adresy URL streamování, které mohou klienti používat. 

Proces vytváření **lokátoru streamování se** nazývá publikování. Ve výchozím nastavení je **lokátor streamování** platný ihned po volání rozhraní API a trvá, dokud není odstraněn, pokud nenakonfigurujete volitelný počáteční a koncový čas. 

Když vytváříte [streamovací lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators), je potřeba zadat požadovaný název zásad streamování (**StreamingPolicyName**). V tomto příkladu budete streamovat v jasném (nebo nešifrovaném obsahu), takže se použije předdefinovaná zásada prostého streamování (**PredefinedStreamingPolicy.ClearStreamingOnly).**

> [!IMPORTANT]
> Při použití vlastní [zásady streamování](https://docs.microsoft.com/rest/api/media/streamingpolicies), měli byste navrhnout omezenou sadu těchto zásad pro váš účet Media Service a znovu použít pro streamingLocators vždy, když jsou potřeba stejné možnosti šifrování a protokoly. Váš účet služby Media Service má kvótu pro počet položek zásad streamování. Pro každý lokátor streamování byste neměli vytvářet nové zásady streamování.

Následující kód předpokládá, že funkci voláte s jedinečným názvem lokátoru.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Zatímco ukázka v tomto tématu popisuje streamování, můžete použít stejné volání k vytvoření lokátoru streamování pro doručování videa prostřednictvím postupného stahování.

### <a name="get-streaming-urls"></a>Vytvoření adres URL pro streamování

Nyní, když byl vytvořen [lokátor streamování,](https://docs.microsoft.com/rest/api/media/streaminglocators) můžete získat adresy URL streamování, jak je znázorněno v **adrese GetStreamingURL**. Chcete-li vytvořit adresu URL, musíte zřetězit název hostitele [koncového bodu streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints) a cestu **lokátoru streamování.** V této ukázce se používá *výchozí* **koncový bod streamování.** Při prvním vytvoření účtu služby Media Service bude tento *výchozí* **koncový bod streamování** v zastaveném stavu, takže je třeba zavolat **start**.

> [!NOTE]
> V této metodě potřebujete locatorName, který byl použit při vytváření **streamování Lokátor** pro výstupní asset.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Testování s Azure Media Playerem

Tento článek používá k otestování streamu přehrávač Azure Media Player. 

> [!NOTE]
> Pokud se přehrávač hostuje na webu HTTPS, nezapomeňte adresu URL aktualizovat tak, aby obsahovala „https“. 

1. Otevřete webový prohlížeč [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)a přejděte na .
2. Do pole **URL:** vložte jednu z hodnot adresy URL streamování, které jste získali z aplikace. 
 
     Adresu URL můžete vložit ve formátu HLS, Dash nebo Smooth a Azure Media Player se automaticky přepne na příslušný protokol streamování pro automatické přehrávání na vašem zařízení.
3. Stiskněte **Update Player** (Aktualizovat přehrávač).

Azure Media Player můžete použít pro účely testování, nesmí se ale používat v produkčním prostředí. 

## <a name="next-steps"></a>Další kroky

[Analýza videí](analyze-videos-tutorial-with-api.md)
