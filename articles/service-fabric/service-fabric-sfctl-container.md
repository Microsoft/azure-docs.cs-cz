---
title: Kontejner Azure Service Fabric rozhraní příkazového řádku - sfctl | Microsoft Docs
description: Popisuje příkazy Service Fabric rozhraní příkazového řádku sfctl kontejneru.
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
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: cd3725ac547a1ed1fd9207dc48ba3b6227e85ef1
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34764002"
---
# <a name="sfctl-container"></a>sfctl kontejneru
Spusťte kontejner související příkazy na uzlu clusteru.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| volání rozhraní api | Vyvolání kontejneru REST API. |
| Protokoly | Načítání protokoly kontejneru. |

## <a name="sfctl-container-invoke-api"></a>vyvolání kontejneru sfctl-api
Vyvolání kontejneru REST API.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. |
| --kódu balíček instance-id [vyžaduje] | ID instance balíček kódu, které může načíst "service kódu balíček – seznam". |
| --kódu balíček name [vyžaduje] | Název balíčku kódu. |
| ---api-uri cesta kontejneru [vyžaduje] | Cesta kontejneru URI rozhraní API REST, použití {ID} místo názvu nebo id kontejneru. |
| – Název uzlu [vyžaduje] | Název uzlu. |
| ---manifest – název služby [vyžaduje] | Název manifestu služby. |
| --kontejneru api textu | Datová část požadavku HTTP pro kontejner REST API. |
| --kontejneru api-content-type | Typ obsahu pro kontejner rozhraní REST API, výchozí hodnota je "application/json". |
| --kontejneru rozhraní api http operace | Akce HTTP pro kontejner rozhraní REST API, použije se výchozí hodnota GET. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-container-logs"></a>protokoly sfctl kontejneru
Načítání protokoly kontejneru.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. |
| --kódu balíček instance-id [vyžaduje] | ID instance balíček kódu, které může načíst "service kódu balíček – seznam". |
| --kódu balíček name [vyžaduje] | Název balíčku kódu. |
| – Název uzlu [vyžaduje] | Název uzlu. |
| ---manifest – název služby [vyžaduje] | Název manifestu služby. |
| --tail | Vrátíte pouze tento počet řádků protokolu od konce protokoly. Zadejte celé číslo nebo všechny k vypsání všech řádků protokolu. Výchozí nastavení pro všechny. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="next-steps"></a>Další postup
- [Nastavit](service-fabric-cli.md) rozhraní příkazového řádku služby prostředků infrastruktury.
- Další informace o použití pomocí Service Fabric rozhraní příkazového řádku [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).