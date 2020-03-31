---
title: Obnovení sdílených složek Azure pomocí příkazového příkazového příkazu Azure
description: Zjistěte, jak pomocí příkazového příkazového příkazu Azure obnovit zálohované sdílené složky Azure v trezoru služby Recovery Services
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 63b2be2fe24c1274ed1581b7b849de578c978842
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76931035"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Obnovení sdílených složek Azure pomocí příkazového příkazového příkazu Azure

Azure CLI poskytuje prostředí příkazového řádku pro správu prostředků Azure. Je to skvělý nástroj pro vytváření vlastní automatizace pro použití prostředků Azure. Tento článek vysvětluje, jak obnovit celou sdílenou složku nebo konkrétní soubory z bodu obnovení vytvořeného [službou Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) pomocí rozhraní příkazového řádku Azure. K provedení těchto kroků můžete také využít [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) nebo [Azure Portal](backup-afs.md).

Na konci tohoto článku se dozvíte, jak provádět následující operace s Azure CLI:

* Zobrazte body obnovení pro zálohovanou sdílenou složku Azure.
* Obnovte úplnou sdílenou složku Azure.
* Obnovte jednotlivé soubory nebo složky.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete rozhraní příkazového řádku nainstalovat a používat místně, musíte použít Azure CLI verze 2.0.18 nebo novější. Verzi rozhraní příkazového řádku zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že už máte sdílenou složku Azure, která je zálohována službou Azure Backup. Pokud ho nemáte, přečtěte si část [Zálohování sdílených složek Azure pomocí rozhraní příkazového řádku](backup-afs-cli.md) a nakonfigurujte zálohování pro sdílenou složku. Pro tento článek použijte následující zdroje:

| Sdílená složka  | Účet úložiště | Region (Oblast) | Podrobnosti                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *soubory azurefiles*  | *afsaccount*      | Východní US | Původní zdroj zálohovaný pomocí služby Azure Backup                 |
| *azurefiles1* | *účet1*      | Východní US | Cílový zdroj používaný pro obnovení alternativního umístění |

Podobnou strukturu pro sdílené složky můžete použít k vyzkoušení různých typů obnovení vysvětlených v tomto článku.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Načtení bodů obnovení pro sdílenou složku Azure

Pomocí rutiny [seznamu az zálohování recoverypoint](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) uveďte všechny body obnovení pro zálohovanou sdílenou složku.

Následující příklad načte seznam bodů obnovení pro sdílenou složku *azurefiles* v účtu úložiště *afsaccount.*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --backup-management-type azurestorage --item-name “AzureFileShare;azurefiles” --workload-type azurefileshare --out table
```

Předchozí rutinu můžete také spustit pomocí popisný název kontejneru a položky poskytnutím následujících dvou dalších parametrů:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Sada výsledků je seznam bodů obnovení s podrobnostmi o čase a konzistenci pro každý bod obnovení.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

Name **Name** Atribut ve výstupu odpovídá název bodu obnovení, který lze použít jako hodnotu parametru **--rp-name** v operacích obnovení.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Obnovení úplného sdílení pomocí příkazového příkazového příkazu Azure

Tuto možnost obnovení můžete použít k obnovení úplné sdílené složky v původním nebo alternativním umístění.

Definujte následující parametry pro provádění operací obnovení:

* **--název kontejneru**: Název účtu úložiště, který hostuje zálohovanou původní sdílenou složku. Chcete-li načíst název nebo popisný název kontejneru, použijte příkaz [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--název položky**: Název zálohované původní sdílené složky, kterou chcete použít pro operaci obnovení. Chcete-li načíst název nebo popisný název zálohované položky, použijte příkaz [az backup item.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

### <a name="restore-a-full-share-to-the-original-location"></a>Obnovení úplné sdílené složky do původního umístění

Při obnovení do původního umístění není nutné zadávat parametry související s cílem. Musí být k dispozici pouze **vyřešit konflikt.**

Následující příklad používá rutinu [obnovení zálohování az azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) s režimem obnovení nastavenou na *původní umístění* k obnovení sdílené složky *azurefiles* v původním umístění. Bod obnovení 932883129628959823, který jste získali v [bodech obnovení fetch pro sdílenou složku Azure](#fetch-recovery-points-for-the-azure-file-share):

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

Name **Name** Atribut ve výstupu odpovídá názvu úlohy, která je vytvořena službou zálohování pro operaci obnovení. Chcete-li sledovat stav úlohy, použijte rutinu [az zálohování úlohy.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

### <a name="restore-a-full-share-to-an-alternate-location"></a>Obnovení úplné sdílené složky do alternativního umístění

Tuto možnost můžete použít k obnovení sdílené složky do alternativního umístění a zachování původní sdílené složky tak, jak je. Zadejte následující parametry pro obnovení alternativního umístění:

* **--target-storage-account**: Účet úložiště, do kterého je obnoven zálohovaný obsah. Cílový účet úložiště musí být ve stejném umístění jako trezor.
* **--target-file-share**: Sdílená složka v rámci cílového účtu úložiště, do kterého je obnoven zálohovaný obsah.
* **--target-folder**: Složka pod sdílenou složkou souboru, do které jsou data obnovena. Pokud má být zálohovaný obsah obnoven do kořenové složky, přiřazujte hodnoty cílové složky jako prázdný řetězec.
* **--resolve-conflict**: Instrukce, pokud dojde ke konfliktu s obnovenými daty. Přijímá **přepsání** nebo **přeskočení**.

Následující příklad používá [az obnovení obnovení azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) s režimem obnovení jako *alternativní umístění* k obnovení sdílené složky *azurefiles* v účtu *úložiště afsaccount* do sdílené složky *azurefiles1"* v účtu úložiště *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

Name **Name** Atribut ve výstupu odpovídá názvu úlohy, která je vytvořena službou zálohování pro operaci obnovení. Chcete-li sledovat stav úlohy, použijte rutinu [az zálohování úlohy.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="item-level-recovery"></a>Obnovení na úrovni položek

Tuto možnost obnovení můžete použít k obnovení jednotlivých souborů nebo složek v původním nebo alternativním umístění.

Definujte následující parametry pro provádění operací obnovení:

* **--název kontejneru**: Název účtu úložiště, který hostuje zálohovanou původní sdílenou složku. Chcete-li načíst název nebo popisný název kontejneru, použijte příkaz [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--název položky**: Název zálohované původní sdílené složky, kterou chcete použít pro operaci obnovení. Chcete-li načíst název nebo popisný název zálohované položky, použijte příkaz [az backup item.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

Zadejte následující parametry pro položky, které chcete obnovit:

* **SourceFilePath**: Absolutní cesta k souboru, který má být obnoven ve sdílené složce jako řetězec. Tato cesta je stejná cesta použitá při [stahování souboru úložiště az](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) nebo [soubor úložiště az zobrazit](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) příkazy rozhraní příkazového příkazu.
* **SourceFileType**: Zvolte, zda je vybraný adresář nebo soubor. Přijímá **adresář** nebo **soubor**.
* **ResolveConflict**: Instrukce, pokud dojde ke konfliktu s obnovenými daty. Přijímá **přepsání** nebo **přeskočení**.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Obnovení jednotlivých souborů nebo složek do původního umístění

Použijte rutinu obnovení [obnovení az azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) s režimem obnovení nastaveným na *původní umístění* k obnovení určitých souborů nebo složek do původního umístění.

Následující příklad obnoví soubor *RestoreTest.txt* v původním umístění: sdílené složky *azurefiles.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Name **Name** Atribut ve výstupu odpovídá názvu úlohy, která je vytvořena službou zálohování pro operaci obnovení. Chcete-li sledovat stav úlohy, použijte rutinu [az zálohování úlohy.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Obnovení jednotlivých souborů nebo složek do alternativního umístění

Chcete-li obnovit určité soubory nebo složky do alternativního umístění, použijte rutinu [obnovení zálohování az s](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) režimem obnovení nastaveným na alternativní *umístění* a zadejte následující parametry související s cílem:

* **--target-storage-account**: Účet úložiště, do kterého je obnoven zálohovaný obsah. Cílový účet úložiště musí být ve stejném umístění jako trezor.
* **--target-file-share**: Sdílená složka v rámci cílového účtu úložiště, do kterého je obnoven zálohovaný obsah.
* **--target-folder**: Složka pod sdílenou složkou souboru, do které jsou data obnovena. Pokud má být zálohovaný obsah obnoven do kořenové složky, přiřazujte hodnotu cílové složky jako prázdný řetězec.

Následující příklad obnoví soubor *RestoreTest.txt,* který byl původně přítomen ve sdílené složce *azurefiles,* do alternativního umístění: složka *restoredata* ve sdílené složce *azurefiles1* hostovaná v účtu úložiště *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Name **Name** Atribut ve výstupu odpovídá názvu úlohy, která je vytvořena službou zálohování pro operaci obnovení. Chcete-li sledovat stav úlohy, použijte rutinu [az zálohování úlohy.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [spravovat zálohy sdílení souborů Azure pomocí rozhraní příkazového řádku Azure](manage-afs-backup-cli.md).
