---
title: Rychlý start Azure – Vytvoření objektu blob v úložišti objektů pomocí Ruby | Microsoft Docs
description: V tomto rychlém startu vytvoříte v úložišti objektů (blob) účet úložiště a kontejner. Pak použijete klientskou knihovnu pro úložiště pro Ruby k nahrání objektu blob do služby Azure Storage, stažení objektu blob a výpisu objektů blob v kontejneru.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 0bde1b7be15d49d82818f26d07c2ec633dc4526c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95523259"
---
# <a name="quickstart-upload-download-and-list-blobs-using-ruby"></a>Rychlý start: Nahrávání, stahování a výpis objektů blob pomocí Ruby

V tomto rychlém startu zjistíte, jak pomocí Ruby nahrávat, stahovat a vypisovat objekty blob bloku v kontejneru v úložišti objektů blob v Azure. 

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Ujistěte se, že máte nainstalované následující další požadavky:

* [Ruby](https://www.ruby-lang.org/en/downloads/)
* [Knihovna Azure Storage pro Ruby]()pomocí balíčku rubygem: 

    ```
    gem install azure-storage-blob
    ```
    
## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace
[Ukázková aplikace](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) použitá v tomto rychlém startu je základní aplikace v Ruby.  

Pomocí [Gitu](https://git-scm.com/) si stáhněte kopii aplikace do vývojového prostředí. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git 
```

Tento příkaz naklonuje úložiště do vaší místní složky gitu. Pokud chcete ukázkovou aplikaci Ruby otevřít, vyhledejte složku storage-blobs-ruby-quickstart a otevřete soubor example.rb.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště
V aplikaci musíte zadat název svého účtu úložiště a klíč účtu pro vytvoření instance `BlobService` ve vaší aplikaci. V Průzkumníku řešení vašeho integrovaného vývojového prostředí (IDE) otevřete soubor `example.rb`. Hodnoty **accountname** a **accountkey** nahraďte názvem a klíčem vašeho účtu. 

```ruby 
blob_client = Azure::Storage::Blob::BlobService.create(
            storage_account_name: account_name,
            storage_access_key: account_key
          )
```

## <a name="run-the-sample"></a>Spuštění ukázky
Tato ukázka vytvoří testovací soubor ve složce Dokumenty. Ukázkový program nahraje testovací soubor do úložiště objektů blob, vypíše objekty blob v kontejneru a stáhne soubor s novým názvem. 

Spusťte ukázku. Následující výstup je příkladem výstupu vráceného po spuštění aplikace:
  
```
Creating a container: quickstartblobs7b278be3-a0dd-438b-b9cc-473401f0c0e8

Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Když budete pokračovat stisknutím libovolné klávesy, ukázkový program odstraní kontejner úložiště i soubory. Než budete pokračovat, zkontrolujte, jestli složka Dokumenty obsahuje příslušné dva soubory. Můžete je otevřít a podívat se, že jsou identické.

K zobrazení souborů v úložišti objektů blob můžete použít také nástroj, jako je [Průzkumník služby Azure Storage](https://storageexplorer.com). Průzkumník služby Azure Storage je bezplatný nástroj pro více platforem, který umožňuje přístup k informacím o účtu úložiště. 

Po ověření souborů stiskněte libovolnou klávesu a dokončete ukázku a odstraňte testovací soubory. Když teď víte, co ukázka dělá, otevřete soubor example.rb a prohlédněte si kód. 

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

Dále si projdeme vzorový kód, abyste pochopili, jak funguje.

### <a name="get-references-to-the-storage-objects"></a>Získání odkazů na objekty úložiště
První věc, kterou je potřeba udělat, je vytvořit odkazy na objekty sloužící k přístupu k úložišti objektů blob a jeho správě. Tyto objekty se vzájemně využívají a každý z nich je využívaný dalším objektem v seznamu.

* Vytvořte instanci objektu úložiště Azure **BlobService** pro nastavení přihlašovacích údajů pro připojení. 
* Vytvořte objekt **Container**, který představuje kontejner, ke kterému přistupujete. Kontejnery slouží k uspořádání objektů blob podobně jako složky na počítači k uspořádání souborů.

Jakmile budete mít CloudBlobContainer, můžete vytvořit objekt blobu **Block**, který odkazuje na konkrétní blob, který vás zajímá, a provádět operace, jako jsou nahrávání, stahování a kopírování.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

V této části vytvoříte instanci klienta úložiště Azure, instanci objektu služby blobu, nový kontejner a pak nastavíte oprávnění ke kontejneru tak, aby objekty blob byly veřejné. Kontejner má název **quickstartblobs**. 

```ruby 
# Create a BlobService object
blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
    )

# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs' + SecureRandom.uuid
container = blob_client.create_container(container_name)   

# Set the permission so the blobs are public.
blob_client.set_container_acl(container_name, "container")
```

### <a name="upload-blobs-to-the-container"></a>Nahrání objektů blob do kontejneru

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Nejčastěji používané jsou objekty blob bloku, které se používají také v tomto rychlém startu.  

Pokud chcete nahrát soubor do objektu blob, získejte úplnou cestu k souboru spojením názvu adresáře a názvu souboru na místním disku. Pak můžete soubor nahrát do zadané cesty pomocí metody **Create \_ Block \_ BLOB ()** . 

Vzorový kód vytvoří místní soubor, který se použije k nahrání a stažení. Soubor k nahrání uloží do proměnné **full\_path\_to\_file** a název objektu blob do proměnné **local\_file\_name**. Následující příklad nahraje soubor do kontejneru **quickstartblobs**.

```ruby
# Create a file in Documents to test the upload and download.
local_path=File.expand_path("~/Documents")
local_file_name ="QuickStart_" + SecureRandom.uuid + ".txt"
full_path_to_file =File.join(local_path, local_file_name)

# Write text to the file.
file = File.open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

puts "Temp file = " + full_path_to_file
puts "\nUploading to Blob storage as blob" + local_file_name

# Upload the created file, using local_file_name for the blob name
blob_client.create_block_blob(container.name, local_file_name, full_path_to_file)
```

K provedení částečné aktualizace obsahu objektu blob bloku použijte metodu **vytvořit \_ seznam blokovaných objektů \_ ()** . Objekty blob bloku můžou mít velikost až 4,7 TB a můžou být čímkoli od tabulky aplikace Excel po velké videosoubory. Objekty blob stránky se používají hlavně pro soubory VHD využívané virtuálními počítači IaaS. Doplňovací objekty blob se používají k protokolování, například když chcete zapisovat do souboru a pak přidávat další informace. Doplňovací objekt blob by se měl používat v modelu s jedním zapisujícím procesem. Většina objektů uložených v úložišti objektů blob je objekty blob bloku.

### <a name="list-the-blobs-in-a-container"></a>Seznam objektů blob v kontejneru

Seznam souborů v kontejneru můžete získat pomocí metody **List \_ BLOBS ()** . Následující kód načte seznam objektů blob, pak je ve smyčce projde a zobrazí názvy nalezených objektů blob v kontejneru.  

```ruby
# List the blobs in the container
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-the-blobs"></a>Stažení objektů blob

Objekty blob můžete stáhnout na místní disk pomocí metody **get\_blob()**. Následující kód stáhne objekt blob nahraný v předchozí části. K názvu objektu blob se přidá přípona „_DOWNLOADED“, takže na místním disku uvidíte oba soubory. 

```ruby
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = File.join(local_path, local_file_name.gsub('.txt', '_DOWNLOADED.txt'))

puts "\n Downloading blob to " + full_path_to_file2
blob, content = blob_client.get_blob(container_name,local_file_name)
File.open(full_path_to_file2,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už nepotřebujete objekty blob nahrané v rámci tohoto rychlého startu, můžete celý kontejner odstranit pomocí metody **Delete \_ Container ()** . Pokud již vytvořené soubory nejsou potřeba, použijte k odstranění souborů metodu **Delete \_ BLOB ()** .

```ruby
# Clean up resources. This includes the container and the temp files
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
File.delete(full_path_to_file2)    
```
## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Zdroje informací pro vývoj aplikací v Ruby s využitím objektů blob

Prohlédněte si tyto další zdroje informací o vývoji v Ruby s využitím úložiště objektů blob:

- Prohlédněte a stáhněte si [zdrojový kód klientské knihovny pro Ruby](https://github.com/Azure/azure-storage-ruby) pro službu Azure Storage na GitHubu.
- Prozkoumejte [ukázky pro úložiště objektů blob](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=ruby&term=blob) napsané s využitím klientské knihovny pro Ruby.

## <a name="next-steps"></a>Další kroky
 
V tomto rychlém startu jste zjistili, jak přenášet soubory mezi místním diskem a úložištěm objektů blob v Azure pomocí Ruby. Další informace o práci s úložištěm objektů blob najdete v postupech pro úložiště objektů blob.

> [!div class="nextstepaction"]
> [Operace s úložištěm objektů blob – postupy]()


Další informace o Průzkumníku služby Storage a objektech blob najdete v tématu [Správa prostředků úložiště objektů blob v Azure pomocí Průzkumníka služby Storage](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).