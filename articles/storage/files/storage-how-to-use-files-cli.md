---
title: Rychlý start pro správu sdílených složek Azure pomocí Azure CLI
description: V tomto rychlém startu zjistíte, jak pomocí Azure CLI spravovat službu Soubory Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: aab248ac7c9adf7d996406ec35e0317594ce0b68
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945014"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Rychlý start: Vytváření a správa sdílených složek Azure pomocí Azure CLI
Tato příručka vás provede základy práce se [sdílenými složkami Azure](storage-files-introduction.md) pomocí Azure CLI. Sdílené složky Azure jsou stejné jako ostatní sdílené složky, ale jsou uložené v cloudu a využívají platformu Azure. Sdílené složky Azure podporují standardní průmyslový protokol SMB a umožňují sdílení souborů mezi různými počítači, aplikacemi a instancemi. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat Azure CLI místně, musíte používat Azure CLI ve verzi 2.0.4 nebo novější, abyste se mohli řídit postupem v tomto článku. Verzi Azure CLI zjistíte spuštěním příkazu **az --version**. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 

Ve výchozím nastavení vrací příkazy Azure CLI formát JavaScript Object Notation (JSON). Formát JSON je standardní způsob pro odesílání a přijímání zpráv z rozhraní REST API. Pro usnadnění práce s odpověďmi ve formátu JSON některé z příkladů v tomto článku používají v příkazech Azure CLI parametr *dotaz*. Tento parametr k parsování formátu JSON používá [dotazovací jazyk JMESPath](http://jmespath.org/). Další informace o způsobu použití výsledků příkazů Azure CLI prostřednictvím dotazovacího jazyka JMESPath najdete v [kurzu JMESPath](http://jmespath.org/tutorial.html).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Pokud používáte Azure CLI místně, otevřete příkazový řádek a přihlaste se k Azure, pokud jste to ještě neudělali.

```bash 
az login
```

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

Názvy sdílených složek mohou obsahovat pouze malá písmena, číslice a spojovníky (nesmí ale spojovníkem začínat). Kompletní informace o zadávání názvů sdílených složek a souborů najdete v tématu věnovaném [pojmenovávání sdílených složek, souborů a metadat a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Použití sdílené složky Azure
Služba Soubory Azure nabízí dva způsoby práce se soubory a složkami ve sdílené složce Azure: standardní průmyslový [protokol SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) a [souborový protokol REST](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Informace o připojení sdílené složky s využitím protokolu SMB najdete v následujících dokumentech v závislosti na vašem operačním systému:
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Použití sdílené složky Azure se souborovým protokolem REST 
Se souborovým protokolem REST můžete pracovat přímo (tedy zpracovávat volání REST HTTP ručně), ale nejběžnějším způsobem, jak používat souborový protokol REST, je použít Azure CLI, [modul Azure RM PowerShellu](storage-how-to-use-files-powershell.md) nebo sadu SDK služby Azure Storage. Všechny tyto metody poskytují praktickou obálku souborového protokolu REST v libovolném skriptovacím nebo programovacím jazyce.  

Předpokládáme, že ve většině případů použití služby Soubory Azure budete chtít se sdílenými složkami Azure pracovat přes protokol SMB, protože vám to umožní používat stávající aplikace a nástroje, které očekáváte, že budete moct použít. Existuje však několik důvodů, proč může být výhodnější místo protokolu SMB použít souborové rozhraní REST API, jako například:

- Procházíte sdílenou složku z prostředí Bash ve službě Azure Cloud Shell (které nedokáže připojit sdílené složky přes protokol SMB).
- Potřebujete spustit skript nebo aplikaci z klienta, který nedokáže připojit sdílené složky SMB, jako jsou například místní klienti s blokovaným portem 445.
- Využíváte bezserverové prostředky, jako je služba [Azure Functions](../../azure-functions/functions-overview.md). 

Následující příklady ukazují, jak pomocí modulu AzureRM PowerShellu manipulovat se sdílenou složkou Azure s využitím souborového protokolu REST. 

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

> [!div class="nextstepaction"]
> [Co je služba Soubory Azure?](storage-files-introduction.md)