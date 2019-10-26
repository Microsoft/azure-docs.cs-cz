---
title: Azure Service Fabric CLI – nasazení sítě sfctl | Microsoft Docs
description: Popisuje příkazy Service Fabric pro nasazení sítě sfctl CLI.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: dbecf8e96c6cb5d0d6d873309d848304fe33eaaa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901252"
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