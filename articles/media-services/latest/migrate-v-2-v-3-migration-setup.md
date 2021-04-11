---
title: Nastavení migrace Media Services V2 na V3
description: Tento článek vám pomůže s nastavením prostředí pro migraci z Azure Media Services V2 na v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, migrace, Stream, vysílání, Live, SDK
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 9d61e9ff753c37268be19e95db9450e2cd923d96
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279676"
---
# <a name="step-3---set-up-to-migrate-to-the-v3-rest-api-or-client-sdk"></a>Krok 3 – nastavení pro migraci na verzi V3 REST API nebo klientskou sadu SDK

![logo Průvodce migrací](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroky migrace 2](./media/migration-guide/steps-3.svg)

Následující článek popisuje kroky pro nastavení prostředí pro použití rozhraní API Media Services V3.

## <a name="sdk-model"></a>Model sady SDK

V rozhraní API v2 existovaly dvě různé klientské sady SDK: jeden pro rozhraní API pro správu, který povoluje programové vytváření účtů a jeden pro správu prostředků.

V minulosti mohli vývojáři pracovat s ID klienta instančního objektu Azure a s tajným klíčem klienta, a to spolu s konkrétním koncovým bodem V2 REST API pro svůj účet AMS.

Rozhraní V3 API je založené na službě Azure Resource Management (ARM). Pro připojení k rozhraní API používá Azure Active Directory (Azure AD) instančních kódů a klíčů. Vývojáři budou muset vytvořit instanční objekty nebo spravované identity pro připojení k rozhraní API. V rozhraní V3 API používá rozhraní API standardní koncové body ARM, používá podobný a konzistentní model pro všechny ostatní služby Azure.

Zákazníci, kteří dříve používali verzi 2015-10-01 rozhraní API pro správu ARM ke správě účtů v2, by měli používat verzi 2020-05-01 rozhraní API pro správu ARM podporovanou pro přístup k rozhraním API v3.

## <a name="create-a-new-media-services-account-for-testing"></a>Vytvoření nového účtu Media Services pro testování

Postupujte podle pokynů k rychlému zprovoznění a [nastavte své prostředí](setup-azure-subscription-how-to.md?tabs=portal) pomocí Azure Portal. Vyberte přístup přes rozhraní API a ověřování instančního objektu a vygenerujte nové ID aplikace Azure AD a tajné klíče pro použití s tímto testovacím účtem.

[Vytvořte účet Media Services](account-create-how-to.md?tabs=portal).
[Získat přihlašovací údaje pro přístup k Media Services rozhraní API](access-api-howto.md?tabs=portal)

## <a name="download-client-sdk-of-your-choice-and-set-up-your-environment"></a>Stažení klientské sady SDK podle vašeho výběru a nastavení prostředí

- Sady SDK dostupné [pro .NET](/dotnet/api/overview/azure/mediaservices/management), .NET Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [Python](/python/api/overview/azure/mediaservices/management), [Java](/java/api/overview/azure/mediaservices/management), [Přejít](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media)a [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md).
- Rozhraní příkazového [řádku Azure](/cli/azure/ams)   integrace pro podporu jednoduchého skriptování.

> [!NOTE]
> Sada SDK PHP pro komunitu už není dostupná pro Azure Media Services na v3. Pokud používáte PHP na v2, měli byste migrovat na REST API přímo v kódu.

## <a name="open-api-specifications"></a>Specifikace Open API

- Hodnota v3 je založena na sjednocené ploše rozhraní API, která zpřístupňuje funkce pro správu i operace založené na Azure Resource Manager. Šablony Azure Resource Manager lze použít k vytváření a nasazování transformací, koncových bodů streamování, živých událostí a dalších.

- Dokument [specifikace openapi](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01) (dříve označovaný jako Swagger) vysvětluje schéma pro všechny součásti služby.

- Všechny klientské sady SDK jsou odvozeny a vygenerovány ze specifikací Open API publikovaných na GitHubu. V době zveřejnění tohoto článku se nejnovější specifikace Open API udržují veřejně na GitHubu. Verze 2020-05-01 je [nejnovější stabilní verzí](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01).

## <a name="rest"></a>[REST](#tab/rest)

Pro volání Media Services V3 REST API použijte [metodu post](./setup-postman-rest-how-to.md) .
Přečtěte si [referenční stránky REST API](/rest/api/media/).

V kolekci po použití byste měli použít řetězec verze 2020-05-01.

## <a name="net"></a>[.NET](#tab/net)

Přečtěte si článek, [Připojte se k rozhraní Media Services V3 API s rozhraním .NET](configure-connect-dotnet-howto.md) a nastavte své prostředí.

Pokud chcete jednoduše nainstalovat nejnovější sadu SDK pomocí PackageManager, použijte následující příkaz:

```Install-Package Microsoft.Azure.Management.Media```

Nebo chcete-li nainstalovat nejnovější sadu SDK pomocí rozhraní .NET CLI, použijte následující příkaz:

```dotnet add package Microsoft.Azure.Management.Media```

Kromě toho jsou k dispozici kompletní ukázky .NET v [Azure-Samples/Media-Services-V3-dotnet](https://github.com/Azure-Samples/media-services-v3-dotnet) pro různé scénáře. Projekty v tomto úložišti ukazují, jak implementovat různé scénáře Azure Media Services pomocí verze v3.

### <a name="get-started-adjusting-your-code"></a>Začněte s úpravami kódu

Pokud `CloudMediaContext` chcete začít proces upgradu na rozhraní V3 API, prohledejte základ kódu.

Následující kód ukazuje, jak byl dříve použit rozhraní v2 API pomocí sady v2 .NET SDK. Vývojáři začínají vytvořením `CloudMediaContext` a vytvořením instance s `AzureAdTokenCredentials` objektem.

```dotnet

class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

```

## <a name="java"></a>[Java](#tab/java)

Přečtěte si článek, [Připojte se k Media Services V3 API pomocí Java](configure-connect-java-howto.md) a nastavte své prostředí.

## <a name="python"></a>[Python](#tab/python)

Přečtěte si článek, [Připojte se k Azure Media Services V3 API-Python](configure-connect-python-howto.md) a nastavte své prostředí.

## <a name="nodejs"></a>[Node.js](#tab/nodejs)

Přečtěte si článek [připojení k Azure Media Services V3 API-Node.js](configure-connect-nodejs-howto.md) k nastavení vašeho prostředí.

## <a name="ruby"></a>[Ruby](#tab/ruby)

- Získejte sadu [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) SDK na GitHubu.

## <a name="go"></a>[Přejít](#tab/go)

Stáhněte si sadu SDK [Přejít](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) .

---
