---
title: Obnovení sdílených složek Azure pomocí Azure CLI
description: Naučte se používat Azure CLI k obnovení zálohovaných sdílených složek Azure v trezoru Recovery Services.
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 8d91031ff9ee0d4f32cc74ff7031d39ddab3e2b4
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294406"
---
# <a name="restore-azure-file-shares-with-cli"></a>Obnovení sdílených složek Azure pomocí rozhraní příkazového řádku

Rozhraní příkazového řádku Azure (CLI) poskytuje prostředí příkazového řádku pro správu prostředků Azure. Je skvělým nástrojem pro vytváření vlastních automatizace pro používání prostředků Azure. Tento článek vysvětluje, jak obnovit celou sdílenou složku nebo konkrétní soubory z bodu obnovení vytvořeného pomocí služby [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) pomocí Azure CLI. K provedení těchto kroků můžete také využít [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) nebo [Azure Portal](backup-afs.md).

Na konci tohoto kurzu se dozvíte, jak pomocí Azure CLI provádět následující operace:

* Zobrazit body obnovení pro zálohovanou sdílenou složku Azure
* Obnovení úplné sdílené složky Azure
* Obnovení jednotlivých souborů nebo složek

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete rozhraní příkazového řádku nainstalovat a používat místně, musíte použít Azure CLI verze 2.0.18 nebo novější. Chcete-li zjistit verzi rozhraní příkazového řádku, `run az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že už máte sdílenou složku Azure, která je zálohovaná službou Azure Backup. Pokud ho nemáte, přečtěte si téma [Zálohování sdílených složek Azure pomocí](backup-afs-cli.md) rozhraní příkazového řádku, ve kterém můžete nakonfigurovat zálohování sdílené složky. V tomto článku budeme používat následující zdroje:

| Sdílená složka  | Účet úložiště | Region (Oblast) | Podrobnosti                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *azurefiles*  | *afsaccount*      | USA – východ | Původní zdroj zálohovaný pomocí Azure Backup                 |
| *azurefiles1* | *afaccount1*      | USA – východ | Cílový zdroj, který se používá pro obnovení do alternativního umístění |

Podobnou strukturu pro sdílené složky můžete použít k vyzkoušení různých typů obnovení popsaných v tomto dokumentu.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Načítají se body obnovení pro sdílenou složku Azure.

K vypsání všech bodů obnovení pro zálohované sdílení souborů použijte rutinu [AZ Backup RecoveryPoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) .

Následující příklad načte seznam bodů obnovení pro sdílenou složku *azurefiles* v účtu úložiště *afsaccount* .

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --backup-management-type azurestorage --item-name “AzureFileShare;azurefiles” --workload-type azurefileshare --out table
```

Můžete také provést rutinu uvedenou výše pomocí popisného názvu pro kontejner a položku zadáním následujících dvou dalších parametrů:

* **--zálohování-Správa-typ**: *azurestorage*
* **--úlohu-typ**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Sada výsledků bude seznam bodů obnovení s podrobnostmi o čase a konzistenci pro každý bod obnovení.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

Atribut **Name** ve výstupu odpovídá názvu bodu obnovení, který se dá použít jako hodnota parametru **--RP-Name** v operacích obnovení.

## <a name="full-share-recovery-using-azure-cli"></a>Úplné obnovení sdílení pomocí Azure CLI

Pomocí této možnosti obnovení můžete obnovit úplnou sdílenou složku v původním nebo alternativním umístění.

Zadejte následující parametry pro provedení operací obnovení:

* **--Container-Name** je název účtu úložiště hostujícího zálohovanou původní sdílenou složku. Pokud chcete načíst **název** nebo **popisný název** svého kontejneru, použijte příkaz [AZ Backup Container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name** je název zálohované původní sdílené složky, kterou chcete použít pro operaci obnovení. Pokud chcete načíst **název** nebo **popisný název** zálohované položky, použijte příkaz [AZ Backup Item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

### <a name="restore-full-share-to-the-original-location"></a>Obnovit úplnou sdílenou složku do původního umístění

Při obnovení do původního umístění není nutné zadávat parametry související s cíli. Pouze **vyřešit konflikt** je nutné zadat.

V následujícím příkladu se používá rutina [AZ Backup Restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) s režimem obnovení nastaveným na *originallocation* pro obnovení sdílené složky *azurefiles* v původním umístění pomocí bodu obnovení 932883129628959823, který jsme získali s [načtením bodů obnovení pro sdílenou složku Azure](#fetch-recovery-points-for-the-azure-file-share):

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

Atribut **Name** ve výstupu odpovídá názvu úlohy, kterou vytvořila služba zálohování pro operaci obnovení. Chcete-li sledovat stav úlohy, použijte rutinu [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

### <a name="restore-full-share-to-an-alternate-location"></a>Obnovení úplné sdílené složky do alternativního umístění

Tuto možnost můžete použít k obnovení sdílené složky do alternativního umístění a zachování původní sdílené složky tak, jak je. Zadejte následující parametry pro obnovení do alternativního umístění:

* **--target-Storage-Account**: účet úložiště, na který se obnovil zálohovaný obsah. Cílový účet úložiště musí být ve stejném umístění jako trezor.
* **--target-File-Share**: sdílená složka v rámci cílového účtu úložiště, do kterého se obnovil zálohovaný obsah.
* **--target-Folder**: složka ve sdílené složce, do které se mají obnovit data. Pokud bude zálohovaný obsah obnoven do kořenové složky, zadejte hodnoty cílové složky jako prázdný řetězec.
* **--Resolve-konflikt**: instrukce, pokud dojde ke konfliktu s obnovenými daty. Přijímá **přepis** nebo **Skip**.

Následující příklad používá příkaz [AZ Backup Restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) with Restore Mode, protože *alternatelocation* pro obnovení sdílené složky *azurefiles* v účtu úložiště *afsaccount* na *azurefiles1 "* sdílení souborů v *afaccount1* účtu úložiště.

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

Atribut **Name** ve výstupu odpovídá názvu úlohy, kterou vytvořila služba zálohování pro operaci obnovení. Chcete-li sledovat stav úlohy, použijte rutinu [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="item-level-recovery"></a>Obnovení na úrovni položek

Tuto možnost obnovení můžete použít k obnovení jednotlivých souborů nebo složek v původním nebo alternativním umístění.

Zadejte následující parametry pro provedení operací obnovení:

* **--Container-Name** je název účtu úložiště hostujícího zálohovanou původní sdílenou složku. Pokud chcete načíst **název** nebo **popisný název** svého kontejneru, použijte příkaz [AZ Backup Container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name** je název zálohované původní sdílené složky, kterou chcete použít pro operaci obnovení. Pokud chcete načíst **název** nebo **popisný název** zálohované položky, použijte příkaz [AZ Backup Item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

Zadejte následující parametry pro položky, které chcete obnovit:

* **SourceFilePath**: absolutní cesta k souboru, která se má obnovit v rámci sdílené složky jako řetězec. Tato cesta se shoduje s použitím příkazu [AZ Storage File Download](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) nebo [AZ Storage File show](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) CLI.
* **SourceFileType**: vyberte, zda je vybrán adresář nebo soubor. Přijímá **adresář** nebo **soubor**.
* **ResolveConflict selhalo**: instrukce, pokud dojde ke konfliktu s obnovenými daty. Přijímá **přepis** nebo **Skip**.

### <a name="restore-individual-filesfolders-to-the-original-location"></a>Obnovte jednotlivé soubory nebo složky do původního umístění.

Pomocí rutiny [AZ Backup Restore-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) s režimem obnovení nastavenou na *originallocation* obnovte konkrétní soubory/složky do jejich původního umístění.

Následující příklad obnoví soubor *"RestoreTest. txt"* v původním umístění: sdílená složka *azurefiles* .

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Atribut **Name** ve výstupu odpovídá názvu úlohy, kterou vytvořila služba zálohování pro operaci obnovení. Chcete-li sledovat stav úlohy, použijte rutinu [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

### <a name="restore-individual-filesfolders-to-an-alternate-location"></a>Obnovení jednotlivých souborů nebo složek do alternativního umístění

Chcete-li obnovit konkrétní soubory nebo složky do alternativního umístění, použijte rutinu [AZ Backup Restore-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) s režimem obnovení nastavenou na *alternatelocation* a zadejte následující parametry související s cíli:

* **--target-Storage-Account**: účet úložiště, na který se obnovil zálohovaný obsah. Cílový účet úložiště musí být ve stejném umístění jako trezor.
* **--target-File-Share**: sdílená složka v rámci cílového účtu úložiště, do kterého se obnovil zálohovaný obsah.
* **--target-Folder**: složka ve sdílené složce, do které se mají obnovit data. Pokud bude zálohovaný obsah obnoven do kořenové složky, zadejte hodnotu cílové složky jako prázdný řetězec.

Následující příklad obnoví soubor *RestoreTest. txt* původně přítomný ve sdílené složce *azurefiles* do alternativního umístění: složka *restoredata* ve sdílené složce *azurefiles1* hostovaná v účtu úložiště *afaccount1* .

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Atribut **Name** ve výstupu odpovídá názvu úlohy, kterou vytvořila služba zálohování pro operaci obnovení. Chcete-li sledovat stav úlohy, použijte rutinu [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="next-steps"></a>Další kroky

Naučte se [Spravovat zálohy sdílených složek Azure pomocí Azure CLI](manage-afs-backup-cli.md) .
