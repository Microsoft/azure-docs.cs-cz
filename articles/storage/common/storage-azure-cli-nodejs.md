---
title: Použití rozhraní příkazového řádku Azure Classic s Azure Storage | Microsoft Docs
description: Naučte se používat rozhraní příkazového řádku (CLI) Azure Classic s Azure Storage k vytváření a správě účtů úložiště a práci s objekty BLOB a soubory Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/30/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 6554385a879b054153dcb808c3dff4b60c136458
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120856"
---
# <a name="using-the-azure-classic-cli-with-azure-storage"></a>Použití rozhraní příkazového řádku Azure Classic s Azure Storage

## <a name="overview"></a>Přehled

Rozhraní příkazového řádku Azure Classic poskytuje sadu open source příkazů pro různé platformy pro práci s platformou Azure. Poskytuje mnohem stejné funkce jako v [Azure Portal](https://portal.azure.com) a také bohatě dostupné funkce pro přístup k datům.

V této příručce se naučíme používat [Azure Classic CLI](../../cli-install-nodejs.md) k provádění nejrůznějších úloh vývoje a správy s Azure Storage. Před použitím této příručky doporučujeme stáhnout a nainstalovat nebo upgradovat na nejnovější verzi rozhraní příkazového řádku Classic.

V tomto průvodci se předpokládá, že rozumíte základním konceptům Azure Storage. Průvodce poskytuje řadu skriptů k předvedení použití klasického rozhraní příkazového řádku s Azure Storage. Před spuštěním každého skriptu nezapomeňte aktualizovat proměnné skriptu na základě konfigurace.

> [!NOTE]
> Průvodce poskytuje příklady příkazů a skriptů rozhraní příkazového řádku Azure Classic pro účty klasického úložiště. Další informace najdete v tématu [použití Azure CLI pro Mac, Linux a Windows s Azure Resource Management](../../virtual-machines/azure-cli-arm-commands.md#storage-objects) pro příkazy rozhraní příkazového řádku Azure classic pro správce prostředků účty úložiště.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-classic-cli-in-5-minutes"></a>Začínáme s Azure Storage a Azure Classic CLI za 5 minut
Tato příručka používá Ubuntu jako příklady, ale další platformy operačního systému by měly provádět podobně.

**Novinka do Azure:** Získejte Microsoft Azure předplatné a účet Microsoft přidružené k tomuto předplatnému. Informace o možnostech nákupu v Azure najdete v tématu [bezplatné zkušební verze](https://azure.microsoft.com/pricing/free-trial/), [možnosti nákupu](https://azure.microsoft.com/pricing/purchase-options/)a [nabídky členů](https://azure.microsoft.com/pricing/member-offers/) (pro členy MSDN, Microsoft Partner Network a BizSpark a další programy společnosti Microsoft).

Další informace o předplatných Azure najdete v tématu [přiřazení rolí správce v Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) .

**Po vytvoření předplatného Microsoft Azure a účtu:**

1. Stáhněte a nainstalujte rozhraní příkazového řádku Azure Classic podle pokynů uvedených v tématu [instalace rozhraní](../../cli-install-nodejs.md)příkazového řádku Azure Classic.
2. Po nainstalování klasického rozhraní příkazového řádku budete moct pomocí příkazu Azure v rozhraní příkazového řádku (bash, terminál, příkazový řádek) získat přístup k klasickým příkazům CLI. Zadejte příkaz _Azure_ a měl by se zobrazit následující výstup.

    ![Výstup příkazu Azure](./media/storage-azure-cli/azure_command.png)   
3. V rozhraní příkazového řádku zadejte `azure storage` pro výpis všech příkazů Azure Storage a získejte první dojem o funkcích, které klasický CLI nabízí. Můžete zadat název příkazu s parametrem **-h** (například `azure storage share create -h`), chcete-li zobrazit podrobnosti o syntaxi příkazu.
4. Teď vám poskytneme jednoduchý skript, který zobrazí základní příkazy rozhraní příkazového řádku pro přístup k Azure Storage. Skript nejprve požádá o nastavení dvou proměnných pro účet úložiště a klíč. Skript potom vytvoří nový kontejner v tomto novém účtu úložiště a nahraje do tohoto kontejneru existující soubor obrázku (BLOB). Jakmile skript vypíše všechny objekty BLOB v tomto kontejneru, stáhne soubor bitové kopie do cílového adresáře, který existuje v místním počítači.

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

5. V místním počítači otevřete preferovaný textový editor (například systém vim). Do textového editoru zadejte výše uvedený skript.
6. Teď je potřeba aktualizovat proměnné skriptu na základě nastavení konfigurace.

   * **< storage_account_name >** Ve skriptu použijte daný název nebo zadejte nový název účtu úložiště. **Důležité informace:** Název účtu úložiště musí být v Azure jedinečný. Musí se jednat o malá a velká písmena.
   * **< storage_account_key >** Přístupový klíč účtu úložiště
   * **< container_name >** Ve skriptu použijte daný název nebo zadejte nový název kontejneru.
   * **< image_to_upload >** Zadejte cestu k obrázku v místním počítači, například: ~/images/HelloWorld.png.
   * **< destination_folder >** Zadejte cestu k místnímu adresáři, kam se mají ukládat soubory stažené z Azure Storage, například: ~/downloadImages.
7. Po aktualizaci potřebných proměnných ve službě vim stiskněte kombinaci kláves `ESC`, `:``wq!`, čímž se skript uloží.
8. Tento skript můžete spustit pouhým zadáním názvu souboru skriptu v konzole bash. Po spuštění tohoto skriptu byste měli mít místní cílovou složku, která obsahuje stažený soubor obrázku. Následující snímek obrazovky ukazuje příklad výstupu:

Po spuštění skriptu byste měli mít místní cílovou složku, která obsahuje stažený soubor obrázku.

## <a name="manage-storage-accounts-with-the-azure-classic-cli"></a>Správa účtů úložiště pomocí rozhraní příkazového řádku Azure Classic
### <a name="connect-to-your-azure-subscription"></a>Připojení k předplatnému služby Azure
I když většina příkazů úložiště bude fungovat bez předplatného Azure, doporučujeme, abyste se připojili k předplatnému z klasického rozhraní příkazového řádku.

### <a name="create-a-new-storage-account"></a>Vytvoření nového účtu úložiště
Pokud chcete používat službu Azure Storage, budete potřebovat účet úložiště. Po nakonfigurování počítače pro připojení k vašemu předplatnému můžete vytvořit nový účet úložiště Azure.

```azurecli
azure storage account create <account_name>
```

Název účtu úložiště musí být dlouhý 3 až 24 znaků a obsahovat jenom číslice a malá písmena.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Nastavení výchozího účtu služby Azure Storage v proměnných prostředí
Ve vašem předplatném můžete mít víc účtů úložiště. Můžete zvolit jednu z nich a nastavit ji v proměnných prostředí pro všechny příkazy úložiště ve stejné relaci. To vám umožní spustit příkazy klasického úložiště CLI bez explicitního zadání účtu úložiště a klíče.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Dalším způsobem, jak nastavit výchozí účet úložiště, je použití připojovacího řetězce. Nejprve získejte připojovací řetězec pomocí příkazu:

```azurecli
azure storage account connectionstring show <account_name>
```

Pak zkopírujte výstupní připojovací řetězec a nastavte ho na proměnnou prostředí:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Vytváření a správa objektů blob
Azure Blob Storage je služba pro ukládání velkých objemů nestrukturovaných dat, jako jsou textová nebo binární data, ke kterým se dá dostat odkudkoli na světě přes HTTP nebo HTTPS. V této části se předpokládá, že už jste obeznámeni s koncepty služby Azure Blob Storage. Podrobné informace najdete v tématu Začínáme [s úložištěm objektů BLOB v Azure pomocí](../blobs/storage-dotnet-how-to-use-blobs.md) [konceptů .NET a služby BLOB Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Vytvoření kontejneru
Každý objekt BLOB ve službě Azure Storage musí být v kontejneru. Privátní kontejner můžete vytvořit pomocí příkazu `azure storage container create`:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> Existují tři úrovně anonymního přístupu pro čtení: **vypnuto**, **BLOB**a **kontejner**. Pokud chcete zabránit anonymnímu přístupu k objektům blob, nastavte parametr oprávnění na **off**. Ve výchozím nastavení je nový kontejner soukromý a dá se k němu přihlížet pouze vlastníkem účtu. Pokud chcete anonymnímu veřejnému přístupu pro čtení propustit prostředky objektů blob, ale ne metadata kontejneru nebo seznamu objektů BLOB v kontejneru, nastavte parametr oprávnění na **BLOB**. Pokud chcete všem prostředkům blobu, metadatům kontejneru a seznamu objektů BLOB v kontejneru poskytnout úplný veřejný přístup pro čtení, nastavte parametr oprávnění na **Container**. Další informace najdete v tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](../blobs/storage-manage-access-to-resources.md).
>
>

### <a name="upload-a-blob-into-a-container"></a>Nahrání objektu blob do kontejneru
Úložiště objektů blob v Azure podporuje objekty blob bloku a objekty blob stránky. Další informace najdete v tématu [Principy objektů blob bloku, doplňovacích objektů BLOB a objektů blob stránky](https://msdn.microsoft.com/library/azure/ee691964.aspx).

Pokud chcete nahrát objekty blob do kontejneru, můžete použít `azure storage blob upload`. Ve výchozím nastavení tento příkaz nahraje místní soubory do objektu blob bloku. Chcete-li zadat typ objektu blob, můžete použít parametr `--blobtype`.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Stažení objektů BLOB z kontejneru
Následující příklad ukazuje, jak stahovat objekty BLOB z kontejneru.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Kopírování objektů BLOB
Můžete asynchronně kopírovat objekty blob v rámci účtů a oblastí nebo napříč nimi.

Následující příklad ukazuje, jak kopírovat objekt blob z jednoho účtu úložiště do druhého. V této ukázce vytvoříme kontejner, ve kterém jsou objekty blob veřejně přístupné.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

Tato ukázka provede asynchronní kopírování. Stav každé operace kopírování můžete monitorovat spuštěním operace `azure storage blob copy show`.

Všimněte si, že zdrojová adresa URL zadaná pro operaci kopírování musí být buď veřejně přístupná, nebo zahrnovat token SAS (sdíleného přístupového podpisu).

### <a name="delete-a-blob"></a>Odstranění objektu blob
Pokud chcete odstranit objekt blob, použijte následující příkaz:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Vytváření a Správa sdílených složek
Služba soubory Azure nabízí sdílené úložiště pro aplikace používající standardní protokol SMB. Microsoft Azure virtuálních počítačů a cloudových služeb i v místních aplikacích může sdílet souborová data prostřednictvím připojených sdílených složek. Pomocí klasického rozhraní příkazového řádku můžete spravovat sdílené složky a data souborů. Další informace o službě soubory Azure najdete v tématu [Seznámení se soubory Azure](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Vytvoření sdílené složky
Sdílená složka Azure je sdílená složka SMB v Azure. Všechny adresáře a soubory musí být vytvořeny ve sdílené složce. Účet může obsahovat neomezený počet sdílených složek a sdílená složka může ukládat neomezený počet souborů až do omezení kapacity účtu úložiště. Následující příklad vytvoří sdílenou složku s názvem **myshare**.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Vytvoření adresáře
Adresář poskytuje volitelnou hierarchickou strukturu sdílené složky Azure. Následující příklad vytvoří adresář s názvem **myDir** ve sdílené složce.

```azurecli
azure storage directory create myshare myDir
```

Všimněte *si, že*cesta k adresáři může zahrnovat několik úrovní, například **a/b**. Je však nutné zajistit, aby existovaly všechny nadřazené adresáře. Například pro cestu **a/b** **musíte nejprve vytvořit adresář a pak** vytvořit adresář **b**.

### <a name="upload-a-local-file-to-directory"></a>Nahrání místního souboru do adresáře
Následující příklad nahraje soubor z **~/TEMP/samplefile.txt** do adresáře **myDir** . Upravte cestu k souboru tak, aby odkazovala na platný soubor v místním počítači:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Všimněte si, že velikost souboru ve sdílené složce může být až 1 TB.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Výpis souborů v kořenu nebo adresáři pro sdílení
Soubory a podadresáře můžete zobrazit v kořenu sdílené složky nebo v adresáři pomocí následujícího příkazu:

```azurecli
azure storage file list myshare myDir
```

Všimněte si, že název adresáře je pro operaci výpisu volitelný. Pokud tento příkaz vynecháte, zobrazí se v něm obsah kořenového adresáře sdílené složky.

### <a name="copy-files"></a>Kopírování souborů
Počínaje verzí 0.9.8 klasického rozhraní příkazového řádku můžete zkopírovat soubor do jiného souboru, soubor do objektu BLOB nebo objekt blob do souboru. Níže jsme ukázali, jak provádět tyto operace kopírování pomocí příkazů rozhraní příkazového řádku. Zkopírování souboru do nového adresáře:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

Postup kopírování objektu blob do adresáře souborů:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>Další kroky

Odkazy na příkazy Azure Classic CLI pro práci s prostředky úložiště najdete tady:

* [Příkazy rozhraní příkazového řádku Azure Classic v režimu Správce prostředků](../../virtual-machines/azure-cli-arm-commands.md#storage-objects)
* [Příkazy rozhraní příkazového řádku Azure Classic v režimu správy služeb Azure](../../cli-install-nodejs.md)

Můžete si také vyzkoušet nejnovější verzi rozhraní příkazového [řádku Azure CLI](../storage-azure-cli.md)pro použití s modelem nasazení Správce prostředků.
