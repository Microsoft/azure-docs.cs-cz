---
title: Rychlý Start – vytvoření objektu BLOB pomocí Azure CLI
titleSuffix: Azure Storage
description: V tomto rychlém startu se dozvíte, jak pomocí rozhraní příkazového řádku Azure CLI nahrát objekt blob do Azure Storage, stáhnout objekt BLOB a vypsat seznam objektů BLOB v kontejneru.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: tamram
ms.openlocfilehash: b0fe0cf0d477d1360d3789f74f30565e15cfd42e
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206911"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Rychlý Start: vytvoření, stažení a výpis objektů BLOB pomocí Azure CLI

Azure CLI je prostředí příkazového řádku Azure pro správu prostředků Azure. Můžete ho používat ve svém prohlížeči prostřednictvím služby Azure Cloud Shell. Můžete ho také nainstalovat v systému macOS, Linux nebo Windows a spouštět z příkazového řádku. V tomto rychlém startu zjistíte, jak pomocí Azure CLI nahrávat data do úložiště objektů blob v Azure a stahovat data z úložiště objektů blob v Azure.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>Místní instalace Azure CLI

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku Azure CLI místně, musíte mít spuštěnou verzi Azure CLI 2.0.46 nebo novější. Svou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

Pokud používáte Azure CLI místně, musíte se přihlásit a ověřit. Tento krok není nutný, pokud používáte Azure Cloud Shell. Pokud se chcete přihlásit k rozhraní příkazového řádku Azure, spusťte `az login` a ověřte v okně prohlížeče:

```azurecli
az login
```

Další informace o ověřování pomocí rozhraní příkazového řádku Azure najdete v tématu [přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="authorize-access-to-blob-storage"></a>Autorizace přístupu k úložišti objektů BLOB

Přístup k úložišti objektů blob můžete autorizovat z Azure CLI buď pomocí přihlašovacích údajů Azure AD, nebo pomocí přístupového klíče účtu úložiště. Doporučuje se použít přihlašovací údaje Azure AD. Tento článek ukazuje, jak autorizovat operace BLOB Storage pomocí Azure AD.

Příkazy rozhraní příkazového řádku Azure pro datové operace s úložištěm objektů BLOB podporují parametr `--auth-mode`, který umožňuje určit, jak autorizovat danou operaci. Nastavte parametr `--auth-mode`, aby `login` k autorizaci pomocí přihlašovacích údajů Azure AD. Další informace najdete v tématu [autorizace přístupu k datům BLOB nebo Queue pomocí Azure CLI](../common/authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Pouze datové operace služby Blob Storage podporují parametr `--auth-mode`. Operace správy, jako je vytvoření skupiny prostředků nebo účtu úložiště, automaticky používají přihlašovací údaje Azure AD k autorizaci.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Účet úložiště pro obecné účely vytvoříte příkazem [az storage account create](/cli/azure/storage/account). Účet úložiště pro obecné účely můžete použít pro všechny čtyři služby: objekty blob, soubory, tabulky a fronty.

Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="create-a-container"></a>Vytvoření kontejneru

Objekty blob se vždy nahrávají do kontejneru. Skupiny objektů blob můžete uspořádat v kontejnerech podobně jako při organizování souborů v počítači ve složkách.

K vytvoření kontejneru pro ukládání objektů blob použijte příkaz [az storage container create](/cli/azure/storage/container). Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

## <a name="upload-a-blob"></a>Nahrání objektu blob

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Příklady v tomto rychlém startu ukazují, jak pracovat s objekty blob bloku.

Nejdřív vytvořte soubor, který se nahraje do objektu blob bloku. Pokud používáte Azure Cloud Shell, vytvořte soubor pomocí následujícího příkazu:

```bash
vi helloworld
```

Po otevření souboru stiskněte **Vložit**. Zadejte *Hello World*a pak stiskněte klávesu **ESC**. Dále zadejte *: x*a potom stiskněte klávesu **ENTER**.

V tomto příkladu nahrajete objekt blob do kontejneru, který jste vytvořili v předchozím kroku, a to pomocí příkazu [az storage blob upload](/cli/azure/storage/blob). Není nutné zadávat cestu k souboru, protože soubor byl vytvořen v kořenovém adresáři. Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

Tato operace vytvoří objekt blob, pokud ještě neexistuje, a přepíše ho, pokud už existoval. Než budete pokračovat, můžete nahrát libovolné množství souborů.

Pokud chcete nahrát více souborů najednou, můžete použít příkaz [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Pomocí příkazu [az storage blob list](/cli/azure/storage/blob) zobrazte seznam objektů blob v kontejneru. Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Stažení objektu blob

Pomocí příkazu [az storage blob download](/cli/azure/storage/blob) stáhněte objekt blob, který jste nahráli dříve. Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>Přenos dat pomocí AzCopy

Nástroj příkazového řádku AzCopy nabízí vysoce výkonný a skriptový přenos dat pro Azure Storage. AzCopy můžete použít k přenosu dat do a ze služby Blob Storage a souborů Azure. Další informace o AzCopy v10 za účelem, nejnovější verzi AzCopy, najdete v tématu [Začínáme s AzCopy](../common/storage-use-azcopy-v10.md). Další informace o použití AzCopy v10 za účelem s úložištěm objektů BLOB najdete v tématu [přenos dat pomocí AzCopy a BLOB Storage](../common/storage-use-azcopy-blobs.md).

Následující příklad používá AzCopy k nahrání místního souboru do objektu BLOB. Nezapomeňte nahradit vzorové hodnoty vlastními hodnotami:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už v rámci skupiny prostředků nepotřebujete žádné prostředky, včetně účtu úložiště, který jste vytvořili v tomto rychlém startu, odstraňte skupinu prostředků pomocí příkazu [AZ Group Delete](/cli/azure/group) . Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak přenášet soubory mezi místním systémem souborů a kontejnerem v úložišti objektů BLOB v Azure. Další informace o práci s objekty blob ve službě Azure Storage najdete v kurzu práce s úložištěm objektů blob v Azure.

> [!div class="nextstepaction"]
> [Postup: Operace s úložištěm objektů blob pomocí Azure CLI](storage-how-to-use-blobs-cli.md)
