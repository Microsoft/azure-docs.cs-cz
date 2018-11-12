---
title: Použití Azure classic CLI s Azure Storage | Dokumentace Microsoftu
description: Další informace o použití Azure classic rozhraní příkazového řádku (CLI) pomocí služby Azure Storage k vytváření a Správa účtů úložiště a pracovat s objekty BLOB Azure a soubory.
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 01/30/2017
ms.author: seguler
ms.component: common
ms.openlocfilehash: 28c9e342aec3eb3ba61b46f4f80c7d097b0653b6
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258710"
---
# <a name="using-the-azure-classic-cli-with-azure-storage"></a>Použití Azure classic CLI s Azure Storage

## <a name="overview"></a>Přehled

Azure classic CLI poskytuje sadu jako software open source příkazů pro různé platformy pro práci s platformou Azure. Poskytuje většinu stejné funkce, které jsou součástí [webu Azure portal](https://portal.azure.com) jako stejně velké množství dat a přístup k funkcím.

V této příručce se podíváme, jak používat [klasické rozhraní příkazového řádku Azure](../../cli-install-nodejs.md) provádět různé úlohy vývoje a správy pomocí služby Azure Storage. Doporučujeme stáhnout a instalaci nebo upgradu nejnovější rozhraní příkazového řádku classic před použitím tohoto průvodce.

Tento průvodce to předpokládá, že chápete základní koncepty služby Azure Storage. Průvodce poskytuje řadu skripty, které ukazují použití klasického rozhraní příkazového řádku pomocí služby Azure Storage. Nezapomeňte aktualizovat proměnné skript na základě vaší konfigurace před spuštěním každého skriptu.

> [!NOTE]
> Průvodce poskytuje Azure classic rozhraní příkazového řádku příkaz a skript příklady pro účty úložiště classic. Zobrazit [pomocí Azure CLI pro Mac, Linux a Windows s Azure Resource Manageru](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) pro Azure classic příkazy rozhraní příkazového řádku pro účty úložiště Resource Manageru.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-classic-cli-in-5-minutes"></a>Začínáme s Azure Storage a Azure classic CLI během 5 minut
Tato příručka používá Ubuntu příklady, ale jiné platformy operačního systému by měl provádět podobně.

**Zatím Azure:** získat předplatné Microsoft Azure a účet Microsoft spojený s tímto předplatným. Informace o možnostech nákupu Azure najdete v tématu [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/), [možnostmi nákupu](https://azure.microsoft.com/pricing/purchase-options/), a [nabídky pro členy](https://azure.microsoft.com/pricing/member-offers/) (pro členy programu MSDN, Microsoft Partner Network a BizSpark, a Další programy společnosti Microsoft).

Zobrazit [přiřazení rolí správce v Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) Další informace o předplatných Azure.

**Po vytvoření předplatného Microsoft Azure a účet:**

1. Stáhněte a nainstalujte Azure CLI classic podle pokynů uvedených v [instalace Azure classic CLI](../../cli-install-nodejs.md).
2. Po instalaci rozhraní příkazového řádku classic budete moct používat příkaz azure z rozhraní příkazového řádku (Bash, terminálu, příkazový řádek) pro přístup k classic příkazy rozhraní příkazového řádku. Typ _azure_ příkazu by se měla zobrazit následující výstup.

    ![Výstup příkazu Azure](./media/storage-azure-cli/azure_command.png)   
3. V rozhraní příkazového řádku zadejte `azure storage` seznam všech příkazů služby azure storage a získat první dojem funkcí rozhraní příkazového řádku classic poskytuje. Můžete zadat název příkazu s **-h** parametrů (například `azure storage share create -h`) zobrazíte podrobnosti o syntaxi příkazu.
4. Nyní dáme vám jednoduchý skript, který zobrazuje základní příkazy rozhraní příkazového řádku classic pro přístup k úložišti Azure. Skript nejprve vás vyzve k nastavení dvou proměnných pro účet úložiště a klíč. Skript potom vytvoříte nový kontejner v rámci tohoto nového účtu úložiště a nahrajte existující soubor bitové kopie (blob) do tohoto kontejneru. Jakmile skript vypíše všechny objekty BLOB v tomto kontejneru, stáhne soubor bitové kopie do cílového adresáře, který existuje v místním počítači.

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

5. V místním počítači otevřete upřednostňovaném textovém editoru (například vim). Skript výše zadejte do textového editoru.
6. Teď budete muset aktualizovat skript proměnné podle nastavení konfigurace.

   * **< Název_účtu_úložiště >** použít ve skriptu se zadaným názvem nebo zadejte název nového účtu úložiště. **Důležité:** název účtu úložiště musí být jedinečný v Azure. Musí být malými písmeny příliš!
   * **< Klíč_účtu_úložiště >** přístupový klíč účtu úložiště.
   * **< Container_name >** použít ve skriptu se zadaným názvem nebo zadejte nový název pro váš kontejner.
   * **< Image_to_upload >** zadejte cestu k obrázku v místním počítači, jako například: "~ / images/HelloWorld.png".
   * **< Destination_folder >** zadejte cestu k místní adresář pro uložení souborů, které stáhne ze služby Azure Storage, jako například: "~/downloadImages".
7. Po aktualizaci nezbytné proměnné vim, stisknutím kombinace kláves `ESC`, `:`, `wq!` skript uložte.
8. Ke spuštění tohoto skriptu, jednoduše zadejte název souboru skriptu v konzole prostředí bash. Po spuštění tohoto skriptu, měli byste mít místní cílovou složku, která obsahuje soubor stažený obraz. Následující snímek obrazovky ukazuje příklad výstupu:

Po spuštění skriptu, měli byste mít místní cílovou složku, která obsahuje soubor stažený obraz.

## <a name="manage-storage-accounts-with-the-azure-classic-cli"></a>Správa účtů úložiště pomocí Azure classic CLI
### <a name="connect-to-your-azure-subscription"></a>Připojení k předplatnému služby Azure
Zatímco většina příkazů úložiště bude fungovat bez předplatného Azure, doporučujeme vám umožní připojit se ke svému předplatnému z klasického příkazového řádku.

### <a name="create-a-new-storage-account"></a>Vytvoření nového účtu úložiště
Pokud chcete používat Azure storage, budete potřebovat účet úložiště. Po konfiguraci počítače pro připojení k vašemu předplatnému, můžete vytvořit nový účet úložiště Azure.

```azurecli
azure storage account create <account_name>
```

Název účtu úložiště musí být dlouhý 3 až 24 znaků a použít pouze číslice a malá písmena.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Nastavit výchozí účet úložiště Azure v proměnných prostředí
V rámci vašeho předplatného může mít více účtů úložiště. Můžete zvolit jeden z nich a nastavit v proměnné prostředí pro všechny příkazy úložiště ve stejné relaci. To umožňuje spustit klasického úložiště příkazy rozhraní příkazového řádku bez zadání účtu úložiště a klíč explicitně.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Dalším způsobem, jak nastavit výchozí účet úložiště používá připojovací řetězec. Nejprve získejte připojovací řetězec pomocí příkazu:

```azurecli
azure storage account connectionstring show <account_name>
```

Potom zkopírujte připojovací řetězec výstup a nastavte ho na proměnné prostředí:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Vytvoření a Správa objektů BLOB
Azure Blob storage je služba pro ukládání velkých objemů nestrukturovaných dat, jako jsou textová nebo binární data, ke kterým můžete přistupovat odkudkoli na světě prostřednictvím protokolu HTTP nebo HTTPS. V této části se předpokládá, že jste už obeznámení s koncepty úložiště objektů Blob v Azure. Podrobné informace najdete v tématu [Začínáme s Azure Blob storage pomocí .NET](../blobs/storage-dotnet-how-to-use-blobs.md) a [koncepty služby Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Vytvoření kontejneru
Každý objekt blob ve službě Azure storage, musí být v kontejneru. Můžete vytvořit privátní kontejneru pomocí `azure storage container create` příkaz:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> Existují tři úrovně anonymní přístup pro čtení: **vypnout**, **Blob**, a **kontejneru**. Pokud chcete zabránit anonymní přístup k objektům BLOB, nastavte parametr oprávnění na **vypnout**. Ve výchozím nastavení je nový kontejner je privátní a je přístupný pouze vlastník účtu. Chcete-li povolit anonymní veřejné oprávnění ke čtení k prostředkům blob, ale metadata kontejneru nebo seznam objektů BLOB v kontejneru, nastavte parametr oprávnění na **Blob**. Chcete-li povolit úplné veřejné oprávnění ke čtení do objektu blob prostředky, metadata kontejneru a seznamu objektů BLOB v kontejneru, nastavte parametr oprávnění **kontejneru**. Další informace najdete v tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](../blobs/storage-manage-access-to-resources.md).
>
>

### <a name="upload-a-blob-into-a-container"></a>Nahrání objektu blob do kontejneru
Úložiště objektů blob v Azure podporuje objekty blob bloku a objekty blob stránky. Další informace najdete v tématu [vysvětlení objektů BLOB bloku, doplňovací objekty BLOB a objekty BLOB stránky](https://msdn.microsoft.com/library/azure/ee691964.aspx).

K nahrání objektů BLOB v kontejneru, můžete použít `azure storage blob upload`. Ve výchozím nastavení tento příkaz nahraje místní soubory do objektu blob bloku. Chcete-li určit typ pro tento objekt blob, můžete použít `--blobtype` parametru.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Stažení objektů blob z kontejneru
Následující příklad ukazuje, jak stahovat objekty BLOB z kontejneru.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Kopírování objektů BLOB
Můžete asynchronně kopírovat objekty blob v rámci účtů a oblastí nebo napříč nimi.

Následující příklad ukazuje, jak kopírovat objekt blob z jednoho účtu úložiště do druhého. V tomto příkladu vytvoříme kontejner, ve kterém jsou objekty BLOB veřejně, anonymně přístupné.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

Tato ukázka provádí asynchronní kopírování. Stav jednotlivých operací kopírování můžete monitorovat spuštěním `azure storage blob copy show` operace.

Všimněte si, že zadaná adresa URL zdroje pro operaci kopírování musí být buď veřejně přístupný, nebo zahrnout token SAS (sdílený přístupový podpis).

### <a name="delete-a-blob"></a>Odstranění objektu blob
Chcete-li odstranit objekt blob, použijte následující příkaz:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Vytvoření a správě sdílených složek
Služba soubory Azure nabízí sdílené úložiště pro aplikace používající standardní protokol SMB. Virtuální počítače Microsoft Azure a cloudových služeb, jakož i místní aplikace můžou sdílet souborová data přes sdílené složky. Můžete spravovat sdílené složky a data souborů přes rozhraní příkazového řádku classic. Další informace o službě soubory Azure najdete v tématu [Úvod do služby soubory Azure](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Vytvoření sdílené složky
Sdílené složky Azure je sdílené složky protokolu SMB v Azure. Všechny adresáře a soubory musí být vytvořeny ve sdílené složce. Účet může obsahovat neomezený počet sdílených složek a sdílené složky můžete ukládat neomezený počet souborů až do omezení kapacity účtu úložiště. Následující příklad vytvoří sdílenou **myshare**.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Vytvoření adresáře
Adresář obsahuje volitelné hierarchickou strukturu pro sdílené složky Azure. Následující příklad vytvoří adresář s názvem **slozka** ve sdílené složce.

```azurecli
azure storage directory create myshare myDir
```

Poznámka: Tento adresář může obsahovat několik úrovní *například*, **/ b**. Ale musíte zajistit, aby existovaly všechny nadřazené adresáře. Například pro cestu **/ b**, je nutné vytvořit adresář nejdřív poté vytvořte adresář **b**.

### <a name="upload-a-local-file-to-directory"></a>Nahrání místního souboru do adresáře
Následující příklad nahraje soubor z **~/temp/samplefile.txt** k **slozka** adresáře. Upravte cestu k souboru tak, aby odkazovala na platný soubor na místním počítači:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Všimněte si, že soubor ve sdílené složce může být velikost až 1 TB.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Seznam souborů ve sdílené složce kořenový server WSUS nebo adresář
Můžete vytvořit seznam souborů a podsložek v kořenové sdílené složky nebo do adresáře, pomocí následujícího příkazu:

```azurecli
azure storage file list myshare myDir
```

Všimněte si, že název adresáře je nepovinné pro operace výpisu. Pokud tento parametr vynechán, příkaz vypíše obsah kořenovém adresáři sdílené složky.

### <a name="copy-files"></a>Kopírování souborů
Od verze 0.9.8 rozhraní příkazového řádku classic, můžete zkopírovat soubor do jiného souboru, soubor do objektu blob nebo objekt blob do souboru. Dole ukážeme, jak provádět operace kopírovaní pomocí příkazů rozhraní příkazového řádku. Zkopírujte soubor do nového adresáře:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

Zkopírování objektu blob do souboru adresáře:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>Další kroky

Azure classic informace o příkazech rozhraní příkazového řádku můžete najít pro práci s prostředky úložiště tady:

* [Azure classic příkazy rozhraní příkazového řádku v režimu Resource Manageru](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects)
* [Azure classic příkazy rozhraní příkazového řádku v režimu Azure Service Management](../../cli-install-nodejs.md)

Může také jako vyzkoušet nejnovější verzi [rozhraní příkazového řádku Azure](../storage-azure-cli.md), pro použití s modelem nasazení Resource Manager.
