---
title: Obnovení sdílených složek Azure pomocí Azure CLI
description: Naučte se používat Azure CLI k obnovení zálohovaných sdílených složek Azure v trezoru Recovery Services.
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: a025de7bfb9db037b2008d69be7782feabb482f3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94562317"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Obnovení sdílených složek Azure pomocí Azure CLI

Azure CLI poskytuje prostředí příkazového řádku pro správu prostředků Azure. Je skvělým nástrojem pro vytváření vlastních automatizace pro používání prostředků Azure. Tento článek vysvětluje, jak obnovit celou sdílenou složku nebo konkrétní soubory z bodu obnovení vytvořeného pomocí služby [Azure Backup](./backup-overview.md) pomocí rozhraní příkazového řádku Azure. K provedení těchto kroků můžete také využít [Azure PowerShell](./backup-azure-afs-automation.md) nebo [Azure Portal](backup-afs.md).

Na konci tohoto článku se dozvíte, jak pomocí Azure CLI provádět následující operace:

* Zobrazit body obnovení pro zálohovanou sdílenou složku Azure.
* Obnovte úplnou sdílenou složku Azure.
* Obnovte jednotlivé soubory nebo složky.

>[!NOTE]
> Azure Backup teď podporuje obnovení více souborů nebo složek do původního nebo alternativního umístění pomocí Azure CLI. Další informace najdete v části [obnovení více souborů nebo složek do původního nebo alternativního umístění](#restore-multiple-files-or-folders-to-original-or-alternate-location) v tomto dokumentu.

## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že už máte sdílenou složku Azure, která je zálohovaná pomocí Azure Backup. Pokud ho nemáte, přečtěte si téma [Zálohování sdílených složek Azure pomocí rozhraní](backup-afs-cli.md) příkazového řádku pro konfiguraci zálohování sdílené složky. V tomto článku použijete následující prostředky:

| Sdílená složka | Účet úložiště | Oblast | Podrobnosti |
|---|---|---|---|
| *azurefiles* | *afsaccount* | EastUS | Původní zdroj zálohovaný pomocí Azure Backup |
| *azurefiles1* | *afaccount1* | EastUS | Cílový zdroj, který se používá pro obnovení do alternativního umístění |

Podobnou strukturu pro sdílené složky můžete použít k vyzkoušení různých typů obnovení popsaných v tomto článku.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

 - V tomto kurzu se vyžaduje verze rozhraní příkazového řádku Azure 2.0.18 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Načítají se body obnovení pro sdílenou složku Azure.

K vypsání všech bodů obnovení pro zálohované sdílení souborů použijte rutinu [AZ Backup RecoveryPoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) .

Následující příklad načte seznam bodů obnovení pro sdílenou složku *azurefiles* v účtu úložiště *afsaccount* .

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

Předchozí rutinu můžete také spustit pomocí popisného názvu kontejneru a položky zadáním následujících dvou dalších parametrů:

* **--zálohování-Správa-typ**: *azurestorage*
* **--úlohu-typ**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Sada výsledků dotazu je seznam bodů obnovení s údaji o času a konzistenci pro každý bod obnovení.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

Atribut **Name** ve výstupu odpovídá názvu bodu obnovení, který se dá použít jako hodnota parametru **--RP-Name** v operacích obnovení.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Úplné obnovení sdílení pomocí Azure CLI

Pomocí této možnosti obnovení můžete obnovit úplnou sdílenou složku v původním nebo alternativním umístění.

Zadejte následující parametry pro provedení operací obnovení:

* **--Container-Name**: název účtu úložiště, který hostuje zálohovanou původní sdílenou složku. Pokud chcete načíst název nebo popisný název svého kontejneru, použijte příkaz [AZ Backup Container list](/cli/azure/backup/container#az-backup-container-list) .
* **--Item-Name**: název zálohované původní sdílené složky, kterou chcete použít pro operaci obnovení. Pokud chcete načíst název nebo popisný název zálohované položky, použijte příkaz [AZ Backup Item list](/cli/azure/backup/item#az-backup-item-list) .

### <a name="restore-a-full-share-to-the-original-location"></a>Obnovení úplné sdílené složky do původního umístění

Při obnovení do původního umístění není nutné zadávat parametry související s cíli. Pouze **vyřešit konflikt** je nutné zadat.

V následujícím příkladu se používá rutina [AZ Backup Restore-azurefileshare](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) s režimem obnovení nastaveným na *originallocation* pro obnovení sdílené složky *azurefiles* v původním umístění. Použijete bod obnovení 932883129628959823, který jste získali v [části načtení bodů obnovení pro sdílenou složku Azure](#fetch-recovery-points-for-the-azure-file-share):

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

Atribut **Name** ve výstupu odpovídá názvu úlohy, kterou vytvořila služba zálohování pro vaši operaci obnovení. Chcete-li sledovat stav úlohy, použijte rutinu [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) .

### <a name="restore-a-full-share-to-an-alternate-location"></a>Obnovení úplné sdílené složky do alternativního umístění

Tuto možnost můžete použít k obnovení sdílené složky do alternativního umístění a zachování původní sdílené složky tak, jak je. Zadejte následující parametry pro obnovení do alternativního umístění:

* **--target-Storage-Account**: účet úložiště, na který se obnovil zálohovaný obsah. Cílový účet úložiště musí být ve stejném umístění jako trezor.
* **--target-File-Share**: sdílená složka v rámci cílového účtu úložiště, do kterého se obnovil zálohovaný obsah.
* **--target-Folder**: složka ve sdílené složce, do které se mají obnovit data. Pokud bude zálohovaný obsah obnoven do kořenové složky, zadejte hodnoty cílové složky jako prázdný řetězec.
* **--Resolve-konflikt**: instrukce, pokud dojde ke konfliktu s obnovenými daty. Přijímá **přepis** nebo **Skip**.

Následující příklad používá příkaz [AZ Backup Restore-azurefileshare](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) with Restore Mode *jako alternatelocation* pro obnovení sdílené složky *azurefiles* v účtu úložiště *afsaccount* do sdílené složky *azurefiles1* v účtu úložiště *afaccount1* .

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

Atribut **Name** ve výstupu odpovídá názvu úlohy, kterou vytvořila služba zálohování pro vaši operaci obnovení. Chcete-li sledovat stav úlohy, použijte rutinu [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) .

## <a name="item-level-recovery"></a>Obnovení na úrovni položek

Tuto možnost obnovení můžete použít k obnovení jednotlivých souborů nebo složek v původním nebo alternativním umístění.

Zadejte následující parametry pro provedení operací obnovení:

* **--Container-Name**: název účtu úložiště, který hostuje zálohovanou původní sdílenou složku. Pokud chcete načíst název nebo popisný název svého kontejneru, použijte příkaz [AZ Backup Container list](/cli/azure/backup/container#az-backup-container-list) .
* **--Item-Name**: název zálohované původní sdílené složky, kterou chcete použít pro operaci obnovení. Pokud chcete načíst název nebo popisný název zálohované položky, použijte příkaz [AZ Backup Item list](/cli/azure/backup/item#az-backup-item-list) .

Zadejte následující parametry pro položky, které chcete obnovit:

* **SourceFilePath**: absolutní cesta k souboru, která se má obnovit v rámci sdílené složky jako řetězec. Tato cesta se shoduje s použitím příkazu [AZ Storage File Download](/cli/azure/storage/file#az-storage-file-download) nebo [AZ Storage File show](/cli/azure/storage/file#az-storage-file-show) CLI.
* **SourceFileType**: vyberte, zda je vybrán adresář nebo soubor. Přijímá **adresář** nebo **soubor**.
* **ResolveConflict selhalo**: instrukce, pokud dojde ke konfliktu s obnovenými daty. Přijímá **přepis** nebo **Skip**.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Obnovte jednotlivé soubory nebo složky do původního umístění.

Pomocí rutiny [AZ Backup Restore-azurefiles](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) s režimem obnovení nastavenou na *originallocation* obnovte konkrétní soubory nebo složky do jejich původního umístění.

Následující příklad obnoví soubor *RestoreTest.txt* v původním umístění: sdílená složka *azurefiles* .

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Atribut **Name** ve výstupu odpovídá názvu úlohy, kterou vytvořila služba zálohování pro vaši operaci obnovení. Chcete-li sledovat stav úlohy, použijte rutinu [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) .

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Obnovení jednotlivých souborů nebo složek do alternativního umístění

Chcete-li obnovit konkrétní soubory nebo složky do alternativního umístění, použijte rutinu [AZ Backup Restore-azurefiles](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) s režimem obnovení nastavenou na *alternatelocation* a zadejte následující parametry související s cíli:

* **--target-Storage-Account**: účet úložiště, na který se obnovil zálohovaný obsah. Cílový účet úložiště musí být ve stejném umístění jako trezor.
* **--target-File-Share**: sdílená složka v rámci cílového účtu úložiště, do kterého se obnovil zálohovaný obsah.
* **--target-Folder**: složka ve sdílené složce, do které se mají obnovit data. Pokud bude zálohovaný obsah obnoven do kořenové složky, zadejte hodnotu cílové složky jako prázdný řetězec.

Následující příklad obnoví *RestoreTest.txt* soubor původně přítomný ve sdílené složce *azurefiles* do alternativního umístění: složka *restoredata* ve sdílené složce *azurefiles1* hostovaná v účtu úložiště *afaccount1* .

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Atribut **Name** ve výstupu odpovídá názvu úlohy, kterou vytvořila služba zálohování pro vaši operaci obnovení. Chcete-li sledovat stav úlohy, použijte rutinu [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) .

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Obnovení více souborů nebo složek do původního nebo alternativního umístění

Chcete-li provést obnovení více položek, předejte hodnotu parametru **Source-File-Path** jako cestu **oddělenou mezerou** pro všechny soubory nebo složky, které chcete obnovit.

Následující příklad obnoví *Restore.txt* a *testování AFS Report.docx* soubory v původním umístění.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

Výstup se bude podobat tomuto:

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

Atribut **Name** ve výstupu odpovídá názvu úlohy, kterou vytvořila služba zálohování pro vaši operaci obnovení. Chcete-li sledovat stav úlohy, použijte rutinu [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) .

Pokud chcete obnovit více položek do alternativního umístění, použijte příkaz uvedený výše zadáním parametrů souvisejících s cíli, jak je vysvětleno v části [obnovení jednotlivých souborů nebo složek do alternativního umístění](#restore-individual-files-or-folders-to-an-alternate-location) .

## <a name="next-steps"></a>Další kroky

Naučte se [Spravovat zálohy sdílených složek Azure pomocí Azure CLI](manage-afs-backup-cli.md).
