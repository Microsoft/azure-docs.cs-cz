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
ms.date: 09/25/2018
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 3641806870c3eab0b3a05f538015a72d9a0b34ef
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47221926"
---
# <a name="what-is-azure-media-services-v3"></a>Co jsou Azure Media Services v3?

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Verze 2 – GA](../previous/media-services-overview.md)
> * [Verze 3 – Preview](media-services-overview.md)

> [!NOTE]
> Nejnovější verze služby Azure Media Services je ve verzi Preview a může být označována jako v3.

Azure Media Services je cloudová platforma, která umožňuje vytvářet řešení zajišťující streamování videa ve vysílací kvalitě, vylepšovat dostupnost a distribuci, analyzovat obsah a nejenom to. Ať už vyvíjíte aplikace, provozujete call centrum, zastupujete orgán státní správy nebo pracujete v zábavním průmyslu, služba Media Services vám pomůže vytvořit aplikace, ve kterých zpřístupníte svůj mediální obsah široké základně uživatelů, v nejoblíbenějších mobilních zařízeních a prohlížečích, jaké dnes existují, a v prvotřídní kvalitě. 

## <a name="what-can-i-do-with-media-services"></a>Co všechno jde dělat v Media Services?

Ve službě Media Services můžete vytvářet různé pracovní postupy pro práci s mediálním obsahem v cloudu. Tady je pár příkladů toho, co služba Media Services umožňuje:  

* Vytvářet videa v různých formátech pro přehrávání v široké škále prohlížečů a zařízení. Videoobsah i zvukový obsah pro různé klienty (mobilní zařízení, TV, PC a další ) vyžaduje různé způsoby šifrování a zabalení – jak v případě obsahu na vyžádání, tak v případě živého streamování. Jak takový obsah dodávat a streamovat, se dozvíte v článku [Rychlý start: kódování a streamování souborů](stream-files-dotnet-quickstart.md).
* Streamovat živě fotbalové nebo baseballové zápasy, různá školní utkání a další sportovní události široké základně příjemců. 
* Vysílat veřejná jednání, jako je zasedání městské rady nebo legislativních orgánů, a podobné události.
* Analyzovat zaznamenaný videoobsah a zvukový obsah. Pro větší komfort při sledování může například organizace u svého obsahu převést řeč na text nebo vytvořit index vyhledávání a řídicí panel. Může také analyzovat nejčastější témata stížností, jejich zdroje a další související data. 
* Vytvořit službu odběru videa a streamovat obsah chráněný technologií DRM, pokud zákazník (například filmové studio) potřebuje omezit přístup a pracuje s díly chráněnými autorským zákonem.
* Poskytovat offline obsah pro přehrávání v letadlech, vlacích a automobilech. Zákazník si může obsah stáhnout do svého telefonu nebo tabletu pro pozdější přehrání, pokud očekává nedostupnost sítě.
* Přidat do videa titulky nebo skryté titulky, aby bylo dostupné pro více uživatelů (například osoby s postižením sluchu nebo kohokoli, kdo si chce obsah přečíst v jiném jazyce). 
* Implementovat do e-learningové videoplatformy Azure Media Services a [rozhraní API služby Azure Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) umožňující převod řeči na text, překlad do více jazyků apod.
* Povolit Azure CDN k zajištění širšího škálování pro lepší zvládání náhlého vysokého zatížení (například při uvádění produktu na trh). 

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

Další informace o vytváření názvů Azure Resource Manageru najdete v tématech věnovaných [požadavkům na vytváření názvů](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) a [zásadám vytváření názvů](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="media-services-v3-api-design-principles"></a>Principy návrhu rozhraní API služby Media Services v3

Jedním z klíčových principů návrhu rozhraní API v3 je vyšší zabezpečení rozhraní API. Rozhraní API v3 nevrací tajné kódy nebo přihlašovací údaje v rámci operací **Get** nebo **List**. Klíče v odpovědi mají vždy hodnotu null, jsou prázdné nebo upravené. Abyste získali tajné kódy nebo přihlašovací údaje, je třeba volat metodu samostatné akce. Samostatné akce umožňují nastavit různá oprávnění zabezpečení RBAC v případě, že některá rozhraní API načítají nebo zobrazují tajné kódy a jiná rozhraní API ne. Informace o správě přístupu pomocí RBAC najdete v tématu popisujícím [použití RBAC pro správu přístupu](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Příklady zahrnují 

* nevracení hodnot ContentKey v operaci Get pro StreamingLocator, 
* nevracení klíče omezení v operaci Get pro ContentKeyPolicy, 
* nevracení části řetězce dotazu v adrese URL (kvůli odebrání podpisu) z adres URL vstupu HTTP úloh.

Následující příklad .NET ukazuje, jak jde získat podpisový klíč z existující zásady. K získání klíče je třeba použít **GetPolicyPropertiesWithSecretsAsync**.

```csharp
private static async Task<ContentKeyPolicy> GetOrCreateContentKeyPolicyAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string contentKeyPolicyName)
{
    ContentKeyPolicy policy = await client.ContentKeyPolicies.GetAsync(resourceGroupName, accountName, contentKeyPolicyName);

    if (policy == null)
    {
        // Configure and create a new policy.
        
        . . . 
        policy = await client.ContentKeyPolicies.CreateOrUpdateAsync(resourceGroupName, accountName, contentKeyPolicyName, options);
    }
    else
    {
        var policyProperties = await client.ContentKeyPolicies.GetPolicyPropertiesWithSecretsAsync(resourceGroupName, accountName, contentKeyPolicyName);
        var restriction = policyProperties.Options[0].Restriction as ContentKeyPolicyTokenRestriction;
        if (restriction != null)
        {
            var signingKey = restriction.PrimaryVerificationKey as ContentKeyPolicySymmetricTokenKey;
            if (signingKey != null)
            {
                TokenSigningKey = signingKey.KeyValue;
            }
        }
    }

    return policy;
}
```

## <a name="how-can-i-get-started-with-v3"></a>Jak začít s v3?

Jako vývojáři můžete používat [rozhraní REST API](https://go.microsoft.com/fwlink/p/?linkid=873030) služby Media Services nebo klientské knihovny, které rozhraní REST API podporují, a snadno tak vytvářet, spravovat a udržovat vlastní pracovní postupy spojené s mediálním obsahem.  

Služba Media Services poskytuje [soubory Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media), pomocí kterých můžete generovat sady SDK pro vámi upřednostňovaný jazyk a technologii.  

Microsoft generuje a podporuje následující klientské knihovny: 

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

## <a name="next-steps"></a>Další kroky

Projděte si článek o [streamování souborů](stream-files-dotnet-quickstart.md), kde se dozvíte, jak je snadné začít s kódováním a streamováním videosouborů. 

