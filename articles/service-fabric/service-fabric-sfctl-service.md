---
title: Služba Azure Service Fabric CLI - sfctl | Dokumentace Microsoftu
description: Popisuje příkazy Service Fabric CLI sfctl služby.
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
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 84c2faaf137e19d78e7e17527feb50baebf8041b
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494570"
---
# <a name="sfctl-service"></a>sfctl service
Vytvářet, odstraňovat a spravovat služby, typů služeb a balíčky služeb.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| název aplikace | Získá název aplikace Service Fabric pro službu. |
| seznam balíčků kódu | Získá seznam balíčky kódu, které jsou nasazené na uzlu Service Fabricu. |
| vytvoření | Vytvoří zadané služby Service Fabric. |
| delete | Odstraní existující službu Service Fabric. |
| Typ nasazení | Získá informace o zadané služby typu aplikace nasazená na uzlu v clusteru Service Fabric. |
| nasadit seznam typů | Získá seznam obsahující informace o typech služeb z aplikace nasazené na uzly v clusteru Service Fabric. |
| description | Získá popis existující službu Service Fabric. |
| získat protokoly kontejneru | Získá protokoly kontejneru pro nasazení v uzlu Service Fabric container. |
| stav | Získá stav zadané služby Service Fabric. |
| informace | Získá informace o konkrétní služby, které patří k aplikaci Service Fabric. |
| Seznam | Získá informace o všech služeb, které patří k aplikaci podle ID aplikace. |
| Manifest | Získá manifest popisující typ služby. |
| nasazení balíčku | Soubory ke stažení balíčků přidružených k zadané služby manifestu do bitové kopie mezipaměti na zadaný uzel. |
| package-health | Získá informace o stavu balíčku služby pro konkrétní aplikace nasazená pro uzel Service Fabric a aplikace. |
| package-info | Získá seznam balíčků služeb nasazených na odpovídající zadanému názvu uzlu Service Fabricu. |
| seznam balíčků | Získá seznam balíčků služeb nasazených na uzlu Service Fabricu. |
| Obnovit | Do clusteru Service Fabric označuje, že by měl pokusí o zotavení zadanou službu, která je aktuálně zablokované ve ztrátě kvora. |
| Stav sestavy | Odešle zprávu o stavu ve službě Service Fabric. |
| řešení | Přeložit oddíl Service Fabric. |
| seznam typů | Získá seznam obsahující informace o typech služeb, které jsou podporovány podle typu aplikací zřízených v clusteru Service Fabric. |
| update | Aktualizuje zadaný služby pomocí popis pro danou aktualizaci. |

## <a name="sfctl-service-app-name"></a>sfctl služba aplikace name
Získá název aplikace Service Fabric pro službu.

Získá název aplikace pro zadaná služba. 404 FABRIC_E_SERVICE_DOES_NOT_EXIST chyba je vrácena, pokud služba s ID poskytnutá služba neexistuje.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-service-code-package-list"></a>sfctl služby code-package-list
Získá seznam balíčky kódu, které jsou nasazené na uzlu Service Fabricu.

Získá seznam balíčků kódu, které jsou nasazené na uzlu Service Fabricu pro danou aplikaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --[povinný] název uzlu | Název uzlu. |
| – Název balíčku kódu | Název zadaný v manifestu služby, které jsou zapsaní v rámci typu aplikace v clusteru Service Fabric balíček kódu. |
| – název manifestu služby | Název manifestu služby zapsaní v rámci typu aplikace v clusteru Service Fabric. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-service-create"></a>vytvoření služby sfctl
Vytvoří zadané služby Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| [povinné] id – aplikace | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "prostředků infrastruktury\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --[povinný] název | Název služby. To by měl být podřízeným id aplikace. Toto je úplný název včetně `fabric\:` identifikátoru URI. Například služba `fabric\:/A/B` je podřízeným prvkem aplikace `fabric\:/A`. |
| – Typ služby [povinné] | Název typu služby. |
| --Aktivace režimu | Aktivace režimu pro balíček služby. |
| – omezení | Omezení umístění jako řetězec. Omezení umístění jsou logické výrazy na vlastnosti uzlu a umožňují omezení služby pro konkrétní uzly podle požadavků na službu. Například k umístění služby na uzlech, kde je modrá NodeType zadat následující\:"NodeColor == blue". |
| : korelační služby | Název cílové služby pro korelaci s. |
| --korelace | Je možné korelovat služby pomocí existující služby pomocí spřažení zarovnání. |
| --dns-name | Název DNS služby, který se má vytvořit. Systémová služba Service Fabric DNS musí být povolena pro toto nastavení. |
| --počet instancí | Počet instancí. To platí pro pouze bezstavové služby. |
| --int-scheme | Označuje, že služba by měly být rovnoměrně rozdělené mezi širokou škálou celých čísel bez znaménka. |
| int –. schéma počet | Počet oddílů uvnitř klíče rozsah celých čísel k vytvoření, pokud používáte schéma oddílu jednotného celého čísla. |
| int – schéma vysoké | Koncový rozsah klíčů celých čísel, pokud používáte schéma oddílu jednotného celého čísla. |
| --int-scheme-low | Počáteční rozsah klíčů celých čísel, pokud používáte schéma oddílu jednotného celého čísla. |
| --zatížení metrics | JSON kódovaný seznam metrik při vyrovnávání zatížení služeb mezi uzly. |
| --min-replica-set-size | Minimální repliky nastavit velikost jako číslo. To platí pro pouze stavové služby. |
| --náklady na přesunutí | Určuje, náklady na přesunutí pro službu. Možné hodnoty jsou\: "Nula", "Nízká", "Střední", "Vysoká". |
| --named-scheme | Označuje, že služba by měla mít několik pojmenovaných oddílů. |
| --named-scheme-list | JSON kódovaný seznam názvů při vytváření oddílů service, pokud používáte schéma s názvem oddílu. |
| --no trvalý stav | Při hodnotě true označuje služba nemá trvalý stav uložené na místním disku, nebo pouze ukládá stav v paměti. |
| – Seznam umístění zásad | JSON kódovaný v seznamu zásad umístění pro službu a všechna přidružená názvy domén. Zásady je možné jednu nebo více\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --quorum-loss-wait | Maximální doba trvání v sekundách, pro který oddíl se může být ve stavu ztrátě kvora. To platí pro pouze stavové služby. |
| --repliky restart-wait | Doba v sekundách mezi Pokud replika přestane fungovat a když se vytvoří novou repliku. To platí pro pouze stavové služby. |
| – zásady škálování | JSON kódovaný seznam škálování zásady pro tuto službu. |
| --singleton-scheme | Určuje službu má mít jeden oddíl nebo službu bez oddílů. |
| – samostatná podle repliky udržovat | Maximální doba trvání v sekundách, pro které pohotovostní režim repliky bude udržovat než jsou odstraněny. To platí pro pouze stavové služby. |
| --Stavová | Označuje, že služba je do stavové služby. |
| --Bezstavové | Označuje, že služba je Bezstavová služba. |
| --target--velikost sady replik | Velikost sady replik cíl jako číslo. To platí pro pouze stavové služby. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-service-delete"></a>Odstranění služby sfctl
Odstraní existující službu Service Fabric.

Služby musí být vytvořeny, aby se Dal odstranit. Ve výchozím nastavení Service Fabric se pokusí zavřete repliky služby řádné způsobem a pak odstraňte službu. Ale pokud služba je potíže řádně zavření repliky, operace odstranění může trvat dlouhou dobu nebo zablokuje. Použijte volitelný příznak ForceRemove vynuceně odstranit službu a přeskočit řádné zavření sekvence.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id služby [povinné] | Identita služby. Toto ID se obvykle celý název služby bez "fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "myapp\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| --force odebrat | Odebrání aplikace Service Fabric nebo služby vynuceně bez nutnosti kontaktovat řádné vypnutí pořadí. Tento parametr lze použít k vynuceně odstranění aplikace nebo služby, pro které delete je bezproblémové vypršení časového limitu z důvodu problémů v kódu služby, které brání zavřít replik. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-service-deployed-type"></a>sfctl služby nasazené – typ
Získá informace o zadané služby typu aplikace nasazená na uzlu v clusteru Service Fabric.

Získá seznam obsahující informace o konkrétní služby typu z aplikace nasazené na uzly v clusteru Service Fabric. Odpověď obsahuje název typu služby, jeho stav registrace, balíček kódu, který zaregistrované a aktivace ID balíčku služby. Každý záznam představuje jeden aktivace typu služby rozlišené pomocí ID aktivace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --[povinný] název uzlu | Název uzlu. |
| --service-type-name [povinné] | Určuje název typu služby Service Fabric. |
| – název manifestu služby | Název manifestu služby, chcete-li filtrovat seznam informací o typu nasazené služby. Pokud zadaná, odpověď bude obsahovat pouze informace o typech služeb, které jsou definovány v manifestu služby. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-service-deployed-type-list"></a>sfctl služby nasazené type-list
Získá seznam obsahující informace o typech služeb z aplikace nasazené na uzly v clusteru Service Fabric.

Získá seznam obsahující informace o typech služeb z aplikace nasazené na uzly v clusteru Service Fabric. Odpověď obsahuje název typu služby, jeho stav registrace, balíček kódu, který zaregistrované a aktivace ID balíčku služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --[povinný] název uzlu | Název uzlu. |
| – název manifestu služby | Název manifestu služby, chcete-li filtrovat seznam informací o typu nasazené služby. Pokud zadaná, odpověď bude obsahovat pouze informace o typech služeb, které jsou definovány v manifestu služby. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-service-description"></a>Popis služby sfctl
Získá popis existující službu Service Fabric.

Získá popis existující službu Service Fabric. Služba musí být vytvořeny, předtím, než je možné získat jeho popis.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-service-get-container-logs"></a>sfctl služba get-container-logs
Získá protokoly kontejneru pro nasazení v uzlu Service Fabric container.

Získá kontejner protokoly pro kontejner nasadit na uzlu Service Fabricu pro balíček daného kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| – Kód balíčku název-[povinné] | Název zadaný v manifestu služby, které jsou zapsaní v rámci typu aplikace v clusteru Service Fabric balíček kódu. |
| --[povinný] název uzlu | Název uzlu. |
| --service-manifest-name [povinné] | Název manifestu služby zapsaní v rámci typu aplikace v clusteru Service Fabric. |
| --předchozí | Určuje, jestli se má získat protokoly kontejneru z kontejnerů byl ukončen/dead instance balíček kódu. |
| --Funkce tail | Počet řádků k zobrazení od konce protokoly. Výchozí hodnota je 100. 'všechny zobrazíte kompletní protokoly. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-service-health"></a>sfctl service health
Získá stav zadané služby Service Fabric.

Získá informace o stavu zadané služby. EventsHealthStateFilter použijte k filtrování kolekce událostí stavu hlášené pro službu založenou na stav v pořádku. Použití PartitionsHealthStateFilter k filtrování kolekce oddíly vrátila. Pokud chcete zadat službu, která neexistuje v health store, tento požadavek vrátí chybu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id služby [povinné] | Identita služby. Toto ID se obvykle celý název služby bez "fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "myapp\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| --události stavu stavu filtru | Umožňuje filtrování vrácených objektů HealthEvent kolekce na základě stavu. Možné hodnoty pro tento parametr obsahovat celočíselnou hodnotu některého z následujících stavů. Vrátí se jenom události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadán, budou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota může být kombinací těchto hodnot, pomocí bitový operátor "OR". Například pokud zadaná hodnota je 6 všechny události s hodnotou elementu HealthState OK (2) a upozorněním (4), jsou vráceny.  <br> – Výchozí – výchozí hodnota. Odpovídá jakékoli stav HealthState. Hodnota je nula.  <br> -Žádný - filtr, který se pravděpodobně neshoduje s žádnou hodnotu stavu HealthState. Použít, aby nevracela žádné výsledky na dané kolekce stavů. Hodnota je 1.  <br> -Ok – umožňuje filtrovat, že odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Warning - filtr, že hodnota vstupu odpovídá k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který se shoduje s hodnotou elementu HealthState Chyba vstupu. Hodnota je 8.  <br> -All - filtr, který odpovídá s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --exclude statistiky stavu | Určuje, zda má být vrácen statistik stavu jako součást výsledků dotazu. Ve výchozím nastavení False. Statistiky ukazují počet podřízených entit ve stavu Ok, upozornění a chyby. |
| --oddíly stavu stavu filtru | Umožňuje filtrování oddílů stavu stavu objektů vrácené ve výsledku dotazu service health na základě jejich stavu. Možné hodnoty pro tento parametr obsahovat celočíselnou hodnotu některého z následujících stavů. Jsou vráceny pouze oddíly, které odpovídají filtru. Všechny oddíly se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadán, budou vráceny všechny položky. Hodnoty stavu se výčet založený na příznak, tak hodnota může být kombinací těchto hodnota získaná pomocí bitového operátoru 'OR'. Například pokud zadaná hodnota je 6 pak stav oddílů s hodnotou elementu HealthState OK (2) a upozorněním (4) vrátí se.  <br> – Výchozí – výchozí hodnota. Odpovídá jakékoli stav HealthState. Hodnota je nula.  <br> -Žádný - filtr, který se pravděpodobně neshoduje s žádnou hodnotu stavu HealthState. Použít, aby nevracela žádné výsledky na dané kolekce stavů. Hodnota je 1.  <br> -Ok – umožňuje filtrovat, že odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Warning - filtr, že hodnota vstupu odpovídá k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který se shoduje s hodnotou elementu HealthState Chyba vstupu. Hodnota je 8.  <br> -All - filtr, který odpovídá s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-service-info"></a>informace o službě sfctl
Získá informace o konkrétní služby, které patří k aplikaci Service Fabric.

Vrátí informace o zadané služby, které patří do zadané aplikace Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
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

## <a name="sfctl-service-list"></a>seznam služeb sfctl
Získá informace o všech služeb, které patří k aplikaci podle ID aplikace.

Vrátí informace o všech služeb, které patří k aplikaci podle ID aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --token pro pokračování | Parametr tokenu pokračování slouží k získání další sadu výsledků. Token pro pokračování se neprázdná hodnota je zahrnutý v odpovědi rozhraní API, když výsledky ze systému se nevejdou do odpověď o jedné. Když je tato hodnota předána na další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak pokračovací token neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódování URL. |
| --service-type-name | Název typu služby použít k filtrování služby dotázat. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-service-manifest"></a>sfctl manifestu služby
Získá manifest popisující typ služby.

Získá manifest popisující typ služby. Odpověď obsahuje XML manifestu služby jako řetězec.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – aplikace type-name [povinné] | Název typu aplikace. |
| --– verzi typu aplikace – [povinné] | Verze typu aplikace. |
| --service-manifest-name [povinné] | Název manifestu služby zapsaní v rámci typu aplikace v clusteru Service Fabric. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-service-package-deploy"></a>sfctl služby. nasazení balíčku
Soubory ke stažení balíčků přidružených k zadané služby manifestu do bitové kopie mezipaměti na zadaný uzel.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – aplikace-type-name [povinné] | Název manifestu aplikace pro manifest odpovídající požadovanou službu. |
| ---Typ verze aplikace [povinné] | Verze manifestu aplikace pro manifest odpovídající požadovanou službu. |
| --[povinný] název uzlu | Název uzlu. |
| --service-manifest-name [povinné] | Název manifestu služby, které jsou přidružené k balíčkům, které budou staženy. |
| – zásady sdílené složky | Seznam kódování JSON zásady sdílení. Každý prvek sdílení zásad se skládá z "name" a "obor". Název odpovídá názvu balíčku kódu, konfigurace nebo dat, která je sdílet. Rozsahem může být buď 'None', 'Vše', 'Kód', 'Config' nebo 'Data'. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-service-package-health"></a>sfctl balíčku stav služby
Získá informace o stavu balíčku služby pro konkrétní aplikace nasazená pro uzel Service Fabric a aplikace.

Získá informace o stavu balíčku služby pro konkrétní aplikace nasazená na uzlu Service Fabric. Pomocí EventsHealthStateFilter volitelně filtruje pro kolekci objektů HealthEvent hlášené pro balíček nasazené služby na základě stavu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --[povinný] název uzlu | Název uzlu. |
| --service-package-name [povinné] | Název balíčku služby. |
| --události stavu stavu filtru | Umožňuje filtrování vrácených objektů HealthEvent kolekce na základě stavu. Možné hodnoty pro tento parametr obsahovat celočíselnou hodnotu některého z následujících stavů. Vrátí se jenom události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadán, budou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota může být kombinací těchto hodnot, pomocí bitový operátor "OR". Například pokud zadaná hodnota je 6 všechny události s hodnotou elementu HealthState OK (2) a upozorněním (4), jsou vráceny.  <br> – Výchozí – výchozí hodnota. Odpovídá jakékoli stav HealthState. Hodnota je nula.  <br> -Žádný - filtr, který se pravděpodobně neshoduje s žádnou hodnotu stavu HealthState. Použít, aby nevracela žádné výsledky na dané kolekce stavů. Hodnota je 1.  <br> -Ok – umožňuje filtrovat, že odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Warning - filtr, že hodnota vstupu odpovídá k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který se shoduje s hodnotou elementu HealthState Chyba vstupu. Hodnota je 8.  <br> -All - filtr, který odpovídá s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-service-package-info"></a>sfctl služby-informace o balíčku
Získá seznam balíčků služeb nasazených na odpovídající zadanému názvu uzlu Service Fabricu.

Vrátí informace o balíčcích služby nasazené na uzlu Service Fabricu pro danou aplikaci. Tyto výsledky jsou balíčky služeb, jejichž název odpovídá přesně název balíčku služby uvedené jako parametr.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --[povinný] název uzlu | Název uzlu. |
| --service-package-name [povinné] | Název balíčku služby. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-service-package-list"></a>seznam balíčků služby sfctl
Získá seznam balíčků služeb nasazených na uzlu Service Fabricu.

Vrátí informace o balíčcích služby nasazené na uzlu Service Fabricu pro danou aplikaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --[povinný] název uzlu | Název uzlu. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-service-recover"></a>sfctl obnovení služby
Do clusteru Service Fabric označuje, že by měl pokusí o zotavení zadanou službu, která je aktuálně zablokované ve ztrátě kvora.

Do clusteru Service Fabric označuje, že by měl pokusí o zotavení zadanou službu, která je aktuálně zablokované ve ztrátě kvora. Tato operace by měla provést pouze v případě je známo, že není možné obnovit repliky, které jsou vypnuté. Nesprávné použití tohoto rozhraní API může způsobit ztrátu dat.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-service-report-health"></a>Stav sestavy služby sfctl
Odešle zprávu o stavu ve službě Service Fabric.

Sestavy stavu zadané služby Service Fabric. Sestava může obsahovat informace o zdroji sestava stavu a na kterém je uvedena vlastnost. Sestava je odeslána do Service Fabric gateway službu, která předává k úložišti stavů. Sestava může být přijal brány, ale odmítnuté úložiště stavu po další ověření. Úložiště stavu může třeba odmítnout sestavy z důvodu neplatného parametru, jako jsou zastaralé pořadové číslo. Pokud chcete zjistit, zda byla použita sestava v health store, zkontrolujte, zda je sestava zobrazena v události stavu služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Stav – vlastnost [povinné] | Vlastnost s informacemi o stavu. <br><br> Entita může mít sestav o stavu pro různé vlastnosti. Vlastnost je řetězec a oprava výčet umožňují flexibilní reportérka ke kategorizaci podmínku stavu, který spouští sestavu. Například reportérka s ID zdroje "LocalWatchdog" můžete monitorovat stav disku v uzlu, tak ho může hlásit vlastnost "AvailableDisk" v tomto uzlu. Stejné reportérka můžete monitorovat připojení uzlu, takže ho může hlásit vlastnost "Připojení" ve stejném uzlu. V health store tyto sestavy jsou považovány za události samostatné stavu pro zadaný uzel. Spolu s ID zdroje vlastnost jednoznačně identifikuje informací o stavu. |
| – Stav [povinné] | Možné hodnoty zahrnují\: "Neplatný", "Ok", "Upozorňující", "Chyba", "Neznámý". |
| – id služby [povinné] | Identita služby. <br><br> Toto je obvykle celý název služby bez "prostředky infrastruktury\:' schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název služby "prostředků infrastruktury\:/myapp/app1/svc1", bude identita služby "myapp\~app1\~svc1" 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| – id zdroje [povinné] | Název zdroje, které identifikuje součásti klienta/sledovacího zařízení/systému, který se vygenerovat informace o stavu. |
| – Popis | Popis informací o stavu. <br><br> Představuje libovolný text pro přidání lidské čitelné informace o sestavě. Maximální délka řetězce popis je 4096 znaků. Pokud zadaný řetězec je delší, bude automaticky zkrácen. Při zkráceno, poslední znaky popis obsahovat značky "[zkrátila]" a celkový počet řetězec velikost je 4 096 znaků. Výskyt značky určuje uživatelům této zkrácení došlo k chybě. Všimněte si, že při zkráceno, popis má menší než 4096 znaků z původního řetězce. |
| --okamžité | Příznak označující, zda mají být okamžitě odesílány sestavy. <br><br> Sestava stavu posílá do Service Fabric gateway aplikace, který se předává k úložišti stavů. Pokud okamžité nastavená na hodnotu true, zpráva se odešle okamžitě ze brána protokolu HTTP k úložišti stavů, bez ohledu na nastavení klienta fabric, které používá brána aplikace HTTP. To je užitečné pro kritické zprávy, které by měly být odeslány co nejdříve. V závislosti na načasování a jiných podmínek odesílat sestavy může stále nezdaří, pokud bránu HTTP je uzavřený nebo zprávy nelze navázat spojení s bránou. Okamžité je nastavený na hodnotu false, sestava se odesílá podle nastavení stavu klienta ze brána protokolu HTTP. Proto bude možné dávce závislosti na konfiguraci HealthReportSendInterval. Toto je doporučené nastavení, protože umožňuje stavu klienta k optimalizaci zdraví, vytváření sestav zprávy k úložišti stavů, stejně jako zpracování sestavy stavu. Ve výchozím nastavení nejsou odesílány sestavy okamžitě. |
| – vypršela platnost odebrat v případě | Hodnota, která označuje, zda sestava se odebere z health store, když jeho platnost vyprší. <br><br> Je-li nastavena hodnota true, sestava se odebere z health store po vypršení její platnosti. Pokud je nastaven na hodnotu false, sestava je považováno za chybu, pokud vypršela platnost. Hodnota této vlastnosti je ve výchozím nastavení hodnotu false. Když klienti pravidelně hlásit, nastavují by měl RemoveWhenExpired false (výchozí). Tímto způsobem je osoby podávající hlášení dochází k problémům (například zablokování) a nemůže oznamovat entity je vyhodnocena v chybě, když vyprší platnost sestava stavu. Označí entitu jako patřící do chybového stavu. |
| --pořadové číslo | Pořadové číslo pro tuto sestavu stavu jako číselný řetězec. <br><br> Pořadové číslo sestav se používá v úložišti stavů ke zjišťování zastaralých sestavy. Pokud není zadán, je číslo sekvence automaticky generované klientem stavu při přidání sestavy. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |
| – Hodnota ttl | Doba trvání, pro kterou je tato sestava stavu platná. Toto pole je použít formát ISO8601 pro zadání dobu trvání. <br><br> Když klienti pravidelně hlásit, odesílají by zprávy s frekvencí vyšší než hodnota time to live. Pokud klienti nenahlásí na přechod, nastavují time to live nekonečno. Když vyprší čas TTL, událost stavu, který obsahuje informace o stavu je odebrán z health store, pokud je RemoveWhenExpired hodnotu true, nebo vyhodnocovány v chybě, pokud RemoveWhenExpired false. Pokud není zadaný, čas TTL výchozí hodnoty na nekonečnou hodnotu. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-service-resolve"></a>sfctl service resolve
Přeložit oddíl Service Fabric.

Přeložit oddíl služby Service Fabric a získání koncových bodů služby replik.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id služby [povinné] | Identita služby. Toto ID se obvykle celý název služby bez "fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název služby "fabric\:/myapp/app1/svc1", bude identita služby "myapp\~app1\~svc1" v 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| --typu klíče oddílu | Typ klíče oddílu. Tento parametr je povinný, pokud je schéma oddílu služby Int64Range nebo název. Možné hodnoty jsou následující. -Žádný (1) – označuje, že není zadán parametr PartitionKeyValue. Toto je platná pro oddíly s dělením schéma jako Singleton. Toto je výchozí hodnota. Hodnota je 1. -Int64Range (2) – označuje, že je parametr PartitionKeyValue klíčem oddílu int64. Toto je platná pro dělení schéma jako Int64Range oddíly. Hodnota je 2. -S názvem (3) – označuje, že je parametr PartitionKeyValue název oddílu. Toto je platná pro oddíly s jako pojmenované schéma vytváření oddílů. Hodnota je 3. |
| --hodnotu klíče oddílu | Klíč oddílu. To je potřeba, pokud je schéma oddílu služby Int64Range nebo název. |
| – předchozí verze rsp | Hodnota v poli verze, která dříve byla přijata odpověď. Toto je nezbytné, pokud uživatel ví, že výsledek, který byl zadán dříve je zastaralá. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-service-type-list"></a>sfctl služby – seznam typů
Získá seznam obsahující informace o typech služeb, které jsou podporovány podle typu aplikací zřízených v clusteru Service Fabric.

Získá seznam obsahující informace o typech služeb, které jsou podporovány podle typu aplikací zřízených v clusteru Service Fabric. Zadaná aplikace typu, musí existovat. Jinak se vrátí stav 404.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – aplikace type-name [povinné] | Název typu aplikace. |
| --– verzi typu aplikace – [povinné] | Verze typu aplikace. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-service-update"></a>aktualizace služby sfctl
Aktualizuje zadaný služby pomocí popis pro danou aktualizaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id služby [povinné] | Identita služby. Toto je obvykle celý název služby bez "prostředky infrastruktury\:' schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název služby "prostředků infrastruktury\:/myapp/app1/svc1", bude identita služby "myapp\~app1\~svc1" 6.0 + a "myapp/app1/svc1" v předchozích verzích. |
| – omezení | Omezení umístění jako řetězec. Omezení umístění jsou logické výrazy na vlastnosti uzlu a umožňují omezení služby pro konkrétní uzly podle požadavků na službu. Například k umístění služby na uzlech, kde je modrá NodeType zadat následující\: "NodeColor == blue". |
| : korelační služby | Název cílové služby pro korelaci s. |
| --korelace | Je možné korelovat služby pomocí existující služby pomocí spřažení zarovnání. |
| --počet instancí | Počet instancí. To platí pro pouze bezstavové služby. |
| --zatížení metrics | JSON kódovaný seznam metrik nepoužívá, pokud mezi uzly Vyrovnávání zatížení. |
| --min-replica-set-size | Minimální repliky nastavit velikost jako číslo. To platí pro pouze stavové služby. |
| --náklady na přesunutí | Určuje, náklady na přesunutí pro službu. Možné hodnoty jsou\: "Nula", "Nízká", "Střední", "Vysoká". |
| – Seznam umístění zásad | JSON kódovaný v seznamu zásad umístění pro službu a všechna přidružená názvy domén. Zásady je možné jednu nebo více\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --quorum-loss-wait | Maximální doba trvání v sekundách, pro který oddíl se může být ve stavu ztrátě kvora. To platí pro pouze stavové služby. |
| --repliky restart-wait | Doba v sekundách mezi Pokud replika přestane fungovat a když se vytvoří novou repliku. To platí pro pouze stavové služby. |
| – zásady škálování | JSON kódovaný seznam škálování zásady pro tuto službu. |
| – samostatná podle repliky udržovat | Maximální doba trvání v sekundách, pro které pohotovostní režim repliky bude udržovat než jsou odstraněny. To platí pro pouze stavové služby. |
| --Stavová | Označuje, že cílová služba je do stavové služby. |
| --Bezstavové | Označuje, že cílová služba je Bezstavová služba. |
| --target--velikost sady replik | Velikost sady replik cíl jako číslo. To platí pro pouze stavové služby. |
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
- [Nastavit](service-fabric-cli.md) Service Fabric CLI.
- Další informace o použití nástroje příkazového řádku Service Fabric pomocí [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).
