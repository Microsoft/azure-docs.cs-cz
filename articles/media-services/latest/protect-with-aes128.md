---
title: Používat dynamické šifrování AES Azure Media Services | Dokumentace Microsoftu
description: Doručování obsahu pomocí klíčů AES 128-bit šifrování, šifrován pomocí Microsoft Azure Media Services. Služba Media Services také poskytuje službu doručení klíče, který poskytuje šifrovací klíče na oprávněné uživatele. Toto téma ukazuje, jak dynamicky šifrovat pomocí standardu AES-128 a používat službu doručování klíčů.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 87d427bd6b4a58948e43c42d81337f7603659e5a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991446"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Použití dynamického šifrování AES-128 a doručení klíče služby

Media Services můžete použít k zajištění HTTP Live Streaming (HLS), MPEG-DASH a Smooth Streaming, šifrují pomocí AES pomocí 128bitového šifrování klíčů. Služba Media Services také poskytuje službu doručení klíče, který poskytuje šifrovací klíče na oprávněné uživatele. Pokud chcete pro Media Services k šifrování prostředek, šifrovací klíč přidružit StreamingLocator a taky nakonfigurovat obsahu klíče zásad. Datový proud je žádost přehrávač, Media Services používá k dynamické šifrování obsahu pomocí šifrování AES se zadaným klíčem. K dešifrování streamu si přehrávač vyžádá klíč ze služby doručování klíčů. K tomu, aby služba zjistila, jestli má daný uživatel povolené získání klíče, vyhodnocuje zásadu symetrického klíče, kterou jste pro klíč určili.

Tento článek je založen na [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) vzorku. Vzorek ukazuje, jak vytvořit kódování transformaci, která používá integrované přednastavení kódování s adaptivní přenosovou rychlostí a ingestuje souboru přímo z [adresa URL zdroje HTTPs](job-input-from-http-how-to.md). Prostředku výstupu se potom zveřejní pomocí šifrování AES (ClearKey). Výstup z ukázky je adresa URL pro Azure Media Player, včetně DASH manifestu a AES token potřebné k přehrávání obsahu. Ukázka nastaví vypršení platnosti tokenu JWT na 1 hodinu. Můžete otevřít prohlížeč a vložit bude Výsledná adresa URL ke spuštění stránky ukázku Azure Media Player pomocí adresy URL a tokenu doplnit pro vás již v následujícím formátu: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.

> [!NOTE]
> Každý prostředek můžete zašifrovat i pomocí několika typů šifrování (AES-128, PlayReady, Widevine, FairPlay). V článku [Typy streamovacích protokolů a šifrování](content-protection-overview.md#streaming-protocols-and-encryption-types) se dozvíte, jaké kombinace dávají smysl.

## <a name="prerequisites"></a>Požadavky

K dokončení kurzu potřebujete následující:

* Přečíst si článek [Přehled ochrany obsahu](content-protection-overview.md)
* Nainstalovat Visual Studio Code nebo Visual Studio
* Vytvořit si nový účet služby Azure Media Services podle popisu [v tomto rychlém startu](create-account-cli-quickstart.md)
* Získat přihlašovací údaje nutné k používání rozhraní API služby Media Services podle článku [Přístup k rozhraním API](access-api-cli-how-to.md)

## <a name="download-code"></a>Stažení kódu

Pomocí následujícího příkazu naklonujte do svého počítače úložiště GitHub s úplnou ukázkou streamování .NET, o které pojednává tento článek:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Tato ukázka "šifrování pomocí AES-128" se nachází v [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) složky.

> [!NOTE]
> Při každém spuštění aplikace vytvoří ukázka jedinečné prostředky. Stávající prostředky, jako jsou transformace a zásady, můžete většinou používat opakovaně (pokud má stávající prostředek požadovanou konfiguraci). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Začínáme s rozhraním API služby Media Services se sadou .NET SDK

Pokud chcete začít používat rozhraní Media Services API se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V kódu, který jste naklonovali na začátku článku, vytvoří funkce **GetCredentialsAsync** objekt ServiceClientCredentials na základě pověření zadaných v místním konfiguračním souboru. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Vytvoření výstupního prostředku  

Výstupní [prostředek](https://docs.microsoft.com/rest/api/media/assets) uloží výsledek vaší úlohy kódování.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Získání nebo vytvoření transformace kódování

Když vytváříte novou instanci [Transformace](https://docs.microsoft.com/rest/api/media/transforms), musíte určit, co má být jejím výstupem. Objekt **TransformOutput** v níže uvedeném kódu je povinný parametr. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Předvolba** popisuje podrobné pokyny operací zpracování videa nebo zvuku, které se používají ke generování požadovaného objektu **TransformOutput**. Ukázka popsaná v tomto článku používá předdefinovanou předvolbu s názvem **AdaptiveStreaming**. Tato předvolba zakóduje vstupní video na základě vstupního rozlišení a přenosové rychlosti do automaticky generované dvojice přenosová rychlost / rozlišení (tzv. bitrate ladder) a vytvoří soubory ISO MP4 s videem H.264 a zvukem AAC odpovídající jednotlivým dvojicím přenosová rychlost / rozlišení. 

Než začnete vytvářet novou [transformaci](https://docs.microsoft.com/rest/api/media/transforms), ověřte si nejdřív pomocí metody **Get**, jestli už neexistuje, jak vidíte v následujícím kódu.  Pokud entita v Media Services v3 neexistuje, metoda **Get** vrátí hodnotu **null** (v názvu se nerozlišují malá a velká písmena).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Spuštění úlohy

Jak je uvedeno výše, objekt [Transformace](https://docs.microsoft.com/rest/api/media/transforms) je předpis a [Úloha](https://docs.microsoft.com/rest/api/media/jobs) je vlastní požadavek na službu Media Services, aby **transformaci** použila na daný vstupní videoobsah nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění pro výstup.

V tomto kurzu vytvoříme vstup úlohy na základě souboru, který se ingestuje přímo z [adresy URL zdroje HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Čekání na dokončení úlohy

Úloze chvíli trvá, než se dokončí, a když k tomu dojde, budete na to pravděpodobně chtít upozornit. Následující ukázka kódu je příkladem toho, jak se ve službě dotazovat na stav [úlohy](https://docs.microsoft.com/rest/api/media/jobs). Dotazování nepatří mezi doporučené postupy, jak sestavovat aplikace, protože může mít prodlevu. Pokud se dotazování u některého účtu používá nadměrně, je možné ho omezit. Místo dotazování by vývojáři měli používat službu Event Grid. Další informace najdete v článku [Směrování událostí na vlastní webový koncový bod](job-state-events-cli-how-to.md).

**Úlohy** obvykle prochází následujících stavů: **Naplánované**, **ve frontě**, **zpracování**, **dokončeno** (konečný stav). Pokud během provádění úlohy dojde k chybě, přejde úloha do stavu **Chyba**. Když úlohu zrušíte, změní se její stav na **Rušení** a potom na **Zrušeno**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkeypolicy"></a>Vytvoření zásady symetrického klíče

Symetrický klíč poskytuje zabezpečený přístup k vašim prostředkům. Je potřeba vytvořit **ContentKeyPolicy** , který konfiguruje, jak je klíč k obsahu doručit koncovým klientům. Klíč obsahu je přidružené k **StreamingLocator**. Služba Media Services také poskytuje službu doručení klíče, který poskytuje šifrovací klíče na oprávněné uživatele. 

Datový proud je žádost přehrávač, Media Services využívá se zadaným klíčem k dynamické šifrování obsahu (v tomto případě s použitím šifrování AES.) K dešifrování streamu si přehrávač vyžádá klíč ze služby doručování klíčů. K tomu, aby služba zjistila, jestli má daný uživatel povolené získání klíče, vyhodnocuje zásadu symetrického klíče, kterou jste pro klíč určili.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaminglocator"></a>Vytvoření lokátoru streamování StreamingLocator

Po dokončení kódování a nastavení zásady symetrického klíče spočívá další krok ve vytvoření videa ve výstupním prostředku, které budou moct klienti přehrávat. To sestává ze dvou kroků: 

1. Vytvoření lokátoru streamování [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)
2. Vytvoření adres URL pro streamování, které můžou používat klienti 

Proces vytváření streamovacího lokátoru **StreamingLocator** označujeme jako publikování. Pokud nenakonfigurujete volitelný počáteční a koncový čas, je **streamovací lokátor** ve výchozím nastavení platný hned po zavolání rozhraní API a jeho platnost zrušíte až jeho odstraněním. 

Když vytváříte [streamovací lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators), je potřeba zadat požadovaný název zásad streamování (**StreamingPolicyName**). V tomto kurzu používáme jednu z předdefinovaných zásad streamování, která sděluje službě Azure Media Services, jak publikovat obsah určený ke streamování. V tomto příkladu se použije šifrování standardu AES Envelope (označované také jako ClearKey šifrování vzhledem k tomu, že je klíč doručen klientovi přehrávání prostřednictvím protokolu HTTPS a ne licence DRM).

> [!IMPORTANT]
> Pokud chcete definovat vlastní [zásady streamování](https://docs.microsoft.com/rest/api/media/streamingpolicies), doporučujeme navrhnout pro účet služby Media Service omezený počet takovýchto zásad a používat je opakovaně pro streamovací lokátory, kdykoli potřebujete stejné protokoly a možnosti šifrování. Počet záznamů StreamingPolicy je pro účty služby Media Service omezený kvótou. Neměli byste vytvářet samostatnou zásadu streamování pro každý streamovací lokátor.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Získání testovacího tokenu
        
V tomto kurzu určíme, že má mít zásada symetrického klíče omezení tokenu. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve formátu JWT ([JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) a právě ten v ukázce nakonfigurujeme.

ContentKeyIdentifierClaim se používá v ContentKeyPolicy, což znamená, že token, který zobrazí ve službě Key doručování musí mít identifikátor ContentKey v ní. V ukázce při vytváření streamovacího lokátoru StreamingLocator neurčujeme symetrický klíč a systém ho tedy vytvoří náhodně. K vygenerování testovacího tokenu musíme získat identifikátor symetrického klíče, který se vloží do deklarace ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Vytvoření adresy URL pro streamování DASH

Vytvořili jste streamovací lokátor [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) a teď můžete vytvořit adresy URL pro streamování. Pokud chcete vytvořit adresu URL, musíte zřetězit název hostitele [koncového bodu hostování](https://docs.microsoft.com/rest/api/media/streamingendpoints) a cestu **streamovacího lokátoru**. V této ukázce je použit *výchozí* **koncový bod streamování**. Když poprvé vytvoříte účet Media Service, tento *výchozí* **koncový bod streamování** bude v zastaveném stavu, proto je potřeba zavolat **spuštění**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Vyčištění prostředků v účtu služby Media Services

Obecně platí, že byste měli vyčistit všechno kromě objektů, které máte v plánu použít znovu, (obvykle jsou to transformace, streamovací lokátory apod.). Pokud chcete účet po experimentování vyčistit, měli byste odstranit prostředky, které nemáte v plánu znovu použít.  Následující kód například odstraní Úlohy.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="next-steps"></a>Další postup

Podívejte se na tom, jak [chránit pomocí DRM](protect-with-drm.md)
