---
title: '.NET SDK: Operace správy účtů na Azure Data Lake Storage Gen1 | Dokumenty společnosti Microsoft'
description: Použití azure data lake storage gen1 .NET SDK k provádění operací správy účtů v data lake storage gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8da40aa04381542c8c750c8d7e33c9a29879371d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65900878"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Operace správy účtů v Azure Data Lake Storage Gen1 pomocí .NET SDK
> [!div class="op_single_selector"]
> * [Sada SDK rozhraní .NET](data-lake-store-get-started-net-sdk.md)
> * [ROZHRANÍ API PRO ODPOČINEK](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

V tomto článku se dozvíte, jak provádět operace správy účtů na Azure Data Lake Storage Gen1 pomocí .NET SDK. Operace správy účtů zahrnují vytvoření účtu Data Lake Storage Gen1, výpis účtů v předplatném Azure, odstranění účtů atd.

Pokyny k provádění operací správy dat v souboru .NET SDK úložiště Data Lake Storage 1 naleznete v tématu [Operace souborového systému v souborovém úložišti Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Požadavky
* **Visual Studio 2013 nebo vyšší**. Níže uvedené pokyny používají Visual Studio 2019.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

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

6. Deklarujte proměnné a nahraďte zástupné symboly příslušnými hodnotami. Také se ujistěte, že na počítači existuje místní cesta a název souboru, které zadáte.

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
                    _adlsAccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net"; 
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";                    
                }
            }
        }

Ve zbývajících oddílech tohoto článku uvidíte, jak používat dostupné metody rozhraní .NET k provádění operací, jako je ověřování, nahrávání souborů atd.

## <a name="authentication"></a>Ověřování

* Informace o ověřování koncových uživatelů pro vaši aplikaci najdete [v tématu Ověřování koncových uživatelů pomocí data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Informace o ověřování mezi službami pro vaši aplikaci najdete v [tématu Ověřování mezi službami pomocí služby Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Vytvoření objektu klienta
Následující fragment vytvoří objekt klienta účtu účtu Data Lake Storage Gen1, který se používá k vydávání požadavků na správu účtu službě, jako je například vytvoření účtu, odstranění účtu atd.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Vytvoření účtu Data Lake Storage Gen1
Následující fragment vytvoří účet Data Lake Storage Gen1 v předplatném Azure, které jste zadali při vytváření objektu klienta účtu účtu Data Lake Storage Gen1.

    // Create Data Lake Storage Gen1 account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>Seznam všech účtů Data Lake Storage Gen1 v rámci předplatného
Přidejte následující metodu do vaší definice třídy. Následující fragment obsahuje seznam všech účtů Data Lake Storage Gen1 v rámci daného předplatného Azure.

    // List all Data Lake Storage Gen1 accounts within the subscription
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

## <a name="delete-a-data-lake-storage-gen1-account"></a>Odstranění účtu Gen1 úložiště datového jezera
Následující úryvek odstraní účet Data Lake Storage Gen1, který jste vytvořili dříve.

    // Delete Data Lake Storage Gen1 account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>Viz také
* [Operace souborového systému na úložišti Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-data-operations-net-sdk.md)
* [Odkaz na úložiště datových jezer Gen1 .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Další kroky
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
