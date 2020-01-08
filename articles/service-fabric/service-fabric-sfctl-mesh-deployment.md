---
title: Nasazení sítě Azure Service Fabric CLI – sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro vytvoření Service Fabricch prostředků sítě.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 6512cce075906fc8708a39fa0747ec27be33e961
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645357"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Vytvořte Service Fabric prostředky sítě.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| vytvoření | Vytvoří nasazení Service Fabricch prostředků sítě. |

## <a name="sfctl-mesh-deployment-create"></a>vytvoření nasazení sítě sfctl
Vytvoří nasazení Service Fabricch prostředků sítě.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Input-YAML-Files [povinné] | Relativní/absolutní cesty k souborům YAML nebo relativní/absolutní cesta k adresáři (rekurzivní), který obsahuje soubory YAML, oddělených čárkami. |
| --parametry | Relativní nebo absolutní cesta k souboru YAML nebo objektu JSON, který obsahuje parametry, které je třeba přepsat. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

### <a name="examples"></a>Příklady

Konsoliduje a nasadí všechny prostředky do clusteru přepsáním parametrů uvedených v souboru YAML.
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

Konsoliduje a nasadí všechny prostředky v adresáři do clusteru přepsáním parametrů uvedených v souboru YAML.

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Konsoliduje a nasadí všechny prostředky v adresáři do clusteru přepsáním parametrů, které se předávají přímo jako objekt JSON.
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).