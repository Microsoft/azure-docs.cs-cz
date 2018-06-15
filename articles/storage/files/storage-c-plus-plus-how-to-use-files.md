---
title: Vývoj pro Azure soubory s jazykem C++ | Microsoft Docs
description: Další informace jak vyvíjet aplikace C++ a služby, které používají Azure souborů k ukládání dat souborů.
services: storage
documentationcenter: .net
author: renashahmsft
manager: aungoo
editor: tamram
ms.assetid: a1e8c99e-47a6-43a9-9541-c9262eb00b38
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: renashahmsft
ms.openlocfilehash: e0b5974780813eb4f3d67c42781db4d95829814d
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737567"
---
# <a name="develop-for-azure-files-with-c"></a>Vývoj pro Azure soubory s C++
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>O tomto kurzu
V tomto kurzu budete zjistěte, jak provádět základní operace s Azure Files. Prostřednictvím napsané v jazyce C++ – ukázky budete zjistěte, jak vytvářet sdílené složky a adresáře, nahrát, seznamu a odstraňovat soubory. Pokud jste nové soubory Azure, bude užitečné pro porozumění ukázky projít koncepty v následujících částech.

* Vytvářet a odstraňovat sdílené složky Azure
* Vytvářet a odstraňovat adresáře
* Vytvoření výčtu souborů a adresářů v sdílenou složku Azure
* Odesílání, stahování a odstranění souboru
* Nastavit kvótu (maximální velikost) pro sdílenou složku Azure
* Vytvořte sdílený přístupový podpis (klíč SAS) pro soubor, který používá zásady sdíleného přístupu definované ve sdílené složce.

> [!Note]  
> Protože soubory Azure může mít přístup přes protokol SMB, je možné psát jednoduché aplikace, které přístup k Azure souborové složce přes standardní C++ vstupně-výstupních operací třídy a funkce. Tento článek popisuje, jak k psaní aplikací, které používají Azure SDK C++ úložiště, který používá [souboru REST API](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) ke komunikaci s Azure Files.

## <a name="create-a-c-application"></a>Vytvoření aplikace C++
Pokud chcete vytvořit ukázky, musíte nainstalovat Klientská knihovna pro úložiště Azure 2.4.0 jazyka C++. Musí také jste vytvořili účet úložiště Azure.

Chcete-li nainstalovat klienta úložiště Azure 2.4.0 jazyka C++, můžete použít jednu z následujících metod:

* **Linux:** postupujte podle pokynů uvedených v [Klientská knihovna pro úložiště Azure pro C++ – soubor README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) stránky.
* **Windows:** v sadě Visual Studio, klikněte na tlačítko **nástroje &gt; Správce balíčků NuGet &gt; Konzola správce balíčků**. Pomocí následujícího příkazu do [Konzola správce balíčků NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) a stiskněte klávesu **ENTER**.
  
```
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-files"></a>Nastavení aplikace pomocí Azure Files
Přidejte následující příkazy do horní části C++ zdrojový soubor, kam chcete pracovat se soubory Azure obsahovat:

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Nastavit připojovací řetězec úložiště Azure
Používání úložiště File, budete muset připojit k účtu úložiště Azure. Prvním krokem by mohla být nakonfigurovat připojovací řetězec, který jsme budete používat pro připojení k vašemu účtu úložiště. Umožňuje definovat statické proměnné na to.

```cpp
// Define the connection-string with your values.
const utility::string_t 
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Připojení k účtu úložiště Azure
Můžete použít **cloud_storage_account** třída představující informací o vašem účtu úložiště. Chcete-li načíst informace o účtu úložiště z připojovacího řetězce úložiště, můžete použít **analyzovat** metoda.

```cpp
// Retrieve storage account from connection string.    
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
Všechny soubory a adresáře v sdílenou složku Azure nacházet v kontejneru názvem **sdílet**. Váš účet úložiště může mít libovolný počet sdílených složek jako umožňuje kapacitě vašeho účtu. Pokud chcete získat přístup ke sdílené složce a její obsah, budete muset použít Azure soubory klienta.

```cpp
// Create the Azure Files client.
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();
```

Pomocí Azure soubory klienta, můžete pak získat odkaz na sdílenou složku.

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

Chcete-li vytvořit sdílenou složku, použijte **create_if_not_exists** metodu **cloud_file_share** objektu.

```cpp
if (share.create_if_not_exists()) {    
    std::wcout << U("New share created") << std::endl;    
}
```

V tomto okamžiku **sdílet** obsahuje odkaz na sdílenou složku s názvem **my ukázka share**.

## <a name="delete-an-azure-file-share"></a>Odstranit sdílenou složku Azure
Odstranění sdílené složky se provádí volání **delete_if_exists** metoda cloud_file_share objektu. Tady je ukázkový kód, který nemá.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>Vytvoření adresáře
Úložiště můžete uspořádat umístěním soubory podadresáře místo nutnosti všechny z nich v kořenovém adresáři. Soubory Azure můžete vytvořit tolik adresáře, které umožní váš účet. Následující kód vytvoří adresář s názvem **Moje ukázka directory** v kořenovém adresáři, jakož i podadresáři s názvem **Moje ukázka podadresáři**.

```cpp
// Retrieve a reference to a directory
azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Return value is true if the share did not exist and was successfully created.
directory.create_if_not_exists();

// Create a subdirectory.
azure::storage::cloud_file_directory subdirectory = 
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
subdirectory.create_if_not_exists();
```

## <a name="delete-a-directory"></a>Odstranění adresáře
Při odstranění adresáře je jednoduchou úlohou, i když je potřeba poznamenat, že nelze odstranit adresář, který ještě obsahuje soubory nebo ostatních adresářů.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// Get a reference to the directory.
azure::storage::cloud_file_directory directory = 
  share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Get a reference to the subdirectory you want to delete.
azure::storage::cloud_file_directory sub_directory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));

// Delete the subdirectory and the sample directory.
sub_directory.delete_directory_if_exists();

directory.delete_directory_if_exists();
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Vytvoření výčtu souborů a adresářů v sdílenou složku Azure
Získání seznamu souborů a adresářů v rámci sdílené složky se snadno provádí voláním **list_files_and_directories** na **cloud_file_directory** odkaz. Pro přístup k bohaté sadě vlastností a metod vrácené **list_file_and_directory_item**, musí volat **list_file_and_directory_item.as_file** metodu za účelem získání **cloud_file** objekt, nebo **list_file_and_directory_item.as_directory** metodu za účelem získání **cloud_file_directory** objektu.

Následující kód ukazuje, jak načíst a získat výstup URI pro každou položku v kořenovém adresáři sdílené složky.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = 
  share.get_root_directory_reference();

// Output URI of each item.
azure::storage::list_file_and_diretory_result_iterator end_of_results;

for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
{
    if(it->is_directory())
    {
        ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
    else if (it->is_file())
    {
        ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
    }        
}
```

## <a name="upload-a-file"></a>Nahrání souboru
V každém sdílenou složku Azure obsahuje kořenový adresář, kde mohou být uloženy soubory. V této části se dozvíte jak nahrát soubor z místního úložiště do kořenového adresáře sdílenou složku.

Prvním krokem při nahrání souboru se má získat odkaz na adresář, kde by měl být umístěn. To provedete pomocí volání **get_root_directory_reference** metodu objektu sdílené složky.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

Teď, když máte odkaz na kořenovém adresáři sdílené složky, můžete nahrát soubor do ho. Tento příklad ukládání ze souboru, z textu a z datového proudu.

```cpp
// Upload a file from a stream.
concurrency::streams::istream input_stream = 
  concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

azure::storage::cloud_file file1 = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
file1.upload_from_stream(input_stream);

// Upload some files from text.
azure::storage::cloud_file file2 = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
file2.upload_text(_XPLATSTR("more text"));

// Upload a file from a file.
azure::storage::cloud_file file4 = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
file4.upload_from_file(_XPLATSTR("DataFile.txt"));    
```

## <a name="download-a-file"></a>Stažení souboru
Ke stažení souborů, nejdřív načtěte odkaz na soubor a pak zavolají **download_to_stream** způsob přenosu obsahu souboru na objekt proudu, který potom můžete zachovat do místního souboru. Alternativně můžete použít **download_to_file** metoda pro stažení obsahu souboru do místního souboru. Můžete použít **download_text** metoda pro stažení obsahu soubor jako textový řetězec.

Následující příklad používá **download_to_stream** a **download_text** metody k předvedení stahování souborů, které byly vytvořeny v předchozích částech.

```cpp
// Download as text
azure::storage::cloud_file text_file = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
utility::string_t text = text_file.download_text();
ucout << "File Text: " << text << std::endl;

// Download as a stream.
azure::storage::cloud_file stream_file = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
stream_file.download_to_stream(output_stream);
std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();
outfile.write((char *)&data[0], buffer.size());
outfile.close();
```

## <a name="delete-a-file"></a>Odstranění souboru
Další běžné operace Azure Files je odstranění souborů. Následující kód odstraní soubor s názvem Moje – ukázka – soubor-3 uložená v kořenovém adresáři.

```cpp
// Get a reference to the root directory for the share.    
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

azure::storage::cloud_file_directory root_dir = 
  share.get_root_directory_reference();

azure::storage::cloud_file file = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

file.delete_file_if_exists();
```

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>Nastavit kvótu (maximální velikost) pro sdílenou složku Azure
Můžete nastavit kvótu (nebo maximální velikost) pro sdílenou složku, v gigabajtech. Můžete se taky podívat, kolik data je aktuálně uloženo ve sdílené složce.

Pokud nastavíte kvótu sdílené složky, můžete omezit celkovou velikost souborů uložených ve sdílené složce. Pokud celková velikost souborů ve sdílené složce překročí kvótu nastavenou pro sdílenou složku, klienti nebudou moct zvyšovat velikost existujících souborů, s výjimkou situace, když je velikost souborů nulová.

Dole uvedený příklad ukazuje, jak můžete zkontrolovat aktuální využití sdílené složky a jak nastavit kvótu pro sdílenou složku.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client.
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();

// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
if (share.exists())
{
    std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();

    //This line sets the quota to 2560GB
    share.resize(2560);

    std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();

}
```

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Vygenerování sdíleného přístupového podpisu pro soubor nebo sdílenou složku
Můžete vygenerovat sdílený přístupový podpis (SAS) pro sdílenou složku nebo pro konkrétní soubor. Můžete taky vytvořit sdílené zásady přístupu pro sdílenou složku ke správě sdílených přístupových podpisů. Vytvoření sdílených zásad přístupu se doporučuje, protože se tím nabízí způsob odvolání SAS v případě narušení jeho integrity nebo důvěryhodnosti.

V následujícím příkladu se vytvoří sdílená zásada přístupu pro sdílenou složku a ta se pak použije k omezení pro SAS na souboru ve sdílené složce.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client and get a reference to the share
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();

azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

if (share.exists())
{
    // Create and assign a policy
    utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

    azure::storage::file_shared_access_policy sharedPolicy = 
      azure::storage::file_shared_access_policy();

    //set permissions to expire in 90 minutes
    sharedPolicy.set_expiry(utility::datetime::utc_now() + 
       utility::datetime::from_minutes(90));

    //give read and write permissions
    sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

    //set permissions for the share
    azure::storage::file_share_permissions permissions;    

    //retrieve the current list of shared access policies
    azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;

    //add the new shared policy
    policies.insert(std::make_pair(policy_name, sharedPolicy));

    //save the updated policy list
    permissions.set_policies(policies);
    share.upload_permissions(permissions);

    //Retrieve the root directory and file references
    azure::storage::cloud_file_directory root_dir = 
        share.get_root_directory_reference();
    azure::storage::cloud_file file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

    // Generate a SAS for a file in the share 
    //  and associate this access policy with it.        
    utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);

    // Create a new CloudFile object from the SAS, and write some text to the file.        
    azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
    utility::string_t text = _XPLATSTR("My sample content");        
    file_with_sas.upload_text(text);        

    //Download and print URL with SAS.
    utility::string_t downloaded_text = file_with_sas.download_text();        
    ucout << downloaded_text << std::endl;        
    ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;

}
```
## <a name="next-steps"></a>Další postup
Další informace o službě Azure Storage najdete v těchto zdrojích informací:

* [Klientská knihovna pro úložiště pro C++](https://github.com/Azure/azure-storage-cpp)
* [Úložiště azure služba vzorky souborů v jazyce C++] (https://github.com/Azure-Samples/storage-file-cpp-getting-started)
* [Azure Storage Explorer](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Dokumentace k Azure Storage](https://azure.microsoft.com/documentation/services/storage/)