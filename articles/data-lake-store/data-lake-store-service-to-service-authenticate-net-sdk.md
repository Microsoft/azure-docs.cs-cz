---
title: 'Ověřování služby služby: .NET SDK s Data Lake Store pomocí Azure Active Directory | Microsoft Docs'
description: Zjistěte, jak dosáhnout service-to-service ověřování s Data Lake Store pomocí Azure Active Directory pomocí sady .NET SDK
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 388b84024a031a181625404ec1429087982dffbe
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625487"
---
# <a name="service-to-service-authentication-with-data-lake-store-using-net-sdk"></a>Service-to-service ověřování s Data Lake Store pomocí sady .NET SDK
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-service-to-service-authenticate-java.md)
> * [Pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

V tomto článku se dozvíte o tom, jak používat sadu .NET SDK udělat service-to-service ověřování s Azure Data Lake Store. Pro ověřování koncového uživatele s Data Lake Store pomocí sady .NET SDK, najdete v části [ověřování koncového uživatele s Data Lake Store pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).


## <a name="prerequisites"></a>Požadavky
* **Visual Studio 2013, 2015 nebo 2017**. Níže uvedené pokyny používají sadu Visual Studio 2017.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvoření aplikace Azure Active Directory "Web"**. Musí mít dokončili postup v [Service-to-service ověřování s Data Lake Store pomocí Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Vytvoření aplikace .NET
1. Otevřete Visual Studio a vytvořte konzolovou aplikaci.
2. V nabídce **Soubor** klikněte na položku **Nový** a potom klikněte na položku **Projekt**.
3. V části **Nový projekt** zadejte nebo vyberte tyto hodnoty:

   | Vlastnost | Hodnota |
   | --- | --- |
   | Kategorie |Šablony/Visual C#/Windows |
   | Šablona |Konzolová aplikace |
   | Název |VytvořeníAplikaceADL |
4. Projekt vytvoříte kliknutím na **OK**.

5. Přidejte do projektu balíčky NuGet.

   1. V Průzkumníku řešení klikněte pravým tlačítkem na název projektu a klikněte na možnost **Správa balíčků NuGet**.
   2. Ujistěte se, že na kartě **Správce balíčků NuGet** je položka **Zdroj balíčku** nastavena na hodnotu **nuget.org** a že je zaškrtnuto políčko **Zahrnout předběžné verze**.
   3. Vyhledejte a nainstalujte následující balíčky NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` – Tento kurz používá verzi v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – Tento kurz používá verzi v2.2.12.

        ![Přidání zdroje NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Vytvoření nového účtu Azure Data Lake")
   4. Zavřete **Správce balíčků NuGet**.

6. Otevřete soubor **Program.cs**, odstraňte stávající kód a potom vložte následující příkazy, čímž přidáte odkazy na obory názvů.

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

## <a name="service-to-service-authentication-with-client-secret"></a>Service-to-service ověřování s tajný klíč klienta
Přidejte tento fragment kódu do klientské aplikace .NET. Zástupné hodnoty nahraďte hodnoty získané z webové aplikace služby Azure AD (uvedené jako předpoklad).  Tento fragment kódu umožňuje aplikaci ověřovat **neinteraktivně** s Data Lake Store pomocí tajný klíč nebo klíč klienta pro Azure AD webové aplikace. 

    private static void Main(string[] args)
    {    
        // Service principal / appplication authentication with client secret / key
        // Use the client ID of an existing AAD "Web App" application.
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
        var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
        var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
    }

Fragment kódu předcházející používá pomocné funkce `GetCreds_SPI_SecretKey`. Kód pro tato pomocná funkce je k dispozici [sem na Githubu](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Service-to-service ověřování pomocí certifikátu

Přidejte tento fragment kódu do klientské aplikace .NET. Zástupné hodnoty nahraďte hodnoty získané z webové aplikace služby Azure AD (uvedené jako předpoklad). Tento fragment kódu umožňuje aplikaci ověřovat **neinteraktivně** s Data Lake Store pomocí certifikátu pro Azure AD webové aplikace. Pokyny o tom, jak vytvořit aplikaci Azure AD najdete v tématu [vytvoření instančního objektu s certifikáty](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

    
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

Fragment kódu předcházející používá pomocné funkce `GetCreds_SPI_Cert`. Kód pro tato pomocná funkce je k dispozici [sem na Githubu](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat ověřování service-to-service k ověřování s Azure Data Lake Store pomocí sady .NET SDK. Teď můžete prohlédnout v následujících článcích, které mluvit o tom, jak používat sadu .NET SDK pro práci s Azure Data Lake Store.

* [Operace správy účtů ve službě Data Lake Store pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operace dat v Data Lake Store pomocí sady .NET SDK](data-lake-store-data-operations-net-sdk.md)


