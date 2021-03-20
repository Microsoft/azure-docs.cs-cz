---
title: Jak používat úložiště objektů (BLOB) z C++ – Azure | Microsoft Docs
description: Naučte se ukládat nestrukturovaná data (objekty BLOB) do cloudu pomocí úložiště objektů BLOB (Object) Azure pomocí C++.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/16/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 64069292ea0059216d06bfc41316c2aed7484dd0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011094"
---
# <a name="how-to-use-blob-storage-from-c"></a>Používání úložiště BLOB z C++

Tato příručka ukazuje, jak provádět běžné scénáře pomocí úložiště objektů BLOB v Azure. Příklady ukazují, jak nahrát, vypsat, stáhnout a odstranit objekty blob. Ukázky jsou napsané v C++ a využívají [klientskou knihovnu služby Azure Storage pro C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).
Další informace o službě BLOB Storage najdete v tématu [Úvod do úložiště objektů BLOB v Azure](storage-blobs-introduction.md).

> [!NOTE]
> Tato příručka je určená pro klientskou knihovnu služby Azure Storage pro C++ verze 1.0.0 nebo novější. Microsoft doporučuje používat nejnovější verzi klientské knihovny pro úložiště pro C++, která je dostupná přes [NuGet](https://www.nuget.org/packages/wastorage) nebo [GitHub](https://github.com/Azure/azure-storage-cpp).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Vytvoření aplikace C++
V této příručce budete používat funkce úložiště, které se dají spouštět v rámci aplikace v jazyce C++.

Abyste mohli pokračovat, musíte si nainstalovat klientskou knihovnu služby Azure Storage pro C++ a vytvořit ve svém předplatném účet úložiště Azure.

Klientskou knihovnu služby Azure Storage pro C++ můžete nainstalovat následujícími způsoby:

- **Linux:** Postupujte podle pokynů uvedených v tématu [Azure Storage Client Library for C++ Ready: Začínáme na stránce systému Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) .
- **Windows:** Ve Windows použijte [vcpkg](https://github.com/microsoft/vcpkg) jako správce závislostí. Postupujte podle pokynů v [rychlém](https://github.com/microsoft/vcpkg#quick-start) startu a inicializujte vcpkg. Potom pomocí následujícího příkazu nainstalujte knihovnu:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Můžete najít průvodce pro sestavení zdrojového kódu a exportovat ho do NuGet v souboru [Readme](https://github.com/Azure/azure-storage-cpp#download--install) .

## <a name="configure-your-application-to-access-blob-storage"></a>Konfigurace aplikace pro přístup k úložišti objektů BLOB
Do horní části souboru C++ přidejte následující příkazy include, u kterých chcete používat rozhraní API služby Azure Storage pro přístup k objektům blob:

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Nastavení připojovacího řetězce úložiště Azure
Klient úložiště Azure používá připojovací řetězec úložiště k uložení koncových bodů a přihlašovacích údajů pro přístup ke službám správy dat. Při spuštění v klientské aplikaci musíte zadat připojovací řetězec úložiště v následujícím formátu s použitím názvu účtu úložiště a přístupového klíče úložiště pro účet úložiště, který je uvedený v [Azure Portal](https://portal.azure.com) pro hodnoty *account* a *AccountKey* . Informace o účtech úložiště a přístupových klíčích najdete v tématu [informace o Azure Storagech účtech](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Tento příklad ukazuje deklaraci statického pole pro uložení připojovacího řetězce:

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

K otestování aplikace na místním počítači s Windows můžete použít [emulátor úložiště Azurite](../common/storage-use-azurite.md). Azurite je nástroj, který simuluje služby objektů BLOB a front, které jsou dostupné v Azure, na vašem místním vývojovém počítači. Následující příklad ukazuje deklaraci statického pole pro uložení připojovacího řetězce k místnímu emulátoru úložiště:

```cpp
// Define the connection-string with Azurite.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));
```

Pokud chcete začít Azurite, přečtěte si téma [použití emulátoru Azurite pro vývoj místních Azure Storage](../common/storage-use-azurite.md).

V následujících ukázkách se předpokládá, že jste pomocí některé z těchto dvou metod získali připojovací řetězec úložiště.

## <a name="retrieve-your-storage-account"></a>Načtení účtu úložiště
K reprezentaci informací o účtu úložiště můžete použít třídu **cloud_storage_account** . K načtení informací o vašem účtu úložiště z připojovacího řetězce úložiště můžete použít metodu **parse**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Dále získáte odkaz na třídu **cloud_blob_client** , protože umožňuje načíst objekty, které reprezentují kontejnery a objekty blob uložené v úložišti objektů BLOB. Následující kód vytvoří objekt **cloud_blob_client** pomocí objektu účtu úložiště, který jsme získali výše:

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

Ve výchozím nastavení je nový kontejner privátní a musíte zadat přístupový klíč k úložišti pro stahování objektů BLOB z tohoto kontejneru. Pokud chcete, aby byly soubory (objekty BLOB) v kontejneru dostupné všem uživatelům, můžete nastavit, aby byl kontejner veřejný, a to pomocí následujícího kódu:

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Kdokoli na internetu může vidět objekty blob ve veřejném kontejneru, ale můžete je upravit nebo odstranit jenom v případě, že máte příslušný přístupový klíč.

## <a name="how-to-upload-a-blob-into-a-container"></a>Postupy: nahrání objektu blob do kontejneru
Úložiště objektů BLOB v Azure podporuje objekty blob bloku a objekty blob stránky. Ve většině případů se jako vhodný typ k použití doporučuje objekt blob bloku.

Když chcete nahrát soubor do objektu blob bloku, získejte odkaz na kontejner a použijte ho k získání odkazu objektu blob bloku. Jakmile budete mít odkaz na objekt blob, můžete do něj nahrát libovolný datový proud zavoláním metody **upload_from_stream** . Tahle operace vytvoří objekt blob, pokud už dříve neexistoval, nebo ho přepíše, pokud už existoval. Následující příklad ukazuje, jak nahrát objekt blob do kontejneru, zároveň předpokládá, že kontejner byl již vytvořen.

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

Alternativně můžete použít metodu **upload_from_file** pro nahrání souboru do objektu blob bloku.

## <a name="how-to-list-the-blobs-in-a-container"></a>Postupy: výpis objektů BLOB v kontejneru
Pokud chcete mít seznam objektů blob v kontejneru, nejdřív získejte odkaz na kontejner. Pak můžete použít metodu **list_blobs** kontejneru k načtení objektů BLOB a adresářů v ní. Chcete-li získat přístup k bohatě se sadou vlastností a metod vrácených **list_blob_item**, je nutné volat metodu **list_blob_item. as _blob** pro získání objektu  **cloud_blob** nebo metody **list_blob. as _directory** pro získání objektu cloud_blob_directory. Následující kód ukazuje, jak načíst a vystavit výstup identifikátoru URI každé položky v kontejneru **My-Sample-Container** :

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

Další informace o výpisu operací najdete v tématu [seznam Azure Storage prostředků v jazyce C++](../common/storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Postupy: stahování objektů BLOB
Chcete-li stáhnout objekty blob, načtěte nejprve odkaz na objekt BLOB a potom zavolejte metodu **download_to_stream** . Následující příklad používá metodu **download_to_stream** k přenosu obsahu objektu blob do objektu streamu, který pak můžete zachovat do místního souboru.

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

Alternativně můžete pomocí metody **download_to_file** stáhnout obsah objektu blob do souboru.
Kromě toho můžete také pomocí metody **download_text** stáhnout obsah objektu BLOB jako textový řetězec.

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
Pokud chcete odstranit objekt blob, nejdřív Získejte odkaz na objekt BLOB a pak na něj volejte metodu **delete_blob** .

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
Teď, když jste se naučili základy služby Blob Storage, můžete získat další informace o Azure Storage pomocí těchto odkazů.

- [Používání úložiště Queue z C++](../queues/storage-c-plus-plus-how-to-use-queues.md)
- [Použití Table Storage z C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
- [Seznam prostředků Azure Storage v jazyce C++](../common/storage-c-plus-plus-enumeration.md)
- [Reference ke klientské knihovně pro úložiště pro C++](https://azure.github.io/azure-storage-cpp)
- [Dokumentace k Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
- [Přenos dat pomocí nástroje příkazového řádku AzCopy](../common/storage-use-azcopy-v10.md)