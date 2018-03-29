---
title: Ověřování pomocí Azure AD pro přístup k rozhraní API služby Azure Media Services s .NET | Microsoft Docs
description: Toto téma ukazuje, jak používat ověřování Azure Active Directory (Azure AD) pro přístup k rozhraní API Azure Media Services (AMS) pomocí rozhraní .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: 8817bb0da526769470e96b63b9c80620309757bf
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Používat ověřování Azure AD pro přístup k rozhraní API služby Azure Media Services pomocí rozhraní .NET

Počínaje windowsazure.mediaservices 4.0.0.4, Azure Media Services podporuje ověřování založené na Azure Active Directory (Azure AD). Toto téma ukazuje, jak používat ověřování Azure AD pro přístup k rozhraní API služby Azure Media Services pomocí rozhraní Microsoft .NET.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure. Podrobnosti najdete na stránce [bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Účet Media Services. Další informace najdete v tématu [vytvoření účtu Azure Media Services pomocí webu Azure portal](media-services-portal-create-account.md).
- Nejnovější [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) balíčku.
- Znalost tématu [přístup k Azure Media Services API s Přehled ověřování AAD](media-services-use-aad-auth-to-access-ams-api.md). 

Pokud používáte ověřování Azure AD pomocí služby Azure Media Services, můžete ověřovat v jednom ze dvou způsobů:

- **Ověření uživatele** ověřuje osoba, která používá aplikace komunikovat s prostředky Azure Media Services. Interaktivní aplikace by měla nejprve vyzvou uživatele k zadání přihlašovacích údajů. Příkladem je konzolovou aplikaci správy, která se používá Autorizovaní uživatelé k monitorování kódování úloh nebo živé streamování. 
- **Objekt zabezpečení ověřování služby** ověřuje služby. Aplikace, které běžně používají tuto metodu ověřování jsou aplikace, které běží služby démon, střední vrstvy služby nebo naplánované úlohy, jako jsou webové aplikace, funkce aplikací, aplikace logiky, rozhraní API nebo mikroslužeb.

>[!IMPORTANT]
>Azure Media Service aktuálně podporuje model ověřování služby Řízení přístupu Azure. Řízení přístupu autorizace však bude na 22 června 2018 zastaralá. Doporučujeme migrovat na Azure Active Directory authentication model co nejdříve.

## <a name="get-an-azure-ad-access-token"></a>Získání tokenu přístupu Azure AD

Pro připojení k Azure Media Services API pomocí ověřování Azure AD, klientská aplikace musí požádat o token přístupu Azure AD. Pokud používáte klienta Media Services .NET SDK, řadu podrobnosti o tom, jak získat přístupový token služby Azure AD jsou zabalená a zjednodušená pro v [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) a [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs)třídy. 

Například nemusíte poskytnutí autority Azure AD, identifikátor URI prostředku Media Services nebo nativní podrobností o aplikaci Azure AD. Toto jsou známé hodnoty, které už jsou nakonfigurované v třídě zprostředkovatel tokenu přístupu Azure AD. 

Pokud nepoužíváte .NET SDK služby Azure Media Service, doporučujeme použít [knihovna ověřování Azure AD](../active-directory/develop/active-directory-authentication-libraries.md). Chcete-li získat hodnoty pro parametry, které budete muset použít s knihovna ověřování Azure AD, přečtěte si téma [používat portál Azure pro přístup k nastavení ověřování Azure AD](media-services-portal-get-started-with-aad.md).

Máte také možnost nahradit výchozí implementaci **AzureAdTokenProvider** s vlastní implementaci.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Instalace a konfigurace Azure Media Services .NET SDK

>[!NOTE] 
>Pokud chcete používat ověřování Azure AD pomocí .NET SDK služby Media Services, musíte mít nejnovější [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) balíčku. Také přidat odkaz na **Microsoft.IdentityModel.Clients.ActiveDirectory** sestavení. Pokud používáte stávající aplikace, zahrnout **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** sestavení. 

1. Vytvořte novou aplikaci konzoly C# v sadě Visual Studio.
2. Použití [windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) balíček NuGet k instalaci **Azure Media Services .NET SDK**. 

    Přidání odkazů pomocí NuGet, proveďte následující kroky: v **Průzkumníku řešení**, klikněte pravým tlačítkem na název projektu a pak vyberte **spravovat balíčky NuGet balíčky**. Poté vyhledejte **windowsazure.mediaservices** a vyberte **nainstalovat**.
    
    -nebo-

    Spusťte následující příkaz **Konzola správce balíčků** v sadě Visual Studio.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Přidat **pomocí** ke zdrojovému kódu.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>Použít ověření uživatele

Pro připojení k Azure Media Service API s možností ověřování uživatelů, klientská aplikace potřebuje k vyžádání tokenu Azure AD pomocí následujících parametrů:  

- Koncový bod pro klienta Azure AD. Informace o klienta můžete načíst z portálu Azure. Podržte ukazatel nad přihlášeného uživatele v pravém horním rohu.
- Identifikátor URI prostředku Media Services.
- ID klienta aplikace Media Services (nativní). 
- Identifikátor URI pro přesměrování služby Media Services (nativní) aplikace. 

Hodnoty pro tyto parametry lze nalézt v **AzureEnvironments.AzureCloudEnvironment**. **AzureEnvironments.AzureCloudEnvironment** konstanta je pomocné rutiny v sadě SDK .NET pro získání správné prostředí proměnné nastavení pro veřejné datového centra Azure. 

Obsahuje nastavení předem definované prostředí pro přístup k Media Services v pouze veřejné datových centrech. Pro suverénní nebo státní cloudových oblastí, můžete použít **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvrionment**, nebo **AzureGermanCloudEnvironment** v uvedeném pořadí.

Následující příklad kódu vytvoří token:
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
Pokud chcete spustit programové ošetření Media Services, musíte vytvořit **CloudMediaContext** instanci, která představuje kontext serveru. **CloudMediaContext** obsahuje odkazy na důležité kolekcí včetně úloh, prostředky, soubory, zásady přístupu a lokátory. 

Musíte také předat **identifikátor URI pro Media REST Services** k **CloudMediaContext** konstruktor. Chcete-li získat identifikátor URI pro Media Services REST, přihlášený k portálu Azure vyberte svůj účet Azure Media Services, vyberte **přístup pomocí rozhraní API**a potom vyberte **připojit k Azure Media Services s ověřováním uživatele**. 

Následující příklad kódu vytvoří **CloudMediaContext** instance:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

Následující příklad ukazuje, jak vytvořit token Azure AD a kontextu:

    namespace AADAuthSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR AAD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);
    
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
>Pokud dojde k výjimce, která uvádí, že "vzdálený server vrátil chybu: (401) Neautorizováno" v tématu [řízení přístupu](media-services-use-aad-auth-to-access-ams-api.md#access-control) části přístup k API služby Azure Media Services s Přehled ověřování Azure AD.

## <a name="use-service-principal-authentication"></a>Objekt zabezpečení ověřování pomocí služby
    
Pro připojení k rozhraní API služby Azure Media Services s hlavní možností služby, aplikace střední vrstvy (webové rozhraní API nebo webovou aplikaci) musí požadavky tokenu Azure AD s následujícími parametry:  

- Koncový bod pro klienta Azure AD. Informace o klienta můžete načíst z portálu Azure. Podržte ukazatel nad přihlášeného uživatele v pravém horním rohu.
- Identifikátor URI prostředku Media Services.
- Hodnoty aplikace Azure AD: **ID klienta** a **tajný klíč klienta**.

Hodnoty **ID klienta** a **tajný klíč klienta** parametry lze nalézt v portálu Azure. Další informace najdete v tématu [Začínáme s ověřováním Azure AD pomocí portálu Azure](media-services-portal-get-started-with-aad.md).

Následující příklad kódu vytvoří token pomocí **AzureAdTokenCredentials** konstruktor, který přebírá **AzureAdClientSymmetricKey** jako parametr: 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

Můžete také určit **AzureAdTokenCredentials** konstruktor, který přebírá **AzureAdClientCertificate** jako parametr. 

Pokyny o tom, jak vytvořit a nakonfigurovat certifikát ve formuláři, který můžete použít Azure AD najdete v tématu [ověřování do služby Azure AD v aplikacích démon s certifikáty - postup ruční konfigurace](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md).

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

Pokud chcete spustit programové ošetření Media Services, musíte vytvořit **CloudMediaContext** instanci, která představuje kontext serveru. Musíte také předat **identifikátor URI pro Media REST Services** k **CloudMediaContext** konstruktor. Můžete získat **identifikátor URI pro Media REST Services** hodnotu z webu Azure portal.

Následující příklad kódu vytvoří **CloudMediaContext** instance:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
Následující příklad ukazuje, jak vytvořit token Azure AD a kontextu:

    namespace AADAuthSample
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
