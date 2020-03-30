---
title: Přehled služby Azure Media Services v3
titleSuffix: Azure Media Services
description: Podrobný přehled Azure Media Services v3 s odkazy na rychlé starty, kurzy a ukázky kódu.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, všesměrové vysílání, live, režim offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: bd3890757377525cf9c178866a2a2fbc0791b9de
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79461006"
---
# <a name="azure-media-services-v3-overview"></a>Přehled služby Azure Media Services v3

Azure Media Services je cloudová platforma, která umožňuje vytvářet řešení zajišťující streamování videa ve vysílací kvalitě, vylepšovat dostupnost a distribuci, analyzovat obsah a nejenom to. Ať už jste vývojář aplikací, call centrum, vládní agentura nebo zábavní společnost, Media Services vám pomůže vytvářet aplikace, které poskytují mediální zážitky vynikající kvality širokému publiku na dnešních nejoblíbenějších mobilních zařízeních a prohlížečích.

Sady Media Services v3 SDK jsou založeny na [specifikaci OpenAPI (Swagger) služby Media Services v3](https://aka.ms/ams-v3-rest-sdk).

> [!NOTE]
> V současné době můžete na [webu Azure Portal:](https://portal.azure.com/) spravovat živé [události](live-events-outputs-concept.md)Media Services v3 , zobrazovat (nespravovat) [datové zdroje](assets-concept.md)v3 , získat informace [o přístupu k virtuálním i matům](access-api-portal.md). Pro všechny ostatní úlohy správy (například [Transformace a úlohy](transforms-jobs-concept.md) a [ochrana obsahu](content-protection-overview.md)) použijte rozhraní [REST API](https://docs.microsoft.com/rest/api/media/), ROZHRANÍ [CLI](https://aka.ms/ams-v3-cli-ref)nebo jednu z podporovaných [sad SDK](media-services-apis-overview.md#sdks).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Dodržování předpisů, ochrana osobních údajů a zabezpečení

Jako důležité připomenutí musíte při používání Mediálních služeb Azure dodržovat všechny platné zákony a nesmíte používat mediální služby ani žádnou službu Azure způsobem, který porušuje práva ostatních nebo který může být škodlivý pro ostatní.

Před nahráním jakéhokoli videa/obrázku do mediálních služeb musíte mít veškerá příslušná práva k používání videa/obrázku, včetně všech potřebných souhlasů jednotlivců (pokud existuje) ve videu/obrázku, pro použití, zpracování a ukládání jejich dat ve službách Media Services a Azure. Některé jurisdikce mohou stanovit zvláštní právní požadavky na shromažďování, on-line zpracování a uchovávání určitých kategorií údajů, jako jsou biometrické údaje. Před použitím Media Services a Azure pro zpracování a ukládání jakýchkoli dat, na které se vztahují zvláštní právní požadavky, musíte zajistit dodržování všech takových právních požadavků, které se na vás mohou vztahovat.

Informace o dodržování předpisů, ochraně osobních údajů a zabezpečení ve službách Media Services naleznete v [centru zabezpečení společnosti](https://www.microsoft.com/trust-center/?rtc=1)Microsoft . Pokud jde o povinnosti společnosti Microsoft týkající se ochrany osobních údajů, postupy zpracování a uchovávání dat, včetně způsobu odstranění vašich dat, přečtěte si prohlášení společnosti Microsoft [o zásadách ochrany osobních údajů](https://privacy.microsoft.com/PrivacyStatement), [smluvních podmínkách online služeb](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") a [dodatku ke zpracování dat](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Používáním mediálních služeb souhlasíte s tím, že budete vázáni prohlášením o ochraně osobních údajů, dpa a prohlášením o ochraně osobních údajů.
 
## <a name="what-can-i-do-with-media-services"></a>Co všechno jde dělat v Media Services?

Mediální služby umožňují vytvářet různé mediální pracovní postupy v cloudu. Mezi příklady, co můžete se službami Media Services dělat, patří:

* Vytvářet videa v různých formátech pro přehrávání v široké škále prohlížečů a zařízení. Pro doručování na vyžádání i živé vysílání různým klientům (mobilní zařízení, televize, počítač a tak dále) musí být video a zvukový obsah odpovídajícím způsobem zakódován a zabalen. Jak takový obsah dodávat a streamovat, se dozvíte v článku [Rychlý start: kódování a streamování souborů](stream-files-dotnet-quickstart.md).
* Streamujte živé sportovní události velkému online publiku, jako je fotbal, baseball, vysokoškolské a středoškolské sporty a další.
* Vysílat veřejná setkání a akce, jako jsou radnice, zasedání městské rady a zákonodárné orgány.
* Analyzovat zaznamenaný videoobsah a zvukový obsah. Pro větší komfort při sledování může například organizace u svého obsahu převést řeč na text nebo vytvořit index vyhledávání a řídicí panel. Může také analyzovat nejčastější témata stížností, jejich zdroje a další související data.
* Vytvořit službu odběru videa a streamovat obsah chráněný technologií DRM, pokud zákazník (například filmové studio) potřebuje omezit přístup a pracuje s díly chráněnými autorským zákonem.
* Poskytovat offline obsah pro přehrávání v letadlech, vlacích a automobilech. Zákazník si může obsah stáhnout do svého telefonu nebo tabletu pro pozdější přehrání, pokud očekává nedostupnost sítě.
* Implementujte vzdělávací platformu pro e-learningová videa pomocí Azure Media Services a [Azure Cognitive Services API](https://docs.microsoft.com/azure/?pivot=products&panel=ai) pro titulkování řeči na text, překlady do více jazyků a tak dále.
* Pomocí Mediálních služeb Azure spolu s [rozhraními API Azure Cognitive Services](https://docs.microsoft.com/azure/?pivot=products&panel=ai) můžete k videím přidávat titulky a titulky, aby bylo postaráno o širší publikum (například osoby se sluchovým postižením nebo osoby, které si chtějí číst v jiném jazyce).
* Povolte Azure CDN k dosažení velkého škálování pro lepší zpracování okamžité vysoké zatížení (například spuštění události spuštění produktu).

## <a name="how-can-i-get-started-with-v3"></a>Jak začít s v3? 

Naučte se kódovat a balit obsah, streamovat videa na vyžádání, vysílat živě a analyzovat videa pomocí Mediálních služeb v3. Kurzy, reference k rozhraní API a další dokumentace ukazují, jak bezpečně doručovat živé video a video na vyžádání nebo zvukový stream, který se škáluje milionům uživatelů.

> [!TIP]
> Než začnete vyvíjet, zkontrolujte:<br/>* [Základní pojmy](concepts-overview.md) (zahrnuje důležité pojmy, jako je balení, kódování a ochrana)<br/>* [Vývoj pomocí datových a 3 řešení api služby Media Services](media-services-apis-overview.md) (zahrnuje informace o přístupu k souborům API, konvencích pojmenování a tak dále)

### <a name="sdks"></a>Sady SDK

Začněte vyvíjet pomocí [sad SDK pro klienty Azure Media Services v3](media-services-apis-overview.md#sdks).

### <a name="quickstarts"></a>Rychlé starty  

Rychlé starty zobrazují základní pokyny pro den 1 pro nové zákazníky, aby si rychle vyzkoušeli služby Media Services.

* [Streamování videosouborů – .NET](stream-files-dotnet-quickstart.md)
* [Streamování video souborů – CLI](stream-files-cli-quickstart.md)
* [Streamování videosouborů – Node.js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>Kurzy

Kurzy ukazují postupy založené na scénářích pro některé z hlavních úloh mediálních služeb.

* [Kódování vzdáleného souboru a streamování videa – REST](stream-files-tutorial-with-rest.md)
* [Kódování načteného souboru a streamování videa – .NET](stream-files-tutorial-with-api.md)
* [Živé streamování – .NET](stream-live-tutorial-with-api.md)
* [Analýza videa – .NET](analyze-videos-tutorial-with-api.md)
* [Dynamické šifrování AES-128 – .NET](protect-with-aes128.md)

### <a name="samples"></a>ukázky

Pomocí [tohoto ukázkového prohlížeče](https://docs.microsoft.com/samples/browse/?products=azure-media-services) můžete procházet ukázky kódu Mediální služby Azure.

### <a name="how-to-guides"></a>Návody

Návody obsahují ukázky kódu, které ukazují, jak dokončit úkol. V této části najdete mnoho příkladů. Zde je několik z nich:

* [Vytvoření účtu – rozhraní příkazového řádku](create-account-cli-how-to.md)
* [Přístup k rozhraním API – rozhraní příkazového řádku](access-api-cli-how-to.md)
* [Kódování se protokolem HTTPS jako vstupem úlohy - .NET](job-input-from-http-how-to.md)  
* [Monitorování událostí – portál](monitor-events-portal-how-to.md)
* [Šifrovat dynamicky pomocí multi-DRM - .NET](protect-with-drm.md) 
* [Jak kódovat pomocí vlastní transformace - CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Ptejte se, podávejte zpětnou vazbu, získejte aktualizace

Podívejte se na článek [komunity Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="next-steps"></a>Další kroky

[Seznamte se se základními pojmy](concepts-overview.md)
