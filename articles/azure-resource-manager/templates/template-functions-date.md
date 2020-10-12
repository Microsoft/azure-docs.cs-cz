---
title: Šablony – funkce – datum
description: Popisuje funkce, které se použijí v šabloně Azure Resource Manager pro práci s daty.
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: abdc88ce15279b90f8f9dc05a38a2ae236498f12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86058040"
---
# <a name="date-functions-for-arm-templates"></a>Funkce data pro šablony ARM

Správce prostředků poskytuje následující funkce pro práci s daty v šablonách Azure Resource Manager (ARM):

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Přidá do základní hodnoty časový interval. Očekává se formát ISO 8601.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Description |
|:--- |:--- |:--- |:--- |
| base | Yes | řetězec | Počáteční hodnota DateTime pro sčítání. Použijte [Formát časového razítka ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
| doba trvání | Yes | řetězec | Hodnota času, která se má přidat do základu Může se jednat o zápornou hodnotu. Použijte [Formát doby trvání ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| formát | No | řetězec | Formát výstupu pro výsledek data a času. Pokud není zadaný, použije se formát základní hodnoty. Použijte buď [standardní formátovací řetězce](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [Vlastní řetězce formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Vrácená hodnota

Hodnota DateTime, která je výsledkem přidání hodnoty Duration k základní hodnotě.

### <a name="examples"></a>Příklady

Následující příklad šablony ukazuje různé způsoby přidávání časových hodnot.

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

Je-li předchozí šablona nasazena se základním časem `2020-04-07 14:53:14Z` , je výstup:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| add3Years | Řetězec | 4/7/2023 2:53:14 ODP. |
| subtract9Days | Řetězec | 3/29/2020 2:53:14 ODP. |
| add1Hour | Řetězec | 4/7/2020 3:53:14 ODP. |

Následující příklad šablony ukazuje, jak nastavit počáteční čas pro plán automatizace.

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

Vrátí aktuální hodnotu DateTime (UTC) v zadaném formátu. Pokud není zadán žádný formát, bude použit formát ISO 8601 (yyyyMMddTHHmmssZ). **Tato funkce se dá použít jenom ve výchozí hodnotě pro parametr.**

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Description |
|:--- |:--- |:--- |:--- |
| formát |No |řetězec |Hodnota zakódovaná identifikátorem URI, která má být převedena na řetězec. Použijte buď [standardní formátovací řetězce](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [Vlastní řetězce formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Poznámky

Tuto funkci lze použít pouze v rámci výrazu pro výchozí hodnotu parametru. Použití této funkce kdekoli jinde v šabloně vrátí chybu. Funkce není povolena v ostatních částech šablony, protože vrací jinou hodnotu pokaždé, když je volána. Nasazení stejné šablony se stejnými parametry by nespolehlivě přineslo stejné výsledky.

Pokud použijete [možnost vrátit zpět při chybě](rollback-on-error.md) k dřívějšímu úspěšnému nasazení a předchozí nasazení zahrnuje parametr, který používá UtcNow, parametr není znovu vyhodnocen. Místo toho je hodnota parametru z dřívějšího nasazení automaticky znovu použita při nasazení zpět.

Buďte opatrní při opětovném nasazení šablony, která spoléhá na funkci utcNow, na výchozí hodnotu. Když znovu nasadíte a nezadáte hodnotu parametru, funkce se znovu vyhodnotí. Pokud chcete aktualizovat existující prostředek místo vytvoření nového prostředku, předejte hodnotu parametru z dřívějšího nasazení.

### <a name="return-value"></a>Vrácená hodnota

Aktuální hodnota DateTime UTC.

### <a name="examples"></a>Příklady

Následující příklad šablony ukazuje různé formáty hodnoty data a času.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Výstup z výše uvedeného příkladu se u každého nasazení liší, ale bude vypadat přibližně takto:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| utcOutput | řetězec | 20190305T175318Z |
| utcShortOutput | řetězec | 03/05/2019 |
| utcCustomOutput | řetězec | 3 5 |

Další příklad ukazuje, jak použít hodnotu z funkce při nastavení hodnoty značky.

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

## <a name="next-steps"></a>Další kroky

* Popis sekcí v šabloně Azure Resource Manager najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
