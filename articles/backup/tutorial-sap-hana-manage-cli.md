---
title: 'Kurz: Správa zálohovaných SAP HANA DB pomocí rozhraní příkazového řádku'
description: V tomto kurzu se naučíte Spravovat zálohované SAP HANA databáze běžící na virtuálním počítači Azure pomocí Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 665dfc64e750f448fc4c1a2d7e18f0cb6552f223
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371766"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Kurz: Správa databází SAP HANA ve virtuálním počítači Azure pomocí Azure CLI

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo prostřednictvím skriptů. Tato dokumentace podrobně popisuje, jak spravovat zálohovanou SAP HANA databázi na virtuálním počítači Azure – to vše pomocí Azure CLI. Tyto kroky můžete provést také pomocí [Azure Portal](./sap-hana-db-manage.md).

Pomocí [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) spustit příkazy rozhraní příkazového řádku.

Na konci tohoto kurzu budete umět:

> [!div class="checklist"]
>
> * Monitorování úloh zálohování a obnovení
> * Ochrana nových databází přidaných do instance SAP HANA
> * Změnit zásadu
> * Zastavení ochrany
> * Obnovit ochranu

Pokud jste použili [zálohu databáze SAP HANA v Azure pomocí](tutorial-sap-hana-backup-cli.md) rozhraní příkazového řádku k zálohování databáze SAP HANA, budete používat následující prostředky:

* Skupina prostředků s názvem *saphanaResourceGroup*
* Trezor s názvem *saphanaVault*
* chráněný kontejner s názvem *VMAppContainer; COMPUTE; saphanaResourceGroup; saphanaVM*
* Zálohovaná databáze/položka s názvem *saphanadatabase; hxe; hxe*
* prostředky v oblasti *westus2*

Azure CLI usnadňuje správu SAP HANA databáze běžící na virtuálním počítači Azure, který je zálohovaný pomocí Azure Backup. Tento kurz podrobně popisuje jednotlivé operace správy.

## <a name="monitor-backup-and-restore-jobs"></a>Monitorování úloh zálohování a obnovení

Pokud chcete monitorovat dokončené nebo aktuálně spuštěné úlohy (zálohování nebo obnovení), použijte rutinu [AZ Backup Job list](/cli/azure/backup/job#az-backup-job-list) . CLI také umožňuje [pozastavit aktuálně spuštěnou úlohu](/cli/azure/backup/job#az-backup-job-stop) nebo počkat na [dokončení úlohy](/cli/azure/backup/job#az-backup-job-wait).

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

Výstup bude vypadat zhruba takto:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Změnit zásady

Pokud chcete změnit zásady pro SAP HANA konfiguraci zálohování, použijte rutinu [AZ Backup Policy set](/cli/azure/backup/policy#az-backup-policy-set) . Parametr Name v této rutině odkazuje na zálohovanou položku, jejíž zásady chceme změnit. Pro účely tohoto kurzu nahrazujeme zásadu naší SAP HANA databáze *saphanadatabase; hxe; hxe* novou zásadou *newsaphanaPolicy*. Nové zásady se dají vytvořit pomocí rutiny [AZ Backup Policy Create](/cli/azure/backup/policy#az-backup-policy-create) .

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

Výstup by měl vypadat takto:

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="create-incremental-backup-policy"></a>Vytvořit zásady přírůstkového zálohování

Chcete-li vytvořit zásady přírůstkového zálohování, spusťte příkaz [AZ Backup Policy Create](https://docs.microsoft.com/cli/azure/backup/policy#az_backup_policy_create) s následujícími parametry:

* **--zálohování-Správa-typ** – úlohy Azure
* **--typ úlohy-typ** – SAPHana
* **--Name** – název zásady
* **--Policy** -soubor. JSON s příslušnými podrobnostmi pro plán a uchovávání
* **--Resource-Group** -skupina prostředků trezoru
* **--trezor-Name** – název trezoru

Příklad:

```azurecli
az backup policy create --resource-group saphanaResourceGroup --vault-name saphanaVault --name sappolicy --backup-management-type AzureWorkload --policy sappolicy.json --workload-type SAPHana
```

Ukázkový JSON (sappolicy.jszapnutý):

```json
  "eTag": null,
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/saphanaVault/backupPolicies/sappolicy",
  "location": null,
  "name": "sappolicy",
  "properties": {
    "backupManagementType": "AzureWorkload",
    "makePolicyConsistent": null,
    "protectedItemsCount": 0,
    "settings": {
      "isCompression": false,
      "issqlcompression": false,
      "timeZone": "UTC"
    },
    "subProtectionPolicy": [
      {
        "policyType": "Full",
        "retentionPolicy": {
          "dailySchedule": null,
          "monthlySchedule": {
            "retentionDuration": {
              "count": 60,
              "durationType": "Months"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "retentionPolicyType": "LongTermRetentionPolicy",
          "weeklySchedule": {
            "daysOfTheWeek": [
              "Sunday"
            ],
            "retentionDuration": {
              "count": 104,
              "durationType": "Weeks"
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "yearlySchedule": {
            "monthsOfYear": [
              "January"
            ],
            "retentionDuration": {
              "count": 10,
              "durationType": "Years"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          }
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Sunday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2021-01-19T00:30:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Incremental",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 30,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday",
            "Saturday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2017-03-07T02:00:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Log",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 15,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "scheduleFrequencyInMins": 120,
          "schedulePolicyType": "LogSchedulePolicy"
        }
      }
    ],
    "workLoadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
} 
```
Po úspěšném vytvoření zásady se ve výstupu příkazu zobrazí JSON zásad, který jste předali jako parametr při provádění příkazu.

Pokud chcete určit požadovanou četnost zálohování a uchování přírůstkových záloh, můžete upravit následující část zásady.

Příklad:

```json
{
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 30,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Monday",
      "Tuesday",
      "Wednesday",
      "Thursday",
      "Friday",
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

Příklad:

Pokud chcete provádět přírůstkové zálohování pouze v sobotu a uchovat je po dobu 60 dnů, proveďte v zásadě tyto změny:

* Aktualizace počtu **retentionDurationů** na 60 dní
* Zadat pouze sobotu jako **ScheduleRunDays**

```json
 {
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 60,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Ochrana nových databází přidaných do instance SAP HANA

Při [registraci instance SAP HANA s Recovery Services trezorem](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) se automaticky zjistí všechny databáze v této instanci.

V případě, že se do instance SAP HANA později přidají nové databáze, použijte rutinu [AZ Backup Protected-Item Initialize](/cli/azure/backup/protectable-item#az-backup-protectable-item-initialize) . Tato rutina zjišťuje nově přidané databáze.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Potom pomocí rutiny [AZ Backup Protected-Item list](/cli/azure/backup/protectable-item#az-backup-protectable-item-list) vypíšete všechny databáze, které byly zjištěny v instanci SAP HANA. Tento seznam však vyloučí databáze, na kterých bylo zálohování již nakonfigurováno. Po zjištění databáze, která se má zálohovat, najdete informace  [v tématu Povolení zálohování databáze SAP HANA](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database).

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

V tomto seznamu se zobrazí nová databáze, kterou chcete zálohovat, a to bude vypadat takto:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>Zastavení ochrany pro databázi SAP HANA

Ochranu databáze SAP HANA můžete zastavit několika způsoby:

* Zastavte všechny budoucí úlohy zálohování a odstraňte všechny body obnovení.
* Zastavte všechny budoucí úlohy zálohování a ponechte body obnovení beze změn.

Pokud se rozhodnete zachovat body obnovení, mějte na paměti tyto informace:

* Všechny body obnovení zůstanou nedotčené trvale a všechny vyřazení se zastaví při zastavení ochrany s uchováním dat.
* Bude se vám účtovat chráněná instance a spotřebované úložiště.
* Pokud odstraníte zdroj dat bez zastavení zálohování, nové zálohování se nezdaří.

Pojďme se podívat na všechny způsoby, jak zastavit ochranu podrobněji.

### <a name="stop-protection-with-retain-data"></a>Zastavení ochrany se zachováním dat

Pokud chcete zastavit ochranu s uchováním dat, použijte rutinu [AZ Backup Protection Disable](/cli/azure/backup/protection#az-backup-protection-disable) .

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

Výstup by měl vypadat takto:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Chcete-li zjistit stav této operace, použijte rutinu [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) .

### <a name="stop-protection-without-retain-data"></a>Zastavení ochrany bez zachování dat

Pokud chcete zastavit ochranu, aniž byste zachovali data, použijte rutinu [AZ Backup Protection Disable](/cli/azure/backup/protection#az-backup-protection-disable) .

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

Výstup by měl vypadat takto:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Chcete-li zjistit stav této operace, použijte rutinu [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) .

## <a name="resume-protection"></a>Obnovit ochranu

Když zastavíte ochranu pro SAP HANA databázi s uchováním dat, můžete později obnovit ochranu. Pokud nezachováte zálohovaná data, nebudete moci obnovit ochranu.

Pokud chcete pokračovat v ochraně, použijte rutinu [AZ Backup Protection Resume](/cli/azure/backup/protection#az-backup-protection-resume) .

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

Výstup by měl vypadat takto:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

Chcete-li zjistit stav této operace, použijte rutinu [AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) .

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak zálohovat databázi SAP HANA běžící na virtuálním počítači Azure pomocí Azure Portal, najdete [v tématu zálohování SAP HANA databáze na virtuálních počítačích Azure](./backup-azure-sap-hana-database.md) .

* Informace o tom, jak spravovat zálohovanou SAP HANA databázi běžící na virtuálním počítači Azure pomocí Azure Portal, najdete v tématu [Správa zálohovaných SAP HANA databází na virtuálním počítači Azure](./sap-hana-db-manage.md) .
