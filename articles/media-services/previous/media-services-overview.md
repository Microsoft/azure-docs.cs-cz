---
title: Přehled služby Azure Media Services | Dokumentace Microsoftu
description: Microsoft Azure Media Services je rozšiřitelná cloudová platforma, která vývojářům umožňuje vytvářet škálovatelné aplikace pro správu a doručování médií. Tento článek poskytuje přehled Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: inhenkel
ms.openlocfilehash: c6f889963e87d900040d1fe77031f943ab368837
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348740"
---
# <a name="azure-media-services-overview"></a>Přehled služby Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Vyberte verzi Media Services, kterou používáte:"]
> * [Verze 3](../latest/media-services-overview.md)
> * [Verze 2](media-services-overview.md)

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-from-v2-to-v3.md) .

Microsoft Azure Media Services (AMS) je rozšiřitelná cloudová platforma, která vývojářům umožňuje vytvářet škálovatelné aplikace pro správu a doručování médií. Služba Media Services využívá rozhraní REST API, které vám umožní bezpečně nahrávat, ukládat, kódovat a balit obsah (video nebo zvuk) doručovaný na vyžádání i v živě streamovaný různým klientům (například do televizí, počítačů a mobilních zařízení).

Pomocí Media Services můžete vytvářet pracovní postupy od začátku až do konce. V některých částech pracovního postupu můžete použít komponenty třetích stran. Můžete například kódovat pomocí kodéru třetí strany. Potom obsah nahrajete, zabezpečíte, zabalíte a doručíte pomocí služby Media Services. Svůj obsah můžete streamovat živě nebo doručovat na vyžádání. 


## <a name="compliance-privacy-and-security"></a>Dodržování předpisů, ochrana osobních údajů a zabezpečení

Důležitou připomínkou je, že je nutné dodržovat všechny použitelné zákony v používání Azure Media Services a nesmíte používat Media Services ani žádnou službu Azure způsobem, který porušuje práva ostatních nebo kteří můžou být pro ostatní škodlivé.

Před nahráním videa nebo obrázku do Media Services musíte mít všechna správná práva k používání videa nebo obrázku, včetně, pokud to vyžaduje zákon, všech nezbytných souhlasů od jednotlivců (pokud existují) na videu nebo obrázku, pro použití, zpracování a ukládání svých dat v Media Services a Azure. Některé jurisdikce můžou u této kolekce stanovit zvláštní zákonné požadavky, online zpracování a ukládání určitých kategorií dat, jako je biometriková data. Než začnete používat Media Services a Azure pro zpracování a ukládání jakýchkoli dat, která se vztahují na zvláštní zákonné požadavky, musíte zajistit dodržování předpisů u všech právních požadavků, které se na vás můžou vztahovat.

Informace o dodržování předpisů, ochraně osobních údajů a zabezpečení v Media Services najdete na webu Microsoft [Trust Center](https://www.microsoft.com/trust-center/?rtc=1). Pro závazky společnosti Microsoft, postupy pro zpracování a uchovávání dat, včetně toho, jak odstranit vaše data, přečtěte si [prohlášení o zásadách ochrany osobních údajů](https://privacy.microsoft.com/PrivacyStatement)od Microsoftu, [podmínky používání služeb Online Services](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") a [doplněk pro zpracování dat](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (DPA). Pomocí Media Services souhlasíte s tím, že DPA a prohlášením o zásadách ochrany osobních údajů.
 
## <a name="prerequisites"></a>Požadavky

Pokud chcete začít používat Azure Media Services, potřebujete následující:

* Účet Azure: Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com).
* Účet Azure Media Services. Další informace najdete v článku o [vytvoření účtu](media-services-portal-create-account.md).
* (Volitelné) Nastavte vývojové prostředí. Jako vývojové prostředí si zvolte .NET nebo REST API. Další informace najdete v článku o [nastavení prostředí](media-services-dotnet-how-to-use.md).

    Seznamte se také s postupem [připojení prostřednictvím kódu programu k rozhraní API pro AMS](media-services-use-aad-auth-to-access-ams-api.md).
* Koncový bod streamování Standard nebo Premium ve spuštěném stavu.  Další informace najdete v článku o [správě koncových bodů streamování](media-services-portal-manage-streaming-endpoints.md).

## <a name="sdks-and-tools"></a>Sady SDK a nástroje

Pokud chcete vytvořit řešení Media Services, můžete použít následující pomůcky:

* [Rozhraní REST API služby Media Services](/rest/api/media/operations/azure-media-services-rest-api-reference)
* Jednu z dostupných klientských sad SDK:
    * Sada SDK Azure Media Services pro .NET
    
        * [Balíček NuGet](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [Zdrojový kód GitHubu](https://github.com/Azure/azure-sdk-for-media-services)
    * [Sada Azure SDK pro jazyk Java](https://github.com/Azure/azure-sdk-for-java),
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php),
    * [Azure Media Services pro Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Jedná se o verzi sady SDK, kterou nevytvořil Microsoft. Spravuje ji komunita a aktuálně nemá 100% pokrytí rozhraní API pro AMS.)
* Existující nástroje:
    * [Azure Portal](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) je aplikace napsaná v jazyce Winforms/C# pro Windows.)

> [!NOTE]
> Pokud chcete získat nejnovější verzi sady Java SDK a začít s vývojem v jazyce Java, přečtěte si článek [Začínáme s klientskou sadou Java SDK pro Media Services](./media-services-java-how-to-use.md). <br/>
> Pokud si chcete stáhnout nejnovější sadu PHP SDK pro službu Media Services, najděte si v [úložišti Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7) balíček Microsoft/WindowsAzure verze 0.5.7.  

## <a name="code-samples"></a>Ukázky kódů

V galerii **vzorových kódů Azure** najdete několik vzorových kódů: [Vzorové kódy pro Azure Media Services](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Koncepty

Informace o konceptech Azure Media Services najdete v článku [Koncepty](media-services-concepts.md).

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Podporované scénáře a dostupnost služby Media Services v datových centrech

Další informace o běžných scénářích Azure najdete v tématu věnovaném [scénářům AMS](scenarios-and-availability.md).
Informace o regionální dostupnosti najdete v tématu [dostupnost služby Media Service](availability-regions-v-2.md).

## <a name="service-level-agreement-sla"></a>Smlouvy o úrovni služeb (SLA)

Další informace najdete v článku [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/).

## <a name="support"></a>Podpora

[Podpora Azure](https://azure.microsoft.com/support/options/) nabízí možnosti odborné pomoci pro Azure včetně služby Media Services.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
