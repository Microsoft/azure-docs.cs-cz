---
title: '.NET SDK: Operace správy účtů v Azure Data Lake Store | Dokumentace Microsoftu'
description: Použití sady .NET SDK pro Azure Data Lake Store k provádění operací správy účtů ve službě Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: ee3c528345232090227c413aebaebe7cc265bc76
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643052"
---
# <a name="account-management-operations-on-azure-data-lake-store-using-net-sdk"></a>Operace správy účtů v Azure Data Lake Store pomocí sady .NET SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

V tomto článku se dozvíte, jak provádět operace správy účtů ve službě Data Lake Store pomocí sady .NET SDK. Mezi operace správy účtů patří vytvoření účtu Data Lake Store, výpis účtů v předplatném Azure, odstranění účtů atd.

Pokyny k provádění operací správy dat ve službě Data Lake Store pomocí sady .NET SDK najdete v tématu [Operace systému souborů ve službě Data Lake Store pomocí sady .NET SDK](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Požadavky
* **Visual Studio 2013, 2015 nebo 2017**. Níže uvedené pokyny používají sadu Visual Studio 2017.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

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

7. Deklarujte proměnné a nahraďte zástupné symboly příslušnými hodnotami. Také se ujistěte, že na počítači existuje místní cesta a název souboru, které zadáte.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>.azuredatalakestore.net"; 
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";                    
                }
            }
        }

Ve zbývajících oddílech tohoto článku uvidíte, jak používat dostupné metody rozhraní .NET k provádění operací, jako je ověřování, nahrávání souborů atd.

## <a name="authentication"></a>Authentication

* Pokud chcete ve své aplikaci ověřování koncového uživatele, přečtěte si téma [Ověřování koncového uživatele pomocí služby Data Lake Store s použitím sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Pokud chcete ve své aplikaci ověřování služba-služba, přečtěte si téma [Ověřování služba-služba pomocí služby Data Lake Store s použitím sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Vytvoření objektu klienta
Následující fragment kódu vytvoří objekt klienta účtu Data Lake Store, který slouží k zasílání požadavků na správu účtů, jako je například vytvoření účtu, odstranění účtu a další, do služby.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-store-account"></a>Vytvoření účtu Data Lake Store
Následující fragment kódu vytvoří účet Data Lake Store v předplatném Azure, které jste zadali při vytváření objektu klienta účtu Data Lake Store.

    // Create Data Lake Store account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Zobrazení seznamu všech účtů Data Lake Store v rámci předplatného
Přidejte následující metodu do vaší definice třídy. Následující fragment kódu zobrazí všechny účty Data Lake Store v rámci daného předplatného Azure.

    // List all Data Lake Store accounts within the subscription
    public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccountBasic>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="delete-a-data-lake-store-account"></a>Odstranění účtu Data Lake Store
Následující fragment kódu odstraní účet Data Lake Store, který jste vytvořili dříve.

    // Delete Data Lake Store account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>Další informace najdete v tématech
* [Operace systému souborů ve službě Data Lake Store pomocí sady .NET SDK](data-lake-store-data-operations-net-sdk.md)
* [Referenční dokumentace sady SDK rozhraní .NET služby Data Lake Store](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Další postup
* [Zabezpečení dat ve službě Data Lake Store](data-lake-store-secure-data.md)
