---
title: '.NET SDK: Operace systému souborů v Azure Data Lake Store | Dokumentace Microsoftu'
description: Použití sady .NET SDK pro Azure Data Lake Store k provádění operací systému souborů ve službě Data Lake Store, jako je například vytváření složek atd.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/30/2018
ms.author: nitinme
ms.openlocfilehash: 1094a5ea3c000707aa7736d22d4df0558da32b5e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2018
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-net-sdk"></a>Operace systému souborů v Azure Data Lake Store pomocí sady .NET SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

V tomto článku se dozvíte, jak provádět operace systému souborů ve službě Data Lake Store pomocí sady .NET SDK. Mezi operace systému souborů patří vytváření složek v účtu Data Lake Store, nahrávání souborů, stahování souborů atd.

Pokyny k provádění operací správy účtů ve službě Data Lake Store pomocí sady .NET SDK najdete v tématu [Operace správy účtů ve službě Data Lake Store pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Požadavky
* **Visual Studio 2013, 2015 nebo 2017**. Níže uvedené pokyny používají sadu Visual Studio 2017.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Účet Azure Data Lake Store**. Pokyny k vytvoření účtu najdete v tématu [Začínáme s Azure Data Lake Store](data-lake-store-get-started-portal.md).

## <a name="create-a-net-application"></a>Vytvoření aplikace .NET
Ukázka kódu, která je k dispozici [na GitHubu](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted), vás provede procesem vytvoření souborů v úložišti, zřetězení souborů, stažení souboru a nakonec odstranění některých souborů v úložišti. Tato část článku vás provede hlavními částmi kódu.

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

      * `Microsoft.Azure.DataLake.Store` – Tento kurz používá verzi v1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – Tento kurz používá verzi v2.3.1.
    
    Zavřete **Správce balíčků NuGet**.

6. Otevřete soubor **Program.cs**, odstraňte stávající kód a potom vložte následující příkazy, čímž přidáte odkazy na obory názvů.

        using System;
        using System.IO;using System.Threading;
        using System.Linq;
        using System.Text;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.DataLake.Store;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. Deklarujte proměnné, jak je znázorněno níže, a nahraďte zástupné symboly příslušnými hodnotami. Také se ujistěte, že na počítači existuje místní cesta a název souboru, které tady zadáte.

        namespace SdkSample
        {
            class Program
            {
                private static string _adlsAccountName = "<DATA-LAKE-STORE-NAME>.azuredatalakestore.net";        
            }
        }

Ve zbývajících oddílech tohoto článku uvidíte, jak používat dostupné metody rozhraní .NET k provádění operací, jako je ověřování, nahrávání souborů atd.

## <a name="authentication"></a>Authentication

* Pokud chcete ve své aplikaci ověřování koncového uživatele, přečtěte si téma [Ověřování koncového uživatele pomocí služby Data Lake Store s použitím sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Pokud chcete ve své aplikaci ověřování služba-služba, přečtěte si téma [Ověřování služba-služba pomocí služby Data Lake Store s použitím sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).


## <a name="create-client-object"></a>Vytvoření objektu klienta
Následující fragment kódu vytvoří objekt klienta systému souborů Data Lake Store, který slouží k vydávání požadavků na službu.

    // Create client objects
    AdlsClient client = AdlsClient.CreateClient(_adlsAccountName, adlCreds);

## <a name="create-a-file-and-directory"></a>Vytvoření souboru a adresáře
Přidejte do své aplikace následující fragment kódu. Tento fragment kódu přidá soubor a případně všechny nadřazené adresáře, které neexistují.

    // Create a file - automatically creates any parent directories that don't exist
    // The AdlsOuputStream preserves record boundaries - it does not break records while writing to the store
    using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);

        textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="append-to-a-file"></a>Připojení k souboru
Následující fragment kódu připojí data k existujícímu souboru v účtu Data Lake Store.

    // Append to existing file
    using (var stream = client.GetAppendStream(fileName))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="read-a-file"></a>Čtení souboru
Následující fragment kódu přečte obsah souboru ve službě Data Lake Store.

    //Read file contents
    using (var readStream = new StreamReader(client.GetReadStream(fileName)))
    {
        string line;
        while ((line = readStream.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }

## <a name="get-file-properties"></a>Získání vlastností souboru
Následující fragment kódu vrátí vlastnosti přidružené k souboru nebo adresáři.

    // Get file properties
    var directoryEntry = client.GetDirectoryEntry(fileName);
    PrintDirectoryEntry(directoryEntry);

Definice metody `PrintDirectoryEntry` je k dispozici jako součást ukázky [na GitHubu](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted). 

## <a name="rename-a-file"></a>Přejmenování souboru
Následující fragment kódu přejmenuje existující soubor v účtu Data Lake Store.

    // Rename a file
    string destFilePath = "/Test/testRenameDest3.txt";
    client.Rename(fileName, destFilePath, true);

## <a name="enumerate-a-directory"></a>Výpis obsahu adresáře
Následující fragment kódu vypíše obsah adresářů v účtu Data Lake Store.

    // Enumerate directory
    foreach (var entry in client.EnumerateDirectory("/Test"))
    {
        PrintDirectoryEntry(entry);
    }

Definice metody `PrintDirectoryEntry` je k dispozici jako součást ukázky [na GitHubu](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Rekurzivní odstranění adresářů
Následující fragment kódu rekurzivně odstraní adresář a všechny jeho podadresáře.

    // Delete a directory and all it's subdirectories and files
    client.DeleteRecursive("/Test");

## <a name="samples"></a>Ukázky
Tady je několik ukázek použití sady SDK pro systém souborů Data Lake Store.
* [Základní ukázka na GitHubu](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Pokročilá ukázka na GitHubu](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Viz také
* [Operace správy účtů ve službě Data Lake Store pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Referenční dokumentace sady SDK rozhraní .NET služby Data Lake Store](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Další kroky
* [Zabezpečení dat ve službě Data Lake Store](data-lake-store-secure-data.md)
