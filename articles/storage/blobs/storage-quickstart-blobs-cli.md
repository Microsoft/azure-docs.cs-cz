---
title: Úvodní příručka – vytvoření objektu blob pomocí azure cli
titleSuffix: Azure Storage
description: V tomto rychlém startu se dozvíte, jak používat Azure CLI nahrát objekt blob do Služby Azure Storage, stáhnout objekt blob a seznam objektů BLOB v kontejneru.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: tamram
ms.openlocfilehash: 2e1b1ac2ea315759b18dc882b98837bca0a84d46
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061438"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Úvodní příručka: Vytváření, stahování a seznam objektů BLOB pomocí rozhraní příkazového příkazového příkazu Azure

Azure CLI je prostředí příkazového řádku Azure pro správu prostředků Azure. Můžete ho používat ve svém prohlížeči prostřednictvím služby Azure Cloud Shell. Můžete ho také nainstalovat v systému macOS, Linux nebo Windows a spouštět z příkazového řádku. V tomto rychlém startu zjistíte, jak pomocí Azure CLI nahrávat data do úložiště objektů blob v Azure a stahovat data z úložiště objektů blob v Azure.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>Instalace azure cli místně

Pokud se rozhodnete nainstalovat a používat Azure CLI místně, tento rychlý start vyžaduje, abyste spustili Azure CLI verze 2.0.46 nebo novější. Svou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

Pokud používáte Azure CLI místně, musíte se přihlásit a ověřit. Tento krok není nutný, pokud používáte Azure Cloud Shell. Pokud se chcete přihlásit `az login` k nastavení příkazového příkazu K Azure, spusťte a ověřte v okně prohlížeče:

```azurecli
az login
```

Další informace o ověřování pomocí azure cli najdete [v tématu Přihlášení pomocí azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="authorize-access-to-blob-storage"></a>Autorizace přístupu k úložišti objektů Blob

Přístup k úložišti objektů Blob můžete autorizovat z azure cli buď pomocí přihlašovacích údajů Azure AD, nebo pomocí přístupového klíče účtu úložiště. Doporučujeme používat přihlašovací údaje Azure AD. Tento článek ukazuje, jak autorizovat operace úložiště objektů Blob pomocí Azure AD.

Příkazy Azure CLI pro datové operace `--auth-mode` proti úložišti objektů Blob podporují parametr, který umožňuje určit, jak autorizovat danou operaci. Nastavte `--auth-mode` parametr `login` pro autorizaci s přihlašovacími údaji Azure AD. Další informace najdete [v tématu Autorizace přístupu k datům objektů blob nebo fronty pomocí azure CLI](../common/authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Parametr podporují pouze operace `--auth-mode` s daty úložiště objektů blob. Operace správy, jako je například vytvoření skupiny prostředků nebo účtu úložiště, automaticky používají přihlašovací údaje Azure AD pro autorizaci.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Účet úložiště pro obecné účely vytvoříte příkazem [az storage account create](/cli/azure/storage/account). Účet úložiště pro obecné účely můžete použít pro všechny čtyři služby: objekty blob, soubory, tabulky a fronty.

Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="create-a-container"></a>Vytvoření kontejneru

Objekty blob se vždy nahrávají do kontejneru. Skupiny objektů BLOB můžete uspořádat do kontejnerů podobně jako soubory v počítači do složek.

K vytvoření kontejneru pro ukládání objektů blob použijte příkaz [az storage container create](/cli/azure/storage/container). Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

## <a name="upload-a-blob"></a>Nahrání objektu blob

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Příklady v tomto rychlém startu ukazují, jak pracovat s objekty BLOB bloku.

Nejprve vytvořte soubor, který chcete odeslat do objektu blob bloku. Pokud používáte Azure Cloud Shell, vytvořte soubor pomocí následujícího příkazu:

```bash
vi helloworld
```

Po otevření souboru stiskněte **tlačítko insert**. Typ *Hello svět*, pak stiskněte **klávesu Esc**. Dále zadejte *:x*a stiskněte **klávesu Enter**.

V tomto příkladu nahrajete objekt blob do kontejneru, který jste vytvořili v předchozím kroku, a to pomocí příkazu [az storage blob upload](/cli/azure/storage/blob). Není nutné určit cestu k souboru, protože soubor byl vytvořen v kořenovém adresáři. Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

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

Pomocí příkazu [az storage blob list](/cli/azure/storage/blob) zobrazte seznam objektů blob v kontejneru. Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Stažení objektu blob

Pomocí příkazu [az storage blob download](/cli/azure/storage/blob) stáhněte objekt blob, který jste nahráli dříve. Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>Přenos dat pomocí AzCopy

Nástroj příkazového řádku AzCopy nabízí vysoce výkonný a skriptovatelný přenos dat pro Azure Storage. AzCopy můžete použít k přenosu dat do a z úložiště objektů Blob a soubory Azure. Další informace o AzCopy v10, nejnovější verzi AzCopy, najdete v tématu [Začínáme s AzCopy](../common/storage-use-azcopy-v10.md). Informace o používání AzCopy v10 s úložištěm objektů Blob najdete v tématu [Přenos dat pomocí úložiště AzCopy a Blob](../common/storage-use-azcopy-blobs.md).

Následující příklad používá AzCopy k nahrání místního souboru do objektu blob. Nezapomeňte nahradit hodnoty vzorku vlastními hodnotami:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odstranit prostředky, které jste vytvořili jako součást tohoto rychlého startu, včetně účtu úložiště, odstraňte skupinu prostředků pomocí příkazu [delete skupiny az.](/cli/azure/group) Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak přenášet soubory mezi místním systémem souborů a kontejnerem v úložišti objektů Blob Azure. Další informace o práci s objekty blob ve službě Azure Storage najdete v kurzu práce s úložištěm objektů blob v Azure.

> [!div class="nextstepaction"]
> [Postup: Operace s úložištěm objektů blob pomocí Azure CLI](storage-how-to-use-blobs-cli.md)
