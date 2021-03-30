---
title: Azure Service Fabric CLI – oddíl sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu oddílů pro službu.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fff569c586548d84ed55018764363ad7f05e526d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257083"
---
# <a name="sfctl-partition"></a>sfctl partition
Dotazování a Správa oddílů pro libovolnou službu.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| ztráta dat | Toto rozhraní API bude mít za následek ztrátu dat pro zadaný oddíl. |
| data-ztráta stavu | Získá průběh operace ztráty dat oddílu zahájeného pomocí rozhraní StartDataLoss API. |
| nezávadnost | Získá stav zadaného oddílu Service Fabric. |
| příjemce | Získá informace o oddílu Service Fabric. |
| list | Načte seznam oddílů Service Fabric služby. |
| načítání | Získá informace o načtení zadaného oddílu Service Fabric. |
| Load-Reset | Obnoví aktuální zatížení oddílu Service Fabric. |
| výpadek kvora | Vystaví ztrátu kvora pro daný oddíl stavové služby. |
| kvorum – stav ztráty | Získá průběh operace ztráty kvora u oddílu zahájeného pomocí rozhraní StartQuorumLoss API. |
| opravitelné | Určuje Cluster Service Fabric, který by se měl pokusit obnovit konkrétní oddíl, který je aktuálně zablokovaný ve ztrátě kvora. |
| Obnovit – vše | Určuje Cluster Service Fabric, který by se měl pokusit obnovit jakékoli služby (včetně systémových služeb), které jsou aktuálně zablokované ve ztrátě kvora. |
| Sestava – stav | Odešle zprávu o stavu Service Fabric oddíl. |
| restart | Toto rozhraní API bude restartovat některé nebo všechny repliky nebo instance zadaného oddílu. |
| restart – stav | Získá průběh operace PartitionRestart spuštěné pomocí StartPartitionRestart. |
| název svc | Získá název služby Service Fabric pro oddíl. |

## <a name="sfctl-partition-data-loss"></a>data oddílu sfctl – ztráta
Toto rozhraní API bude mít za následek ztrátu dat pro zadaný oddíl.

Spustí volání rozhraní API OnDataLossAsync oddílu.  Toto rozhraní API bude mít za následek ztrátu dat pro zadaný oddíl. Spustí volání rozhraní API OnDataLoss oddílu. Skutečná ztráta dat bude záviset na zadané režim datalossmode.
- PartialDataLoss: odeberou se jenom kvorum replik a pro oddíl se aktivuje OnDataLoss, ale skutečná ztráta dat závisí na přítomnosti replikace v letadle.  
- FullDataLoss: všechny repliky se odeberou, takže se ztratí všechna data a aktivuje se OnDataLoss. Toto rozhraní API by mělo být jako cíl voláno pouze se stavovou službou. Volání tohoto rozhraní API se systémovou službou, protože cíl není doporučeno.

> [!NOTE]   
> Po zavolání tohoto rozhraní API ho nejde vrátit zpět. Volání CancelOperation zastaví provádění a vyčistí stav interního systému. Nebude data obnovovat, pokud příkaz pokračoval dostatečně daleko, aby způsobil ztrátu dat. Zavolejte rozhraní API GetDataLossProgress se stejným OperationId a vraťte informace o operaci spuštěnou s tímto rozhraním API.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --data-ztráta-Mode [povinné] | Tento výčet je předán rozhraní StartDataLoss API, které určuje, jaký typ ztráty dat má způsobit podnět. |
| --operace-ID [povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  Toto se předává do odpovídajícího rozhraní API getprogress. |
| --Partition-ID [povinné] | Identita oddílu |
| --Service-ID [povinné] | Identita služby. Toto ID je obvykle úplný název služby bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Pokud je například název služby "Fabric \: /MyApp/app1/svc1", identita služby by byla "MyApp \~ app1 \~ svc1" ve verzi 6.0 + a "MyApp/app1/svc1" v předchozích verzích. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-data-loss-status"></a>data oddílu sfctl – ztráta stavu
Získá průběh operace ztráty dat oddílu zahájeného pomocí rozhraní StartDataLoss API.

Získá průběh operace ztráty dat zahájené s StartDataLoss pomocí OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --operace-ID [povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  Toto se předává do odpovídajícího rozhraní API getprogress. |
| --Partition-ID [povinné] | Identita oddílu |
| --Service-ID [povinné] | Identita služby. Toto ID je obvykle úplný název služby bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Pokud je například název služby "Fabric \: /MyApp/app1/svc1", identita služby by byla "MyApp \~ app1 \~ svc1" ve verzi 6.0 + a "MyApp/app1/svc1" v předchozích verzích. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-health"></a>stav sfctl oddílu
Získá stav zadaného oddílu Service Fabric.

Pomocí EventsHealthStateFilter můžete filtrovat kolekci událostí stavu hlášených ve službě na základě stavu. Pomocí ReplicasHealthStateFilter můžete filtrovat kolekci objektů ReplicaHealthState v oddílu. Pokud zadáte oddíl, který v Health Store neexistuje, vrátí tato žádost chybu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Partition-ID [povinné] | Identita oddílu |
| --Events – stav – filtr | Umožňuje filtrovat kolekci objektů HealthEvent vrácených na základě stavu. Možné hodnoty pro tento parametr zahrnují celočíselnou hodnotu jednoho z následujících stavů. Vrátí se pouze události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaného stavu. Pokud tento parametr nezadáte, vrátí se všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota by mohla být kombinací těchto hodnot získána pomocí bitového operátoru OR. Pokud je například zadaná hodnota 6, budou vráceny všechny události s hodnotou ' OK (2) a upozornění (4).  <br> -Výchozí-výchozí hodnota. Odpovídá jakémukoli elementu. Hodnota je nula.  <br> -None-Filter, který neodpovídá žádné hodnotě elementu. Používá se k tomu, aby se v dané kolekci stavů nevracely žádné výsledky. Hodnota je 1.  <br> -OK – filtr, který odpovídá zadanému vstupu s hodnotou podstavu OK. Hodnota je 2.  <br> -Warning-Filter, který odpovídá vstupu s upozorněním na podstavovou hodnotu. Hodnota je 4.  <br> – Filtr chyb, který odpovídá zadanému vstupu s chybou hodnoty elementu stav Hodnota je 8.  <br> -All – filtr, který odpovídá zadanému vstupu s jakoukoli hodnotou elementu. Hodnota je 65535. |
| --Exclude-Health-Statistics | Určuje, zda mají být v rámci výsledku dotazu vráceny statistiky stavu. Výchozí hodnota je false. Statistika zobrazuje počet podřízených entit ve stavu OK, varování a chyba. |
| --repliky-stav-filtr | Umožňuje filtrovat kolekci objektů ReplicaHealthState v oddílu. Hodnotu lze získat z členů nebo bitových operací na členech HealthStateFilter. Vrátí se jenom repliky, které odpovídají filtru. Všechny repliky budou použity k vyhodnocení agregovaného stavu. Pokud tento parametr nezadáte, vrátí se všechny položky. Hodnoty stavu jsou výčty založené na příznak, takže hodnota by mohla být kombinací těchto hodnot získaných pomocí bitového operátoru OR. Pokud je například zadaná hodnota 6, budou vráceny všechny události s hodnotou ' OK (2) a upozornění (4). Možné hodnoty pro tento parametr zahrnují celočíselnou hodnotu jednoho z následujících stavů.  <br> -Výchozí-výchozí hodnota. Odpovídá jakémukoli elementu. Hodnota je nula.  <br> -None-Filter, který neodpovídá žádné hodnotě elementu. Používá se k tomu, aby se v dané kolekci stavů nevracely žádné výsledky. Hodnota je 1.  <br> -OK – filtr, který odpovídá zadanému vstupu s hodnotou podstavu OK. Hodnota je 2.  <br> -Warning-Filter, který odpovídá vstupu s upozorněním na podstavovou hodnotu. Hodnota je 4.  <br> – Filtr chyb, který odpovídá zadanému vstupu s chybou hodnoty elementu stav Hodnota je 8.  <br> -All – filtr, který odpovídá zadanému vstupu s jakoukoli hodnotou elementu. Hodnota je 65535. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-info"></a>informace o oddílu sfctl
Získá informace o oddílu Service Fabric.

Načte informace o zadaném oddílu. Odpověď zahrnuje ID oddílu, informace o schématu dělení a klíče podporované oddílem, stavem, stavem a dalšími podrobnostmi o oddílu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Partition-ID [povinné] | Identita oddílu |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-list"></a>Seznam oddílů sfctl
Načte seznam oddílů Service Fabric služby.

Odpověď zahrnuje ID oddílu, informace o schématu dělení a klíče podporované oddílem, stavem, stavem a dalšími podrobnostmi o oddílu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Service-ID [povinné] | Identita služby. Toto ID je obvykle úplný název služby bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Pokud je například název služby "Fabric \: /MyApp/app1/svc1", identita služby by byla "MyApp \~ app1 \~ svc1" ve verzi 6.0 + a "MyApp/app1/svc1" v předchozích verzích. |
| --pokračování-token | Parametr tokenu pokračování slouží k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API v případě, že se výsledky ze systému nevejdou do jediné odpovědi. Když se tato hodnota předává do dalšího volání rozhraní API, vrátí rozhraní API další sadu výsledků. Pokud nejsou k dispozici žádné další výsledky, token pokračování neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódovaná v adrese URL. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-load"></a>zatížení oddílu sfctl
Získá informace o načtení zadaného oddílu Service Fabric.

Vrátí informace o zatížení zadaného oddílu. Odpověď obsahuje seznam sestav načtení pro Service Fabric oddíl. Každá sestava obsahuje název a hodnotu metriky zatížení a poslední nahlášený čas ve standardu UTC.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Partition-ID [povinné] | Identita oddílu |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-load-reset"></a>zátěžové obnovení oddílu sfctl
Obnoví aktuální zatížení oddílu Service Fabric.

Obnoví aktuální zatížení Service Fabricho oddílu na výchozí zatížení služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Partition-ID [povinné] | Identita oddílu |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-quorum-loss"></a>kvorum oddílu sfctl – ztráta
Vystaví ztrátu kvora pro daný oddíl stavové služby.

Toto rozhraní API je užitečné pro situaci s dočasnou ztrátou kvora ve vaší službě. Zavolejte rozhraní API GetQuorumLossProgress se stejným OperationId a vraťte informace o operaci spuštěnou s tímto rozhraním API. Tato možnost se dá volat jenom u stavových trvale trvalých služeb (HasPersistedState = = true).  Nepoužívejte toto rozhraní API pro bezstavové služby nebo stavové služby pouze v paměti.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --operace-ID [povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  Toto se předává do odpovídajícího rozhraní API getprogress. |
| --Partition-ID [povinné] | Identita oddílu |
| --kvorum – ztráta – doba trvání [požadováno] | Doba, po kterou se bude oddíl uchovávat při ztrátě kvora.  Tento parametr je nutné zadat v sekundách. |
| --kvorum – režim ztráty [povinné] | Tento výčet je předán rozhraní StartQuorumLoss API, které určuje, jaký typ ztráty kvora má způsobit. |
| --Service-ID [povinné] | Identita služby. Toto ID je obvykle úplný název služby bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Pokud je například název služby "Fabric \: /MyApp/app1/svc1", identita služby by byla "MyApp \~ app1 \~ svc1" ve verzi 6.0 + a "MyApp/app1/svc1" v předchozích verzích. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-quorum-loss-status"></a>kvorum oddílu sfctl – ztráta – stav
Získá průběh operace ztráty kvora u oddílu zahájeného pomocí rozhraní StartQuorumLoss API.

Načte průběh operace ztráty kvora zahájené s StartQuorumLoss pomocí poskytnutého OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --operace-ID [povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  Toto se předává do odpovídajícího rozhraní API getprogress. |
| --Partition-ID [povinné] | Identita oddílu |
| --Service-ID [povinné] | Identita služby. Toto ID je obvykle úplný název služby bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Pokud je například název služby "Fabric \: /MyApp/app1/svc1", identita služby by byla "MyApp \~ app1 \~ svc1" ve verzi 6.0 + a "MyApp/app1/svc1" v předchozích verzích. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-recover"></a>obnovení oddílu sfctl
Určuje Cluster Service Fabric, který by se měl pokusit obnovit konkrétní oddíl, který je aktuálně zablokovaný ve ztrátě kvora.

Tato operace by měla být provedena pouze v případě, že je známo, že nelze obnovit repliky, které jsou mimo provoz. Nesprávné použití tohoto rozhraní API může způsobit ztrátu dat.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Partition-ID [povinné] | Identita oddílu |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-recover-all"></a>sfctl oddílu Recovery – vše
Určuje Cluster Service Fabric, který by se měl pokusit obnovit jakékoli služby (včetně systémových služeb), které jsou aktuálně zablokované ve ztrátě kvora.

Tato operace by měla být provedena pouze v případě, že je známo, že nelze obnovit repliky, které jsou mimo provoz. Nesprávné použití tohoto rozhraní API může způsobit ztrátu dat.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-report-health"></a>Sestava sfctl oddílu – stav
Odešle zprávu o stavu Service Fabric oddíl.

Hlásí stav zadaného oddílu Service Fabric. Sestava musí obsahovat informace o zdroji sestavy stavu a vlastnosti, na které je hlášena. Sestava se odešle do oddílu Service Fabric brány, který se přepošle na Health Store. Tuto sestavu může přijmout brána, ale Health Store po dodatečném ověření odmítnuta. Health Store například může zprávu odmítat z důvodu neplatného parametru, jako je například zastaralé pořadové číslo. Chcete-li zjistit, zda byla sestava použita v Health Store, zkontrolujte, zda se sestava zobrazí v části události.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Health-vlastnost [required] | Vlastnost informací o stavu. <br><br> Entita může mít sestavy o stavu pro různé vlastnosti. Vlastnost je řetězec, nikoli pevný výčet, který umožňuje, aby se v zpravodaji mohla pružně roztřídit stav stavu, který aktivuje sestavu. Například zpravodaj s SourceId "LocalWatchdog" může monitorovat stav dostupného disku v uzlu, takže může ohlásit vlastnost "AvailableDisk" v tomto uzlu. Stejné zpravodajky můžou monitorovat připojení uzlu, takže může nahlásit vlastnost "připojení" na stejném uzlu. V Health Store jsou tyto sestavy zpracovány jako samostatné události stavu pro zadaný uzel. Společně s ID zdroje (SourceId) vlastnost jednoznačně identifikuje informace o stavu. |
| --Health-State [povinné] | Možné hodnoty zahrnují \: "Invalid", "OK", "Warning", "Error", "unknown". |
| --Partition-ID [povinné] | Identita oddílu |
| --Source-ID [povinné] | Název zdroje, který identifikuje součást klienta/sledovacího zařízení/systému, která vygenerovala informace o stavu. |
| --Description | Popis informací o stavu. <br><br> Představuje bezplatný text, který se používá k přidání lidských čitelných informací o sestavě. Maximální délka řetězce pro popis je 4096 znaků. Pokud je zadaný řetězec delší, automaticky se zkrátí. Při zkrácení obsahují poslední znaky popisu značku "[zkrácené]" a celková velikost řetězce je 4096 znaků. Přítomnost značky indikuje uživatelům, kterým došlo ke zkrácení. Všimněte si, že při zkrácení má popis méně než 4096 znaků z původního řetězce. |
| --Immediate | Příznak, který označuje, zda má být sestava odeslána okamžitě. <br><br> Do aplikace Service Fabric Gateway se pošle zpráva o stavu, která se přepošle na Health Store. Pokud je vlastnost Immediate nastavená na hodnotu true, pošle se z brány HTTP na Health Store zprávu hned, a to bez ohledu na nastavení klienta prostředků infrastruktury, které používá aplikace brány HTTP. To je užitečné pro kritické sestavy, které by se měly odesílat co nejdříve. V závislosti na časování a dalších podmínkách může odeslání sestavy dál selhat, například pokud je brána HTTP uzavřená nebo zpráva nemá přístup k bráně. Pokud je vlastnost Immediate nastavená na false, pošle se sestava na základě nastavení klienta stavu z brány HTTP. Proto se bude provádět dávkování podle konfigurace HealthReportSendInterval. Toto je doporučené nastavení, protože umožňuje klientovi stavu optimalizovat zprávy o stavu, které se mají Health Store, i zpracování sestavy o stavu. Ve výchozím nastavení se zprávy neodesílají okamžitě. |
| --Remove-when-vypršela platnost | Hodnota, která označuje, zda je sestava odebrána z Health Store v případě jejího platnosti. <br><br> Pokud je nastavená hodnota true, sestava se po vypršení platnosti odebere z Health Store. Pokud je nastavena hodnota false, bude sestava považována za chybu, pokud vypršela její platnost. Hodnota této vlastnosti je ve výchozím nastavení false. Při pravidelné sestavě klientů by měly být nastavené RemoveWhenExpired na hodnotu false (výchozí). Tímto způsobem má zpravodaj problémy (například zablokování) a nemůže hlásit, entita je vyhodnocena při vypršení platnosti sestavy stavu. Tím se označí entita jako v chybovém stavu. |
| --pořadové číslo | Pořadové číslo pro tuto sestavu stavu jako číselný řetězec. <br><br> Číslo sekvence sestavy používá Health Store ke zjišťování zastaralých sestav. Není-li tento parametr zadán, je číslo sekvence automaticky generováno klientem stavu při přidání sestavy. |
| --Timeout-t | Výchozí hodnota je \: 60. |
| --TTL | Doba, po kterou je tato sestava stavu platná. Toto pole používá formát ISO8601 k zadání doby trvání. <br><br> Při pravidelné sestavě klientů by měly odesílat sestavy s vyšší frekvencí, než je čas do provozu. Pokud klienti hlásí přechod, můžou nastavit čas na živého na nekonečné. Po vypršení časového limitu životnosti události stavu, která obsahuje informace o stavu, se buď odeberou z Health Store, pokud je RemoveWhenExpired true, nebo se vyhodnotí při chybě, pokud RemoveWhenExpired false. Pokud není zadaný, hodnota TTL (Time to Live) nastaví nekonečnou hodnotu. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-restart"></a>restart oddílu sfctl
Toto rozhraní API bude restartovat některé nebo všechny repliky nebo instance zadaného oddílu.

Toto rozhraní API je užitečné při testování převzetí služeb při selhání. Pokud se používá k cílení na bezstavový oddíl služby, režim restartpartitionmode musí být AllReplicasOrInstances. K získání pokroku volejte rozhraní API GetPartitionRestartProgress pomocí stejného OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --operace-ID [povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  Toto se předává do odpovídajícího rozhraní API getprogress. |
| --Partition-ID [povinné] | Identita oddílu |
| --restart-partition-Mode [required] | Popište, které oddíly se mají restartovat. |
| --Service-ID [povinné] | Identita služby. Toto ID je obvykle úplný název služby bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Pokud je například název služby "Fabric \: /MyApp/app1/svc1", identita služby by byla "MyApp \~ app1 \~ svc1" ve verzi 6.0 + a "MyApp/app1/svc1" v předchozích verzích. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-restart-status"></a>restart oddílu sfctl-stav
Získá průběh operace PartitionRestart spuštěné pomocí StartPartitionRestart.

Získá průběh PartitionRestart Začínáme s StartPartitionRestart pomocí poskytnutého OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --operace-ID [povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  Toto se předává do odpovídajícího rozhraní API getprogress. |
| --Partition-ID [povinné] | Identita oddílu |
| --Service-ID [povinné] | Identita služby. Toto ID je obvykle úplný název služby bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Pokud je například název služby "Fabric \: /MyApp/app1/svc1", identita služby by byla "MyApp \~ app1 \~ svc1" ve verzi 6.0 + a "MyApp/app1/svc1" v předchozích verzích. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-svc-name"></a>sfctl oddíl svc – název
Získá název služby Service Fabric pro oddíl.

Získá název služby pro zadaný oddíl. Pokud ID oddílu v clusteru neexistuje, vrátí se chyba 404.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Partition-ID [povinné] | Identita oddílu |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](./scripts/sfctl-upgrade-application.md).
