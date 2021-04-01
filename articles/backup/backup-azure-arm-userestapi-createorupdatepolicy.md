---
title: Vytvoření zásad zálohování pomocí REST API
description: V tomto článku se dozvíte, jak vytvářet a spravovat zásady zálohování (plánování a uchovávání) pomocí REST API.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: e4e6f5b5cf28c3830a91a494ea60680eee1546f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89179602"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Vytvoření zásad služby Azure Recovery Services Backup pomocí REST API

Postup vytvoření zásady zálohování pro trezor služby Azure Recovery Services je popsaný v [dokumentu REST API zásad](/rest/api/backup/protectionpolicies/createorupdate). Pojďme tento dokument použít jako referenci k vytvoření zásady pro zálohování virtuálních počítačů Azure.

## <a name="create-or-update-a-policy"></a>Vytvořit nebo aktualizovat zásadu

Pokud chcete vytvořit nebo aktualizovat zásady Azure Backup, použijte následující operaci *vložení* .

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2019-05-13
```

`{policyName}`A `{vaultName}` jsou k dispozici v identifikátoru URI. Další informace jsou k dispozici v textu žádosti.

## <a name="create-the-request-body"></a>Vytvoření textu žádosti

Pokud například chcete vytvořit zásadu pro zálohování virtuálního počítače Azure, níže jsou uvedené součásti textu žádosti.

|Name  |Požaduje se  |Typ  |Popis  |
|---------|---------|---------|---------|
|properties     |   Ano      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | Vlastnosti ProtectionPolicyResource        |
|tags     |         | Objekt        |  Značky prostředků       |

Úplný seznam definic v těle žádosti najdete v [dokumentu zásady zálohování REST API](/rest/api/backup/protectionpolicies/createorupdate).

### <a name="example-request-body"></a>Příklad textu žádosti

Následující text požadavku definuje zásady zálohování pro zálohy virtuálních počítačů Azure.

Zásada uvádí:

- Využijte týdenní zálohování každé pondělí, středu, čtvrtek v 10:00. tichomořského času.
- Zachovejte zálohy každé pondělí, středu, čtvrtek po dobu jednoho týdne.
- Zachovejte zálohy provedené v každé první středu a třetí čtvrtek v měsíci po dobu dvou měsíců (potlačí předchozí podmínky uchování, pokud existují).
- Zachovejte zálohy ve čtvrtém pondělí a čtvrtém čtvrtek v únoru a listopadu po dobu čtyř let (potlačí předchozí podmínky uchování, pokud existují).

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
> Formáty času pro plán a uchování podporují pouze datum a čas. Nepodporují pouze formát času.

## <a name="responses"></a>Odpovědi

Vytvoření nebo aktualizace zásad zálohování je [asynchronní operace](../azure-resource-manager/management/async-operations.md). To znamená, že tato operace vytvoří další operaci, která musí být sledována samostatně.

Při vytvoření jiné operace vrátí dvě odpovědi: 202 (přijato) a po dokončení této operace pak 200 (OK).

|Název  |Typ  |Popis  |
|---------|---------|---------|
|200 OK     |    [PolicyResource ochrany](/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202 přijato     |         |     Přijato    |

### <a name="example-responses"></a>Příklady odpovědí

Jakmile odešlete žádost o *vložení* pro vytvoření nebo aktualizaci zásady, počáteční odpověď bude 202 (přijato) s hlavičkou umístění nebo Azure-Async-Header.

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
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Pak Sledujte výslednou operaci pomocí záhlaví umístění nebo hlavičky Azure-AsyncOperation jednoduchým příkazem *Get* .

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
```

Po dokončení operace vrátí 200 (OK) k obsahu zásad v těle odpovědi.

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

Pokud se pro ochranu položky už používá zásada, bude mít jakákoli aktualizace v zásadě za následek [úpravu ochrany](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) všech těchto přidružených položek.

## <a name="next-steps"></a>Další kroky

[Povolte ochranu pro nechráněný virtuální počítač Azure](backup-azure-arm-userestapi-backupazurevms.md).

Další informace o rozhraních REST API Azure Backup najdete v následujících dokumentech:

- [Poskytovatel Azure Recovery Services REST API](/rest/api/recoveryservices/)
- [Začínáme s Azure REST API](/rest/api/azure/)
