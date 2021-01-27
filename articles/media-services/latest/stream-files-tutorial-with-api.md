---
title: nahrání, kódování a streamování pomocí Media Services V3: Azure Media Services Description: kurz ukazující, jak nahrát soubor, zakódovat video a streamovat obsah pomocí Azure Media Services V3.
služby: Media-Services documentationcenter: ' ' Author: IngridAtMicrosoft Manager: femila Editor: ' '

MS. Service: Media-Services MS. rebavování: MS. téma: kurz MS. Custom: MVC MS. Date: 08/31/2020 MS. Author: inhenkel
---

# <a name="tutorial-upload-encode-and-stream-videos-with-media-services-v3"></a>Kurz: nahrávání, kódování a streamování videí pomocí Media Services V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> I když tento kurz používá příklady [sady .NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) , jsou obecné kroky stejné pro [REST API](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event?view=azure-cli-latest)nebo jiné podporované sady [SDK](media-services-apis-overview.md#sdks).

Azure Media Services umožňuje kódování mediálních souborů ve formátech, které se přehrávají na nejrůznějších prohlížečích a zařízeních. Například můžete chtít svůj obsah streamovat ve formátu Apple HLS nebo MPEG DASH. Před streamováním je vhodné soubor digitálního média ve vysoké kvalitě zakódovat. Nápovědu k kódování naleznete v tématu [Encoding koncept](encoding-concept.md). V tomto kurzu se nahraje místní soubor videa a nahraný soubor se zakóduje. Obsah, který zpřístupníte prostřednictvím adresy URL protokolu HTTPS, můžete také kódovat. Další informace najdete v článku o [vytvoření vstupu úlohy z adresy URL protokolu HTTP(S)](job-input-from-http-how-to.md).

![Přehrání videa s Azure Media Player](./media/stream-files-tutorial-with-api/final-video.png)

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Stáhněte si ukázkovou aplikaci popsanou v tématu.
> * Projděte si kód, který nahrává, zakóduje a streamuje.
> * Spusťte aplikaci.
> * Otestujte adresu URL streamování.
> * Vyčistěte prostředky.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte nainstalovanou aplikaci Visual Studio, můžete získat [Visual Studio Community 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Vytvořte účet Media Services](./create-account-howto.md).<br/>Nezapomeňte si pamatovat hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services.
- Postupujte podle kroků v [části přístup k rozhraní API Azure Media Services pomocí Azure CLI](./access-api-howto.md) a přihlašovací údaje uložte. Budete je muset použít pro přístup k rozhraní API.

## <a name="download-and-set-up-the-sample"></a>Stažení a nastavení ukázky

Naklonujte úložiště GitHub s ukázkou streamování .NET do vašeho počítače pomocí následujícího příkazu:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Ukázka se nachází ve složce [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles).

Ve staženém projektu otevřete [appsettings.js](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/appsettings.json) . Nahraďte hodnoty přihlašovacími údaji, které jste získali při [přístupu k rozhraním API](./access-api-howto.md).

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>Kontrola kódu, který provádí nahrávání, kódování a streamování

Tato část popisuje funkce definované v souboru [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) projektu *UploadEncodeAndStreamFiles*.

Tato ukázka provede následující akce:

1. Vytvoří novou **transformaci** (nejprve zkontroluje, jestli Zadaná transformace existuje).
2. Vytvoří výstupní **Asset** , který se používá jako výstup **úlohy** kódování.
3. Vytvoří vstupní **Asset** a nahraje zadaný místní videosoubor do souboru. Prostředek se použije jako vstup úlohy.
4. Odešle úlohu kódování pomocí vytvořeného vstupu a výstupu.
5. Zkontroluje stav úlohy.
6. Vytvoří **Lokátor streamování**.
7. Vytvoří adresy URL pro streamování.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Začínáme s rozhraním API služby Media Services se sadou .NET SDK

Pokud chcete začít používat rozhraní Media Services API se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. Pokud chcete vytvořit objekt, musíte zadat přihlašovací údaje potřebné pro připojení klienta k Azure pomocí Azure AD. V kódu, který jste naklonovali na začátku článku, vytvoří funkce **GetCredentialsAsync** objekt ServiceClientCredentials na základě pověření zadaných v místním konfiguračním souboru.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Vytvoření vstupního prostředku a nahrání místního souboru do tohoto prostředku

Funkce **CreateInputAsset** vytvoří nový vstupní [Asset](/rest/api/media/assets) a nahraje zadaný místní videosoubor do souboru. Tento **prostředek** se používá jako vstup do vaší úlohy kódování. V Media Services V3 může být vstupem do **úlohy** buď **Asset** , nebo obsah, který zpřístupníte účtu Media Services prostřednictvím adres URL protokolu HTTPS. Informace o tom, jak kódovat z adresy URL HTTPS, najdete v [tomto](job-input-from-http-how-to.md) článku.

Ve službě Media Services v3 slouží k nahrání souborů rozhraní API služby Azure Storage. Následující fragment kódu .NET vám ukáže, jak na to.

Uvedená funkce provede následující akce:

* Vytvoří **Asset**.
* Získá zapisovatelnou [adresu URL SAS](../../storage/common/storage-sas-overview.md) kontejneru assetu [v úložišti](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container).

    Pokud používáte funkci [ListContainerSas](/rest/api/media/assets/listcontainersas) assetu k získání adres URL SAS, Všimněte si, že funkce vrací několik adres URL SAS, protože pro každý účet úložiště jsou k dispozici dva klíče účtu úložiště. Účet úložiště má dva klíče, protože umožňuje plynulé střídání klíčů účtu úložiště (například při použití druhého, zahájení použití nového klíče a střídání druhého klíče). První adresa URL SAS představuje úložiště klíč1 a druhé úložiště key2.
* Nahraje soubor do kontejneru v úložišti pomocí adresy URL SAS.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Vytvoření výstupního prostředku k uložení výsledku úlohy

Výstupní [Asset](/rest/api/media/assets) ukládá výsledek vaší úlohy kódování. Projekt definuje funkci **DownloadResults**, která stáhne výsledky z tohoto výstupního prostředku do výstupní složky, kde si je můžete zkontrolovat.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Vytvoření transformace a úlohy, která nahraný soubor zakóduje

Při kódování nebo zpracování obsahu v Media Services se jedná o společný vzor pro nastavení kódování jako recept. Potom stačí odeslat **Úlohu**, která tento předpis použije pro video. Když odešlete nové úlohy pro každé nové video, použijete tento recept na všechna videa v knihovně. Recept v Media Services se nazývá **transformace**. Další informace najdete v tématu [Transformace a úlohy](./transforms-jobs-concept.md). Ukázka popsaná v tomto kurzu definuje předpis, který zakóduje video tak, aby se dalo streamovat na nejrůznějších zařízeních s iOSem a Androidem.

#### <a name="transform"></a>Transformace

Když vytváříte novou instanci [Transformace](/rest/api/media/transforms), musíte určit, co má být jejím výstupem. Objekt **TransformOutput** v níže uvedeném kódu je povinný parametr. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Předvolba** popisuje podrobné pokyny operací zpracování videa nebo zvuku, které se používají ke generování požadovaného objektu **TransformOutput**. Ukázka popsaná v tomto článku používá předdefinovanou předvolbu s názvem **AdaptiveStreaming**. Tato předvolba zakóduje vstupní video na základě vstupního rozlišení a přenosové rychlosti do automaticky generované dvojice přenosová rychlost / rozlišení (tzv. bitrate ladder) a vytvoří soubory ISO MP4 s videem H.264 a zvukem AAC odpovídající jednotlivým dvojicím přenosová rychlost / rozlišení. Informace o této předvolbě najdete v tématu o [automatickém generování dvojic bitrate ladder](autogen-bitrate-ladder.md).

Můžete použít předdefinovanou předvolbu EncoderNamedPreset, nebo si vytvořit vlastní. Další informace najdete v tématu o [postupu přizpůsobení předvoleb kodéru](customize-encoder-presets-how-to.md).

Než začnete vytvářet [transformaci](/rest/api/media/transforms), ověřte si nejdřív pomocí metody **Get**, jestli už neexistuje (viz kód níže). Pokud entita v Media Services v3 neexistuje, metoda **Get** vrátí hodnotu **null** (v názvu se nerozlišují malá a velká písmena).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Úloha

Jak je uvedeno výše, objekt [Transformace](/rest/api/media/transforms) je předpis a [Úloha](/rest/api/media/jobs) je vlastní požadavek na službu Media Services, aby **transformaci** použila na daný vstupní videoobsah nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění pro výstup.

V tomto příkladu je vstupní video nahrané z místního počítače. Pokud se chcete dozvědět, jak kódovat z adresy URL HTTPS, přečtěte si [Tento](job-input-from-http-how-to.md) článek.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Čekání na dokončení úlohy

Úloze chvíli trvá, než se dokončí, a když k tomu dojde, budete na to pravděpodobně chtít upozornit. Následující ukázka kódu ukazuje, jak se má služba dotazovat na stav [úlohy](/rest/api/media/jobs). Cyklické dotazování není doporučeným osvědčeným postupem pro produkční aplikace kvůli možné latenci. Pokud se dotazování u některého účtu používá nadměrně, je možné ho omezit. Místo dotazování by vývojáři měli používat službu Event Grid.

Služba Event Grid je navržená pro vysokou dostupnost, konzistentní výkon a dynamické škálování. Díky službě Event Grid můžou vaše aplikace naslouchat událostem a reagovat na ně, ať už pocházejí z kterékoli služby Azure. Události můžou pocházet i z vlastních zdrojů. Jednoduché, reaktivní zpracování událostí založené na protokolu HTTP pomáhá sestavovat efektivní řešení prostřednictvím inteligentního filtrování a směrování událostí.  Další informace najdete v článku [Směrování událostí na vlastní webový koncový bod](job-state-events-cli-how-to.md).

**Úloha** obvykle prochází následujícími stavy: **Naplánováno**, **Ve frontě**, **Zpracovávání** a **Dokončeno** (konečný stav). Pokud během provádění úlohy dojde k chybě, přejde úloha do stavu **Chyba**. Pokud dojde ke zrušení úlohy, po dokončení operace se akce **zruší** a **zruší** .

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Kódy chyb úlohy

Viz [kódy chyb](/rest/api/media/jobs/get#joberrorcode).

### <a name="get-a-streaming-locator"></a>Získání lokátoru streamování

Po dokončení kódování následuje zpřístupnění videa ve výstupním prostředku, kde je k dispozici klientům pro přehrávání. Dá se zpřístupnit ve dvou krocích: Nejdřív vytvořte [Lokátor streamování](/rest/api/media/streaminglocators)a druhý, sestavte adresy URL streamování, které můžou klienti používat.

Proces vytvoření **lokátoru streamování** se nazývá publikování. Ve výchozím nastavení je **Lokátor streamování** platný hned po volání rozhraní API a trvá až do odstranění, pokud nenastavíte volitelné počáteční a koncové časy.

Při vytváření [StreamingLocator](/rest/api/media/streaminglocators)je nutné zadat požadované **StreamingPolicyName**. V tomto příkladu budete zasílat streamování (nebo nešifrovaný obsah), aby se použily předdefinované zásady zrušení streamování (**PredefinedStreamingPolicy. ClearStreamingOnly**).

> [!IMPORTANT]
> Pokud používáte vlastní [zásady streamování](/rest/api/media/streamingpolicies), měli byste navrhnout určitou sadu takových zásad pro svůj účet Media Service a znovu je použít pro své StreamingLocators, kdykoli budete potřebovat stejné možnosti šifrování a protokoly. Váš účet Media Service má kvótu pro počet položek zásad streamování. Nemusíte vytvářet nové zásady streamování pro každý Lokátor streamování.

Následující kód předpokládá, že zavoláte funkci s jedinečným lokátorem.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

I když ukázka v tomto tématu popisuje streamování, můžete použít stejné volání k vytvoření lokátoru streamování pro doručování videa prostřednictvím progresivního stahování.

### <a name="get-streaming-urls"></a>Vytvoření adres URL pro streamování

Teď, když se vytvořil [Lokátor streamování](/rest/api/media/streaminglocators) , můžete získat adresy URL streamování, jak je znázorněno v **GetStreamingURLs**. Pokud chcete vytvořit adresu URL, musíte zřetězit název hostitele [koncového bodu streamování](/rest/api/media/streamingendpoints) a cestu k **lokátoru streamování** . V této ukázce se používá *výchozí* **koncový bod streamování** . Při prvním vytvoření účtu služby Media Service bude tento *výchozí* **koncový bod streamování** v zastaveném stavu, takže je potřeba zavolat **Start**.

> [!NOTE]
> V této metodě budete potřebovat lokátor, který se použil při vytváření **lokátoru streamování** pro výstupní prostředek.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Vyčištění prostředků v účtu služby Media Services

Obecně platí, že byste měli vyčistit všechno kromě objektů, které plánujete znovu použít (obvykle budete znovu používat transformace a budete uchovávat StreamingLocators atd.). Pokud chcete, aby se Váš účet vyčistil po experimentování, odstraňte prostředky, které nechcete znovu použít. Například následující kód odstraní úlohu, vytvořené prostředky a zásady klíčů obsahu:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

1. Stisknutím kombinace kláves CTRL + F5 spusťte aplikaci *EncodeAndStreamFiles* .
2. Zkopírujte jednu z adresy URL pro streamování z konzoly.

Tento příklad uvádí adresy URL, které můžete použít k přehrávání videa pomocí různých protokolů:

![Příklad výstupu znázorňujícího Media Services streamování videa](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Testování adresy URL pro streamování

Tento článek používá k otestování streamu přehrávač Azure Media Player.

> [!NOTE]
> Pokud se přehrávač hostuje na webu HTTPS, nezapomeňte adresu URL aktualizovat tak, aby obsahovala „https“. 

1. Otevřete webový prohlížeč a přejděte na [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. Do pole **Adresa URL:** vložte jednu z hodnot URL streamování, které jste dostali při spuštění aplikace.
3. Vyberte **aktualizovat přehrávač**.

Azure Media Player lze použít pro testování, ale neměl by se používat v produkčním prostředí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtu úložiště, které jste vytvořili v tomto kurzu, pak tuto dříve vytvořenou skupinu prostředků odstraňte.

Spusťte následující příkaz rozhraní příkazového řádku:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Sady SDK Azure Media Services V3 nejsou bezpečné pro přístup z více vláken. Při vývoji aplikace s více vlákny byste měli vygenerovat a použít nový objekt AzureMediaServicesClient na vlákno.

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

Teď, když už víte, jak nahrávat, kódovat a streamovat videa, podívejte se na následující článek: 

> [!div class="nextstepaction"]
> [Analýza videí](analyze-videos-tutorial-with-api.md)
