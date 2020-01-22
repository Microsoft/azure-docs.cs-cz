---
title: Správa záloh sdílených složek Azure pomocí rozhraní příkazového řádku
description: Naučte se používat Azure CLI ke správě a monitorování sdílených složek Azure zálohovaných službou Azure Backup.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: bf824b1868ad7c9e4df2cceeca101d82272e18d6
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294471"
---
# <a name="manage-azure-file-share-backups-with-azure-cli"></a>Správa záloh sdílených složek Azure pomocí Azure CLI

Rozhraní příkazového řádku Azure (CLI) poskytuje prostředí příkazového řádku pro správu prostředků Azure. Je skvělým nástrojem pro vytváření vlastních automatizace pro používání prostředků Azure. Tento článek vysvětluje, jak provádět následující úlohy pro správu a monitorování sdílených složek Azure, které jsou zálohované službou [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview). Tyto kroky můžete provést také s [Azure Portal](https://portal.azure.com/).

* [Monitorování úloh](#monitor-jobs)
* [Upravit zásadu](#modify-policy)
* [Zastavení ochrany sdílené složky](#stop-protection-on-a-file-share)
* [Obnovení ochrany sdílené složky](#resume-protection-on-a-file-share)
* [Zrušení registrace účtu úložiště](#unregister-a-storage-account)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete rozhraní příkazového řádku nainstalovat a používat místně, musíte použít Azure CLI verze 2.0.18 nebo novější. Verzi rozhraní příkazového řádku zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že už máte sdílenou složku Azure zálohovanou službou [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) . Pokud ho nemáte, přečtěte si téma [Zálohování sdílených složek Azure pomocí](backup-afs-cli.md) rozhraní příkazového řádku, ve kterém můžete nakonfigurovat zálohování sdílených složek. V tomto článku budeme používat následující zdroje:

* **Skupina prostředků**: *azurefiles*
* **RecoveryServicesVault**: *azurefilesvault*
* **Účet úložiště**: *afsaccount*
* **Sdílená složka**: *azurefiles*

## <a name="monitor-jobs"></a>Monitorování úloh

Při aktivaci operací zálohování nebo obnovení vytvoří služba Backup úlohu pro sledování. Chcete-li monitorovat dokončené nebo aktuálně spuštěné úlohy, použijte rutinu [AZ Backup Job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) . CLI také umožňuje [pozastavit aktuálně spuštěnou úlohu](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) nebo počkat na [dokončení úlohy](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait).

Následující příklad zobrazuje stav úloh zálohování pro trezor služby *azurefilesvault* Recovery Services:

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>Změnit zásady

Zásadu zálohování můžete upravit tak, aby se změnila frekvence zálohování nebo rozsah uchování pomocí [příkaz AZ Backup Item set-Policy](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy).

Chcete-li změnit zásadu, definujte následující parametry:

* **--Container-Name** je název účtu úložiště, který hostuje sdílenou složku. Pokud chcete načíst **název** nebo **popisný název** svého kontejneru, použijte příkaz [AZ Backup Container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Name** je název sdílené složky, pro kterou chcete zásadu změnit. Pokud chcete načíst **název** nebo **popisný název** zálohované položky, použijte příkaz [AZ Backup Item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .
* **--Policy-Name** je název zásady zálohování, kterou chcete nastavit pro sdílenou složku. Pokud chcete zobrazit všechny zásady pro svůj trezor, můžete použít možnost [AZ Backup Policy list](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list) .

Následující příklad nastaví zásady zálohování *schedule2* pro sdílenou složku *azurefiles* , která je k dispozici v účtu úložiště *afsaccount* .

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Můžete také provést výše uvedený příkaz s použitím "popisných názvů" pro kontejner a položku zadáním následujících dvou dalších parametrů:

* **--zálohování-Správa-typ**: *azurestorage*
* **--úlohu-typ**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Atribut **Name** ve výstupu odpovídá názvu úlohy, kterou vytvořila služba zálohování pro operaci "Změna zásad". Chcete-li sledovat stav úlohy, použijte rutinu [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="stop-protection-on-a-file-share"></a>Zastavení ochrany sdílené složky

Ochranu sdílených složek Azure můžete zastavit dvěma způsoby:

* Zastavit všechny budoucí úlohy zálohování a *Odstranit* všechny body obnovení
* Zastavte všechny budoucí úlohy zálohování, ale *ponechejte* body obnovení.

Je možné, že jsou k dispozici náklady spojené s ponecháním bodů obnovení v úložišti, protože jsou zachovány základní snímky vytvořené nástrojem Azure Backup. Výhodou ponechání bodů obnovení je ale možnost obnovení sdílené složky později v případě potřeby. Informace o nákladech na ponechávání bodů obnovení najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/storage/files). Pokud se rozhodnete odstranit všechny body obnovení, nebudete moct sdílenou složku obnovit.

Ochranu sdílené složky zabráníte tak, že definujete následující parametry:

* **--Container-Name** je název účtu úložiště, který hostuje sdílenou složku. Pokud chcete načíst **název** nebo **popisný název** svého kontejneru, použijte příkaz [AZ Backup Container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name** je název sdílené složky, pro kterou chcete zastavit ochranu. Pokud chcete načíst **název** nebo **popisný název** zálohované položky, použijte příkaz [AZ Backup Item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

### <a name="stop-protection-and-retain-recovery-points"></a>Zastavení ochrany a zachování bodů obnovení

Pokud chcete zastavit ochranu a zachovat data, použijte rutinu [AZ Backup Protection Disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) .

Následující příklad zastaví ochranu sdílené složky *azurefiles* , ale zachová všechny body obnovení.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Můžete také provést příkaz uvedený výše pomocí popisného názvu pro kontejner a položku zadáním následujících dvou dalších parametrů:

* **--zálohování-Správa-typ**: *azurestorage*
* **--úlohu-typ**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Atribut **Name** ve výstupu odpovídá názvu úlohy, kterou vytvořila služba zálohování pro operaci zastavení ochrany. Chcete-li sledovat stav úlohy, použijte rutinu [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

### <a name="stop-protection-without-retaining-recovery-points"></a>Zastavit ochranu bez zachování bodů obnovení

Pokud chcete zastavit ochranu bez zachování bodů obnovení, použijte rutinu [AZ Backup Protection Disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) s možností th e **Delete-Backup-data** nastavenou na **hodnotu true**.

Následující příklad zastaví ochranu sdílené složky *azurefiles* bez zachování bodů obnovení:

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Můžete také provést výše uvedený příkaz pomocí popisného názvu pro kontejner a položku zadáním následujících dvou dalších parametrů:

* **--zálohování-Správa-typ**: *azurestorage*
* **--úlohu-typ**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Obnovení ochrany sdílené složky

Pokud jste zastavili ochranu sdílené složky Azure, ale zachovali body obnovení, můžete ochranu obnovit později. Pokud neuchováváte body obnovení, nelze obnovit ochranu.

Chcete-li obnovit ochranu sdílené složky, definujte následující parametry:

* **--Container-Name** je název účtu úložiště, který hostuje sdílenou složku. Pokud chcete načíst **název** nebo **popisný název** svého kontejneru, použijte příkaz [AZ Backup Container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name** je název sdílené složky, pro kterou chcete obnovit ochranu. Pokud chcete načíst **název** nebo **popisný název** zálohované položky, použijte příkaz [AZ Backup Item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .
* **--Policy-Name** je název zásad zálohování, pro které chcete obnovit ochranu sdílené složky.

Následující příklad používá rutinu [AZ Backup Protection Resume](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) k obnovení ochrany sdílené složky *azurefiles* pomocí zásady zálohování *schedule1* .

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Můžete také provést výše uvedený příkaz pomocí popisného názvu pro kontejner a položku zadáním následujících dvou dalších parametrů:

* **--zálohování-Správa-typ**: *azurestorage*
* **--úlohu-typ**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

Atribut **Name** ve výstupu odpovídá názvu úlohy, kterou vytvořila zálohovací služba pro operaci obnovení ochrany. Chcete-li sledovat stav úlohy, použijte rutinu [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="unregister-a-storage-account"></a>Zrušení registrace účtu úložiště

Pokud chcete chránit sdílené složky v konkrétním účtu úložiště pomocí jiného trezoru služby Recovery Services, nejdřív [Zastavte ochranu pro všechny sdílené složky](#stop-protection-on-a-file-share) v tomto účtu úložiště. Pak zrušte registraci účtu z trezoru služby Recovery Services, který se aktuálně používá k ochraně.

Abyste mohli zrušit registraci účtu úložiště, musíte zadat název kontejneru. Pokud chcete načíst **název** nebo **popisný název** svého kontejneru, použijte příkaz [AZ Backup Container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .

Následující příklad zruší registraci účtu úložiště *afsaccount* z *azurefilesvault* pomocí rutiny [AZ Backup Container Unregister](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister) .

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Můžete také provést rutinu uvedenou výše pomocí popisného názvu pro kontejner zadáním následujícího dalšího parametru:

* **--zálohování-Správa-typ**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [řešení chyb zálohování a obnovení sdílených složek Azure](troubleshoot-azure-files.md) .
