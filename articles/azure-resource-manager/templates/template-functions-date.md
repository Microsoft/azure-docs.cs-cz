---
title: Funkce šablony - datum
description: Popisuje funkce, které se mají použít v šabloně Azure Resource Manager pro práci s daty.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 75234a142b9f2fbe61c337bfeb378b47534bac79
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986275"
---
# <a name="date-functions-for-arm-templates"></a>Funkce data pro šablony ARM

Správce prostředků poskytuje následující funkce pro práci s daty v šablonách Správce prostředků Azure (ARM):

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Přidá dobu trvání času k základní hodnotě datatime.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| base | Ano | řetězec | Počáteční datetime hodnota pro přidání. Použijte [formát časového razítka ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
| doba trvání | Ano | řetězec | Hodnota času, která má být přidejte do základny. Může to být záporná hodnota. Použijte [formát doby trvání ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| formát | Ne | řetězec | Výstupní formát pro výsledek data času. Pokud není k dispozici, použije se formát základní hodnoty. Použijte standardní [formátovací řetězce](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací řetězce](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Návratová hodnota

Hodnota datetime, která je výsledkem přidání hodnoty doby trvání k základní hodnotě.

### <a name="examples"></a>Příklady

Následující ukázková šablona ukazuje různé způsoby přidávání časových hodnot.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]"
        }
    },
    "variables": {
        "add3Years": "[dateTimeAdd(parameters('baseTime'), 'P3Y')]",
        "subtract9Days": "[dateTimeAdd(parameters('baseTime'), '-P9D')]",
        "add1Hour": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [],
    "outputs": {
        "add3Years": {
            "value": "[variables('add3Years')]",
            "type": "string"
        },
        "subtract9Days": {
            "value": "[variables('subtract9Days')]",
            "type": "string"
        },
        "add1Hour": {
            "value": "[variables('add1Hour')]",
            "type": "string"
        },
    }
}
```

Při nasazení předchozí šablony se základním časem `2020-04-07 14:53:14Z`, výstup je:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| add3Roky | Řetězec | 4/7/2023 14:53:14 |
| odečíst9Days | Řetězec | 3/29/2020 14:53:14 |
| add1Hodin | Řetězec | 4/7/2020 15:53:14 |

Další ukázková šablona ukazuje, jak nastavit čas zahájení pro plán automatizace.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "omsAutomationAccountName": {
            "type": "string",
            "defaultValue": "demoAutomation",
            "metadata": {
                "description": "Use an existing Automation account."
            }
        },
        "scheduleName": {
            "type": "string",
            "defaultValue": "demoSchedule1",
            "metadata": {
                "description": "Name of the new schedule."
            }
        },
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]",
            "metadata": {
                "description": "Schedule will start one hour from this time."
            }
        }
    },
    "variables": {
        "startTime": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [
        {
            "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",
            "type": "microsoft.automation/automationAccounts/schedules",
            "apiVersion": "2015-10-31",
            "location": "eastus2",
            "tags": {
            },
            "properties": {
                "description": "Demo Scheduler",
                "startTime": "[variables('startTime')]",
                "isEnabled": "true",
                "interval": 1,
                "frequency": "hour"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Vrátí aktuální hodnotu datatime (UTC) v zadaném formátu. Pokud není k dispozici žádný formát, použije se formát ISO 8601 (yyyMMddTHHmmmssZ). **Tuto funkci lze použít pouze ve výchozí hodnotě parametru.**

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| formát |Ne |řetězec |Hodnota kódovaná identifikátorem URI, který chcete převést na řetězec. Použijte standardní [formátovací řetězce](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací řetězce](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Poznámky

Tuto funkci lze použít pouze ve výrazu pro výchozí hodnotu parametru. Použití této funkce kdekoli jinde v šabloně vrátí chybu. Funkce není povolena v jiných částech šablony, protože při každém volání vrátí jinou hodnotu. Nasazení stejné šablony se stejnými parametry by spolehlivě nepřineslo stejné výsledky.

Pokud použijete [možnost znovu nasadit dřívější úspěšné nasazení](rollback-on-error.md)a dřívější nasazení obsahuje parametr, který používá utcNow, parametr není znovu vyhodnocen. Místo toho hodnota parametru z předchozího nasazení je automaticky znovu použita v nasazení vrácení zpět.

Buďte opatrní opětovné nasazení šablony, která závisí na funkci utcNow pro výchozí hodnotu. Při opětovném nasazení a neposkytují hodnotu parametru, funkce je přehodnocena. Pokud chcete aktualizovat existující prostředek spíše než vytvořit nový, předat hodnotu parametru z předchozího nasazení.

### <a name="return-value"></a>Návratová hodnota

Aktuální hodnota datačasu uTC.

### <a name="examples"></a>Příklady

Následující ukázková šablona zobrazuje různé formáty pro hodnotu datetime.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

Výstup z předchozího příkladu se liší pro každé nasazení, ale bude podobný:

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| utcOutput | řetězec | 20190305T175318Z |
| utcShortOutput | řetězec | 03/05/2019 |
| utcCustomOutput | řetězec | 3 5 |

Následující příklad ukazuje, jak použít hodnotu z funkce při nastavování hodnoty značky.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```