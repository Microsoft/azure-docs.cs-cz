---
title: Služba Azure Service Fabric rozhraní příkazového řádku - sfctl | Microsoft Docs
description: Popisuje příkazy služby sfctl Service Fabric rozhraní příkazového řádku.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/23/2018
ms.author: ryanwi
ms.openlocfilehash: 5b30d3732ff00e5bb79e2d58a9f0b3e5b29dedf8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-service"></a>sfctl service
Vytvořit, odstranit a spravovat službu, typů služeb a balíčky služeb.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
|    název aplikace       | Získá název aplikace Service Fabric pro službu.|
|    seznam balíčků kódu | Získá seznam balíčků kód nasazen v Service Fabric uzlu.|
|    vytvoření         | Vytvoří zadané služby Service Fabric z popisu.|
|    odstraňovat         | Odstraní existující službu Service Fabric.|
|    Typ nasazení  | Získá informace o typu určenou službu aplikace nasazené na uzlu v clusteru Service Fabric.|
|    nasazení seznam typů| Získá seznam obsahující informace o typech služby z aplikace nasazené na uzlu v clusteru Service Fabric.|
|    description    | Získá popis existující službu Service Fabric.|
|Get kontejneru protokoly| Získá kontejner protokoly pro kontejner nasazené na uzlu Service Fabric.|
|    stav         | Získá stav zadané služby Service Fabric.|
|    Informace o           | Získá informace o konkrétní službu, které patří do aplikace Service Fabric.|
|    Seznam           | Získá informace o všech služeb, které patří k aplikaci určeného ID aplikace.|
|    Manifest       | Získá manifest popisující typ služby.|
|    nasazení balíčku | Stahování balíčků přidružených manifest zadané služby do mezipaměti bitové kopie na určeného uzlu.|
|    package-health | Získá informace o stavu balíčku služby pro konkrétní aplikace nasazené pro uzel Service Fabric a aplikace.|
|    package-info   | Získá seznam balíčků služby nasazené na uzlu Service Fabric odpovídající zadanému názvu.|
|    seznam balíčků   | Získá seznam balíčků služby nasazené na uzlu Service Fabric.|
|    Obnovení        | Do clusteru Service Fabric označuje, že mají pokusit o obnovení zadané služby, která je aktuálně zablokované ve ztrátě kvora.|
|    Sestava stavu  | Odešle zprávu o stavu ve službě Service Fabric.|
|    vyřešit        | Vyřešte oddíl Service Fabric.|
|    seznam typů      | Získá seznam obsahující informace o typech služby, které jsou podporovány pomocí typu zřízené aplikace v clusteru Service Fabric.|
|    Aktualizace         | Aktualizuje určenou službu pomocí popis pro danou aktualizaci.|


## <a name="sfctl-service-create"></a>vytvoření služby sfctl
Vytvoří zadané služby Service Fabric z popisu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| id – aplikace [vyžaduje]| Identita s nadřazenou aplikací. Toto je obvykle úplné ID aplikace bez ' prostředků infrastruktury:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s ' ~' znak. Například, pokud je název aplikace se fabric: / myapp/app1 ', identita aplikace by být ' Moje aplikace ~ app1' 6.0 + a ' myapp/app1' v předchozích verzích.|
| – název [vyžaduje]| Název služby. Měl by být podřízenou ID aplikace.           Toto je celý název včetně `fabric:` identifikátor URI. Například služby `fabric:/A/B` je podřízená aplikace `fabric:/A`.|
| – Typ služby [vyžaduje]| Název typu služby.|
| --Aktivace režimu     | Aktivace režimu pro balíček služby.|
| – omezení         | Omezení umístění jako řetězec. Omezení umístění jsou logické výrazy na vlastnosti uzlu a umožňují omezení služby pro konkrétní uzly v závislosti na požadavcích služby. Můžete například umístit služba na uzly, kde je blue NodeType zadat následující: "NodeColor == blue".|
| --Korelační služby  | Název cílové služby ke korelaci s.|
| – korelace         | Korelovat služby pomocí existující službu pomocí zarovnání spřažení.|
| --dns-name            | Název DNS služby, který se má vytvořit. Služba system Service Fabric DNS musí být povolena pro toto nastavení.|
| – počet instancí      | Počet instancí. To se týká pouze bezstavové služby.|
| --int-scheme          | Označuje, že služba by měla být jednotně rozděleného mezi rozsah celých čísel bez znaménka.|
| int –. schéma počet    | Počet oddílů v celé číslo klíče rozsah (pro schéma oddílu uniform celé číslo) k vytvoření.|
| int –. schéma vysoká     | Konec rozsahu klíče celých čísel, pokud se používá schéma oddílu uniform celé číslo.|
| --int-scheme-low      | Začátek rozsahu klíče celých čísel, pokud se používá schéma oddílu uniform celé číslo.|
| --metriky zatížení        | JSON kódovaný seznam metriky používá při vyrovnávání zatížení služby mezi uzly.|
| --min-replica-set-size| Minimální repliky nastavit velikost jako číslo. To se týká pouze stavové služby.|
| – náklady na přesunutí           | Určuje náklady na přesunutí pro službu. Možné hodnoty jsou: 'Nula', 'Dolní', 'Střední', 'Horní'.|
| --named-scheme        | Označuje, že služba by měla mít více oddílů s názvem.|
| --named-scheme-list   | JSON kódovaný seznamu názvů oddílu služba napříč, pokud se používá schéma s názvem oddílu.|
| – ne jako trvalý stav  | V případě hodnoty true označuje službu má žádné trvalý stav uložené na místním disku, nebo jenom ukládá stav v paměti.|
| --umístění seznamu zásad  | JSON kódovaný seznam zásad umístění služby a všech přidružených názvy domén. Zásady je možné jednu nebo více: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`.|
| --quorum-loss-wait    | Maximální doba v sekundách, pro které je povoleno oddíl ve stavu ztrátě kvora. To se týká pouze stavové služby.|
| --repliky. restartování čekání| Doba v sekundách mezi Pokud replika přestane fungovat a když je vytvořena nová replika. To se týká pouze stavové služby.|
| --singleton-scheme    | Určuje, služba by měl mít jeden oddíl nebo jinou hodnotu než oddílů služby.|
| --Úsporný režim podle repliky zachovat  | Maximální délka trvání, v sekundách, pro které pohotovostní režim jsou repliky udržovat než jsou odstraněny. To se týká pouze stavové služby.|
| --stateful            | Určuje, zda že je služba stavové služby.|
| --Bezstavové           | Určuje, zda že je služba bezstavové služby.|
| --cíl repliky nastavit velikost| Cíl repliky nastavit velikost jako číslo. To se týká pouze stavové služby.|
| časový limit – -t          | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění               | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h             | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o           | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.|
| --dotazu               | Řetězec dotazu JMESPath. Další informace a příklady naleznete v tématu http://jmespath.org/.|
| -verbose             | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="sfctl-service-delete"></a>Odstranění služby sfctl
Odstraní existující službu Service Fabric.

Odstraní existující službu Service Fabric. Služba musí být vytvořeny, než mohl být odstraněn. Ve výchozím nastavení pokusí Service Fabric v řádně ukončit službu repliky a pak odstraňte službu. Ale pokud služba je s problémy řádně zavření repliky, může trvat dlouhou dobu operace odstranění nebo zablokuje. Použijte příznak volitelné ForceRemove přeskočit řádné zavření sekvence a vynuceně odstranit službu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id služby [vyžaduje]| Identita služby. Toto je obvykle úplný název služby bez ' prostředků infrastruktury:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "~" znak. Například, pokud je název služby fabric: / myapp/app1/svc1 ", bude identita služby" Moje aplikace ~ app1 ~ svc1 "v 6.0 + a" myapp/app1/svc1"v předchozích verzích.|
| --remove silou      | Odebrání aplikace Service Fabric nebo služby vynuceně bez průchodu přes pořadí řádné vypnutí. Tento parametr slouží k vynuceně odstranit aplikace nebo služby, pro které odstranění je řádně vypršení časového limitu z důvodu problémů v kódu služby, která zabraňuje ukončení repliky.|
| časový limit – -t        | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění             | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h           | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o         | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.|
| --dotazu             | Řetězec dotazu JMESPath. Další informace a příklady naleznete v tématu http://jmespath.org/.|
| -verbose           | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="sfctl-service-description"></a>Popis služby sfctl
Získá popis existující službu Service Fabric.

Získá popis existující službu Service Fabric. Před jeho popis můžete získat, je třeba vytvořit službu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id služby [vyžaduje]| Identita služby. Toto je obvykle úplný název služby bez ' prostředků infrastruktury:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "~" znak. Například, pokud je název služby "fabric: / myapp/app1/svc1", bude identita služby "Moje aplikace ~ app1 ~ svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích.|
| časový limit – -t        | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění             | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h           | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o         | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.|
| --dotazu             | Řetězec dotazu JMESPath. Další informace a příklady naleznete v tématu http://jmespath.org/.|
| -verbose           | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="sfctl-service-health"></a>Stav služby sfctl
Získá stav zadané služby Service Fabric.

Získá informace o stavu zadané služby. EventsHealthStateFilter použijte k filtrování kolekce události stavu ohlášeny služby založené na stav v pořádku. PartitionsHealthStateFilter použít k filtrování kolekce oddílů vrátila. Pokud zadáte služba, která neexistuje v úložišti stavů, tato rutina vrátí chybu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id služby [vyžaduje]| Identita služby. Toto je obvykle úplný název služby bez ' prostředků infrastruktury:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "~" znak. Například, pokud je název služby "fabric: / myapp/app1/svc1", bude identita služby "Moje aplikace ~ app1 ~ svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích.|
| --události stavu stavu filtru | Umožňuje filtrování vrácených objektů HealthEvent kolekce na základě stavu. Možné hodnoty pro tento parametr patří celočíselná hodnota jednoho z následujících stavů. Se vrátí jenom události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadaný, jsou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnotou může být kombinací tyto hodnoty získané pomocí bitový operátor 'OR'. Například pokud zadaná hodnota je 6 všechny události s hodnotou elementu HealthState OK (2) a upozornění (4), jsou vráceny. -Výchozí – výchozí hodnota. Vyhledá všechny stav HealthState. Hodnota je nula. -None - filtr, který se neshoduje se žádnou hodnotu stavu HealthState. Použít cílem vrátit žádné výsledky v dané kolekci stavů. Hodnota je 1. -Ok - filtrujte, aby odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2. -Upozornění - filtr, hodnota odpovídá vstup k elementu HealthState upozornění. Hodnota je 4. -Chyba – filtr, který odpovídá vstup s hodnotou elementu HealthState chyby. Hodnota je 8. -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou elementu HealthState. Hodnota je 65535.|
|--statistiky vyloučení stavu     | Určuje, zda má být vrácen stav statistiky jako součást výsledků dotazu. Chcete-li hodnotu false ve výchozím nastavení. Statistiku zobrazují počet podřízených entit ve stavu Ok, upozornění a chyby.|
| --oddíly stavu stavu filtru| Umožňuje filtrování stavu objektů oddíly stavu vrátil ve výsledku dotazu stavu služby na základě jejich stavu. Možné hodnoty pro tento parametr patří celočíselná hodnota jednoho z následujících stavů. Vrátí se pouze oddíly, které odpovídají filtru. Všechny oddíly se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadaný, jsou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnotou může být kombinací tyto hodnoty získané pomocí bitový operátor 'OR'. Například pokud zadaná hodnota je "6" stav oddílů s hodnotou elementu HealthState OK (2) a upozornění (4), jsou vráceny. -Výchozí – výchozí hodnota. Vyhledá všechny stav HealthState.                  Hodnota je nula. -None - filtr, který se neshoduje se žádnou hodnotu stavu HealthState. Použít cílem vrátit žádné výsledky v dané kolekci stavů. Hodnota je 1. -Ok - filtrujte, aby odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2. -Upozornění - filtr, hodnota odpovídá vstup k elementu HealthState upozornění. Hodnota je 4. -Chyba – filtr, který odpovídá vstup s hodnotou elementu HealthState chyby. Hodnota je 8. -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou elementu HealthState. Hodnota je 65535.|
| časový limit – -t                 | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění                      | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h                    | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o                  | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.                  Výchozí: json.|
| --dotazu                      | Řetězec dotazu JMESPath. V tématu http://jmespath.org/ Další informace a příklady.|
| -verbose                    | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="sfctl-service-info"></a>informace o službě sfctl
Získá informace o konkrétní službu, které patří do aplikace Service Fabric.

Vrací informace o určenou službu náležejících k zadané aplikaci Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje]| Identita aplikace. Toto je obvykle úplný název aplikace bez ' prostředků infrastruktury:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "~" znak. Například, pokud je název aplikace "fabric: / myapp/app1", identita aplikace by být "Moje aplikace ~ app1" v 6.0 + a "myapp/app1" v předchozích verzích.|
| – id služby [vyžaduje]| Identita služby. Toto je obvykle úplný název služby bez ' prostředků infrastruktury:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "~" znak. Například, pokud je název služby "fabric: / myapp/app1/svc1", bude identita služby "Moje aplikace ~ app1 ~ svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích.|
| časový limit – -t            | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění                 | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h               | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o             | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.|
| --dotazu                 | Řetězec dotazu JMESPath. Další informace a příklady naleznete v tématu http://jmespath.org/.|
| -verbose               | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="sfctl-service-list"></a>seznam služeb sfctl
Získá informace o všech služeb, které patří k aplikaci určeného ID aplikace.

Vrací informace o všech služeb, které patří k aplikaci určeného ID aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje]| Identita aplikace. Toto je obvykle úplný název aplikace bez ' prostředků infrastruktury:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "~" znak. Například, pokud je název aplikace "fabric: / myapp/app1", identita aplikace by být "Moje aplikace ~ app1" v 6.0 + a "myapp/app1" v předchozích verzích.|
| --token pokračování    | Parametr token pokračování slouží k získání další sadu výsledků. Token pokračování s hodnotou neprázdné je zahrnutý v odpovědi rozhraní API, když výsledky ze systému nelze uložit do odpověď o jedné. Pokud je tato hodnota předaná pro další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud nejsou žádné další výsledky, pak token pro pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódovaná adresou URL.|
| --service-type-name     | Název typu služby použít k filtrování služby se dotázat na.|
| časový limit – -t            | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění                 | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h               | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o             | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.|
| --dotazu                 | Řetězec dotazu JMESPath. Další informace a příklady naleznete v tématu http://jmespath.org/.|
| -verbose               | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="sfctl-service-manifest"></a>manifest služby sfctl
Získá manifest popisující typ služby.

Získá manifest popisující typ služby. Odpověď obsahuje manifest služby XML jako řetězec.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --– Název typu aplikace – [vyžaduje]| Název typu aplikace.|
| --– verze typu aplikace – [vyžaduje]| Verze typu aplikace.|
| ---manifest – název služby [vyžaduje]| Název služby manifestu zaregistrován v rámci typ aplikace v clusteru Service Fabric.|
| časový limit – -t                      | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění                           | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h                         | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o                       | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.                       Výchozí: json.|
| --dotazu                           | Řetězec dotazu JMESPath. V tématu http://jmespath.org/ Další informace a příklady.|
| -verbose                         | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="sfctl-service-recover"></a>obnovení služby sfctl
Do clusteru Service Fabric označuje, že mají pokusit o obnovení zadané služby, která je aktuálně zablokované ve ztrátě kvora.

Do clusteru Service Fabric označuje, že mají pokusit o obnovení zadané služby, která je aktuálně zablokované ve ztrátě kvora. Tuto operaci musí provést pouze v případě repliky, které jsou vypnutí nelze obnovit. Nesprávné použití toto rozhraní API může způsobit ztrátu dat.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id služby [vyžaduje]| Identita služby. Toto je obvykle úplný název služby bez ' prostředků infrastruktury:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "~" znak. Například, pokud je název služby fabric: / myapp/app1/svc1 ", bude identita služby" Moje aplikace ~ app1 ~ svc1 "v 6.0 + a" myapp/app1/svc1"v předchozích verzích.|
| časový limit – -t        | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění             | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h           | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o         | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.|
| --dotazu             | Řetězec dotazu JMESPath. Další informace a příklady naleznete v tématu http://jmespath.org/.|
| -verbose           | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="sfctl-service-resolve"></a>Vyřešte sfctl služby
Vyřešte oddíl Service Fabric.

Oddíl služby Service Fabric, získat koncové body služby replik vyřešte.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id služby [vyžaduje]| Identita služby. Toto je obvykle úplný název služby bez ' prostředků infrastruktury:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "~" znak. Například, pokud je název služby "fabric: / myapp/app1/svc1", bude identita služby "Moje aplikace ~ app1 ~ svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích.|
| – Typ klíče oddílu| Typ klíče oddílu. Tento parametr je povinný, pokud je schéma oddílu pro službu Int64Range nebo Named. Možné hodnoty jsou následující. -Žádný (1) – označuje, zda nebyl zadaný parametr PartitionKeyValue. Toto je platná pro oddíly s dělení schéma jako Singleton. Toto je výchozí hodnota. Hodnota je 1. -Int64Range (2) – označuje, že je parametr PartitionKeyValue klíčem oddílu int64. Toto je platná pro oddíly s dělení schéma jako Int64Range. Hodnota je 2. -S názvem (3) – označuje, že je parametr PartitionKeyValue název oddílu. Toto je platná pro oddíly s dělení schéma jako pojmenované. Hodnota je 3.|
| --hodnotu klíče oddílu  | Klíč oddílu. To je potřeba, pokud je schéma oddílu pro službu Int64Range nebo Named.|
| --předchozí verzi konfigurace | Hodnota v poli verze odpovědi, který jste získali dříve. To je potřeba, pokud uživatel ví, že výsledek, který byl získali dříve je zastaralé.|
| časový limit – -t        | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění             | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h           | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o         | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.|
| --dotazu             | Řetězec dotazu JMESPath. Další informace a příklady naleznete v tématu http://jmespath.org/.|
| -verbose           | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="sfctl-service-update"></a>aktualizace služby sfctl
Aktualizuje určenou službu pomocí popis pro danou aktualizaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id služby [vyžaduje]| Chcete-li aktualizovat cílovou službu. To je obvykle úplné ID služby bez ' prostředků infrastruktury:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "~" znak. Například, pokud je název služby ' fabric: / myapp/app1/svc1 ', identita služby by ' Moje aplikace ~ app1 ~ svc1' v 6.0 + a ' myapp/app1/svc1' v předchozích verzích.|
| – omezení         | Omezení umístění jako řetězec. Omezení umístění jsou logické výrazy na vlastnosti uzlu a umožňují omezení služby pro konkrétní uzly v závislosti na požadavcích služby. Můžete například umístit služba na uzly, kde je blue NodeType zadat následující: "NodeColor == blue".|
| --Korelační služby  | Název cílové služby ke korelaci s.|
| – korelace         | Korelovat služby pomocí existující službu pomocí zarovnání spřažení.|
| – počet instancí      | Počet instancí. To se týká pouze bezstavové služby.|
| --metriky zatížení        | JSON kódovaný seznam metriky použít, když mezi uzly Vyrovnávání zatížení.|
| --min-replica-set-size| Minimální repliky nastavit velikost jako číslo. To se týká pouze stavové služby.|
| – náklady na přesunutí           | Určuje náklady na přesunutí pro službu. Možné hodnoty jsou: 'Nula', 'Dolní', 'Střední', 'Horní'.|
| --umístění seznamu zásad  | JSON kódovaný seznam zásad umístění služby a všech přidružených názvy domén. Zásady je možné jednu nebo více: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`.|
| --quorum-loss-wait    | Maximální doba v sekundách, pro které je povoleno oddíl ve stavu ztrátě kvora. To se týká pouze stavové služby.|
| --repliky. restartování čekání| Doba v sekundách mezi Pokud replika přestane fungovat a když je vytvořena nová replika. To se týká pouze stavové služby.|
| --Úsporný režim podle repliky zachovat  | Maximální délka trvání, v sekundách, pro které pohotovostní režim jsou repliky udržovat než jsou odstraněny. To se týká pouze stavové služby.|
| --stateful            | Označuje, že cílová služba je stavové služby.|
| --Bezstavové           | Označuje, že cílová služba je bezstavové služby.|
| --cíl repliky nastavit velikost| Cíl repliky nastavit velikost jako číslo. To se týká pouze stavové služby.|
| časový limit – -t          | Server časový limit v sekundách.  Výchozí: 60.|

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění               | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění.|
| – Nápověda -h             | Zobrazte tuto zprávu nápovědy a ukončení.|
| --výstup -o           | Výstupní formát.  Povolené hodnoty: formát json, jsonc, tabulce, tsv.  Výchozí: json.|
| --dotazu               | Řetězec dotazu JMESPath. Další informace a příklady naleznete v tématu http://jmespath.org/.|
| -verbose             | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly.|

## <a name="next-steps"></a>Další postup
- [Nastavit](service-fabric-cli.md) rozhraní příkazového řádku služby prostředků infrastruktury.
- Další informace o použití pomocí Service Fabric rozhraní příkazového řádku [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).
