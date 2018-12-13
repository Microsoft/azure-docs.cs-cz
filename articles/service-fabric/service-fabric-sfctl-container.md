---
title: Azure Service Fabric CLI sfctl kontejner | Dokumentace Microsoftu
description: Popisuje příkazy Service Fabric CLI sfctl kontejneru.
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
ms.openlocfilehash: 455b2a70568566bff5b1ea4c185568a1758f7db3
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274900"
---
# <a name="sfctl-container"></a>sfctl container
Spuštění kontejneru související s příkazy v uzlu clusteru.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| volání rozhraní api | Vyvolání kontejneru rozhraní API v kontejneru nasadit na uzlu Service Fabricu pro balíček daného kódu. |
| Protokoly | Získá kontejner protokoly pro kontejner nasadit na uzlu Service Fabricu pro balíček daného kódu. |

## <a name="sfctl-container-invoke-api"></a>vyvolat kontejner sfctl-api
Vyvolání kontejneru rozhraní API v kontejneru nasadit na uzlu Service Fabricu pro balíček daného kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. <br><br> Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| – Kód –-instance-id balíčku [povinné] | ID, které jednoznačně identifikuje instanci balíčku kód nasazen na uzel service fabric. <br><br> Je možné načíst podle "service code-package-list". |
| – Kód balíčku název-[povinné] | Název zadaný v manifestu služby, které jsou zapsaní v rámci typu aplikace v clusteru Service Fabric balíček kódu. |
| --– rozhraní api-uri cesta kontejneru [povinné] | Cesta identifikátoru URI rozhraní API REST kontejneru, použijte {id}' místo názvu nebo id kontejneru. |
| --[povinný] název uzlu | Název uzlu. |
| --service-manifest-name [povinné] | Název manifestu služby zapsaní v rámci typu aplikace v clusteru Service Fabric. |
| --container-api-body | Datová část požadavku HTTP pro kontejner REST API. |
| --container-api-content-type | Typ obsahu pro kontejner REST API, výchozí hodnota je "application/json". |
| --container rozhraní api protokolu http příkaz | Příkaz HTTP pro kontejner REST API, výchozí hodnota je GET. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-container-logs"></a>sfctl protokoly kontejneru
Získá kontejner protokoly pro kontejner nasadit na uzlu Service Fabricu pro balíček daného kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. <br><br> Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| – Kód –-instance-id balíčku [povinné] | ID instance balíček kódu, která se dá načíst pomocí "service code-package-list". |
| – Kód balíčku název-[povinné] | Název zadaný v manifestu služby, které jsou zapsaní v rámci typu aplikace v clusteru Service Fabric balíček kódu. |
| --[povinný] název uzlu | Název uzlu. |
| --service-manifest-name [povinné] | Název manifestu služby zapsaní v rámci typu aplikace v clusteru Service Fabric. |
| --Funkce tail | Počet řádků k zobrazení od konce protokoly. Výchozí hodnota je 100. 'všechny zobrazíte kompletní protokoly. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |


## <a name="next-steps"></a>Další postup
- [Nastavit](service-fabric-cli.md) Service Fabric CLI.
- Další informace o použití nástroje příkazového řádku Service Fabric pomocí [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).