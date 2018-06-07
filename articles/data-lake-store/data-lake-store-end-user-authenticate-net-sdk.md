---
title: 'Ověřování koncového uživatele: .NET SDK s Data Lake Store pomocí Azure Active Directory | Microsoft Docs'
description: Zjistěte, jak zajistit ověření koncového uživatele s Data Lake Store pomocí sady .NET SDK služby Azure Active Directory
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
ms.openlocfilehash: cbb0f703f61b6c15b3a827dc75821286b7914c21
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623957"
---
# <a name="end-user-authentication-with-data-lake-store-using-net-sdk"></a>Ověřování koncového uživatele s Data Lake Store pomocí sady .NET SDK
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-end-user-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

V tomto článku se dozvíte o tom, jak používat sadu .NET SDK chcete ověření koncových uživatelů s Azure Data Lake Store. Service-to-service ověřování s Data Lake Store pomocí sady .NET SDK, naleznete v části [Service-to-service ověřování s Data Lake Store pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Požadavky
* **Visual Studio 2013, 2015 nebo 2017**. Níže uvedené pokyny používají sadu Visual Studio 2017.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvoření aplikace Azure Active Directory "Nativní"**. Musí mít dokončili postup v [ověřování koncového uživatele s Data Lake Store pomocí Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

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

6. Otevřete **Program.cs**
7. Nahradit bránu pomocí příkazů s následujícími řádky:

    ```csharp
    using System;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Collections.Generic;
            
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```     

## <a name="end-user-authentication"></a>Ověřování koncových uživatelů
Přidejte tento fragment kódu do klientské aplikace .NET. Zástupné hodnoty nahraďte hodnoty získané z nativní aplikaci Azure AD (uvedené jako požadavek). Tento fragment kódu umožňuje aplikaci ověřovat **interaktivně** s Data Lake Store, což znamená, zobrazí se výzva k zadání přihlašovacích údajů Azure.

Následující fragment kódu pro snadné použití, používá výchozí hodnoty pro ID klienta a identifikátor URI, které jsou platné pro jakéhokoli předplatného Azure pro přesměrování. V následující fragment kódu potřebujete jenom zadejte hodnotu pro vaše ID klienta. Můžete načíst ID klienta pomocí pokynů uvedených v [získání ID tenanta](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
    
- Funkce Main() nahraďte následujícím kódem:

    ```csharp
    private static void Main(string[] args)
    {
        //User login via interactive popup
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string MY_DOCUMENTS = System.Environment.GetFolderPath(System.Environment.SpecialFolder.MyDocuments);
        string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");
        var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
        var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
        var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
    }
    ```

Několik věcí, které potřebujete vědět o předchozím fragmentu kódu:

* Podpůrné funkce používá fragmentu předcházející `GetTokenCache` a `GetCreds_User_Popup`. Kód pro tyto pomocné funkce je k dispozici [sem na Githubu](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Fragment vám pomohou dokončit tento kurz rychleji, používá ID klienta nativní aplikace, která je k dispozici ve výchozím nastavení pro všechny odběry služby Azure. Můžete tedy **použít ve své aplikaci tento fragment kódu bez jakýchkoli úprav**.
* Pokud však chcete používat vlastní doménu a ID klienta aplikace Azure AD, musíte vytvořit nativní aplikaci Azure AD a pak pro vytvořenou aplikaci použít příslušné ID tenanta, ID klienta a identifikátor URI přesměrování Azure AD. Pokyny najdete v tématu [Vytvoření aplikace Active Directory pro ověřování koncového uživatele pomocí služby Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md).

  
## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat ověřování koncových uživatelů k ověřování s Azure Data Lake Store pomocí sady .NET SDK. Teď můžete prohlédnout v následujících článcích, které mluvit o tom, jak používat sadu .NET SDK pro práci s Azure Data Lake Store.

* [Operace správy účtů ve službě Data Lake Store pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operace dat v Data Lake Store pomocí sady .NET SDK](data-lake-store-data-operations-net-sdk.md)

