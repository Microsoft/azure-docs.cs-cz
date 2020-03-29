---
title: 'Ověřování koncových uživatelů: Sada .NET SDK s Azure Data Lake Storage Gen1 pomocí Služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak dosáhnout ověřování koncových uživatelů pomocí Azure Data Lake Storage Gen1 pomocí Azure Active Directory s .NET SDK
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
ms.openlocfilehash: 215b839c21c2590c08ac2f4250086eaf97914ce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66243716"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Ověřování koncových uživatelů pomocí azure data lake storage gen1 pomocí sady .NET SDK
> [!div class="op_single_selector"]
> * [Pomocí Javy](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Pomocí Pythonu](data-lake-store-end-user-authenticate-python.md)
> * [Pomocí rozhraní REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

V tomto článku se dozvíte, jak pomocí sady .NET SDK provést ověřování koncových uživatelů pomocí Azure Data Lake Storage Gen1. Ověřování mezi službami pomocí sady Data Lake Storage Gen1 pomocí sady .NET SDK najdete v [tématu Ověřování mezi službami pomocí sady Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Požadavky
* **Visual Studio 2013 nebo vyšší**. Níže uvedené pokyny používají Visual Studio 2019.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Vytvořte nativní aplikaci služby Azure Active Directory**. Kroky ověřování koncových uživatelů musíte provést [pomocí služby Data Lake Storage Gen1 pomocí služby Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

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

5. Otevřít **Program.cs**
6. Nahraďte příkazy using následujícími řádky:

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
Přidejte tento úryvek do klientské aplikace .NET. Nahraďte zástupné hodnoty hodnotami načtenými z nativní aplikace Azure AD (uvedené jako předpoklad). Tento fragment umožňuje **interaktivní** ověření aplikace pomocí data lake storage gen1, což znamená, že budete vyzváni k zadání přihlašovacích údajů Azure.

Pro snadné použití následující úryvek používá výchozí hodnoty pro ID klienta a přesměrování URI, které jsou platné pro jakékoli předplatné Azure. V následujícím fragmentu stačí zadat hodnotu pro id vašeho klienta. ID klienta můžete načíst pomocí pokynů uvedených na [získání ID klienta](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
    
- Nahraďte funkci Main() následujícím kódem:

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

Pár věcí, které je třeba vědět o předchozím úryvku:

* Předchozí úryvek používá pomocné `GetTokenCache` funkce `GetCreds_User_Popup`a . Kód pro tyto pomocné funkce je k dispozici [zde na GitHubu](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Aby vám výstřižek pomohl dokončit kurz rychleji, fragment používá nativní ID klienta aplikace, které je ve výchozím nastavení k dispozici pro všechna předplatná Azure. Můžete tedy **použít ve své aplikaci tento fragment kódu bez jakýchkoli úprav**.
* Pokud však chcete používat vlastní doménu a ID klienta aplikace Azure AD, musíte vytvořit nativní aplikaci Azure AD a pak pro vytvořenou aplikaci použít příslušné ID tenanta, ID klienta a identifikátor URI přesměrování Azure AD. Pokyny naleznete [v tématu Vytvoření aplikace služby Active Directory pro ověřování koncových uživatelů pomocí programu Data Lake Storage Gen1.](data-lake-store-end-user-authenticate-using-active-directory.md)

  
## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli, jak pomocí ověřování koncových uživatelů k ověření pomocí Azure Data Lake Storage Gen1 pomocí .NET SDK. Teď se můžete podívat na následující články, které hovoří o tom, jak používat .NET SDK pro práci s Azure Data Lake Storage Gen1.

* [Operace správy účtů v úložišti Datového jezera Gen1 pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operace s daty v síti Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-data-operations-net-sdk.md)

