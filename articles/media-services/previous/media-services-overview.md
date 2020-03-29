---
title: Přehled služby Azure Media Services | Dokumentace Microsoftu
description: Microsoft Azure Media Services je rozšiřitelná cloudová platforma, která vývojářům umožňuje vytvářet škálovatelné aplikace pro správu a doručování médií. Tento článek poskytuje přehled Mediálních služeb Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: juliako
ms.openlocfilehash: 1c2d6287a89c7816c30cf26978859c07dba0251d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197500"
---
# <a name="azure-media-services-overview"></a>Přehled služby Azure Media Services 

> [!div class="op_single_selector" title1="Vyberte verzi služby Media Services, kterou používáte:"]
> * [Verze 3](../latest/media-services-overview.md)
> * [Verze 2](media-services-overview.md)

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Microsoft Azure Media Services (AMS) je rozšiřitelná cloudová platforma, která vývojářům umožňuje vytvářet škálovatelné aplikace pro správu a doručování médií. Služba Media Services využívá rozhraní REST API, které vám umožní bezpečně nahrávat, ukládat, kódovat a balit obsah (video nebo zvuk) doručovaný na vyžádání i v živě streamovaný různým klientům (například do televizí, počítačů a mobilních zařízení).

Pomocí Media Services můžete vytvářet pracovní postupy od začátku až do konce. V některých částech pracovního postupu můžete použít komponenty třetích stran. Můžete například kódovat pomocí kodéru třetí strany. Potom obsah nahrajete, zabezpečíte, zabalíte a doručíte pomocí služby Media Services. Svůj obsah můžete streamovat živě nebo doručovat na vyžádání. 


## <a name="compliance-privacy-and-security"></a>Dodržování předpisů, ochrana osobních údajů a zabezpečení

Jako důležité připomenutí musíte při používání Mediálních služeb Azure dodržovat všechny platné zákony a nesmíte používat mediální služby ani žádnou službu Azure způsobem, který porušuje práva ostatních nebo který může být škodlivý pro ostatní.

Před nahráním jakéhokoli videa/obrázku do mediálních služeb musíte mít veškerá příslušná práva k používání videa/obrázku, včetně všech potřebných souhlasů jednotlivců (pokud existuje) ve videu/obrázku, pro použití, zpracování a ukládání jejich dat ve službách Media Services a Azure. Některé jurisdikce mohou stanovit zvláštní právní požadavky na shromažďování, on-line zpracování a uchovávání určitých kategorií údajů, jako jsou biometrické údaje. Před použitím Media Services a Azure pro zpracování a ukládání jakýchkoli dat, na které se vztahují zvláštní právní požadavky, musíte zajistit dodržování všech takových právních požadavků, které se na vás mohou vztahovat.

Informace o dodržování předpisů, ochraně osobních údajů a zabezpečení ve službách Media Services naleznete v [centru zabezpečení společnosti](https://www.microsoft.com/trust-center/?rtc=1)Microsoft . Pokud jde o povinnosti společnosti Microsoft týkající se ochrany osobních údajů, postupy zpracování a uchovávání dat, včetně způsobu odstranění vašich dat, přečtěte si prohlášení společnosti Microsoft [o zásadách ochrany osobních údajů](https://privacy.microsoft.com/PrivacyStatement), [smluvních podmínkách online služeb](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") a [dodatku ke zpracování dat](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Používáním mediálních služeb souhlasíte s tím, že budete vázáni prohlášením o ochraně osobních údajů, dpa a prohlášením o ochraně osobních údajů.
 
## <a name="prerequisites"></a>Požadavky

Pokud chcete začít používat Azure Media Services, potřebujete následující:

* Účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com).
* Účet Azure Media Services. Další informace najdete v článku o [vytvoření účtu](media-services-portal-create-account.md).
* (Volitelné) Nastavte vývojové prostředí. Jako vývojové prostředí si zvolte .NET nebo REST API. Další informace najdete v článku o [nastavení prostředí](media-services-dotnet-how-to-use.md).

    Seznamte se také s postupem [připojení prostřednictvím kódu programu k rozhraní API pro AMS](media-services-use-aad-auth-to-access-ams-api.md).
* Koncový bod streamování Standard nebo Premium ve spuštěném stavu.  Další informace najdete v článku o [správě koncových bodů streamování](media-services-portal-manage-streaming-endpoints.md).

## <a name="sdks-and-tools"></a>Sady SDK a nástroje

Pokud chcete vytvořit řešení Media Services, můžete použít následující pomůcky:

* [Rozhraní REST API služby Media Services](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* Jednu z dostupných klientských sad SDK:
    * Sada Azure Media Services SDK pro rozhraní .NET
    
        * [Balíček NuGet](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [Zdrojový kód GitHubu](https://github.com/Azure/azure-sdk-for-media-services)
    * [Azure SDK pro Javu](https://github.com/Azure/azure-sdk-for-java),
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php),
    * [Azure Media Services pro Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Jedná se o verzi sady SDK, kterou nevytvořil Microsoft. Spravuje ji komunita a aktuálně nemá 100% pokrytí rozhraní API pro AMS.)
* Existující nástroje:
    * [Portál Azure](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) je aplikace napsaná v jazyce Winforms/C# pro Windows.)

> [!NOTE]
> Pokud chcete získat nejnovější verzi sady Java SDK a začít s vývojem v jazyce Java, přečtěte si článek [Začínáme s klientskou sadou Java SDK pro Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Pokud si chcete stáhnout nejnovější sadu PHP SDK pro službu Media Services, najděte si v [úložišti Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7) balíček Microsoft/WindowsAzure verze 0.5.7.  

## <a name="code-samples"></a>Ukázky kódů

V galerii **vzorových kódů Azure** najdete několik vzorových kódů: [Vzorové kódy pro Azure Media Services](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Koncepty

Informace o konceptech Azure Media Services najdete v článku [Koncepty](media-services-concepts.md).

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Podporované scénáře a dostupnost služby Media Services v datových centrech

Podrobné informace najdete v tématu [Scénáře a dostupnost funkcí a služeb AMS v datových centrech](scenarios-and-availability.md).

## <a name="service-level-agreement-sla"></a>Smlouvy o úrovni služeb (SLA)

Další informace najdete v článku [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/).

Informace o dostupnosti v datových centrech najdete v části [Dostupnost](scenarios-and-availability.md#availability).

## <a name="support"></a>Podpora

[Podpora Azure](https://azure.microsoft.com/support/options/) nabízí možnosti odborné pomoci pro Azure včetně služby Media Services.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
