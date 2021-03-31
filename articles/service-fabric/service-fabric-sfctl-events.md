---
title: Sfctl události CLI – Azure Service Fabric
description: Popisuje příkazy Service Fabric CLI sfctl Events.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "76906107"
---
# <a name="sfctl-events"></a>události sfctl
Načtěte události z úložiště událostí (Pokud je už nainstalovaná služba Eventstoru).

Systémovou službu Eventstoru lze přidat prostřednictvím upgradu konfigurace na libovolný cluster SFRP se systémem >= 6,4. Zkontrolujte prosím následující adresu URL \: https \: //docs.Microsoft.com/Azure/Service-Fabric/Service-Fabric-Diagnostics-eventstore.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| všechny aplikace – seznam | Načte všechny události související s aplikacemi. |
| všechny uzly – seznam | Načte všechny události související s uzly. |
| všechny oddíly – seznam | Načte všechny události související s oddíly. |
| všechny služby – seznam | Načte všechny události související se službami. |
| seznam aplikací | Získá události související s aplikací. |
| seznam clusterů | Načte všechny události související s clusterem. |
| seznam uzlů | Načte události související s uzlem. |
| oddíl – všechny repliky – seznam | Načte všechny události související s replikami pro oddíl. |
| Seznam oddílů | Získá události související s oddíly. |
| oddíl-Replica-list | Získá události související s replikou oddílu. |
| seznam služeb | Načte události související se službou. |

## <a name="sfctl-events-all-applications-list"></a>sfctl události všechny aplikace – seznam
Načte všechny události související s aplikacemi.

Odpovědí je seznam objektů ApplicationEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --koncový čas – UTC [povinné] | Koncový čas vyhledávacího dotazu ve formátu ISO UTC yyyy-MM-ddTHH \: mm \: SSZ. |
| --Start-Time-UTC [povinné] | Čas zahájení vyhledávacího dotazu ve formátu ISO UTC RRRR-MM-ddTHH \: mm \: SSZ. |
| --Events-Types – filtr | Toto je řetězec oddělený čárkami určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --Exclude-analýza-události | Tento parametr zakáže načtení AnalysisEvents, pokud je hodnota true. |
| --Skip-Correlation – vyhledávání | Tento parametr zakáže hledání informací CorrelatedEvents v případě, že je předána hodnota true. v opačném případě se naplní pole CorrelationEvents Get a HasCorrelatedEvents v každém FabricEvent. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-events-all-nodes-list"></a>sfctl události – všechny uzly – seznam
Načte všechny události související s uzly.

Odpovědí je seznam objektů NodeEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --koncový čas – UTC [povinné] | Koncový čas vyhledávacího dotazu ve formátu ISO UTC yyyy-MM-ddTHH \: mm \: SSZ. |
| --Start-Time-UTC [povinné] | Čas zahájení vyhledávacího dotazu ve formátu ISO UTC RRRR-MM-ddTHH \: mm \: SSZ. |
| --Events-Types – filtr | Toto je řetězec oddělený čárkami určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --Exclude-analýza-události | Tento parametr zakáže načtení AnalysisEvents, pokud je hodnota true. |
| --Skip-Correlation – vyhledávání | Tento parametr zakáže hledání informací CorrelatedEvents v případě, že je předána hodnota true. v opačném případě se naplní pole CorrelationEvents Get a HasCorrelatedEvents v každém FabricEvent. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-events-all-partitions-list"></a>sfctl události All-partitions-list
Načte všechny události související s oddíly.

Odpovědí je seznam objektů PartitionEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --koncový čas – UTC [povinné] | Koncový čas vyhledávacího dotazu ve formátu ISO UTC yyyy-MM-ddTHH \: mm \: SSZ. |
| --Start-Time-UTC [povinné] | Čas zahájení vyhledávacího dotazu ve formátu ISO UTC RRRR-MM-ddTHH \: mm \: SSZ. |
| --Events-Types – filtr | Toto je řetězec oddělený čárkami určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --Exclude-analýza-události | Tento parametr zakáže načtení AnalysisEvents, pokud je hodnota true. |
| --Skip-Correlation – vyhledávání | Tento parametr zakáže hledání informací CorrelatedEvents v případě, že je předána hodnota true. v opačném případě se naplní pole CorrelationEvents Get a HasCorrelatedEvents v každém FabricEvent. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-events-all-services-list"></a>sfctl události všechny služby – seznam
Načte všechny události související se službami.

Odpovědí je seznam objektů ServiceEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --koncový čas – UTC [povinné] | Koncový čas vyhledávacího dotazu ve formátu ISO UTC yyyy-MM-ddTHH \: mm \: SSZ. |
| --Start-Time-UTC [povinné] | Čas zahájení vyhledávacího dotazu ve formátu ISO UTC RRRR-MM-ddTHH \: mm \: SSZ. |
| --Events-Types – filtr | Toto je řetězec oddělený čárkami určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --Exclude-analýza-události | Tento parametr zakáže načtení AnalysisEvents, pokud je hodnota true. |
| --Skip-Correlation – vyhledávání | Tento parametr zakáže hledání informací CorrelatedEvents v případě, že je předána hodnota true. v opačném případě se naplní pole CorrelationEvents Get a HasCorrelatedEvents v každém FabricEvent. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-events-application-list"></a>sfctl události – seznam aplikací
Získá události související s aplikací.

Odpovědí je seznam objektů ApplicationEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --koncový čas – UTC [povinné] | Koncový čas vyhledávacího dotazu ve formátu ISO UTC yyyy-MM-ddTHH \: mm \: SSZ. |
| --Start-Time-UTC [povinné] | Čas zahájení vyhledávacího dotazu ve formátu ISO UTC RRRR-MM-ddTHH \: mm \: SSZ. |
| --Events-Types – filtr | Toto je řetězec oddělený čárkami určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --Exclude-analýza-události | Tento parametr zakáže načtení AnalysisEvents, pokud je hodnota true. |
| --Skip-Correlation – vyhledávání | Tento parametr zakáže hledání informací CorrelatedEvents v případě, že je předána hodnota true. v opačném případě se naplní pole CorrelationEvents Get a HasCorrelatedEvents v každém FabricEvent. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-events-cluster-list"></a>cluster událostí sfctl – seznam
Načte všechny události související s clusterem.

Odpovědí je seznam objektů ClusterEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --koncový čas – UTC [povinné] | Koncový čas vyhledávacího dotazu ve formátu ISO UTC yyyy-MM-ddTHH \: mm \: SSZ. |
| --Start-Time-UTC [povinné] | Čas zahájení vyhledávacího dotazu ve formátu ISO UTC RRRR-MM-ddTHH \: mm \: SSZ. |
| --Events-Types – filtr | Toto je řetězec oddělený čárkami určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --Exclude-analýza-události | Tento parametr zakáže načtení AnalysisEvents, pokud je hodnota true. |
| --Skip-Correlation – vyhledávání | Tento parametr zakáže hledání informací CorrelatedEvents v případě, že je předána hodnota true. v opačném případě se naplní pole CorrelationEvents Get a HasCorrelatedEvents v každém FabricEvent. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-events-node-list"></a>uzel událostí sfctl – seznam
Načte události související s uzlem.

Odpovědí je seznam objektů NodeEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --koncový čas – UTC [povinné] | Koncový čas vyhledávacího dotazu ve formátu ISO UTC yyyy-MM-ddTHH \: mm \: SSZ. |
| --Node-Name [povinné] | Název uzlu |
| --Start-Time-UTC [povinné] | Čas zahájení vyhledávacího dotazu ve formátu ISO UTC RRRR-MM-ddTHH \: mm \: SSZ. |
| --Events-Types – filtr | Toto je řetězec oddělený čárkami určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --Exclude-analýza-události | Tento parametr zakáže načtení AnalysisEvents, pokud je hodnota true. |
| --Skip-Correlation – vyhledávání | Tento parametr zakáže hledání informací CorrelatedEvents v případě, že je předána hodnota true. v opačném případě se naplní pole CorrelationEvents Get a HasCorrelatedEvents v každém FabricEvent. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl události oddíl – všechny repliky – seznam
Načte všechny události související s replikami pro oddíl.

Odpovědí je seznam objektů ReplicaEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --koncový čas – UTC [povinné] | Koncový čas vyhledávacího dotazu ve formátu ISO UTC yyyy-MM-ddTHH \: mm \: SSZ. |
| --Partition-ID [povinné] | Identita oddílu |
| --Start-Time-UTC [povinné] | Čas zahájení vyhledávacího dotazu ve formátu ISO UTC RRRR-MM-ddTHH \: mm \: SSZ. |
| --Events-Types – filtr | Toto je řetězec oddělený čárkami určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --Exclude-analýza-události | Tento parametr zakáže načtení AnalysisEvents, pokud je hodnota true. |
| --Skip-Correlation – vyhledávání | Tento parametr zakáže hledání informací CorrelatedEvents v případě, že je předána hodnota true. v opačném případě se naplní pole CorrelationEvents Get a HasCorrelatedEvents v každém FabricEvent. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-events-partition-list"></a>sfctl události – seznam oddílů
Získá události související s oddíly.

Odpovědí je seznam objektů PartitionEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --koncový čas – UTC [povinné] | Koncový čas vyhledávacího dotazu ve formátu ISO UTC yyyy-MM-ddTHH \: mm \: SSZ. |
| --Partition-ID [povinné] | Identita oddílu |
| --Start-Time-UTC [povinné] | Čas zahájení vyhledávacího dotazu ve formátu ISO UTC RRRR-MM-ddTHH \: mm \: SSZ. |
| --Events-Types – filtr | Toto je řetězec oddělený čárkami určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --Exclude-analýza-události | Tento parametr zakáže načtení AnalysisEvents, pokud je hodnota true. |
| --Skip-Correlation – vyhledávání | Tento parametr zakáže hledání informací CorrelatedEvents v případě, že je předána hodnota true. v opačném případě se naplní pole CorrelationEvents Get a HasCorrelatedEvents v každém FabricEvent. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-events-partition-replica-list"></a>sfctl události oddíl – seznam replik
Získá události související s replikou oddílu.

Odpovědí je seznam objektů ReplicaEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --koncový čas – UTC [povinné] | Koncový čas vyhledávacího dotazu ve formátu ISO UTC yyyy-MM-ddTHH \: mm \: SSZ. |
| --Partition-ID [povinné] | Identita oddílu |
| --Replica-ID [povinné] | Identifikátor repliky |
| --Start-Time-UTC [povinné] | Čas zahájení vyhledávacího dotazu ve formátu ISO UTC RRRR-MM-ddTHH \: mm \: SSZ. |
| --Events-Types – filtr | Toto je řetězec oddělený čárkami určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --Exclude-analýza-události | Tento parametr zakáže načtení AnalysisEvents, pokud je hodnota true. |
| --Skip-Correlation – vyhledávání | Tento parametr zakáže hledání informací CorrelatedEvents v případě, že je předána hodnota true. v opačném případě se naplní pole CorrelationEvents Get a HasCorrelatedEvents v každém FabricEvent. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-events-service-list"></a>Služba sfctl Events – seznam
Načte události související se službou.

Odpovědí je seznam objektů ServiceEvent.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --koncový čas – UTC [povinné] | Koncový čas vyhledávacího dotazu ve formátu ISO UTC yyyy-MM-ddTHH \: mm \: SSZ. |
| --Service-ID [povinné] | Identita služby. Toto ID je obvykle úplný název služby bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Pokud je například název služby "Fabric \: /MyApp/app1/svc1", identita služby by byla "MyApp \~ app1 \~ svc1" ve verzi 6.0 + a "MyApp/app1/svc1" v předchozích verzích. |
| --Start-Time-UTC [povinné] | Čas zahájení vyhledávacího dotazu ve formátu ISO UTC RRRR-MM-ddTHH \: mm \: SSZ. |
| --Events-Types – filtr | Toto je řetězec oddělený čárkami určující typy FabricEvents, které by měly být zahrnuty pouze v odpovědi. |
| --Exclude-analýza-události | Tento parametr zakáže načtení AnalysisEvents, pokud je hodnota true. |
| --Skip-Correlation – vyhledávání | Tento parametr zakáže hledání informací CorrelatedEvents v případě, že je předána hodnota true. v opačném případě se naplní pole CorrelationEvents Get a HasCorrelatedEvents v každém FabricEvent. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

