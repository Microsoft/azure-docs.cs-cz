---
title: 'Ověřování koncového uživatele: sady .NET SDK s Azure Data Lake Storage Gen1 pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak dokončit ověřování koncového uživatele pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory pomocí .NET SDK
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
ms.openlocfilehash: 65672d5dc42ae1e584a36bcc72c16911f6c1f6e2
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46122821"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Ověřování koncového uživatele pomocí Azure Data Lake Storage Gen1 pomocí sady .NET SDK
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-end-user-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

V tomto článku najdete informace o tom, jak pomocí sady .NET SDK provádět ověřování koncového uživatele pomocí Azure Data Lake Storage Gen1. Ověřování služba služba s Data Lake Storage Gen1 pomocí sady .NET SDK, přečtěte si téma [ověřování služba služba Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Požadavky
* **Visual Studio 2013, 2015 nebo 2017**. Níže uvedené pokyny používají sadu Visual Studio 2017.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvoření aplikace Azure Active Directory "Nativní"**. Je nutné dokončit kroky v [ověřování koncového uživatele s Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

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

6. Otevřít **Program.cs**
7. Nahraďte na pomocí příkazů s následujícími řádky:

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
Přidejte tento fragment kódu do klientské aplikace .NET. Nahraďte zástupné hodnoty hodnotami, které je načten z nativní aplikaci Azure AD (uvedené jako předpoklad). Tento fragment kódu umožňuje ověřování vaší aplikace **interaktivně** s Data Lake Storage Gen1, což znamená, že budete vyzváni k zadání přihlašovacích údajů Azure.

Následující fragment kódu pro snadnější použití používá výchozí hodnoty pro ID klienta a jsou platné pro jakékoli předplatné identifikátor URI pro přesměrování. V následujícím fragmentu kódu stačí zadat hodnotu vašeho ID tenanta. Můžete získat ID klienta pomocí pokynů uvedených v [získání ID tenanta](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
    
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

* Předchozí fragment kódu používá pomocné funkce `GetTokenCache` a `GetCreds_User_Popup`. Kód pro tyto pomocné funkce je k dispozici [si na Githubu](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Můžete projít tento kurz rychleji, fragment kódu používá ID klienta nativní aplikace, která je k dispozici ve výchozím nastavení pro všechna předplatná Azure. Můžete tedy **použít ve své aplikaci tento fragment kódu bez jakýchkoli úprav**.
* Pokud však chcete používat vlastní doménu a ID klienta aplikace Azure AD, musíte vytvořit nativní aplikaci Azure AD a pak pro vytvořenou aplikaci použít příslušné ID tenanta, ID klienta a identifikátor URI přesměrování Azure AD. Zobrazit [vytvoření aplikace Active Directory pro ověřování koncového uživatele s Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md) pokyny.

  
## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat ověřování koncového uživatele k ověření pomocí Azure Data Lake Storage Gen1 pomocí sady .NET SDK. Teď můžete prohlédnout v následujících článcích, které mluvit o tom, jak používat sadu .NET SDK pro práci s Azure Data Lake Storage Gen1.

* [Operace správy účtů v Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operace s daty v Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-data-operations-net-sdk.md)

