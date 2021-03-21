---
title: Funkce šablon – any
description: Popisuje všechny funkce, které jsou k dispozici v bicep pro převod typů.
ms.topic: conceptual
author: tfitzmac
ms.author: tomfitz
ms.service: azure-resource-manager
ms.subservice: templates
ms.date: 03/02/2021
ms.openlocfilehash: b0cb51c9a79d1100de7f1ef32fe326eddcdd6dcc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744960"
---
# <a name="any-function-for-bicep"></a>Jakákoli funkce pro bicep

Bicep podporuje funkci volanou `any()` k řešení chyb typu v systému typů bicep. Tuto funkci použijete, pokud formát hodnoty, kterou zadáte, neodpovídá tomu, co systém typů očekává. Například pokud vlastnost vyžaduje číslo, ale je třeba ji zadat jako řetězec, například `'0.5'` . Pomocí `any()` funkce potlačíte chybu hlášenou systémem typů.

Tato funkce neexistuje v modulu runtime šablony Azure Resource Manager. Používá ho jenom bicep a nevysílá se v kódu JSON pro vytvořenou šablonu.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="any"></a>Libovolný

`any(value)`

Vrátí hodnotu, která je kompatibilní s jakýmkoli datovým typem.

### <a name="parameters"></a>Parametry

| Parametr | Povinné | Typ | Description |
|:--- |:--- |:--- |:--- |
| hodnota | Yes | všechny typy | Hodnota, která má být převedena na kompatibilní typ. |

### <a name="return-value"></a>Vrácená hodnota

Hodnota ve formuláři, která je kompatibilní s jakýmkoli datovým typem.

### <a name="examples"></a>Příklady

Následující příklad šablony ukazuje, jak použít `any()` funkci k poskytnutí číselných hodnot jako řetězců.

```bicep
resource wpAci 'microsoft.containerInstance/containerGroups@2019-12-01' = {
  name: 'wordpress-containerinstance'
  location: location
  properties: {
    containers: [
      {
        name: 'wordpress'
        properties: {
          ...
          resources: {
            requests: {
              cpu: any('0.5')
              memoryInGB: any('0.7')
            }
          }
        }
      }
    ]
  }
}
```

Funkce funguje na jakékoli přiřazené hodnotě v bicep. Následující příklad používá `any()` s výrazem Ternární jako argument.  

```bicep
publicIPAddress: any((pipId == '') ? null : {
  id: pipId
})
```

## <a name="next-steps"></a>Další kroky

Složitější použití `any()` funkce naleznete v následujících příkladech:

* [Podřízené prostředky, které vyžadují konkrétní názvy](https://github.com/Azure/bicep/blob/main/docs/examples/201/api-management-create-all-resources/main.bicep#L246)
* [Vlastnost prostředku není definována v typu prostředku, i když existuje.](https://github.com/Azure/bicep/blob/main/docs/examples/201/log-analytics-with-solutions-and-diagnostics/main.bicep#L26)

