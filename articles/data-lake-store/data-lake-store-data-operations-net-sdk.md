---
title: .NET SDK – operace se systémem souborů v systému Data Lake Storage Gen1 – Azure
description: Použijte sadu Azure Data Lake Storage Gen1 .NET SDK pro operace systému souborů na Data Lake Storage Gen1, jako je například vytváření složek atd.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/03/2020
ms.author: twooley
ms.custom: devx-track-dotnet
ms.openlocfilehash: cb6d5fafa8d956c296124c5a20f8a087160961da
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109234"
---
# <a name="filesystem-operations-on-data-lake-storage-gen1-using-the-net-sdk"></a>Operace systému souborů při Data Lake Storage Gen1 pomocí sady .NET SDK

> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

V tomto článku se dozvíte, jak provádět operace systému souborů na Data Lake Storage Gen1 pomocí sady .NET SDK. Mezi operace systému souborů patří vytváření složek v účtu Data Lake Storage Gen1, nahrávání souborů, stahování souborů atd.

Pokyny k provádění operací správy účtů v Data Lake Storage Gen1 pomocí sady .NET SDK najdete v tématu [operace správy účtů pro data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Předpoklady

* **Visual Studio 2013 nebo vyšší**. Pokyny v tomto článku používají Visual Studio 2019.

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Účet Azure Data Lake Storage Gen1**. Pokyny k vytvoření účtu najdete v tématu [Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

## <a name="create-a-net-application"></a>Vytvoření aplikace .NET

Ukázka kódu, která je k dispozici [na GitHubu](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted), vás provede procesem vytvoření souborů v úložišti, zřetězení souborů, stažení souboru a nakonec odstranění některých souborů v úložišti. Tato část článku vás provede hlavními částmi kódu.

1. V aplikaci Visual Studio vyberte nabídku **soubor** , **Nový**a poté **projekt**.
1. Zvolte **Konzolová aplikace (.NET Framework)** a pak vyberte **Další**.
1. Do **název projektu**zadejte `CreateADLApplication` a pak vyberte **vytvořit**.
1. Přidejte do projektu balíčky NuGet.

   1. V Průzkumníku řešení klikněte pravým tlačítkem na název projektu a klikněte na možnost **Správa balíčků NuGet**.
   1. Na kartě **Správce balíčků NuGet** se ujistěte, že je **zdroj balíčku** nastavený na **NuGet.org**. Ujistěte se také, že je zaškrtnuto políčko **zahrnout předběžné verze** .
   1. Vyhledejte a nainstalujte následující balíčky NuGet:

      * `Microsoft.Azure.DataLake.Store` – Tento článek používá verzi v 1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – Tento článek používá v 2.3.1.

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

Ve zbývajících částech článku vidíte, jak používat dostupné metody .NET k provádění operací, jako je ověřování, nahrávání souborů atd.

## <a name="authentication"></a>Authentication

* Pro ověřování koncového uživatele pro vaši aplikaci, přečtěte si téma [ověřování koncových uživatelů s Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Pro ověřování služby pro vaši aplikaci, přečtěte si téma [ověřování služba-služba s Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Vytvoření objektu klienta

Následující fragment kódu vytvoří objekt klienta systému souborů Data Lake Storage Gen1, který slouží k vystavování požadavků službě.

```
// Create client objects
AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);
```

## <a name="create-a-file-and-directory"></a>Vytvoření souboru a adresáře

Přidejte do své aplikace následující fragment kódu. Tento fragment kódu přidá soubor a jakýkoli nadřazený adresář, který neexistuje.

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

Následující fragment kódu připojí data do existujícího souboru v Data Lake Storage Gen1 účet.

```
// Append to existing file

using (var stream = client.GetAppendStream(fileName))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="read-a-file"></a>Čtení souboru

Následující fragment kódu přečte obsah souboru v Data Lake Storage Gen1.

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

Následující fragment kódu přejmenuje existující soubor do účtu Data Lake Storage Gen1.

```
// Rename a file
string destFilePath = "/Test/testRenameDest3.txt";
client.Rename(fileName, destFilePath, true);
```

## <a name="enumerate-a-directory"></a>Výpis obsahu adresáře

Následující fragment kódu vytvoří výčet adresářů v účtu Data Lake Storage Gen1.

```
// Enumerate directory
foreach (var entry in client.EnumerateDirectory("/Test"))
{
    PrintDirectoryEntry(entry);
}
```

Definice `PrintDirectoryEntry` metody je k dispozici jako součást ukázky [na GitHubu](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Rekurzivní odstranění adresářů

Následující fragment kódu odstraní adresář a všechny jeho podadresáře rekurzivně.

```
// Delete a directory and all its subdirectories and files
client.DeleteRecursive("/Test");
```

## <a name="samples"></a>ukázky

Tady je několik ukázek, které ukazují, jak používat sadu SDK Data Lake Storage Gen1 systému souborů.

* [Základní ukázka na GitHubu](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Pokročilá ukázka na GitHubu](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Viz také

* [Operace správy účtů při Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Referenční informace k sadě Data Lake Storage Gen1 .NET SDK](/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Další kroky

* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)