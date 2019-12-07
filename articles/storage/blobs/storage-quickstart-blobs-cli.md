---
title: Rychlý Start – vytvoření objektu BLOB pomocí Azure CLI
titleSuffix: Azure Storage
description: V tomto rychlém startu se dozvíte, jak pomocí rozhraní příkazového řádku Azure CLI nahrát objekt blob do Azure Storage, stáhnout objekt BLOB a vypsat seznam objektů BLOB v kontejneru.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c913cb978796abeed5766ffa030aaeb6142320ec
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892919"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Rychlý Start: vytvoření, stažení a výpis objektů BLOB pomocí Azure CLI

Azure CLI je prostředí příkazového řádku Azure pro správu prostředků Azure. Můžete ho používat ve svém prohlížeči prostřednictvím služby Azure Cloud Shell. Můžete ho také nainstalovat v systému macOS, Linux nebo Windows a spouštět z příkazového řádku. V tomto rychlém startu zjistíte, jak pomocí Azure CLI nahrávat data do úložiště objektů blob v Azure a stahovat data z úložiště objektů blob v Azure.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Svou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Vytvoření kontejneru

Objekty blob se vždy nahrávají do kontejneru. Skupiny objektů blob můžete organizovat podobně, jako organizujete soubory do složek na svém počítači.

K vytvoření kontejneru pro ukládání objektů blob použijte příkaz [az storage container create](/cli/azure/storage/container).

```azurecli-interactive
az storage container create --name sample-container
```

## <a name="upload-a-blob"></a>Nahrání objektu blob

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Příklady v tomto rychlém startu ukazují, jak pracovat s objekty blob bloku.

Nejdřív vytvořte soubor, který se nahraje do objektu blob bloku. Pokud používáte Azure Cloud Shell, vytvořte soubor pomocí následujícího příkazu:

```bash
vi helloworld
```

Po otevření souboru stiskněte **Vložit**. Zadejte *Hello World*a pak stiskněte klávesu **ESC**. Dále zadejte *: x*a potom stiskněte klávesu **ENTER**.

V tomto příkladu nahrajete objekt blob do kontejneru, který jste vytvořili v předchozím kroku, a to pomocí příkazu [az storage blob upload](/cli/azure/storage/blob). Není nutné zadávat cestu k souboru, protože soubor byl vytvořen v kořenovém adresáři:

```azurecli-interactive
az storage blob upload \
    --container-name sample-container \
    --name helloworld \
    --file helloworld
```

Tato operace vytvoří objekt blob, pokud ještě neexistuje, a přepíše ho, pokud už existoval. Než budete pokračovat, můžete nahrát libovolné množství souborů.

Pokud chcete nahrát více souborů najednou, můžete použít příkaz [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Pomocí příkazu [az storage blob list](/cli/azure/storage/blob) zobrazte seznam objektů blob v kontejneru.

```azurecli-interactive
az storage blob list \
    --container-name sample-container \
    --output table
```

## <a name="download-a-blob"></a>Stažení objektu blob

Pomocí příkazu [az storage blob download](/cli/azure/storage/blob) stáhněte objekt blob, který jste nahráli dříve.

```azurecli-interactive
az storage blob download \
    --container-name sample-container \
    --name helloworld \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Přenos dat pomocí AzCopy

Nástroj [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) představuje další možnost vysoce výkonného a skriptovatelného přenosu dat pro službu Azure Storage. Pomocí AzCopy můžete přenášet data do a ze služeb Blob, File a Table Storage.

Následující příklad používá AzCopy k nahrání souboru s názvem *MyFile. txt* do kontejneru *Sample-Container* . Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://<account-name>.blob.core.windows.net/sample-container \
    --dest-key <account-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už v rámci skupiny prostředků nepotřebujete žádné prostředky, včetně účtu úložiště, který jste vytvořili v tomto rychlém startu, odstraňte skupinu prostředků pomocí příkazu [AZ Group Delete](/cli/azure/group) . Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```azurecli-interactive
az group delete --name <resource-group-name>
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak přenášet soubory mezi místním systémem souborů a kontejnerem v úložišti objektů BLOB v Azure. Další informace o práci s objekty blob ve službě Azure Storage najdete v kurzu práce s úložištěm objektů blob v Azure.

> [!div class="nextstepaction"]
> [Postup: Operace s úložištěm objektů blob pomocí Azure CLI](storage-how-to-use-blobs-cli.md)
