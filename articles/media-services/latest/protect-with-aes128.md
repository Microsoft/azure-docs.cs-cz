---
title: Zašifrujte video pomocí AES-128: Azure Media Services popis: Naučte se šifrovat video pomocí šifrování AES 128-bit a jak používat službu pro doručování klíčů v Azure Media Services.
služby: Media-Services documentationcenter: ' ' Author: IngridAtMicrosoft Manager: femila Editor: ' '

MS. Service: Media-Services MS. rebavování: Media ms.tgt_pltfrm: na MS. devlang: na MS. téma: tutorial MS. Date: 03/17/2021 MS. Author: inhenkel

---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Kurz: šifrování videa pomocí AES-128 a používání služby pro doručování klíčů

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> I když tento kurz používá příklady [sady .NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent) , jsou obecné kroky stejné pro [REST API](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event)nebo jiné podporované sady [SDK](media-services-apis-overview.md#sdks).

Media Services můžete použít k doručování HTTP Live Streaming (HLS), MPEG-POMLČKy a Smooth Streaming šifrovaných pomocí AES pomocí 128 bitových šifrovacích klíčů. Media Services taky poskytuje službu pro doručování klíčů, která poskytuje šifrovací klíče autorizovaným uživatelům. Pokud chcete, aby Media Services dynamicky zašifroval vaše video, přiřadíte šifrovací klíč k lokátoru streamování a také nakonfigurujete zásady klíče obsahu. Když hráč vyžádá datový proud, Media Services použije zadaný klíč k dynamickému šifrování vašeho obsahu pomocí AES-128. K dešifrování streamu si přehrávač vyžádá klíč ze služby doručování klíčů. K tomu, aby služba zjistila, jestli má daný uživatel povolené získání klíče, vyhodnocuje zásadu symetrického klíče, kterou jste pro klíč určili.

Každý prostředek můžete zašifrovat i pomocí několika typů šifrování (AES-128, PlayReady, Widevine, FairPlay). V článku [Typy streamovacích protokolů a šifrování](content-protection-overview.md#streaming-protocols-and-encryption-types) se dozvíte, jaké kombinace dávají smysl. Viz také [jak chránit pomocí DRM](protect-with-drm.md).

Výstup z ukázky tohoto článku obsahuje adresu URL Azure Media Player, adresu URL manifestu a token AES potřebný k přehrání obsahu. Ukázka nastaví vypršení platnosti tokenu JSON Web Token (JWT) na 1 hodinu. Můžete otevřít prohlížeč a vložit výslednou adresu URL pro spuštění ukázkové stránky Azure Media Player s adresou URL a tokenem, který jste vyplnili již v následujícím formátu: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}``` .

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Stáhněte si ukázku [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) popsanou v článku.
> * Začněte používat rozhraní API pro Media Services se sadou .NET SDK.
> * Vytvořte výstupní Asset.
> * Vytvoří transformaci kódování.
> * Odešlete úlohu.
> * Počkejte, než se úloha dokončí.
> * Vytvoření zásad pro klíč obsahu
> * Nakonfigurujte zásady tak, aby používaly omezení tokenu JWT.
> * Vytvořte Lokátor streamování.
> * Nakonfigurujte Lokátor streamování pro šifrování videa pomocí standardu AES (ClearKey).
> * Získejte testovací token.
> * Vytvořte adresu URL streamování.
> * Vyčistěte prostředky.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

K dokončení kurzu potřebujete následující:

* Přečíst si článek [Přehled ochrany obsahu](content-protection-overview.md)
* Nainstalujte Visual Studio Code nebo Visual Studio.
* [Vytvořte účet Media Services](./create-account-howto.md).
* Získání přihlašovacích údajů potřebných k použití Media Services rozhraní API pomocí [přístupových rozhraní API](./access-api-howto.md).

## <a name="download-code"></a>Stažení kódu

Pomocí následujícího příkazu naklonujte do svého počítače úložiště GitHub s úplnou ukázkou streamování .NET, o které pojednává tento článek:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Ukázka "šifrování pomocí AES-128" se nachází ve složce [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) .

> [!NOTE]
> Ukázka vytvoří jedinečné prostředky pokaždé, když aplikaci spustíte. Obvykle znovu použijete stávající prostředky, jako jsou transformace a zásady (Pokud existující prostředek má požadované konfigurace).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Začínáme s rozhraním API služby Media Services se sadou .NET SDK

Chcete-li začít používat rozhraní Media Services API s rozhraním .NET, vytvořte objekt **AzureMediaServicesClient** . K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V kódu, který jste naklonovali na začátku článku, vytvoří funkce **GetCredentialsAsync** objekt ServiceClientCredentials na základě pověření zadaných v místním konfiguračním souboru.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Vytvoření výstupního prostředku  

Výstupní [Asset](/rest/api/media/assets) ukládá výsledek vaší úlohy kódování.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Získání nebo vytvoření transformace kódování

Když vytváříte novou instanci [Transformace](/rest/api/media/transforms), musíte určit, co má být jejím výstupem. Objekt **TransformOutput** v níže uvedeném kódu je povinný parametr. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Předvolba** popisuje podrobné pokyny operací zpracování videa nebo zvuku, které se používají ke generování požadovaného objektu **TransformOutput**. Ukázka popsaná v tomto článku používá předdefinovanou předvolbu s názvem **AdaptiveStreaming**. Přednastavení zakóduje vstupní video do automaticky vygenerovaného žebříku přenosové rychlosti (páry přenosů s rozlišením) na základě vstupních rozlišení a přenosové rychlosti a pak vytvoří soubory ISO MP4 s H. 264 a zvukem AAC odpovídajícím páru rozlišení přenosové rychlosti.

Před vytvořením nové [transformace](/rest/api/media/transforms)nejprve ověřte, zda již existuje pomocí metody **Get** , jak je znázorněno v následujícím kódu. Pokud entita v Media Services v3 neexistuje, metoda **Get** vrátí hodnotu **null** (v názvu se nerozlišují malá a velká písmena).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Spuštění úlohy

Jak je uvedeno výše, objekt [Transformace](/rest/api/media/transforms) je předpis a [Úloha](/rest/api/media/jobs) je vlastní požadavek na službu Media Services, aby **transformaci** použila na daný vstupní videoobsah nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění výstupu.

V tomto kurzu vytvoříme vstup úlohy na základě souboru, který je ingestný přímo z [adresy URL zdroje https](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Čekání na dokončení úlohy

Dokončení úlohy trvá déle. V takovém případě chcete být upozorněni. Následující ukázka kódu ukazuje, jak se má služba dotazovat na stav [úlohy](/rest/api/media/jobs). Cyklické dotazování není doporučeným osvědčeným postupem pro produkční aplikace kvůli možné latenci. Pokud se dotazování u některého účtu používá nadměrně, je možné ho omezit. Místo dotazování by vývojáři měli používat službu Event Grid. Další informace najdete v tématu [Směrování událostí do vlastního webového koncového bodu](monitoring/job-state-events-cli-how-to.md).

**Úloha** obvykle prochází následujícími stavy: **Naplánováno**, **Ve frontě**, **Zpracovávání** a **Dokončeno** (konečný stav). Pokud se úloha dokončí v rámci chyby, zobrazí se **chybový** stav. Pokud dojde ke zrušení úlohy, po dokončení operace se akce **zruší** a **zruší** .

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Vytvoření zásad pro klíč obsahu

Symetrický klíč poskytuje zabezpečený přístup k vašim prostředkům. Musíte vytvořit **zásadu klíče obsahu** , která konfiguruje způsob doručení klíče obsahu koncovým klientům. Klíč obsahu je přidružen k **lokátoru streamování**. Media Services taky poskytuje službu pro doručování klíčů, která poskytuje šifrovací klíče autorizovaným uživatelům.

Když hráč vyžádá datový proud, Media Services použije zadaný klíč k dynamickému šifrování vašeho obsahu (v tomto případě pomocí šifrování AES). Aby byl datový proud dešifrován, přehrávač si vyžádá klíč od služby doručování klíčů. K tomu, aby služba zjistila, jestli má daný uživatel povolené získání klíče, vyhodnocuje zásadu symetrického klíče, kterou jste pro klíč určili.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Vytvoření lokátoru streamování

Po dokončení kódování a nastavení zásady symetrického klíče spočívá další krok ve vytvoření videa ve výstupním prostředku, které budou moct klienti přehrávat. Video zpřístupníte ve dvou krocích:

1. Vytvořte [Lokátor streamování](/rest/api/media/streaminglocators).
2. Vytvoření adres URL pro streamování, které můžou používat klienti

Proces vytvoření **lokátoru streamování** se nazývá publikování. Ve výchozím nastavení je **Lokátor streamování** platný ihned po volání rozhraní API. Bude trvat až do odstranění, pokud nenastavíte volitelné počáteční a koncové časy.

Při vytváření [lokátoru streamování](/rest/api/media/streaminglocators)budete muset zadat požadované **StreamingPolicyName**. V tomto kurzu používáme jeden z PredefinedStreamingPolicies, který oznamuje Azure Media Services způsob publikování obsahu pro streamování. V tomto příkladu se používá šifrování pomocí kódování AES (Toto šifrování se také označuje jako šifrování ClearKey, protože klíč se doručuje do klienta pro přehrávání prostřednictvím protokolu HTTPS a nikoli licence DRM).

> [!IMPORTANT]
> Pokud používáte vlastní [StreamingPolicy](/rest/api/media/streamingpolicies), měli byste navrhnout určitou sadu takových zásad pro svůj účet Media Service a znovu je použít pro své lokátory streamování, kdykoli budete potřebovat stejné možnosti šifrování a protokoly. Počet záznamů StreamingPolicy je pro účty služby Media Service omezený kvótou. Neměli byste vytvářet novou StreamingPolicy pro každý Lokátor streamování.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Získání testovacího tokenu

V tomto kurzu určíme, že má mít zásada symetrického klíče omezení tokenu. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Media Services podporuje tokeny ve formátu [JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) a to je to, co v ukázce nakonfigurujeme.

ContentKeyIdentifierClaim se používá v **zásadách klíčů obsahu**, což znamená, že token prezentovaný službě doručení klíčů musí mít identifikátor klíče obsahu. V ukázce jsme při vytváření lokátoru streamování neurčili klíč obsahu, systém pro nás vytvořil náhodný. Pro vygenerování testovacího tokenu je potřeba získat ContentKeyId, který se vloží do deklarace ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Vytvoření adresy URL pro streamování DASH

Teď, když je [Lokátor streamování](/rest/api/media/streaminglocators) vytvořený, můžete získat adresy URL streamování. Pokud chcete vytvořit adresu URL, musíte zřetězit název hostitele [StreamingEndpoint](/rest/api/media/streamingendpoints) a cestu k **lokátoru streamování** . V této ukázce se používá *výchozí* **koncový bod streamování** . Při prvním vytvoření účtu služby Media Service bude tento *výchozí* **koncový bod streamování** v zastaveném stavu, takže je potřeba zavolat **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Vyčištění prostředků v účtu služby Media Services

Obecně byste měli vyčistit vše kromě objektů, které plánujete znovu použít (obvykle budete znovu používat transformace, Lokátory streamování atd.). Pokud chcete, aby se Váš účet vyčistil po experimentování, odstraňte prostředky, které nechcete znovu použít. Například následující kód odstraní úlohu, vytvořené prostředky a zásady klíčů obsahu:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtu úložiště, které jste vytvořili v tomto kurzu, pak tuto dříve vytvořenou skupinu prostředků odstraňte.

Spusťte následující příkaz rozhraní příkazového řádku:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Ochrana pomocí DRM](protect-with-drm.md)
