---
title: Přehled služby Azure Media Services v3 | Microsoft Docs
description: Tento článek obsahuje základní přehled služby Media Services a odkazy na články s dalšími podrobnostmi.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: azure media services, stream, všesměrové vysílání, live, režim offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/27/2018
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 33afe5fb24309547a7aacfcbe3b799ed413594ac
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
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
* Implementovat do e-learningové videoplatformy Azure Media Services a [rozhraní API služby Azure Cognitive Services](https://docs.microsoft.com/en-us/azure/#pivot=products&panel=ai) umožňující převod řeči na text, překlad do více jazyků apod.
* Povolit Azure CDN k zajištění širšího škálování pro lepší zvládání náhlého vysokého zatížení (například při uvádění produktu na trh). 

## <a name="v3-capabilities"></a>Možnosti v3

V3 používá prostor Unified API, který zpřístupňuje funkce pro správu i provoz založené na **Azure Resource Manageru**. V této verzi jsou k dispozici následující funkce:  

* **Transformace** – umožňují definovat jednoduché pracovní úlohy zpracování médií a analýzy. Transformace usnadňují zpracování videosouborů a zvukových souborů. Můžete je opakovaně použít ke zpracování všech souborů v knihovně obsahu – stačí odeslat úlohu k transformaci.
* **Úlohy** na zpracování videí (zakódování nebo analýzu). Vstupní obsah se dá v úloze specifikovat jako HTTP, URL, SAS nebo cesta k souboru v úložišti objektů Blob Azure. 
* **Oznámení** – slouží ke sledování průběhu nebo stavu úlohy, ke spuštění nebo zastavení živého kanálu a k oznamování výskytu chybových událostí. Oznámení jsou integrovaná v oznamovacím systému Azure Event Gridu. V Azure Media Services se můžete snadno přihlásit k událostem z několika prostředků. 
* Šablony **správy prostředků Azure** – lze je použít k vytvoření a nasazení transformací, koncových bodů streamování, kanálů a dalších funkcí.
* **Řízení přístupu na základě role** – dá se nastavit na úrovni prostředku, a umožňuje tak uzamknout přístup ke konkrétním prostředkům, jako jsou transformace, kanály a další.
* **Klientské sady SDK** v různých jazycích: .NET, .NET Core, Python, Go, Java a Node.js.

## <a name="how-can-i-get-started-with-v3"></a>Jak začít s v3?

Jako vývojáři můžete používat [rozhraní REST API](https://go.microsoft.com/fwlink/p/?linkid=873030) služby Media Services nebo klientské knihovny, které rozhraní REST API podporují, a snadno tak vytvářet, spravovat a udržovat vlastní pracovní postupy spojené s mediálním obsahem. Microsoft generuje a podporuje následující klientské knihovny: 

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Jazyky .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0)
* .NET Core 
* Java

  Přidejte do svého projektu následující závislost:
  
  ```
  <dependency>
    <groupId>com.microsoft.azure.media-2018-03-30-preview</groupId>
    <artifactId>azure-mgmt- media</artifactId>
    <version>0.0.1-beta</version>
  </dependency> 
  ```
* Node.js 

  Použijte následující příkaz:
  
  ```
  npm install azure-arm-mediaservices
  ```
  
* [Python](https://pypi.org/project/azure-mgmt-media/1.0.0rc1/)
* [Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/mediaservices/mgmt/2018-03-30-preview/media)

Služba Media Services poskytuje [soubory Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media), pomocí kterých můžete generovat sady SDK pro vámi upřednostňovaný jazyk a technologii.  

## <a name="next-steps"></a>Další kroky

Projděte si článek o [streamování souborů](stream-files-dotnet-quickstart.md), kde se dozvíte, jak je snadné začít s kódováním a streamováním videosouborů. 

