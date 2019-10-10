---
title: Přehled Azure Media Services V3 | Microsoft Docs
description: Tento článek obsahuje podrobný přehled Media Services a obsahuje odkazy na články, kde najdete další podrobnosti.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, Stream, Broadcast, Live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 09/17/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: c6359cf2401ff198b0242243dbf6dfdf2e35ce47
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244025"
---
# <a name="azure-media-services-v3-overview"></a>Přehled Azure Media Services V3

Azure Media Services je cloudová platforma, která umožňuje vytvářet řešení, která dosahují streamování videa ve vysílání, vylepšit přístupnost a distribuci, analyzovat obsah a mnoho dalšího. Bez ohledu na to, jestli jste vývojář aplikace, centrum volání, státní úřad, entertainmentská společnost, Media Services vám pomůže vytvářet aplikace, které dodávají mediální prostředí vynikající kvality pro velké cílové skupiny na dnešních nejoblíbenějších mobilních zařízeních. a prohlížeče. 

Sady SDK Media Services V3 jsou založené na [Media Services V3 openapi Specification (Swagger)](https://aka.ms/ams-v3-rest-sdk).

> [!NOTE]
> V současné době nemůžete použít Azure Portal ke správě prostředků v3. Použijte [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref)nebo jednu z podporovaných [sad SDK](media-services-apis-overview.md#sdks).

## <a name="what-can-i-do-with-media-services"></a>Co se dá dělat s Media Services?

Media Services vám umožní vytvářet různé pracovní postupy pro média v cloudu, což je několik příkladů toho, co je možné dosáhnout pomocí Media Services.  

* Dodávejte videa v různých formátech, aby je bylo možné přehrávat na nejrůznějších prohlížečích a zařízeních. V případě doručování na vyžádání a živého streamování do různých klientů (mobilní zařízení, televizní přijímač, počítač atd.) je potřeba kódování a správné kódování videa a zvukového obsahu. Informace o tom, jak doručovat a streamovat takový obsah, najdete v tématu [rychlý Start: kódování a streamování souborů](stream-files-dotnet-quickstart.md).
* Streamujte živé sportovní události na velkou online cílovou skupinu, jako je fotbal, baseball, školní a vysoký školní Sport a další. 
* Vysílat veřejné schůzky a události, jako je město sály, schůze města a legislativní organizace.
* Analyzujte zaznamenaná videa nebo zvukový obsah. Například pro dosažení vyšší spokojenosti zákazníků mohou organizace rozbalit převod řeči na text a sestavit indexy a řídicí panely pro vyhledávání. Pak můžou extrahovat inteligentní stížnosti, zdroje stížností a další relevantní data.
* Pokud zákazník (například Movie Studio) potřebuje omezit přístup a využívat vlastní autorské dílo, můžete vytvořit službu video s předplatným a streamovat chráněný obsah DRM.
* Doručovat offline obsah pro přehrávání na plánech, vlacích a Automobiles. Zákazník může potřebovat stáhnout obsah na telefonu nebo tabletu pro přehrávání, když očekává odpojení od sítě.
* Implementujte výukovou platformu pro vzdělávání e-learningu s využitím Azure Media Services a [Azure rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) pro titulkování textu na text, překladu na více jazyků atd. 
* Pomocí Azure Media Services společně se službou [Azure rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) můžete přidat titulky a titulky do videí do služby stravování pro širší cílovou skupinu (například lidem s postižením sluchu nebo lidem, kteří chtějí načítat v jiném jazyce).
* Umožněte Azure CDN dosáhnout velkého měřítka, aby bylo možné lépe zvládnout okamžité vysoké zátěže (například začátek události spuštění produktu). 

## <a name="how-can-i-get-started-with-v3"></a>Jak můžu začít se systémem V3? 

Naučte se kódovat a zabalit obsah, streamovat videa na vyžádání, živě vysílat a analyzovat vaše videa pomocí Media Services V3. Kurzy, Reference k rozhraní API a další dokumentace ukazují, jak bezpečně doručovat datové proudy na vyžádání a živé video nebo zvukové streamy, které se škálují na miliony uživatelů.

> [!TIP]
> Než začnete s vývojem, přečtěte si:<br/>@no__t – 0[základních konceptů](concepts-overview.md) (incudes důležité koncepty: balení, kódování, ochrana atd.)<br/>* [vývoj s rozhraními api Media Services V3](media-services-apis-overview.md) (obsahuje informace o přístupu k rozhraním API, konvencím pojmenování atd.)

### <a name="quickstarts"></a>Rychlé starty  

V rychlých startech se při rychlém vyzkoušení Media Services v nových zákaznících zobrazují základní pokyny od dne 1.

* [Streamování souborů videa – .NET](stream-files-dotnet-quickstart.md)
* [Streamování souborů videa – CLI](stream-files-cli-quickstart.md)
* [Streamování souborů videa – Node. js](stream-files-nodejs-quickstart.md)
    
### <a name="tutorials"></a>Kurzy 

Kurzy ukazují postupy založené na scénářích pro některé z hlavních Media Services úloh.

* [Zakódovat vzdálené souborové video a streamování – REST](stream-files-tutorial-with-rest.md)
* [Kódování nahraného souboru a streamování videa – .NET](stream-files-tutorial-with-api.md)
* [Živé streamování – .NET](stream-live-tutorial-with-api.md)
* [Analýza videa – .NET](analyze-videos-tutorial-with-api.md)
* [Dynamické šifrování AES-128 – .NET](protect-with-aes128.md)
    
### <a name="samples"></a>Ukázky

Pomocí [tohoto prohlížeče ukázek](https://docs.microsoft.com/samples/browse/?products=azure-media-services) můžete procházet Azure Media Services ukázky kódu.

### <a name="how-to-guides"></a>Praktičtí průvodci

Články obsahují ukázky kódu, které demonstrují, jak dokončit úlohu. V této části najdete spoustu příkladů, ale tady je několik příkladů:

* [Vytvoření účtu – rozhraní příkazového řádku](create-account-cli-how-to.md)
* [Přístup k rozhraním API – CLI](access-api-cli-how-to.md)
* [Kódování pomocí protokolu HTTPS jako vstup úlohy – .NET](job-input-from-http-how-to.md)  
* [Monitorování událostí – portál](monitor-events-portal-how-to.md)
* [Dynamické šifrování pomocí více DRM – technologie .NET](protect-with-drm.md) 
* [Postup kódování pomocí vlastního transformačního rozhraní příkazového řádku](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

[Seznamte se se základními koncepty](concepts-overview.md)

