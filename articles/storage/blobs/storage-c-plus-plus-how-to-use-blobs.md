---
title: Jak používat úložiště objektů (objekt Blob) z jazyka C++ - Azure | Microsoft Docs
description: Ukládání nestrukturovaných dat v cloudu s úložištěm Azure Blob (objekt).
services: storage
author: MichaelHauss
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/21/2018
ms.author: michaelhauss
ms.openlocfilehash: d3297ae7bc4a5ac7e2a43d9d44a05365004b685f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31418782"
---
# <a name="how-to-use-blob-storage-from-c"></a>Používání úložiště Blob z jazyka C++

Tato příručka ukazuje, jak provádět běžné scénáře s využitím služby Azure Blob storage. Ukázky jsou napsané v C++ a použít [Klientská knihovna pro úložiště Azure pro jazyk C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Pokryté scénáře zahrnují odesílání, výpis, stahování a odstraňování objektů BLOB.  

> [!NOTE]
> Tato příručka se zaměřuje Klientská knihovna pro úložiště Azure pro jazyk C++ verze 1.0.0 a vyšší. Společnost Microsoft doporučuje používat nejnovější verzi klientské knihovny pro úložiště pro jazyk C++, k dispozici prostřednictvím [NuGet](http://www.nuget.org/packages/wastorage) nebo [Githubu](https://github.com/Azure/azure-storage-cpp).

## <a name="what-is-blob-storage"></a>Co je služba Blob storage?

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Vytvoření aplikace C++
V této příručce bude používat funkce úložiště, které lze spustit v rámci aplikace C++.  

V takovém případě budete muset nainstalovat Klientská knihovna pro úložiště Azure pro jazyk C++ a vytvoření účtu úložiště Azure ve vašem předplatném Azure.   

Pokud chcete nainstalovat Klientská knihovna pro úložiště Azure pro jazyk C++, můžete použít následující metody:

* **Linux:** postupujte podle pokynů uvedených v [Klientská knihovna pro úložiště Azure pro C++ – soubor README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) stránky.  
* **Windows:** v sadě Visual Studio, klikněte na tlačítko **nástroje > Správce balíčků NuGet > Konzola správce balíčků**. Pomocí následujícího příkazu do [Konzola správce balíčků NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) a stiskněte klávesu **ENTER**.  
  
     Install-Package wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Konfigurace aplikace pro přístup k úložišti objektů Blob
Přidejte následující příkazy na začátek souboru C++, ve které chcete používat pro přístup k objektům BLOB úložiště Azure rozhraní API obsahovat:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Instalační program připojovací řetězec úložiště Azure
Klienta Azure storage používá připojovací řetězec úložiště k ukládání koncových bodů a pověření pro přístup ke službám dat správy. Když spustíte v aplikaci klienta, je nutné zadat připojovací řetězec úložiště v následujícím formátu, pomocí názvu účtu úložiště a přístupový klíč úložiště pro účet úložiště uvedené v [portálu Azure](https://portal.azure.com) pro *AccountName* a *AccountKey* hodnoty. Informace o účtech úložiště a přístupové klávesy, najdete v části [o účtech úložiště Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Tento příklad ukazuje, jak můžou deklarovat statické pole pro uložení připojovací řetězec:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

K testování aplikace v místním počítači s Windows, můžete použít Microsoft Azure [emulátor úložiště](../storage-use-emulator.md) který se instaluje s [Azure SDK](https://azure.microsoft.com/downloads/). Emulátor úložiště je nástroj, který simuluje služby objektů Blob, fronty a tabulky, které jsou v Azure k dispozici na místním vývojovém počítači. Následující příklad ukazuje, jak můžou deklarovat statické pole pro uložení připojovací řetězec k vaší emulátor místního úložiště:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Spusťte emulátor úložiště Azure, vyberte **spustit** tlačítko nebo klikněte na tlačítko **Windows** klíč. Začněte psát **emulátoru úložiště Azure**a vyberte **emulátor úložiště Microsoft Azure** ze seznamu aplikací.  

Následující ukázky předpokládejme, že používáte jednu z těchto dvou metod k získání připojovacího řetězce úložiště.  

## <a name="retrieve-your-connection-string"></a>Načtení připojovacího řetězce
Můžete použít **cloud_storage_account** třída představující informací o vašem účtu úložiště. Chcete-li načíst informace o účtu úložiště z připojovacího řetězce úložiště, můžete použít **analyzovat** metoda.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

V dalším kroku získat odkaz na **cloud_blob_client** třídy jako umožňuje načtení objektů, které představují kontejnery a objekty BLOB uložené v úložišti objektů Blob. Následující kód vytvoří **cloud_blob_client** pomocí objekt účtu úložiště jsme načíst výše:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Postupy: vytvoření kontejneru
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Tento příklad ukazuje, jak vytvořit kontejner, pokud ještě neexistuje:  

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

Ve výchozím nastavení je nový kontejner privátní a je nutné zadat přístupový klíč k úložišti ke stažení z tohoto kontejneru objektů BLOB. Pokud chcete, aby soubory (objektů BLOB) v kontejneru dostupné pro všechny uživatele, můžete nastavit kontejner, aby se veřejné, pomocí následujícího kódu:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Kdokoli na Internetu může vidět objekty BLOB ve veřejném kontejneru, ale můžete upravit nebo odstranit pouze v případě, že máte příslušný přístupový klíč.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Postupy: nahrát objekt blob do kontejneru
Azure Blob storage podporuje blok objektů BLOB a objekty BLOB stránky. Ve většině případů se jako vhodný typ k použití doporučuje objekt blob bloku.  

Když chcete nahrát soubor do objektu blob bloku, získejte odkaz na kontejner a použijte ho k získání odkazu objektu blob bloku. Až budete mít odkaz na objekt blob, můžete nahrát jakýkoli proud dat k němu voláním **upload_from_stream** metoda. Tahle operace vytvoří objekt blob, pokud už dříve neexistoval, nebo ho přepíše, pokud už existoval. Následující příklad ukazuje, jak nahrát objekt blob do kontejneru, zároveň předpokládá, že kontejner byl již vytvořen.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

Alternativně můžete použít **upload_from_file** metoda chcete nahrát soubor do objektu blob bloku.

## <a name="how-to-list-the-blobs-in-a-container"></a>Postupy: seznam objektů BLOB v kontejneru
Pokud chcete mít seznam objektů blob v kontejneru, nejdřív získejte odkaz na kontejner. Pak můžete použít kontejneru **list_blobs** metoda pro načtení objektů BLOB a/nebo obsažené adresáře. Pro přístup k bohaté sadě vlastností a metod vrácené **list_blob_item**, musí volat **list_blob_item.as_blob** metodu za účelem získání **cloud_blob** objekt, nebo **list_blob.as_directory** metoda, jak získat objekt cloud_blob_directory. Následující kód ukazuje, jak načíst a získat výstup URI pro každou položku v **Moje ukázkový kontejner** kontejneru:

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

Další podrobnosti týkající se výpisu operací najdete v tématu [seznamu prostředků úložiště Azure v jazyce C++](../storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Postupy: stáhnout objekty BLOB
Pokud chcete stáhnout objekty BLOB, nejdřív načtěte odkaz na objekt blob a potom zavolejte **download_to_stream** metoda. Následující příklad používá **download_to_stream** způsob přenosu obsahu objektu blob na objekt proudu, který potom můžete zachovat do místního souboru.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

Alternativně můžete použít **download_to_file** metoda pro stažení obsahu objektu blob do souboru.
Kromě toho můžete také použít **download_text** metoda pro stažení obsahu objektu blob jako textový řetězec.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>Postupy: odstranění objektů BLOB
Chcete-li odstranit objekt blob, nejdřív získejte odkaz na objekt blob a potom volejte **delete_blob** metoda na něm.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili základy používání blob storage, postupujte podle následujících odkazech na další informace o službě Azure Storage.  

* [Postup používání úložiště Queue z jazyka C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Postup používání úložiště Table z jazyka C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Seznam prostředků úložiště Azure v jazyce C++](../storage-c-plus-plus-enumeration.md)
* [Klientská knihovna pro úložiště pro C++ – referenční informace](http://azure.github.io/azure-storage-cpp)
* [Dokumentace k Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Přenos dat pomocí nástroje příkazového řádku AzCopy](../storage-use-azcopy.md)

