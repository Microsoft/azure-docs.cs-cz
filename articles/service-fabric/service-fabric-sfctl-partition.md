---
title: Oddíl CLI- sfcTL azure service fabric
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu oddílů pro službu.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: c038ef3266a727bf6984a5bd88ca540a589380db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905850"
---
# <a name="sfctl-partition"></a>sfctl partition
Dotazovat a spravovat oddíly pro všechny služby.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| ztráta dat | Toto rozhraní API způsobí ztrátu dat pro zadaný oddíl. |
| stav ztráty dat | Získá průběh operace ztráty dat oddílu začal pomocí Rozhraní API StartDataLoss. |
| Zdraví | Získá stav zadaného oddílu Service Fabric. |
| Info | Získá informace o oddílu Service Fabric. |
| list | Získá seznam oddílů služby Service Fabric služby. |
| načítání | Získá informace o zatížení zadaného oddílu Service Fabric. |
| resetování zatížení | Obnoví aktuální zatížení oddílu Service Fabric. |
| ztráta kvora | Indukuje ztrátu kvora pro daný oddíl stavové služby. |
| status kvora-ztráta | Získá průběh operace ztráty kvora na oddílu začal pomocí StartQuorumLoss ROZHRANÍ API. |
| Obnovit | Označuje clusteru Service Fabric, že by se měl pokusit obnovit konkrétní oddíl, který je aktuálně zablokovaný ve ztrátě kvora. |
| recover-all | Označuje clusteru Service Fabric, že by se měl pokusit obnovit všechny služby (včetně systémových služeb), které jsou aktuálně uvízlé ve ztrátě kvora. |
| zpráva-zdraví | Odešle zprávu o stavu oddílu Service Fabric. |
| restart | Toto rozhraní API restartuje některé nebo všechny repliky nebo instance zadaného oddílu. |
| stav restartování | Získá průběh partitionRestart operace spuštěna pomocí StartPartitionRestart. |
| svc-název | Získá název služby Service Fabric pro oddíl. |

## <a name="sfctl-partition-data-loss"></a>sfctl oddíl ztráta dat
Toto rozhraní API způsobí ztrátu dat pro zadaný oddíl.

Spustí volání onDataLossAsync API oddílu.  Toto rozhraní API způsobí ztrátu dat pro zadaný oddíl. Spustí volání onDataLoss ROZHRANÍ API oddílu. Skutečná ztráta dat bude záviset na zadaném parametru DataLossMode.
- PartialDataLoss: Pouze kvorum repliky jsou odebrány a OnDataLoss se aktivuje pro oddíl, ale skutečná ztráta dat závisí na přítomnosti in-flight replikace.  
- FullDataLoss: Všechny repliky jsou odebrány, proto jsou všechna data ztracena a onDataLoss je spuštěn. Toto rozhraní API by měla být volána pouze se stavovou službou jako cíl. Volání tohoto rozhraní API se systémovou službou jako cíl se nedoporučuje.

> [!NOTE]   
> Jakmile toto rozhraní API byla volána, nelze vrátit zpět. Volání CancelOperation pouze zastaví provádění a vyčistit stav vnitřního systému. Nebude obnovit data, pokud příkaz pokročil dostatečně daleko způsobit ztrátu dat. Volání rozhraní API GetDataLossProgress se stejným OperationId vrátit informace o operaci spuštěné s tímto rozhraním API.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --data-loss-mode [Povinné] | Tento výčet je předán a StartDataLoss ROZHRANÍ API k označení, jaký typ ztráty dat vyvolat. |
| --operation-id [Povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  To je předán do odpovídající rozhraní API GetProgress. |
| --partition-id [Povinné] | Identita oddílu. |
| --service-id [Povinné] | Identita služby. Toto ID je obvykle úplný název služby\:bez schématu URI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název služby\:je "fabric /myapp/app1/svc1", identita\~služby\~by "myapp app1 svc1" v 6.0+ a "myapp/app1/svc1" v předchozích verzích. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl oddíl data-loss-status
Získá průběh operace ztráty dat oddílu začal pomocí Rozhraní API StartDataLoss.

Získá průběh operace ztráty dat spuštěna s StartDataLoss pomocí OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --operation-id [Povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  To je předán do odpovídající rozhraní API GetProgress. |
| --partition-id [Povinné] | Identita oddílu. |
| --service-id [Povinné] | Identita služby. Toto ID je obvykle úplný název služby\:bez schématu URI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název služby\:je "fabric /myapp/app1/svc1", identita\~služby\~by "myapp app1 svc1" v 6.0+ a "myapp/app1/svc1" v předchozích verzích. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-health"></a>sfctl stav oddílu
Získá stav zadaného oddílu Service Fabric.

Pomocí filtru EventsHealthStateFilter můžete filtrovat kolekci událostí stavu vykazovanou ve službě na základě stavu. Pomocí filtru ReplicasHealthStateFilter můžete filtrovat kolekci objektů ReplicaHealthState v oddílu. Pokud zadáte oddíl, který neexistuje v úložišti stavu, tento požadavek vrátí chybu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --partition-id [Povinné] | Identita oddílu. |
| --events-health-state-filter | Umožňuje filtrování kolekce HealthEvent objekty vrácené na základě stavu. Možné hodnoty pro tento parametr zahrnují tetinovou hodnotu jednoho z následujících stavů. Jsou vráceny pouze události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaného stavu. Pokud není zadán, jsou vráceny všechny položky. Hodnoty stavu jsou výčty založené na příznaku, takže hodnota může být kombinací těchto hodnot získaných pomocí bitového operátoru "OR". Například Pokud je zadaný hodnota 6 pak jsou vráceny všechny události s HealthState hodnotu OK (2) a Upozornění (4).  <br> - Výchozí - Výchozí hodnota. Odpovídá jakékoli HealthState. Hodnota je nula.  <br> - Žádný - filtr, který neodpovídá žádné hodnotě HealthState. Používá se k vrácení žádné výsledky na dané kolekci stavů. Hodnota je 1.  <br> - Ok - Filtr, který odpovídá vstupu s hodnotou HealthState Ok. Hodnota je 2.  <br> - Upozornění - filtr, který odpovídá vstupu s HealthState hodnota Upozornění. Hodnota je 4.  <br> - Chyba - filtr, který odpovídá vstupu s Hodnotou HealthState Error. Hodnota je 8.  <br> - All - Filtr, který odpovídá vstupu s libovolnou hodnotou HealthState. Hodnota je 65535. |
| --vyloučit-zdravotní statistiky | Označuje, zda by měly být vráceny statistiky stavu jako součást výsledku dotazu. False ve výchozím nastavení. Statistiky ukazují počet podřízených entit ve stavu Ok, Upozornění a Chyba. |
| --replicas-state-state-filter --replicas-state-filter --replicas-health-state-filter --replicas- | Umožňuje filtrování kolekce objektů ReplicaHealthState v oddílu. Hodnotu lze získat z členů nebo bitových operací na členy HealthStateFilter. Budou vráceny pouze repliky, které odpovídají filtru. Všechny repliky budou použity k vyhodnocení agregovaného stavu. Pokud není zadán, budou vráceny všechny položky. Hodnoty stavu jsou výčty založené na příznaku, takže hodnota může být kombinací těchto hodnot získaných pomocí bitového operátoru "OR". Například Pokud je zadaný hodnota 6 pak všechny události s HealthState hodnotu OK (2) a upozornění (4) budou vráceny. Možné hodnoty pro tento parametr zahrnují tetinovou hodnotu jednoho z následujících stavů.  <br> - Výchozí - Výchozí hodnota. Odpovídá jakékoli HealthState. Hodnota je nula.  <br> - Žádný - filtr, který neodpovídá žádné hodnotě HealthState. Používá se k vrácení žádné výsledky na dané kolekci stavů. Hodnota je 1.  <br> - Ok - Filtr, který odpovídá vstupu s hodnotou HealthState Ok. Hodnota je 2.  <br> - Upozornění - filtr, který odpovídá vstupu s HealthState hodnota Upozornění. Hodnota je 4.  <br> - Chyba - filtr, který odpovídá vstupu s Hodnotou HealthState Error. Hodnota je 8.  <br> - All - Filtr, který odpovídá vstupu s libovolnou hodnotou HealthState. Hodnota je 65535. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-info"></a>sfctl informace o oddílu
Získá informace o oddílu Service Fabric.

Získá informace o zadaném oddílu. Odpověď zahrnuje ID oddílu, informace o schématu dělení, klíče podporované oddílem, stav, stav a další podrobnosti o oddílu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --partition-id [Povinné] | Identita oddílu. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-list"></a>seznam oddílů sfctl
Získá seznam oddílů služby Service Fabric služby.

Odpověď zahrnuje ID oddílu, informace o schématu dělení, klíče podporované oddílem, stav, stav a další podrobnosti o oddílu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --service-id [Povinné] | Identita služby. Toto ID je obvykle úplný název služby\:bez schématu URI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název služby\:je "fabric /myapp/app1/svc1", identita\~služby\~by "myapp app1 svc1" v 6.0+ a "myapp/app1/svc1" v předchozích verzích. |
| --continuation-token | Parametr tokenu pokračování se používá k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API, pokud se výsledky ze systému nevejdou do jediné odpovědi. Když je tato hodnota předána dalšímu volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak token pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódována adresou URL. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-load"></a>sfctl zatížení oddílu
Získá informace o zatížení zadaného oddílu Service Fabric.

Vrátí informace o zatížení zadaného oddílu. Odpověď obsahuje seznam sestav zatížení pro oddíl Service Fabric. Každá sestava obsahuje název metriky zatížení, hodnotu a naposledy vykázaný čas v čase UTC.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --partition-id [Povinné] | Identita oddílu. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-load-reset"></a>sfctl partition load-reset
Obnoví aktuální zatížení oddílu Service Fabric.

Obnoví aktuální zatížení oddílu Service Fabric na výchozí zatížení služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --partition-id [Povinné] | Identita oddílu. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-quorum-loss"></a>sfctl oddíl kvorum-ztráta
Indukuje ztrátu kvora pro daný oddíl stavové služby.

Toto rozhraní API je užitečné pro situaci dočasné ztráty kvora ve vaší službě. Volání Rozhraní API GetQuorumLossProgress se stejným OperationId vrátit informace o operaci spuštěna s tímto rozhraním API. To lze volat pouze na stavové trvalé (HasPersistedState==true) služby.  Nepoužívejte toto rozhraní API na bezstavové služby nebo stavové pouze služby v paměti.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --operation-id [Povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  To je předán do odpovídající rozhraní API GetProgress. |
| --partition-id [Povinné] | Identita oddílu. |
| --quorum-loss-duration [Povinné] | Doba, po kterou bude oddíl udržován ve ztrátě kvora.  To musí být zadáno v sekundách. |
| --režim ztráty kvora [Povinné] | Tento výčet je předán a StartQuorumLoss ROZHRANÍ API k označení, jaký typ ztráty kvora vyvolat. |
| --service-id [Povinné] | Identita služby. Toto ID je obvykle úplný název služby\:bez schématu URI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název služby\:je "fabric /myapp/app1/svc1", identita\~služby\~by "myapp app1 svc1" v 6.0+ a "myapp/app1/svc1" v předchozích verzích. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl oddíl kvor-ztráta-stav
Získá průběh operace ztráty kvora na oddílu začal pomocí StartQuorumLoss ROZHRANÍ API.

Získá průběh operace ztráty kvora spuštěna s StartQuorumLoss pomocí za předpokladu OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --operation-id [Povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  To je předán do odpovídající rozhraní API GetProgress. |
| --partition-id [Povinné] | Identita oddílu. |
| --service-id [Povinné] | Identita služby. Toto ID je obvykle úplný název služby\:bez schématu URI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název služby\:je "fabric /myapp/app1/svc1", identita\~služby\~by "myapp app1 svc1" v 6.0+ a "myapp/app1/svc1" v předchozích verzích. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-recover"></a>oddíl sfctl se obnovuje
Označuje clusteru Service Fabric, že by se měl pokusit obnovit konkrétní oddíl, který je aktuálně zablokovaný ve ztrátě kvora.

Tato operace by měla být provedena pouze v případě, že je známo, že repliky, které jsou dole nelze obnovit. Nesprávné použití tohoto rozhraní API může způsobit potenciální ztrátu dat.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --partition-id [Povinné] | Identita oddílu. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-recover-all"></a>sfctl oddíl recover-all
Označuje clusteru Service Fabric, že by se měl pokusit obnovit všechny služby (včetně systémových služeb), které jsou aktuálně uvízlé ve ztrátě kvora.

Tato operace by měla být provedena pouze v případě, že je známo, že repliky, které jsou dole nelze obnovit. Nesprávné použití tohoto rozhraní API může způsobit potenciální ztrátu dat.

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

## <a name="sfctl-partition-report-health"></a>sfctl partition zpráva-stav
Odešle zprávu o stavu oddílu Service Fabric.

Hlásí stav zadaného oddílu Service Fabric. Sestava musí obsahovat informace o zdroji sestavy stavu a vlastnosti, na které je hlášena. Sestava je odeslána do oddílu brány Service Fabric, který předá do úložiště stavu. Sestava může být přijata bránou, ale odmítnuta úložištěm stavu po dalším ověření. Úložiště stavu může například odmítnout sestavu z důvodu neplatného parametru, jako je zastaralé pořadové číslo. Chcete-li zjistit, zda byla sestava použita v úložišti stavu, zkontrolujte, zda se sestava zobrazuje v části události.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --health-property [Povinné] | Vlastnost informací o stavu. <br><br> Entita může mít sestavy stavu pro různé vlastnosti. Vlastnost je řetězec a není pevný výčet, aby reportér flexibilitu kategorizovat podmínku stavu, který aktivuje sestavu. Například reportér s SourceId "LocalWatchdog" můžete sledovat stav dostupného disku na uzlu, tak to může hlásit "AvailableDisk" vlastnost na tomto uzlu. Stejný reportér může sledovat připojení uzlu, takže může hlásit vlastnost "Připojení" na stejném uzlu. V úložišti stavu jsou tyto sestavy považovány za samostatné události stavu pro zadaný uzel. Spolu s SourceId vlastnost jednoznačně identifikuje informace o stavu. |
| --stav -state [Povinné] | Možné hodnoty\: zahrnují "Neplatné", Ok, "Upozornění", "Chyba", Neznámý. |
| --partition-id [Povinné] | Identita oddílu. |
| --source-id [Povinné] | Název zdroje, který identifikuje klient/watchdog/systémová součást, která generovala informace o stavu. |
| --popis | Popis informací o zdravotním stavu. <br><br> Představuje volný text používaný k přidání lidsky čitelných informací o sestavě. Maximální délka řetězce pro popis je 4096 znaků. Pokud je zadaný řetězec delší, bude automaticky zkrácen. Při zkrácení poslední znaky popisu obsahují značku "[Zkráceno]" a celková velikost řetězce je 4096 znaků. Přítomnost značky označuje uživatelům, že došlo ke zkrácení. Všimněte si, že při zkrácení popis má méně než 4096 znaků z původního řetězce. |
| --okamžitá | Příznak, který označuje, zda má být sestava odeslána okamžitě. <br><br> Sestava stavu je odeslána do aplikace brány Service Fabric, která předá do úložiště stavu. Pokud immediate je nastavena na hodnotu true, sestava je odeslána okamžitě z brány HTTP do úložiště stavu, bez ohledu na nastavení klienta prostředků infrastruktury, které používá aplikace brány HTTP. To je užitečné pro kritické sestavy, které by měly být odeslány co nejdříve. V závislosti na časování a dalších podmínkách může odesílání sestavy stále nezdaří, například pokud je brána HTTP uzavřena nebo zpráva nedosáhne brány. Pokud immediate je nastavena na false, sestava je odeslána na základě nastavení klienta stavu z brány HTTP. Proto bude dávkově podle konfigurace HealthReportSendInterval. Toto je doporučené nastavení, protože umožňuje klientovi stavu optimalizovat zprávy o stavu do úložiště stavu, jakož i zpracování sestavy stavu. Ve výchozím nastavení nejsou sestavy odesílány okamžitě. |
| --remove-when-expired --remove-when-expired --remove-when-expired --remove | Hodnota, která označuje, zda je sestava odebrána z úložiště stavu po vypršení jeho platnosti. <br><br> Pokud je nastavena na hodnotu true, sestava je odebrána z úložiště stavu po vypršení jeho platnosti. Pokud je nastavena na hodnotu false, sestava je považována za chybu při vypršení platnosti. Hodnota této vlastnosti je ve výchozím nastavení false. Když klienti pravidelně hlásí, měli by nastavit RemoveWhenExpired false (výchozí). Tímto způsobem je reportér má problémy (např. zablokování) a nelze hlásit, entita je vyhodnocena při chybě při vypršení platnosti sestavy stavu. To označí entitu jako ve stavu Chyba. |
| --pořadové číslo | Pořadové číslo pro tuto sestavu stavu jako číselný řetězec. <br><br> Pořadové číslo sestavy používá úložiště stavu ke zjištění zastaralých sestav. Pokud není zadán, pořadové číslo je automaticky generovánklientem stavu při přidání sestavy. |
| --časový čas -t | Výchozí\: 60. |
| --ttl | Doba, po kterou je tato zpráva o stavu platná. Toto pole používá pro určení doby trvání formát ISO8601. <br><br> Když klienti pravidelně hlásí, měli by odesílat sestavy s vyšší frekvencí než čas žít. Pokud klienti hlásí přechod, mohou nastavit čas žít na nekonečno. Když vyprší čas live, událost stavu, která obsahuje informace o stavu je buď odebrána z úložiště stavu, pokud RemoveWhenExpired je true, nebo vyhodnocena na chybu, pokud RemoveWhenExpired false. Pokud není zadán, čas žít výchozí nekonečnou hodnotu. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-restart"></a>sfctl partition restart
Toto rozhraní API restartuje některé nebo všechny repliky nebo instance zadaného oddílu.

Toto rozhraní API je užitečné pro testování převzetí služeb při selhání. Pokud se používá k cílení bezstavový oddíl služby, RestartPartitionMode musí být AllReplicasOrInstances. Volání rozhraní API GetPartitionRestartProgress pomocí stejné operationId získat průběh.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --operation-id [Povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  To je předán do odpovídající rozhraní API GetProgress. |
| --partition-id [Povinné] | Identita oddílu. |
| --restart-partition-mode [Povinné] | Popište, které oddíly chcete restartovat. |
| --service-id [Povinné] | Identita služby. Toto ID je obvykle úplný název služby\:bez schématu URI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název služby\:je "fabric /myapp/app1/svc1", identita\~služby\~by "myapp app1 svc1" v 6.0+ a "myapp/app1/svc1" v předchozích verzích. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-restart-status"></a>sfctl partition restart-status
Získá průběh partitionRestart operace spuštěna pomocí StartPartitionRestart.

Získá průběh PartitionRestart spuštěn a StartPartitionRestart pomocí zapředpokladu OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --operation-id [Povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  To je předán do odpovídající rozhraní API GetProgress. |
| --partition-id [Povinné] | Identita oddílu. |
| --service-id [Povinné] | Identita služby. Toto ID je obvykle úplný název služby\:bez schématu URI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název služby\:je "fabric /myapp/app1/svc1", identita\~služby\~by "myapp app1 svc1" v 6.0+ a "myapp/app1/svc1" v předchozích verzích. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-partition-svc-name"></a>sfctl partition svc-name
Získá název služby Service Fabric pro oddíl.

Získá název služby pro zadaný oddíl. Pokud ID oddílu v clusteru neexistuje, je vrácena chyba 404.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --partition-id [Povinné] | Identita oddílu. |
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
- [Setup](service-fabric-cli.md) the Service Fabric CLI.
- Naučte se používat funkce CLI service fabric pomocí [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).
