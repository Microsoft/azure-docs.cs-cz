---
title: Nasazení sítě Azure Service Fabric CLI – sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro vytvoření Service Fabricch prostředků sítě.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fb2adafab88eb1d3855cdec8268601fb4e15dcbb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86257267"
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

|Argument|Description|
| --- | --- |
| --Input-YAML-Files [povinné] | Relativní nebo absolutní cesty k souborům YAML nebo relativní nebo absolutní cesta k adresáři (rekurzivní), který obsahuje soubory YAML, oddělených čárkami. |
| --parametry | Relativní nebo absolutní cesta k souboru YAML nebo objektu JSON, který obsahuje parametry, které je třeba přepsat. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
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
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](./scripts/sfctl-upgrade-application.md).
