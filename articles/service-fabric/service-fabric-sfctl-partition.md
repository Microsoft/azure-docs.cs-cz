---
title: Azure Service Fabric rozhraní příkazového řádku - sfctl partition | Microsoft Docs
description: Popisuje příkazy Service Fabric rozhraní příkazového řádku sfctl oddílu.
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
ms.openlocfilehash: a9455683c5fad7fad4dda62fd967da617d8a8496
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763642"
---
# <a name="sfctl-partition"></a>sfctl partition
Dotazování a správu oddílů pro libovolnou službu.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| ztrátě dat. | Toto rozhraní API způsobí ztrátu dat pro zadaný oddíl. |
| Stav ztráty dat | Získá průběh operace ztráty dat oddílu pomocí rozhraní API StartDataLoss spuštěna. |
| stav | Získá stav zadaný oddíl Service Fabric. |
| informace | Získá informace o oddílu Service Fabric. |
| Seznam | Získá seznam oddílů služby Service Fabric. |
| načítání | Získá načíst informace o zadané oddílu Service Fabric. |
| Resetování zatížení | Obnoví aktuální zatížení oddílu Service Fabric. |
| ztrátě kvora | Indukuje ztrátě kvora pro daný stavové služby oddíl. |
| kvora. ke ztrátě stavu | Získá průběh operace ztráty kvora v oddílu pomocí rozhraní API StartQuorumLoss spuštěna. |
| Obnovení | Do clusteru Service Fabric označuje, že mají pokusit o obnovení na konkrétní oddíl, který je aktuálně zablokované ve ztrátě kvora. |
| Obnovit všechny | Do clusteru Service Fabric označuje, že mají pokusit o obnovení služby (včetně systémových služeb), které jsou aktuálně zablokované ve ztrátě kvora. |
| Sestava stavu | Odešle zprávu o stavu v oddílu Service Fabric. |
| Restartování | Toto rozhraní API se restartuje, některé nebo všechny repliky nebo instancí zadaný oddíl. |
| restart stavu | Získá průběh operace PartitionRestart pomocí StartPartitionRestart spuštěna. |
| svc-name | Získá název služby Service Fabric pro oddíl. |

## <a name="sfctl-partition-data-loss"></a>sfctl oddílu-ztrátě dat.
Toto rozhraní API způsobí ztrátu dat pro zadaný oddíl.

Spustí volání rozhraní API OnDataLossAsync oddílu.  Toto rozhraní API způsobí ztrátu dat pro zadaný oddíl. Spustí volání rozhraní API OnDataLoss oddílu. Skutečné únikem bude záviset na zadaný DataLossMode. <br> PartialDataLoss - pouze kvorum repliky, se odeberou a OnDataLoss se aktivuje pro oddíl, ale skutečný únikem závisí na přítomnost během letu replikace. <br>FullDataLoss – všechny repliky byly odebrány proto dojde ke ztrátě všech dat a OnDataLoss se aktivuje. <br>Toto rozhraní API by měla být volána pouze pomocí stavové služby jako cíl. Volající toto rozhraní API službou systému jako cíl se nedoporučuje. 
> [!NOTE]
> Jakmile se toto rozhraní API byla volána, nelze vrátit. Volání metody CancelOperation pouze zastavit provádění a vyčištění stavu interní systému. Pokud příkaz dospěla dostatečně způsobí ztrátu dat, se nebude obnovit data. Volání rozhraní API GetDataLossProgress pomocí stejné OperationId k vrácení informací o operaci začít s toto rozhraní API.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --data ztrátě mode [vyžaduje] | Tento výčet je předán do rozhraní API StartDataLoss označíte, jaký typ dojít ke ztrátě dat na vyvolat. |
| – id operace [vyžaduje] | Identifikátor GUID, který identifikuje volání toto rozhraní API.  To je předán do odpovídající GetProgress API. |
| – id oddílu [vyžaduje] | Identita oddílu. |
| – id služby [vyžaduje] | Identita služby. Toto je obvykle úplný název služby bez ' prostředků infrastruktury\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "Moje aplikace\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl oddílu data ztrátě status
Získá průběh operace ztráty dat oddílu pomocí rozhraní API StartDataLoss spuštěna.

Získá průběh operace ztráty dat začít s StartDataLoss, pomocí OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id operace [vyžaduje] | Identifikátor GUID, který identifikuje volání toto rozhraní API.  To je předán do odpovídající GetProgress API. |
| – id oddílu [vyžaduje] | Identita oddílu. |
| – id služby [vyžaduje] | Identita služby. Toto je obvykle úplný název služby bez ' prostředků infrastruktury\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "Moje aplikace\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-partition-health"></a>sfctl oddílu stavu
Získá stav zadaný oddíl Service Fabric.

Získá informace o stavu zadaného oddílu. EventsHealthStateFilter použijte k filtrování kolekce události stavu ohlášeny služby založené na stav v pořádku. ReplicasHealthStateFilter použijte k filtrování kolekce objektů ReplicaHealthState v oddílu. Pokud zadáte oddílu, který neexistuje v úložišti stavů, tento požadavek vrátí chybu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [vyžaduje] | Identita oddílu. |
| --události stavu stavu filtru | Umožňuje filtrování vrácených objektů HealthEvent kolekce na základě stavu. Možné hodnoty pro tento parametr patří celočíselná hodnota jednoho z následujících stavů. Se vrátí jenom události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadaný, jsou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnotou může být kombinací tyto hodnoty získané pomocí bitový operátor 'OR'. Například pokud zadaná hodnota je 6 všechny události s hodnotou elementu HealthState OK (2) a upozornění (4), jsou vráceny.  <br> -Výchozí – výchozí hodnota. Vyhledá všechny stav HealthState. Hodnota je nula.  <br> -None - filtr, který se neshoduje se žádnou hodnotu stavu HealthState. Použít cílem vrátit žádné výsledky v dané kolekci stavů. Hodnota je 1.  <br> -Ok - filtrujte, aby odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Upozornění - filtr, hodnota odpovídá vstup k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který odpovídá vstup s hodnotou elementu HealthState chyby. Hodnota je 8.  <br> -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --statistiky vyloučení stavu | Určuje, zda má být vrácen stav statistiky jako součást výsledků dotazu. Chcete-li hodnotu false ve výchozím nastavení. Statistiku zobrazují počet podřízených entit ve stavu Ok, upozornění a chyby. |
| --repliky stavu stavu filtru | Umožňuje filtrování kolekce objektů ReplicaHealthState v oddílu. Hodnota je možné získat od členů nebo bitové operace u členů HealthStateFilter. Bude vrácen pouze u replik, které odpovídají filtru. Všechny repliky se použije k vyhodnocení agregovaný stav v pořádku. Pokud není zadáno, budou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnotou může být kombinací tyto hodnoty získané pomocí bitový operátor 'OR'. Například pokud zadaná hodnota je 6 pak všechny události s hodnotou elementu HealthState OK (2) a upozornění (4), bude vrácen. Možné hodnoty pro tento parametr patří celočíselná hodnota jednoho z následujících stavů.  <br> -Výchozí – výchozí hodnota. Vyhledá všechny stav HealthState. Hodnota je nula.  <br> -None - filtr, který se neshoduje se žádnou hodnotu stavu HealthState. Použít cílem vrátit žádné výsledky v dané kolekci stavů. Hodnota je 1.  <br> -Ok - filtrujte, aby odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Upozornění - filtr, hodnota odpovídá vstup k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který odpovídá vstup s hodnotou elementu HealthState chyby. Hodnota je 8.  <br> -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-partition-info"></a>informace o oddílu sfctl
Získá informace o oddílu Service Fabric.

Získá informace o zadaný oddíl. Odpověď obsahuje ID oddílu, informace o vytváření oddílů schéma, klíče nepodporuje oddílu, stav, stav a další podrobnosti o oddílu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [vyžaduje] | Identita oddílu. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-partition-list"></a>seznam oddílů sfctl
Získá seznam oddílů služby Service Fabric.

Získá seznam oddílů služby Service Fabric. Odpověď obsahuje ID oddílu, informace o vytváření oddílů schéma, klíče nepodporuje oddílu, stav, stav a další podrobnosti o oddílu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id služby [vyžaduje] | Identita služby. Toto je obvykle úplný název služby bez ' prostředků infrastruktury\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "Moje aplikace\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| --token pokračování | Parametr token pokračování slouží k získání další sadu výsledků. Token pokračování s hodnotou neprázdný je zahrnutý v odpovědi rozhraní API, když výsledky ze systému nelze uložit do odpověď o jedné. Pokud je tato hodnota předaná pro další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud nejsou žádné další výsledky, pak token pro pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódovaná adresou URL. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-partition-load"></a>zatížení oddílu sfctl
Získá načíst informace o zadané oddílu Service Fabric.

Vrací informace o zatížení zadaný oddíl. Odpověď obsahuje seznam sestav zatížení pro oddíl Service Fabric. Všechny sestavy obsahuje název metriky zatížení, hodnotu a čas poslední hlášené ve standardu UTC.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [vyžaduje] | Identita oddílu. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-partition-load-reset"></a>oddíl sfctl resetování zatížení
Obnoví aktuální zatížení oddílu Service Fabric.

Aktuální zatížení oddílu Service Fabric se obnoví na výchozí zatížení pro danou službu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [vyžaduje] | Identita oddílu. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-partition-quorum-loss"></a>sfctl oddílu-ztrátě kvora
Indukuje ztrátě kvora pro daný stavové služby oddíl.

Indukuje ztrátě kvora pro daný stavové služby oddíl.  Toto rozhraní API je užitečná pro dočasné kvora situaci ztrátě vaší služby. Volání rozhraní API GetQuorumLossProgress pomocí stejné OperationId k vrácení informací o operaci začít s toto rozhraní API. Lze volat jen na stateful trvalé (HasPersistedState == true) služby.  Nepoužívejte toto rozhraní API na bezstavové služby nebo stavové služby pouze v paměti.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id operace [vyžaduje] | Identifikátor GUID, který identifikuje volání toto rozhraní API.  To je předán do odpovídající GetProgress API. |
| – id oddílu [vyžaduje] | Identita oddílu. |
| --kvora ztrátě trvání [vyžaduje] | Množství času, pro který se zachová oddílu ve ztrátě kvora.  To je třeba zadat v sekundách. |
| --– ztráta Režim kvora [vyžaduje] | Tento výčet je předán do rozhraní API StartQuorumLoss označíte, jaký typ ztrátě kvora do vyvolat. |
| – id služby [vyžaduje] | Identita služby. Toto je obvykle úplný název služby bez ' prostředků infrastruktury\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "Moje aplikace\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl oddílu kvora ztrátě status
Získá průběh operace ztráty kvora v oddílu pomocí rozhraní API StartQuorumLoss spuštěna.

Získá průběh práce s StartQuorumLoss, pomocí zadané OperationId operace ztrátě kvora.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id operace [vyžaduje] | Identifikátor GUID, který identifikuje volání toto rozhraní API.  To je předán do odpovídající GetProgress API. |
| – id oddílu [vyžaduje] | Identita oddílu. |
| – id služby [vyžaduje] | Identita služby. Toto je obvykle úplný název služby bez ' prostředků infrastruktury\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "Moje aplikace\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-partition-recover"></a>Obnovit sfctl oddílu
Do clusteru Service Fabric označuje, že mají pokusit o obnovení na konkrétní oddíl, který je aktuálně zablokované ve ztrátě kvora.

Do clusteru Service Fabric označuje, že mají pokusit o obnovení na konkrétní oddíl, který je aktuálně zablokované ve ztrátě kvora. Tuto operaci musí provést pouze v případě se ví, že repliky, které jsou vypnutí nelze obnovit. Nesprávné použití toto rozhraní API může způsobit ztrátu dat.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [vyžaduje] | Identita oddílu. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-partition-recover-all"></a>oddíl sfctl obnovit všechny
Do clusteru Service Fabric označuje, že mají pokusit o obnovení služby (včetně systémových služeb), které jsou aktuálně zablokované ve ztrátě kvora.

Do clusteru Service Fabric označuje, že mají pokusit o obnovení služby (včetně systémových služeb), které jsou aktuálně zablokované ve ztrátě kvora. Tuto operaci musí provést pouze v případě se ví, že repliky, které jsou vypnutí nelze obnovit. Nesprávné použití toto rozhraní API může způsobit ztrátu dat.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-partition-report-health"></a>oddíl sfctl sestavy stavu
Odešle zprávu o stavu v oddílu Service Fabric.

Sestavy stavu zadaný oddíl Service Fabric. Sestava musí obsahovat informace o zdroji této sestavy stavu a vlastnosti, na kterém je zaznamenána. Sestava je odeslána do Service Fabric brány oddíl, který předává do úložiště stavu. Sestava může přijal bránou, ale byl odmítnut úložiště zdravotní po doplňující ověření. Úložiště stavu může například odmítnout sestavy z důvodu neplatný parametr, jako je zastaralé pořadové číslo. Pokud chcete zjistit, zda sestava byla použita v health store, zkontrolujte, že sestava se zobrazí v části události.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --stavu – vlastnost [vyžaduje] | Vlastnost s informacemi o stavu. <br><br> Entita může mít sestavy stavu pro různé vlastnosti. Vlastnost je řetězec a pevnou výčtu umožňuje flexibilitu ohlašování zařadit do kategorií stavu podmínku, která aktivuje sestavy. Ohlašování s ID zdroje "LocalWatchdog" například můžete monitorovat stav je k dispozici disk na uzlu, takže ho mohou zasílat zprávy o "AvailableDisk" vlastnost v tomto uzlu. Stejné ohlašování můžete monitorovat připojení k uzlu, takže ho mohou zasílat zprávy o vlastnost "Připojení" ve stejném uzlu. V health store tyto sestavy jsou považovány za samostatné stavu události pro určeného uzlu. Společně s ID zdroje vlastnost jednoznačně identifikuje informace o stavu. |
| --stavu [vyžaduje] | Možné hodnoty patří\: 'Neplatný', 'Ok', 'Upozornění', "Chyba", "Neznámý". |
| – id oddílu [vyžaduje] | Identita oddílu. |
| – id zdroje [vyžaduje] | Název zdroje, který identifikuje součásti klienta nebo sledovací zařízení nebo systému, která generuje informace o stavu. |
| – Popis | Popis informací o stavu. <br><br> Reprezentuje textové použít k přidání lidského čtení informací o sestavě. Maximální délka řetězce pro popis je 4 096 znaků. Pokud zadaný text je delší, bude automaticky zkrácen. Pokud zkrácen, poslední znaky popis obsahovat značku "[Truncated]" a velikost celkový řetězce je 4 096 znaků. Přítomnost značky naznačuje pro uživatele, že zkrácení došlo k chybě. Upozorňujeme, že pokud zkrácen, popis má méně než 4 096 znaků z původního řetězce. |
| --okamžitou | Příznak, který indikuje, zda mají být sestavy odesílány okamžitě. <br><br> Sestava stavu posílá bránu Service Fabric aplikace, který předává do úložiště stavu. Pokud Immediate je nastaven na hodnotu true, zpráva se odešle okamžitě z brány protokolu HTTP k úložišti stavů, bez ohledu na nastavení klienta fabric, které používá aplikace brány protokolu HTTP. To je užitečné pro kritické sestavy, které by měly být odeslány co nejdříve. V závislosti na načasování a jiných podmínek odesílat sestavy může stále selžou, například pokud je uzavřen bráně HTTP nebo zprávy není kontaktovat bránu. Pokud Immediate nastavena na hodnotu false, sestavy se odesílá podle nastavení stavu klienta z brány v protokolu HTTP. Proto se bude zpracovat v dávce závislosti na konfiguraci HealthReportSendInterval. Toto je doporučená nastavení, protože umožňuje stavu klienta k optimalizaci zpráv, které mají stav úložiště, jakož i zpracování sestavy stavu vykazování stavu. Ve výchozím nastavení nejsou odesílány okamžitě sestavy. |
| --odebrat Pokud platnost | Hodnota, která určuje, zda sestava je po jeho vypršení odebrat z health store. <br><br> Pokud je nastaven na hodnotu true, sestavy se odebere z health store po vypršení platnosti. Pokud je nastaven na hodnotu false, sestava je považovat za chybu, pokud vypršela platnost. Hodnota této vlastnosti je ve výchozím nastavení hodnota false. Když klienti pravidelně zprávy, nastavují by měl RemoveWhenExpired NEPRAVDA (výchozí). Tímto způsobem je zpravodaje, která se vyskytují problémy (například zablokování) a nemůžete hlásit entity je vyhodnocován v chybě, když vyprší platnost sestava stavu. Označí entitu, že je ve stavu chyby příznakem. |
| --pořadové číslo | Pořadové číslo pro tuto sestavu stavu jako číselných řetězců. <br><br> Pořadové číslo sestavy se používají ve store stavu ke zjišťování zastaralých sestavy. Pokud není zadáno, je číslo sekvence automaticky generovaný klientem stavu při přidání sestavy. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |
| Hodnota ttl-- | Doba trvání, pro který je tato sestava stavu platný. Toto pole pro zadání trvání používá ve formátu ISO 8601. <br><br> Když klienti pravidelně zprávy, se musí odesílat zprávy s četností vyšší než hodnota time to live. Pokud klienti zprávy na přechod, nastavují TTL k nekonečné. Když vyprší platnost hodnota time to live, událost stavu, který obsahuje informace o stavu je buď odebrán z health store, pokud je RemoveWhenExpired hodnotu true, nebo vyhodnocen na chyby, pokud RemoveWhenExpired false. Pokud není zadaný, hodnota time to live výchozí hodnoty na nekonečnou hodnotu. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-partition-restart"></a>sfctl oddílu restartování
Toto rozhraní API se restartuje, některé nebo všechny repliky nebo instancí zadaný oddíl.

Toto rozhraní API je užitečné pro testování převzetí služeb při selhání. Pokud se používají k zaměření bezstavové služby oddíl, musí být RestartPartitionMode AllReplicasOrInstances. Volání rozhraní API GetPartitionRestartProgress pomocí stejné OperationId zobrazíte průběh.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id operace [vyžaduje] | Identifikátor GUID, který identifikuje volání toto rozhraní API.  To je předán do odpovídající GetProgress API. |
| – id oddílu [vyžaduje] | Identita oddílu. |
| – restartování oddílu mode [vyžaduje] | Popisují oddílů pro restartování. |
| – id služby [vyžaduje] | Identita služby. Toto je obvykle úplný název služby bez ' prostředků infrastruktury\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "Moje aplikace\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-partition-restart-status"></a>oddíl sfctl restart-status
Získá průběh operace PartitionRestart pomocí StartPartitionRestart spuštěna.

Získá průběh PartitionRestart začít s StartPartitionRestart pomocí zadané OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id operace [vyžaduje] | Identifikátor GUID, který identifikuje volání toto rozhraní API.  To je předán do odpovídající GetProgress API. |
| – id oddílu [vyžaduje] | Identita oddílu. |
| – id služby [vyžaduje] | Identita služby. Toto je obvykle úplný název služby bez ' prostředků infrastruktury\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "Moje aplikace\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-partition-svc-name"></a>Název se službou sfctl oddílu svc
Získá název služby Service Fabric pro oddíl.

Získá název služby pro zadaný oddíl. 404 chyba je vrácena, pokud ID oddílu neexistuje v clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [vyžaduje] | Identita oddílu. |
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
- [Instalační program](service-fabric-cli.md) Service Fabric rozhraní příkazového řádku.
- Další informace o použití pomocí Service Fabric rozhraní příkazového řádku [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).
