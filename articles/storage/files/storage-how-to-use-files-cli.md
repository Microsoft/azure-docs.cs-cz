---
title: Správa sdílených složek Azure pomocí Azure CLI
description: Zjistěte, jak pomocí Azure CLI spravovat službu Soubory Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: ebf8605a0f4686a69f89adf0c36d072f12d3c750
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525252"
---
# <a name="manage-azure-file-shares-using-azure-cli"></a>Správa sdílených složek Azure pomocí Azure CLI
Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Sdílené složky Azure je možné připojit v systémech Windows, Linux a macOS. Tento článek vás provede základy práce se sdílenými složkami Azure pomocí Azure CLI. Naučte se: 

> [!div class="checklist"]
> * Vytvoření skupiny prostředků a účtu úložiště
> * Vytvoření sdílené složky Azure 
> * Vytvoření adresáře
> * Nahrání souboru 
> * Stažení souboru
> * Vytvoření a použití snímku sdílené složky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat Azure CLI místně, musíte používat Azure CLI ve verzi 2.0.4 nebo novější, abyste se mohli řídit postupem v tomto článku. Verzi Azure CLI zjistíte spuštěním příkazu **az --version**. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 

Ve výchozím nastavení vrací příkazy Azure CLI formát JavaScript Object Notation (JSON). Formát JSON je standardní způsob pro odesílání a přijímání zpráv z rozhraní REST API. Pro usnadnění práce s odpověďmi ve formátu JSON některé z příkladů v tomto článku používají v příkazech Azure CLI parametr *dotaz*. Tento parametr k parsování formátu JSON používá [dotazovací jazyk JMESPath](http://jmespath.org/). Další informace o způsobu použití výsledků příkazů Azure CLI prostřednictvím dotazovacího jazyka JMESPath najdete v [kurzu JMESPath](http://jmespath.org/tutorial.html).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pokud ještě skupinu prostředků Azure nemáte, můžete si ji vytvořit pomocí příkazu [az group create](/cli/azure/group#create). 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *USA – východ*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště
Účet úložiště je sdílený fond úložiště, ve kterém můžete nasazovat sdílené složky Azure nebo jiné prostředky úložiště, jako jsou objekty blob nebo fronty. Účet úložiště může obsahovat neomezený počet sdílených složek. Sdílená složka může obsahovat neomezený počet souborů až do výše maximální kapacity účtu úložiště.

Následující příklad vytvoří pomocí příkazu [az storage account create](/cli/azure/storage/account#create) účet úložiště s názvem *mystorageaccount\<náhodné číslo\>* a pak vloží název tohoto účtu úložiště do proměnné `$STORAGEACCT`. Názvy účtů úložiště musí být jedinečné. Použitím `$RANDOM` připojíte k názvu účtu úložiště nějaké číslo, díky kterému bude název jedinečný. 

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>Získání klíče účtu úložiště
Klíče účtu úložiště řídí přístup k prostředkům v účtu úložiště. Vytváří se automaticky při vytvoření účtu úložiště. Klíče vašeho účtu úložiště můžete získat pomocí příkazu [az storage account keys list](/cli/azure/storage/account/keys#list): 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
Nyní můžete vytvořit svou první sdílenou složku Azure. Sdílené složky vytvoříte pomocí příkazu [az storage share create](/cli/azure/storage/share#create). Tento příklad vytvoří sdílenou složku Azure s názvem *myshare*: 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

> [!IMPORTANT]  
> Názvy sdílených složek mohou obsahovat pouze malá písmena, číslice a spojovníky (nesmí ale spojovníkem začínat). Kompletní informace o zadávání názvů sdílených složek a souborů najdete v tématu věnovaném [pojmenovávání sdílených složek, souborů a metadat a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="work-with-the-contents-of-an-azure-file-share"></a>Práce s obsahem sdílené složky Azure
Teď, když jste vytvořili sdílenou složku Azure, můžete sdílenou složku připojit pomocí protokolu SMB v systému [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) nebo [macOS](storage-how-to-use-files-mac.md). Alternativně můžete se sdílenou složkou Azure pracovat pomocí Azure CLI. Výhodou použití Azure CLI místo připojování sdílené složky pomocí protokolu SMB je, že všechny požadavky vytvořené pomocí Azure CLI se vytváří použitím souborového rozhraní REST API. Souborové rozhraní REST API můžete použít k vytvoření, úpravě a odstranění souborů a adresářů ve své sdílené složce z těchto umístění:

- Služby Azure Cloud Shell s prostředím Bash (která aktuálně nedokáže připojit sdílené složky přes protokol SMB).
- Klientů, kteří nedokáží připojit sdílené složky SMB, jako jsou místní klienti bez odblokovaného portu 445.
- Scénářů bez serveru jako případě služby [Azure Functions](../../azure-functions/functions-overview.md).

### <a name="create-a-directory"></a>Vytvoření adresáře
Pomocí příkazu [`az storage directory create`](/cli/azure/storage/directory#az_storage_directory_create) vytvořte nový adresář *myDirectory* v kořenovém adresáři sdílené složky Azure:

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>Nahrání souboru
Abychom mohli ukázat, jak soubor pomocí příkazu [`az storage file upload`](/cli/azure/storage/file#az_storage_file_upload) nahrát, musíte nejprve vytvořit soubor, který potom nahrajete do pomocné jednotky služby Cloud Shell. V následujícím příkladu soubor vytvoříte a potom ho nahrajete:

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Pokud používáte Azure CLI místně, nahraďte `~/clouddrive` za cestu, která existuje na vašem počítači.

Po nahrání souboru můžete pomocí příkazu [`az storage file list`](/cli/azure/storage/file#az_storage_file_list) zkontrolovat nahrání souboru do sdílené složky Azure:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Stažení souboru
Pomocí příkazu [`az storage file download`](/cli/azure/storage/file#az_storage_file_download) můžete stáhnout kopii souboru, který jste nahráli do pomocné jednotky služby Cloud Shell:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>Kopírování souborů
Jednou z běžných úloh je kopírování souborů z jedné sdílené složky do jiné nebo do a z kontejneru úložiště objektů blob v Azure. Abychom mohli tuto funkci předvést, vytvořte novou sdílenou složku. Pomocí příkazu [az storage file copy](/cli/azure/storage/file/copy) zkopírujte do této nové sdílené složky nahraný soubor: 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Pokud nyní vypíšete soubory v nové sdílené složce, měl by se zkopírovaný soubor na tomto seznamu objevit:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

Přestože je příkaz `az storage file copy start` praktický pro přesouvání souborů mezi sdílenými složkami Azure a kontejnery úložiště objektů blob v Azure, pro větší přesuny doporučujeme nástroj příkazového řádku AzCopy. (Větší z hlediska počtu nebo velikosti přesouvaných souborů.) Další informace o [AzCopy pro Linux](../common/storage-use-azcopy-linux.md) a [AzCopy pro Windows](../common/storage-use-azcopy.md). Nástroj příkazového řádku AzCopy musí být nainstalovaný místně. Nástroj příkazového řádku AzCopy není ve službě Cloud Shell k dispozici. 

## <a name="create-and-modify-share-snapshots"></a>Vytváření a úpravy snímků sdílených složek
Další užitečnou úlohou, kterou se sdílenými složkami Azure můžete provádět, je vytváření snímků sdílených složek. Snímek uchovává kopii sdílené složky Azure k určitému bodu v čase. Snímky sdílených složek jsou podobné některým technologiím operačního systému, které už možná znáte:
- Snímky [Správce logických svazků (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) v případě systémů Linux.
- Snímky [systému souborů Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) v případě macOS
- [Služba Stínová kopie svazku (VSS)](https://docs.microsoft.com/en-us/windows/desktop/VSS/volume-shadow-copy-service-portal) v případě systémů souborů Windows, jako jsou NTFS a ReFS

Snímek sdílené složky můžete vytvořit pomocí příkazu [`az storage share snapshot`](/cli/azure/storage/share#az_storage_share_snapshot):

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Procházení obsahu snímku sdílené složky
Obsah snímku sdílené složky můžete procházet předáním časového razítka zachyceného snímku sdílené složky v proměnné `$SNAPSHOT` do příkazu `az storage file list`:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>Výpis snímků sdílené složky
Seznam snímků, které jste pořídili pro svou sdílenou složku, můžete zobrazit pomocí následujícího příkazu:

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>Obnovení ze snímku sdílené složky
Soubor můžete obnovit pomocí příkazu `az storage file copy start`, který jste použili dříve. Nejprve odstraňte nahraný soubor SampleUpload.txt, abyste ho mohli obnovit ze snímku:

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"

# Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Odstranění snímku sdílené složky
Snímek sdílené složky můžete odstranit pomocí příkazu [`az storage share delete`](/cli/azure/storage/share#az_storage_share_delete). Použijte proměnnou, která obsahuje odkaz `$SNAPSHOT` na parametr `--snapshot`:

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Jakmile budete hotoví, můžete k odebrání skupiny prostředků a všech souvisejících prostředků použít příkaz [`az group delete`](/cli/azure/group#delete): 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

Další možností je odebrat prostředky jednotlivě.
- Odebrání sdílených složek Azure, které jste vytvořili pro účely tohoto článku:

    ```azurecli-interactive
    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare" \
        --delete-snapshots include

    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare2" \
        --delete-snapshots include
    ```

- Odebrání samotného účtu úložiště (tím se implicitně odeberou sdílené složky Azure, které jste vytvořili, a všechny ostatní případné prostředky úložiště, jako je kontejner úložiště objektů blob v Azure):

    ```azurecli-interactive
    az storage account delete \
        --resource-group "myResourceGroup" \
        --name $STORAGEACCT \
        --yes
    ```

## <a name="next-steps"></a>Další kroky
- [Správa sdílených složek pomocí webu Azure Portal](storage-how-to-use-files-portal.md)
- [Správa sdílených složek pomocí Azure PowerShellu](storage-how-to-use-files-powershell.md)
- [Správa sdílených složek pomocí Průzkumníka služby Storage](storage-how-to-use-files-storage-explorer.md)
- [Plánování nasazení služby Soubory Azure](storage-files-planning.md)