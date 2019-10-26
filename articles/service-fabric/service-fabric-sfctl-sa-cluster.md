---
title: Azure Service Fabric CLI – sfctl SA – cluster | Microsoft Docs
description: V této části najdete popis příkazů samostatného clusteru Service Fabric CLI sfctl.
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
ms.openlocfilehash: ecdd288d7cb320b91ab4c69697d334f8d9459e62
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901018"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Spravujte samostatné Service Fabric clustery.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| Konfigurace | Získejte konfiguraci samostatného clusteru Service Fabric. |
| konfigurace – upgrade | Spusťte upgrade konfigurace samostatného clusteru Service Fabric. |
| upgrade – stav | Získá stav upgradu konfigurace clusteru Service Fabric samostatného clusteru. |

## <a name="sfctl-sa-cluster-config"></a>sfctl SA – konfigurace clusteru
Získejte konfiguraci samostatného clusteru Service Fabric.

Konfigurace clusteru obsahuje vlastnosti clusteru, které zahrnují různé typy uzlů v clusteru, konfigurace zabezpečení, selhání a upgrady doménových topologií atd.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Configuration-API-Version [požadováno] | Verze rozhraní API samostatné konfigurace JSON clusteru |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl SA – konfigurace clusteru – upgrade
Spusťte upgrade konfigurace samostatného clusteru Service Fabric.

Ověřte zadané parametry upgradu konfigurace a spusťte upgrade konfigurace clusteru, pokud jsou parametry platné.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --cluster-config [požadováno] | Konfigurace clusteru. |
| --Application-Health-policies | Slovník kódovaný ve formátu JSON páry názvu typu aplikace a maximální procento není v pořádku před vyvoláním chyby. |
| --Delta-není v pořádku – uzly | Maximální povolené procento rozdílového snížení stavu během upgradu. Povolené hodnoty jsou celočíselné hodnoty od nuly do 100. |
| --Health-Check-opakování | Doba mezi pokusy o provedení kontrol stavu, pokud aplikace nebo cluster není v pořádku.  Výchozí\: PT0H0M0S. |
| --Health-Check-stabilní | Doba, po kterou musí aplikace nebo cluster zůstat v pořádku, než bude upgrade pokračovat na další upgradovací doménu.  Výchozí\: PT0H0M0S. <br><br> Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. |
| --Health-Check-Wait | Doba, po kterou se má po dokončení upgradu domény počkat, než se spustí proces kontroly stavu.  Výchozí\: PT0H0M0S. |
| --Timeout-t | Výchozí\: 60. |
| --není v pořádku – aplikace | Maximální povolené procento nezdravých aplikací během upgradu. Povolené hodnoty jsou celočíselné hodnoty od nuly do 100. |
| – není v pořádku – uzly | Maximální povolené procento uzlů, které nejsou v pořádku během upgradu. Povolené hodnoty jsou celočíselné hodnoty od nuly do 100. |
| --upgrade-Domain-rozdíl-není v pořádku – uzly | Maximální povolené procento rozdílového snížení stavu domény upgradu během upgradu. Povolené hodnoty jsou celočíselné hodnoty od nuly do 100. |
| --upgrade-Domain – vypršel časový limit. | Doba, po kterou musí být každá upgradovací doména dokončena před provedením FailureAction.  Výchozí\: PT0H0M0S. <br><br> Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. |
| --upgrade – časový limit | Doba, po kterou musí být celkový upgrade dokončen před provedením FailureAction.  Výchozí\: PT0H0M0S. <br><br> Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

### <a name="examples"></a>Příklady

Spuštění aktualizace konfigurace clusteru
``` 
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-    
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"   
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl SA – upgrade clusteru – stav
Získá stav upgradu konfigurace clusteru Service Fabric samostatného clusteru.

Podrobnosti o stavu upgradu konfigurace clusteru pro samostatný cluster Service Fabric

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty\: JSON, jsonc, Table, TSV.  Výchozí\: JSON. |
| --dotaz | Řetězec dotazu JMESPath Další informace a příklady najdete v tématu http\://jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).