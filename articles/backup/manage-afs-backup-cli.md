---
title: Správa záloh sdílení souborů Azure pomocí rozhraní příkazového řádku Azure
description: Zjistěte, jak pomocí rozhraní příkazového řádku Azure spravovat a monitorovat sdílené složky Azure zálohované službou Azure Backup.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 44a49913abd99b285397b8b78ad9d4c0f9df52ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934879"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Správa záloh sdílení souborů Azure pomocí rozhraní příkazového řádku Azure

Azure CLI poskytuje prostředí příkazového řádku pro správu prostředků Azure. Je to skvělý nástroj pro vytváření vlastní automatizace pro použití prostředků Azure. Tento článek vysvětluje, jak provádět úlohy pro správu a monitorování sdílených složek Azure, které jsou zálohovány [službou Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview). Tyto kroky můžete provést také na [portálu Azure](https://portal.azure.com/). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Chcete-li nainstalovat a používat příkaz příkazového příkazového příkazu místně, musíte spustit Azure CLI verze 2.0.18 nebo novější. Verzi rozhraní příkazového řádku zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že už máte sdílenou složku Azure podporovanou [službou Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview). Pokud ho nemáte, přečtěte si informace [o zálohování sdílených složek Azure pomocí rozhraní příkazového řádku](backup-afs-cli.md) a nakonfigurujte zálohování sdílených složek. Pro tento článek použijte následující zdroje:

* **Skupina prostředků**: *azurefiles*
* **RecoveryServicesVault**: *azurefilesvault*
* **Účet úložiště**: *afsaccount*
* **Sdílení souborů**: *azurefiles*

## <a name="monitor-jobs"></a>Monitorování úloh

Když spustíte operace zálohování nebo obnovení, služba zálohování vytvoří úlohu pro sledování. Chcete-li sledovat dokončené nebo aktuálně spuštěné úlohy, použijte rutinu [seznamu úloh zálohování az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) Pomocí zadávacích příkazů můžete také [pozastavit aktuálně spuštěnou úlohu](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) nebo [počkat na dokončení úlohy](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait).

Následující příklad zobrazuje stav úloh zálohování pro trezor *služby azurefilesvault* Recovery Services:

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

## <a name="modify-policy"></a>Změnit zásadu

Zásady zálohování můžete upravit a změnit tak frekvenci zálohování nebo rozsah uchování pomocí [zásady sady položek zálohování az](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy).

Chcete-li změnit zásadu, definujte následující parametry:

* **--název kontejneru**: Název účtu úložiště, který je hostitelem sdílené složky. Chcete-li načíst **název** nebo **popisný název** kontejneru, použijte příkaz [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--name**: Název sdílené složky, pro kterou chcete změnit zásadu. Chcete-li načíst **název** nebo **popisný název** zálohované položky, použijte příkaz [az backup item.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--policy-name**: Název zásady zálohování, kterou chcete nastavit pro sdílenou složku. [Pomocí seznamu zásad zálohování az](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list) můžete zobrazit všechny zásady pro váš trezor.

Následující příklad nastaví zásady zálohování *schedule2* pro sdílenou složku *azurefiles,* která je k dispozici v účtu úložiště *afsaccount.*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Předchozí příkaz můžete spustit také pomocí popisných názvů kontejneru a položky poskytnutím následujících dvou dalších parametrů:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Name **Name** Atribut ve výstupu odpovídá názvu úlohy, která je vytvořena službou zálohování pro operaci zásad změny. Chcete-li sledovat stav úlohy, použijte rutinu [az zálohování úlohy.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="stop-protection-on-a-file-share"></a>Zastavení ochrany sdílené složky

Ochranu sdílených složek Azure můžete zastavit dvěma způsoby:

* Zastavte všechny budoucí úlohy zálohování a *odstraňte* všechny body obnovení.
* Zastavte všechny budoucí úlohy zálohování, ale *ponechte* body obnovení.

Může být náklady spojené s opuštěním body obnovení v úložišti, protože základní snímky vytvořené službou Azure Backup zůstanou zachovány. Výhodou opuštění bodů obnovení je možnost obnovit sdílenou složku později, pokud chcete. Informace o nákladech na opuštění bodů obnovení naleznete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/storage/files). Pokud se rozhodnete odstranit všechny body obnovení, nemůžete sdílenou složku obnovit.

Chcete-li ukončit ochranu sdílené složky, definujte následující parametry:

* **--název kontejneru**: Název účtu úložiště, který je hostitelem sdílené složky. Chcete-li načíst **název** nebo **popisný název** kontejneru, použijte příkaz [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--název položky**: Název sdílené složky, pro kterou chcete ochranu zastavit. Chcete-li načíst **název** nebo **popisný název** zálohované položky, použijte příkaz [az backup item.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

### <a name="stop-protection-and-retain-recovery-points"></a>Zastavit ochranu a zachovat body obnovení

Chcete-li ochranu zastavit při zachování dat, použijte rutinu [az zálohování zakázat.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable)

Následující příklad zastaví ochranu sdílené složky *azurefiles,* ale zachová všechny body obnovení.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Předchozí příkaz můžete spustit také pomocí popisný název kontejneru a položky poskytnutím následujících dvou dalších parametrů:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Name **Name** Atribut ve výstupu odpovídá názvu úlohy, která je vytvořena službou zálohování pro operaci stop ochrany. Chcete-li sledovat stav úlohy, použijte rutinu [az zálohování úlohy.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

### <a name="stop-protection-without-retaining-recovery-points"></a>Zastavení ochrany bez zachování bodů obnovení

Chcete-li ochranu zastavit bez zachování bodů obnovení, použijte rutinu [az backup protection disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) s možností **delete-backup-data** nastavenou na **hodnotu true**.

Následující příklad zastaví ochranu sdílené složky *azurefiles* bez zachování bodů obnovení.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Předchozí příkaz můžete spustit také pomocí popisný název kontejneru a položky poskytnutím následujících dvou dalších parametrů:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Obnovení ochrany sdílené složky

Pokud jste zastavili ochranu sdílené složky Azure, ale zachovali body obnovení, můžete ochranu obnovit později. Pokud body obnovení nezachováte, nemůžete ochranu obnovit.

Chcete-li obnovit ochranu sdílené složky, definujte následující parametry:

* **--název kontejneru**: Název účtu úložiště, který je hostitelem sdílené složky. Chcete-li načíst **název** nebo **popisný název** kontejneru, použijte příkaz [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--název položky**: Název sdílené složky, pro kterou chcete obnovit ochranu. Chcete-li načíst **název** nebo **popisný název** zálohované položky, použijte příkaz [az backup item.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--policy-name**: Název zásady zálohování, pro kterou chcete obnovit ochranu sdílené složky.

Následující příklad používá rutinu [obnovení zabezpečení zálohování az](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) k obnovení ochrany pro sdílenou složku *azurefiles* pomocí zásad zálohování *schedule1.*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Předchozí příkaz můžete spustit také pomocí popisný název kontejneru a položky poskytnutím následujících dvou dalších parametrů:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

Name **Name** Atribut ve výstupu odpovídá názvu úlohy, která je vytvořena službou zálohování pro operaci obnovení ochrany. Chcete-li sledovat stav úlohy, použijte rutinu [az zálohování úlohy.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="unregister-a-storage-account"></a>Zrušení registrace účtu úložiště

Pokud chcete chránit sdílené složky v určitém účtu úložiště pomocí jiného trezoru služby Recovery Services, [nejprve zastavte ochranu všech sdílených složek](#stop-protection-on-a-file-share) v tomto účtu úložiště. Potom zrušit registraci účtu z trezoru služby Recovery Services aktuálně používaného pro ochranu.

Chcete-li zrušit registraci účtu úložiště, musíte zadat název kontejneru. Chcete-li načíst **název** nebo **popisný název** kontejneru, použijte příkaz [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)

Následující příklad zruší registraci účtu *úložiště afsaccount* z *azurefilesvault* pomocí rutiny [az backup container unregister.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister)

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Předchozí rutinu můžete spustit také pomocí popisný název kontejneru poskytnutím následujícího dalšího parametru:

* **--backup-management-type**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Další kroky

Další informace najdete [v tématu Poradce při potížích se zálohováním sdílených složek Azure](troubleshoot-azure-files.md).
