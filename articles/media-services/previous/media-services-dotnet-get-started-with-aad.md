---
title: Použití ověřování Azure AD pro přístup k rozhraní API Azure Media Services s rozhraním .NET | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak používat ověřování Azure Active Directory (Azure AD) pro přístup k rozhraní API Azure Media Services (AMS) s rozhraním .NET.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b53fca292630ef988ee1357ea50adc4d7b7e9be5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162875"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Použití ověřování Azure AD pro přístup k rozhraní API Azure Media Services s rozhraním .NET

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Počínaje windowsazure.mediaservices 4.0.0.4 podporuje Azure Media Services ověřování na základě Azure Active Directory (Azure AD). Toto téma ukazuje, jak používat ověřování Azure AD pro přístup k rozhraní API Azure Media Services s Microsoftem .NET.

## <a name="prerequisites"></a>Požadavky

- Účet Azure. Podrobnosti najdete v [tématu bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Účet Media Services. Další informace najdete [v tématu Vytvoření účtu Mediální chod Azure pomocí portálu Azure](media-services-portal-create-account.md).
- Nejnovější [balíček NuGet.](https://www.nuget.org/packages/windowsazure.mediaservices)
- Seznamte se s tématem [Přístup k rozhraní API Azure Media Services s přehledem ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

Když používáte ověřování Azure AD pomocí Azure Media Services, můžete se ověřit jedním ze dvou způsobů:

- **Ověření uživatele** ověřuje osobu, která používá aplikaci k interakci s prostředky Azure Media Services. Interaktivní aplikace by měla nejprve vyzvat uživatele k zadání pověření. Příkladem je aplikace konzoly pro správu, kterou používají oprávnění uživatelé ke sledování úloh kódování nebo živého streamování. 
- **Ověření instančního objektu** ověřuje službu. Aplikace, které běžně používají tuto metodu ověřování jsou aplikace, které spouštějí služby daemon, služby střední vrstvy nebo naplánované úlohy, jako jsou webové aplikace, aplikace funkcí, aplikace logiky, api nebo mikroslužby.

>[!IMPORTANT]
>Azure Media Service aktuálně podporuje model ověřování služby Řízení přístupu Azure. 22. června 2018 se však autorizace řízení přístupu stane zastaralou. Doporučujeme migrovat na model ověřování služby Azure Active Directory co nejdříve.

## <a name="get-an-azure-ad-access-token"></a>Získání přístupového tokenu Azure AD

Chcete-li se připojit k rozhraní API Azure Media Services pomocí ověřování Azure AD, klientská aplikace potřebuje požádat o přístupový token Azure AD. Při použití media services .NET klienta SDK, mnoho podrobností o tom, jak získat přístupový token Azure AD jsou zabaleny a zjednodušeny pro vás ve třídách [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) a [AzureAdTokenCredentials.](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs) 

Například nemusíte poskytovat autoritu Azure AD, identifikátor URI prostředků Mediálních služeb nebo podrobnosti o nativní aplikaci Azure AD. Jedná se o dobře známé hodnoty, které jsou již nakonfigurované třídou zprostředkovatele přístupového tokenu Azure AD. 

Pokud nepoužíváte Azure Media Service .NET SDK, doporučujeme použít [Azure AD Authentication Library](../../active-directory/azuread-dev/active-directory-authentication-libraries.md). Pokud chcete získat hodnoty parametrů, které potřebujete použít s Knihovnou ověřování Azure AD, [přečtěte si informace o použití portálu Azure pro přístup k nastavení ověřování Azure AD](media-services-portal-get-started-with-aad.md).

Máte také možnost nahradit výchozí implementaci **AzureAdTokenProvider** s vlastní implementaci.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Instalace a konfigurace sady Azure Media Services .NET SDK

>[!NOTE] 
>Chcete-li použít ověřování Azure AD s sadou Media Services .NET SDK, musíte mít nejnovější balíček [NuGet.](https://www.nuget.org/packages/windowsazure.mediaservices) Přidejte také odkaz na sestavení **Microsoft.IdentityModel.Clients.ActiveDirectory.** Pokud používáte existující aplikaci, zahrňte sestavení **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll.** 

1. Vytvořte novou konzolovou aplikaci C# v sadě Visual Studio.
2. K instalaci sady Azure Media Services **.NET SDK**použijte balíček [Windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) NuGet . 

    Chcete-li přidat odkazy pomocí aplikace NuGet, proveďte následující kroky: v **Průzkumníku řešení**klepněte pravým tlačítkem myši na název projektu a vyberte **příkaz Manage NuGet packages**. Potom vyhledejte **windowsazure.mediaservices** a vyberte **Nainstalovat**.
    
    -nebo-

    Spusťte následující příkaz v **konzole Správce balíčků** v sadě Visual Studio.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Přidejte **pomocí** do zdrojového kódu.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>Použití ověřování uživatele

Chcete-li se připojit k rozhraní API služby Azure Media Service pomocí možnosti ověřování uživatele, klientská aplikace musí požádat o token Azure AD pomocí následujících parametrů:  

- Koncový bod klienta Azure AD. Informace o tenantovi se můžou načítat z webu Azure Portal. Najeďte na přihlášený uživatel v pravém horním rohu.
- Identifikátor URI prostředku mediálních služeb.
- Media Services (nativní) ID klienta aplikace. 
- Identifikátor URI přesměrovává aplikace Media Services (nativní) 

Hodnoty pro tyto parametry najdete v **AzureEnvironments.AzureCloudEnvironment**. Konstanta **AzureEnvironments.AzureCloudEnvironment** je pomocníkem v sdk .NET SDK, který získá správné nastavení proměnných prostředí pro veřejné datové centrum Azure. 

Obsahuje předdefinované nastavení prostředí pro přístup k mediálním službám pouze ve veřejných datových centrech. Pro oblasti suverénního nebo vládního cloudu můžete použít **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvironment**nebo **AzureGermanCloudEnvironment.**

Následující příklad kódu vytvoří token:
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
Chcete-li spustit programování proti Media Services, musíte vytvořit instanci **CloudMediaContext,** která představuje kontext serveru. **CloudMediaContext** obsahuje odkazy na důležité kolekce, včetně úlohy, prostředky, soubory, zásady přístupu a lokátory. 

Také je třeba předat **uri prostředek pro media REST Services** konstruktoru **CloudMediaContext.** Pokud chcete získat identifikátor URI prostředku pro služby Media REST Services, přihlaste se na portál Azure, vyberte účet Azure Media Services, vyberte **přístup k rozhraní API**a pak vyberte Připojit k Mediální službě Azure pomocí ověřování **uživatelů**. 

Následující příklad kódu vytvoří instanci **CloudMediaContext:**

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

Následující příklad ukazuje, jak vytvořit token Azure AD a kontext:

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
>Pokud se zobrazí výjimka s nápisem "Vzdálený server vrátil chybu: (401) Neautorizováno", přečtěte si část [Řízení přístupu](media-services-use-aad-auth-to-access-ams-api.md#access-control) v rozhraní Accessing Azure Media Services API s přehledem ověřování Azure AD.

## <a name="use-service-principal-authentication"></a>Použití ověřování instančního objektu
    
Chcete-li se připojit k rozhraní API Mediálních služeb Azure pomocí možnosti instančního objektu služby, musí vaše aplikace střední vrstvy (webové rozhraní API nebo webová aplikace) požadovat token Azure AD s následujícími parametry:  

- Koncový bod klienta Azure AD. Informace o tenantovi se můžou načítat z webu Azure Portal. Najeďte na přihlášený uživatel v pravém horním rohu.
- Identifikátor URI prostředku mediálních služeb.
- Hodnoty aplikace Azure AD: **ID klienta** a **tajný klíč klienta**.

Hodnoty pro **ID klienta** a parametry **tajného klíče klienta** najdete na webu Azure Portal. Další informace najdete [v tématu Začínáme s ověřováním Azure AD pomocí portálu Azure](media-services-portal-get-started-with-aad.md).

Následující příklad kódu vytvoří token pomocí konstruktoru **AzureAdTokenCredentials,** který přebírá **AzureAdClientSymmetricKey** jako parametr: 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

Můžete také zadat konstruktor **AzureAdTokenCredentials,** který přebírá **AzureAdClientCertificate** jako parametr. 

Pokyny k vytvoření a konfiguraci certifikátu ve formuláři, který může používat Služba Azure AD, najdete v tématu [Ověřování do Azure AD v aplikacích pro daemon s certifikáty – ruční kroky konfigurace](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md).

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

Chcete-li spustit programování proti Media Services, musíte vytvořit instanci **CloudMediaContext,** která představuje kontext serveru. Také je třeba předat **uri prostředek pro media REST Services** konstruktoru **CloudMediaContext.** Uri prostředků **pro media REST Services** můžete získat také z portálu Azure.

Následující příklad kódu vytvoří instanci **CloudMediaContext:**

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
Následující příklad ukazuje, jak vytvořit token Azure AD a kontext:

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

## <a name="next-steps"></a>Další kroky

Začínáme s [nahráváním souborů do svého účtu](media-services-dotnet-upload-files.md).
