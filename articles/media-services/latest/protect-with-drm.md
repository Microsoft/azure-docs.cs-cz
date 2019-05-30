---
title: Pomocí DRM dynamického šifrování a licence služby pro doručování pomocí Azure Media Services | Dokumentace Microsoftu
description: Azure Media Services můžete použít k doručování datových proudů šifrovaných pomocí licencí Microsoft PlayReady, Google Widevine nebo Apple FairPlay.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ce3b7a29f6f57b2bc309c719dbbab6c4574f0a46
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306492"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Kurz: Použití ochrany DRM s dynamickým šifrováním a služby doručování licencí

Azure Media Services můžete použít k doručování datových proudů šifrovaných pomocí licencí Microsoft PlayReady, Google Widevine nebo Apple FairPlay. Podrobnější vysvětlení najdete v tématu [Content protection v případě dynamického šifrování](content-protection-overview.md).

Kromě toho Media Services poskytuje službu doručování licencí DRM PlayReady, Widevine a FairPlay. Když si uživatel vyžádá obsah chráněný pomocí DRM, aplikace přehrávače požádá o licenci z licenční služby Media Services. Pokud je aplikace přehrávače oprávněná, licenční služba Media Services vydá přehrávači licenci. Licence obsahuje dešifrovací klíč, kterým může klientský přehrávač dešifrovat a streamovat obsah.

Tento článek vychází z ukázky [šifrování pomocí ochrany DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM). 

Ukázka popsaná v tomto článku vede k tomuto výsledku:

![Video chráněný pomocí DRM AMS](./media/protect-with-drm/ams_player.png)

V tomto kurzu získáte informace o následujících postupech:    

> [!div class="checklist"]
> * Vytvoření kódování transformace
> * Nastavte podpisový klíč používaný k ověření tokenu
> * Nastavit požadavky na obsahu klíče zásad
> * Vytvoření StreamingLocator zásadám zadaného datového proudu
> * Vytvořit adresu URL použít k přehrávání souboru

## <a name="prerequisites"></a>Požadavky

K dokončení kurzu potřebujete následující:

* Přečíst si článek [Přehled ochrany obsahu](content-protection-overview.md)
* Zkontrolujte [návrhu systému s více variantami DRM ochrany obsahu pomocí řízení přístupu](design-multi-drm-system-with-access-control.md)
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

Výstupní [prostředek](assets-concept.md) uloží výsledek vaší úlohy kódování.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Získání nebo vytvoření transformace kódování

Když vytváříte novou instanci [Transformace](transforms-jobs-concept.md), musíte určit, co má být jejím výstupem. Je povinný parametr `transformOutput` objektu, jak je znázorněno v následujícím kódu. Obsahuje každou TransformOutput **přednastavení**. Přednastavení popisuje podrobný postup zpracování videa nebo zvukový operací, které se použije k vygenerování požadovaného TransformOutput. Ukázka popsaná v tomto článku používá předdefinovanou předvolbu s názvem **AdaptiveStreaming**. Tato předvolba zakóduje vstupní video na základě vstupního rozlišení a přenosové rychlosti do automaticky generované dvojice přenosová rychlost / rozlišení (tzv. bitrate ladder) a vytvoří soubory ISO MP4 s videem H.264 a zvukem AAC odpovídající jednotlivým dvojicím přenosová rychlost / rozlišení. 

Než začnete vytvářet novou **transformaci**, ověřte si nejdřív pomocí metody **Get**, jestli už neexistuje, jak vidíte v následujícím kódu.  Pokud entita v Media Services v3 neexistuje, metoda **Get** vrátí hodnotu **null** (v názvu se nerozlišují malá a velká písmena).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Spuštění úlohy

Jak je uvedeno výše, objekt **Transformace** je předpis a [Úloha](transforms-jobs-concept.md) je vlastní požadavek na službu Media Services, aby **transformaci** použila na daný vstupní videoobsah nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění pro výstup.

V tomto kurzu vytvoříme vstup úlohy na základě souboru, který se ingestuje přímo z [adresy URL zdroje HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Čekání na dokončení úlohy

Úloze chvíli trvá, než se dokončí, a když k tomu dojde, budete na to pravděpodobně chtít upozornit. Následující ukázka kódu je příkladem toho, jak se ve službě dotazovat na stav **úlohy**. Dotazování nepatří mezi doporučené postupy, jak sestavovat aplikace, protože může mít prodlevu. Pokud se dotazování u některého účtu používá nadměrně, je možné ho omezit. Místo dotazování by vývojáři měli používat službu Event Grid. Další informace najdete v článku [Směrování událostí na vlastní webový koncový bod](job-state-events-cli-how-to.md).

**Úlohy** obvykle prochází následujících stavů: **Naplánované**, **ve frontě**, **zpracování**, **dokončeno** (konečný stav). Pokud během provádění úlohy dojde k chybě, přejde úloha do stavu **Chyba**. Když úlohu zrušíte, změní se její stav na **Rušení** a potom na **Zrušeno**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Vytvořit zásadu klíče obsahu

Symetrický klíč poskytuje zabezpečený přístup k vašim prostředkům. Je potřeba vytvořit [zásad klíče k obsahu](content-key-policy-concept.md) při šifrování obsahu pomocí DRM. Zásady lze konfigurovat, jak je klíč k obsahu doručit koncovým klientům. Klíč obsahu je přidružený Lokátor streamování. Služba Media Services poskytuje taky službu doručování klíčů, která doručuje šifrovací klíče a licence autorizovaným uživatelům. 

Je třeba nastavit požadavky (omezení) na **obsahu zásad klíče** , který musí být splněny k doručování klíčů s danou konfigurací. V tomto příkladu jsme nastavili následující konfigurace a požadavky:

* Konfigurace 

    Licence [PlayReady](playready-license-template-overview.md) a [Widevine](widevine-license-template-overview.md) jsou nakonfigurované tak, že se dají doručit pomocí služby doručení licencí Media Services. Tato ukázková aplikace sice nekonfiguruje licenci [FairPlay](fairplay-license-overview.md), ale obsahuje metodu, kterou můžete ke konfiguraci FairPlay použít. Jako další možnost můžete přidat konfiguraci FairPlay.

* Omezení

    Aplikace nastavuje v zásadě omezení typu tokenu JWT.

Když přehrávač zadá požadavek na stream, služba Media Services pomocí zadaného klíče dynamicky zašifruje váš obsah. K dešifrování streamu si přehrávač vyžádá klíč ze služby doručování klíčů. K tomu, aby služba zjistila, jestli má daný uživatel povolené získání klíče, vyhodnocuje zásadu symetrického klíče, kterou jste pro klíč určili.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Vytvořit lokátor streamování

Po dokončení kódování a nastavení zásady symetrického klíče spočívá další krok ve vytvoření videa ve výstupním prostředku, které budou moct klienti přehrávat. To sestává ze dvou kroků: 

1. Vytvoření [Lokátor streamování](streaming-locators-concept.md)
2. Vytvoření adres URL pro streamování, které můžou používat klienti 

Proces vytváření **Lokátor streamování** nazývá publikování. Ve výchozím nastavení **Lokátor streamování** platnost okamžitě po provedení volání rozhraní API a trvá, dokud je odstraníme, pokud nenakonfigurujete volitelné počáteční a koncový čas. 

Při vytváření **Lokátor streamování**, je třeba zadat požadovaný `StreamingPolicyName`. V tomto kurzu se používá jednu z předdefinovaných zásad datových proudů, která informuje Azure Media Services, jak publikovat obsah pro streamování. V tomto příkladu jsme nastavili název StreamingLocator.StreamingPolicyName na zásadu Predefined_MultiDrmCencStreaming. Šifrování pomocí PlayReady a Widevine se použijí, je klíč doručen klientovi přehrávání podle nakonfigurovaného licence DRM. Pokud zároveň chcete svůj stream zašifrovat pomocí CBCS (FairPlay), použijte zásadu Predefined_MultiDrmStreaming. 

> [!IMPORTANT]
> Při použití vlastního [streamování zásad](streaming-policy-concept.md), by měly omezenou sadu zásad návrhu pro svůj účet Media Service a znovu je použít pro vaše StreamingLocators pokaždé, když jsou potřeba stejné možnosti šifrování a protokoly. Počet záznamů StreamingPolicy je pro účty služby Media Service omezený kvótou. Neměli byste vytvářet samostatnou zásadu streamování pro každý streamovací lokátor.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Získání testovacího tokenu
        
V tomto kurzu určíme, že má mít zásada symetrického klíče omezení tokenu. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Služba Media Services podporuje tokeny ve formátu JWT ([JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) a právě ten v ukázce nakonfigurujeme.

V zásadě ContentKeyPolicy se používá deklarace ContentKeyIdentifierClaim, což znamená, že token předaný službě doručování klíčů musí mít v sobě identifikátor symetrického klíče. V ukázce jsme nezadávejte klíče k obsahu, když vytvoříte Lokátor streamování, systém vytvoří náhodný jeden pro nás. K vygenerování testovacího tokenu musíme získat identifikátor symetrického klíče, který se vloží do deklarace ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Vytvořit adresu URL streamování

Vytvořili jste streamovací lokátor [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) a teď můžete vytvořit adresy URL pro streamování. Sestavit adresu URL, je nutné zřetězit [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) název hostitele a **Lokátor streamování** cestu. V této ukázce *výchozí* **koncový bod streamování** se používá. Při prvním vytvoření účtů Media Service, to *výchozí* **koncový bod streamování** budou v zastaveném stavu, takže je potřeba volat **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Po spuštění aplikace se zobrazí následující:

![Ochrana s využitím DRM](./media/protect-with-drm/playready_encrypted_url.png)

Můžete otevřít prohlížeč a zadáním výsledné adresy URL spustit ukázkovou stránku Azure Media Player, na které je už předem vyplněná adresa URL a token. 
 
## <a name="clean-up-resources-in-your-media-services-account"></a>Vyčištění prostředků v účtu služby Media Services

Obecně platí, že byste měli vyčistit všechno kromě objektů, které máte v plánu použít znovu, (obvykle jsou to transformace, streamovací lokátory apod.). Pokud chcete účet po experimentování vyčistit, měli byste odstranit prostředky, které nemáte v plánu znovu použít.  Následující kód například odstraní Úlohy.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtu úložiště, které jste vytvořili v tomto kurzu, pak tuto dříve vytvořenou skupinu prostředků odstraňte. 

Spusťte následující příkaz rozhraní příkazového řádku:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Klást otázky, váš názor, získávat aktualizace

Podívejte se [komunita Azure Media Services](media-services-community.md) článek a zobrazit různé způsoby můžete klást otázky, poskytnout zpětnou vazbu a aktualizace o Media Services.

## <a name="next-steps"></a>Další postup

Seznamte se

> [!div class="nextstepaction"]
> [Ochrana pomocí AES-128](protect-with-aes128.md)

