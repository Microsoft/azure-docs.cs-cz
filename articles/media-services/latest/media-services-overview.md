---
title: Přehled služby Azure Media Services v3 | Microsoft Docs
description: Tento článek obsahuje základní přehled služby Media Services a odkazy na články s dalšími podrobnostmi.
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
ms.date: 03/29/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: c65c797612e179a9f3c02696d827131f5c830b3c
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755907"
---
# <a name="what-is-azure-media-services-v3"></a>Co jsou Azure Media Services v3?

Azure Media Services je cloudová platforma, která umožňuje vytvářet řešení zajišťující streamování videa ve vysílací kvalitě, vylepšovat dostupnost a distribuci, analyzovat obsah a nejenom to. Ať už vyvíjíte aplikace, provozujete call centrum, zastupujete orgán státní správy nebo pracujete v zábavním průmyslu, služba Media Services vám pomůže vytvořit aplikace, ve kterých zpřístupníte svůj mediální obsah široké základně uživatelů, v nejoblíbenějších mobilních zařízeních a prohlížečích, jaké dnes existují, a v prvotřídní kvalitě. 

> [!NOTE]
> V současné době nelze použít na webu Azure portal ke správě prostředků v3. Použití [rozhraní REST API](https://aka.ms/ams-v3-rest-ref), [rozhraní příkazového řádku](https://aka.ms/ams-v3-cli-ref), nebo jeden z podporovaných [sady SDK](developers-guide.md).

## <a name="what-can-i-do-with-media-services"></a>Co všechno jde dělat v Media Services?

Ve službě Media Services můžete vytvářet různé pracovní postupy pro práci s mediálním obsahem v cloudu. Tady je pár příkladů toho, co služba Media Services umožňuje:  

* Vytvářet videa v různých formátech pro přehrávání v široké škále prohlížečů a zařízení. Videoobsah i zvukový obsah pro různé klienty (mobilní zařízení, TV, PC a další ) vyžaduje různé způsoby šifrování a zabalení – jak v případě obsahu na vyžádání, tak v případě živého streamování. Informace o tom, k poskytování a Streamovat obsah, najdete v článku [rychlý start: Kódování a streamování soubory](stream-files-dotnet-quickstart.md).
* Streamovat živě fotbalové nebo baseballové zápasy, různá školní utkání a další sportovní události široké základně příjemců. 
* Vysílat veřejná jednání, jako je zasedání městské rady nebo legislativních orgánů, a podobné události.
* Analyzovat zaznamenaný videoobsah a zvukový obsah. Pro větší komfort při sledování může například organizace u svého obsahu převést řeč na text nebo vytvořit index vyhledávání a řídicí panel. Může také analyzovat nejčastější témata stížností, jejich zdroje a další související data.
* Vytvořit službu odběru videa a streamovat obsah chráněný technologií DRM, pokud zákazník (například filmové studio) potřebuje omezit přístup a pracuje s díly chráněnými autorským zákonem.
* Poskytovat offline obsah pro přehrávání v letadlech, vlacích a automobilech. Zákazník si může obsah stáhnout do svého telefonu nebo tabletu pro pozdější přehrání, pokud očekává nedostupnost sítě.
* Implementovat do e-learningové videoplatformy Azure Media Services a [rozhraní API služby Azure Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) umožňující převod řeči na text, překlad do více jazyků apod. 
* Používání Azure Media Services spolu s [Azure API služeb Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) přidání titulků a popisků do videa pro širší cílovou skupinu (například osobám s postižením sluchu nebo osoby, které doporučujeme přečíst si společně v jiné Jazyk).
* Povolení Azure CDN k dosažení velké škálování pro lepší zpracování náhlého vysoké zatížení (například začátek uvedení produktu na trh). 

## <a name="how-can-i-get-started-with-v3"></a>Jak začít s v3? 

Naučte se kódovat a balit obsah, streamování videa na vyžádání, vysílat živě, analyzovat vaše videa pomocí Media Services v3. Kurzy, reference k rozhraní API a další dokumentace ukazují, jak bezpečně doručovat živé video a video na vyžádání nebo zvukový stream, který se škáluje milionům uživatelů.

Než začnete s vývojem, projděte si [základní koncepty](concepts-overview.md)<br/>

### <a name="quickstarts"></a>Šablony Rychlý start  

Rychlých startech zobrazit základní 1 den pokyny pro rychlé vyzkoušení služby Media Services ji noví zákazníci.

* [Stream video soubory – .NET](stream-files-dotnet-quickstart.md)
* [Stream video soubory – rozhraní příkazového řádku](stream-files-cli-quickstart.md)
* [Stream video soubory – Node.js](stream-files-nodejs-quickstart.md)
    
### <a name="tutorials"></a>Kurzy 

Kurzy ukazují postupy založené na scénářích pro některé nejčastější úkoly Media Services.

* [Kódování vzdáleného souboru a streamování videa – REST](stream-files-tutorial-with-rest.md)
* [Kódování nahraný soubor a streamování videa – .NET](stream-files-tutorial-with-api.md)
* [Stream živě – .NET](stream-live-tutorial-with-api.md)
* [Analýza videa – .NET](analyze-videos-tutorial-with-api.md)
* [Dynamické šifrování AES-128 - .NET](protect-with-aes128.md)
    
### <a name="how-to-guides"></a>Návody

Články obsahují ukázky kódu, které ukazují, jak k dokončení úkolu. V této části najdete mnoho příkladů, tady je několik příkladů z nich:

* [Vytvoření účtu – rozhraní příkazového řádku](create-account-cli-how-to.md)
* [Přístup k rozhraní API – rozhraní příkazového řádku](access-api-cli-how-to.md)
* [Začněte vyvíjet s využitím sady SDK](developers-guide.md)
* [Kódování pomocí protokolu HTTPS jako vstup - úlohy .NET](job-input-from-http-how-to.md)  
* [Monitorování událostí – portál](monitor-events-portal-how-to.md)
* [Dynamicky šifrovat pomocí několika variant DRM – .NET](protect-with-drm.md) 
* [Kódování s vlastní transformace – rozhraní příkazového řádku](custom-preset-cli-howto.md)

## <a name="next-steps"></a>Další postup

Jak začít s v3? 

> [!div class="nextstepaction"]
> [Další informace o základních konceptech](concepts-overview.md)<br/>
> [Vývoj s využitím rozhraní API v3 Media Services pomocí sady SDK](developers-guide.md) 

