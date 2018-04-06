---
title: Správa sdílených složek Azure pomocí Azure CLI
description: Zjistěte, jak pomocí Azure CLI spravovat službu Soubory Azure.
services: storage
documentationcenter: na
author: wmgries
manager: jeconnoc
editor: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 066a43b553be18a5a0bc889fff441824df301b98
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-using-the-azure-cli"></a>Správa sdílených složek Azure pomocí Azure CLI
Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Sdílené složky Azure je možné připojit v systémech Windows, Linux a macOS. Tato příručka vás provede základy práce se sdílenými složkami Azure pomocí Azure CLI. Naučte se: 

> [!div class="checklist"]
> * Vytvoření skupiny prostředků a účtu úložiště
> * Vytvoření sdílené složky Azure 
> * Vytvoření adresáře
> * Nahrání souboru 
> * Stažení souboru
> * Vytvoření a použití snímku sdílené složky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat Azure CLI místně, musíte mít Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu **az --version**. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 

Ve výchozím nastavení příkazy Azure CLI vrací JSON (JavaScript Object Notation), což je standardní způsob odesílání a příjmu zpráv z rozhraní REST API. Pro usnadnění práce s odpověďmi ve formátu JSON některé z příkladů v této příručce používají v příkazech Azure CLI parametr dotazu. Tento parametr k parsování formátu JSON používá [dotazovací jazyk JMESPath](http://jmespath.org/). Další informace o práci s výsledky příkazů Azure CLI najdete v [kurzu JMESPath](http://jmespath.org/tutorial.html).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pokud ještě nemáte skupinu prostředků Azure, můžete vytvořit novou pomocí příkazu [az group create](/cli/azure/group#create). 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *USA – východ*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště
Účet úložiště je sdílený fond úložiště, ve kterém můžete nasazovat sdílené složky Azure nebo jiné prostředky úložiště, jako jsou objekty blob nebo fronty. Účet úložiště může obsahovat neomezený počet sdílených složek a sdílená složka můžete obsahovat neomezený počet souborů až do omezení kapacity účtu úložiště.

Tento příklad pomocí příkazu [az storage account create](/cli/azure/storage/account#create) vytvoří účet úložiště `mystorageaccount<random number>` a pak vloží název tohoto účtu úložiště do proměnné `$STORAGEACCT`. Názvy účtů úložiště musí být jedinečné. Pomocí proměnné `$RANDOM` se na konec názvu účtu úložiště přidá číslo a tím se zajistí jeho jedinečnost. 

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>Získání klíče účtu úložiště
Klíče účtu úložiště slouží k řízení přístupu k prostředkům v účtu úložiště. Vytváří se automaticky při vytvoření účtu úložiště. Klíče účtu úložiště můžete získat pomocí příkazu [az storage account keys list](/cli/azure/storage/account/keys#list). 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
Teď můžete vytvořit svou první sdílenou složku Azure. Sdílené složky můžete vytvářet pomocí příkazu [az storage share create](/cli/azure/storage/share#create). Tento příklad vytvoří sdílenou složku Azure `myshare`. 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

> [!Important]  
> Názvy sdílených složek můžou obsahovat pouze malá písmena, číslice a spojovníky a nesmí začínat na spojovník. Kompletní informace o zadávání názvů sdílených složek a souborů najdete v tématu [Pojmenování a odkazování na sdílené složky, soubory a metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Práce s obsahem sdílené složky Azure
Teď, když jste vytvořili sdílenou složku Azure, můžete sdílenou složku připojit pomocí protokolu SMB v systému [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) nebo [macOS](storage-how-to-use-files-mac.md). Alternativně můžete se sdílenou složkou Azure pracovat pomocí Azure CLI. To je výhodnější než připojení sdílené složky přes protokol SMB, protože všechny požadavky provedené přes Azure CLI se provedou pomocí souborového rozhraní REST API, které umožňuje vytvářet, upravovat a odstraňovat soubory a adresáře ve sdílené složce z:

- Služby Cloud Shell s prostředím Bash (která aktuálně nedokáže připojit sdílené složky přes protokol SMB).
- Klientů, kteří nedokáží připojit sdílené složky SMB, jako jsou místní klienti bez odblokovaného portu 445.
- Scénářů bez serveru, jako například v případě služby [Azure Functions](../../azure-functions/functions-overview.md). 

### <a name="create-directory"></a>Vytvoření adresáře
Pomocí příkazu [`az storage directory create`](/cli/azure/storage/directory#az_storage_directory_create) vytvořte nový adresář *myDirectory* v kořenovém adresáři sdílené složky Azure.

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>Nahrání souboru
Abychom ukázali, jak nahrát soubor pomocí příkazu [`az storage file upload`](/cli/azure/storage/file#az_storage_file_upload), musíme nejprve v pomocné jednotce služby Cloud Shell s Azure CLI vytvořit soubor k nahrání. V následujícím příkladu soubor vytvoříme a pak nahrajeme.

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Pokud používáte Azure CLI místně, musíte nahradit `~/clouddrive` za cestu, která existuje na vašem počítači.

Po nahrání souboru můžete pomocí příkazu [`az storage file list`](/cli/azure/storage/file#az_storage_file_list) zkontrolovat nahrání souboru do sdílené složky Azure.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Stažení souboru
Pomocí příkazu [`az storage file download`](/cli/azure/storage/file#az_storage_file_download) můžete stáhnout kopii souboru, který jste nahráli do pomocné jednotky vaší služby Cloud Shell.

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>Kopírování souborů
Jednou z běžných úloh je kopírování souborů z jedné sdílené složky do jiné nebo do a z kontejneru úložiště objektů blob v Azure. Abychom si předvedli tuto funkci, můžete vytvořit novou sdílenou složku a pomocí příkazu [az storage file copy](/cli/azure/storage/file/copy) do této nové sdílené složky zkopírovat soubor, který jste nahráli. 

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

Pokud teď vypíšete soubory v nové sdílené složce, měl by se zobrazit váš zkopírovaný soubor.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

Přestože je příkaz `az storage file copy start` praktický pro ad hoc přesun souborů mezi sdílenými složkami Azure a kontejnery úložiště objektů blob v Azure, pro větší přesuny (z hlediska počtu nebo velikosti přesouvaných souborů) doporučujeme použít AzCopy. Další informace o [AzCopy pro Linux](../common/storage-use-azcopy-linux.md) a [AzCopy pro Windows](../common/storage-use-azcopy.md). Nástroj AzCopy musí být nainstalovaný místně – ve službě Cloud Shell není dostupný. 

## <a name="create-and-modify-share-snapshots"></a>Vytváření a úpravy snímků sdílených složek
Další užitečnou úlohou, kterou se sdílenými složkami Azure můžete provádět, je vytváření snímků sdílených složek. Snímek uchovává sdílenou složku Azure k určitému bodu v čase. Snímky sdílených složek jsou podobné technologiím operačního systému, které už možná znáte, jako například:
- Snímky [Správce logických svazků (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) v případě systémů Linux.
- Snímky [systému souborů Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) v případě macOS. 
- [Služba Stínová kopie svazku (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) v případě systémů souborů Windows, jako jsou NTFS a ReFS.

Snímek sdílené složky můžete vytvořit pomocí příkazu [`az storage share snapshot`](/cli/azure/storage/share#az_storage_share_snapshot).

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Procházení obsahu snímku sdílené složky
Obsah snímku sdílené složky můžete procházet předáním časového razítka zachyceného snímku sdílené složky v proměnné `$SNAPSHOT` do příkazu `az storage file list`.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>Výpis snímků sdílené složky
Seznam snímků, které jste pořídili pro svou sdílenou složku, můžete zobrazit pomocí následujícího příkazu.

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>Obnovení ze snímku sdílené složky
Soubor můžete obnovit pomocí příkazu `az storage file copy start`, který jsme použili dříve. Nejprve odstraníme soubor `SampleUpload.txt`, který jsme nahráli, abychom ho mohli obnovit ze snímku.

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"

# Build the source URI for snapshot restore
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
Snímek sdílené složky můžete odstranit pomocí příkazu [`az storage share delete`](/cli/azure/storage/share#az_storage_share_delete) s proměnnou obsahující odkaz `$SNAPSHOT` na parametr `--snapshot`.

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Jakmile budete hotovi, můžete k odebrání skupiny prostředků a všech souvisejících prostředků použít příkaz [`az group delete`](/cli/azure/group#delete). 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

Alternativně můžete prostředky odebrat jeden po druhém:
- Odebrání sdílených složek Azure, které jsme vytvořili pro účely tohoto rychlého startu:

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

- Odebrání samotného účtu úložiště (tím se implicitně odeberou sdílené složky Azure, které jsme vytvořili, i všechny další prostředky úložiště, které jste mohli vytvořit, jako je například kontejner úložiště objektů blob v Azure):

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