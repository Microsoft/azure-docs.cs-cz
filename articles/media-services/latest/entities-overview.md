---
title: Přehled entity Azure Media Services – Azure | Dokumentace Microsoftu
description: Tento článek obsahuje přehled služby Azure Media Services entity.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/24/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 3f3322245983508e374d081e5d7905f67344ad7a
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54912642"
---
# <a name="azure-media-services-entities-overview"></a>Přehled entity služby Azure Media Services

Tento článek poskytuje stručný přehled entit Azure Media Services a bodů do článků na další informace o používání každé entitě v pracovních postupech služby Media Services. 

| Téma | Popis |
|---|---|
| [Účet filtry a filtry Asset](filters-dynamic-manifest-overview.md)|Při doručování obsahu zákazníkům (streamování živých událostí a videa na vyžádání) vašeho klienta může být nutné více flexibility než co je popsána v souboru manifestu výchozí Asset. Azure Media Services umožňuje definovat [filtrů účtů](https://docs.microsoft.com/rest/api/media/accountfilters) a [Asset filtry](https://docs.microsoft.com/rest/api/media/assetfilters). Potom použijte **dynamických manifestů** na základě předdefinovaných filtrů. |
| [Prostředky](assets-concept.md)|[Asset](https://docs.microsoft.com/rest/api/media/assets) entita obsahuje digitální soubory (včetně video, zvuk, obrázky, kolekci miniatur, textové stopy a soubory s titulky) a metadata o těchto souborech. Jakmile jsou digitální soubory nahrát do Assetu, můžete použít ve službě Media Services encoding, streamování, analýze obsahu pracovních postupů.|
| [Zásady klíče obsahu](content-key-policy-concept.md)|Služba Media Services můžete použít k zabezpečení médií od okamžiku opuštění počítače přes úložiště, zpracování a dodání. Pomocí služby Media Services můžete doručovat na vyžádání a živé obsah dynamicky šifrován Advanced Encryption Standard (AES-128) nebo některým z tři systémů hlavní digital rights management (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům.|
| [Události v reálném čase a živé výstupy](live-events-outputs-concept.md)|Služba Media Services umožňuje doručovat živé události do vašich zákazníků v cloudu Azure. Pokud chcete nakonfigurovat živě streamovaných událostí v Media Services v3, budete muset Další informace o [živé události](https://docs.microsoft.com/rest/api/media/liveevents) a [Live výstupy](https://docs.microsoft.com/rest/api/media/liveoutputs).|
| [Koncové body streamování](streaming-endpoint-concept.md)|A [koncové body streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints) entity reprezentuje službu streamování, která může doručovat obsah přímo do klientské aplikace přehrávače nebo do Content Delivery Network (CDN) k další distribuci. Výstupní datový proud z koncového bodu streamování služby může být živý stream nebo Asset videa na vyžádání ve vašem účtu Media Services. Při vytváření účtu Azure Media Services, **výchozí** koncový bod streamování je vytvořen v zastaveném stavu. Nelze odstranit **výchozí** koncový bod streamování. Další koncové body streamování se dají vytvořit v rámci účtu. Pokud chcete spustit streamování videa, musíte spustit koncový bod streamování ze kterého chcete Streamovat videa. |
| [Lokátory streamování](streaming-locators-concept.md)|Je potřeba zadat kódovaný klienty pomocí adresy URL, můžete použít k přehrávání video nebo zvukové soubory, je potřeba vytvořit [Lokátor streamování](https://docs.microsoft.com/rest/api/media/streaminglocators) a vytvoření adresy URL pro streamování.|
| [Streamování zásady](streaming-policy-concept.md)| [Streamování zásady](https://docs.microsoft.com/rest/api/media/streamingpolicies) vám umožňují definovat protokolů streamování a možnosti šifrování pro vaše StreamingLocators. Můžete zadat název vlastní zásady streamování jste vytvořili, nebo použijte jednu z předdefinovaných zásad streamování nabízených službou Media Services. <br/><br/>Při použití vlastních zásad streamování, by měly omezenou sadu zásad návrhu pro svůj účet Media Service a je znovu použít pro vaše lokátory streamování pokaždé, když jsou potřeba stejné možnosti šifrování a protokoly. By neměl být vytváření nových zásad streamování pro každý Lokátor streamování.|
| [Transformace a úlohy](transforms-jobs-concept.md)|Použití [transformuje](https://docs.microsoft.com/rest/api/media/transforms) konfigurace běžné úlohy kódování nebo analyzovat videa. Každý **transformace** popisuje nebudou tím správným nebo pracovního postupu úloh zpracování videa nebo zvukových souborů.<br/><br/>A [úlohy](https://docs.microsoft.com/rest/api/media/jobs) je skutečnou žádost do služby Azure Media Services použít **transformace** do daného vstupního videa nebo zvukový obsah. **Úlohy** Určuje informace, jako jsou umístění vstupním videu a umístění pro výstup. Můžete zadat umístění váš vstup videa pomocí: Adresy URL HTTPS, adresy URL SAS nebo prostředek.|

## <a name="next-steps"></a>Další postup

[Streamování souboru](stream-files-dotnet-quickstart.md)
