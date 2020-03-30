---
title: Kontejner CLI- sfcTL azure service fabric
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro kontejnery.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 07861e2b67eea67740b341cbea994de978973664
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906110"
---
# <a name="sfctl-container"></a>sfctl container
Spusťte příkazy související s kontejnery v uzlu clusteru.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| vyvolat-api | Vyvolat rozhraní API kontejneru na kontejneru nasazeném v uzlu Service Fabric pro daný balíček kódu. |
| Protokoly | Získá protokoly kontejneru pro kontejner nasazený v uzlu Service Fabric. |

## <a name="sfctl-container-invoke-api"></a>sfctl kontejner invoke-api
Vyvolat rozhraní API kontejneru na kontejneru nasazeném v uzlu Service Fabric pro daný balíček kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. <br><br> Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --code-package-instance-id [Povinné] | ID, které jednoznačně identifikuje instanci balíčku kódu nasazenou v uzlu prostředků infrastruktury služby. <br><br> Lze načíst pomocí 'service code-package-list'. |
| --code-package-name [Povinné] | Název balíčku kódu zadaný v manifestu služby registrovaný jako součást typu aplikace v clusteru Service Fabric. |
| --container-api-uri-path [Povinné] | Cesta URI rozhraní API kontejneru, místo názvu/id kontejneru použijte {ID}. |
| --název uzlu [Povinné] | Název uzlu |
| --service-manifest-name [Povinné] | Název manifestu služby registrovaný jako součást typu aplikace v clusteru Service Fabric. |
| --container-api-body --container-api-body --container-api-body --container | Tělo požadavku HTTP pro rozhraní REST API kontejneru. |
| --container-api-content-type | Typ obsahu pro rozhraní REST API kontejneru, výchozí hodnota "application/json". |
| --container-api-http-verb | HTTP sloveso pro rozhraní REST API kontejneru, výchozí hodnoty GET. |
| --časový čas -t | Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-container-logs"></a>protokoly kontejnerů sfctl
Získá protokoly kontejneru pro kontejner nasazený v uzlu Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. <br><br> Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --code-package-instance-id [Povinné] | ID instance balíčku kódu, které lze načíst pomocí "seznamu balíčků balíčků služby". |
| --code-package-name [Povinné] | Název balíčku kódu zadaný v manifestu služby registrovaný jako součást typu aplikace v clusteru Service Fabric. |
| --název uzlu [Povinné] | Název uzlu |
| --service-manifest-name [Povinné] | Název manifestu služby registrovaný jako součást typu aplikace v clusteru Service Fabric. |
| --ocas | Počet řádků, které mají být zobrazovány z konce protokolů. Výchozí hodnota je 100. 'all' pro zobrazení úplných protokolů. |
| --časový čas -t | Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) cli service fabric.
- Naučte se používat funkce CLI service fabric pomocí [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).