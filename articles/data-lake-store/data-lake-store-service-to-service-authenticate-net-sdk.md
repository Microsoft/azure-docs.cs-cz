---
title: 'Ověřování mezi službami: .NET SDK s Azure Data Lake Storage Gen1 pomocí Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak dosáhnout ověřování mezi službami pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory pomocí .NET SDK
services: data-lake-store
documentationcenter: ''
author: twooley
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 96c496ef67e26a3079577bf52e9d019d963467b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265529"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Ověřování mezi službami pomocí Azure Data Lake Storage Gen1 pomocí sady .NET SDK
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-service-to-service-authenticate-java.md)
> * [Pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

V tomto článku se dozvíte, jak pomocí sady .NET SDK provést ověřování mezi službami pomocí Azure Data Lake Storage Gen1. Ověřování u koncových uživatelů pomocí sady Data Lake Storage Gen1 pomocí sady .NET SDK najdete [v tématu Ověřování koncových uživatelů pomocí sady Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Požadavky
* **Visual Studio 2013 nebo vyšší**. Níže uvedené pokyny používají Visual Studio 2019.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvořte "webovou" aplikaci služby Azure Active Directory**. Kroky ověřování mezi službami musíte provést [pomocí služby Data Lake Storage Gen1 pomocí služby Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Vytvoření aplikace .NET
1. V sadě Visual Studio vyberte nabídku **Soubor,** **Nový**a potom **aplikaci Project**.
2. Zvolte **Konzolová aplikace (.NET Framework)** a pak vyberte **Další**.
3. V **názvu**projektu `CreateADLApplication`zadejte a vyberte **Vytvořit**.

4. Přidejte do projektu balíčky NuGet.

   1. V Průzkumníku řešení klikněte pravým tlačítkem na název projektu a klikněte na možnost **Správa balíčků NuGet**.
   2. Ujistěte se, že na kartě **Správce balíčků NuGet** je položka **Zdroj balíčku** nastavena na hodnotu **nuget.org** a že je zaškrtnuto políčko **Zahrnout předběžné verze**.
   3. Vyhledejte a nainstalujte následující balíčky NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` – Tento kurz používá verzi v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – Tento kurz používá verzi v2.2.12.

        ![Přidání zdroje NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Vytvoření nového účtu Azure Data Lake")
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

## <a name="service-to-service-authentication-with-client-secret"></a>Ověřování mezi službami s tajným klíči klienta
Přidejte tento úryvek do klientské aplikace .NET. Nahraďte zástupné hodnoty hodnotami načtenými z webové aplikace Azure AD (uvedené jako předpoklad). Tento fragment umožňuje ověřit vaši aplikaci **neinteraktivně** pomocí Data Lake Storage Gen1 pomocí tajného klíče klienta nebo klíče pro webovou aplikaci Azure AD.

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

Předchozí úryvek používá pomocnou `GetCreds_SPI_SecretKey`funkci . Kód pro tuto pomocnou funkci je k dispozici [zde na GitHubu](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Ověřování mezi službami s certifikátem

Přidejte tento úryvek do klientské aplikace .NET. Nahraďte zástupné hodnoty hodnotami načtenými z webové aplikace Azure AD (uvedené jako předpoklad). Tento fragment umožňuje ověřit vaši aplikaci **neinteraktivně** pomocí Data Lake Storage Gen1 pomocí certifikátu pro webovou aplikaci Azure AD. Pokyny k vytvoření aplikace Azure AD najdete v tématu [Vytvoření instančního objektu s certifikáty](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

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

Předchozí úryvek používá pomocnou `GetCreds_SPI_Cert`funkci . Kód pro tuto pomocnou funkci je k dispozici [zde na GitHubu](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli, jak používat ověřování mezi službami k ověření pomocí data lake storage gen1 pomocí sady .NET SDK. Nyní se můžete podívat na následující články, které hovoří o tom, jak používat .NET SDK pro práci s Data Lake Storage Gen1.

* [Operace správy účtů v úložišti Datového jezera Gen1 pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operace s daty v síti Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-data-operations-net-sdk.md)
