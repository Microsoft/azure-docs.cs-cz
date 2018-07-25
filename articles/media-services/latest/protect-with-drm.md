---
title: Použití služby doručování licencí s ochranou DRM s dynamickým šifrováním ve službě Azure Media Services| Microsoft Docs
description: Azure Media Services můžete použít k doručování datových proudů šifrovaných pomocí licencí Microsoft PlayReady, Google Widevine nebo Apple FairPlay.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/15/2018
ms.author: juliako
ms.openlocfilehash: 2ceae5b530d1967ccca2b33c05c183302684be41
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116763"
---
# <a name="use-drm-dynamic-encryption-and-license-delivery-service"></a>Použití ochrany DRM s dynamickým šifrováním a služby doručování licencí

Službu Azure Media Services můžete použít k doručování datových proudů MPEG-DASH, Smooth Streaming a HTTP Live Streaming (HLS) chráněných [technologií správy digitálních práv (DRM) PlayReady](https://www.microsoft.com/playready/overview/). Službu Media Services můžete taky použít k doručování šifrovaných datových proudů DASH chráněných licencemi **Google Widevine** s ochranou DRM. Technologie PlayReady i Widevine jsou šifrované podle specifikace Common Encryption (ISO/IEC CENC 23001-7). Služba Media Services umožňuje taky šifrovat obsah HLS pomocí **Apple FairPlay** (AES-128 CBC). 

Kromě toho Media Services poskytuje službu doručování licencí DRM PlayReady, Widevine a FairPlay. Když si uživatel vyžádá obsah chráněný pomocí DRM, aplikace přehrávače požádá o licenci z licenční služby Media Services. Pokud je aplikace přehrávače oprávněná, licenční služba Media Services vydá přehrávači licenci. Licence obsahuje dešifrovací klíč, kterým může klientský přehrávač dešifrovat a streamovat obsah.

Tento článek vychází z ukázky [šifrování pomocí ochrany DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM). Tato ukázka mimo jiné předvádí tyto úkony:

* Vytvoření transformace kódování, která provádí kódování s adaptivní přenosovou rychlostí pomocí integrované předvolby a ingestuje soubor přímo z [adresy URL zdroje HTTPs](job-input-from-http-how-to.md).
* Nastavení podpisového klíče sloužícího k ověření vašeho tokenu.
* Nastavení požadavků (omezení) v zásadě symetrického klíče, které je třeba splnit, aby bylo možné doručit klíče se stanovenou konfigurací. 

    * Konfigurace 
    
        V této ukázce jsou licence [PlayReady](playready-license-template-overview.md) a [Widevine](widevine-license-template-overview.md) nakonfigurované tak, že se dají doručit pomocí služby doručení licencí Media Services. Tato ukázková aplikace sice nekonfiguruje licenci [FairPlay](fairplay-license-overview.md), ale obsahuje metodu, kterou můžete ke konfiguraci FairPlay použít. Pokud chcete, můžete konfiguraci FairPlay přidat jako další možnost.

    * Omezení

        Aplikace nastavuje v zásadě omezení typu tokenu JWT.

* Vytvoření streamovacího lokátoru StreamingLocator s určeným názvem zásady streamování pro určitý prostředek. V tomto případě se použije předdefinovaná zásada. Nastaví u streamovacího lokátoru StreamingLocator dva klíče obsahu: AES-128 (obálka) a CENC (PlayReady a Widevine).  
    
    Po vytvoření streamovacího lokátoru StreamingLocator dojde k publikování výstupního prostředku a k jeho zpřístupnění klientům pro účely přehrávání.

    > [!NOTE]
    > Zkontrolujte, jestli je koncový bod streamování, ze kterého chcete streamovat, ve stavu Spuštěno.

* Vytvoření adresy URL pro Azure Media Player, která obsahuje jak manifest DASH, tak token PlayReady nutný k přehrávání obsahu zašifrovaného pomocí PlayReady. Ukázka nastaví dobu platnosti tokenu na 1 hodinu. 

    Můžete otevřít prohlížeč a zadáním výsledné adresy URL spustit ukázkovou stránku Azure Media Player, na které je už předem vyplněná adresa URL a token.  

    ![ochrana pomocí drm](./media/protect-with-drm/playready_encrypted_url.png)

> [!NOTE]
> Každý prostředek můžete zašifrovat i pomocí několika typů šifrování (AES-128, PlayReady, Widevine, FairPlay). V článku [Typy streamovacích protokolů a šifrování](content-protection-overview.md#streaming-protocols-and-encryption-types) se dozvíte, jaké kombinace dávají smysl.

Ukázka popsaná v tomto článku vede k tomuto výsledku:

![ochrana pomocí drm](./media/protect-with-drm/ams_player.png)

## <a name="prerequisites"></a>Požadavky

K dokončení kurzu potřebujete následující:

* Přečíst si článek [Přehled ochrany obsahu](content-protection-overview.md)
* Nainstalovat Visual Studio Code nebo Visual Studio
* Vytvořit si nový účet služby Azure Media Services podle popisu [v tomto rychlém startu](create-account-cli-quickstart.md)
* Získat přihlašovací údaje nutné k používání rozhraní API služby Media Services podle článku [Přístup k rozhraním API](access-api-cli-how-to.md)
* Nastavit odpovídající hodnoty v konfiguračním souboru aplikace (appsettings.json).

## <a name="download-code"></a>Stažení kódu

Pomocí následujícího příkazu naklonujte do svého počítače úložiště GitHub s úplnou ukázkou streamování .NET, o které pojednává tento článek:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Ukázka „Encrypt with DRM“ se nachází ve složce [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> Při každém spuštění aplikace vytvoří ukázka jedinečné prostředky. Stávající prostředky, jako jsou transformace a zásady, můžete většinou používat opakovaně (pokud má stávající prostředek požadovanou konfiguraci). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Začínáme s rozhraním API služby Media Services se sadou .NET SDK

Pokud chcete začít používat rozhraní Media Services API se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V kódu, který jste naklonovali na začátku článku, vytvoří funkce **GetCredentialsAsync** objekt ServiceClientCredentials na základě pověření zadaných v místním konfiguračním souboru. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Vytvoření výstupního prostředku  

Výstupní [prostředek](https://docs.microsoft.com/rest/api/media/assets) uloží výsledek vaší úlohy kódování.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Získání nebo vytvoření transformace kódování

Když vytváříte novou instanci [Transformace](https://docs.microsoft.com/rest/api/media/transforms), musíte určit, co má být jejím výstupem. Objekt **TransformOutput** v níže uvedeném kódu je povinný parametr. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Předvolba** popisuje podrobné pokyny operací zpracování videa nebo zvuku, které se používají ke generování požadovaného objektu **TransformOutput**. Ukázka popsaná v tomto článku používá předdefinovanou předvolbu s názvem **AdaptiveStreaming**. Tato předvolba zakóduje vstupní video na základě vstupního rozlišení a přenosové rychlosti do automaticky generované dvojice přenosová rychlost / rozlišení (tzv. bitrate ladder) a vytvoří soubory ISO MP4 s videem H.264 a zvukem AAC odpovídající jednotlivým dvojicím přenosová rychlost / rozlišení. 

Než začnete vytvářet novou [transformaci](https://docs.microsoft.com/rest/api/media/transforms), ověřte si nejdřív pomocí metody **Get**, jestli už neexistuje, jak vidíte v následujícím kódu.  Pokud entita v Media Services v3 neexistuje, metoda **Get** vrátí hodnotu **null** (v názvu se nerozlišují malá a velká písmena).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Spuštění úlohy

Jak je uvedeno výše, objekt [Transformace](https://docs.microsoft.com/rest/api/media/transforms) je předpis a [Úloha](https://docs.microsoft.com/rest/api/media/jobs) je vlastní požadavek na službu Media Services, aby **transformaci** použila na daný vstupní videoobsah nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění pro výstup.

V tomto kurzu vytvoříme vstup úlohy na základě souboru, který se ingestuje přímo z [adresy URL zdroje HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Čekání na dokončení úlohy

Úloze chvíli trvá, než se dokončí, a když k tomu dojde, budete na to pravděpodobně chtít upozornit. Následující ukázka kódu je příkladem toho, jak se ve službě dotazovat na stav [úlohy](https://docs.microsoft.com/rest/api/media/jobs). Dotazování nepatří mezi doporučené postupy, jak sestavovat aplikace, protože může mít prodlevu. Pokud se dotazování u některého účtu používá nadměrně, je možné ho omezit. Místo dotazování by vývojáři měli používat službu Event Grid. Další informace najdete v článku [Směrování událostí na vlastní webový koncový bod](job-state-events-cli-how-to.md).

**Úloha** obvykle prochází následujícími stavy: **Naplánováno**, **Ve frontě**, **Zpracovávání** a **Dokončeno** (konečný stav). Pokud během provádění úlohy dojde k chybě, přejde úloha do stavu **Chyba**. Když úlohu zrušíte, změní se její stav na **Rušení** a potom na **Zrušeno**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkeypolicy"></a>Vytvoření zásady symetrického klíče

Symetrický klíč poskytuje zabezpečený přístup k vašim prostředkům. Potřebujete vytvořit zásadu symetrického klíče, která určí, jak se má symetrický klíč doručovat koncovým klientům. Symetrický klíč je přidružený ke streamovacímu lokátoru StreamingLocator. Služba Media Services poskytuje taky službu doručování klíčů, která doručuje šifrovací klíče a licence autorizovaným uživatelům. 

V zásadě symetrického klíče je potřeba nastavit požadavky (omezení), které je třeba splnit, aby bylo možné doručit klíče se stanovenou konfigurací. V tomto příkladu jsme nastavili následující konfigurace a požadavky:

* Konfigurace 

    Licence [PlayReady](playready-license-template-overview.md) a [Widevine](widevine-license-template-overview.md) jsou nakonfigurované tak, že se dají doručit pomocí služby doručení licencí Media Services. Tato ukázková aplikace sice nekonfiguruje licenci [FairPlay](fairplay-license-overview.md), ale obsahuje metodu, kterou můžete ke konfiguraci FairPlay použít. Jako další možnost můžete přidat konfiguraci FairPlay.

* Omezení

    Aplikace nastavuje v zásadě omezení typu tokenu JWT.

Když přehrávač zadá požadavek na stream, služba Media Services pomocí zadaného klíče dynamicky zašifruje váš obsah. K dešifrování streamu si přehrávač vyžádá klíč ze služby doručování klíčů. K tomu, aby služba zjistila, jestli má daný uživatel povolené získání klíče, vyhodnocuje zásadu symetrického klíče, kterou jste pro klíč určili.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaminglocator"></a>Vytvoření lokátoru streamování StreamingLocator

Po dokončení kódování a nastavení zásady symetrického klíče spočívá další krok ve vytvoření videa ve výstupním prostředku, které budou moct klienti přehrávat. To sestává ze dvou kroků: 

1. Vytvoření lokátoru streamování [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)
2. Vytvoření adres URL pro streamování, které můžou používat klienti 

Proces vytváření streamovacího lokátoru **StreamingLocator** označujeme jako publikování. Pokud nenakonfigurujete volitelný počáteční a koncový čas, je **streamovací lokátor** ve výchozím nastavení platný hned po zavolání rozhraní API a jeho platnost zrušíte až jeho odstraněním. 

Když vytváříte streamovací lokátor [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), je potřeba zadat požadovaný název zásady streamování **StreamingPolicyName**. V tomto kurzu používáme jednu z předdefinovaných zásad streamování, která sděluje službě Azure Media Services, jak publikovat obsah určený ke streamování. V tomto příkladu jsme nastavili název StreamingLocator.StreamingPolicyName na zásadu SecureStreaming. Tato zásada udává, že chcete vygenerovat a nastavit v lokátoru dva symetrické klíče (obálku a CENC). Tím dojde k nastavení obálky a šifrování PlayReady a Widevine (klíč se doručí klientovi pro přehrávání na základě nakonfigurovaných licencí DRM). Pokud zároveň chcete svůj stream zašifrovat pomocí CBCS (FairPlay), zásadu PredefinedStreamingPolicy.SecureStreamingWithFairPlay. 

> [!IMPORTANT]
> Pokud chcete definovat vlastní [zásady streamování](https://docs.microsoft.com/rest/api/media/streamingpolicies), doporučujeme navrhnout pro účet služby Media Service omezený počet takovýchto zásad a používat je opakovaně pro streamovací lokátory, kdykoli potřebujete stejné protokoly a možnosti šifrování. Počet záznamů StreamingPolicy je pro účty služby Media Service omezený kvótou. Neměli byste vytvářet samostatnou zásadu streamování pro každý streamovací lokátor.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Získání testovacího tokenu
        
V tomto kurzu určíme, že má mít zásada symetrického klíče omezení tokenu. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve formátu JWT ([JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) a právě ten v ukázce nakonfigurujeme.

V zásadě ContentKeyPolicy se používá deklarace ContentKeyIdentifierClaim, což znamená, že token předaný službě doručování klíčů musí mít v sobě identifikátor symetrického klíče. V ukázce při vytváření streamovacího lokátoru StreamingLocator neurčujeme symetrický klíč a systém ho tedy vytvoří náhodně. K vygenerování testovacího tokenu musíme získat identifikátor symetrického klíče, který se vloží do deklarace ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Vytvoření adresy URL pro streamování DASH

Vytvořili jste streamovací lokátor [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) a teď můžete vytvořit adresy URL pro streamování. Pokud chcete vytvořit adresu URL, musíte zřetězit název hostitele [koncového bodu hostování](https://docs.microsoft.com/rest/api/media/streamingendpoints) a cestu **streamovacího lokátoru**. V této ukázce je použit *výchozí* **koncový bod streamování**. Když poprvé vytvoříte účet Media Service, tento *výchozí* **koncový bod streamování** bude v zastaveném stavu, proto je potřeba zavolat **spuštění**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Vyčištění prostředků v účtu služby Media Services

Obecně platí, že byste měli vyčistit všechno kromě objektů, které máte v plánu použít znovu, (obvykle jsou to transformace, streamovací lokátory apod.). Pokud chcete účet po experimentování vyčistit, měli byste odstranit prostředky, které nemáte v plánu znovu použít.  Následující kód například odstraní Úlohy.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="next-steps"></a>Další kroky

Podívejte se, jak zajistit [ochranu pomocí AES-128](protect-with-aes128.md).
