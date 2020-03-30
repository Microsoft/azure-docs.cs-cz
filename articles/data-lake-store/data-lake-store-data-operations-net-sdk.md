---
title: '.NET SDK: Operace souborového systému v Azure Data Lake Storage Gen1'
description: Azure Data Lake Storage Gen1 .NET SDK pro operace souborového systému na Data Lake Storage Gen1, jako je vytváření složek, atd.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 7e33ecbbb49fc2b0683d0757da36deec72796806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638897"
---
# <a name="filesystem-operations-on-data-lake-storage-gen1-using-the-net-sdk"></a>Operace souborového systému v síti Data Lake Storage Gen1 pomocí sady .NET SDK

> [!div class="op_single_selector"]
> * [Sada SDK rozhraní .NET](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [ROZHRANÍ API PRO ODPOČINEK](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

V tomto článku se dozvíte, jak provádět operace systému souborů na Data Lake Storage Gen1 pomocí .NET SDK. Operace souborového systému zahrnují vytváření složek v účtu Data Lake Storage Gen1, nahrávání souborů, stahování souborů atd.

Pokyny k operacím správy účtů v souboru Data Lake Storage Gen1 pomocí sady .NET SDK naleznete v [tématu Operace správy účtů v souboru Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Požadavky

* **Visual Studio 2013 nebo vyšší**. Pokyny v tomto článku používají Visual Studio 2019.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Účet Azure Data Lake Storage Gen1**. Pokyny k vytvoření účtu najdete v tématu [Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

## <a name="create-a-net-application"></a>Vytvoření aplikace .NET

Ukázka kódu, která je k dispozici [na GitHubu](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted), vás provede procesem vytvoření souborů v úložišti, zřetězení souborů, stažení souboru a nakonec odstranění některých souborů v úložišti. Tato část článku vás provede hlavními částmi kódu.

1. V sadě Visual Studio vyberte nabídku **Soubor,** **Nový**a potom **aplikaci Project**.
1. Zvolte **Konzolová aplikace (.NET Framework)** a pak vyberte **Další**.
1. V **názvu**projektu `CreateADLApplication`zadejte a vyberte **Vytvořit**.
1. Přidejte do projektu balíčky NuGet.

   1. V Průzkumníku řešení klikněte pravým tlačítkem na název projektu a klikněte na možnost **Správa balíčků NuGet**.
   1. Na kartě **NuGet Package Manager** zkontrolujte, zda je **zdroj balíčku** nastaven na **nuget.org**. Zkontrolujte také, zda je zaškrtnuté políčko **Zahrnout předběžnou verzi.**
   1. Vyhledejte a nainstalujte následující balíčky NuGet:

      * `Microsoft.Azure.DataLake.Store`- Tento článek používá v1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication`- Tento článek používá v2.3.1.

      Zavřete **Správce balíčků NuGet**.

1. Otevřete soubor **Program.cs**, odstraňte stávající kód a potom vložte následující příkazy, čímž přidáte odkazy na obory názvů.

    ```
    using System;
    using System.IO;using System.Threading;
    using System.Linq;
    using System.Text;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you're using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.DataLake.Store;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Deklarujte proměnné, jak je znázorněno níže, a nahraďte zástupné symboly příslušnými hodnotami. Také se ujistěte, že na počítači existuje místní cesta a název souboru, které tady zadáte.

    ```
    namespace SdkSample
    {
        class Program
        {
            private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";
        }
    }
    ```

Ve zbývajících částech článku můžete vidět, jak používat dostupné metody .NET k operacím, jako je ověřování, nahrávání souborů atd.

## <a name="authentication"></a>Ověřování

* Informace o ověřování koncových uživatelů pro vaši aplikaci najdete [v tématu Ověřování koncových uživatelů pomocí data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Informace o ověřování mezi službami pro vaši aplikaci najdete v [tématu Ověřování mezi službami pomocí služby Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Vytvoření objektu klienta

Následující úryvek vytvoří objekt klienta souborového systému Data Lake Storage Gen1, který se používá k vydávání požadavků na službu.

```
// Create client objects
AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);
```

## <a name="create-a-file-and-directory"></a>Vytvoření souboru a adresáře

Přidejte do své aplikace následující fragment kódu. Tento úryvek přidá soubor a všechny nadřazené adresáře, který neexistuje.

```
// Create a file - automatically creates any parent directories that don't exist
// The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store

using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);

    textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="append-to-a-file"></a>Připojení k souboru

Následující úryvek připojí data k existujícímu souboru v účtu Data Lake Storage Gen1.

```
// Append to existing file

using (var stream = client.GetAppendStream(fileName))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="read-a-file"></a>Čtení souboru

Následující úryvek přečte obsah souboru v datovém úložišti jezera Gen1.

```
//Read file contents

using (var readStream = new StreamReader(client.GetReadStream(fileName)))
{
    string line;
    while ((line = readStream.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

## <a name="get-file-properties"></a>Získání vlastností souboru

Následující fragment kódu vrátí vlastnosti přidružené k souboru nebo adresáři.

```
// Get file properties
var directoryEntry = client.GetDirectoryEntry(fileName);
PrintDirectoryEntry(directoryEntry);
```

Definice `PrintDirectoryEntry` metody je k dispozici jako součást ukázky [na GitHubu](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="rename-a-file"></a>Přejmenování souboru

Následující úryvek přejmenuje existující soubor v účtu Data Lake Storage Gen1.

```
// Rename a file
string destFilePath = "/Test/testRenameDest3.txt";
client.Rename(fileName, destFilePath, true);
```

## <a name="enumerate-a-directory"></a>Výpis obsahu adresáře

Následující úryvek vyjmenovává adresáře v účtu Data Lake Storage Gen1.

```
// Enumerate directory
foreach (var entry in client.EnumerateDirectory("/Test"))
{
    PrintDirectoryEntry(entry);
}
```

Definice `PrintDirectoryEntry` metody je k dispozici jako součást ukázky [na GitHubu](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Rekurzivní odstranění adresářů

Následující úryvek rekurzivně odstraní adresář a všechny jeho podadresáře.

```
// Delete a directory and all its subdirectories and files
client.DeleteRecursive("/Test");
```

## <a name="samples"></a>ukázky

Zde je několik ukázek, které ukazují, jak používat Data Lake Storage Gen1 File SDK.

* [Základní ukázka na GitHubu](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Pokročilá ukázka na GitHubu](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Viz také

* [Operace správy účtů v úložišti Datového jezera Gen1 pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Odkaz na úložiště datových jezer Gen1 .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Další kroky

* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
