---
title: Služba Azure Service Fabric rozhraní příkazového řádku - sfctl | Microsoft Docs
description: Popisuje příkazy služby sfctl Service Fabric rozhraní příkazového řádku.
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
ms.openlocfilehash: e027bb7f61d19fc274f7eddd8f28e9e6dac099ce
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763540"
---
# <a name="sfctl-service"></a>sfctl service
Vytvořit, odstranit a spravovat službu, typů služeb a balíčky služeb.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| název aplikace | Získá název aplikace Service Fabric pro službu. |
| seznam balíčků kódu | Získá seznam balíčků kód nasazen v Service Fabric uzlu. |
| vytvoření | Vytvoří zadané služby Service Fabric. |
| odstraňovat | Odstraní existující službu Service Fabric. |
| Typ nasazení | Získá informace o typu určenou službu aplikace nasazené na uzlu v clusteru Service Fabric. |
| nasazení seznam typů | Získá seznam obsahující informace o typech služby z aplikace nasazené na uzlu v clusteru Service Fabric. |
| description | Získá popis existující službu Service Fabric. |
| Get kontejneru protokoly | Získá kontejner protokoly pro kontejner nasazené na uzlu Service Fabric. |
| stav | Získá stav zadané služby Service Fabric. |
| informace | Získá informace o službě konkrétní náležící k aplikaci Service Fabric. |
| Seznam | Získá informace o všech služeb, které patří k aplikaci určeného ID aplikace. |
| Manifest | Získá manifest popisující typ služby. |
| nasazení balíčku | Stahování balíčků přidružených manifest zadané služby do mezipaměti bitové kopie na určeného uzlu. |
| package-health | Získá informace o stavu balíčku služby pro konkrétní aplikace nasazené pro uzel Service Fabric a aplikace. |
| package-info | Získá seznam balíčků služby nasazené na uzlu Service Fabric odpovídající zadanému názvu. |
| seznam balíčků | Získá seznam balíčků služby nasazené na uzlu Service Fabric. |
| Obnovení | Do clusteru Service Fabric označuje, že mají pokusit o obnovení určenou službu, která je aktuálně zablokované ve ztrátě kvora. |
| Sestava stavu | Odešle zprávu o stavu ve službě Service Fabric. |
| vyřešit | Vyřešte oddíl Service Fabric. |
| seznam typů | Získá seznam obsahující informace o typech služby, které jsou podporovány pomocí typu zřízené aplikace v clusteru Service Fabric. |
| Aktualizace | Aktualizuje určenou službu pomocí popis pro danou aktualizaci. |

## <a name="sfctl-service-app-name"></a>název aplikace služby sfctl
Získá název aplikace Service Fabric pro službu.

Získá název aplikace pro zadaná služba. Chybu 404 FABRIC_E_SERVICE_DOES_NOT_EXIST je vrácena, pokud služba s ID zadané služby neexistuje.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-service-code-package-list"></a>sfctl služby kódu--seznam balíčků
Získá seznam balíčků kód nasazen v Service Fabric uzlu.

Získá seznam balíčků kód nasazen v Service Fabric uzlu pro danou aplikaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| – Název uzlu [vyžaduje] | Název uzlu. |
| – Název balíčku kódu | Název zadaný v service manifest zaregistrován v rámci typ aplikace v clusteru Service Fabric balíček kódu. |
| – název manifestu služby | Název služby manifestu zaregistrován v rámci typ aplikace v clusteru Service Fabric. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-service-create"></a>vytvoření služby sfctl
Vytvoří zadané služby Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| id – aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s '\~' znak. Například, pokud je název aplikace se prostředků infrastruktury\:/myapp/app1 ', identita aplikace by se Moje aplikace\~app1' v 6.0 + a ' myapp/app1' v předchozích verzích. |
| – název [vyžaduje] | Název služby. Měl by být podřízenou ID aplikace. Toto je celý název včetně `fabric\:` identifikátor URI. Například služby `fabric\:/A/B` je podřízená aplikace `fabric\:/A`. |
| – Typ služby [vyžaduje] | Název typu služby. |
| --Aktivace režimu | Aktivace režimu pro balíček služby. |
| – omezení | Omezení umístění jako řetězec. Omezení umístění jsou logické výrazy na vlastnosti uzlu a umožňují omezení služby pro konkrétní uzly v závislosti na požadavcích služby. Můžete například umístit služba na uzly, kde je blue NodeType zadat následující\:"NodeColor == blue". |
| --Korelační služby | Název cílové služby ke korelaci s. |
| – korelace | Korelovat služby pomocí existující službu pomocí zarovnání spřažení. |
| --dns-name | Název DNS služby, který se má vytvořit. Služba system Service Fabric DNS musí být povolena pro toto nastavení. |
| – počet instancí | Počet instancí. To se týká pouze bezstavové služby. |
| --int-scheme | Označuje, že služba by měla být jednotně rozděleného mezi rozsah celých čísel bez znaménka. |
| int –. schéma počet | Počet oddílů uvnitř klíče rozsah celých čísel chcete vytvořit, pokud se používá schéma oddílu uniform celé číslo. |
| int –. schéma vysoká | Konec rozsahu klíče celých čísel, pokud se používá schéma oddílu uniform celé číslo. |
| --int-scheme-low | Začátek rozsahu klíče celých čísel, pokud se používá schéma oddílu uniform celé číslo. |
| --metriky zatížení | JSON kódovaný seznam metriky používá při vyrovnávání zatížení služby mezi uzly. |
| --min-replica-set-size | Minimální repliky nastavit velikost jako číslo. To se týká pouze stavové služby. |
| – náklady na přesunutí | Určuje náklady na přesunutí pro službu. Možné hodnoty jsou\: 'Nula', 'Dolní', 'Střední', 'Horní'. |
| --named-scheme | Označuje, že služba by měla mít více oddílů s názvem. |
| --named-scheme-list | JSON kódovaný seznamu názvů oddílu služba napříč, pokud se používá schéma s názvem oddílu. |
| – ne jako trvalý stav | V případě hodnoty true označuje službu má žádné trvalý stav uložené na místním disku, nebo jenom ukládá stav v paměti. |
| --umístění seznamu zásad | JSON kódovaný seznam zásad umístění služby a všech přidružených názvy domén. Zásady je možné jednu nebo více\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --quorum-loss-wait | Maximální doba v sekundách, pro které je povoleno oddíl ve stavu ztrátě kvora. To se týká pouze stavové služby. |
| --repliky. restartování čekání | Doba v sekundách mezi Pokud replika přestane fungovat a když je vytvořena nová replika. To se týká pouze stavové služby. |
| --škálování zásady | JSON kódovaný seznam škálování zásady pro tuto službu. |
| --singleton-scheme | Určuje, služba by měl mít jeden oddíl nebo služby bez oddílů. |
| --Úsporný režim podle repliky zachovat | Maximální délka trvání, v sekundách, pro které pohotovostní režim repliky udržovat než jsou odstraněny. To se týká pouze stavové služby. |
| --stateful | Určuje, zda že je služba stavové služby. |
| --Bezstavové | Určuje, zda že je služba bezstavové služby. |
| --cíl repliky nastavit velikost | Cíl repliky nastavit velikost jako číslo. To se týká pouze stavové služby. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-service-delete"></a>Odstranění služby sfctl
Odstraní existující službu Service Fabric.

Služba musí být vytvořeny, než mohl být odstraněn. Ve výchozím nastavení Service Fabric se pokusí v řádně ukončit službu repliky a pak odstraňte službu. Ale pokud služba je s problémy řádně zavření repliky, operace odstranění může trvat dlouhou dobu nebo zablokuje. Použijte příznak volitelné ForceRemove přeskočit řádné zavření sekvence a vynuceně odstranit službu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id služby [vyžaduje] | Identita služby. Toto je obvykle úplný název služby bez ' prostředků infrastruktury\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "Moje aplikace\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| --remove silou | Odebrání aplikace Service Fabric nebo služby vynuceně bez průchodu přes pořadí řádné vypnutí. Tento parametr slouží k vynuceně odstranit aplikace nebo služby, pro které odstranění je řádně vypršení časového limitu z důvodu problémů v kódu služby, která zabraňuje ukončení repliky. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-service-deployed-type"></a>nasazení typ sfctl služby
Získá informace o typu určenou službu aplikace nasazené na uzlu v clusteru Service Fabric.

Získá seznam obsahující informace o typu konkrétní služby z aplikace nasazené na uzlu v clusteru Service Fabric. Odpověď obsahuje název typu služby, jeho stav registrace, balíček kódu, které registrované ho a aktivaci ID balíčku služby. Každá položka představuje jeden aktivace typu služby rozlišené pomocí ID aktivace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| – Název uzlu [vyžaduje] | Název uzlu. |
| --– Název typu služby-[vyžaduje] | Určuje název typu služby Service Fabric. |
| – název manifestu služby | Název služby manifest pro filtrování seznamu informace o typu nasazené služby. -Li zadána, bude odpověď obsahovat pouze informace o typech služby, které jsou definovány v manifestu této služby. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-service-deployed-type-list"></a>sfctl služby nasazené--seznam typů
Získá seznam obsahující informace o typech služby z aplikace nasazené na uzlu v clusteru Service Fabric.

Získá seznam obsahující informace o typech služby z aplikace nasazené na uzlu v clusteru Service Fabric. Odpověď obsahuje název typu služby, jeho stav registrace, balíček kódu, které registrované ho a aktivaci ID balíčku služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| – Název uzlu [vyžaduje] | Název uzlu. |
| – název manifestu služby | Název služby manifest pro filtrování seznamu informace o typu nasazené služby. -Li zadána, bude odpověď obsahovat pouze informace o typech služby, které jsou definovány v manifestu této služby. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-service-description"></a>Popis služby sfctl
Získá popis existující službu Service Fabric.

Získá popis existující službu Service Fabric. Před jeho popis můžete získat, je třeba vytvořit službu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-service-get-container-logs"></a>sfctl get kontejneru protokoly služby
Získá kontejner protokoly pro kontejner nasazené na uzlu Service Fabric.

Získá kontejner protokoly pro kontejner nasazené v Service Fabric uzlu pro balíček daného kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --kódu balíček name [vyžaduje] | Název zadaný v service manifest zaregistrován v rámci typ aplikace v clusteru Service Fabric balíček kódu. |
| – Název uzlu [vyžaduje] | Název uzlu. |
| ---manifest – název služby [vyžaduje] | Název služby manifestu zaregistrován v rámci typ aplikace v clusteru Service Fabric. |
| --předchozí | Určuje, jestli se získat protokoly kontejneru z kontejnerů byl ukončen nebo zpráv instance balíček kódu. |
| --tail | Počet řádků určených k zobrazení od konce protokoly. Výchozí hodnota je 100. 'všechny zobrazíte kompletní protokoly. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-service-health"></a>Stav služby sfctl
Získá stav zadané služby Service Fabric.

Získá informace o stavu zadané služby. EventsHealthStateFilter použijte k filtrování kolekce události stavu ohlášeny služby založené na stav v pořádku. PartitionsHealthStateFilter použít k filtrování kolekce oddílů vrátila. Pokud zadáte služba, která neexistuje v úložišti stavů, tento požadavek vrátí chybu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id služby [vyžaduje] | Identita služby. Toto je obvykle úplný název služby bez ' prostředků infrastruktury\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "Moje aplikace\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| --události stavu stavu filtru | Umožňuje filtrování vrácených objektů HealthEvent kolekce na základě stavu. Možné hodnoty pro tento parametr patří celočíselná hodnota jednoho z následujících stavů. Se vrátí jenom události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadaný, jsou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnotou může být kombinací tyto hodnoty získané pomocí bitový operátor 'OR'. Například pokud zadaná hodnota je 6 všechny události s hodnotou elementu HealthState OK (2) a upozornění (4), jsou vráceny.  <br> -Výchozí – výchozí hodnota. Vyhledá všechny stav HealthState. Hodnota je nula.  <br> -None - filtr, který se neshoduje se žádnou hodnotu stavu HealthState. Použít cílem vrátit žádné výsledky v dané kolekci stavů. Hodnota je 1.  <br> -Ok - filtrujte, aby odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Upozornění - filtr, hodnota odpovídá vstup k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který odpovídá vstup s hodnotou elementu HealthState chyby. Hodnota je 8.  <br> -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --statistiky vyloučení stavu | Určuje, zda má být vrácen stav statistiky jako součást výsledků dotazu. Chcete-li hodnotu false ve výchozím nastavení. Statistiku zobrazují počet podřízených entit ve stavu Ok, upozornění a chyby. |
| --oddíly stavu stavu filtru | Umožňuje filtrování stavu objektů oddíly stavu vrátil ve výsledku dotazu stavu služby na základě jejich stavu. Možné hodnoty pro tento parametr patří celočíselná hodnota jednoho z následujících stavů. Vrátí se pouze oddíly, které odpovídají filtru. Všechny oddíly se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadaný, jsou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnotou může být kombinací tyto hodnoty získané pomocí bitový operátor 'OR'. Například pokud zadaná hodnota je 6 pak stav oddílů s hodnotou elementu HealthState OK (2) a upozornění (4), bude vrácen.  <br> -Výchozí – výchozí hodnota. Vyhledá všechny stav HealthState. Hodnota je nula.  <br> -None - filtr, který se neshoduje se žádnou hodnotu stavu HealthState. Použít cílem vrátit žádné výsledky v dané kolekci stavů. Hodnota je 1.  <br> -Ok - filtrujte, aby odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Upozornění - filtr, hodnota odpovídá vstup k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který odpovídá vstup s hodnotou elementu HealthState chyby. Hodnota je 8.  <br> -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-service-info"></a>informace o službě sfctl
Získá informace o službě konkrétní náležící k aplikaci Service Fabric.

Vrací informace o službě zadaný náležejících k zadané aplikaci Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
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

## <a name="sfctl-service-list"></a>seznam služeb sfctl
Získá informace o všech služeb, které patří k aplikaci určeného ID aplikace.

Vrací informace o všech služeb, které patří k aplikaci určeného ID aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --token pokračování | Parametr token pokračování slouží k získání další sadu výsledků. Token pokračování s hodnotou neprázdný je zahrnutý v odpovědi rozhraní API, když výsledky ze systému nelze uložit do odpověď o jedné. Pokud je tato hodnota předaná pro další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud nejsou žádné další výsledky, pak token pro pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódovaná adresou URL. |
| --service-type-name | Název typu služby použít k filtrování služby se dotázat na. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-service-manifest"></a>manifest služby sfctl
Získá manifest popisující typ služby.

Získá manifest popisující typ služby. Odpověď obsahuje manifest služby XML jako řetězec.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --– Název typu aplikace – [vyžaduje] | Název typu aplikace. |
| --– verze typu aplikace – [vyžaduje] | Verze typu aplikace. |
| ---manifest – název služby [vyžaduje] | Název služby manifestu zaregistrován v rámci typ aplikace v clusteru Service Fabric. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-service-package-deploy"></a>nasazení balíčku sfctl služby
Stahování balíčků přidružených manifest zadané služby do mezipaměti bitové kopie na určeného uzlu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| aplikace –--název typu [vyžaduje] | Název manifest aplikace pro manifest odpovídající požadované služby. |
| --aplikace-typ version [vyžaduje] | Verze manifestu aplikace pro manifest odpovídající požadované služby. |
| – Název uzlu [vyžaduje] | Název uzlu. |
| ---manifest – název služby [vyžaduje] | Název manifestu služby přidružené k balíčkům, které budou staženy. |
| – zásady sdílené složky | JSON kódovaný seznam sdílení zásady. Každý prvek sdílení zásad se skládá z "název" a "obor". Název odpovídá názvu balíčku kódu, konfigurace nebo data, který chcete sdílet. Obor může být 'None', 'Vše', 'Kód', 'Config' nebo 'Údaje'. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-service-package-health"></a>sfctl balíček – stav služby
Získá informace o stavu balíčku služby pro konkrétní aplikace nasazené pro uzel Service Fabric a aplikace.

Získá informace o stavu balíčku služby pro konkrétní aplikace nasazené na uzlu Service Fabric. Volitelně můžete filtrovat kolekci objektů HealthEvent ohlášeny balíček nasazené služby na základě stavu, které pomocí EventsHealthStateFilter.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| – Název uzlu [vyžaduje] | Název uzlu. |
| --balíček – název služby [vyžaduje] | Název balíčku služby. |
| --události stavu stavu filtru | Umožňuje filtrování vrácených objektů HealthEvent kolekce na základě stavu. Možné hodnoty pro tento parametr patří celočíselná hodnota jednoho z následujících stavů. Se vrátí jenom události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadaný, jsou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnotou může být kombinací tyto hodnoty získané pomocí bitový operátor 'OR'. Například pokud zadaná hodnota je 6 všechny události s hodnotou elementu HealthState OK (2) a upozornění (4), jsou vráceny.  <br> -Výchozí – výchozí hodnota. Vyhledá všechny stav HealthState. Hodnota je nula.  <br> -None - filtr, který se neshoduje se žádnou hodnotu stavu HealthState. Použít cílem vrátit žádné výsledky v dané kolekci stavů. Hodnota je 1.  <br> -Ok - filtrujte, aby odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Upozornění - filtr, hodnota odpovídá vstup k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který odpovídá vstup s hodnotou elementu HealthState chyby. Hodnota je 8.  <br> -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-service-package-info"></a>balíček služby sfctl-info
Získá seznam balíčků služby nasazené na uzlu Service Fabric odpovídající zadanému názvu.

Vrací informace o balíčky služeb, které jsou nasazené na uzlu Service Fabric pro danou aplikaci. Tyto výsledky jsou balíčky služeb, jejíž název odpovídat přesně názvu balíčku služby uvedené jako parametr.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| – Název uzlu [vyžaduje] | Název uzlu. |
| --balíček – název služby [vyžaduje] | Název balíčku služby. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-service-package-list"></a>balíček služby sfctl – seznam
Získá seznam balíčků služby nasazené na uzlu Service Fabric.

Vrací informace o balíčky služeb, které jsou nasazené na uzlu Service Fabric pro danou aplikaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| – Název uzlu [vyžaduje] | Název uzlu. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-service-recover"></a>obnovení služby sfctl
Do clusteru Service Fabric označuje, že mají pokusit o obnovení určenou službu, která je aktuálně zablokované ve ztrátě kvora.

Do clusteru Service Fabric označuje, že mají pokusit o obnovení určenou službu, která je aktuálně zablokované ve ztrátě kvora. Tuto operaci musí provést pouze v případě se ví, že repliky, které jsou vypnutí nelze obnovit. Nesprávné použití toto rozhraní API může způsobit ztrátu dat.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-service-report-health"></a>sfctl sestava – Stav služby
Odešle zprávu o stavu ve službě Service Fabric.

Sestava stavu zadané služby Service Fabric. Sestava musí obsahovat informace o zdroji této sestavy stavu a vlastnosti, na kterém je zaznamenána. Sestava je odeslána do Service Fabric brány služby, která předá k úložišti stavu. Sestava může přijal bránou, ale byl odmítnut úložiště zdravotní po doplňující ověření. Úložiště stavu může například odmítnout sestavy z důvodu neplatný parametr, jako je zastaralé pořadové číslo. Pokud chcete zobrazit, zda sestava byla použita v health store, zkontrolujte, že sestavy se zobrazí v události stavu služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --stavu – vlastnost [vyžaduje] | Vlastnost s informacemi o stavu. <br><br> Entita může mít sestavy stavu pro různé vlastnosti. Vlastnost je řetězec a pevnou výčtu umožňuje flexibilitu ohlašování zařadit do kategorií stavu podmínku, která aktivuje sestavy. Ohlašování s ID zdroje "LocalWatchdog" například můžete monitorovat stav je k dispozici disk na uzlu, takže ho mohou zasílat zprávy o "AvailableDisk" vlastnost v tomto uzlu. Stejné ohlašování můžete monitorovat připojení k uzlu, takže ho mohou zasílat zprávy o vlastnost "Připojení" ve stejném uzlu. V health store tyto sestavy jsou považovány za samostatné stavu události pro určeného uzlu. Společně s ID zdroje vlastnost jednoznačně identifikuje informace o stavu. |
| --stavu [vyžaduje] | Možné hodnoty patří\: 'Neplatný', 'Ok', 'Upozornění', "Chyba", "Neznámý". |
| – id služby [vyžaduje] | Identita služby. <br><br> Toto je obvykle úplný název služby bez ' prostředků infrastruktury\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s '\~' znak. Například, pokud je název služby, prostředků infrastruktury\:/myapp/app1/svc1', identita služby by se Moje aplikace\~app1\~svc1' v 6.0 + a ' myapp/app1/svc1' v předchozích verzích. |
| – id zdroje [vyžaduje] | Název zdroje, který identifikuje klienta/sledovací zařízení/systémová komponenta, která generuje informace o stavu. |
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

## <a name="sfctl-service-resolve"></a>Vyřešte sfctl služby
Vyřešte oddíl Service Fabric.

Oddíl služby Service Fabric získat koncové body služby replik vyřešte.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id služby [vyžaduje] | Identita služby. Toto je obvykle úplný název služby bez ' prostředků infrastruktury\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "Moje aplikace\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| – Typ klíče oddílu | Typ klíče oddílu. Tento parametr je povinný, pokud je schéma oddílu pro službu Int64Range nebo Named. Možné hodnoty jsou následující. -Žádný (1) – označuje, zda nebyl zadaný parametr PartitionKeyValue. Toto je platná pro oddíly s dělení schéma jako Singleton. Toto je výchozí hodnota. Hodnota je 1. -Int64Range (2) – označuje, že je parametr PartitionKeyValue klíčem oddílu int64. Toto je platná pro oddíly s dělení schéma jako Int64Range. Hodnota je 2. -S názvem (3) – označuje, že je parametr PartitionKeyValue název oddílu. Toto je platná pro oddíly s dělení schéma jako pojmenované. Hodnota je 3. |
| --hodnotu klíče oddílu | Klíč oddílu. To je potřeba, pokud je schéma oddílu pro službu Int64Range nebo Named. |
| --předchozí verzi konfigurace | Hodnota v poli verze odpovědi, který jste získali dříve. To je potřeba, pokud uživatel ví, že výsledek, který byl zadán dříve je zastaralé. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-service-type-list"></a>sfctl služby-seznam typů
Získá seznam obsahující informace o typech služby, které jsou podporovány pomocí typu zřízené aplikace v clusteru Service Fabric.

Získá seznam obsahující informace o typech služby, které jsou podporovány pomocí typu zřízené aplikace v clusteru Service Fabric. Typ zadané aplikace, musí existovat. Jinak se vrátí stav 404.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --– Název typu aplikace – [vyžaduje] | Název typu aplikace. |
| --– verze typu aplikace – [vyžaduje] | Verze typu aplikace. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-service-update"></a>aktualizace služby sfctl
Aktualizuje určenou službu pomocí popis pro danou aktualizaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id služby [vyžaduje] | ID služby. Toto ID je obvykle úplný název služby bez "fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název služby, prostředků infrastruktury\:/myapp/app1/svc1', identita služby by se Moje aplikace\~app1\~svc1' v 6.0 + a ' myapp/app1/svc1' v předchozích verzích. |
| – omezení | Omezení umístění jako řetězec. Omezení umístění jsou logické výrazy na vlastnosti uzlu a umožňují omezení služby pro konkrétní uzly v závislosti na požadavcích služby. Můžete například umístit služba na uzly, kde je blue NodeType zadat následující\: "NodeColor == blue". |
| --Korelační služby | Název cílové služby ke korelaci s. |
| – korelace | Korelovat služby pomocí existující službu pomocí zarovnání spřažení. |
| – počet instancí | Počet instancí. To se týká pouze bezstavové služby. |
| --metriky zatížení | JSON kódovaný seznam metriky použít, když mezi uzly Vyrovnávání zatížení. |
| --min-replica-set-size | Minimální repliky nastavit velikost jako číslo. Tato nastavení velikosti platí pro pouze stavové služby. |
| – náklady na přesunutí | Určuje náklady na přesunutí pro službu. Možné hodnoty jsou\: 'Nula', 'Dolní', 'Střední', 'Horní'. |
| --umístění seznamu zásad | JSON kódovaný seznam zásad umístění služby a všech přidružených názvy domén. Zásady je možné jednu nebo více\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --quorum-loss-wait | Maximální doba v sekundách, pro které je povoleno oddíl ve stavu ztrátě kvora. Tato doba trvání platí pro pouze stavové služby. |
| --repliky. restartování čekání | Doba v sekundách mezi Pokud replika přestane fungovat a když je vytvořena nová replika. Tato doba trvání platí pro pouze stavové služby. |
| --škálování zásady | JSON kódovaný seznam škálování zásady pro tuto službu. |
| --Úsporný režim podle repliky zachovat | Maximální délka trvání, v sekundách, pro které pohotovostní režim repliky udržovat než jsou odstraněny. Tato doba trvání platí pro pouze stavové služby. |
| --stateful | Označuje, že cílová služba je stavové služby. |
| --Bezstavové | Označuje, že cílová služba je bezstavové služby. |
| --cíl repliky nastavit velikost | Cíl repliky nastavit velikost jako číslo. Tato nastavení velikosti platí pro pouze stavové služby. |
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
