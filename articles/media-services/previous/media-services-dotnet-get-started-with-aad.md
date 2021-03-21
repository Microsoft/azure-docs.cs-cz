---
title: Použití ověřování Azure AD pro přístup k rozhraní API Azure Media Services pomocí .NET | Microsoft Docs
description: V tomto tématu se dozvíte, jak používat ověřování pomocí Azure Active Directory (Azure AD) pro přístup k rozhraní API Azure Media Services (AMS) pomocí .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: fd19bce05d18eb8f9b8fd3806082caeebe98bdad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013988"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Použití ověřování Azure AD pro přístup k rozhraní API Azure Media Services pomocí .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

Počínaje windowsazure. MediaServices 4.0.0.4, Azure Media Services podporuje ověřování založené na Azure Active Directory (Azure AD). V tomto tématu se dozvíte, jak používat ověřování Azure AD pro přístup k rozhraní Azure Media Services API pomocí Microsoft .NET.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure: Podrobnosti najdete v článku [bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
- Účet Media Services. Další informace najdete v tématu [Vytvoření účtu Azure Media Services pomocí Azure Portal](media-services-portal-create-account.md).
- Nejnovější balíček [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) .
- Seznamte se s tématem, který [přistupuje k rozhraní Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

Pokud používáte ověřování Azure AD s Azure Media Services, můžete ověřit jedním ze dvou způsobů:

- **Ověřování uživatele** ověřuje osobu, která aplikaci používá k interakci s Azure Media Servicesmi prostředky. Interaktivní aplikace by měla nejdřív vyzvat uživatele k zadání přihlašovacích údajů. Příkladem je aplikace konzoly pro správu, kterou používají autorizovaní uživatelé k monitorování úloh kódování nebo živého streamování.
- **Ověřování instančního objektu** ověřuje službu. Aplikace, které běžně používají tuto metodu ověřování, jsou aplikace, které spouštějí služby démon, služby střední vrstvy nebo naplánované úlohy, jako jsou webové aplikace, aplikace Function App, Logic Apps, rozhraní API nebo mikroslužby.

>[!IMPORTANT]
>Azure Media Service aktuálně podporuje model ověřování Azure Access Control Service. Access Control autorizaci ale budete zastarali od 22. června 2018. Doporučujeme, abyste provedli migraci na model ověřování typu Azure Active Directory, co nejrychleji.

## <a name="get-an-azure-ad-access-token"></a>Získání přístupového tokenu Azure AD

Aby klientská aplikace mohla se připojit k rozhraní Azure Media Services API pomocí ověřování Azure AD, musí požádat o přístupový token Azure AD. Když použijete sadu SDK klienta Media Services .NET, spousta podrobností o získání přístupového tokenu Azure AD je zabalená a zjednodušená pro vás v třídách [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) a [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs) .

Například nemusíte zadávat autoritu Azure AD, Media Services identifikátor URI prostředku nebo nativní podrobnosti o aplikaci Azure AD. Jedná se o známé hodnoty, které už jsou nakonfigurovány třídou poskytovatele přístupového tokenu Azure AD.

Pokud nepoužíváte sadu Azure Media Service .NET SDK, doporučujeme použít [knihovnu ověřování Azure AD](../../active-directory/azuread-dev/active-directory-authentication-libraries.md). Pokud chcete získat hodnoty parametrů, které potřebujete použít s knihovnou ověřování Azure AD, přečtěte si téma [použití Azure Portal pro přístup k nastavení ověřování Azure AD](media-services-portal-get-started-with-aad.md).

Máte také možnost nahradit výchozí implementaci **AzureAdTokenProvider** vlastní implementací.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Instalace a konfigurace Azure Media Services .NET SDK

>[!NOTE]
>Pokud chcete používat ověřování Azure AD s Media Services .NET SDK, musíte mít nejnovější balíček [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) . Přidejte také odkaz na sestavení **Microsoft. IdentityModel. clients. Active** . Pokud používáte existující aplikaci, zahrňte **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** sestavení.

1. Vytvořte novou konzolovou aplikaci v jazyce C# v aplikaci Visual Studio.
2. K instalaci **Azure Media Services .NET SDK** použijte balíček NuGet [windowsazure. MediaServices](https://www.nuget.org/packages/windowsazure.mediaservices) .

    Chcete-li přidat odkazy pomocí NuGet, proveďte následující kroky: v **Průzkumník řešení** klikněte pravým tlačítkem myši na název projektu a pak vyberte **Spravovat balíčky NuGet**. Pak vyhledejte **windowsazure. MediaServices** a vyberte **nainstalovat**.

    -nebo-

    Spusťte následující příkaz v **konzole správce balíčků** v aplikaci Visual Studio.

    ```console
    Install-Package windowsazure.mediaservices -Version 4.0.0.4
    ```

3. Přidejte **použití** do zdrojového kódu.

    ```csharp
    using Microsoft.WindowsAzure.MediaServices.Client;
    ```

## <a name="use-user-authentication"></a>Použít ověřování uživatelů

Pokud se chcete připojit k rozhraní API služby Azure Media Service pomocí možnosti ověření uživatele, musí klientská aplikace požádat o token Azure AD pomocí následujících parametrů:

- Koncový bod tenanta Azure AD. Informace o tenantovi lze získat z Azure Portal. Najeďte myší na přihlášeného uživatele v pravém horním rohu.
- Media Services identifikátor URI prostředku.
- ID klienta aplikace Media Services (nativní).
- Identifikátor URI přesměrování aplikace Media Services (nativní).

Hodnoty pro tyto parametry lze nalézt v **AzureEnvironments. AzureCloudEnvironment**. Konstanta **AzureEnvironments. AzureCloudEnvironment** je pomocná rutinou v sadě .NET SDK pro získání správného nastavení proměnných prostředí pro veřejné datové centrum Azure.

Obsahuje předdefinovaná nastavení prostředí pro přístup k Media Services pouze ve veřejných datových centrech. V případě státních nebo státních cloudových oblastí můžete použít **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvironment** nebo **AzureGermanCloudEnvironment** .

Následující příklad kódu vytvoří token:

```csharp
var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
```

Chcete-li začít programovat proti Media Services, je nutné vytvořit instanci **CloudMediaContext** , která představuje kontext serveru. **CloudMediaContext** obsahuje odkazy na důležité kolekce, včetně úloh, prostředků, souborů, zásad přístupu a lokátorů.

Také je nutné předat **identifikátor URI prostředku pro služby Media REST Services** do konstruktoru **CloudMediaContext** . Chcete-li získat identifikátor URI prostředku pro služby Media REST Services, přihlaste se k Azure Portal, vyberte účet Azure Media Services, vyberte možnost **přístup přes rozhraní API** a pak vyberte možnost **připojit k Azure Media Services s ověřováním uživatele**.

Následující příklad kódu vytvoří instanci **CloudMediaContext** :

```csharp
CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
```

Následující příklad ukazuje, jak vytvořit token Azure AD a kontext:

```csharp
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
```

>[!NOTE]
>Pokud se zobrazí výjimka oznamující, že vzdálený server vrátil chybu: (401) Neautorizováno, "Podívejte se do části [řízení přístupu](media-services-use-aad-auth-to-access-ams-api.md#access-control) v tématu přístup k rozhraní Azure Media Services API pomocí služby Azure AD Authentication.

## <a name="use-service-principal-authentication"></a>Použít ověřování instančního objektu

Pokud se chcete připojit k rozhraní Azure Media Services API pomocí možnosti instančního objektu, musí vaše aplikace střední vrstvy (webové rozhraní API nebo webová aplikace) požadovat token Azure AD s následujícími parametry:

- Koncový bod tenanta Azure AD. Informace o tenantovi lze získat z Azure Portal. Najeďte myší na přihlášeného uživatele v pravém horním rohu.
- Media Services identifikátor URI prostředku.
- Hodnoty aplikace Azure AD: **ID klienta** a **tajný kód klienta**.

Hodnoty pro parametry **ID klienta** a **tajného klíče klienta** najdete v Azure Portal. Další informace najdete v tématu [Začínáme s ověřováním Azure AD pomocí Azure Portal](media-services-portal-get-started-with-aad.md).

Následující příklad kódu vytvoří token pomocí konstruktoru **AzureAdTokenCredentials** , který jako parametr přijímá **AzureAdClientSymmetricKey** :

```csharp
var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                        new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"),
                        AzureEnvironments.AzureCloudEnvironment);

var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
```

Můžete také zadat konstruktor **AzureAdTokenCredentials** , který jako parametr přijímá **AzureAdClientCertificate** .

Pokyny, jak vytvořit a nakonfigurovat certifikát ve formuláři, který může používat Azure AD, najdete v tématu [ověřování ve službě Azure AD v aplikacích démon pomocí certifikátů – ruční postup konfigurace](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2).

```csharp
var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                        new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"),
                        AzureEnvironments.AzureCloudEnvironment);
```

Chcete-li začít programovat proti Media Services, je nutné vytvořit instanci **CloudMediaContext** , která představuje kontext serveru. Také je nutné předat **identifikátor URI prostředku pro služby Media REST Services** do konstruktoru **CloudMediaContext** . Z Azure Portal taky můžete získat **identifikátor URI prostředku pro hodnotu Media REST Services** .

Následující příklad kódu vytvoří instanci **CloudMediaContext** :

```csharp
CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
```

Následující příklad ukazuje, jak vytvořit token Azure AD a kontext:

```csharp
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
```

## <a name="next-steps"></a>Další kroky

Začněte s [nahráváním souborů na svůj účet](media-services-dotnet-upload-files.md).
