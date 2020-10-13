---
title: Ověřování koncových uživatelů – .NET s Data Lake Storage Gen1 – Azure
description: Naučte se, jak dosáhnout ověřování koncovými uživateli pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory se sadou .NET SDK.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-csharp
ms.openlocfilehash: 67ba4f12aec9e987d79109b7197d03301bf40650
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89004778"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Ověřování koncových uživatelů s Azure Data Lake Storage Gen1 pomocí sady .NET SDK
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-end-user-authenticate-python.md)
> * [Použití REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

V tomto článku se dozvíte, jak používat sadu .NET SDK k ověřování koncových uživatelů pomocí Azure Data Lake Storage Gen1. Pro ověřování služby-služba s Data Lake Storage Gen1 pomocí sady .NET SDK, přečtěte si téma [ověřování služba-služba s Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Požadavky
* **Visual Studio 2013 nebo vyšší**. Níže uvedené pokyny používají Visual Studio 2019.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvořte Azure Active Directory "nativní" aplikaci**. Musíte dokončit kroky v části [ověřování koncového uživatele s Data Lake Storage Gen1 pomocí Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Vytvoření aplikace .NET
1. V aplikaci Visual Studio vyberte nabídku **soubor** , **Nový**a poté **projekt**.
2. Zvolte **Konzolová aplikace (.NET Framework)** a pak vyberte **Další**.
3. Do **název projektu**zadejte `CreateADLApplication` a pak vyberte **vytvořit**.

4. Přidejte do projektu balíčky NuGet.

   1. V Průzkumníku řešení klikněte pravým tlačítkem na název projektu a klikněte na možnost **Správa balíčků NuGet**.
   2. Ujistěte se, že na kartě **Správce balíčků NuGet** je položka **Zdroj balíčku** nastavena na hodnotu **nuget.org** a že je zaškrtnuto políčko **Zahrnout předběžné verze**.
   3. Vyhledejte a nainstalujte následující balíčky NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` – Tento kurz používá verzi v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – Tento kurz používá verzi v2.2.12.

        ![Přidání zdroje NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Vytvořit nový účet Azure Data Lake")
   4. Zavřete **Správce balíčků NuGet**.

5. Otevřít **program.cs**
6. Příkazy using nahraďte následujícími řádky:

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
Přidejte tento fragment kódu do klientské aplikace .NET. Nahraďte zástupné hodnoty hodnotami načtenými z nativní aplikace Azure AD (uvedené jako předpoklad). Tento fragment kódu umožňuje **interaktivní** ověřování aplikace pomocí Data Lake Storage Gen1, což znamená, že se zobrazí výzva k zadání přihlašovacích údajů Azure.

Pro snadné použití následující fragment kódu používá výchozí hodnoty pro ID klienta a identifikátor URI pro přesměrování, které jsou platné pro jakékoli předplatné Azure. V následujícím fragmentu kódu stačí zadat hodnotu pro ID tenanta. ID tenanta můžete načíst pomocí pokynů uvedených na adrese [získat ID tenanta](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
    
- Funkci main () nahraďte následujícím kódem:

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

Několik věcí, které se dozvíte o předchozím fragmentu kódu:

* Předchozí fragment kódu používá pomocné funkce `GetTokenCache` a `GetCreds_User_Popup` . Kód pro tyto pomocné funkce je k dispozici [na GitHubu](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Pro snadnější dokončení kurzu fragment kódu používá nativní ID klienta aplikace, které je ve výchozím nastavení dostupné pro všechna předplatná Azure. Můžete tedy **použít ve své aplikaci tento fragment kódu bez jakýchkoli úprav**.
* Pokud však chcete používat vlastní doménu a ID klienta aplikace Azure AD, musíte vytvořit nativní aplikaci Azure AD a pak pro vytvořenou aplikaci použít příslušné ID tenanta, ID klienta a identifikátor URI přesměrování Azure AD. Pokyny najdete v tématu [Vytvoření aplikace Active Directory pro ověřování koncových uživatelů pomocí Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md) .

  
## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak používat ověřování koncovými uživateli k ověřování pomocí Azure Data Lake Storage Gen1 pomocí sady .NET SDK. Nyní se můžete podívat na následující články, které vás seznámí s postupem použití sady .NET SDK pro práci s Azure Data Lake Storage Gen1.

* [Operace správy účtů při Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operace s daty při Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-data-operations-net-sdk.md)

