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
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 27108d27ee27346e4cba44e6778faff56df70a36
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495124"
---
# <a name="sfctl-container"></a>sfctl container
Spuštění kontejneru související s příkazy v uzlu clusteru.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| volání rozhraní api | Vyvolejte kontejner REST API. |
| Protokoly | Načítání protokoly kontejneru. |

## <a name="sfctl-container-invoke-api"></a>vyvolat kontejner sfctl-api
Vyvolejte kontejner REST API.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identita aplikace. |
| – Kód –-instance-id balíčku [povinné] | ID instance balíček kódu, která se dá načíst pomocí "service code-package-list". |
| – Kód balíčku název-[povinné] | Název balíčku kódu. |
| --– rozhraní api-uri cesta kontejneru [povinné] | Cesta identifikátoru URI rozhraní API REST kontejneru, použijte {id}' místo názvu nebo id kontejneru. |
| --[povinný] název uzlu | Název uzlu. |
| --service-manifest-name [povinné] | Název manifestu služby. |
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
Načítání protokoly kontejneru.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identita aplikace. |
| – Kód –-instance-id balíčku [povinné] | ID instance balíček kódu, která se dá načíst pomocí "service code-package-list". |
| – Kód balíčku název-[povinné] | Název balíčku kódu. |
| --[povinný] název uzlu | Název uzlu. |
| --service-manifest-name [povinné] | Název manifestu služby. |
| --Funkce tail | Vrátíte pouze tento počet řádků protokolu od konce protokoly. Zadejte jako celé číslo nebo všechny výstup všech řádků protokolu. Výchozí nastavení pro všechny. |
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