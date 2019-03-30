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
ms.openlocfilehash: 19e94fc65ddc1719c601397adfe77f8f9445e4fa
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662121"
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

## <a name="naming-conventions"></a>Zásady vytváření názvů

Na názvy prostředků služby Azure Media Services v3 (například prostředky, úlohy, transformace) se vztahují omezení vytváření názvů Azure Resource Manageru. V souladu s Azure Resource Managerem jsou názvy prostředků vždy jedinečné. Jako názvy prostředků tedy můžete použít jakékoli řetězce jedinečného identifikátoru (například identifikátory GUID). 

Názvy prostředků služby Media Services nemůže obsahovat znaky <, >, %, &, :, &#92;, ?, /, *, +, ., jednoduché uvozovky ani žádné řídicí znaky. Všechny ostatní znaky jsou povolené. Maximální délka názvu prostředku je 260 znaků. 

Další informace o zadávání názvů Azure Resource Manageru najdete v tématu: [Požadavky na pojmenování](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) a [zásady vytváření názvů](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles"></a>Principy návrhu rozhraní API v3

Jedním z klíčových principů návrhu rozhraní API v3 je vyšší zabezpečení rozhraní API. Rozhraní API v3 nevrací tajné kódy nebo přihlašovací údaje v rámci operací **Get** nebo **List**. Klíče v odpovědi mají vždy hodnotu null, jsou prázdné nebo upravené. Abyste získali tajné kódy nebo přihlašovací údaje, je třeba volat metodu samostatné akce. Samostatné akce umožňují nastavit různá oprávnění zabezpečení RBAC v případě, že některá rozhraní API načítají nebo zobrazují tajné kódy a jiná rozhraní API ne. Informace o správě přístupu pomocí RBAC najdete v tématu popisujícím [použití RBAC pro správu přístupu](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Příklady zahrnují 

* nevracení hodnot ContentKey v operaci Get pro StreamingLocator, 
* nevracení klíče omezení v operaci Get pro ContentKeyPolicy, 
* nevracení části řetězce dotazu v adrese URL (kvůli odebrání podpisu) z adres URL vstupu HTTP úloh.

Zobrazit [získání obsahu klíče zásad – .NET](get-content-key-policy-dotnet-howto.md) příklad.


## <a name="how-can-i-get-started-with-v3"></a>Jak začít s v3? 

Naučte se kódovat a balit obsah, streamování videa na vyžádání, vysílat živě, analyzovat vaše videa pomocí Media Services v3. Kurzy, reference k rozhraní API a další dokumentace ukazují, jak bezpečně doručovat živé video a video na vyžádání nebo zvukový stream, který se škáluje milionům uživatelů.

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
* [Kódování s vlastní transformace s využitím rozhraní příkazového řádku](custom-preset-cli-howto.md)

## <a name="next-steps"></a>Další postup

Jak začít s v3? 

> [!div class="nextstepaction"]
> [Další informace o základních konceptech](concepts-overview.md)<br/>
> [Vývoj s využitím rozhraní API v3 Media Services pomocí sady SDK](developers-guide.md) 

