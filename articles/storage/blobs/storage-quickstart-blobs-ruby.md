---
title: 'Rychlý Start: Klientská knihovna pro Azure Blob Storage – Ruby'
description: Vytvořte účet úložiště a kontejner ve službě Azure Blob Storage. Použijte klientskou knihovnu pro úložiště pro Ruby k vytvoření objektu blob, stažení objektu BLOB a výpisu objektů BLOB v kontejneru.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/04/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: ec3fc490466f5fce36b67b2f3744e4ee5cc0ae79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96781092"
---
# <a name="quickstart-azure-blob-storage-client-library-for-ruby"></a>Rychlý Start: Klientská knihovna pro Azure Blob Storage pro Ruby

Naučte se pomocí Ruby vytvářet, stahovat a vypisovat objekty BLOB v kontejneru ve Microsoft Azure Blob Storage.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Ujistěte se, že máte nainstalované následující další požadavky:

- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Knihovna Azure Storage pro Ruby](https://github.com/azure/azure-storage-ruby)pomocí [balíčku RubyGem](https://rubygems.org/gems/azure-storage-blob):

    ```console
    gem install azure-storage-blob
    ```

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

[Ukázková aplikace](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) použitá v tomto rychlém startu je základní aplikace v Ruby.

Pomocí [Gitu](https://git-scm.com/) si stáhněte kopii aplikace do vývojového prostředí. Tento příkaz naklonuje úložiště do místního počítače:

```console
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git
```

Přejděte do složky *Storage-BLOBs-Ruby-Started* a otevřete *vzorový soubor. RB* v editoru kódu.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

Zadejte název účtu úložiště a klíč účtu pro vytvoření instance [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) pro vaši aplikaci.

Následující kód v souboru *example. RB* vytvoří instanci nového objektu [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) . Hodnoty *accountname* a *accountkey* nahraďte názvem a klíčem vašeho účtu.

```ruby
# Create a BlobService object
account_name = "accountname"
account_key = "accountkey"

blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
)
```

## <a name="run-the-sample"></a>Spuštění ukázky

Ukázka vytvoří kontejner v Blob Storage, vytvoří nový objekt BLOB v kontejneru, vypíše objekty BLOB v kontejneru a stáhne objekt blob do místního souboru.

Spusťte ukázku. Tady je příklad výstupu spuštění aplikace:

```console
C:\azure-samples\storage-blobs-ruby-quickstart> ruby example.rb

Creating a container: quickstartblobs18cd9ec0-f4ac-4688-a979-75c31a70503e

Creating blob: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

List blobs in the container following continuation token
        Blob name: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Downloading blob to C:/Users/azureuser/Documents/QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Paused, press the Enter key to delete resources created by the sample and exit the application
```

Když budete pokračovat stisknutím klávesy ENTER, ukázkový program odstraní kontejner úložiště a místní soubor. Než budete pokračovat, vyhledejte stažený soubor ve složce *dokumenty* .

Můžete také použít [Průzkumník služby Azure Storage](https://storageexplorer.com) k zobrazení souborů v účtu úložiště. Průzkumník služby Azure Storage je bezplatný nástroj pro více platforem, který umožňuje přístup k informacím o účtu úložiště.

Po ověření souborů stiskněte klávesu ENTER a odstraňte testovací soubory a ukončete ukázku. Otevřete soubor *example. RB* a Prohlédněte si kód.

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

V dalším kroku projdeme ukázkový kód, abyste mohli pochopit, jak to funguje.

### <a name="get-references-to-the-storage-objects"></a>Získání odkazů na objekty úložiště

První věc, kterou je třeba udělat, je vytvořit instance objektů používaných pro přístup a správu Blob Storage. Tyto objekty se sestavují jeden podle druhého. Každý další objekt použije ten, který je v seznamu před ním.

- Vytvořte instanci objektu úložiště Azure [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) pro nastavení přihlašovacích údajů pro připojení.
- Vytvořte [kontejnerový](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container/Container) objekt, který představuje kontejner, ke kterému přistupujete. Kontejnery slouží k uspořádání objektů blob podobně jako složky na počítači k uspořádání souborů.

Jakmile budete mít objekt kontejneru, můžete vytvořit objekt BLOB [bloku](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) , který odkazuje na konkrétní objekt blob, který vás zajímá. Objekt [Block](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) použijte k vytvoření, stažení a zkopírování objektů BLOB.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o názvech kontejnerů a objektů BLOB najdete v tématu [pojmenování a odkazování kontejnerů, objektů BLOB a metadat](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Následující příklad kódu:

- Vytvoří nový kontejner.
- Nastaví oprávnění ke kontejneru tak, aby objekty blob byly veřejné. Kontejner se nazývá *quickstartblobs* s připojeným jedinečným ID.

```ruby
# Create a container
container_name = "quickstartblobs" + SecureRandom.uuid
puts "\nCreating a container: " + container_name
container = blob_client.create_container(container_name)

# Set the permission so the blobs are public
blob_client.set_container_acl(container_name, "container")
```

### <a name="create-a-blob-in-the-container"></a>Vytvoření objektu BLOB v kontejneru

Blob Storage podporuje objekty blob bloku, doplňovací objekty BLOB a objekty blob stránky. Chcete-li vytvořit objekt blob, zavolejte metodu [create_block_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#create_block_blob-instance_method) , která předává data objektu BLOB.

Následující příklad vytvoří objekt BLOB s názvem *QuickStart_* s jedinečným ID a příponou souboru *. txt* v kontejneru, který byl vytvořen dříve.

```ruby
# Create a new block blob containing 'Hello, World!'
blob_name = "QuickStart_" + SecureRandom.uuid + ".txt"
blob_data = "Hello, World!"
puts "\nCreating blob: " + blob_name
blob_client.create_block_blob(container.name, blob_name, blob_data)
```

Objekty blob bloku můžou být velké až 4,7 TB a můžou být cokoli z tabulek až po velké videosoubory. Objekty blob stránky se primárně používají pro soubory VHD, které back IaaS virtuální počítače. Doplňovací objekty BLOB se běžně používají k protokolování, například když chcete zapisovat do souboru a pak dál přidávat další informace.

### <a name="list-the-blobs-in-a-container"></a>Seznam objektů blob v kontejneru

Získejte seznam souborů v kontejneru pomocí metody [list_blobs](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#list_blobs-instance_method). Následující kód načte seznam objektů BLOB a potom zobrazí jejich názvy.

```ruby
# List the blobs in the container
puts "\nList blobs in the container following continuation token"
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name: #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-a-blob"></a>Stažení objektu blob

Stáhněte si objekt blob na místní disk pomocí metody [get_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#get_blob-instance_method) . Následující kód stáhne objekt BLOB vytvořený v předchozí části.

```ruby
# Download the blob

# Set the path to the local folder for downloading
if(is_windows)
    local_path = File.expand_path("~/Documents")
else 
    local_path = File.expand_path("~/")
end

# Create the full path to the downloaded file
full_path_to_file = File.join(local_path, blob_name)

puts "\nDownloading blob to " + full_path_to_file
blob, content = blob_client.get_blob(container_name, blob_name)
File.open(full_path_to_file,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už objekt BLOB nepotřebujete, odeberte ho pomocí [delete_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#delete_blob-instance_method) . Pomocí metody [delete_container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#delete_container-instance_method) odstraňte celý kontejner. Odstraněním kontejneru dojde také k odstranění všech objektů BLOB uložených v kontejneru.

```ruby
# Clean up resources, including the container and the downloaded file
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
```

## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Zdroje informací pro vývoj aplikací v Ruby s využitím objektů blob

Podívejte se na tyto další zdroje informací o vývoji Ruby:

- Prohlédněte a stáhněte si [zdrojový kód klientské knihovny pro Ruby](https://github.com/Azure/azure-storage-ruby) pro službu Azure Storage na GitHubu.
- Prozkoumejte [ukázky Azure](/samples/browse/?products=azure&languages=ruby) napsané pomocí klientské knihovny Ruby.
- [Ukázka: Začínáme s Azure Storage v Ruby](https://github.com/Azure-Samples/storage-blob-ruby-getting-started)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak přenášet soubory mezi službou Azure Blob Storage a místním diskem pomocí Ruby. Pokud chcete získat další informace o práci s Blob Storage, přejděte k přehledu účtu úložiště.

> [!div class="nextstepaction"]
> [Přehled účtu úložiště](../common/storage-account-overview.md)

Další informace o Průzkumník služby Storage a objektech blob najdete v tématu [Správa prostředků Azure Blob Storage pomocí Průzkumník služby Storage](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
