---
title: Azure Service Fabric CLI- sfctl sa-cluster
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu samostatných clusterů.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 233148c04fb1a9c6cf1d6c7042c12c54eebd0205
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904924"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Spravujte samostatné clustery Service Fabric.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| config | Získejte konfiguraci samostatného clusteru Service Fabric. |
| config-upgrade | Začněte inovovat konfiguraci samostatného clusteru Service Fabric. |
| stav upgradu | Získejte stav upgradu konfigurace clusteru samostatného clusteru Service Fabric. |

## <a name="sfctl-sa-cluster-config"></a>konfigurace sfctl sa-clusteru
Získejte konfiguraci samostatného clusteru Service Fabric.

Konfigurace clusteru obsahuje vlastnosti clusteru, které zahrnují různé typy uzlů v clusteru, konfigurace zabezpečení, selhání a topologie domény upgradu atd.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --configuration-api-version [Povinné] | Verze rozhraní API konfigurace json samostatného clusteru. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config-upgrade
Začněte inovovat konfiguraci samostatného clusteru Service Fabric.

Ověřte zadané parametry upgradu konfigurace a začněte inovovat konfiguraci clusteru, pokud jsou parametry platné.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --cluster-config [Povinné] | Konfigurace clusteru. |
| --zásady stavu aplikace | JSON kódované slovník párů názvu typu aplikace a maximální procento není v pořádku před vyvolání chyby. |
| --delta-unhealthy-uzly | Maximální povolené procento snížení rozdílového stavu během upgradu. Povolené hodnoty jsou celé hodnoty od nuly do 100. |
| --retry kontroly stavu | Doba mezi pokusy o provedení kontroly stavu, pokud aplikace nebo cluster není v pořádku.  Výchozí\: PT0H0M0S. |
| --zdravotní kontrola-stabilní | Doba, po kterou musí aplikace nebo cluster zůstat v pořádku, než upgrade přejde na další doménu upgradu.  Výchozí\: PT0H0M0S. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. |
| --kontrola stavu-čekání | Doba čekání po dokončení upgradu domény před zahájením procesu kontroly stavu.  Výchozí\: PT0H0M0S. |
| --časový čas -t | Výchozí\: 60. |
| --nezdravé aplikace | Maximální povolené procento nefunkčních aplikací během inovace. Povolené hodnoty jsou celé hodnoty od nuly do 100. |
| --nezdravé uzly | Maximální povolené procento uzlů není v pořádku během upgradu. Povolené hodnoty jsou celé hodnoty od nuly do 100. |
| --upgrade-domain-delta-unhealthy-uzly --upgrade-domain-delta-unhealthy-nodes --upgrade-domain-delta-unhealthy-nodes -- | Maximální povolené procento snížení rozdílového stavu domény upgradu během upgradu. Povolené hodnoty jsou celé hodnoty od nuly do 100. |
| --upgrade-domain-timeout | Doba, po kterou musí být každá upgradovací doména dokončena před provedením akce FailureAction.  Výchozí\: PT0H0M0S. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. |
| --upgrade-timeout | Doba, po kterou má být dokončena celková inovace, než je provedena akce Selhání.  Výchozí\: PT0H0M0S. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

### <a name="examples"></a>Příklady

Spuštění aktualizace konfigurace clusteru
```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster upgrade-status
Získejte stav upgradu konfigurace clusteru samostatného clusteru Service Fabric.

Získejte podrobnosti o stavu upgradu konfigurace clusteru samostatného clusteru Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

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