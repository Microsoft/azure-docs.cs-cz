---
title: Rychlý start Azure – Vytvoření objektu blob v úložišti objektů pomocí PHP | Microsoft Docs
description: Rychle se naučíte, jak přenášet objekty do a z úložiště objektů blob v Azure pomocí PHP.
services: storage
author: roygara
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: rogarana
ms.openlocfilehash: 463b6051009ab79013f9bef958fcadec2ef3cd16
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-php"></a>Přenos objektů do a z úložiště objektů blob v Azure pomocí PHP
V tomto rychlém startu zjistíte, jak pomocí PHP nahrávat, stahovat a vypisovat objekty blob bloku v kontejneru v úložišti objektů blob v Azure. 

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba: 
* Nainstalovat [PHP](http://php.net/downloads.php).
* Nainstalovat sadu [SDK služby Azure Storage pro PHP](https://github.com/Azure/azure-storage-php).


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace
[Ukázková aplikace](https://github.com/Azure-Samples/storage-blobs-php-quickstart.git) použitá v tomto rychlém startu je základní aplikace PHP.  

Pomocí [gitu](https://git-scm.com/) stáhněte kopii aplikace do svého vývojového prostředí. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-php-quickstart.git
```

Tento příkaz naklonuje úložiště do vaší místní složky gitu. Pokud chcete ukázkovou aplikaci PHP otevřít, vyhledejte složku storage-blobs-php-quickstart a otevřete soubor phpqs.php.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště
V aplikaci musíte zadat název svého účtu úložiště a klíč účtu pro vytvoření instance **BlobRestProxy** ve vaší aplikaci. Doporučujeme uložit tyto identifikátory do proměnné prostředí v místním počítači, na kterém aplikaci spouštíte. V závislosti na operačním systému vytvořte proměnnou prostředí pomocí jednoho z následujících příkladů. Hodnoty **youraccountname** a **youraccountkey** nahraďte názvem a klíčem vašeho účtu.

# <a name="linux-tablinux"></a>[Linux] (#tab/linux)

```bash
export ACCOUNT_NAME=<youraccountname>
export ACCOUNT_KEY=<youraccountkey>
```

# <a name="windows-tabwindows"></a>[Windows] (#tab/windows)

```cmd
setx ACCOUNT_NAME=<youraccountname>
setx ACCOUNT_KEY=<youraccountkey>
```
---

## <a name="configure-your-environment"></a>Konfigurace prostředí
Nahraďte složku v adresáři obsluhovaném vaším serverem PHP složkou z vaší místní složky gitu. Pak otevřete příkazový řádek v oboru stejného adresáře a zadejte: `php composer.phar install`

## <a name="run-the-sample"></a>Spuštění ukázky
Tato ukázka vytvoří ve složce „.“ testovací soubor. Ukázkový program nahraje testovací soubor do úložiště objektů blob, vypíše objekty blob v kontejneru a stáhne soubor s novým názvem. 

Spusťte ukázku. Následující výstup je příkladem výstupu vráceného po spuštění aplikace:
  
```
Uploading BlockBlob: HelloWorld.txt
These are the blobs present in the container: HelloWorld.txt: https://myexamplesacct.blob.core.windows.net/blockblobsleqvxd/HelloWorld.txt

This is the content of the blob uploaded: Hello Azure!
```
Když stisknete zobrazenou klávesu, ukázkový program odstraní kontejner úložiště i soubory. Než budete pokračovat, zkontrolujte, jestli složka na vašem serveru obsahuje příslušné dva soubory. Můžete je otevřít a podívat se, že jsou identické.

K zobrazení souborů v úložišti objektů blob můžete použít také nástroj, jako je [Průzkumník služby Azure Storage](http://storageexplorer.com). Průzkumník služby Azure Storage je bezplatný nástroj pro více platforem, který umožňuje přístup k informacím o účtu úložiště. 

Po ověření souborů stiskněte libovolnou klávesu a dokončete ukázku a odstraňte testovací soubory. Když teď víte, co ukázka dělá, otevřete soubor example.rb a prohlédněte si kód. 

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

Dále si projdeme vzorový kód, abyste pochopili, jak funguje.

### <a name="get-references-to-the-storage-objects"></a>Získání odkazů na objekty úložiště
První věc, kterou je potřeba udělat, je vytvořit odkazy na objekty sloužící k přístupu k úložišti objektů blob a jeho správě. Tyto objekty se vzájemně využívají a každý z nich je využívaný dalším objektem v seznamu.

* Vytvořte instanci objektu úložiště Azure **BlobRestProxy** pro nastavení přihlašovacích údajů pro připojení. 
* Vytvořte objekt **BlobService**, který odkazuje na službu Blob service ve vašem účtu úložiště. 
* Vytvořte objekt **Container**, který představuje kontejner, ke kterému přistupujete. Kontejnery slouží k uspořádání objektů blob podobně jako složky na počítači k uspořádání souborů.

Jakmile budete mít objekt kontejneru **blobClient**, můžete vytvořit objekt blob **Block**, který odkazuje na konkrétní objekt blob, který vás zajímá. Pak můžete provádět operace, jako jsou nahrávání, stahování a kopírování.

> [!IMPORTANT]
> Názvy kontejnerů musí obsahovat jen malá písmena. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

V této části vytvoříte instanci klienta úložiště Azure, instanci objektu služby Blob, nový kontejner a nastavíte oprávnění ke kontejneru tak, aby objekty blob byly veřejné. Kontejner má název **quickstartblobs**. 

```PHP
    # Setup a specific instance of an Azure::Storage::Client
    $connectionString = "DefaultEndpointsProtocol=https;AccountName=".getenv('account_name').";AccountKey=".getenv('account_key');
    
    // Create blob client.
    $blobClient = BlobRestProxy::createBlobService($connectionString);
    
    # Create the BlobService that represents the Blob service for the storage account
    $createContainerOptions = new CreateContainerOptions();
    
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
    
    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    $containerName = "blockblobs".generateRandomString();

    try    {
        // Create container.
        $blobClient->createContainer($containerName, $createContainerOptions);
```

### <a name="upload-blobs-to-the-container"></a>Nahrání objektů blob do kontejneru

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Nejčastěji používané jsou objekty blob bloku, které se používají také v tomto rychlém startu.  

Pokud chcete nahrát soubor do objektu blob, získejte úplnou cestu k souboru spojením názvu adresáře a názvu souboru na místním disku. Pak můžete soubor nahrát do zadané cesty pomocí metody **createBlockBlob()**. 

Ukázkový kód vezme místní soubor a nahraje ho do Azure. Soubor se uloží jako **myfile** a název objektu blob se v kódu uloží jako **fileToUpload**. Následující příklad nahraje soubor do kontejneru **quickstartblobs**.

```PHP
    $myfile = fopen("HelloWorld.txt", "w") or die("Unable to open file!");
    fclose($myfile);

    # Upload file as a block blob
    echo "Uploading BlockBlob: ".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    
    $content = fopen($fileToUpload, "r");

    //Upload blob
    $blobClient->createBlockBlob($containerName, $fileToUpload, $content);
```

Pokud chcete upravit část obsahu objektu blob bloku, použijte metodu **createblocklist()**. Objekty blob bloku můžou mít velikost až 4,7 TB a můžou být čímkoli od tabulky aplikace Excel po velké videosoubory. Objekty blob stránky se používají hlavně pro soubory VHD využívané virtuálními počítači IaaS. Doplňovací objekty blob se používají k protokolování, například když chcete zapisovat do souboru a pak přidávat další informace. Doplňovací objekt blob by se měl používat v modelu s jedním zapisujícím procesem. Většina objektů blob ukládaných do úložiště jsou typu blok.

### <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Seznam souborů v kontejneru můžete získat pomocí metody **listBlobs()**. Následující kód načte seznam objektů blob, pak je ve smyčce projde a zobrazí názvy nalezených objektů blob v kontejneru.  

```PHP
    $listBlobsOptions = new ListBlobsOptions();
    $listBlobsOptions->setPrefix("HelloWorld");
    
    echo "These are the blobs present in the container: ";
    
    do{
        $result = $blobClient->listBlobs($containerName, $listBlobsOptions);
        foreach ($result->getBlobs() as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
        
        $listBlobsOptions->setContinuationToken($result->getContinuationToken());
    } while($result->getContinuationToken());
```

### <a name="get-the-content-of-your-blobs"></a>Získání obsahu objektů blob

Obsah objektů blob získáte pomocí metody **getBlob()**. Následující kód zobrazí obsah objektu blob nahraného v předchozí části.

```PHP
    $blob = $blobClient->getBlob($containerName, $fileToUpload);
    fpassthru($blob->getContentStream());
```

### <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už nepotřebujete objekty blob nahrané v rámci tohoto rychlého startu, můžete celý kontejner odstranit pomocí metody **deleteContainer()**. Pokud už nepotřebujete vytvořené soubory, pomocí metody **deleteBlob()** je odstraníte.

```PHP
    // Delete blob.
    echo "Deleting Blob".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    $blobClient->deleteBlob($_GET["containerName"], $fileToUpload);

    // Delete container.
    echo "Deleting Container".PHP_EOL;
    echo $_GET["containerName"].PHP_EOL;
    echo "<br />";
    $blobClient->deleteContainer($_GET["containerName"]);

    //Deleting local file
    echo "Deleting file".PHP_EOL;
    echo "<br />";
    unlink($fileToUpload);   
```

## <a name="resources-for-developing-php-applications-with-blobs"></a>Zdroje informací pro vývoj aplikací PHP s využitím objektů blob

Prohlédněte si tyto další zdroje informací o vývoji v PHP s využitím úložiště objektů blob:

- Prohlédněte, stáhněte a nainstalujte si [zdrojový kód klientské knihovny pro PHP](https://github.com/Azure/azure-storage-php) pro službu Azure Storage na GitHubu.
- Prozkoumejte [ukázky pro úložiště objektů blob](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=php&term=blob) napsané s využitím klientské knihovny pro PHP.

## <a name="next-steps"></a>Další kroky
 
V tomto rychlém startu jste zjistili, jak přenášet soubory mezi místním diskem a úložištěm objektů blob v Azure pomocí PHP. Další informace o práci s PHP najdete v našem Centru pro vývojáře PHP.

> [!div class="nextstepaction"]
> [Centrum pro vývojáře PHP](https://azure.microsoft.com/develop/php/)


Další informace o Průzkumníku služby Storage a objektech blob najdete v tématu [Správa prostředků úložiště objektů blob v Azure pomocí Průzkumníka služby Storage](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
