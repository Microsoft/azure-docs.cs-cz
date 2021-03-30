---
title: Služba Azure Service Fabric CLI – sfctl Service
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu služeb, typů služeb a balíčků služeb.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 426220f38e6ddfaaf8b24bf4f7d34473d881ae56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245598"
---
# <a name="sfctl-service"></a>sfctl service
Umožňuje vytvářet, odstraňovat a spravovat služby, typy služeb a balíčky služeb.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| název aplikace | Získá název aplikace Service Fabric pro službu. |
| sada Code-Package-list | Získá seznam balíčků kódu nasazených na Service Fabric uzlu. |
| vytvoření | Vytvoří zadanou službu Service Fabric. |
| delete | Odstraní existující službu Service Fabric. |
| nasazený typ | Získá informace o zadaném typu služby aplikace nasazené na uzlu v clusteru Service Fabric. |
| nasazený typ – seznam | Získá seznam obsahující informace o typech služeb z aplikací nasazených na uzlu v clusteru Service Fabric. |
| description | Získá popis existující služby Service Fabric. |
| získat protokoly kontejnerů | Získá protokoly kontejneru pro kontejner nasazený na uzlu Service Fabric. |
| nezávadnost | Získá stav zadané služby Service Fabric. |
| příjemce | Získá informace o konkrétní službě, která patří do aplikace Service Fabric. |
| list | Získá informace o všech službách, které patří do aplikace určené ID aplikace. |
| manifest | Načte manifest popisující typ služby. |
| balíček – nasazení | Stáhne balíčky přidružené k zadanému manifestu služby do mezipaměti imagí v zadaném uzlu. |
| stav balíčku | Získá informace o stavu balíčku služby pro konkrétní aplikaci nasazenou pro Service Fabric uzel a aplikaci. |
| informace o balíčku | Načte seznam balíčků služeb nasazených na Service Fabric uzlu, které odpovídají přesně zadanému názvu. |
| seznam balíčků | Načte seznam balíčků služeb nasazených na Service Fabric uzlu. |
| opravitelné | Určuje Cluster Service Fabric, který by se měl pokusit obnovit zadanou službu, která je aktuálně zablokovaná ve ztrátě kvora. |
| Sestava – stav | Odešle zprávu o stavu služby Service Fabric. |
| vyřeší | Vyřešte Service Fabric oddíl. |
| typ – seznam | Získá seznam obsahující informace o typech služeb, které jsou podporované typem zřízené aplikace v clusteru Service Fabric. |
| update | Aktualizuje zadanou službu pomocí zadaného popisu aktualizace. |

## <a name="sfctl-service-app-name"></a>název aplikace služby sfctl
Získá název aplikace Service Fabric pro službu.

Získá název aplikace pro určenou službu. Pokud služba s uvedeným ID služby neexistuje, bude vrácena chyba 404 FABRIC_E_SERVICE_DOES_NOT_EXIST.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-service-code-package-list"></a>sfctl Service Code-Package-list
Získá seznam balíčků kódu nasazených na Service Fabric uzlu.

Získá seznam balíčků kódu nasazených na Service Fabric uzlu pro danou aplikaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Node-Name [povinné] | Název uzlu |
| --Code-Package-Name | Název balíčku kódu zadaný v manifestu služby zaregistrovaný jako součást typu aplikace v clusteru Service Fabric. |
| --Service-manifest-název | Název manifestu služby registrovaného jako součást typu aplikace v clusteru Service Fabric. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-service-create"></a>Vytvoření služby sfctl
Vytvoří zadanou službu Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --App-ID [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem. Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Name [povinné] | Název služby Tato položka by měla být podřízená ID aplikace. Toto je úplný název, včetně `fabric\:` identifikátoru URI. Například služba `fabric\:/A/B` je podřízenou položkou aplikace `fabric\:/A` . |
| --Service-Type [povinné] | Název typu služby |
| --Aktivace-režim | Režim aktivace balíčku služby |
| --omezení | Omezení umístění jako řetězec. Omezení umístění jsou logické výrazy ve vlastnostech uzlu a umožňují omezení služby na konkrétní uzly na základě požadavků služby. Pokud například chcete umístit službu na uzly, kde je typ uzlu NodeType modrý, zadejte následující \: "NodeColor = = Blue". |
| --korelujed-Service | Název cílové služby, ke které se má korelovat. |
| --korelační | Proveďte korelaci služby s existující službou pomocí spřažení zarovnání. |
| --DNS-Name | Název DNS služby, která se má vytvořit Pro toto nastavení musí být povolena Service Fabric systémová služba DNS. |
| --instance-počet | Počet instancí. To platí jenom pro bezstavové služby. |
| --int-schéma | Indikuje, že by služba měla být rovnoměrně rozdělená mezi rozsah celých čísel bez znaménka. |
| --int-schéma-počet | Počet oddílů uvnitř rozsahu celočíselného klíče, který se má vytvořit, pokud se používá jednotné celé číslo schématu oddílu |
| --int-schéma-vysoká | Konec rozsahu celého čísla klíče, pokud používáte jednotné celočíselné schéma oddílu. |
| --int-schéma-nízká | Začátek rozsahu celého čísla klíče, pokud používáte jednotné celočíselné schéma oddílu. |
| --Load-metriky | Seznam metrik kódovaný ve formátu JSON, který se používá při vyrovnávání zatížení služeb napříč uzly. |
| --min-replika-set-Size | Minimální velikost sady replik jako číslo. To platí jenom pro stavové služby. |
| --Přesun-cost | Určuje náklady na přesun služby. Možné hodnoty jsou \: "Zero", "low", "Medium", "High", "VeryHigh". |
| --pojmenované schéma | Indikuje, že služba by měla mít více pojmenovaných oddílů. |
| --název-schématu-list | Seznam názvů kódovaných ve formátu JSON, které se mají rozdělit do oddílů, pokud se používá schéma pojmenovaného oddílu |
| --bez trvalého stavu | Pokud je hodnota true, znamená to, že služba nemá trvalý stav uložený na místním disku, nebo ukládá pouze stav paměti. |
| --umístění-Zásady-seznam | Seznam zásad umístění pro službu a všechny přidružené názvy domén v kódování JSON Zásady můžou být jedna nebo víc z \: `NonPartiallyPlaceService` , `PreferPrimaryDomain` , `RequireDomain` , `RequireDomainDistribution` . |
| --kvorum – ztráta – čekání | Maximální doba trvání v sekundách, po kterou může být oddíl ve stavu ztráty kvora. To platí jenom pro stavové služby. |
| --replika-restart-Wait | Doba trvání v sekundách mezi tím, kdy replika vychází a kdy se vytvoří nová replika. To platí jenom pro stavové služby. |
| --škálování – zásady | Seznam zásad škálování v kódování JSON pro tuto službu. |
| --Service-doba umístění | Doba, po kterou mohou repliky zůstat před vytvářením sestav, že sestavení je zablokováno. To platí jenom pro stavové služby. |
| --singleton-schéma | Indikuje, že služba by měla mít jeden oddíl nebo být služba, která není rozdělená na oddíly. |
| --rereplice – udržování | Maximální doba trvání v sekundách, po kterou budou pohotovostní repliky před odebráním zachovány. To platí jenom pro stavové služby. |
| --Stavová | Označuje, že služba je stavová služba. |
| – bez stavu | Indikuje, že služba je Bezstavová služba. |
| --Target-replika-set-Size | Velikost cílové sady replik jako číslo. To platí jenom pro stavové služby. |
| --Timeout-t | Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-service-delete"></a>odstranění služby sfctl
Odstraní existující službu Service Fabric.

Aby bylo možné službu odstranit, je nutné ji vytvořit. Ve výchozím nastavení se Service Fabric pokusí zavřít repliky služby řádným způsobem a pak tuto službu odstranit. Pokud ale dojde k řádnému zavření repliky služby, operace odstranění může trvat dlouhou dobu nebo se zablokuje. Pomocí volitelného příznaku ForceRemove přeskočíte plynulé ukončení sekvence a vynuceně odstraníte službu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Service-ID [povinné] | Identita služby. Toto ID je obvykle úplný název služby bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Pokud je například název služby "Fabric \: /MyApp/app1/svc1", identita služby by byla "MyApp \~ app1 \~ svc1" ve verzi 6.0 + a "MyApp/app1/svc1" v předchozích verzích. |
| --Force-Remove | Odeberte Service Fabric aplikaci nebo službu vynuceně, aniž byste museli projít sekvenci řádného vypnutí. Tento parametr lze použít k vynucenému odstranění aplikace nebo služby, pro kterou odstranění probíhá časování z důvodu problémů v kódu služby, který brání řádnému zavření replik. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-service-deployed-type"></a>nasazený typ služby sfctl
Získá informace o zadaném typu služby aplikace nasazené na uzlu v clusteru Service Fabric.

Získá seznam obsahující informace o konkrétním typu služby z aplikací nasazených na uzlu v clusteru Service Fabric. Odpověď obsahuje název typu služby, jeho stav registrace, balíček kódu, který zaregistroval a ID aktivace balíčku služby. Každá položka představuje jednu aktivaci typu služby odlišnou od ID aktivace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Node-Name [povinné] | Název uzlu |
| --Service-Type-Name [povinné] | Určuje název typu Service Fabric služby. |
| --Service-manifest-název | Název manifestu služby pro filtrování seznamu nasazených informací o typu služby. Je-li tento parametr zadán, bude odpověď obsahovat pouze informace o typech služeb, které jsou definovány v tomto manifestu služby. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-service-deployed-type-list"></a>nasazený typ služby sfctl – seznam
Získá seznam obsahující informace o typech služeb z aplikací nasazených na uzlu v clusteru Service Fabric.

Získá seznam obsahující informace o typech služeb z aplikací nasazených na uzlu v clusteru Service Fabric. Odpověď obsahuje název typu služby, jeho stav registrace, balíček kódu, který zaregistroval a ID aktivace balíčku služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Node-Name [povinné] | Název uzlu |
| --Service-manifest-název | Název manifestu služby pro filtrování seznamu nasazených informací o typu služby. Je-li tento parametr zadán, bude odpověď obsahovat pouze informace o typech služeb, které jsou definovány v tomto manifestu služby. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-service-description"></a>Popis služby sfctl
Získá popis existující služby Service Fabric.

Získá popis existující služby Service Fabric. Aby bylo možné získat jeho popis, musí být služba vytvořena.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-service-get-container-logs"></a>protokoly sfctl služby Get-Container
Získá protokoly kontejneru pro kontejner nasazený na uzlu Service Fabric.

Získá protokoly kontejneru pro kontejner nasazený na uzlu Service Fabric pro daný balíček kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Code-Package-Name [povinné] | Název balíčku kódu zadaný v manifestu služby zaregistrovaný jako součást typu aplikace v clusteru Service Fabric. |
| --Node-Name [povinné] | Název uzlu |
| --Service-manifest-název [povinné] | Název manifestu služby registrovaného jako součást typu aplikace v clusteru Service Fabric. |
| --předchozí | Určuje, jestli se mají získat protokoly kontejneru z ukončených/mrtvých kontejnerů instance balíčku kódu. |
| --konec | Počet řádků, které se mají zobrazit na konci protokolů Výchozí hodnota je 100. All pro zobrazení úplných protokolů. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-service-health"></a>stav služby sfctl
Získá stav zadané služby Service Fabric.

Načte informace o stavu zadané služby. Pomocí EventsHealthStateFilter můžete filtrovat kolekci událostí stavu hlášených ve službě na základě stavu. Pomocí PartitionsHealthStateFilter můžete filtrovat kolekci vrácených oddílů. Pokud zadáte službu, která v Health Store neexistuje, vrátí tato žádost chybu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Service-ID [povinné] | Identita služby. Toto ID je obvykle úplný název služby bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Pokud je například název služby "Fabric \: /MyApp/app1/svc1", identita služby by byla "MyApp \~ app1 \~ svc1" ve verzi 6.0 + a "MyApp/app1/svc1" v předchozích verzích. |
| --Events – stav – filtr | Umožňuje filtrovat kolekci objektů HealthEvent vrácených na základě stavu. Možné hodnoty pro tento parametr zahrnují celočíselnou hodnotu jednoho z následujících stavů. Vrátí se pouze události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaného stavu. Pokud tento parametr nezadáte, vrátí se všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota by mohla být kombinací těchto hodnot získána pomocí bitového operátoru OR. Pokud je například zadaná hodnota 6, budou vráceny všechny události s hodnotou ' OK (2) a upozornění (4).  <br> -Výchozí-výchozí hodnota. Odpovídá jakémukoli elementu. Hodnota je nula.  <br> -None-Filter, který neodpovídá žádné hodnotě elementu. Používá se k tomu, aby se v dané kolekci stavů nevracely žádné výsledky. Hodnota je 1.  <br> -OK – filtr, který odpovídá zadanému vstupu s hodnotou podstavu OK. Hodnota je 2.  <br> -Warning-Filter, který odpovídá vstupu s upozorněním na podstavovou hodnotu. Hodnota je 4.  <br> – Filtr chyb, který odpovídá zadanému vstupu s chybou hodnoty elementu stav Hodnota je 8.  <br> -All – filtr, který odpovídá zadanému vstupu s jakoukoli hodnotou elementu. Hodnota je 65535. |
| --Exclude-Health-Statistics | Určuje, zda mají být v rámci výsledku dotazu vráceny statistiky stavu. Výchozí hodnota je false. Statistika zobrazuje počet podřízených entit ve stavu OK, varování a chyba. |
| --partitions-Health-State-Filter | Umožňuje filtrování objektů stavu oddílů vrácených v důsledku dotazu na stav služby na základě jejich stavu. Možné hodnoty pro tento parametr zahrnují celočíselnou hodnotu jednoho z následujících stavů. Vrátí se jenom oddíly, které odpovídají filtru. Všechny oddíly slouží k vyhodnocení agregovaného stavu. Pokud tento parametr nezadáte, vrátí se všechny položky. Hodnoty stavu jsou výčty založené na příznak, takže hodnota by mohla být kombinací hodnot získaných pomocí bitového operátoru OR. Například pokud je zadaná hodnota 6, pak se vrátí stav oddílů s hodnotou OK (2) a upozorněním (4).  <br> -Výchozí-výchozí hodnota. Odpovídá jakémukoli elementu. Hodnota je nula.  <br> -None-Filter, který neodpovídá žádné hodnotě elementu. Používá se k tomu, aby se v dané kolekci stavů nevracely žádné výsledky. Hodnota je 1.  <br> -OK – filtr, který odpovídá zadanému vstupu s hodnotou podstavu OK. Hodnota je 2.  <br> -Warning-Filter, který odpovídá vstupu s upozorněním na podstavovou hodnotu. Hodnota je 4.  <br> – Filtr chyb, který odpovídá zadanému vstupu s chybou hodnoty elementu stav Hodnota je 8.  <br> -All – filtr, který odpovídá zadanému vstupu s jakoukoli hodnotou elementu. Hodnota je 65535. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-service-info"></a>informace o službě sfctl
Získá informace o konkrétní službě, která patří do aplikace Service Fabric.

Vrátí informace o zadané službě patřící do určené aplikace Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
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

## <a name="sfctl-service-list"></a>seznam služeb sfctl
Získá informace o všech službách, které patří do aplikace určené ID aplikace.

Vrátí informace o všech službách, které patří do aplikace určené ID aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --pokračování-token | Parametr tokenu pokračování slouží k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API v případě, že se výsledky ze systému nevejdou do jediné odpovědi. Když se tato hodnota předává do dalšího volání rozhraní API, vrátí rozhraní API další sadu výsledků. Pokud nejsou k dispozici žádné další výsledky, token pokračování neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódovaná v adrese URL. |
| --Service-Type-Name | Název typu služby, který slouží k filtrování služeb pro dotazování. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-service-manifest"></a>manifest služby sfctl
Načte manifest popisující typ služby.

Načte manifest popisující typ služby. Odpověď obsahuje XML manifestu služby jako řetězec.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Typ aplikace-název [povinné] | Název typu aplikace |
| --Typ aplikace-verze [požadováno] | Verze typu aplikace |
| --Service-manifest-název [povinné] | Název manifestu služby registrovaného jako součást typu aplikace v clusteru Service Fabric. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-service-package-deploy"></a>balíček služby sfctl – nasazení
Stáhne balíčky přidružené k zadanému manifestu služby do mezipaměti imagí v zadaném uzlu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --App-type-name [povinné] | Název manifestu aplikace pro odpovídající požadovaný manifest služby. |
| --Typ aplikace-verze [požadováno] | Verze manifestu aplikace pro odpovídající požadovaný manifest služby. |
| --Node-Name [povinné] | Název uzlu |
| --Service-manifest-název [povinné] | Název manifestu služby přidruženého k balíčkům, které budou staženy. |
| --Share-Policy | Seznam zásad sdílení kódovaných pomocí JSON. Každý element zásad sdílení se skládá z názvu a rozsahu. Název odpovídá názvu kódu, konfigurace nebo balíčku dat, který má být sdílen. Rozsah může být "none", "All", "Code", "config" nebo "data". |
| --Timeout-t | Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-service-package-health"></a>balíček služby sfctl – stav
Získá informace o stavu balíčku služby pro konkrétní aplikaci nasazenou pro Service Fabric uzel a aplikaci.

Získá informace o stavu balíčku služby pro konkrétní aplikaci nasazenou na uzlu Service Fabric. EventsHealthStateFilter můžete použít k volitelnému filtrování kolekce objektů HealthEvent hlášených na nasazeném balíčku služby na základě stavu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Node-Name [povinné] | Název uzlu |
| --Service-Package-Name [povinné] | Název balíčku služby. |
| --Events – stav – filtr | Umožňuje filtrovat kolekci objektů HealthEvent vrácených na základě stavu. Možné hodnoty pro tento parametr zahrnují celočíselnou hodnotu jednoho z následujících stavů. Vrátí se pouze události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaného stavu. Pokud tento parametr nezadáte, vrátí se všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota by mohla být kombinací těchto hodnot získána pomocí bitového operátoru OR. Pokud je například zadaná hodnota 6, budou vráceny všechny události s hodnotou ' OK (2) a upozornění (4).  <br> -Výchozí-výchozí hodnota. Odpovídá jakémukoli elementu. Hodnota je nula.  <br> -None-Filter, který neodpovídá žádné hodnotě elementu. Používá se k tomu, aby se v dané kolekci stavů nevracely žádné výsledky. Hodnota je 1.  <br> -OK – filtr, který odpovídá zadanému vstupu s hodnotou podstavu OK. Hodnota je 2.  <br> -Warning-Filter, který odpovídá vstupu s upozorněním na podstavovou hodnotu. Hodnota je 4.  <br> – Filtr chyb, který odpovídá zadanému vstupu s chybou hodnoty elementu stav Hodnota je 8.  <br> -All – filtr, který odpovídá zadanému vstupu s jakoukoli hodnotou elementu. Hodnota je 65535. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-service-package-info"></a>balíček služby sfctl – informace
Načte seznam balíčků služeb nasazených na Service Fabric uzlu, které odpovídají přesně zadanému názvu.

Vrátí informace o balíčcích služeb nasazených na Service Fabric uzlu pro danou aplikaci. Tyto výsledky jsou balíčky služeb, jejichž název odpovídá přesně názvu balíčku služby zadanému jako parametr.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Node-Name [povinné] | Název uzlu |
| --Service-Package-Name [povinné] | Název balíčku služby. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-service-package-list"></a>balíček služby sfctl – seznam
Načte seznam balíčků služeb nasazených na Service Fabric uzlu.

Vrátí informace o balíčcích služeb nasazených na Service Fabric uzlu pro danou aplikaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Node-Name [povinné] | Název uzlu |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-service-recover"></a>obnovení služby sfctl
Určuje Cluster Service Fabric, který by se měl pokusit obnovit zadanou službu, která je aktuálně zablokovaná ve ztrátě kvora.

Určuje Cluster Service Fabric, který by se měl pokusit obnovit zadanou službu, která je aktuálně zablokovaná ve ztrátě kvora. Tato operace by měla být provedena pouze v případě, že je známo, že nelze obnovit repliky, které jsou mimo provoz. Nesprávné použití tohoto rozhraní API může způsobit ztrátu dat.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-service-report-health"></a>Sestava služby sfctl – stav
Odešle zprávu o stavu služby Service Fabric.

Hlásí stav zadané Service Fabric služby. Sestava musí obsahovat informace o zdroji sestavy stavu a vlastnosti, na které je hlášena. Zpráva se odešle do služby Service Fabric Gateway, která se přepošle na Health Store. Tuto sestavu může přijmout brána, ale Health Store po dodatečném ověření odmítnuta. Health Store například může zprávu odmítat z důvodu neplatného parametru, jako je například zastaralé pořadové číslo. Chcete-li zjistit, zda byla sestava použita v Health Store, zkontrolujte, zda se sestava zobrazí ve stavu události služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Health-vlastnost [required] | Vlastnost informací o stavu. <br><br> Entita může mít sestavy o stavu pro různé vlastnosti. Vlastnost je řetězec, nikoli pevný výčet, který umožňuje, aby se v zpravodaji mohla pružně roztřídit stav stavu, který aktivuje sestavu. Například zpravodaj s SourceId "LocalWatchdog" může monitorovat stav dostupného disku v uzlu, takže může ohlásit vlastnost "AvailableDisk" v tomto uzlu. Stejné zpravodajky můžou monitorovat připojení uzlu, takže může nahlásit vlastnost "připojení" na stejném uzlu. V Health Store jsou tyto sestavy zpracovány jako samostatné události stavu pro zadaný uzel. Společně s ID zdroje (SourceId) vlastnost jednoznačně identifikuje informace o stavu. |
| --Health-State [povinné] | Možné hodnoty zahrnují \: "Invalid", "OK", "Warning", "Error", "unknown". |
| --Service-ID [povinné] | Identita služby. <br><br> Většinou se jedná o úplný název služby bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem. Například pokud je název služby "Fabric \: /MyApp/app1/svc1", identita služby by byla "MyApp \~ app1 \~ svc1" v 6.0 + a "MyApp/app1/svc1" v předchozích verzích. |
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

## <a name="sfctl-service-resolve"></a>řešení služby sfctl
Vyřešte Service Fabric oddíl.

Chcete-li získat koncové body replik služby, přečtěte si oddíl služby Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Service-ID [povinné] | Identita služby. Toto ID je obvykle úplný název služby bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Pokud je například název služby "Fabric \: /MyApp/app1/svc1", identita služby by byla "MyApp \~ app1 \~ svc1" ve verzi 6.0 + a "MyApp/app1/svc1" v předchozích verzích. |
| --Partition-typ klíče | Typ klíče pro oddíl Tento parametr je vyžadován, pokud je schéma oddílu pro službu Int64Range nebo pojmenované. Možné hodnoty jsou následující. -None (1) – označuje, že není zadán parametr PartitionKeyValue. To platí pro oddíly se schématy dělení na typ singleton. Toto je výchozí hodnota. Hodnota je 1. -Int64Range (2) – určuje, že parametr PartitionKeyValue je klíč oddílu Int64. To platí pro oddíly, které mají schéma dělení jako Int64Range. Hodnota je 2. -Name (3) – určuje, že parametr PartitionKeyValue je název oddílu. To je platné pro oddíly se schématem dělení jako pojmenované. Hodnota je 3. |
| --Partition-klíč-hodnota | Klíč oddílu. To je vyžadováno, pokud je schéma oddílu pro službu Int64Range nebo pojmenované. Toto není ID oddílu, ale místo toho buď hodnota celočíselného klíče, nebo název ID oddílu. Pokud například vaše služba používá oddíly s rozsahy od 0 do 10, pak by PartitionKeyValue jako celé číslo v tomto rozsahu. Popis služby dotazů pro zobrazení rozsahu nebo názvu |
| --předchozí-RSP – verze | Hodnota v poli verze odpovědi, která byla dříve přijata. Tento údaj je nutný, pokud uživatel ví, že byl dříve zastaralý výsledek. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-service-type-list"></a>typ služby sfctl – seznam
Získá seznam obsahující informace o typech služeb, které jsou podporované typem zřízené aplikace v clusteru Service Fabric.

Získá seznam obsahující informace o typech služeb, které jsou podporované typem zřízené aplikace v clusteru Service Fabric. Zadaný typ aplikace musí existovat. V opačném případě se vrátí stav 404.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Typ aplikace-název [povinné] | Název typu aplikace |
| --Typ aplikace-verze [požadováno] | Verze typu aplikace |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-service-update"></a>aktualizace služby sfctl
Aktualizuje zadanou službu pomocí zadaného popisu aktualizace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Service-ID [povinné] | Identita služby. Většinou se jedná o úplný název služby bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název služby "Fabric \: /MyApp/app1/svc1", identita služby by byla "MyApp \~ app1 \~ svc1" v 6.0 + a "MyApp/app1/svc1" v předchozích verzích. |
| --omezení | Omezení umístění jako řetězec. Omezení umístění jsou logické výrazy ve vlastnostech uzlu a umožňují omezení služby na konkrétní uzly na základě požadavků služby. Pokud například chcete umístit službu na uzly, kde je typ uzlu NodeType modrý, zadejte následující \: "NodeColor = = Blue". |
| --korelujed-Service | Název cílové služby, ke které se má korelovat. |
| --korelační | Proveďte korelaci služby s existující službou pomocí spřažení zarovnání. |
| --instance-počet | Počet instancí. To platí jenom pro bezstavové služby. |
| --Load-metriky | Seznam metrik kódovaný ve formátu JSON, který se používá při vyrovnávání zatížení napříč uzly. |
| --min-replika-set-Size | Minimální velikost sady replik jako číslo. To platí jenom pro stavové služby. |
| --Přesun-cost | Určuje náklady na přesun služby. Možné hodnoty jsou \: "Zero", "low", "Medium", "High", "VeryHigh". |
| --umístění-Zásady-seznam | Seznam zásad umístění pro službu a všechny přidružené názvy domén v kódování JSON Zásady můžou být jedna nebo víc z \: `NonPartiallyPlaceService` , `PreferPrimaryDomain` , `RequireDomain` , `RequireDomainDistribution` . |
| --kvorum – ztráta – čekání | Maximální doba trvání v sekundách, po kterou může být oddíl ve stavu ztráty kvora. To platí jenom pro stavové služby. |
| --replika-restart-Wait | Doba trvání v sekundách mezi tím, kdy replika vychází a kdy se vytvoří nová replika. To platí jenom pro stavové služby. |
| --škálování – zásady | Seznam zásad škálování v kódování JSON pro tuto službu. |
| --Service-doba umístění | Doba, po kterou mohou repliky zůstat před vytvářením sestav, že sestavení je zablokováno. To platí jenom pro stavové služby. |
| --rereplice – udržování | Maximální doba trvání v sekundách, po kterou budou pohotovostní repliky před odebráním zachovány. To platí jenom pro stavové služby. |
| --Stavová | Indikuje, že cílová služba je stavová služba. |
| – bez stavu | Indikuje, že cílová služba je Bezstavová služba. |
| --Target-replika-set-Size | Velikost cílové sady replik jako číslo. To platí jenom pro stavové služby. |
| --Timeout-t | Výchozí hodnota je \: 60. |

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
