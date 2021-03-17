---
title: Ověřování .NET-Service-to-Service – Data Lake Storage Gen1
description: Naučte se, jak dosáhnout ověřování služby-služba pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory pomocí sady .NET SDK.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 19b4ac619ec3e72c787efc8e9f043f42dbd8b09b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010295"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Ověřování služba-služba s Azure Data Lake Storage Gen1 pomocí sady .NET SDK
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-service-to-service-authenticate-java.md)
> * [Pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

V tomto článku se dozvíte, jak používat sadu .NET SDK k ověřování služby Service-to-Service pomocí Azure Data Lake Storage Gen1. Ověřování koncových uživatelů s Data Lake Storage Gen1 pomocí sady .NET SDK najdete v tématu [ověřování koncových uživatelů pomocí Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Požadavky
* **Visual Studio 2013 nebo vyšší**. Níže uvedené pokyny používají Visual Studio 2019.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvořte Azure Active Directory aplikaci "Web"**. Musíte dokončit kroky v [ověřování služba-služba s Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Vytvoření aplikace .NET
1. V aplikaci Visual Studio vyberte nabídku **soubor** , **Nový** a poté **projekt**.
2. Zvolte **Konzolová aplikace (.NET Framework)** a pak vyberte **Další**.
3. Do **název projektu** zadejte `CreateADLApplication` a pak vyberte **vytvořit**.

4. Přidejte do projektu balíčky NuGet.

   1. V Průzkumníku řešení klikněte pravým tlačítkem na název projektu a klikněte na možnost **Správa balíčků NuGet**.
   2. Ujistěte se, že na kartě **Správce balíčků NuGet** je položka **Zdroj balíčku** nastavena na hodnotu **nuget.org** a že je zaškrtnuto políčko **Zahrnout předběžné verze**.
   3. Vyhledejte a nainstalujte následující balíčky NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` – Tento kurz používá verzi v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – Tento kurz používá verzi v2.2.12.

        ![Přidání zdroje NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Vytvořit nový účet Azure Data Lake")
   4. Zavřete **Správce balíčků NuGet**.

5. Otevřete soubor **Program.cs**, odstraňte stávající kód a potom vložte následující příkazy, čímž přidáte odkazy na obory názvů.

```csharp
using System;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Collections.Generic;
using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

## <a name="service-to-service-authentication-with-client-secret"></a>Ověřování služba-služba s tajným klíčem klienta
Přidejte tento fragment kódu do klientské aplikace .NET. Zástupné hodnoty nahraďte hodnotami načtenými z webové aplikace Azure AD (uvedené jako předpoklad). Tento fragment kódu umožňuje **neinteraktivní ověřování vaší aplikace pomocí** Data Lake Storage Gen1 s využitím tajného klíče klienta nebo klíče pro webovou aplikaci Azure AD.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
    var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
    var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
}
```

Předchozí fragment kódu používá pomocnou funkci `GetCreds_SPI_SecretKey` . Kód pro tuto pomocnou funkci je k dispozici [na GitHubu](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Ověřování služba-služba s certifikátem

Přidejte tento fragment kódu do klientské aplikace .NET. Zástupné hodnoty nahraďte hodnotami načtenými z webové aplikace Azure AD (uvedené jako předpoklad). Tento fragment kódu umožňuje **neinteraktivní ověřování aplikace Data Lake Storage Gen1** pomocí certifikátu pro webovou aplikaci Azure AD. Pokyny k vytvoření aplikace služby Azure AD najdete v tématu [Vytvoření instančního objektu pomocí certifikátů](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
    var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
    var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
}
```

Předchozí fragment kódu používá pomocnou funkci `GetCreds_SPI_Cert` . Kód pro tuto pomocnou funkci je k dispozici [na GitHubu](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak používat ověřování služby-služba k ověřování pomocí Data Lake Storage Gen1 pomocí sady .NET SDK. Nyní se můžete podívat na následující články, které vás seznámí s postupem použití sady .NET SDK pro práci s Data Lake Storage Gen1.

* [Operace správy účtů při Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operace s daty při Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-data-operations-net-sdk.md)
