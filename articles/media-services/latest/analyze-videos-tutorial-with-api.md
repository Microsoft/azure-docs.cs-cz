---
title: Analýza videa pomocí služby Azure Media Services | Microsoft Docs
description: Pokud chcete analyzovat video pomocí služby Azure Media Services, postupujte podle kroků v tomto kurzu.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/09/2018
ms.author: juliako
ms.openlocfilehash: 0fdc8c6dc9fae96a79e2ab2b05b7db3012834c1e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362290"
---
# <a name="tutorial-analyze-videos-with-azure-media-services"></a>Kurz: Analýza videa pomocí služby Azure Media Services 

Tento kurz vám ukáže, jak analyzovat video pomocí služby Azure Media Services. Existuje mnoho případů, ve kterých můžete potřebovat získat podrobnější přehled o zaznamenaném video nebo audio obsahu. Pokud chtějí organizace například dosáhnout vyšší spokojeností zákazníků, mohou převést záznamy řeči zákaznické podpory na text a vytvořit prohledávatelný katalog s rejstříky a řídicími panely. Nebo mohou získat přehledy o svých podnicích, jako je třeba seznam častých stížností, zdroje těchto stížností atd.

V tomto kurzu získáte informace o následujících postupech:    

> [!div class="checklist"]
> * Spuštění služby Azure Cloud Shell
> * Vytvoření účtu Media Services
> * Přístup k rozhraní API služby Media Services
> * Konfiguraci ukázkové aplikace
> * Podrobné prozkoumání vzorového kódu
> * Spuštění aplikace
> * Prozkoumání výstupu
> * Vyčištění prostředků

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pokud nemáte nainstalovanou sadu Visual Studio, můžete získat sadu [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).

## <a name="download-the-sample"></a>Stažení ukázky

Do svého počítače naklonujte pomocí následujícího příkazu úložiště GitHub obsahující ukázku .NET:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-sample-code-in-detail"></a>Podrobné prozkoumání vzorového kódu

Tato část prozkoumává funkce definované v souboru [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) z projektu *AnalyzeVideos*.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Začínáme používat rozhraní Media Services API se sadou .NET SDK

Pokud chcete začít používat rozhraní Media Services API se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. Nejprve musíte získat token a potom z navráceného objektu vytvořit objekt **ClientCredential**. K získání tokenu slouží objekt **ArmClientCredential** v kódu, který jste naklonovali na začátku článku.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Vytvoření výstupního prostředku k uložení výsledku úlohy 

Výstupní [prostředek](https://docs.microsoft.com/rest/api/media/assets) uloží výsledek vaší úlohy. Projekt definuje funkci **DownloadResults**, která stáhne výsledky z tohoto výstupního prostředku do výstupní složky, kde si je můžete zkontrolovat.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Vytvoření transformace a úlohy, která analyzuje videa

Když kódujete nebo zpracováváte obsah v Media Services, kódování se obvykle nastaví jako předpis. Potom stačí odeslat **Úlohu**, která tento předpis použije pro video. Když odešlete novou Úlohu pro každé nové video, použije se tento předpis pro všechna videa ve vaší knihovně. V Media Services se pro předpis používá označení **transformace**. Další informace najdete v tématu [Transformace a úlohy](transform-concept.md). Ukázka popsaná v tomto kurzu definuje předpis, který analyzuje zadané video. 

#### <a name="transform"></a>Transformace

Když vytváříte novou instanci [Transformace](https://docs.microsoft.com/rest/api/media/transforms), musíte určit, co má vytvořit jako výstup. Jak je vidět ve výše uvedeném kódu, povinným parametrem je objekt **TransformOutput**. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Předvolba** popisuje podrobné pokyny operací zpracování videa a/nebo audia, které se používají ke generování požadovaného objektu **TransformOutput**. V tomto příkladu se používá předvolba **VideoAnalyzerPreset** a jazyk („en-US“) se předává jejímu konstruktoru. Tato předvolba vám umožňuje z videa extrahovat několik audio a video přehledů. Pokud potřebujete z videa extrahovat pouze několik audio přehledů, můžete použít předvolbu **AudioAnalyzerPreset**. 

Než začnete vytvářet **transformaci**, ověřte si nejdřív pomocí metody **Get**, jestli už neexistuje (viz kód níže).  Pokud entita v Media Services v3 neexistuje, metoda **Get** vrátí hodnotu **null** (v názvu se nerozlišují malá a velká písmena).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Úloha

Jak je uvedeno výše, objekt [Transformace](https://docs.microsoft.com/rest/api/media/transforms) je předpis a [Úloha](https://docs.microsoft.com/en-us/rest/api/media/jobs) je vlastní požadavek na službu Media Services, aby **transformaci** použila na daný vstupní videoobsah nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění pro výstup. Umístění videa můžete zadat pomocí adres URL protokolu HTTPS, adres URL podpisu SAS nebo prostředků, které máte ve svém účtu služby Media Services. 

V tomto příkladu je vstupem úlohy místní video.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Čekání na dokončení úlohy

Úloze chvíli trvá, než se dokončí, a když k tomu dojde, budete na to pravděpodobně chtít upozornit. Existuje několik možností, jak se nechat na dokončení [úlohy](https://docs.microsoft.com/en-us/rest/api/media/jobs) upozornit. Nejjednodušší možností (kterou zde ukazujeme) je použití dotazování. 

Dotazování nepatří mezi doporučené postupy, jak sestavovat aplikace, protože může mít prodlevu. Pokud se dotazování u některého účtu používá nadměrně, je možné ho omezit. Místo dotazování by vývojáři měli používat službu Event Grid.

Služba Event Grid je navržená pro vysokou dostupnost, konzistentní výkon a dynamické škálování. Díky službě Event Grid můžou vaše aplikace naslouchat událostem a reagovat na ně, ať už pocházejí z kterékoli služby Azure. Události můžou pocházet i z vlastních zdrojů. Jednoduché, reaktivní zpracování událostí založené na protokolu HTTP pomáhá sestavovat efektivní řešení prostřednictvím inteligentního filtrování a směrování událostí. Další informace najdete v článku [Směrování událostí na vlastní webový koncový bod](job-state-events-cli-how-to.md).

**Úloha** obvykle prochází následujícími stavy: **Naplánováno**, **Ve frontě**, **Zpracovávání** a **Dokončeno** (konečný stav). Pokud během provádění úlohy dojde k chybě, přejde úloha do stavu **Chyba**. Když úlohu zrušíte, změní se její stav na **Rušení** a potom na **Zrušeno**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="download-the-result-of-the-job"></a>Stažení výsledku úlohy

Následující funkce stáhne výsledky z výstupního [prostředku](https://docs.microsoft.com/rest/api/media/assets) do „výstupní“ složky, abyste si mohli výsledky úlohy prohlédnout. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Vyčištění prostředků v účtu služby Media Services

Obecně platí, že byste měli vyčistit všechno kromě objektů, které máte v plánu použít znovu, (obvykle jsou to transformace, streamovací lokátory apod.). Pokud chcete účet po experimentování vyčistit, měli byste odstranit prostředky, které nemáte v plánu znovu použít. Následující kód například odstraní Úlohy.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Stisknutím kombinace kláves CTRL+F5 spusťte aplikaci *AnalyzeVideos*.

Když program spustíte, úloha vytvoří miniatury každého obličeje, který ve videu najde. Zároveň vytvoří soubor insights.json.

## <a name="examine-the-output"></a>Prozkoumání výstupu

Výstupní soubor analyzování videa se nazývá insights.json. Tento soubor obsahuje přehledy o vašem videu. Popis jednotlivých elementů nalezených v souboru JSON získáte v článku [Inteligentní funkce médií](intelligence-concept.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtu úložiště, které jste vytvořili v tomto kurzu, pak tuto dříve vytvořenou skupinu prostředků odstraňte. Můžete použít nástroj **CloudShell**.

V nástroji **CloudShell** spusťte následující příkaz:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Sady SDK služby Azure Media Services v3 nejsou bezpečné pro přístup z více vláken. Při práci s vícevláknovou aplikací byste měli pro každé vlákno vygenerovat nový objekt AzureMediaServicesClient.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Nahrávání, kódování a streamování souborů](stream-files-tutorial-with-api.md)
