---
title: Jak používat úložiště objektů (Blob) z C++ – Azure | Dokumenty společnosti Microsoft
description: Uklápěte nestrukturovaná data v cloudu pomocí úložiště objektů blob (objekt) Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/21/2018
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 0a9015e33f5456efeac7f7c887995ac4a69f0259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941810"
---
# <a name="how-to-use-blob-storage-from-c"></a>Použití úložiště objektů Blob z C++

Tato příručka ukazuje, jak provádět běžné scénáře pomocí úložiště objektů blob Azure. Příklady ukazují, jak nahrát, zobrazit seznam, stáhnout a odstranit objekty BLOB. Ukázky jsou napsané v C++ a využívají [klientskou knihovnu služby Azure Storage pro C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).   

Další informace o úložišti objektů Blob najdete [v tématu Úvod do úložiště objektů blob Azure](storage-blobs-introduction.md).

> [!NOTE]
> Tato příručka je určená pro klientskou knihovnu služby Azure Storage pro C++ verze 1.0.0 nebo novější. Společnost Microsoft doporučuje používat nejnovější verzi klientské knihovny úložiště pro C++, která je k dispozici prostřednictvím [NuGet](https://www.nuget.org/packages/wastorage) nebo [GitHub](https://github.com/Azure/azure-storage-cpp).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Vytvoření aplikace C++
V této příručce budete používat funkce úložiště, které lze spustit v rámci aplikace Jazyka C++.  

Abyste mohli pokračovat, musíte si nainstalovat klientskou knihovnu služby Azure Storage pro C++ a vytvořit ve svém předplatném účet úložiště Azure.   

Klientskou knihovnu služby Azure Storage pro C++ můžete nainstalovat následujícími způsoby:

* **Linux:** Postupujte podle pokynů uvedených v [klientské knihovně úložiště Azure pro C++ README: Začínáme na](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) stránce Linux.
* **Windows:** V systému Windows použijte [vcpkg](https://github.com/microsoft/vcpkg) jako správce závislostí. Postupujte podle [rychlého startu](https://github.com/microsoft/vcpkg#quick-start) a inicializovat vcpkg. Potom použijte následující příkaz k instalaci knihovny:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Můžete najít návod, jak vytvořit zdrojový kód a exportovat do NuGet v souboru [README.](https://github.com/Azure/azure-storage-cpp#download--install)

## <a name="configure-your-application-to-access-blob-storage"></a>Konfigurace aplikace pro přístup k úložišti objektů Blob
Přidejte následující příkazy zahrnout do horní části souboru C++, kde chcete použít Azure storage API pro přístup k objektům BLOB:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Nastavení připojovacího řetězce úložiště Azure
Klient úložiště Azure používá připojovací řetězec úložiště k uložení koncových bodů a přihlašovacích údajů pro přístup ke službám správy dat. Při spuštění v klientské aplikaci, musíte zadat připojovací řetězec úložiště v následujícím formátu, pomocí názvu účtu úložiště a přístupový klíč úložiště pro účet úložiště uvedené na [portálu Azure pro](https://portal.azure.com) *AccountName* a *AccountKey* hodnoty. Informace o účtech úložiště a přístupových klíčích najdete [v tématu O účtech úložiště Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Tento příklad ukazuje deklaraci statického pole pro uložení připojovacího řetězce:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Chcete-li otestovat aplikaci v místním počítači se systémem Windows, můžete použít [emulátor úložiště](../storage-use-emulator.md) Microsoft Azure, který je nainstalovaný pomocí [sady Azure SDK](https://azure.microsoft.com/downloads/). Emulátor úložiště je nástroj, který simuluje služby objektů Blob, Fronty a Tabulka dostupné v Azure na místním vývojovém počítači. Následující příklad ukazuje deklaraci statického pole pro uložení připojovacího řetězce k místnímu emulátoru úložiště:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Pokud chcete spustit emulátor úložiště Azure, vyberte tlačítko **Start** nebo stiskněte klávesu **Windows.** Začněte psát **Emulátor úložiště Azure**a ze seznamu aplikací vyberte **Emulátor úložiště Microsoft Azure.**  

V následujících ukázkách se předpokládá, že jste pomocí některé z těchto dvou metod získali připojovací řetězec úložiště.  

## <a name="retrieve-your-storage-account"></a>Načtení účtu úložiště
Třídu **cloud_storage_account** můžete použít k reprezentaci informací o účtu úložiště. K načtení informací o vašem účtu úložiště z připojovacího řetězce úložiště můžete použít metodu **parse**.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Dále získat odkaz na **třídu cloud_blob_client,** protože umožňuje načíst objekty, které představují kontejnery a objekty BLOB uložené v úložišti objektů Blob. Následující kód vytvoří **objekt cloud_blob_client** pomocí objektu účtu úložiště, který jsme načetli výše:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Postup: Vytvoření kontejneru
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

Ve výchozím nastavení je nový kontejner soukromý a je nutné zadat přístupový klíč úložiště pro stahování objektů BLOB z tohoto kontejneru. Pokud chcete zpřístupnit soubory (objekty BLOB) v kontejneru všem uživatelům, můžete nastavit kontejner jako veřejný pomocí následujícího kódu:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Kdokoli na Internetu může zobrazit objekty BLOB ve veřejném kontejneru, ale můžete je upravit nebo odstranit pouze v případě, že máte příslušný přístupový klíč.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Postup: Nahrání objektu blob do kontejneru
Azure Blob storage podporuje objekty BLOB bloku a objekty BLOB stránky. Ve většině případů se jako vhodný typ k použití doporučuje objekt blob bloku.  

Když chcete nahrát soubor do objektu blob bloku, získejte odkaz na kontejner a použijte ho k získání odkazu objektu blob bloku. Jakmile budete mít odkaz na objekt blob, můžete do něj nahrát libovolný datový proud voláním **metody upload_from_stream.** Tahle operace vytvoří objekt blob, pokud už dříve neexistoval, nebo ho přepíše, pokud už existoval. Následující příklad ukazuje, jak nahrát objekt blob do kontejneru, zároveň předpokládá, že kontejner byl již vytvořen.  

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

Alternativně můžete použít **metodu upload_from_file** k nahrání souboru do objektu blob bloku.

## <a name="how-to-list-the-blobs-in-a-container"></a>Postup: Vypsat objekty BLOB v kontejneru
Pokud chcete mít seznam objektů blob v kontejneru, nejdřív získejte odkaz na kontejner. Potom můžete použít **list_blobs** metody kontejneru k načtení objektů BLOB nebo adresářů v něm. Chcete-li získat přístup k bohaté sadě vlastností a metod pro vrácenou **list_blob_item**, musíte volat metodu **list_blob_item.as_blob,** abyste získali **cloud_blob** objekt, nebo metodu **list_blob.as_directory,** abyste získali cloud_blob_directory objekt. Následující kód ukazuje, jak načíst a výstup URI každé položky v kontejneru **kontejneru můj vzorek:**

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

Další podrobnosti o výpisu operací, najdete [v seznamu prostředků úložiště Azure v Jazyce C++](../storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Postup: Stažení objektů BLOB
Chcete-li stáhnout objekty BLOB, nejprve načtěte odkaz na objekt blob a pak zavolejte metodu **download_to_stream.** Následující příklad používá **metodu download_to_stream** k přenosu obsahu objektu blob do objektu datového proudu, který pak můžete zachovat do místního souboru.  

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

Alternativně můžete použít **metodu download_to_file** ke stažení obsahu objektu blob do souboru.
Kromě toho můžete také použít **metodu download_text** ke stažení obsahu objektu blob jako textového řetězce.  

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

## <a name="how-to-delete-blobs"></a>Postup: Odstranění objektů BLOB
Chcete-li odstranit objekt blob, nejprve získat odkaz na objekt blob a potom volat **delete_blob** metodu na něm.  

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

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy úložiště objektů blob, postupujte podle těchto odkazů další informace o Azure Storage.  

* [Používání úložiště Queue z C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Použití úložiště tabulek z jazyka C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Seznam prostředků úložiště Azure v jazyce C++](../storage-c-plus-plus-enumeration.md)
* [Klientská knihovna úložiště pro odkaz na C++](https://azure.github.io/azure-storage-cpp)
* [Dokumentace k Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Přenos dat pomocí nástroje příkazového řádku AzCopy](../storage-use-azcopy.md)

