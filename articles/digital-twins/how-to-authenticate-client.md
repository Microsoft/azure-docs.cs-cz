---
title: Zápis ověřovacího kódu aplikace
titleSuffix: Azure Digital Twins
description: Viz jak psát ověřovací kód v klientské aplikaci.
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f2cef34413f46608e8bc35a009a29212af5ddf20
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893590"
---
# <a name="write-client-app-authentication-code"></a>Zápis ověřovacího kódu klientské aplikace

Po [Nastavení instance a ověřování digitálních vláken Azure](how-to-set-up-instance-portal.md)můžete vytvořit klientskou aplikaci, kterou použijete k interakci s instancí. Jakmile nastavíte projekt počátečního klienta, budete muset **v této klientské aplikaci napsat kód pro ověření** pomocí instance digitálního vlákna Azure.

Digitální vlákna Azure provádí ověřování pomocí [tokenů zabezpečení Azure AD na základě OAUTH 2,0](../active-directory/develop/security-tokens.md#json-web-tokens-jwts-and-claims). K ověření vaší sady SDK budete muset získat nosný token se správnými oprávněními pro digitální vlákna Azure a předat ho spolu s vaším voláním rozhraní API. 

Tento článek popisuje, jak získat pověření pomocí `Azure.Identity` klientské knihovny. Tento článek ukazuje příklady kódu v jazyce C#, například to, co byste napsali pro [.NET (C#) SDK](https://www.nuget.org/packages/Azure.DigitalTwins.Core), můžete použít verzi bez ohledu na to, `Azure.Identity` jakou sadu SDK používáte (Další informace o sadách SDK dostupných pro digitální vlákna Azure najdete v tématu [*Postupy: použití rozhraní API a sad SDK pro digitální vlákna Azure*](how-to-use-apis-sdks.md)).

## <a name="prerequisites"></a>Požadavky

Nejdřív dokončete kroky nastavení v tématu [*Postupy: nastavení instance a ověřování*](how-to-set-up-instance-portal.md). Tím se zajistí, že máte instanci digitálního vlákna Azure, ke které má váš uživatel oprávnění pro přístup, a Vy jste nastavili oprávnění pro klientské aplikace. Po dokončení všech těchto nastavení jste připraveni k psaní kódu klientské aplikace.

Chcete-li pokračovat, budete potřebovat projekt klientské aplikace, ve kterém budete psát kód. Pokud ještě nemáte nastavený projekt klientské aplikace, vytvořte si základní projekt v jazyce zvoleném pro použití v tomto kurzu.

## <a name="common-authentication-methods-with-azureidentity"></a>Běžné metody ověřování s Azure. identity

`Azure.Identity` je Klientská knihovna, která poskytuje několik metod získání přihlašovacích údajů, které můžete použít k získání nosného tokenu a k ověření pomocí sady SDK. I když tento článek obsahuje příklady v jazyce C#, můžete si ho prohlédnout v `Azure.Identity` několika jazycích, včetně...
* [.NET (C#)](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)
* [Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme?view=azure-java-stable&preserve-view=true)
* [JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme?view=azure-node-latest&preserve-view=true)
* [Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme?view=azure-python&preserve-view=true)

Tři běžné metody získávání přihlašovacích údajů v nástroji `Azure.Identity` :
* [DefaultAzureCredential](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) poskytuje výchozí `TokenCredential` tok ověřování pro aplikace, které se nasazují do Azure, a je **doporučená volba pro místní vývoj**. Dá se taky povolit, aby se vyzkoušely další dvě metody doporučené v tomto článku. zalomí `ManagedIdentityCredential` a má přístup k `InteractiveBrowserCredential` konfigurační proměnné.
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) funguje skvěle v případech, kdy potřebujete [spravované identity (MSI)](../active-directory/managed-identities-azure-resources/overview.md), a je vhodným kandidátem na práci s Azure functions a nasazením do služeb Azure.
* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) je určený pro interaktivní aplikace a dá se použít k vytvoření ověřeného klienta SDK.

Následující příklad ukazuje, jak použít každou z nich v sadě .NET (C#) SDK.

## <a name="authentication-examples-net-c-sdk"></a>Příklady ověřování: .NET (C#) SDK

Tato část ukazuje příklad v jazyce C# pro použití poskytnuté sady .NET SDK pro zápis ověřovacího kódu.

Nejprve zahrňte balíček sady SDK `Azure.DigitalTwins.Core` a `Azure.Identity` balíček do projektu. V závislosti na zvolených nástrojích můžete balíčky zahrnout pomocí Správce balíčků sady Visual Studio nebo `dotnet` nástroje příkazového řádku. 

Také budete muset do kódu projektu přidat následující příkazy using:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

Pak přidejte kód pro získání přihlašovacích údajů pomocí jedné z metod v `Azure.Identity` .

### <a name="defaultazurecredential-method"></a>Metoda DefaultAzureCredential

[DefaultAzureCredential](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) poskytuje výchozí `TokenCredential` tok ověřování pro aplikace, které se nasazují do Azure, a je **doporučená volba pro místní vývoj**.

Pokud chcete použít výchozí přihlašovací údaje Azure, budete potřebovat adresu URL instance digitálního vlákna Azure ([pokyny, které se mají najít](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Zde je ukázka kódu pro přidání do `DefaultAzureCredential` projektu:

```csharp
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new DefaultAzureCredential();
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

### <a name="managedidentitycredential-method"></a>Metoda ManagedIdentityCredential

Metoda [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) funguje skvěle v případech, kdy potřebujete [spravované identity (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)– například při práci s Azure Functions.

To znamená, že můžete použít `ManagedIdentityCredential` ve stejném projektu jako `DefaultAzureCredential` nebo `InteractiveBrowserCredential` , chcete-li ověřit jinou součást projektu.

Pokud chcete použít výchozí přihlašovací údaje Azure, budete potřebovat adresu URL instance digitálního vlákna Azure ([pokyny, které se mají najít](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Ve funkci Azure můžete použít přihlašovací údaje spravované identity podobně jako toto:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

### <a name="interactivebrowsercredential-method"></a>Metoda InteractiveBrowserCredential

Metoda [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) je určena pro interaktivní aplikace a otevře webový prohlížeč pro ověřování. Místo toho můžete použít `DefaultAzureCredential` interaktivní ověřování v případech.

Pokud chcete použít přihlašovací údaje interaktivního prohlížeče, budete potřebovat **registraci aplikace** , která má oprávnění k rozhraním API pro digitální vlákna Azure. Postup nastavení registrace této aplikace naleznete v části [*Nastavení oprávnění přístupu pro klientské aplikace*](how-to-set-up-instance-portal.md#set-up-access-permissions-for-client-applications) v tématu *Postupy: nastavení instance a ověřování*. Po nastavení registrace aplikace budete potřebovat...
* *ID aplikace (klienta)* registrace aplikace
* *ID adresáře (tenanta)* registrace aplikace
* Adresa URL instance digitálního vlákna Azure ([pokyny k vyhledání](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))

Zde je příklad kódu pro vytvoření ověřeného klienta sady SDK pomocí nástroje `InteractiveBrowserCredential` .

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> I když můžete umístit ID klienta, ID tenanta a adresu URL instance přímo do kódu, jak je znázorněno výše, je vhodné, aby kód získal tyto hodnoty z konfiguračního souboru nebo proměnné prostředí.

#### <a name="other-notes-about-authenticating-azure-functions"></a>Další poznámky týkající se ověřování Azure Functions

Projděte si téma [*How to: Set the Azure Function for Data Processing*](how-to-create-azure-function.md) pro ucelený příklad, který vysvětluje některé důležité možnosti konfigurace v kontextu funkcí.

Chcete-li použít ověřování ve funkci, nezapomeňte na:
* [Povolení spravované identity](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* Použijte [proměnné prostředí](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp) podle potřeby.
* Přiřaďte oprávnění k aplikaci Functions, která mu umožní přístup k rozhraním API digitálních vláken. Další informace o procesech Azure Functions najdete v tématu [*Postup: nastavení funkce Azure pro zpracování dat*](how-to-create-azure-function.md).

## <a name="other-credential-methods"></a>Další metody přihlašovacích údajů

Pokud zvýrazněné scénáře ověření výše nezahrnují potřeby vaší aplikace, můžete prozkoumat jiné typy ověřování, které jsou nabízeny na [**platformě Microsoft Identity**](../active-directory/develop/v2-overview.md#getting-started). Dokumentace pro tuto platformu se zabývá dalšími scénáři ověřování uspořádanými podle typu aplikace.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak funguje zabezpečení v Azure Digital autovlákna:
* [*Koncepty: zabezpečení pro řešení digitálních vláken Azure*](concepts-security.md)

Nebo teď, když je toto ověřování nastavené, přejděte k vytváření modelů ve vaší instanci:
* [*Postupy: Správa vlastních modelů*](how-to-manage-model.md)
