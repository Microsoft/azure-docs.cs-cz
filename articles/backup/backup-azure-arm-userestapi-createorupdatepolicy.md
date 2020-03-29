---
title: Vytvoření zásad zálohování pomocí rozhraní REST API
description: V tomto článku se dozvíte, jak vytvořit a spravovat zásady zálohování (plán a uchovávání informací) pomocí rozhraní REST API.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: 0718ebc3612f53f1c2cc279096dd92de69bb5ef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963848"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Vytvoření zásad zálohování služby Azure Recovery Services pomocí rozhraní REST API

Kroky k vytvoření zásady zálohování pro trezor služby Azure Recovery Services jsou uvedeny v [dokumentu rozhraní REST API zásad](/rest/api/backup/protectionpolicies/createorupdate). Využijme tento dokument jako odkaz k vytvoření zásad pro zálohování virtuálních počítačů Azure.

## <a name="create-or-update-a-policy"></a>Vytvoření nebo aktualizace zásady

Chcete-li vytvořit nebo aktualizovat zásady zálohování Azure, použijte následující operaci *PUT*

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2019-05-13
```

A `{policyName}` `{vaultName}` jsou k dispozici v identifikátoru URI. Další informace jsou uvedeny v textu požadavku.

## <a name="create-the-request-body"></a>Vytvoření těla požadavku

Chcete-li například vytvořit zásadu pro zálohování virtuálních počítačích Azure, jsou následující součásti těla požadavku.

|Name (Název)  |Požaduje se  |Typ  |Popis  |
|---------|---------|---------|---------|
|properties     |   True      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | Vlastnosti ProtectionPolicyResource        |
|tags     |         | Objekt        |  Značky prostředků       |

Úplný seznam definic v těle požadavku naleznete v [dokumentu rozhraní REST API zásad zálohování](/rest/api/backup/protectionpolicies/createorupdate).

### <a name="example-request-body"></a>Ukázkové tělo požadavku

Následující tělo požadavku definuje zásady zálohování pro zálohování virtuálních počítačích Azure.

Politika říká:

- Každé pondělí, středu a čtvrtek v 10:00 tichomořského standardního času si můžete každý týden zálohovat.
- Uchovávejte zálohy pořízené každé pondělí, středu, čtvrtek po dobu jednoho týdne.
- Uchovávejte zálohy pořízené každou první středu a třetí čtvrtek v měsíci po dobu dvou měsíců (přepíše předchozí podmínky uchovávání, pokud existují).
- Zachovat zálohy odebrané čtvrté pondělí a čtvrtý čtvrtek v únoru a listopadu po dobu čtyř let (přepíše předchozí podmínky uchovávání, pokud existuje).

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
> Formáty času pro podporu plánu a uchovávání pouze DateTime. Nepodporují formát času sám.

## <a name="responses"></a>Odezvy

Vytvoření nebo aktualizace zásad zálohování je [asynchronní operace](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). To znamená, že tato operace vytvoří další operaci, která je třeba sledovat samostatně.

Vrátí dvě odpovědi: 202 (Přijato) při vytvoření jiné operace a potom 200 (OK) po dokončení této operace.

|Name (Název)  |Typ  |Popis  |
|---------|---------|---------|
|200 OK     |    [Zdroj zásad ochrany](/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202 Přijato     |         |     Accepted    |

### <a name="example-responses"></a>Příklady odpovědí

Po odeslání *put* žádost o vytvoření nebo aktualizaci zásad, počáteční odpověď je 202 (Přijato) s hlavičkou umístění nebo Azure-async-header.

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

Potom sledujte výslednou operaci pomocí hlavičky umístění nebo hlavičky Azure-AsyncOperation pomocí jednoduchého příkazu *GET.*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
```

Po dokončení operace vrátí 200 (OK) s obsahem zásad v těle odpovědi.

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

Pokud se zásada již používá k ochraně položky, všechny aktualizace v zásadách bude mít za následek [změnu ochrany](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) pro všechny tyto přidružené položky.

## <a name="next-steps"></a>Další kroky

[Povolte ochranu nechráněného virtuálního počítače Azure](backup-azure-arm-userestapi-backupazurevms.md).

Další informace o rozhraních API Azure Backup REST najdete v následujících dokumentech:

- [Rozhraní REST ROZHRANÍ REST ZPROSTŘEDKOVATELE Služby Azure Recovery Services](/rest/api/recoveryservices/)
- [Začínáme s Azure REST API](/rest/api/azure/)
