---
title: Azure Service Fabric CLI sfctl oddílem | Dokumentace Microsoftu
description: Popisuje příkazy Service Fabric CLI sfctl oddílu.
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
ms.openlocfilehash: c2bb1c0147d38b4286e2cdfb2d161eaa0704e393
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271483"
---
# <a name="sfctl-partition"></a>sfctl partition
Dotazování a správu oddílů pro libovolnou službu.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| ztráty dat | Toto rozhraní API způsobí ztrátu dat pro zadaný oddíl. |
| Stav ztráty dat | Získá průběh operace ztráty dat oddílu začít používat rozhraní API StartDataLoss. |
| zdravotnictví | Získá stav zadaný oddíl Service Fabric. |
| informace | Získá informace o oddílu Service Fabric. |
| list | Získá seznam oddílů služby Service Fabric. |
| načítání | Získá informace o načtení zadaného oddílu Service Fabric. |
| načíst obnovit | Obnoví aktuální zatížení oddílů Service Fabric. |
| ztráty kvora | Indukuje ztráty kvora pro daný stavové služby oddíl. |
| kvorum. ke ztrátě stavu | Získá průběh operace ztrátou kvora v oddílu pomocí rozhraní API StartQuorumLoss spuštěna. |
| Obnovit | Do clusteru Service Fabric označuje, že by měl pokusí o zotavení konkrétního oddílu, který je aktuálně zablokované ve ztrátě kvora. |
| obnovení – to všechno | Do clusteru Service Fabric označuje, že by měl pokusí o zotavení veškeré služby (včetně systémových služeb), které jsou aktuálně zablokované ve ztrátě kvora. |
| Stav sestavy | Odešle zprávu o stavu v Service Fabric oddílu. |
| restart | Toto rozhraní API se restartuje, některé nebo všechny replik nebo instancí zadaný oddíl. |
| restart stavu | Získá průběh operace PartitionRestart začít používat StartPartitionRestart. |
| svc-name | Získá název služby Service Fabric pro oddíl. |

## <a name="sfctl-partition-data-loss"></a>sfctl oddílu-ztráty dat
Toto rozhraní API způsobí ztrátu dat pro zadaný oddíl.

Aktivuje volání rozhraní API OnDataLossAsync oddílu.  Toto rozhraní API způsobí ztrátu dat pro zadaný oddíl. Aktivuje volání rozhraní API OnDataLoss oddílu. Skutečná data ztráty bude záviset na zadané DataLossMode.  <br> -PartialDataLoss – pouze kvorum replik se odeberou a OnDataLoss se aktivuje pro oddíl, ale skutečná data ztráty závisí na přítomnosti vydávaných za pochodu replikace.  <br> -FullDataLoss – všechny repliky se odeberou. proto dojde ke ztrátě všech dat a OnDataLoss se aktivuje. Toto rozhraní API by měla volat pouze se stavovou službou jako cíl. Volající toto rozhraní API s využitím služby systému jako cíl se nedoporučuje.

> [!NOTE] 
> Po zavolání tohoto rozhraní API nelze vrátit. Volání CancelOperation pouze zastavit provádění a vyčistit stav vnitřní systému. Pokud příkaz posunula dostatečně daleko způsobit ztrátu dat ho nebudou data obnovit. Volání rozhraní API GetDataLossProgress s stejným OperationId k vrácení informací o operaci spustit pomocí tohoto rozhraní API.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --– ztráta režim dat [povinné] | Tento výčet je předán do rozhraní API StartDataLoss označíte, jaký typ ztrátě dat. Chcete-li zahájit. |
| – id operace [povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  To je předán do odpovídajícího GetProgress rozhraní API. |
| – id oddílu [povinné] | Identita oddílu. |
| – id služby [povinné] | Identita služby. Toto ID se obvykle celý název služby bez "fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "myapp\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl oddílu – ztráta stav dat
Získá průběh operace ztráty dat oddílu začít používat rozhraní API StartDataLoss.

Získá průběh operace s daty ke ztrátě StartDataLoss pomocí OperationId můžete začít.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id operace [povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  To je předán do odpovídajícího GetProgress rozhraní API. |
| – id oddílu [povinné] | Identita oddílu. |
| – id služby [povinné] | Identita služby. Toto ID se obvykle celý název služby bez "fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "myapp\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-partition-health"></a>sfctl partition health
Získá stav zadaný oddíl Service Fabric.

EventsHealthStateFilter použijte k filtrování kolekce událostí stavu hlášené pro službu založenou na stav v pořádku. ReplicasHealthStateFilter použijte k filtrování kolekce objektů ReplicaHealthState v oddílu. Pokud chcete zadat oddíl, který neexistuje v health store, tento požadavek vrátí chybu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [povinné] | Identita oddílu. |
| --události stavu stavu filtru | Umožňuje filtrování vrácených objektů HealthEvent kolekce na základě stavu. Možné hodnoty pro tento parametr obsahovat celočíselnou hodnotu některého z následujících stavů. Vrátí se jenom události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadán, budou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota může být kombinací těchto hodnot, pomocí bitový operátor "OR". Například pokud zadaná hodnota je 6 všechny události s hodnotou elementu HealthState OK (2) a upozorněním (4), jsou vráceny.  <br> – Výchozí – výchozí hodnota. Odpovídá jakékoli stav HealthState. Hodnota je nula.  <br> -Žádný - filtr, který se pravděpodobně neshoduje s žádnou hodnotu stavu HealthState. Použít, aby nevracela žádné výsledky na dané kolekce stavů. Hodnota je 1.  <br> -Ok – umožňuje filtrovat, že odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Warning - filtr, že hodnota vstupu odpovídá k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který se shoduje s hodnotou elementu HealthState Chyba vstupu. Hodnota je 8.  <br> -All - filtr, který odpovídá s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --exclude statistiky stavu | Určuje, zda má být vrácen statistik stavu jako součást výsledků dotazu. Ve výchozím nastavení False. Statistiky ukazují počet podřízených entit ve stavu Ok, upozornění a chyby. |
| --repliky stavu stavu filtru | Umožňuje filtrování kolekce objektů ReplicaHealthState v oddílu. Nejde získat hodnotu z členů nebo bitové operace u členů HealthStateFilter. Vrátí se pouze repliky, které odpovídají filtru. Všechny repliky se použije k vyhodnocení agregovaný stav v pořádku. Pokud se nezadá, vrátí se všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota může být kombinací těchto hodnot pomocí bitový operátor "OR". Například pokud zadaná hodnota je 6 pak všechny události s hodnotou elementu HealthState OK (2) a upozorněním (4) vrátí se. Možné hodnoty pro tento parametr obsahovat celočíselnou hodnotu některého z následujících stavů.  <br> – Výchozí – výchozí hodnota. Odpovídá jakékoli stav HealthState. Hodnota je nula.  <br> -Žádný - filtr, který se pravděpodobně neshoduje s žádnou hodnotu stavu HealthState. Použít, aby nevracela žádné výsledky na dané kolekce stavů. Hodnota je 1.  <br> -Ok – umožňuje filtrovat, že odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Warning - filtr, že hodnota vstupu odpovídá k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který se shoduje s hodnotou elementu HealthState Chyba vstupu. Hodnota je 8.  <br> -All - filtr, který odpovídá s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-partition-info"></a>informace o oddílu sfctl
Získá informace o oddílu Service Fabric.

Získá informace o zadaný oddíl. Odpověď obsahuje ID oddílu, informace o dělení schéma, klíče nepodporuje oddílu, stav, stav a další podrobnosti o oddílu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [povinné] | Identita oddílu. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-partition-list"></a>sfctl seznam oddílů
Získá seznam oddílů služby Service Fabric.

Odpověď obsahuje ID oddílu, informace o dělení schéma, klíče nepodporuje oddílu, stav, stav a další podrobnosti o oddílu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id služby [povinné] | Identita služby. Toto ID se obvykle celý název služby bez "fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "myapp\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| --token pro pokračování | Parametr tokenu pokračování slouží k získání další sadu výsledků. Token pro pokračování se neprázdná hodnota je zahrnutý v odpovědi rozhraní API, když výsledky ze systému se nevejdou do odpověď o jedné. Když je tato hodnota předána na další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak pokračovací token neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódování URL. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-partition-load"></a>sfctl zatížení oddílu
Získá informace o načtení zadaného oddílu Service Fabric.

Vrátí informace o načtení zadaný oddíl. Odpověď obsahuje seznam sestav zatížení pro oddíl Service Fabric. Každá sestava obsahuje zatížení metriky název, hodnotu a poslední vykazovaného časového ve standardu UTC.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [povinné] | Identita oddílu. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-partition-load-reset"></a>sfctl oddílu načíst obnovit
Obnoví aktuální zatížení oddílů Service Fabric.

Obnoví aktuální zatížení oddílů Service Fabric zatížení výchozí služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [povinné] | Identita oddílu. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-partition-quorum-loss"></a>sfctl oddílu-ztráty kvora
Indukuje ztráty kvora pro daný stavové služby oddíl.

Toto rozhraní API je vhodný pro situace ke ztrátě dočasné kvora pro vaši službu. Volání rozhraní API GetQuorumLossProgress s stejným OperationId k vrácení informací o operaci spustit pomocí tohoto rozhraní API. Lze volat pouze pro stavové trvalé (HasPersistedState == true) služby.  Nepoužívejte toto rozhraní API na služby bezstavové nebo stavové služby pouze v paměti.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id operace [povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  To je předán do odpovídajícího GetProgress rozhraní API. |
| – id oddílu [povinné] | Identita oddílu. |
| --kvora – ztráta – doba trvání [povinné] | Množství času, pro kterou se uchovají oddílu ve ztrátě kvora.  To je třeba zadat v řádu sekund. |
| --– ztráta Režim kvora [povinné] | Tento výčet je předán rozhraní API StartQuorumLoss označíte, jaký typ vyvolávat ke ztrátě kvora. |
| – id služby [povinné] | Identita služby. Toto ID se obvykle celý název služby bez "fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "myapp\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl oddílu kvora ztrátu status
Získá průběh operace ztrátou kvora v oddílu pomocí rozhraní API StartQuorumLoss spuštěna.

Získá průběh operace ztrátě kvora můžete začít StartQuorumLoss, pomocí zadané ID operace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id operace [povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  To je předán do odpovídajícího GetProgress rozhraní API. |
| – id oddílu [povinné] | Identita oddílu. |
| – id služby [povinné] | Identita služby. Toto ID se obvykle celý název služby bez "fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "myapp\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-partition-recover"></a>Obnovit oddíl sfctl
Do clusteru Service Fabric označuje, že by měl pokusí o zotavení konkrétního oddílu, který je aktuálně zablokované ve ztrátě kvora.

Tato operace by měla provést pouze v případě je známo, že není možné obnovit repliky, které jsou vypnuté. Nesprávné použití tohoto rozhraní API může způsobit ztrátu dat.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [povinné] | Identita oddílu. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-partition-recover-all"></a>sfctl oddílu recover-all
Do clusteru Service Fabric označuje, že by měl pokusí o zotavení veškeré služby (včetně systémových služeb), které jsou aktuálně zablokované ve ztrátě kvora.

Tato operace by měla provést pouze v případě je známo, že není možné obnovit repliky, které jsou vypnuté. Nesprávné použití tohoto rozhraní API může způsobit ztrátu dat.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-partition-report-health"></a>Stav sestavy oddílu sfctl
Odešle zprávu o stavu v Service Fabric oddílu.

Sestavy stav zadaný oddíl Service Fabric. Sestava může obsahovat informace o zdroji sestava stavu a na kterém je uvedena vlastnost. Sestava je odeslána do Service Fabric gateway oddíl, který se předává k úložišti stavů. Sestava může být přijal brány, ale odmítnuté úložiště stavu po další ověření. Úložiště stavu může třeba odmítnout sestavy z důvodu neplatného parametru, jako jsou zastaralé pořadové číslo. Pokud chcete zobrazit, zda byla použita sestava v health store, zkontrolujte, že sestava se zobrazí v části události.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Stav – vlastnost [povinné] | Vlastnost s informacemi o stavu. <br><br> Entita může mít sestav o stavu pro různé vlastnosti. Vlastnost je řetězec a oprava výčet umožňují flexibilní reportérka ke kategorizaci podmínku stavu, který spouští sestavu. Například reportérka s ID zdroje "LocalWatchdog" můžete monitorovat stav disku v uzlu, tak ho může hlásit vlastnost "AvailableDisk" v tomto uzlu. Stejné reportérka můžete monitorovat připojení uzlu, takže ho může hlásit vlastnost "Připojení" ve stejném uzlu. V health store tyto sestavy jsou považovány za události samostatné stavu pro zadaný uzel. Spolu s ID zdroje vlastnost jednoznačně identifikuje informací o stavu. |
| – Stav [povinné] | Možné hodnoty zahrnují\: "Neplatný", "Ok", "Upozorňující", "Chyba", "Neznámý". |
| – id oddílu [povinné] | Identita oddílu. |
| – id zdroje [povinné] | Název zdroje, který identifikuje klienta/sledovacího zařízení/systémové součásti, které vygenerovalo informací o stavu. |
| – Popis | Popis informací o stavu. <br><br> Představuje libovolný text pro přidání lidské čitelné informace o sestavě. Maximální délka řetězce popis je 4096 znaků. Pokud zadaný řetězec je delší, bude automaticky zkrácen. Při zkráceno, poslední znaky popis obsahovat značky "[zkrátila]" a celkový počet řetězec velikost je 4 096 znaků. Výskyt značky určuje uživatelům této zkrácení došlo k chybě. Všimněte si, že při zkráceno, popis má menší než 4096 znaků z původního řetězce. |
| --okamžité | Příznak, který udává, zda sestavy by měl být odesílány okamžitě. <br><br> Sestava stavu posílá do Service Fabric gateway aplikace, který se předává k úložišti stavů. Pokud okamžité nastavená na hodnotu true, zpráva se odešle okamžitě ze brána protokolu HTTP k úložišti stavů, bez ohledu na nastavení klienta fabric, které používá brána aplikace HTTP. To je užitečné pro kritické zprávy, které by měly být odeslány co nejdříve. V závislosti na načasování a jiných podmínek odesílat sestavy může stále nezdaří, pokud bránu HTTP je uzavřený nebo zprávy nelze navázat spojení s bránou. Okamžité je nastavený na hodnotu false, sestava se odesílá podle nastavení stavu klienta ze brána protokolu HTTP. Proto bude možné dávce závislosti na konfiguraci HealthReportSendInterval. Toto je doporučené nastavení, protože umožňuje stavu klienta k optimalizaci zdraví, vytváření sestav zprávy k úložišti stavů, stejně jako zpracování sestavy stavu. Ve výchozím nastavení nejsou odesílány sestavy okamžitě. |
| – vypršela platnost odebrat v případě | Hodnota, která označuje, zda sestava se odebere z health store, když jeho platnost vyprší. <br><br> Je-li nastavena hodnota true, sestava se odebere z health store po vypršení její platnosti. Pokud je nastaven na hodnotu false, sestava je považováno za chybu, pokud vypršela platnost. Hodnota této vlastnosti je ve výchozím nastavení hodnotu false. Když klienti pravidelně hlásit, nastavují by měl RemoveWhenExpired false (výchozí). Tímto způsobem je osoby podávající hlášení dochází k problémům (například zablokování) a nemůže oznamovat entity je vyhodnocena v chybě, když vyprší platnost sestava stavu. Označí entitu jako patřící do chybového stavu. |
| --pořadové číslo | Pořadové číslo pro tuto sestavu stavu jako číselný řetězec. <br><br> Pořadové číslo sestav se používá v úložišti stavů ke zjišťování zastaralých sestavy. Pokud není zadán, je číslo sekvence automaticky generované klientem stavu při přidání sestavy. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |
| – Hodnota ttl | Doba trvání, pro kterou je tato sestava stavu platná. Toto pole používá formát ISO8601 pro zadání dobu trvání. <br><br> Když klienti pravidelně hlásit, odesílají by zprávy s frekvencí vyšší než hodnota time to live. Pokud klienti nenahlásí na přechod, nastavují time to live nekonečno. Když vyprší čas TTL, událost stavu, který obsahuje informace o stavu je odebrán z health store, pokud je RemoveWhenExpired hodnotu true, nebo vyhodnocovány v chybě, pokud RemoveWhenExpired false. Pokud není zadaný, čas TTL výchozí hodnoty na nekonečnou hodnotu. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-partition-restart"></a>sfctl oddílu restartování
Toto rozhraní API se restartuje, některé nebo všechny replik nebo instancí zadaný oddíl.

Toto rozhraní API je užitečné pro testovací převzetí služeb při selhání. Pokud se používají k zaměření Bezstavová služba v oddílu, musí být RestartPartitionMode AllReplicasOrInstances. Volání rozhraní API GetPartitionRestartProgress pomocí stejným OperationId zobrazíte průběh.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id operace [povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  To je předán do odpovídajícího GetProgress rozhraní API. |
| – id oddílu [povinné] | Identita oddílu. |
| --restartování oddílu režimu [povinné] | Popište oddíly, které chcete restartovat. |
| – id služby [povinné] | Identita služby. Toto ID se obvykle celý název služby bez "fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "myapp\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-partition-restart-status"></a>sfctl oddílu restart-status
Získá průběh operace PartitionRestart začít používat StartPartitionRestart.

Získá průběh PartitionRestart začít StartPartitionRestart pomocí zadané ID operace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id operace [povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  To je předán do odpovídajícího GetProgress rozhraní API. |
| – id oddílu [povinné] | Identita oddílu. |
| – id služby [povinné] | Identita služby. Toto ID se obvykle celý název služby bez "fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "myapp\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-partition-svc-name"></a>sfctl svc název oddílu
Získá název služby Service Fabric pro oddíl.

Získá název služby pro zadaný oddíl. 404 chyba je vrácena, pokud ID oddílu neexistuje v clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [povinné] | Identita oddílu. |
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
- [Instalační program](service-fabric-cli.md) příkazového řádku Service Fabric.
- Další informace o použití nástroje příkazového řádku Service Fabric pomocí [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).
