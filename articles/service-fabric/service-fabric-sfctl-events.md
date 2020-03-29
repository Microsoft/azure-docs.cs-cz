---
title: Události CLI- sfcTL azure service fabric
description: Popisuje příkazy událostí sfctl služby Fabric.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906107"
---
# <a name="sfctl-events"></a>Akce sfctl
Načtěte události z úložiště událostí (pokud je služba EventStore již nainstalována).

Systémovou službu EventStore lze přidat prostřednictvím upgradu konfigurace do libovolného clusteru SFRP se systémem >=6.4. Zkontrolujte prosím\: následující\:url https //docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| seznam všech aplikací | Získá všechny události související s aplikacemi. |
| seznam všech uzlů | Získá všechny události související s uzly. |
| seznam všech oddílů | Získá všechny události související s oddíly. |
| seznam všech služeb | Získá všechny události související se službami. |
| seznam aplikací | Získá události související s aplikací. |
| seznam clusterů | Získá všechny události související s clusterem. |
| seznam uzlů | Získá události související s uzly. |
| seznam všech replik | Získá všechny události související s replikami pro oddíl. |
| seznam oddílů | Získá události související s oddílem. |
| seznam replik oddílů | Získá události související s replikou oddílu. |
| seznam služeb | Získá události související se službou. |

## <a name="sfctl-events-all-applications-list"></a>sfctl události all-aplikace-list
Získá všechny události související s aplikacemi.

Odpověď je seznam objektů ApplicationEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --end-time-utc [Povinné] | Čas ukončení vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Povinné] | Čas spuštění vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Toto je řetězec oddělený čárkou určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --exclude-analysis-events | Tento param zakáže načítání AnalysisEvents, pokud true je předán. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup --skip | Tento param zakáže hledání KorerelatedEvents informace, pokud true je předán. jinak correlationEvents získat zpracovány a HasCorrelatedEvents pole v každém FabricEvent získá naplněna. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-events-all-nodes-list"></a>sfctl události all-udes-list
Získá všechny události související s uzly.

Odpověď je seznam objektů NodeEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --end-time-utc [Povinné] | Čas ukončení vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Povinné] | Čas spuštění vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Toto je řetězec oddělený čárkou určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --exclude-analysis-events | Tento param zakáže načítání AnalysisEvents, pokud true je předán. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup --skip | Tento param zakáže hledání KorerelatedEvents informace, pokud true je předán. jinak correlationEvents získat zpracovány a HasCorrelatedEvents pole v každém FabricEvent získá naplněna. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-events-all-partitions-list"></a>sfctl události all-partitions-list
Získá všechny události související s oddíly.

Odpověď je seznam objektů PartitionEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --end-time-utc [Povinné] | Čas ukončení vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Povinné] | Čas spuštění vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Toto je řetězec oddělený čárkou určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --exclude-analysis-events | Tento param zakáže načítání AnalysisEvents, pokud true je předán. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup --skip | Tento param zakáže hledání KorerelatedEvents informace, pokud true je předán. jinak correlationEvents získat zpracovány a HasCorrelatedEvents pole v každém FabricEvent získá naplněna. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-events-all-services-list"></a>sfctl události all-services-list
Získá všechny události související se službami.

Odpověď je seznam objektů ServiceEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --end-time-utc [Povinné] | Čas ukončení vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Povinné] | Čas spuštění vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Toto je řetězec oddělený čárkou určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --exclude-analysis-events | Tento param zakáže načítání AnalysisEvents, pokud true je předán. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup --skip | Tento param zakáže hledání KorerelatedEvents informace, pokud true je předán. jinak correlationEvents získat zpracovány a HasCorrelatedEvents pole v každém FabricEvent získá naplněna. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-events-application-list"></a>sfctl události seznam aplikací
Získá události související s aplikací.

Odpověď je seznam objektů ApplicationEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --end-time-utc [Povinné] | Čas ukončení vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Povinné] | Čas spuštění vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Toto je řetězec oddělený čárkou určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --exclude-analysis-events | Tento param zakáže načítání AnalysisEvents, pokud true je předán. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup --skip | Tento param zakáže hledání KorerelatedEvents informace, pokud true je předán. jinak correlationEvents získat zpracovány a HasCorrelatedEvents pole v každém FabricEvent získá naplněna. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-events-cluster-list"></a>sfctl události cluster-list
Získá všechny události související s clusterem.

Odpověď je seznam objektů ClusterEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --end-time-utc [Povinné] | Čas ukončení vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Povinné] | Čas spuštění vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Toto je řetězec oddělený čárkou určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --exclude-analysis-events | Tento param zakáže načítání AnalysisEvents, pokud true je předán. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup --skip | Tento param zakáže hledání KorerelatedEvents informace, pokud true je předán. jinak correlationEvents získat zpracovány a HasCorrelatedEvents pole v každém FabricEvent získá naplněna. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-events-node-list"></a>sfctl události seznam uzlů
Získá události související s uzly.

Odpověď je seznam objektů NodeEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --end-time-utc [Povinné] | Čas ukončení vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --název uzlu [Povinné] | Název uzlu |
| --start-time-utc [Povinné] | Čas spuštění vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Toto je řetězec oddělený čárkou určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --exclude-analysis-events | Tento param zakáže načítání AnalysisEvents, pokud true je předán. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup --skip | Tento param zakáže hledání KorerelatedEvents informace, pokud true je předán. jinak correlationEvents získat zpracovány a HasCorrelatedEvents pole v každém FabricEvent získá naplněna. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl události partition-all-replicas-list
Získá všechny události související s replikami pro oddíl.

Odpověď je seznam objektů ReplicaEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --end-time-utc [Povinné] | Čas ukončení vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --partition-id [Povinné] | Identita oddílu. |
| --start-time-utc [Povinné] | Čas spuštění vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Toto je řetězec oddělený čárkou určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --exclude-analysis-events | Tento param zakáže načítání AnalysisEvents, pokud true je předán. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup --skip | Tento param zakáže hledání KorerelatedEvents informace, pokud true je předán. jinak correlationEvents získat zpracovány a HasCorrelatedEvents pole v každém FabricEvent získá naplněna. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-events-partition-list"></a>sfctl události seznam oddílů
Získá události související s oddílem.

Odpověď je seznam objektů PartitionEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --end-time-utc [Povinné] | Čas ukončení vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --partition-id [Povinné] | Identita oddílu. |
| --start-time-utc [Povinné] | Čas spuštění vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Toto je řetězec oddělený čárkou určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --exclude-analysis-events | Tento param zakáže načítání AnalysisEvents, pokud true je předán. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup --skip | Tento param zakáže hledání KorerelatedEvents informace, pokud true je předán. jinak correlationEvents získat zpracovány a HasCorrelatedEvents pole v každém FabricEvent získá naplněna. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-events-partition-replica-list"></a>sfctl události partition-replica-list
Získá události související s replikou oddílu.

Odpověď je seznam objektů ReplicaEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --end-time-utc [Povinné] | Čas ukončení vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --partition-id [Povinné] | Identita oddílu. |
| --replika-id [Povinné] | Identifikátor repliky. |
| --start-time-utc [Povinné] | Čas spuštění vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Toto je řetězec oddělený čárkou určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --exclude-analysis-events | Tento param zakáže načítání AnalysisEvents, pokud true je předán. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup --skip | Tento param zakáže hledání KorerelatedEvents informace, pokud true je předán. jinak correlationEvents získat zpracovány a HasCorrelatedEvents pole v každém FabricEvent získá naplněna. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-events-service-list"></a>sfctl události seznam služeb
Získá události související se službou.

Odpověď je seznam objektů ServiceEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --end-time-utc [Povinné] | Čas ukončení vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --service-id [Povinné] | Identita služby. Toto ID je obvykle úplný název služby\:bez schématu URI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název služby\:je "fabric /myapp/app1/svc1", identita\~služby\~by "myapp app1 svc1" v 6.0+ a "myapp/app1/svc1" v předchozích verzích. |
| --start-time-utc [Povinné] | Čas spuštění vyhledávacího dotazu v ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --events-types-filter | Toto je řetězec oddělený čárkou určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --exclude-analysis-events | Tento param zakáže načítání AnalysisEvents, pokud true je předán. |
| --skip-correlation-lookup --skip-correlation-lookup --skip-correlation-lookup --skip | Tento param zakáže hledání KorerelatedEvents informace, pokud true je předán. jinak correlationEvents získat zpracovány a HasCorrelatedEvents pole v každém FabricEvent získá naplněna. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

