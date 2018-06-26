---
title: Použití Azure CLI 1.0 s Azure Storage | Microsoft Docs
description: Naučte se používat rozhraní příkazového řádku Azure (Azure CLI) 1.0, s Azure Storage pro vytváření a správu účtů úložiště a pracovat s Azure BLOB a soubory. Rozhraní příkazového řádku Azure je nástroj pro různé platformy
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: b502232a-e8f6-4d6c-befd-3476592e0e35
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: seguler
ms.openlocfilehash: 1e2c2dc45f2cb91ca225bf183ddde5fa2d1123c4
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936985"
---
# <a name="using-the-azure-cli-10-with-azure-storage"></a>Použití Azure CLI 1.0 s Azure Storage

## <a name="overview"></a>Přehled

Rozhraní příkazového řádku Azure poskytuje sadu softwaru open source, příkazy a platformy pro práci s platformou Azure. Poskytuje hodně podobné funkce v nalezen [portál Azure](https://portal.azure.com) také bohaté data přístup k funkcím.

V této příručce, jsme budete prozkoumat použití [rozhraní příkazového řádku Azure (Azure CLI)](../../cli-install-nodejs.md) k provádění různých úloh vývoj a správu s Azure Storage. Doporučujeme stáhnout a nainstalovat nebo upgradovat na nejnovější rozhraní příkazového řádku Azure před použitím tohoto průvodce.

Tato příručka předpokládá, že chápete základní koncepty Azure Storage. Průvodce poskytuje řadu skripty, které ukazují použití Azure CLI s Azure Storage. Nezapomeňte aktualizovat proměnné skriptu na základě vaší konfigurace před spuštěním každý skript.

> [!NOTE]
> Průvodce poskytuje příklady příkazu a skript příkazového řádku Azure CLI pro klasické účty úložiště. V tématu [pomocí rozhraní příkazového řádku Azure CLI pro Mac, Linux a Windows se správou prostředků Azure](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) pro příkazy příkazového řádku Azure CLI pro účty úložiště Resource Manager.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-cli-in-5-minutes"></a>Začínáme s Azure Storage a rozhraní příkazového řádku Azure během 5 minut
Tato příručka používá Ubuntu příklady, ale jiné platformy operačního systému proveďte podobně.

**Nové do Azure:** získat předplatné Microsoft Azure a účet Microsoft přidružené k tomuto předplatnému. Informace o možnostech nákupu Azure najdete v tématu [bezplatné zkušební verze](https://azure.microsoft.com/pricing/free-trial/), [zakoupit možnosti](https://azure.microsoft.com/pricing/purchase-options/), a [člen nabízí](https://azure.microsoft.com/pricing/member-offers/) (pro členy MSDN, Microsoft Partner Network a BizSpark, a Další programy společnosti Microsoft).

V tématu [přiřazení rolí správce v Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) Další informace o předplatných Azure.

**Po vytvoření odběru služby Microsoft Azure a účet:**

1. Stáhnout a nainstalovat rozhraní příkazového řádku Azure, postupujte podle pokynů uvedených v [nainstalovat Azure CLI](../../cli-install-nodejs.md).
2. Po instalaci rozhraní příkazového řádku Azure, bude možné používat příkaz azure z vaší rozhraní příkazového řádku (Bash, terminálu, příkazového řádku) pro přístup k příkazy rozhraní příkazového řádku Azure. Typ _azure_ příkaz a jste měli vidět následující výstup.

    ![Výstup příkazu Azure](./media/storage-azure-cli/azure_command.png)   
3. V rozhraní příkazového řádku zadejte `azure storage` seznam se všechny příkazy úložiště azure a získat první dojem funkcí rozhraní příkazového řádku Azure poskytuje. Můžete zadat název příkazu s **-h** parametr (například `azure storage share create -h`) můžete zobrazit podrobnosti o syntaxi příkazu.
4. Nyní sdělíme vám jednoduchý skript, který ukazuje základní rozhraní příkazového řádku Azure pro přístup k úložišti Azure. Skript nejprve vás vyzve k nastavení dvou proměnných pro váš účet úložiště a klíč. Skript se pak vytvořit nový kontejner v rámci tohoto nového účtu úložiště a nahrajte existující soubor bitové kopie (binární rozsáhlý objekt) do tohoto kontejneru. Po skript obsahuje seznam všech objektů BLOB v kontejneru, stáhne soubor bitové kopie do cílového adresáře, která již existuje v místním počítači.

    ```azurecli
    #!/bin/bash
    # A simple Azure storage example

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export image_to_upload=<image_to_upload>
    export destination_folder=<destination_folder>

    echo "Creating the container..."
    azure storage container create $container_name

    echo "Uploading the image..."
    azure storage blob upload $image_to_upload $container_name $blob_name

    echo "Listing the blobs..."
    azure storage blob list $container_name

    echo "Downloading the image..."
    azure storage blob download $container_name $blob_name $destination_folder

    echo "Done"
    ```

5. V místním počítači otevřete upřednostňované textový editor (systémem vim např.). Skript výše zadejte do textového editoru.
6. Teď je potřeba aktualizovat proměnné skriptu na základě svého nastavení konfigurace.

   * **< Storage_account_name >** použít daným názvem ve skriptu nebo zadejte nový název pro účet úložiště. **Důležité:** název účtu úložiště musí být jedinečný v Azure. Musí být malými písmeny, příliš!
   * **< Storage_account_key >** přístupový klíč účtu úložiště.
   * **< Container_name >** použít daným názvem ve skriptu nebo zadejte nový název pro váš kontejner.
   * **< Image_to_upload >** zadejte cestu k obrázku v místním počítači, jako například: "~ / images/HelloWorld.png".
   * **< Destination_folder >** zadejte cestu k místnímu adresáři ukládat soubory stahované z Azure Storage, například: "~/downloadImages".
7. Po aktualizaci nezbytné proměnné v vim, stisknutím kombinace kláves `ESC`, `:`, `wq!` Uložte skript.
8. Pokud chcete spustit tento skript, jednoduše zadejte název souboru skriptu v konzole bash. Po spuštění tohoto skriptu, měli byste mít místní cílovou složku, která obsahuje soubor stažený bitové kopie. Následující snímek obrazovky ukazuje příklad výstupu:

Po spuštění skriptu, měli byste mít místní cílovou složku, která obsahuje soubor stažený bitové kopie.

## <a name="manage-storage-accounts-with-the-azure-cli"></a>Správa účtů úložiště pomocí Azure CLI
### <a name="connect-to-your-azure-subscription"></a>Připojení k předplatnému služby Azure
Zatímco většina příkazů, úložiště bude fungovat bez předplatného Azure, doporučujeme vám umožní připojit se k vašemu předplatnému z příkazového řádku Azure. Pokud chcete nakonfigurovat rozhraní příkazového řádku Azure pro práci s vaším předplatným, postupujte podle kroků v [připojení k předplatnému Azure z rozhraní příkazového řádku Azure](/cli/azure/authenticate-azure-cli).

### <a name="create-a-new-storage-account"></a>Vytvoření nového účtu úložiště
Pokud chcete používat úložiště Azure, potřebujete účet úložiště. Po nakonfigurování počítače pro připojení k vašemu předplatnému, můžete vytvořit nový účet úložiště Azure.

```azurecli
azure storage account create <account_name>
```

Název účtu úložiště musí být v rozmezí 3 až 24 znaků a používat jenom číslice a malá písmena.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Nastavit výchozí účet úložiště Azure v proměnné prostředí
Můžete mít více účtů úložiště v rámci vašeho předplatného. Můžete zvolit jeden z nich a nastavte ji v proměnných prostředí pro všechny příkazy úložiště ve stejné relaci. To umožňuje spouštět příkazy příkazového řádku Azure CLI úložiště bez zadání účtu úložiště a klíč explicitně.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Jiný způsob, jak nastavit výchozí účet úložiště používá připojovací řetězec. Za prvé získáte připojovací řetězec pomocí příkazu:

```azurecli
azure storage account connectionstring show <account_name>
```

Pak zkopírujte připojovací řetězec výstup a nastavte ji do proměnné prostředí:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Vytvářet a spravovat objekty BLOB
Azure Blob storage je služba pro ukládání velkého objemu nestrukturovaných dat, jako je například textu nebo binárních dat, která jsou přístupná odkudkoli na světě prostřednictvím protokolu HTTP nebo HTTPS. V této části se předpokládá, že jste již obeznámeni s koncepty úložiště objektů Blob v Azure. Podrobné informace najdete v tématu [Začínáme s Azure Blob storage pomocí rozhraní .NET](../blobs/storage-dotnet-how-to-use-blobs.md) a [koncepty služby objektů Blob](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Vytvoření kontejneru
Každý objekt blob v úložišti Azure musí být v kontejneru. Můžete vytvořit privátní kontejneru pomocí `azure storage container create` příkaz:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> Existují tři úrovně anonymní přístup pro čtení: **vypnout**, **Blob**, a **kontejneru**. Chcete-li zabránit anonymní přístup k objektům BLOB, nastavte parametr oprávnění na **vypnout**. Ve výchozím nastavení je nový kontejner je privátní a můžete přistupovat pouze pomocí vlastníka účtu. Chcete-li povolit anonymní veřejný přístup pro čtení k prostředkům blob, ale ne pro metadata kontejneru nebo do seznamu objektů BLOB v kontejneru, nastavte parametr oprávnění na **Blob**. Povolit úplné veřejný přístup pro čtení do objektu blob prostředků, metadata kontejneru a seznamu objektů BLOB v kontejneru, nastavte parametr oprávnění na **kontejneru**. Další informace najdete v tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](../blobs/storage-manage-access-to-resources.md).
>
>

### <a name="upload-a-blob-into-a-container"></a>Nahrání objektu blob do kontejneru
Úložiště objektů blob v Azure podporuje objekty blob bloku a objekty blob stránky. Další informace najdete v tématu [Principy objekty BLOB bloku a doplňovacích objektů BLOB, objekty BLOB stránky](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Pokud chcete nahrát do kontejneru objektů BLOB v, můžete použít `azure storage blob upload`. Ve výchozím nastavení odešle tento příkaz místních souborů do objekt blob bloku. Chcete-li zadat typ pro tento objekt blob, můžete použít `--blobtype` parametr.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Stáhnout objekty BLOB z kontejneru
Následující příklad ukazuje, jak stáhnout objekty BLOB kontejneru.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Kopírování objektů BLOB
Můžete asynchronně kopírovat objekty blob v rámci účtů a oblastí nebo napříč nimi.

Následující příklad ukazuje, jak kopírovat objekt blob z jednoho účtu úložiště do druhého. V této ukázce jsme vytvořit kontejner, kde jsou veřejně, objekty BLOB anonymně přístupné.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

Tato ukázka provede asynchronní kopírování. Můžete sledovat stav každé operace kopírování spuštěním `azure storage blob copy show` operaci.

Všimněte si, že zadaná adresa URL zdroje operace kopírování musí být veřejně přístupná nebo může zahrnovat token SAS (sdílený přístupový podpis).

### <a name="delete-a-blob"></a>Odstranění objektu blob
Pokud chcete odstranit objekt blob, použijte následující příkaz:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Vytvořit a spravovat sdílené složky
Soubory Azure nabízí sdílené úložiště pro aplikace, které používají standardní protokol SMB. Virtuální počítače Microsoft Azure a cloudové služby i místní aplikace můžou sdílet souborová data přes sdílené složky. Můžete spravovat sdílené složky a data souborů prostřednictvím rozhraní příkazového řádku Azure. Další informace o Azure souborů najdete v tématu [Úvod do Azure Files](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Vytvoření sdílené složky
Sdílenou složku Azure je sdílená složka SMB v Azure. Všechny adresáře a soubory musí být vytvořeny ve sdílené složce. Účet může obsahovat neomezený počet sdílených složek a sdílená složka můžete obsahovat neomezený počet souborů až limity kapacity účtu úložiště. Následující příklad vytvoří sdílenou složku s názvem **název_sdílené_položky**.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Vytvoření adresáře
Adresář poskytuje volitelné hierarchická struktura pro sdílenou složku Azure. Následující příklad vytvoří adresář s názvem **adresář** do sdílené složky.

```azurecli
azure storage directory create myshare myDir
```

Všimněte si, že cesta k adresáři může obsahovat několik úrovní *například*, **/ b**. Nicméně je nutné zajistit, že existují všechny nadřazeného adresáře. Například pro cestu **/ b**, je nutné vytvořit adresář nejdřív poté vytvořte adresář **b**.

### <a name="upload-a-local-file-to-directory"></a>Uložte místní soubor do adresáře
V následujícím příkladu se uloží soubor z **~/temp/samplefile.txt** k **adresář** adresáře. Upravte cestu k souboru tak, aby odkazovala na platný soubor na místním počítači:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Všimněte si, že soubor ve sdílené složce může být velikost až 1 TB.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Seznam souborů v kořenové sdílenou složku nebo adresář
Je možné uvést soubory a podadresáře v kořenové sdílenou složku nebo adresář pomocí následujícího příkazu:

```azurecli
azure storage file list myshare myDir
```

Všimněte si, že je název adresáře pro operace výpisu volitelné. Pokud tento parametr vynechán, příkaz vypíše obsah kořenový adresář sdílené složky.

### <a name="copy-files"></a>Kopírování souborů
Od verze 0.9.8 rozhraní příkazového řádku Azure, můžete zkopírovat soubor do jiného souboru, soubor do objektu blob, nebo objekt blob do souboru. Dole ukážeme, jak provádět operace kopírovaní pomocí rozhraní příkazového řádku. Kopírování souboru do nového adresáře:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

Zkopírovat objekt blob do adresáře souboru:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>Další kroky

Reference k příkazům 1.0 rozhraní příkazového řádku Azure můžete najít pro práci s prostředky úložiště tady:

* [Azure CLI příkazy v režimu Resource Manager](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects)
* [Azure CLI příkazy v režimu Azure Service Management](../../cli-install-nodejs.md)

Může také jako pokusit [Azure CLI 2.0](../storage-azure-cli.md), naše rozhraní příkazového řádku generace napsané v Pythonu, pro použití s modelem nasazení Resource Manager.
