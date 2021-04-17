---
title: Zápis ověřovacího kódu aplikace
titleSuffix: Azure Digital Twins
description: Viz jak psát ověřovací kód v klientské aplikaci.
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 958b0de97b79b447f2570dd9c57c87f380bcd551
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589324"
---
# <a name="write-client-app-authentication-code"></a>Zápis ověřovacího kódu klientské aplikace

Po [Nastavení instance a ověřování digitálních vláken Azure](how-to-set-up-instance-portal.md)můžete vytvořit klientskou aplikaci, kterou použijete k interakci s instancí. Jakmile nastavíte projekt počátečního klienta, budete muset **v této klientské aplikaci napsat kód pro ověření** pomocí instance digitálního vlákna Azure.

Digitální vlákna Azure provádí ověřování pomocí [tokenů zabezpečení Azure AD na základě OAUTH 2,0](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). K ověření vaší sady SDK budete muset získat nosný token se správnými oprávněními pro digitální vlákna Azure a předat ho spolu s vaším voláním rozhraní API. 

Tento článek popisuje, jak získat pověření pomocí `Azure.Identity` klientské knihovny. Tento článek ukazuje příklady kódu v jazyce C#, například to, co byste napsali pro [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client), můžete použít verzi bez ohledu na to, `Azure.Identity` jakou sadu SDK používáte (Další informace o sadách SDK dostupných pro digitální vlákna Azure najdete v tématu [*Postupy: použití rozhraní API a sad SDK pro digitální vlákna Azure*](how-to-use-apis-sdks.md)).

## <a name="prerequisites"></a>Požadavky

Nejdřív dokončete kroky nastavení v tématu [*Postupy: nastavení instance a ověřování*](how-to-set-up-instance-portal.md). Tím zajistíte, že budete mít instanci digitálního vlákna Azure a že má váš uživatel oprávnění k přístupu. Po dokončení instalace jste připraveni k psaní kódu klientské aplikace.

Chcete-li pokračovat, budete potřebovat projekt klientské aplikace, ve kterém budete psát kód. Pokud ještě nemáte nastavený projekt klientské aplikace, vytvořte si základní projekt v jazyce zvoleném pro použití v tomto kurzu.

## <a name="common-authentication-methods-with-azureidentity"></a>Běžné metody ověřování s Azure. identity

`Azure.Identity` je Klientská knihovna, která poskytuje několik metod získání přihlašovacích údajů, které můžete použít k získání nosného tokenu a k ověření pomocí sady SDK. I když tento článek obsahuje příklady v jazyce C#, můžete si ho prohlédnout v `Azure.Identity` několika jazycích, včetně...

* [.NET (C#)](/dotnet/api/azure.identity)
* [Java](/java/api/overview/azure/identity-readme)
* [JavaScript](/javascript/api/overview/azure/identity-readme)
* [Python](/python/api/overview/azure/identity-readme)

Tři běžné metody získávání přihlašovacích údajů v nástroji `Azure.Identity` :

* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) poskytuje výchozí `TokenCredential` tok ověřování pro aplikace, které se nasazují do Azure, a je **doporučená volba pro místní vývoj**. Dá se taky povolit, aby se vyzkoušely další dvě metody doporučené v tomto článku. zalomí `ManagedIdentityCredential` a má přístup k `InteractiveBrowserCredential` konfigurační proměnné.
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) funguje skvěle v případech, kdy potřebujete [spravované identity (MSI)](../active-directory/managed-identities-azure-resources/overview.md), a je vhodným kandidátem na práci s Azure functions a nasazením do služeb Azure.
* [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential) je určený pro interaktivní aplikace a dá se použít k vytvoření ověřeného klienta SDK.

Následující příklad ukazuje, jak použít každou z nich v sadě .NET (C#) SDK.

## <a name="authentication-examples-net-c-sdk"></a>Příklady ověřování: .NET (C#) SDK

Tato část ukazuje příklad v jazyce C# pro použití poskytnuté sady .NET SDK pro zápis ověřovacího kódu.

Nejprve zahrňte balíček sady SDK `Azure.DigitalTwins.Core` a `Azure.Identity` balíček do projektu. V závislosti na zvolených nástrojích můžete balíčky zahrnout pomocí Správce balíčků sady Visual Studio nebo `dotnet` nástroje příkazového řádku. 

Také budete muset do kódu projektu přidat následující příkazy using:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="Azure_Digital_Twins_dependencies":::

Pak přidejte kód pro získání přihlašovacích údajů pomocí jedné z metod v `Azure.Identity` .

### <a name="defaultazurecredential-method"></a>Metoda DefaultAzureCredential

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) poskytuje výchozí `TokenCredential` tok ověřování pro aplikace, které se nasazují do Azure, a je **doporučená volba pro místní vývoj**.

Pokud chcete použít výchozí přihlašovací údaje Azure, budete potřebovat adresu URL instance digitálního vlákna Azure ([pokyny, které se mají najít](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Zde je ukázka kódu pro přidání do `DefaultAzureCredential` projektu:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_full":::

#### <a name="set-up-local-azure-credentials"></a>Nastavit místní přihlašovací údaje Azure

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>Metoda ManagedIdentityCredential

Metoda [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) funguje skvěle v případech, kdy potřebujete [spravované identity (MSI)](../active-directory/managed-identities-azure-resources/overview.md)– například při práci s Azure Functions.

To znamená, že můžete použít `ManagedIdentityCredential` ve stejném projektu jako `DefaultAzureCredential` nebo `InteractiveBrowserCredential` , chcete-li ověřit jinou součást projektu.

Pokud chcete použít výchozí přihlašovací údaje Azure, budete potřebovat adresu URL instance digitálního vlákna Azure ([pokyny, které se mají najít](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Ve funkci Azure můžete použít přihlašovací údaje spravované identity podobně jako toto:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="ManagedIdentityCredential":::

### <a name="interactivebrowsercredential-method"></a>Metoda InteractiveBrowserCredential

Metoda [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential) je určena pro interaktivní aplikace a otevře webový prohlížeč pro ověřování. Místo toho můžete použít `DefaultAzureCredential` interaktivní ověřování v případech.

Pokud chcete použít přihlašovací údaje interaktivního prohlížeče, budete potřebovat **registraci aplikace** , která má oprávnění k rozhraním API pro digitální vlákna Azure. Postup nastavení registrace této aplikace najdete v tématu [*Postupy: Vytvoření registrace aplikace*](how-to-create-app-registration.md). Po nastavení registrace aplikace budete potřebovat...
* *ID aplikace (klienta)* registrace aplikace ([pokyny, které se mají najít](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* *ID adresáře (klienta)* registrace aplikace ([pokyny k vyhledání](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* Adresa URL instance digitálního vlákna Azure ([pokyny k vyhledání](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))

Zde je příklad kódu pro vytvoření ověřeného klienta sady SDK pomocí nástroje `InteractiveBrowserCredential` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="InteractiveBrowserCredential":::

>[!NOTE]
> I když můžete umístit ID klienta, ID tenanta a adresu URL instance přímo do kódu, jak je znázorněno výše, je vhodné, aby kód získal tyto hodnoty z konfiguračního souboru nebo proměnné prostředí.

#### <a name="other-notes-about-authenticating-azure-functions"></a>Další poznámky týkající se ověřování Azure Functions

Projděte si téma [*How to: Set the Azure Function for Data Processing*](how-to-create-azure-function.md) pro ucelený příklad, který vysvětluje některé důležité možnosti konfigurace v kontextu funkcí.

Chcete-li použít ověřování ve funkci, nezapomeňte na:
* [Povolení spravované identity](../app-service/overview-managed-identity.md?tabs=dotnet)
* Použijte [proměnné prostředí](/sandbox/functions-recipes/environment-variables?tabs=csharp) podle potřeby.
* Přiřaďte oprávnění k aplikaci Functions, která mu umožní přístup k rozhraním API digitálních vláken. Další informace o procesech Azure Functions najdete v tématu [*Postup: nastavení funkce Azure pro zpracování dat*](how-to-create-azure-function.md).

## <a name="authenticate-across-tenants"></a>Ověřování mezi tenanty

Digitální vlákna Azure je služba, která podporuje jenom jednoho  [tenanta Azure Active Directory (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md): hlavní tenant z předplatného, kde se nachází instance digitálního vlákna Azure.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

Pokud potřebujete získat přístup k instanci digitálních vláken Azure pomocí instančního objektu nebo uživatelského účtu, který patří do jiného tenanta z instance, můžete všechny federované identity z jiného tenanta požádat o **token** z tenanta z domova instance digitálních vláken Azure. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

V možnostech přihlašovacích údajů ve vašem kódu můžete také zadat domovského tenanta. 

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="other-credential-methods"></a>Další metody přihlašovacích údajů

Pokud zvýrazněné scénáře ověření výše nezahrnují potřeby vaší aplikace, můžete prozkoumat jiné typy ověřování, které jsou nabízeny na [**platformě Microsoft Identity**](../active-directory/develop/v2-overview.md#getting-started). Dokumentace pro tuto platformu se zabývá dalšími scénáři ověřování uspořádanými podle typu aplikace.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak funguje zabezpečení v Azure Digital autovlákna:
* [*Koncepty: zabezpečení pro řešení digitálních vláken Azure*](concepts-security.md)

Nebo teď, když je toto ověřování nastavené, přejděte k vytváření a správě modelů v instanci:
* [*Postupy: Správa modelů DTDL*](how-to-manage-model.md)