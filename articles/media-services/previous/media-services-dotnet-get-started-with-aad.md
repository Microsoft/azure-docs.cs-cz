---
title: Ověřování pomocí služby Azure AD pro přístup k rozhraní API služby Azure Media Services s .NET | Dokumentace Microsoftu
description: Toto téma ukazuje, jak používat ověřování služby Azure Active Directory (Azure AD) pro přístup k rozhraní API služby Azure Media Services (AMS) s využitím .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: e36673c7baa03e1bcf36c149851e6455cea798fe
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945565"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Ověřování pomocí služby Azure AD pro přístup k rozhraní API služby Azure Media Services s .NET

Počínaje windowsazure.mediaservices 4.0.0.4, služba Azure Media Services podporuje ověřování založené na Azure Active Directory (Azure AD). V tomto tématu se dozvíte, jak používat ověřování Azure AD pro přístup k rozhraní API služby Azure Media Services pomocí rozhraní Microsoft .NET.

## <a name="prerequisites"></a>Požadavky

- Účet Azure. Podrobnosti najdete na stránce [bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Účet Media Services. Další informace najdete v tématu [vytvoření účtu služby Azure Media Services pomocí webu Azure portal](media-services-portal-create-account.md).
- Nejnovější [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) balíčku.
- Znalost tématu [přístup k API služby Azure Media pomocí ověřování Azure AD – přehled](media-services-use-aad-auth-to-access-ams-api.md). 

Pokud používáte ověřování Azure AD pomocí služby Azure Media Services, můžete se ověřit jedním ze dvou způsobů:

- **Ověřování uživatelů** ověřuje osoba, která používá aplikace k interakci s prostředky služby Azure Media Services. Interaktivní aplikace by měly nejprve vyzvou uživatele pro přihlašovací údaje. Příkladem je aplikace konzoly správy, který slouží ke sledování úloh kódování nebo živé streamování Autorizovaní uživatelé. 
- **Ověřování instančních objektů** služba se ověřuje. Aplikace, které se běžně používají tuto metodu ověřování jsou aplikace, na kterých běží služby démonů, služby střední vrstvy nebo naplánované úlohy, jako jsou webové aplikace, aplikace function App, logic apps, rozhraní API nebo mikroslužeb.

>[!IMPORTANT]
>Mediálních službách Azure aktuálně podporuje model ověřování Azure Access Control Service. Povolení řízení přístupu se však to přestanou používat 22. června 2018. Doporučujeme migrovat na model ověřování Azure Active Directory co nejdříve.

## <a name="get-an-azure-ad-access-token"></a>Získání tokenu přístupu Azure AD

Pro připojení k rozhraní API Azure Media Services pomocí ověřování Azure AD, klientská aplikace potřebuje požádat o přístupový token Azure AD. Při použití klienta Media Services .NET SDK řadu podrobnosti o tom, jak získat přístupový token Azure AD jsou zabalené a zjednodušená pro vás [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) a [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs)třídy. 

Například nemusíte zadejte autoritu Azure AD, identifikátor URI prostředku služby Media Services nebo nativní podrobnosti o aplikaci Azure AD. Toto jsou známé hodnoty, které jsou už nakonfigurovaná třídou zprostředkovatele tokenu přístupu Azure AD. 

Pokud nepoužíváte sadu .NET SDK služby Azure Media, doporučujeme použít [Azure AD Authentication Library](../../active-directory/develop/active-directory-authentication-libraries.md). K získání hodnot pro parametry, které je potřeba pomocí služby Azure AD Authentication Library, naleznete v tématu [přístup k nastavení ověřování Azure AD pomocí webu Azure portal](media-services-portal-get-started-with-aad.md).

Máte také možnost nahradit výchozí implementaci **AzureAdTokenProvider** s vlastní implementaci.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Instalace a konfigurace Azure Media Services .NET SDK

>[!NOTE] 
>Ověřování Azure AD pomocí Media Services .NET SDK, musíte mít nejnovější [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) balíčku. Přidat odkaz na také, **Microsoft.IdentityModel.Clients.ActiveDirectory** sestavení. Pokud používáte existující aplikaci, přidejte **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** sestavení. 

1. Vytvořte novou aplikaci konzoly C# v sadě Visual Studio.
2. Použití [windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) balíček NuGet nainstalovat **Azure Media Services .NET SDK**. 

    Přidání odkazů pomocí NuGet, postupujte následovně: v **Průzkumníka řešení**, klikněte pravým tlačítkem na název projektu a pak vyberte **Správa balíčků NuGet**. Vyhledejte **windowsazure.mediaservices** a vyberte **nainstalovat**.
    
    -nebo-

    Spuštěním následujícího příkazu **Konzola správce balíčků** v sadě Visual Studio.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Přidat **pomocí** ke zdrojovému kódu.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>Použít ověřování uživatelů

Pro připojení k rozhraní API služby Azure Media pomocí možnosti ověřování uživatelů, klientská aplikace potřebuje požádat o token Azure AD s použitím následujících parametrů:  

- Koncový bod pro tenanta Azure AD. Informace o tenantovi můžete získat z webu Azure portal. Najeďte myší přihlášeného uživatele v pravém horním rohu.
- Identifikátor URI prostředku služby Media Services.
- ID klienta aplikace služby Media Services (nativní). 
- Identifikátor URI pro přesměrování aplikace služby Media Services (nativní). 

Hodnoty těchto parametrů najdete v **AzureEnvironments.AzureCloudEnvironment**. **AzureEnvironments.AzureCloudEnvironment** konstanta je pomocné rutiny v sadě .NET SDK pro získání správné prostředí proměnné nastavení pro veřejné datového centra Azure. 

Obsahuje nastavení předem definovaných prostředí pro přístup k Media Services v pouze veřejné datových centrech. Pro cloudové oblasti suverénních nebo státní správu, můžete použít **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvrionment**, nebo **AzureGermanCloudEnvironment** v uvedeném pořadí.

Následující příklad kódu vytvoří token:
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
Pokud chcete začít programovat za Media Services, je potřeba vytvořit **CloudMediaContext** instance, která reprezentuje kontext serveru. **CloudMediaContext** obsahuje odkazy na důležité kolekcí, včetně úloh, prostředky, soubory, zásady přístupu a lokátory. 

Je také potřeba předat **identifikátor URI pro Media REST Services** k **CloudMediaContext** konstruktoru. Chcete-li získat identifikátor URI pro Media Services REST, přihlášení k webu Azure portal, vyberte svůj účet Azure Media Services, vyberte **přístup přes rozhraní API**a pak vyberte **připojení k Azure Media Services pomocí ověření uživatele**. 

Následující příklad kódu vytvoří **CloudMediaContext** instance:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

Následující příklad ukazuje, jak vytvořit token Azure AD a kontextu:

    namespace AzureADAuthSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);
    
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
            }
    
        }
    }

>[!NOTE]
>Pokud se zobrazí výjimka, která říká "vzdálený server vrátil chybu: (401) Neoprávněno" najdete v článku [řízení přístupu](media-services-use-aad-auth-to-access-ams-api.md#access-control) část přístup k API služby Azure Media Services pomocí ověřování Azure AD – Přehled.

## <a name="use-service-principal-authentication"></a>Použít ověřování instančních objektů
    
Pro připojení k rozhraní API služby Azure Media Services pomocí instančního objektu možnosti služby vaší aplikace střední vrstvy (webové rozhraní API nebo webovou aplikaci) musí požadavky tokenu Azure AD s následujícími parametry:  

- Koncový bod pro tenanta Azure AD. Informace o tenantovi můžete získat z webu Azure portal. Najeďte myší přihlášeného uživatele v pravém horním rohu.
- Identifikátor URI prostředku služby Media Services.
- Hodnoty aplikace Azure AD: **ID klienta** a **tajný kód klienta**.

Hodnoty **ID klienta** a **tajný kód klienta** parametry najdete na webu Azure Portal. Další informace najdete v tématu [Začínáme s ověřováním Azure AD pomocí webu Azure portal](media-services-portal-get-started-with-aad.md).

Následující příklad kódu vytvoří token s použitím **AzureAdTokenCredentials** konstruktor, který přijímá **AzureAdClientSymmetricKey** jako parametr: 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

Můžete také určit, **AzureAdTokenCredentials** konstruktor, který přijímá **AzureAdClientCertificate** jako parametr. 

Pokyny ohledně toho, jak vytvořit a nakonfigurovat certifikát ve formuláři, který můžete použít Azure AD, najdete v části [ověřování v Azure AD aplikace démonů s certifikáty – postup ruční konfigurace](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md).

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

Pokud chcete začít programovat za Media Services, je potřeba vytvořit **CloudMediaContext** instance, která reprezentuje kontext serveru. Je také potřeba předat **identifikátor URI pro Media REST Services** k **CloudMediaContext** konstruktoru. Můžete získat **identifikátor URI pro Media REST Services** hodnotu z portálu Azure portal.

Následující příklad kódu vytvoří **CloudMediaContext** instance:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
Následující příklad ukazuje, jak vytvořit token Azure AD a kontextu:

    namespace AzureADAuthSample
    {
    
        class Program
        {
            static void Main(string[] args)
            {
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                            new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                            AzureEnvironments.AzureCloudEnvironment);
            
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
    
                Console.ReadLine();
            }
    
        }
    }

## <a name="next-steps"></a>Další postup

Začínáme s [nahrávání souborů do účtu](media-services-dotnet-upload-files.md).
