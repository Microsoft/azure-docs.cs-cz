---
title: Používat dynamické šifrování AES Azure Media Services | Dokumentace Microsoftu
description: Doručování obsahu pomocí klíčů AES 128-bit šifrování, šifrován pomocí Microsoft Azure Media Services. Služba Media Services také poskytuje službu doručení klíče, který poskytuje šifrovací klíče na oprávněné uživatele. Toto téma ukazuje, jak dynamicky šifrovat pomocí standardu AES-128 a používat službu doručování klíčů.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: juliako
ms.openlocfilehash: 3e5de521570a587b049702dabd3e3692c4227796
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114789"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Použití dynamického šifrování AES-128 a doručení klíče služby

Media Services můžete použít k zajištění HTTP Live Streaming (HLS), MPEG-DASH a Smooth Streaming, šifrují pomocí AES pomocí 128bitového šifrování klíčů. Služba Media Services také poskytuje službu doručení klíče, který poskytuje šifrovací klíče na oprávněné uživatele. Pokud chcete pro Media Services k šifrování prostředek, šifrovací klíč přidružit StreamingLocator a taky nakonfigurovat obsahu klíče zásad. Datový proud je žádost přehrávač, Media Services používá k dynamické šifrování obsahu pomocí šifrování AES se zadaným klíčem. K dešifrování datového proudu, přehrávače požádá službu doručování klíčů klíč. Pokud chcete zjistit, zda je uživatel oprávnění k získání klíče, služba vyhodnocuje obsahu klíče zásadami, které jste zadali pro klíč.

Tento článek je založen na [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) vzorku. Vzorek ukazuje, jak vytvořit kódování transformaci, která používá integrované přednastavení kódování s adaptivní přenosovou rychlostí a ingestuje souboru přímo z [adresa URL zdroje HTTPs](job-input-from-http-how-to.md). Prostředku výstupu se potom zveřejní pomocí šifrování AES (ClearKey). Výstup z ukázky je adresa URL pro Azure Media Player, včetně DASH manifestu a AES token potřebné k přehrávání obsahu. Ukázka nastaví vypršení platnosti tokenu JWT na 1 hodinu. Můžete otevřít prohlížeč a vložit bude Výsledná adresa URL ke spuštění stránky ukázku Azure Media Player pomocí adresy URL a tokenu doplnit pro vás již v následujícím formátu: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.

> [!NOTE]
> Každý prostředek s více typy šifrování (AES-128, PlayReady, Widevine, FairPlay) můžete šifrovat. Zobrazit [streamování protokolů a typy šifrování](content-protection-overview.md#streaming-protocols-and-encryption-types), pokud chcete zobrazit, co dává smysl kombinovat.

## <a name="prerequisites"></a>Požadavky

K dokončení kurzu potřebujete následující:

* Zkontrolujte [obsahu Přehled služby Endpoint protection](content-protection-overview.md) článku.
* Nainstalovat Visual Studio Code nebo Visual Studio
* Vytvoření nového účtu Azure Media Services, jak je popsáno v [v tomto rychlém startu](create-account-cli-quickstart.md).
* Získání přihlašovacích údajů potřebných k použití rozhraní API služby Media Services pomocí následujících [přístup k rozhraní API](access-api-cli-how-to.md)

## <a name="download-code"></a>Stáhnout kód

Klon úložiště GitHub obsahující úplnou ukázku .NET popisovaných v tomto článku k počítači pomocí následujícího příkazu:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Tato ukázka "šifrování pomocí AES-128" se nachází v [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) složky.

> [!NOTE]
> Ukázka vytvoří jedinečný prostředky pokaždé, když aplikaci spouštíte. Obvykle bude znovu použít existující prostředky jako transformace a zásady (Pokud existující prostředek mají požadované konfigurace). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Začínáme s rozhraním API služby Media Services se sadou .NET SDK

Pokud chcete začít používat rozhraní Media Services API se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V kódu, které jste naklonovali na začátku tohoto článku **GetCredentialsAsync** funkce vytvoří objekt ServiceClientCredentials na základě pověření v místním konfiguračním souboru. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Vytvoření výstupního prostředku  

Výstupní [prostředek](https://docs.microsoft.com/rest/api/media/assets) uloží výsledek vaší úlohy kódování.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Získat nebo vytvořit kódování transformace

Když vytváříte novou instanci [Transformace](https://docs.microsoft.com/rest/api/media/transforms), musíte určit, co má být jejím výstupem. Objekt **TransformOutput** v níže uvedeném kódu je povinný parametr. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Předvolba** popisuje podrobné pokyny operací zpracování videa nebo zvuku, které se používají ke generování požadovaného objektu **TransformOutput**. Ukázka popsaná v tomto článku používá předdefinovanou předvolbu s názvem **AdaptiveStreaming**. Tato předvolba zakóduje vstupní video na základě vstupního rozlišení a přenosové rychlosti do automaticky generované dvojice přenosová rychlost / rozlišení (tzv. bitrate ladder) a vytvoří soubory ISO MP4 s videem H.264 a zvukem AAC odpovídající jednotlivým dvojicím přenosová rychlost / rozlišení. 

Před vytvořením nového [transformace](https://docs.microsoft.com/rest/api/media/transforms), měli by nejdřív zkontrolovat, pokud už existuje pomocí **získat** způsob, jak je znázorněno v následujícím kódu.  Pokud entita v Media Services v3 neexistuje, metoda **Get** vrátí hodnotu **null** (v názvu se nerozlišují malá a velká písmena).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Spustit úlohu

Jak je uvedeno výše, objekt [Transformace](https://docs.microsoft.com/rest/api/media/transforms) je předpis a [Úloha](https://docs.microsoft.com/rest/api/media/jobs) je vlastní požadavek na službu Media Services, aby **transformaci** použila na daný vstupní videoobsah nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění pro výstup.

V tomto kurzu vytvoříme vstupu úlohy na základě souboru, který se ingestuje přímo z [adresa URL zdroje HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Čekání na dokončení úlohy

Úloze chvíli trvá, než se dokončí, a když k tomu dojde, budete na to pravděpodobně chtít upozornit. Následující ukázka kódu je příkladem toho, jak se ve službě dotazovat na stav [úlohy](https://docs.microsoft.com/rest/api/media/jobs). Dotazování nepatří mezi doporučené postupy, jak sestavovat aplikace, protože může mít prodlevu. Pokud se dotazování u některého účtu používá nadměrně, je možné ho omezit. Místo dotazování by vývojáři měli používat službu Event Grid. Další informace najdete v článku [Směrování událostí na vlastní webový koncový bod](job-state-events-cli-how-to.md).

**Úloha** obvykle prochází následujícími stavy: **Naplánováno**, **Ve frontě**, **Zpracovávání** a **Dokončeno** (konečný stav). Pokud během provádění úlohy dojde k chybě, přejde úloha do stavu **Chyba**. Když úlohu zrušíte, změní se její stav na **Rušení** a potom na **Zrušeno**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkeypolicy"></a>Vytvoření ContentKeyPolicy

Klíč k obsahu poskytuje zabezpečený přístup k vaše prostředky. Je potřeba vytvořit **ContentKeyPolicy** , který konfiguruje, jak je klíč k obsahu doručit koncovým klientům. Klíč obsahu je přidružené k **StreamingLocator**. Služba Media Services také poskytuje službu doručení klíče, který poskytuje šifrovací klíče na oprávněné uživatele. 

Datový proud je žádost přehrávač, Media Services využívá se zadaným klíčem k dynamické šifrování obsahu (v tomto případě s použitím šifrování AES.) K dešifrování datového proudu, přehrávače požádá službu doručování klíčů klíč. Pokud chcete zjistit, zda je uživatel oprávnění k získání klíče, služba vyhodnocuje obsahu klíče zásadami, které jste zadali pro klíč.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaminglocator"></a>Vytvoření lokátoru streamování StreamingLocator

Po dokončení kódování a sady obsahu klíče zásad, dalším krokem je, aby video ve výstupu prostředek k dispozici pro klienty pro přehrávání. To můžete provést ve dvou krocích: 

1. Vytvoření [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)
2. Vytvoření datových proudů adresy URL, které můžou klienti používat. 

Proces vytváření **StreamingLocator** nazývá publikování. Pokud nenakonfigurujete volitelný počáteční a koncový čas, je **streamovací lokátor** ve výchozím nastavení platný hned po zavolání rozhraní API a jeho platnost zrušíte až jeho odstraněním. 

Když vytváříte [streamovací lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators), je potřeba zadat požadovaný název zásad streamování (**StreamingPolicyName**). V tomto kurzu se používá jednu z PredefinedStreamingPolicies, která informuje Azure Media Services, jak publikovat obsah pro streamování. V tomto příkladu se použije šifrování standardu AES Envelope (označované také jako ClearKey šifrování vzhledem k tomu, že je klíč doručen klientovi přehrávání prostřednictvím protokolu HTTPS a ne licence DRM).

> [!IMPORTANT]
> Pokud chcete definovat vlastní [zásady streamování](https://docs.microsoft.com/rest/api/media/streamingpolicies), doporučujeme navrhnout pro účet služby Media Service omezený počet takovýchto zásad a používat je opakovaně pro streamovací lokátory, kdykoli potřebujete stejné protokoly a možnosti šifrování. Počet záznamů StreamingPolicy je pro účty služby Media Service omezený kvótou. Neměli byste vytvářet samostatnou zásadu streamování pro každý streamovací lokátor.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Získání testovacího tokenu
        
V tomto kurzu určíme u obsahu klíče zásad mají omezení s tokenem. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve [webového tokenu JSON](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) formáty (JWT) a že je nakonfigurujeme v ukázce.

ContentKeyIdentifierClaim se používá v ContentKeyPolicy, což znamená, že token, který zobrazí ve službě Key doručování musí mít identifikátor ContentKey v ní. V ukázce jsme nezadávejte klíče k obsahu, při vytváření StreamingLocator, systém vytvoří náhodný jeden pro nás. Aby bylo možné generovat testovací token, musí získáme ContentKeyId vložit ContentKeyIdentifierClaim deklarace identity.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Sestavení adresu URL streamování DASH

Teď, když [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) byl vytvořen, můžete získat adresy URL pro streamování. Pokud chcete vytvořit adresu URL, musíte zřetězit název hostitele [koncového bodu hostování](https://docs.microsoft.com/rest/api/media/streamingendpoints) a cestu **streamovacího lokátoru**. V této ukázce je použit *výchozí* **koncový bod streamování**. Když poprvé vytvoříte účet Media Service, tento *výchozí* **koncový bod streamování** bude v zastaveném stavu, proto je potřeba zavolat **spuštění**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Vyčištění prostředků v účtu služby Media Services

Obecně platí, že byste měli vyčistit všechno kromě objektů, které máte v plánu použít znovu, (obvykle jsou to transformace, streamovací lokátory apod.). Pokud chcete účet po experimentování vyčistit, měli byste odstranit prostředky, které nemáte v plánu znovu použít.  Následující kód například odstraní Úlohy.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="next-steps"></a>Další postup

Podívejte se na tom, jak [chránit pomocí DRM](protect-with-drm.md)
