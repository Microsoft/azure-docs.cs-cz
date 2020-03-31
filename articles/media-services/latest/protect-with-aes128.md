---
title: Šifrování videa pomocí AES-128
titleSuffix: Azure Media Services
description: Přečtěte si, jak šifrovat video pomocí 128bitového šifrování AES a jak používat službu doručování klíčů ve službě Azure Media Services.
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
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 126700e6290650221a9cb9711b22472301409fca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974168"
---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Kurz: Šifrování videa pomocí AES-128 a využití služby doručování klíčů

> [!NOTE]
> Přestože kurz používá příklady [sady .NET SDK,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) obecné kroky jsou stejné pro [rozhraní REST API](https://docs.microsoft.com/rest/api/media/liveevents), ROZHRANÍ [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)nebo jiné podporované [sady SDK](media-services-apis-overview.md#sdks).

Pomocí služby Media Services můžete pomocí 128bitových šifrovacích klíčů doručovat živé vysílání HTTP (HLS), MPEG-DASH a plynulé streamování šifrované pomocí aes. Služba Media Services také poskytuje službu doručování klíčů, která poskytuje šifrovací klíče oprávněným uživatelům. Pokud chcete, aby mediální služby dynamicky šifrovalo vaše video, přidružíte šifrovací klíč k lokátoru streamování a také nakonfigurujete zásady klíče obsahu. Pokud je datový proud požadován přehrávačem, služba Media Services používá zadaný klíč k dynamickému šifrování obsahu pomocí AES-128. K dešifrování streamu si přehrávač vyžádá klíč ze služby doručování klíčů. K tomu, aby služba zjistila, jestli má daný uživatel povolené získání klíče, vyhodnocuje zásadu symetrického klíče, kterou jste pro klíč určili.

Každý prostředek můžete zašifrovat i pomocí několika typů šifrování (AES-128, PlayReady, Widevine, FairPlay). V článku [Typy streamovacích protokolů a šifrování](content-protection-overview.md#streaming-protocols-and-encryption-types) se dozvíte, jaké kombinace dávají smysl. Viz také [způsob ochrany pomocí driod .](protect-with-drm.md)

Výstup z ukázkového tohoto článku obsahuje adresu URL přehrávače médií Azure, adresu URL manifestu a token AES potřebný k přehrání obsahu. Ukázka nastaví vypršení platnosti tokenu JSON web tokenu (JWT) na 1 hodinu. Můžete otevřít prohlížeč a vložit výslednou adresu URL a spustit demo stránku Azure Media Player s ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```adresou URL a tokenem vyplněným pro vás již v následujícím formátu: .

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Stáhněte si ukázku [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) popsanou v článku.
> * Začněte používat rozhraní API mediálních služeb se sadou .NET SDK.
> * Vytvořte výstupní datový zdroj.
> * Vytvořte kódovací transformaci.
> * Odeslat úlohu.
> * Počkejte na dokončení úlohy.
> * Vytvoření zásad klíče obsahu
> * Nakonfigurujte zásadu tak, aby používala omezení tokenu JWT.
> * Vytvořte lokátor streamování.
> * Nakonfigurujte lokátor streamování tak, aby zašifroval video pomocí služby AES (ClearKey).
> * Získejte testovací token.
> * Vytvořte adresu URL streamování.
> * Vyčistěte prostředky.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení kurzu potřebujete následující:

* Přečíst si článek [Přehled ochrany obsahu](content-protection-overview.md)
* Nainstalujte kód sady Visual Studio nebo visual studio.
* [Vytvořte účet mediálních služeb](create-account-cli-quickstart.md).
* Pomocí následujících [přístupových api](access-api-cli-how-to.md)získáte pověření potřebná k použití api mediálních služeb.

## <a name="download-code"></a>Stažení kódu

Pomocí následujícího příkazu naklonujte do svého počítače úložiště GitHub s úplnou ukázkou streamování .NET, o které pojednává tento článek:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Ukázka "Šifrovat pomocí AES-128" je umístěna ve složce [EncryptWithAES.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES)

> [!NOTE]
> Ukázka vytvoří jedinečné prostředky při každém spuštění aplikace. Obvykle budete znovu použít existující prostředky, jako jsou transformace a zásady (pokud existující prostředek mají požadované konfigurace).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Začínáme s rozhraním API služby Media Services se sadou .NET SDK

Chcete-li začít používat rozhraní API mediálních služeb s rozhraním .NET, vytvořte objekt **AzureMediaServicesClient.** K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V kódu, který jste naklonovali na začátku článku, vytvoří funkce **GetCredentialsAsync** objekt ServiceClientCredentials na základě pověření zadaných v místním konfiguračním souboru.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Vytvoření výstupního prostředku  

Výstup [Asset](https://docs.microsoft.com/rest/api/media/assets) ukládá výsledek úlohy kódování.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Získání nebo vytvoření transformace kódování

Když vytváříte novou instanci [Transformace](https://docs.microsoft.com/rest/api/media/transforms), musíte určit, co má být jejím výstupem. Objekt **TransformOutput** v níže uvedeném kódu je povinný parametr. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Předvolba** popisuje podrobné pokyny operací zpracování videa nebo zvuku, které se používají ke generování požadovaného objektu **TransformOutput**. Ukázka popsaná v tomto článku používá předdefinovanou předvolbu s názvem **AdaptiveStreaming**. Přednastavení zakóduje vstupní video do automaticky generovaného žebříku datového toku (dvojice s přenosovým tokem a rozlišením) na základě vstupního rozlišení a datového toku a poté vytvoří soubory ISO MP4 s videem H.264 a zvukem AAC odpovídajícím každému páru s rozlišením datového toku.

Před vytvořením nové [transformace](https://docs.microsoft.com/rest/api/media/transforms)nejprve zkontrolujte, zda již existuje pomocí **Get** metoda, jak je znázorněno v kódu, který následuje. Pokud entita v Media Services v3 neexistuje, metoda **Get** vrátí hodnotu **null** (v názvu se nerozlišují malá a velká písmena).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Spuštění úlohy

Jak je uvedeno výše, objekt [Transformace](https://docs.microsoft.com/rest/api/media/transforms) je předpis a [Úloha](https://docs.microsoft.com/rest/api/media/jobs) je vlastní požadavek na službu Media Services, aby **transformaci** použila na daný vstupní videoobsah nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění výstupu.

V tomto kurzu vytvoříme vstup úlohy na základě souboru, který je ingestován přímo ze [zdrojové adresy URL HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Čekání na dokončení úlohy

Dokončení úlohy nějakou dobu trvá. Když se tak stane, chcete být upozorněni. Ukázka kódu níže ukazuje, jak dotazování služby pro stav [úlohy](https://docs.microsoft.com/rest/api/media/jobs). Dotazování není doporučeným osvědčeným postupem pro produkční aplikace z důvodu potenciální latence. Pokud se dotazování u některého účtu používá nadměrně, je možné ho omezit. Místo dotazování by vývojáři měli používat službu Event Grid. Další informace naleznete v [tématu Route events to a custom web endpoint](job-state-events-cli-how-to.md).

**Úloha** obvykle prochází následujícími stavy: **Naplánováno**, **Ve frontě**, **Zpracovávání** a **Dokončeno** (konečný stav). Pokud úloha narazila na chybu, zobrazí se stav **Chyba.** Pokud je úloha v procesu zrušení, dostanete **zrušení** a **zrušení,** když je hotovo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Vytvoření zásad klíče obsahu

Symetrický klíč poskytuje zabezpečený přístup k vašim prostředkům. Je třeba vytvořit **zásady klíče obsahu,** které konfiguruje způsob doručení klíče obsahu koncovým klientům. Klíč obsahu je přidružen k **lokátoru streamování**. Služba Media Services také poskytuje službu doručování klíčů, která poskytuje šifrovací klíče oprávněným uživatelům.

Pokud je datový proud požadován přehrávačem, služba Media Services používá zadaný klíč k dynamickému šifrování obsahu (v tomto případě pomocí šifrování AES).) Chcete-li dešifrovat datový proud, přehrávač požaduje klíč ze služby doručování klíčů. K tomu, aby služba zjistila, jestli má daný uživatel povolené získání klíče, vyhodnocuje zásadu symetrického klíče, kterou jste pro klíč určili.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Vytvoření lokátoru streamování

Po dokončení kódování a nastavení zásady symetrického klíče spočívá další krok ve vytvoření videa ve výstupním prostředku, které budou moct klienti přehrávat. Video zpřístupníte ve dvou krocích:

1. Vytvořte [lokátor streamování](https://docs.microsoft.com/rest/api/media/streaminglocators).
2. Vytvoření adres URL pro streamování, které můžou používat klienti

Proces vytváření **lokátoru streamování se** nazývá publikování. Ve výchozím nastavení je **lokátor streamování** platný ihned po volání rozhraní API. Trvá, dokud není odstraněn, pokud nenakonfigurujete volitelný počáteční a koncový čas.

Při vytváření [lokátoru datových proudů](https://docs.microsoft.com/rest/api/media/streaminglocators)budete muset zadat požadovaný **název StreamingPolicyName**. V tomto kurzu používáme jeden z PredefinedStreamingPolicies, který říká Azure Media Services, jak publikovat obsah pro streamování. V tomto příkladu je použito šifrování Obálky AES (toto šifrování je také známé jako šifrování ClearKey, protože klíč je doručován do klienta přehrávání prostřednictvím protokolu HTTPS a nikoli licence DRM).

> [!IMPORTANT]
> Při použití vlastní [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies), měli byste navrhnout omezenou sadu těchto zásad pro váš účet Media Service a znovu použít pro vaše streamovací lokátory vždy, když jsou potřeba stejné možnosti šifrování a protokoly. Počet záznamů StreamingPolicy je pro účty služby Media Service omezený kvótou. Neměli byste vytvářet nové StreamingPolicy pro každý lokátor streamování.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Získání testovacího tokenu

V tomto kurzu určíme, že má mít zásada symetrického klíče omezení tokenu. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Media Services podporuje tokeny ve formátu [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) a to je to, co nakonfigurujeme v ukázce.

ContentKeyIdentifierClaim se používá v **zásadách klíče obsahu**, což znamená, že token prezentovaný službě doručování klíčů musí mít identifikátor klíče obsahu v něm. V ukázce jsme při vytváření lokátoru streamování nezadali klíč obsahu, systém pro nás vytvořil náhodný. Chcete-li generovat testovací token, musíme získat ContentKeyId umístit do ContentKeyIdentifierClaim deklarace.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Vytvoření adresy URL pro streamování DASH

Nyní, když byl vytvořen [lokátor streamování,](https://docs.microsoft.com/rest/api/media/streaminglocators) můžete získat adresy URL streamování. Chcete-li vytvořit adresu URL, musíte zřetězit název hostitele [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) a cestu **lokátoru streamování.** V této ukázce se používá *výchozí* **koncový bod streamování.** Při prvním vytvoření účtu služby Media Service bude tento *výchozí* **koncový bod streamování** v zastaveném stavu, takže je třeba zavolat **start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Vyčištění prostředků v účtu služby Media Services

Obecně byste měli vyčistit vše kromě objektů, které plánujete znovu použít (obvykle budete znovu používat transformace, lokátory streamování a tak dále). Pokud chcete, aby byl váš účet po experimentování čistý, odstraňte prostředky, které neplánujete znovu použít. Například následující kód odstraní úlohy:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtu úložiště, které jste vytvořili v tomto kurzu, pak tuto dříve vytvořenou skupinu prostředků odstraňte.

Proveďte následující příkaz příkazového příkazu příkazu příkazu:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Ptejte se, podávejte zpětnou vazbu, získejte aktualizace

Podívejte se na článek [komunity Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Ochrana pomocí DRM](protect-with-drm.md)
