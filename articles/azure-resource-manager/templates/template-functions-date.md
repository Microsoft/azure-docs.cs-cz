---
title: Šablony – funkce – datum
description: Popisuje funkce, které se použijí v šabloně Azure Resource Manager (šablona ARM) pro práci s daty.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 58d865f109ecca2629b89eeb55e554743824c195
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96920499"
---
# <a name="date-functions-for-arm-templates"></a>Funkce data pro šablony ARM

Správce prostředků poskytuje následující funkce pro práci s daty v šabloně Azure Resource Manager (šablona ARM):

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Přidá do základní hodnoty časový interval. Očekává se formát ISO 8601.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Description |
|:--- |:--- |:--- |:--- |
| base | Yes | řetězec | Počáteční hodnota DateTime pro sčítání. Použijte [Formát časového razítka ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
| doba trvání | Yes | řetězec | Hodnota času, která se má přidat do základu Může se jednat o zápornou hodnotu. Použijte [Formát doby trvání ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| formát | No | řetězec | Formát výstupu pro výsledek data a času. Pokud není zadaný, použije se formát základní hodnoty. Použijte buď [standardní formátovací řetězce](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [Vlastní řetězce formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Vrácená hodnota

Hodnota DateTime, která je výsledkem přidání hodnoty Duration k základní hodnotě.

### <a name="examples"></a>Příklady

Následující příklad šablony ukazuje různé způsoby přidávání časových hodnot.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "baseTime": {
      "type": "string",
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
    "add3YearsOutput": {
      "value": "[variables('add3Years')]",
      "type": "string"
    },
    "subtract9DaysOutput": {
      "value": "[variables('subtract9Days')]",
      "type": "string"
    },
    "add1HourOutput": {
      "value": "[variables('add1Hour')]",
      "type": "string"
    },
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param baseTime string = utcNow('u')

var add3Years = dateTimeAdd(baseTime, 'P3Y')
var subtract9Days = dateTimeAdd(baseTime, '-P9D')
var add1Hour = dateTimeAdd(baseTime, 'PT1H')

output add3YearsOutput string = add3Years
output subtract9DaysOutput string = subtract9Days
output add1HourOutput string = add1Hour
```

---

Je-li předchozí šablona nasazena se základním časem `2020-04-07 14:53:14Z` , je výstup:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| add3YearsOutput | Řetězec | 4/7/2023 2:53:14 ODP. |
| subtract9DaysOutput | Řetězec | 3/29/2020 2:53:14 ODP. |
| add1HourOutput | Řetězec | 4/7/2020 3:53:14 ODP. |

Následující příklad šablony ukazuje, jak nastavit počáteční čas pro plán automatizace.

# <a name="json"></a>[JSON](#tab/json)

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
    "baseTime": {
      "type": "string",
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
    ...
    {
      "type": "Microsoft.Automation/automationAccounts/schedules",
      "apiVersion": "2015-10-31",
      "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",

      "properties": {
        "description": "Demo Scheduler",
        "startTime": "[variables('startTime')]",
        "interval": 1,
        "frequency": "Hour"
      }
    }
  ],
  "outputs": {
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param omsAutomationAccountName string = 'demoAutomation'
param scheduleName string = 'demSchedule1'
param baseTime string = utcNow('u')

var startTime = dateTimeAdd(baseTime, 'PT1H')

...

resource scheduler 'Microsoft.Automation/automationAccounts/schedules@2015-10-31' = {
  name: concat(omsAutomationAccountName, '/', scheduleName)
  properties: {
    description: 'Demo Scheduler'
    startTime: startTime
    interval: 1
    frequency: 'Hour'
  }
}
```

---

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Vrátí aktuální hodnotu DateTime (UTC) v zadaném formátu. Pokud není zadán žádný formát, použije se formát ISO 8601 ( `yyyyMMddTHHmmssZ` ). **Tato funkce se dá použít jenom ve výchozí hodnotě pro parametr.**

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Description |
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

# <a name="json"></a>[JSON](#tab/json)

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param utcValue string = utcNow()
param utcShortValue string = utcNow('d')
param utcCustomValue string = utcNow('M d')

output utcOutput string = utcValue
output utcShortOutput string = utcShortValue
output utcCustomOutput string = utcCustomValue
```

---

Výstup z výše uvedeného příkladu se u každého nasazení liší, ale bude vypadat přibližně takto:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| utcOutput | řetězec | 20190305T175318Z |
| utcShortOutput | řetězec | 03/05/2019 |
| utcCustomOutput | řetězec | 3 5 |

Další příklad ukazuje, jak použít hodnotu z funkce při nastavení hodnoty značky.

# <a name="json"></a>[JSON](#tab/json)

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
      "tags": {
        "createdDate": "[parameters('utcShort')]"
      },
      "properties": {}
    }
  ],
  "outputs": {
    "utcShortOutput": {
      "type": "string",
      "value": "[parameters('utcShort')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param utcShort string = utcNow('d')
param rgName string

resource myRg 'Microsoft.Resources/resourceGroups@2018-05-01' = {
  name: rgName
  location: 'westeurope'
  tags: {
    createdDate: utcShort
  }
}

output utcShortOutput string = utcShort
```

---

## <a name="next-steps"></a>Další kroky

* Popis sekcí v šabloně ARM najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
