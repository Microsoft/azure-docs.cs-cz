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
ms.date: 02/05/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 31bf120b590ad8de3263e2d3a553f1d5fcfcd7f9
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756882"
---
# <a name="what-is-azure-media-services-v3"></a>Co jsou Azure Media Services v3?

Azure Media Services je cloudová platforma, která umožňuje vytvářet řešení zajišťující streamování videa ve vysílací kvalitě, vylepšovat dostupnost a distribuci, analyzovat obsah a nejenom to. Ať už vyvíjíte aplikace, provozujete call centrum, zastupujete orgán státní správy nebo pracujete v zábavním průmyslu, služba Media Services vám pomůže vytvořit aplikace, ve kterých zpřístupníte svůj mediální obsah široké základně uživatelů, v nejoblíbenějších mobilních zařízeních a prohlížečích, jaké dnes existují, a v prvotřídní kvalitě. 

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

## <a name="v3-capabilities"></a>Možnosti v3

V3 používá prostor Unified API, který zpřístupňuje funkce pro správu i provoz založené na Azure Resource Manageru. 

V této verzi jsou k dispozici následující funkce:  

* **Transformace** – umožňují definovat jednoduché pracovní úlohy zpracování médií a analýzy. Transformace usnadňují zpracování videosouborů a zvukových souborů. Můžete je opakovaně použít ke zpracování všech souborů v knihovně obsahu – stačí odeslat úlohu k transformaci.
* **Úlohy** na zpracování videí (zakódování nebo analýzu). Vstupní obsah se dá v úloze specifikovat jako HTTPS, URL, SAS nebo cesta k souboru v úložišti objektů Blob Azure. AMS v3 v současné době nepodporuje blokového kódování přenosu prostřednictvím adresy URL HTTPS.
* **Oznámení** – slouží ke sledování průběhu nebo stavu úlohy, ke spuštění nebo zastavení živého kanálu a k oznamování výskytu chybových událostí. Oznámení jsou integrovaná v oznamovacím systému Azure Event Gridu. V Azure Media Services se můžete snadno přihlásit k událostem z několika prostředků. 
* Šablony **správy prostředků Azure** – lze je použít k vytvoření a nasazení transformací, koncových bodů streamování, kanálů a dalších funkcí.
* **Řízení přístupu na základě role** – dá se nastavit na úrovni prostředku, a umožňuje tak uzamknout přístup ke konkrétním prostředkům, jako jsou transformace, kanály a další.
* **Klientské sady SDK** v různých jazycích: .NET, .NET Core, Python, Go, Java a Node.js.

## <a name="naming-conventions"></a>Zásady vytváření názvů

Na názvy prostředků služby Azure Media Services v3 (například prostředky, úlohy, transformace) se vztahují omezení vytváření názvů Azure Resource Manageru. V souladu s Azure Resource Managerem jsou názvy prostředků vždy jedinečné. Jako názvy prostředků tedy můžete použít jakékoli řetězce jedinečného identifikátoru (například identifikátory GUID). 

Názvy prostředků služby Media Services nemůže obsahovat znaky <, >, %, &, :, &#92;, ?, /, *, +, ., jednoduché uvozovky ani žádné řídicí znaky. Všechny ostatní znaky jsou povolené. Maximální délka názvu prostředku je 260 znaků. 

Další informace o zadávání názvů Azure Resource Manageru najdete v tématu: [Požadavky na pojmenování](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) a [zásady vytváření názvů](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="media-services-v3-api-design-principles"></a>Principy návrhu rozhraní API služby Media Services v3

Jedním z klíčových principů návrhu rozhraní API v3 je vyšší zabezpečení rozhraní API. Rozhraní API v3 nevrací tajné kódy nebo přihlašovací údaje v rámci operací **Get** nebo **List**. Klíče v odpovědi mají vždy hodnotu null, jsou prázdné nebo upravené. Abyste získali tajné kódy nebo přihlašovací údaje, je třeba volat metodu samostatné akce. Samostatné akce umožňují nastavit různá oprávnění zabezpečení RBAC v případě, že některá rozhraní API načítají nebo zobrazují tajné kódy a jiná rozhraní API ne. Informace o správě přístupu pomocí RBAC najdete v tématu popisujícím [použití RBAC pro správu přístupu](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Příklady zahrnují 

* nevracení hodnot ContentKey v operaci Get pro StreamingLocator, 
* nevracení klíče omezení v operaci Get pro ContentKeyPolicy, 
* nevracení části řetězce dotazu v adrese URL (kvůli odebrání podpisu) z adres URL vstupu HTTP úloh.

Zobrazit [získání obsahu klíče zásad – .NET](get-content-key-policy-dotnet-howto.md) příklad.

## <a name="how-can-i-get-started-with-v3"></a>Jak začít s v3?

Jako vývojář, můžete použít Media Services [rozhraní REST API](https://go.microsoft.com/fwlink/p/?linkid=873030) nebo klientských knihoven, které umožňují pracovat s rozhraním REST API snadno vytvářet, spravovat a udržovat vlastní multimediální pracovní postupy. Media Services v3 podle rozhraní API [specifikace OpenAPI](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media) (dříve označované jako Swagger).

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) je nástroj Windows zákazníkům, kteří chtějí informace o Media Services k dispozici. AMSE je jazyce Winforms /C# aplikaci, která nahrání, stažení, kódování, streamování videa na Vyžádání a živého obsahu pomocí služby Media Services. Nástroj AMSE je pro klienty, kteří chtějí testování Media Services bez psaní kódu. Kód za AMSE je určená pro klienty, kteří potřebují k vývoji Media Services a chcete zobrazit kódu produkční kvality.

AMSE je Opensourcový projekt, je podporované komunitou (jsou hlášeny problémy https://github.com/Azure/Azure-Media-Services-Explorer/issues). Tento projekt přijal [pravidla chování pro Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Další informace najdete v článku [kódu k pravidlům chování](https://opensource.microsoft.com/codeofconduct/faq/) nebo se obraťte na opencode@microsoft.com s případnými dotazy nebo připomínky.
 
Azure Media Services podporuje následujících klientských knihoven: 

|Referenční informace k rozhraní API|Sady SDK nebo nástroje|Příklady|
|---|---|---|---|
|[Referenční informace k REST](https://aka.ms/ams-v3-rest-ref)|[REST SDK](https://aka.ms/ams-v3-rest-sdk)|[Příklady REST Postman](https://github.com/Azure-Samples/media-services-v3-rest-postman)<br/>[Rozhraní REST API založené na Azure Resource Manageru](https://github.com/Azure-Samples/media-services-v3-arm-templates)|
|[Referenční informace k Azure CLI](https://aka.ms/ams-v3-cli-ref)|[Azure CLI](https://aka.ms/ams-v3-cli)|[Příklady rozhraní příkazového řádku Azure](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)||
|[Referenční informace k .NET](https://aka.ms/ams-v3-dotnet-ref)|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[Příklady v .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials)||
||[.NET Core SDK](https://aka.ms/ams-v3-dotnet-sdk) (zvolte kartu **.NET CLI**)|[Příklady v .NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials)||
|[Referenční informace k Javě](https://aka.ms/ams-v3-java-ref)|[Java SDK](https://aka.ms/ams-v3-java-sdk)||
|[Referenční informace k Node.js](https://aka.ms/ams-v3-nodejs-ref)|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk)|[Ukázky Node.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials)||
|[Referenční informace k Pythonu](https://aka.ms/ams-v3-python-ref)|[Python SDK](https://aka.ms/ams-v3-python-sdk)||
|[Referenční informace k jazyku Go](https://aka.ms/ams-v3-go-ref)|[Go SDK](https://aka.ms/ams-v3-go-sdk)||
|Ruby|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

## <a name="next-steps"></a>Další postup

Projděte si článek o [streamování souborů](stream-files-dotnet-quickstart.md), kde se dozvíte, jak je snadné začít s kódováním a streamováním videosouborů. 

