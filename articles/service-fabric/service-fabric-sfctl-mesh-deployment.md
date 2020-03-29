---
title: Nasazení sítě Azure Service Fabric CLI- sfctl
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro vytváření prostředků sítě Service Fabric.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 108389407221779ed20e81310f084b7b5c23b8c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906037"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Vytvořte prostředky sítě Service Fabric.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| vytvoření | Vytvoří nasazení prostředků sítě Service Fabric. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl vytvoření síťového nasazení
Vytvoří nasazení prostředků sítě Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --input-yaml-files [Povinné] | Relativní nebo absolutní cesty souborů oddělené čárkami všech souborů yaml nebo relativní nebo absolutní cesty adresáře (rekurzivní), které obsahují soubory yaml. |
| --parametry | Relativní nebo absolutní cesta k souboru yaml nebo objektu json, který obsahuje parametry, které je třeba přepsat. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

### <a name="examples"></a>Příklady

Konsoliduje a nasazuje všechny prostředky do clusteru přepsáním parametrů uvedených v souboru yaml.
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

Konsoliduje a nasazuje všechny prostředky v adresáři do clusteru přepsáním parametrů uvedených v souboru yaml.

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Konsoliduje a nasazuje všechny prostředky v adresáři do clusteru přepsáním parametrů, které jsou předány přímo jako objekt json.
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) cli service fabric.
- Naučte se používat funkce CLI service fabric pomocí [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).
