---
title: Používat dynamické šifrování AES Azure Media Services | Microsoft Docs
description: Doručovat obsah s klíči šifrování AES 128 bitů, šifrován pomocí Microsoft Azure Media Services. Služba Media Services také poskytuje službu doručení klíče, který doručí šifrovací klíče na oprávněné uživatele. Toto téma ukazuje, jak dynamicky šifrovat pomocí standardu AES-128 a používat službu doručení klíče.
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
ms.date: 06/26/2018
ms.author: juliako
ms.openlocfilehash: 0da5bbee6d0d6401a35c301a8b35dc0efa77da7d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132868"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Použití dynamické šifrování AES-128 a doručení klíče služby

Služba Media Services můžete použít k poskytování HTTP Live Streaming (HLS), MPEG DASH a technologie Smooth Streaming šifrovaný pomocí klíče 128bitové šifrování AES. Služba Media Services také poskytuje službu doručení klíče, který doručí šifrovací klíče na oprávněné uživatele. Pokud chcete pro Media Services k šifrování prostředek, můžete přidružit StreamingLocator šifrovací klíč a také nakonfigurovat zásad obsahu klíče. Datový proud je žádost přehrávač, používá služba Media Services k zadanému klíči dynamicky šifrovat obsah pomocí šifrování AES. Přehrávač dešifrovat datový proud, požadavků klíč ze služby doručení klíče. Pokud chcete zjistit, zda je uživatel autorizovaný k získání klíče, služba vyhodnocuje zásady autorizace, které jste zadali pro klíč.

Podle článku [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) ukázka. Ukázka ukazuje, jak vytvořit kódování transformace, která používá integrované předvolby pro kódování s adaptivní přenosovou rychlostí a ingestuje soubor přímo z [adresa URL zdroje HTTPs](job-input-from-http-how-to.md). Výstupní asset se pak publikuje pomocí šifrování AES (ClearKey). Výstup z ukázky je adresu URL Azure Media Player, včetně DASH manifest a AES token potřebné k přehrávání obsahu. Ukázka nastaví vypršení platnosti tokenu JWT na 1 hodinu. Můžete otevřít prohlížeč a vložte adresu URL výsledné při otevření stránky ukázku přehrávač médií Azure s adresu URL a tokenu doplnit pro vás již (v následujícím formátu: ``` https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.)

> [!NOTE]
> Každý prostředek s více typy šifrování (AES-128, PlayReady, Widevine, FairPlay) můžete šifrovat. V tématu [streamování protokoly a typy šifrování](content-protection-overview.md#streaming-protocols-and-encryption-types), pokud chcete zobrazit, co má smysl kombinovat.

## <a name="prerequisites"></a>Požadavky

K dokončení kurzu potřebujete následující:

* Zkontrolujte [obsahu Přehled ochrany](content-protection-overview.md) článku.
* Nainstalovat Visual Studio Code nebo Visual Studio
* Vytvořit nový účet Azure Media Services, jak je popsáno v [tento rychlý Start](create-account-cli-quickstart.md).
* Získání přihlašovacích údajů, které jsou potřeba k použití rozhraní API pro Media Services pomocí následujících [přístup k rozhraní API](access-api-cli-how-to.md)

## <a name="download-code"></a>Stáhněte si kód

Klonování úložiště GitHub, který obsahuje úplnou ukázku .NET popsané v tomto tématu k počítači pomocí následujícího příkazu:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Ukázka "šifrovat pomocí standardu AES-128" se nachází v [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) složky.

> [!NOTE]
> Ukázka vytvoří jedinečný prostředky při každém spuštění aplikace. Obvykle se bude používat existující prostředky jako transformací a zásady (Pokud existující prostředek mají požadovaná konfigurace). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Začínáme s rozhraním API služby Media Services se sadou .NET SDK

Pokud chcete začít používat rozhraní Media Services API se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V kódu, který jste naklonovali na začátku článku, vytvoří funkce **GetCredentialsAsync** objekt ServiceClientCredentials na základě pověření zadaných v místním konfiguračním souboru. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Vytvoření výstupního prostředku  

Výstupní [prostředek](https://docs.microsoft.com/rest/api/media/assets) uloží výsledek vaší úlohy kódování. Po dokončení kódování neaktualizuje, výstupní asset je publikována pomocí šifrování AES (ClearKey).  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Získejte nebo vytvořte kódování transformace

Když vytváříte novou instanci [Transformace](https://docs.microsoft.com/rest/api/media/transforms), musíte určit, co má být jejím výstupem. Objekt **TransformOutput** v níže uvedeném kódu je povinný parametr. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Předvolba** popisuje podrobné pokyny operací zpracování videa nebo zvuku, které se používají ke generování požadovaného objektu **TransformOutput**. Ukázka popsaná v tomto článku používá předdefinovanou předvolbu s názvem **AdaptiveStreaming**. Tato předvolba zakóduje vstupní video na základě vstupního rozlišení a přenosové rychlosti do automaticky generované dvojice přenosová rychlost / rozlišení (tzv. bitrate ladder) a vytvoří soubory ISO MP4 s videem H.264 a zvukem AAC odpovídající jednotlivým dvojicím přenosová rychlost / rozlišení. 

Před vytvořením nového [transformace](https://docs.microsoft.com/rest/api/media/transforms), musí nejprve zkontrolujte, pokud už existuje pomocí **získat** metoda, jak je znázorněno v následujícím kódu.  Pokud entita v Media Services v3 neexistuje, metoda **Get** vrátí hodnotu **null** (v názvu se nerozlišují malá a velká písmena).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Spustit úlohu

Jak je uvedeno výše, objekt [Transformace](https://docs.microsoft.com/rest/api/media/transforms) je předpis a [Úloha](https://docs.microsoft.com/rest/api/media/jobs) je vlastní požadavek na službu Media Services, aby **transformaci** použila na daný vstupní videoobsah nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění pro výstup.

V tomto kurzu vytvoříme vstupu úlohy, které jsou založené na soubor, který je konzumována přímo z [adresa URL zdroje HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Čekání na dokončení úlohy

Úloze chvíli trvá, než se dokončí, a když k tomu dojde, budete na to pravděpodobně chtít upozornit. Následující ukázka kódu je příkladem toho, jak se ve službě dotazovat na stav [úlohy](https://docs.microsoft.com/rest/api/media/jobs). Dotazování nepatří mezi doporučené postupy, jak sestavovat aplikace, protože může mít prodlevu. Pokud se dotazování u některého účtu používá nadměrně, je možné ho omezit. Místo dotazování by vývojáři měli používat službu Event Grid. Další informace najdete v článku [Směrování událostí na vlastní webový koncový bod](job-state-events-cli-how-to.md).

**Úloha** obvykle prochází následujícími stavy: **Naplánováno**, **Ve frontě**, **Zpracovávání** a **Dokončeno** (konečný stav). Pokud během provádění úlohy dojde k chybě, přejde úloha do stavu **Chyba**. Když úlohu zrušíte, změní se její stav na **Rušení** a potom na **Zrušeno**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkey-policy"></a>Vytvořit zásadu ContentKey

Klíč obsahu poskytuje zabezpečený přístup k vaše prostředky. Musíte vytvořit zásadu obsahu klíče, která nakonfiguruje způsob doručení obsahu klíč k ukončení klientů. Klíč k obsahu je přidružen StreamingLocator. Služba Media Services také poskytuje službu doručení klíče, který doručí šifrovací klíče na oprávněné uživatele. 

Datový proud je žádost přehrávač, Media Services používá k zadanému klíči dynamicky šifrovat obsah (v takovém případě pomocí šifrování AES.) Přehrávač dešifrovat datový proud, požadavků klíč ze služby doručení klíče. Pokud chcete zjistit, zda je uživatel autorizovaný k získání klíče, služba vyhodnocuje zásady autorizace, které jste zadali pro klíč.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="get-a-token"></a>Získání tokenu
        
V tomto kurzu určíme obsahu klíče zásady tak, aby měl token omezení. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve [webových tokenů JSON](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) formátů (JWT) a že je nakonfigurujeme ve vzorku.

ContentKeyIdentifierClaim se používá v ContentKeyPolicy, což znamená, že token prezentovaná službu doručování klíč musí mít identifikátor ContentKey v ní. V ukázce jsme nezadávejte klíč obsahu, při vytváření StreamingLocator, systém vytvoří náhodný jednu pro nás. Pokud chcete generovat testovací token, jsme musíte získat ContentKeyId uvést do ContentKeyIdentifierClaim deklarace identity.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="create-a-streaminglocator"></a>Vytvoření lokátoru streamování StreamingLocator

Po dokončení kódování následuje zpřístupnění videa ve výstupním prostředku, kde je k dispozici klientům pro přehrávání. Video můžete zpřístupnit ve dvou krocích: nejdřív vytvořte streamovací lokátor ([StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)) a pak adresy URL pro streamování, které budou klienti používat. 

Proces vytváření **streamovacího lokátoru** označujeme jako publikování. Pokud nenakonfigurujete volitelný počáteční a koncový čas, je **streamovací lokátor** ve výchozím nastavení platný hned po zavolání rozhraní API a jeho platnost zrušíte až jeho odstraněním. 

Když vytváříte [streamovací lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators), je potřeba zadat požadovaný název zásad streamování (**StreamingPolicyName**). V tomto kurzu se používá jednu z PredefinedStreamingPolicies, který informuje Azure Media Services, jak publikovat obsah pro streamování. V tomto příkladu je šifrování pomocí standardu AES Envelope použita (také označované jako ClearKey šifrování vzhledem k tomu, že je klíč doručen přehrávání klienta prostřednictvím protokolu HTTPS a není licence DRM).

> [!IMPORTANT]
> Pokud chcete definovat vlastní [zásady streamování](https://docs.microsoft.com/rest/api/media/streamingpolicies), doporučujeme navrhnout pro účet služby Media Service omezený počet takovýchto zásad a používat je opakovaně pro streamovací lokátory, kdykoli potřebujete stejné protokoly a možnosti šifrování. Počet záznamů StreamingPolicy je pro účty služby Media Service omezený kvótou. Neměli byste vytvářet samostatnou zásadu streamování pro každý streamovací lokátor.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="build-a-dash-streaming-url"></a>Sestavit adresu URL streamování pomlčkou

Teď, když [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) byl vytvořen, můžete získat streamování adresy URL. Pokud chcete vytvořit adresu URL, musíte zřetězit název hostitele [koncového bodu hostování](https://docs.microsoft.com/rest/api/media/streamingendpoints) a cestu **streamovacího lokátoru**. V této ukázce je použit *výchozí* **koncový bod streamování**. Když poprvé vytvoříte účet Media Service, tento *výchozí* **koncový bod streamování** bude v zastaveném stavu, proto je potřeba zavolat **spuštění**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Vyčištění prostředků v účtu služby Media Services

Obecně platí, že byste měli vyčistit všechno kromě objektů, které máte v plánu použít znovu, (obvykle jsou to transformace, streamovací lokátory apod.). Pokud chcete účet po experimentování vyčistit, měli byste odstranit prostředky, které nemáte v plánu znovu použít.  Následující kód například odstraní Úlohy.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="next-steps"></a>Další postup

[Přehled](content-protection-overview.md)