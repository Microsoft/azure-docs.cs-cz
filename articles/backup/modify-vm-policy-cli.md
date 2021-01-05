---
title: Aktualizovat existující zásady zálohování virtuálního počítače pomocí rozhraní příkazového řádku
description: Přečtěte si, jak aktualizovat existující zásady zálohování virtuálního počítače pomocí Azure CLI.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: ba4ded4c82a1eaea7c20ea94da580a8702467b85
ms.sourcegitcommit: 89c0482c16bfec316a79caa3667c256ee40b163f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2021
ms.locfileid: "97858831"
---
# <a name="update-the-existing-vm-backup-policy-using-cli"></a>Aktualizovat existující zásady zálohování virtuálního počítače pomocí rozhraní příkazového řádku

Pomocí Azure CLI můžete aktualizovat existující zásady zálohování virtuálního počítače. V tomto článku se dozvíte, jak exportovat existující zásady do souboru JSON, upravit soubor a potom pomocí Azure CLI aktualizovat zásady pomocí upravených zásad.

## <a name="modify-an-existing-policy"></a>Úprava existující zásady

Pokud chcete upravit existující zásady zálohování virtuálního počítače, postupujte takto:

1. Spuštěním příkazu [AZ Backup Policy show zobrazíte](https://docs.microsoft.com/cli/azure/backup/policy#az_backup_policy_show) podrobnosti o zásadách, které chcete aktualizovat.

    Příklad:

    ```azurecli
    az backup policy show --name testing123 --resource-group rg1234 --vault-name testvault
    ```

    Výše uvedený příklad zobrazuje podrobnosti o zásadách virtuálních počítačů s názvem *testing123*.

    Výstup:

    ```json
    {
    "eTag": null,
    "id": "/Subscriptions/efgsf-123-test-subscription/resourceGroups/rg1234/providers/Microsoft.RecoveryServices/vaults/testvault/backupPolicies/testing123",
    "location": null,
    "name": "testing123",
    "properties": {
        "backupManagementType": "AzureIaasVM",
        "instantRpDetails": {
        "azureBackupRgNamePrefix": null,
        "azureBackupRgNameSuffix": null
        },
        "instantRpRetentionRangeInDays": 2,
        "protectedItemsCount": 0,
        "retentionPolicy": {
        "dailySchedule": {
            "retentionDuration": {
            "count": 180,
            "durationType": "Days"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "monthlySchedule": null,
        "retentionPolicyType": "LongTermRetentionPolicy",
        "weeklySchedule": {
            "daysOfTheWeek": [
            "Sunday"
            ],
            "retentionDuration": {
            "count": 30,
            "durationType": "Weeks"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "yearlySchedule": null
        },
        "schedulePolicy": {
        "schedulePolicyType": "SimpleSchedulePolicy",
        "scheduleRunDays": null,
        "scheduleRunFrequency": "Daily",
        "scheduleRunTimes": [
            "2020-08-03T04:30:00+00:00"
        ],
        "scheduleWeeklyFrequency": 0
        },
        "timeZone": "UTC"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
    }
    ```

1. Uložte výše uvedený výstup do souboru. JSON. Můžete ho například uložit jako *Policy.jsna*.
1. Aktualizujte soubor JSON na základě vašich požadavků a uložte změny.

    Příklad: Chcete-li aktualizovat týdenní uchování na 60 dní, aktualizujte následující část souboru JSON změnou počtu na 60.

    ```json
            "retentionDuration": {
          "count": 60,
          "durationType": "Weeks"
        }

    ```

1. Uložte změny.
1. Spusťte příkaz [AZ Backup Policy set](https://docs.microsoft.com/cli/azure/backup/policy#az_backup_policy_set) a předejte úplnou cestu aktualizovaného souboru JSON jako hodnotu parametru **--Policy** .

    ```azurecli
    az backup policy set --resource-group rg1234 --vault-name testvault --policy C:\temp2\Policy.json --name testing123
    ```

>[!NOTE]
>Ukázkovou zásadu JSON můžete také načíst spuštěním příkazu [AZ Backup Policy Get-Default-for-VM](https://docs.microsoft.com/cli/azure/backup/policy#az_backup_policy_get_default_for_vm) .

## <a name="next-steps"></a>Další kroky

- [Správa záloh virtuálních počítačů Azure pomocí služby Azure Backup](backup-azure-manage-vms.md)
