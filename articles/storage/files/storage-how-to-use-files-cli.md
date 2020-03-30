---
title: Rychlý start pro správu sdílených složek Azure pomocí Azure CLI
description: V tomto rychlém startu zjistíte, jak pomocí Azure CLI spravovat službu Soubory Azure.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 95d7abca27ec9db46a72140bc8a61b2841c63fcb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77598557"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Rychlý start: Vytváření a správa sdílených složek Azure pomocí Azure CLI
Tato příručka vás provede základy práce se [sdílenými složkami Azure](storage-files-introduction.md) pomocí Azure CLI. Sdílené složky Azure jsou stejné jako ostatní sdílené složky, ale jsou uložené v cloudu a využívají platformu Azure. Sdílené složky Azure podporují standardní průmyslový protokol SMB a umožňují sdílení souborů mezi různými počítači, aplikacemi a instancemi. 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat Azure CLI místně, musíte používat Azure CLI ve verzi 2.0.4 nebo novější, abyste se mohli řídit postupem v tomto článku. Verzi Azure CLI zjistíte spuštěním příkazu **az --version**. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 

Ve výchozím nastavení vrací příkazy Azure CLI formát JavaScript Object Notation (JSON). Formát JSON je standardní způsob pro odesílání a přijímání zpráv z rozhraní REST API. Pro usnadnění práce s odpověďmi ve formátu JSON některé z příkladů v tomto článku používají v příkazech Azure CLI parametr *dotaz*. Tento parametr k parsování formátu JSON používá [dotazovací jazyk JMESPath](http://jmespath.org/). Další informace o způsobu použití výsledků příkazů Azure CLI prostřednictvím dotazovacího jazyka JMESPath najdete v [kurzu JMESPath](http://jmespath.org/tutorial.html).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pokud ještě skupinu prostředků Azure nemáte, můžete si ji vytvořit pomocí příkazu [az group create](/cli/azure/group). 

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *Západní USA 2:*

```azurecli-interactive 
export resourceGroupName="myResourceGroup"
region="westus2"

az group create \
    --name $resourceGroupName \
    --location $region \
    --output none
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště
Účet úložiště je sdílený fond úložiště, ve kterém můžete nasazovat sdílené složky Azure nebo jiné prostředky úložiště, jako jsou objekty blob nebo fronty. Účet úložiště může obsahovat neomezený počet sdílených složek. Sdílená složka může obsahovat neomezený počet souborů až do výše maximální kapacity účtu úložiště.

Následující příklad vytvoří účet úložiště pomocí příkazu [az storage create.](/cli/azure/storage/account) Názvy účtů úložiště musí být jedinečné, proto k názvu pomocí rutiny `$RANDOM` připojte číslo, aby byl jedinečný.

```azurecli-interactive 
export storageAccountName="mystorageacct$RANDOM"

az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --location $region \
    --kind StorageV2 \
    --sku Standard_LRS \
    --enable-large-file-share \
    --output none
```

> [!Note]  
> Sdílené položky větší než 5 TiB (maximálně 100 TiB na akcii) jsou k dispozici pouze v místně redundantních (LRS) a zónových redundantních účtech (ZRS). Chcete-li vytvořit účet úložiště geograficky redundantní (GRS) nebo geograficky `--enable-large-file-share` redundantní (GZRS), odeberte parametr.

### <a name="get-the-storage-account-key"></a>Získání klíče účtu úložiště
Klíče účtu úložiště řídí přístup k prostředkům v účtu úložiště. Vytváří se automaticky při vytvoření účtu úložiště. Klíče vašeho účtu úložiště můžete získat pomocí příkazu [az storage account keys list](/cli/azure/storage/account/keys): 

```azurecli-interactive 
export storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
Nyní můžete vytvořit svou první sdílenou složku Azure. Sdílené složky vytvoříte pomocí příkazu [az storage share create](/cli/azure/storage/share). Tento příklad vytvoří sdílenou složku Azure s názvem *myshare*: 

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

Názvy sdílených složek mohou obsahovat pouze malá písmena, číslice a spojovníky (nesmí ale spojovníkem začínat). Úplné podrobnosti o pojmenování sdílených složek a souborů naleznete v [tématu Pojmenování a odkazování na sdílené složky, adresáře, soubory a metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Použití sdílené složky Azure
Služba Soubory Azure nabízí dva způsoby práce se soubory a složkami ve sdílené složce Azure: standardní průmyslový [protokol SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) a [souborový protokol REST](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Informace o připojení sdílené složky s využitím protokolu SMB najdete v následujících dokumentech v závislosti na vašem operačním systému:
- [Linux](storage-how-to-use-files-linux.md)
- [Macos](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Použití sdílené složky Azure se souborovým protokolem REST 
Je možné pracovat přímo s protokolem File REST přímo (ruční volání REST HTTP sami), ale nejběžnější způsob, jak používat protokol File REST, je použití rozhraní příkazového příkazu Azure, [modulu Azure PowerShell](storage-how-to-use-files-powershell.md)nebo sady Azure Storage SDK, které poskytují pěkný obal kolem protokolu FILE REST v skriptovacím/programovacím jazyce podle vašeho výběru.  

Předpokládáme, že ve většině případů použití služby Soubory Azure budete chtít se sdílenými složkami Azure pracovat přes protokol SMB, protože vám to umožní používat stávající aplikace a nástroje, které očekáváte, že budete moct použít. Existuje však několik důvodů, proč může být výhodnější místo protokolu SMB použít souborové rozhraní REST API, jako například:

- Procházíte sdílenou složku z prostředí Bash ve službě Azure Cloud Shell (které nedokáže připojit sdílené složky přes protokol SMB).
- Využíváte bezserverové prostředky, jako je služba [Azure Functions](../../azure-functions/functions-overview.md). 
- Vytváříte službu s přidanou hodnotou, která bude pracovat s mnoha sdílenými složkami souborů Azure, jako je například zálohování nebo antivirové kontroly.

Následující příklady ukazují, jak používat azure CLI k manipulaci se správou souboru Azure s protokolem FILE REST. 

### <a name="create-a-directory"></a>Vytvoření adresáře
Pomocí příkazu [`az storage directory create`](/cli/azure/storage/directory) vytvořte nový adresář *myDirectory* v kořenovém adresáři sdílené složky Azure:

```azurecli-interactive
az storage directory create \
   --account-name $storageAccountName \
   --account-key $storageAccountKey \
   --share-name $shareName \
   --name "myDirectory" \
   --output none
```

### <a name="upload-a-file"></a>Nahrání souboru
Chcete-li předvést, jak nahrát soubor pomocí příkazu, [`az storage file upload`](/cli/azure/storage/file) nejprve vytvořte soubor pro nahrání na jednotku pro zápis do prostředí Cloud Shell. V následujícím příkladu soubor vytvoříte a potom ho nahrajete:

```azurecli-interactive
cd ~/clouddrive/
date > SampleUpload.txt

az storage file upload \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --source "SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Pokud používáte Azure CLI místně, nahraďte `~/clouddrive` za cestu, která existuje na vašem počítači.

Po nahrání souboru se [`az storage file list`](/cli/azure/storage/file) pomocí příkazu ujistěte, že byl soubor nahrán do sdílené složky Azure:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Stažení souboru
Pomocí příkazu [`az storage file download`](/cli/azure/storage/file) můžete stáhnout kopii souboru, který jste nahráli na odkládací jednotku cloudového prostředí:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -f SampleDownload.txt

az storage file download \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --dest "SampleDownload.txt" \
    --output none
```

### <a name="copy-files"></a>Kopírování souborů
Běžným úkolem je kopírování souborů z jedné sdílené složky do jiné sdílené složky. Abychom mohli tuto funkci předvést, vytvořte novou sdílenou složku. Pomocí příkazu [az storage file copy](/cli/azure/storage/file/copy) zkopírujte do této nové sdílené složky nahraný soubor: 

```azurecli-interactive
otherShareName="myshare2"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $otherShareName \
    --quota 1024 \
    --output none

az storage directory create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --name "myDirectory2" \
    --output none

az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-share $shareName \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share $otherShareName \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Pokud nyní vypíšete soubory v nové sdílené složce, měl by se zkopírovaný soubor na tomto seznamu objevit:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --path "myDirectory2" \
    --output table
```

Zatímco `az storage file copy start` příkaz je vhodný pro přesuny souborů mezi sdílenými složkami Azure, pro migrace a větší přesuny dat doporučujeme `rsync` v systému macOS a Linux u `robocopy` Windows. `rsync`a `robocopy` použít SMB k provádění přesunů dat namísto rozhraní FILEREST API.

## <a name="create-and-manage-share-snapshots"></a>Vytváření a správa snímků sdílených složek
Další užitečnou úlohou, kterou se sdílenými složkami Azure můžete provádět, je vytváření snímků sdílených složek. Snímek uchovává kopii sdílené složky Azure k určitému bodu v čase. Snímky sdílených složek jsou podobné některým technologiím operačního systému, které už možná znáte:

- [Snímky Správce logických svazků (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pro systémy Linux.
- Snímky [systému souborů Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) v případě macOS.
- [Služba Stínová kopie svazku (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) pro systémy souborů Windows, jako jsou ntfs a refs.
 
Snímek sdílené složky můžete [`az storage share snapshot`](/cli/azure/storage/share) vytvořit pomocí příkazu:

```azurecli-interactive
snapshot=$(az storage share snapshot \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Procházení obsahu snímku sdílené složky
Obsah snímku sdílené složky můžete procházet předáním časového razítka zachyceného snímku sdílené složky v proměnné `$snapshot` do příkazu `az storage file list`:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --snapshot $snapshot \
    --output table
```

### <a name="list-share-snapshots"></a>Výpis snímků sdílené složky
Seznam snímků, které jste pořídili pro svou sdílenou složku, můžete zobrazit pomocí následujícího příkazu:

```azurecli-interactive
az storage share list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --include-snapshot \
    --query "[? name== '$shareName' && snapshot!=null].snapshot" \
    --output tsv
```

### <a name="restore-from-a-share-snapshot"></a>Obnovení ze snímku sdílené složky
Soubor můžete obnovit pomocí příkazu `az storage file copy start`, který jste použili dříve. Nejprve odstraňte nahraný soubor SampleUpload.txt, abyste ho mohli obnovit ze snímku:

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --output none

# Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI$shareName"/myDirectory/SampleUpload.txt?sharesnapshot="$snapshot

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-uri $URI \
    --destination-share $shareName \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Odstranění snímku sdílené složky
Snímek sdílené složky můžete [`az storage share delete`](/cli/azure/storage/share) odstranit pomocí příkazu. Použijte proměnnou, která obsahuje odkaz `$SNAPSHOT` na parametr `--snapshot`:

```azurecli-interactive
az storage share delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --snapshot $snapshot \
    --output none
```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Po dokončení můžete pomocí příkazu [`az group delete`](/cli/azure/group) odebrat skupinu prostředků a všechny související prostředky: 

```azurecli-interactive 
az group delete --name $resourceGroupName
```

Další možností je odebrat prostředky jednotlivě.
- Odebrání sdílených složek Azure, které jste vytvořili pro účely tohoto článku:

    ```azurecli-interactive
    az storage share list \
            --account-name $storageAccountName \
            --account-key $storageAccountKey \
            --query "[].name" \
            --output tsv | \
        xargs -L1 bash -ec '\
            az storage share delete \
                --account-name "$storageAccountName" \
                --account-key "$storageAccountKey" \
                --name $0 \
                --delete-snapshots include \
                --output none'
    ```

- Odebrání samotného účtu úložiště (tím se implicitně odeberou sdílené složky Azure, které jste vytvořili, a všechny ostatní případné prostředky úložiště, jako je kontejner úložiště objektů blob v Azure):

    ```azurecli-interactive
    az storage account delete \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --yes
    ```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Co je služba Soubory Azure?](storage-files-introduction.md)