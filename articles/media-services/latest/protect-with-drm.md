---
title: Azure Media Services DRM šifrování a doručovací služba licencí
titleSuffix: Azure Media Services
description: Přečtěte si, jak pomocí dynamického šifrování DRM a služby doručování licencí doručovat streamy šifrované pomocí licencí Microsoft PlayReady, Google Widevine nebo Apple FairPlay.
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
ms.openlocfilehash: 14ba5f270138db22a76fd697b264046e22577427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086732"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Kurz: Použití dynamického šifrování DRM a služby doručování licencí

> [!NOTE]
> I když tento kurz používá příklady [sady .NET SDK,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) obecné kroky jsou stejné pro [rozhraní REST API](https://docs.microsoft.com/rest/api/media/liveevents), ROZHRANÍ [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)nebo jiné podporované [sady SDK](media-services-apis-overview.md#sdks).

Azure Media Services můžete použít k doručování datových proudů šifrovaných pomocí licencí Microsoft PlayReady, Google Widevine nebo Apple FairPlay. Podrobné vysvětlení naleznete v [tématu Ochrana obsahu pomocí dynamického šifrování](content-protection-overview.md).

Mediální služby také poskytují službu pro doručování licencí PlayReady, Widevine a FairPlay DRM. Když uživatel požaduje obsah chráněný drem, aplikace přehrávače požaduje licenci z licenční služby Media Services. Pokud je aplikace přehrávače autorizována, licenční služba Mediálních služeb vydá přehrávači licenci. Licence obsahuje dešifrovací klíč, kterým může klientský přehrávač dešifrovat a streamovat obsah.

Tento článek vychází z ukázky [šifrování pomocí ochrany DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

Ukázka popsaná v tomto článku vede k tomuto výsledku:

![AMS s videem chráněným DRM v Přehrávači médií Azure](./media/protect-with-drm/ams_player.png)

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvořte kódovací transformaci.
> * Nastavení podpisového klíče sloužícího k ověření vašeho tokenu.
> * Nastavte požadavky na zásady klíče obsahu.
> * Vytvořte StreamingLocator se zadanou zásadou streamování.
> * Vytvořte adresu URL použitou k přehrávání souboru.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení kurzu potřebujete následující položky:

* Přečíst si článek [Přehled ochrany obsahu](content-protection-overview.md)
* Zkontrolujte [návrh systému ochrany obsahu s více DRM s řízením přístupu](design-multi-drm-system-with-access-control.md).
* Nainstalujte kód sady Visual Studio nebo visual studio.
* Vytvořit si nový účet služby Azure Media Services podle popisu [v tomto rychlém startu](create-account-cli-quickstart.md)
* Získat přihlašovací údaje nutné k používání rozhraní API služby Media Services podle článku [Přístup k rozhraním API](access-api-cli-how-to.md)
* Nastavte příslušné hodnoty v konfiguračním souboru aplikace (appsettings.json).

## <a name="download-code"></a>Stažení kódu

Pomocí následujícího příkazu naklonujte do svého počítače úložiště GitHub s úplnou ukázkou streamování .NET, o které pojednává tento článek:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Ukázka „Encrypt with DRM“ se nachází ve složce [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> Ukázka vytvoří jedinečné prostředky při každém spuštění aplikace. Obvykle budete znovu použít existující prostředky, jako jsou transformace a zásady (pokud existující prostředek mají požadované konfigurace).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Začínáme s rozhraním API služby Media Services se sadou .NET SDK

Chcete-li začít používat rozhraní API mediálních služeb s rozhraním .NET, vytvořte objekt **AzureMediaServicesClient.** K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V kódu, který jste naklonovali na začátku článku, vytvoří funkce **GetCredentialsAsync** objekt ServiceClientCredentials na základě pověření zadaných v místním konfiguračním souboru.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Vytvoření výstupního prostředku  

Výstup [Asset](assets-concept.md) ukládá výsledek úlohy kódování.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Získání nebo vytvoření transformace kódování

Když vytváříte novou instanci [Transformace](transforms-jobs-concept.md), musíte určit, co má být jejím výstupem. Požadovaný parametr je `transformOutput` objekt, jak je znázorněno v kódu níže. Každý objekt TransformOutput obsahuje **Předvolbu**. Předvolba popisuje podrobné pokyny operací zpracování videa nebo zvuku, které se používají ke generování požadovaného objektu TransformOutput. Ukázka popsaná v tomto článku používá předdefinovanou předvolbu s názvem **AdaptiveStreaming**. Přednastavení zakóduje vstupní video do automaticky generovaného žebříku datového toku (dvojice s přenosovým tokem a rozlišením) na základě vstupního rozlišení a datového toku a vytvoří soubory ISO MP4 s videem H.264 a zvukem AAC odpovídajícím každému páru s rozlišením datového toku. 

Než začnete vytvářet novou **transformaci**, ověřte si nejdřív pomocí metody **Get**, jestli už neexistuje, jak vidíte v následujícím kódu.  Pokud entita v Media Services v3 neexistuje, metoda **Get** vrátí hodnotu **null** (v názvu se nerozlišují malá a velká písmena).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Spuštění úlohy

Jak je uvedeno výše, objekt **Transformace** je předpis a [Úloha](transforms-jobs-concept.md) je vlastní požadavek na službu Media Services, aby **transformaci** použila na daný vstupní videoobsah nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění výstupu.

V tomto kurzu vytvoříme vstup úlohy na základě souboru, který je ingestován přímo ze [zdrojové adresy URL HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Čekání na dokončení úlohy

Dokončení úlohy nějakou dobu trvá. Když se tak stane, chcete být upozorněni. Ukázka kódu níže ukazuje, jak dotazování služby pro stav **úlohy**. Dotazování není doporučeným osvědčeným postupem pro produkční aplikace z důvodu potenciální latence. Pokud se dotazování u některého účtu používá nadměrně, je možné ho omezit. Místo dotazování by vývojáři měli používat službu Event Grid. Další informace najdete v článku [Směrování událostí na vlastní webový koncový bod](job-state-events-cli-how-to.md).

**Úloha** obvykle prochází následujícími stavy: **Naplánováno**, **Ve frontě**, **Zpracovávání** a **Dokončeno** (konečný stav). Pokud úloha narazila na chybu, zobrazí se stav **Chyba.** Pokud je úloha v procesu zrušení, dostanete **zrušení** a **zrušení,** když je hotovo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Vytvoření zásad klíče obsahu

Symetrický klíč poskytuje zabezpečený přístup k vašim prostředkům. Při šifrování obsahu pomocí driody je třeba vytvořit [zásady klíče obsahu.](content-key-policy-concept.md) Zásada konfiguruje způsob doručení klíče obsahu koncovým klientům. Klíč obsahu je přidružen k lokátoru streamování. Služba Media Services poskytuje taky službu doručování klíčů, která doručuje šifrovací klíče a licence autorizovaným uživatelům.

Je třeba nastavit požadavky (omezení) na **zásady klíče obsahu,** které musí být splněny dodat klíče se zadanou konfigurací. V tomto příkladu jsme nastavili následující konfigurace a požadavky:

* Konfigurace

    Licence [PlayReady](playready-license-template-overview.md) a [Widevine](widevine-license-template-overview.md) jsou nakonfigurované tak, že se dají doručit pomocí služby doručení licencí Media Services. I když tato ukázková aplikace nekonfiguruje licenci [FairPlay,](fairplay-license-overview.md) obsahuje metodu, kterou můžete použít ke konfiguraci FairPlay. Jako další možnost můžete přidat konfiguraci FairPlay.

* Omezení

    Aplikace nastaví omezení typu tokenu JSON Web Token (JWT) na zásady.

Když přehrávač zadá požadavek na stream, služba Media Services pomocí zadaného klíče dynamicky zašifruje váš obsah. K dešifrování streamu si přehrávač vyžádá klíč ze služby doručování klíčů. K tomu, aby služba zjistila, jestli má daný uživatel povolené získání klíče, vyhodnocuje zásadu symetrického klíče, kterou jste pro klíč určili.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Vytvoření lokátoru streamování

Po dokončení kódování a nastavení zásady symetrického klíče spočívá další krok ve vytvoření videa ve výstupním prostředku, které budou moct klienti přehrávat. Video zpřístupníte ve dvou krocích:

1. Vytvořte [lokátor streamování](streaming-locators-concept.md).
2. Vytvoření adres URL pro streamování, které můžou používat klienti

Proces vytváření **lokátoru streamování se** nazývá publikování. Ve výchozím nastavení je **lokátor streamování** platný ihned po volání rozhraní API. Trvá, dokud není odstraněn, pokud nenakonfigurujete volitelný počáteční a koncový čas.

Při vytváření **lokátoru datových proudů** `StreamingPolicyName`je třeba zadat požadovaný . V tomto kurzu používáme jednu z předdefinovaných zásad streamování, která říká Mediálním službám Azure, jak publikovat obsah pro streamování. V tomto příkladu jsme nastavili název StreamingLocator.StreamingPolicyName na zásadu Predefined_MultiDrmCencStreaming. Jsou použita šifrování PlayReady a Widevine a klíč je doručen klientovi přehrávání na základě nakonfigurovaných licencí DRM. Pokud zároveň chcete svůj stream zašifrovat pomocí CBCS (FairPlay), použijte zásadu Predefined_MultiDrmStreaming.

> [!IMPORTANT]
> Při použití vlastní [zásady streamování](streaming-policy-concept.md), měli byste navrhnout omezenou sadu těchto zásad pro váš účet Media Service a znovu použít pro streamingLocators vždy, když jsou potřeba stejné možnosti šifrování a protokoly. Počet záznamů StreamingPolicy je pro účty služby Media Service omezený kvótou. Neměli byste vytvářet nové StreamingPolicy pro každý StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Získání testovacího tokenu

V tomto kurzu určíme, že má mít zásada symetrického klíče omezení tokenu. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Media Services podporuje tokeny ve formátech [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) a to je to, co nakonfigurujeme v ukázce.

V zásadě ContentKeyPolicy se používá deklarace ContentKeyIdentifierClaim, což znamená, že token předaný službě doručování klíčů musí mít v sobě identifikátor symetrického klíče. V ukázce neurčujeme klíč obsahu při vytváření lokátoru streamování, systém pro nás vytvoří náhodný klíč. Chcete-li generovat testovací token, musíme získat ContentKeyId umístit do ContentKeyIdentifierClaim deklarace.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Vytvoření adresy URL streamování

Vytvořili jste streamovací lokátor [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) a teď můžete vytvořit adresy URL pro streamování. Chcete-li vytvořit adresu URL, musíte zřetězit název hostitele [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) a cestu **lokátoru streamování.** V této ukázce se používá *výchozí* **koncový bod streamování.** Při prvním vytvoření účtu služby Media Service bude tento *výchozí* **koncový bod streamování** v zastaveném stavu, takže je třeba zavolat **start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Při spuštění aplikace se zobrazí následující obrazovka:

![Ochrana pomocí DRM](./media/protect-with-drm/playready_encrypted_url.png)

Můžete otevřít prohlížeč a zadáním výsledné adresy URL spustit ukázkovou stránku Azure Media Player, na které je už předem vyplněná adresa URL a token.

## <a name="clean-up-resources-in-your-media-services-account"></a>Vyčištění prostředků v účtu služby Media Services

Obecně byste měli vyčistit vše kromě objektů, které plánujete znovu použít (obvykle budete znovu používat transformace, StreamingLocators a tak dále). Pokud chcete, aby byl váš účet po experimentování čistý, odstraňte prostředky, které neplánujete znovu použít. Například následující kód odstraní úlohy:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtu úložiště, které jste vytvořili v tomto kurzu, pak tuto dříve vytvořenou skupinu prostředků odstraňte.

Proveďte následující příkaz příkazového příkazu příkazu příkazu:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="ask-questions-give-feedback-get-updates"></a>Ptejte se, podávejte zpětnou vazbu, získejte aktualizace

Podívejte se na článek [komunity Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="next-steps"></a>Další kroky

Rezervovat

> [!div class="nextstepaction"]
> [Chraňte pomocí AES-128](protect-with-aes128.md)
