---
title: 'Azure Backup: Vytvoření zásady zálohování pomocí REST API služby'
description: Správa zásad zálohování (plán a uchovávání) pomocí rozhraní REST API
services: backup
author: pvrk
manager: shivamg
keywords: ROZHRANÍ REST API; Zálohování virtuálních počítačů Azure; Obnovení virtuálního počítače Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: 657a777da0e984a145c1c617a6194bf4ef56306e
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289831"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Vytvoření zásady zálohování Azure Recovery Services pomocí rozhraní REST API

Postup vytvoření zásady zálohování pro trezor služby Azure Recovery Services jsou uvedeny v [dokument zásad rozhraní API REST](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate). Dejte nám prosím použijte tento dokument jako můžete vytvořit zásadu pro zálohování virtuálních počítačů Azure.

## <a name="backup-policy-essentials"></a>Zásady zálohování essentials

- Zásady zálohování se vytvoří jeden trezor.
- Zásady zálohování lze vytvořit pro následující úlohy zálohování
  - Virtuální počítač Azure
  - SQL na virtuálním počítači Azure
  - Sdílená složka Azure
- Zásady můžete přiřadit ke spoustě prostředků. Zásady zálohování virtuálního počítače Azure je možné chránit mnoha virtuálních počítačů Azure.
- Zásady se skládá ze dvou částí:
  - Plán: Kdy se má vytvořit zálohu
  - Uchovávání dat: Jak dlouho Každá záloha uchovávání.
- Plán může být definován jako "denní" nebo "týdenní" s konkrétní bod v čase.
- Uchovávání informací lze definovat pro "denní", "každý týden", "měsíc", "roční" body záloh.
- "každý týden" odkazuje na zálohy v určitý den v týdnu, "měsíc" znamená, že zálohy v určitý den v měsíci a "rok" odkazuje na zálohy v určitý den v roce.
- Uchování pro body zálohy "měsíc", "roční" se označuje jako "LongTermRetention".
- Po vytvoření trezoru zásadu pro zálohy virtuálních počítačů Azure s názvem "DefaultPolicy" je také vytvořen a slouží k zálohování virtuálních počítačů Azure.

Chcete-li vytvořit nebo aktualizovat zásady služby Azure Backup, použijte následující *UMÍSTIT* operace

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2016-12-01
```

`{policyName}` a `{vaultName}` jsou k dispozici v identifikátoru URI. Další informace najdete v textu požadavku.

## <a name="create-the-request-body"></a>Vytvořit datovou část požadavku

Například můžete vytvořit zásadu pro zálohování virtuálních počítačů Azure, jsou následující komponenty obsahu žádosti.

|Název  |Požaduje se  |Typ  |Popis  |
|---------|---------|---------|---------|
|vlastnosti     |   True (Pravda)      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | Vlastnosti ProtectionPolicyResource        |
|značky     |         | Objekt        |  Značky prostředků       |

Úplný seznam definic v textu požadavku, najdete [zásady zálohování rozhraní REST API dokumentu](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate).

### <a name="example-request-body"></a>Text požadavku na příkladu

Následující text požadavku definuje zásady zálohování pro zálohy virtuálních počítačů Azure.

Uvádí, že zásady:

- Proveďte zálohu týdenní každé pondělí, středu, čtvrtek v 10:00 a Tichomoří (běžný čas).
- Uchovat zálohy pořízené každé pondělí, středu, čtvrtek, jeden týden.
- Uchovat zálohy pořízené v každý první, středu a třetí úterý v měsíci po dobu dvou měsíců (přepíše předchozí uchování podmínky, pokud existuje).
- Uchování záloh provedených ve čtvrtém pondělí a čtvrtý čtvrtek v únoru a listopadu čtyř let (přepíše předchozí uchování podmínky, pokud existuje).

```json
{
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "timeZone": "Pacific Standard Time",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ]
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    }
  }
}
```

> [!IMPORTANT]
> Formáty času pro plán a uchovávání podporují pouze data a času. Formát času samostatně nepodporují.

## <a name="responses"></a>Odpovědi

Vytvoření/aktualizace zásady zálohování se [asynchronní operace](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Znamená to, že tato operace vytvoří jiná operace, která je třeba sledovat samostatně.

Vrátí dva odpovědi: 202 (přijato), když se vytvoří jiná operace a potom 200 (OK) po dokončení této operace.

|Název  |Typ  |Popis  |
|---------|---------|---------|
|200 OK     |    [PolicyResource ochrany](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202 přijato     |         |     Přijato    |

### <a name="example-responses"></a>Ukázkové odpovědi

Jakmile odešlete *UMÍSTIT* žádost pro zásady vytvoření nebo aktualizaci, první odezvy je 202 (přijato) s hlavička umístění nebo Azure asynchronní záhlaví.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operations/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Powered-By: ASP.NET
```

Poté můžete sledovat pomocí hlavičky location nebo Azure-AsyncOperation záhlaví s jednoduchou výsledný operace *získat* příkazu.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
```

Po dokončení této operace vrátí 200 (OK) s obsahem zásady v textu odpovědi.

```json
{
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1",
  "name": "testPolicy1",
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies",
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ],
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    },
    "timeZone": "Pacific Standard Time",
    "protectedItemsCount": 0
  }
}
```

Pokud zásady se již používá k ochraně položku, bude výsledkem kterákoli aktualizace v zásadách [úpravy ochrany](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) pro všechny tyto přidružené položky.

## <a name="next-steps"></a>Další postup

[Povolit ochranu pro nechráněný virtuální počítač Azure](backup-azure-arm-userestapi-backupazurevms.md).

Další informace o rozhraní REST API služby Azure Backup najdete v následujících dokumentech:

- [Rozhraní REST API poskytovatele Azure Recovery Services](/rest/api/recoveryservices/)
- [Začínáme s Azure REST API](/rest/api/azure/)