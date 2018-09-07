---
title: Batch AI ukládání úlohy vstupní a výstupní s Azure Storage | Dokumentace Microsoftu
description: Jak používat Azure Storage s využitím služby Batch AI pro rychlé a snadné cloudové úložiště vstupní a výstupní soubory
services: batch-ai
documentationcenter: ''
author: kevwang1
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 6a9897e94bf8f58ca4000f68ff30bfc6eece6249
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057070"
---
# <a name="store-batch-ai-job-input-and-output-with-azure-storage"></a>Store vstup úlohy služby Batch AI a výstup s Azure Storage

Tato příručka popisuje, jak používat Azure Storage k ukládání vstupních a výstupních souborů při spuštění úlohy. Azure Storage je jedním z několika možností úložiště podporované službou Batch AI. Batch AI se integruje s Azure Storage tak, že připojí systémů služby Azure Storage do služby Batch AI úlohy nebo clusteru systému souborů, umožňuje bezproblémový přístup k souborům uloženým v cloudu. 

## <a name="introduction-to-azure-storage"></a>Seznámení se službou Azure Storage

Azure Storage je řešení cloudového úložiště Microsoftu. Batch AI podporuje připojení kontejnery objektů Blob v Azure a sdílených složek Azure pro úlohy Batch AI nebo clustery, která umožňují přístup z úlohy, jako by byly v nativní systému souborů k souborům. Připojí kontejnery objektů Blob v Azure pomocí služby batch AI [blobfuse](https://github.com/Azure/azure-storage-fuse)a Azure sdílené složky přes protokol SMB. Další informace o Azure Storage najdete v tématu [Úvod do služby Azure Storage](../storage/common/storage-introduction.md).

## <a name="store-datasets-and-input-scripts-in-azure-storage"></a>Store datové sady a vstupní skripty ve službě Azure Storage

Při výběru úložiště Azure pro vaše prostředí služby Batch AI, doporučujeme ukládat vstupních souborů (třeba datových sad) v kontejneru objektů Blob, který má vyšší propustnost, a ukládání výstupu školení ve sdílené složce, která podporuje datové proudy (povoluje čtení protokolů výstup úlohy je spuštěn současně). 

Než budete moct použít Azure Storage, je potřeba [vytvoření účtu služby Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account). Služba batch AI podporuje připojení svazků z obou pro obecné účely v1 (GPv1) a pro obecné účely v2 (GPv2) a Azure Storage účty. Účet služby Azure Storage může obsahovat více kontejnerů objektů Blob nebo soubor instance sdílené složky. Při výběru účtu úložiště zvažte vaše požadavky na náklady a výkon [možnost](../storage/common/storage-account-options.md). 

Vytvořte kontejner objektů Blob a nahrajte vaše datová sada na kontejner objektů Blob v Azure, vyberte jednu z následujících metod:
- [Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md) pro nahrávání pomocí grafického uživatelského rozhraní založeného na webu. Pokud chcete nahrát malý počet souborů, webu Azure portal poskytuje nejjednodušší operaci.
- [Rozhraní příkazového řádku Azure Storage](../storage/blobs/storage-quickstart-blobs-cli.md) pro nahrávání pomocí příkazového řádku (nahrávání adresář podporuje). K nahrání adresářích souborů, použijte `az storage blob upload-batch`.
- [Další postupy](../storage/common/storage-moving-data.md), včetně použití sady SDK služby application.

Podobně k vytvoření sdílené složky Azure, vyberte jednu z následujících metod:
- [Azure Portal](../storage/files/storage-how-to-use-files-portal.md)
- [Úložiště Azure CLI](../storage/files/storage-how-to-use-files-cli.md)
- [Další postupy](../storage/common/storage-moving-data.md)

### <a name="auto-storage-with-batch-ai"></a>Automatického úložiště pomocí služby Batch AI

Alternativně můžete vytvořit účet služby Azure Storage s sdílené složky Azure a kontejner objektů Blob (a automaticky připojit tyto svazky na cluster Batch AI) s použitím `--use-auto-storage` parametr s `az batchai cluster create`. Další informace najdete v tématu [tady](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#auto-storage-account).

## <a name="mount-azure-storage"></a>Připojení Azure Storage 

### <a name="mount-volumes-to-a-job"></a>Připojené svazky do úlohy

Připojení svazku služby Azure Storage umožňuje získávat přístup k systému souborů vytvořené pro každou úlohu. Proto úloha může číst a zapisovat soubory bez problémů do cloudového úložiště, jako kdyby byly místní soubory.

Chcete-li připojení svazku služby Azure Storage do úlohy vytvořené pomocí Azure CLI, použijte `mountVolumes` vlastnost v vaše `job.json` souboru při spuštění `az batchai job create`. Příklad najdete v tématu [Tensorflow GPU distribuované předpisu](https://github.com/Azure/BatchAI/blob/master/recipes/TensorFlow/TensorFlow-GPU-Distributed/job.json). Schéma pro `mountVolumes` je:
```json
{
    "mountVolumes": {
        "azureFileShares": [{
            "azureFileUrl": "https://<STORAGE_ACCOUNT_NAME>.file.core.windows.net/<FILE_SHARE_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }],
        "azureBlobFileSystems": [{
            "accountName": "<STORAGE_ACCOUNT_NAME>",
            "containerName": "<BLOB_CONTAINER_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }]
    }
}
```

`azureFileShares` a `azureBlobFileSystems` jsou obě pole objektů, které představují svazky, které chcete připojit. Popisy zástupné symboly:

- < RELATIVE_MOUNT_PATH > - svazku se připojí na tuto cestu. Například pokud `relativeMountPath` je `jobs`, bude nacházet na svazku `$AZ_BATCHAI_JOB_MOUNT_ROOT/jobs`)
- < název_účtu_úložiště > – název účtu služby Azure Storage, který obsahuje sdílenou složku nebo kontejneru objektů Blob
- < FILE_SHARE_NAME - > název sdílené složky
- < BLOB_CONTAINER_NAME - > název kontejneru objektů Blob

Chcete-li připojit svazky úložiště Azure spolu se sadami SDK služby Azure Batch AI, nastavte `mount_volumes` (Python) nebo `MountVolumes` (C#, Java) vlastnost `JobCreateParameters`. Při připojování svazků pomocí sady SDK služby Azure Batch AI je nutné zadat přihlašovací údaje účtu úložiště. Zobrazení schémat pro připojení svazků v [Python](https://docs.microsoft.com/python/api/azure-mgmt-batchai/azure.mgmt.batchai.models.MountVolumes?view=azure-python), [jazyka C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.batchai.models.mountvolumes?view=azure-dotnet), a [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.management.batchai._mount_volumes?view=azure-java-stable).

### <a name="mount-volumes-to-a-cluster"></a>Připojit svazky na cluster

Batch AI podporuje také připojení svazků úložiště Azure na clusteru Batch AI. Když svazek se připojuje ke clusteru, všechny úlohy spuštěné na tento cluster mohou používat svazků připojených k tomuto clusteru. Při připojení na úrovni úlohy nabízí největší flexibilitu (povoluje Každá úloha má jiný připojených svazků), připojení úrovně clusteru může být v jednoduché scénáře.

Chcete-li připojení svazku služby Azure Storage ke clusteru pomocí Azure CLI vytvořili, použijte `mountVolumes` vlastnost v vaše `cluster.json` souboru při spuštění `az batchai cluster create`. Schéma pro `mountVolumes` při připojení clusteru je stejný jako při připojování k úloze. 

Podobně můžete použít `mount_volumes` (Python) nebo `MountVolumes` (C#, Java) vlastnost `ClusterCreateParameters` při připojování spolu se sadami SDK služby Azure Batch AI. 

## <a name="access-mounted-filesystem-in-a-job"></a>Přístup k připojené systému souborů v rámci úlohy

### <a name="azbatchaijobmountroot-environment-variable"></a>Proměnná prostředí AZ_BATCHAI_JOB_MOUNT_ROOT $

Uvnitř prostředí pro spuštění úlohy, je přístupný adresáře, který obsahuje připojenou úložných systémů s `$AZ_BATCHAI_JOB_MOUNT_ROOT` proměnné prostředí (Pokud používáte úlohy úroveň připojení). Pokud používáte cluster úrovně připojení, je tato proměnná prostředí `$AZ_BATCHAI_MOUNT_ROOT`. Následující příklady předpokládají, že používáte připojení úrovní úlohy.

Pokud chcete zadat cestu k datům v přípojného, použijte proměnnou prostředí `$AZ_BATCHAI_JOB_MOUNT_ROOT` spolu s připojenou cestu. Například pokud cvičný skript `train.py` byl nahrán do služby Azure File připojené sdílené složky na relativní cesta připojení `scripts`, soubor bude k dispozici na `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py`.

Pokud cvičný skript vyžaduje znalosti cesty, je třeba předat jako argument příkazového řádku. Například, pokud uložená vaše data do složky s názvem `train_data` v kontejneru objektů Blob v Azure připojené v cestě `data`, může předat `--data-dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/data/train_data` jako argument příkazového řádku do skriptu. Proto je třeba upravit skript tak, aby přijímal argumenty příkazového řádku.

### <a name="abbreviate-input-paths"></a>Zkrátit vstupní cesta

Chcete-li zkrátit vstupní cesta jako proměnnou prostředí, použijte `inputDirectories` vlastnost vaše `job.json` souboru (nebo `models.JobCreateParamters.input_directories` Pokud pomocí sady SDK služby Batch AI). Schéma `inputDirectories` je:

```json
{
    "inputDirectories": [{
        "id": "<ID>",
        "path": "<PATH>" 
    }]
}
```

Každá cesta se umístí do proměnné prostředí volá `$AZ_BATCHAI_INPUT_<ID>`. Pomocí této metody můžete zjednodušit cesty pro vstupní soubory nebo adresáře. Například, chcete-li zkrátit cestu ke skriptu školení: Pokud `"id"` je `"SCRIPT"` a `"path"` je `"$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py"`, pak je k dispozici na této cestě `$AZ_BATCHAI_INPUT_SCRIPT` v prostředí pro spuštění úlohy.

Další informace najdete v tématu [tady](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#input-directories).

### <a name="abbreviate-output-paths"></a>Zkrátit výstupní cesta

Chcete-li zkrátit výstupní cesta jako proměnnou prostředí, použijte `outputDirectories` vlastnost vaše `job.json` souboru (nebo `models.JobCreateParamters.output_directories` Pokud pomocí sady SDK služby Batch AI). Pomocí této metody můžete zjednodušit cesty pro výstupní soubory. Schéma `outputDirectories` je:

```json
{
    "outputDirectories": [{
        "id": "<ID>",
        "pathPrefix": "<PATH_PREFIX>",
        "pathSuffix": "<PATH_SUFFIX>"
    }]
}
```

Každá cesta se umístí do proměnné prostředí volá `$AZ_BATCHAI_OUTPUT_<ID>`. `pathPrefix` Určuje připojený svazek pro uložení výstupu (například `"$AZ_BATCHAI_JOB_MOUNT_ROOT/output"`). `pathSuffix` Určuje název složky výstupu (například `"logs"`, `"checkpoints"`). Skutečné cesty výstupu je tvořen `pathPrefix`, `jobOutputDirectoryPathSegment` (automaticky generované pro každý projekt) a `pathSuffix`.

Další informace najdete v tématu [tady](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#output-directories).

## <a name="retrieve-job-output-from-azure-storage"></a>Získat výstup úlohy ze služby Azure Storage

### <a name="use-azure-portal"></a>Použití webu Azure Portal

Na webu Azure portal nabízí pohodlný způsob, chcete-li zobrazit výstup úlohy s použitím Průzkumníka souborů grafického uživatelského rozhraní. Nicméně pokud chcete zobrazit výstup z výstupu Stdout a Stderr, nebo z cesty v `outputDirectories`, soubory jsou umístěny v cestě automaticky generované hromadně služby Azure Storage. Další informace najdete níže.

### <a name="access-stdout-and-stderr-output"></a>Výstup přístup Stdout a Stderr

Použití `stdOutErrPathPrefix` vlastnost `job.json` úlohu zjistit, kam umístit výstup Stdout a Stderr a protokolování provádění úlohy. Například, pokud jste připojili sdílené složky na relativní cesta připojení `outputs`, a zadáte `stdOutErrPathPrefix` bude `"$AZ_BATCHAI_JOB_MOUNT_ROOT/outputs"`, bude k dispozici na výstup Stdout a Stderr úlohy `{subscription id}/{resource group}/workspaces/{workspace name}/experiments/{experiment name}/jobs/{job name}/{job uuid}/stdouterr` v, který připojí svazek. Tuto cestu automaticky generované umožňuje zabránit výstup kolizím mezi úlohami se stejným názvem.

Další informace najdete v tématu [tady](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#standard-and-error-output).

### <a name="list-the-output-files"></a>Seznam výstupních souborů

Zobrazte seznam souborů k dispozici výstupu úlohy můžete použít rozhraní příkazového řádku Azure `az batchai job file list` příkazu. Například k zobrazení seznamu souborů v adresáři standardní výstup úlohy, použijte `az batchai job file list -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME>`.

Další informace a příklady najdete v tématu [tady](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories).

### <a name="stream-output-files"></a>Stream výstupní soubory

Rozhraní příkazového řádku Azure můžete použít na soubory datového proudu se `az batchai job file stream` příkazu. Chcete-li například zobrazit:
- STDOUT: `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stdout.txt`
- STDERR: `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stderr.txt`

Další informace a příklady najdete v tématu [tady](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories).

## <a name="next-steps"></a>Další postup
- Chcete-li další informace o příkazech rozhraní příkazového řádku k vytvoření rozhraní služby Azure Storage, podívejte se [dokumentace ke službě Azure Batch AI CLI](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md).
- Další příklady použití služby Batch AI, včetně připojení úložiště a čtení výstupní soubory, najdete v tématu [recepty poznámkového bloku Jupyter pro služby Batch AI](https://github.com/Azure/BatchAI).
- Prozkoumejte další možnosti pro připojení úložiště, včetně [připojení serverem NFS](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs) a [připojení vlastní clusteru systému souborů NFS, cifs nebo GlusterFS](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs)