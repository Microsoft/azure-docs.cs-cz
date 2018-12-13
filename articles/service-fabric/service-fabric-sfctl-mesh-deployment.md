---
title: Azure Service Fabric CLI - sfctl síť nasazení | Dokumentace Microsoftu
description: Popisuje příkazy Service Fabric CLI sfctl síť nasazení.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: b25384d8f3c6e41b6c5cca723d41b79f00b17494
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285442"
---
# <a name="sfctl-mesh-deployment"></a>nasazení sítě sfctl
Vytvoření sítě pro Service Fabric prostředků.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| vytvoření | Vytvoří nasazení mřížky prostředků služby Service Fabric. |

## <a name="sfctl-mesh-deployment-create"></a>Vytvoření nasazení sítě sfctl
Vytvoří nasazení mřížky prostředků služby Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --input-yaml – soubory [povinné] | Exportovaná relativní nebo absolutní cesty k souborům yaml soubory nebo relativní nebo absolutní cestu k adresáři (rekurzivní) obsahujících soubory yaml. |
| --Parametry | Relativní nebo absolutní cesta k souboru yaml nebo objekt json, který obsahuje parametry, které je potřeba přepsat. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

### <a name="examples"></a>Příklady

Konsoliduje a nasadí všechny prostředky do clusteru tak, že přepíšete parametry uvedené v souboru yaml

```
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters
./param.yaml
```

Konsoliduje a všechny prostředky v adresáři nasadí do clusteru tak, že přepíšete parametry uvedené v souboru yaml

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Konsoliduje a všechny prostředky v adresáři nasadí do clusteru tak, že přepíšete parametry, které jsou předány přímo jako objekt json

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :
{'value' : 'my_value'} }"
```


## <a name="next-steps"></a>Další postup
- [Nastavit](service-fabric-cli.md) Service Fabric CLI.
- Další informace o použití nástroje příkazového řádku Service Fabric pomocí [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).