---
title: Služba Azure Service Fabric CLI- sfctl
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu služeb, typů služeb a balíčků služeb.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 696de713129ca71dd7f2451501a7cc9eca0ee9b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906233"
---
# <a name="sfctl-service"></a>sfctl service
Vytvářejte, odstraňujte a spravujte služby, typy služeb a balíčky služeb.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| název aplikace | Získá název aplikace Service Fabric pro službu. |
| seznam balíčků kódů | Získá seznam balíčků kódu nasazených v uzlu Service Fabric. |
| vytvoření | Vytvoří zadanou službu Service Fabric. |
| delete | Odstraní existující službu Service Fabric. |
| nasazený typ | Získá informace o zadaném typu služby aplikace nasazené na uzlu v clusteru Service Fabric. |
| seznam nasazených typů | Získá seznam obsahující informace o typech služeb z aplikací nasazených na uzlu v clusteru Service Fabric. |
| description | Získá popis existující služby Service Fabric služby. |
| get-kontejner-protokoly | Získá protokoly kontejneru pro kontejner nasazený v uzlu Service Fabric. |
| Zdraví | Získá stav zadané služby Service Fabric. |
| Info | Získá informace o konkrétní služby, které patří do aplikace Service Fabric. |
| list | Získá informace o všech službách, které patří do aplikace určené ID aplikace. |
| manifest | Získá manifest popisující typ služby. |
| nasazení balíčku | Stáhne balíčky přidružené k určenému manifestu služby do mezipaměti bitové kopie v zadaném uzlu. |
| zdraví balíčku | Získá informace o stavu balíčku služby pro konkrétní aplikaci nasazenou pro uzel service fabric a aplikace. |
| informace o balíčku | Získá seznam balíčků služeb nasazených v uzlu Service Fabric odpovídající přesně zadaný název. |
| seznam balíčků | Získá seznam balíčků služeb nasazených v uzlu Service Fabric. |
| Obnovit | Označuje clusteru Service Fabric, že by se měl pokusit obnovit zadanou službu, která je aktuálně zablokovaná ve ztrátě kvora. |
| zpráva-zdraví | Odešle zprávu o stavu služby Service Fabric. |
| Vyřešit | Vyřešte oddíl Service Fabric. |
| seznam typů | Získá seznam obsahující informace o typech služeb, které jsou podporovány zřízeného typu aplikace v clusteru Service Fabric. |
| update | Aktualizuje zadanou službu pomocí daného popisu aktualizace. |

## <a name="sfctl-service-app-name"></a>název aplikace služby sfctl
Získá název aplikace Service Fabric pro službu.

Získá název aplikace pro zadanou službu. 404 FABRIC_E_SERVICE_DOES_NOT_EXIST chyba je vrácena, pokud služba s ID zapředpokladu služby neexistuje.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-service-code-package-list"></a>sfctl service code-package-list
Získá seznam balíčků kódu nasazených v uzlu Service Fabric.

Získá seznam balíčků kódu nasazených v uzlu Service Fabric pro danou aplikaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --název uzlu [Povinné] | Název uzlu |
| --kód-package-name | Název balíčku kódu zadaný v manifestu služby registrovaný jako součást typu aplikace v clusteru Service Fabric. |
| --service-manifest-name --service-manifest-name --service-manifest-name --service | Název manifestu služby registrovaný jako součást typu aplikace v clusteru Service Fabric. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-service-create"></a>sfctl vytvořit službu
Vytvoří zadanou službu Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --app-id [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " . Například pokud název aplikace je\:'fabric /myapp/app1', identita aplikace\~by 'myapp app1' v 6.0+ a 'myapp/app1' v předchozích verzích. |
| --název [Povinné] | Název služby To by mělo být dítě ID aplikace. Toto je úplný `fabric\:` název včetně identifikátoru URI. Například `fabric\:/A/B` služba je podřízeným aplikací `fabric\:/A`. |
| --service-type [Povinné] | Název typu služby. |
| --aktivační režim | Režim aktivace balíčku služeb. |
| --omezení | Omezení umístění jako řetězec. Omezení umístění jsou logické výrazy na vlastnosti uzlu a umožňují omezit službu na konkrétní uzly na základě požadavků na službu. Chcete-li například umístit službu na uzly,\:kde je modrá NodeType, zadejte následující "NodeColor == blue". |
| --korelační služba | Název cílové služby, se kterou chcete korelovat. |
| --korelace | Korelujte službu s existující službou pomocí spřažení zarovnání. |
| --dns-name | Název DNS služby, která má být vytvořena. Pro toto nastavení musí být povolena systémová služba Service Fabric DNS. |
| --počet instancí | Počet instancí. To platí pouze pro služby bez státní příslušnosti. |
| --int-scheme | Označuje, že služba by měla být rovnoměrně rozdělena napříč rozsahem nepodepsaných celých čísel. |
| --int-scheme-count | Počet oddílů uvnitř rozsahu klíčů celé číslo vytvořit, pokud používáte jednotné schéma celého čísla oddílu. |
| --int-scheme-high | Konec rozsahu celé číslo klíče, pokud používáte jednotné schéma celého čísla oddílu. |
| --int-scheme-low | Začátek rozsahu celé číslo klíče, pokud používáte jednotné schéma celého čísla oddílu. |
| --load-metrics | JSON kódovaný seznam metrik používaných při službách vyrovnávání zatížení napříč uzly. |
| --min-replika-set-size | Minimální velikost sady replik jako číslo. To platí pouze pro stavové služby. |
| --náklady na přesun | Určuje náklady na přesun služby. Možné hodnoty\: jsou "Nula", "Nízká", "Střední", Vysoká, Velmi vysoká. |
| --pojmenované schéma | Označuje, že služba by měla mít více pojmenovaných oddílů. |
| --pojmenovaný seznam schémat | JSON kódované seznam názvů rozdělit službu napříč, pokud používáte pojmenované schéma oddílů. |
| --no-persisted-state --no-persisted-state --no-persisted-state --no | Pokud true, to znamená, že služba nemá žádný trvalý stav uložený na místním disku, nebo ukládá pouze stav v paměti. |
| --seznam zásad umístění | JSON kódovaný seznam zásad umístění pro službu a všechny přidružené názvy domén. Zásady mohou být\: `NonPartiallyPlaceService`jednu nebo více z , `PreferPrimaryDomain`, `RequireDomain`. `RequireDomainDistribution` |
| --kvorum-ztráta-čekání | Maximální doba trvání v sekundách, po kterou může být oddíl ve stavu ztráty kvora. To platí pouze pro stavové služby. |
| --replika-restart-čekání | Doba trvání v sekundách mezi cestou repliky dolů a vytvořením nové repliky. To platí pouze pro stavové služby. |
| --škálování-zásady | JSON kódovaný seznam zásad škálování pro tuto službu. |
| --service-placement-time | Doba, po kterou repliky může zůstat InBuild před hlášení, že sestavení je zablokovaný. To platí pouze pro stavové služby. |
| --singleton-scheme | Označuje, že služba by měla mít jeden oddíl nebo být bez oddílů služby. |
| --stand-by-replica-keep | Maximální doba trvání v sekundách, pro které budou repliky pohotovostního režimu udržovány před odebráním. To platí pouze pro stavové služby. |
| --stavové | Označuje, že služba je stavová služba. |
| --bez státní příslušnosti | Označuje, že služba je služba bez stavů. |
| --target-replica-set-size --target-replica-set-size --target-replica-set-size --target | Cílová replika nastavit velikost jako číslo. To platí pouze pro stavové služby. |
| --časový čas -t | Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-service-delete"></a>sfctl služba odstranit
Odstraní existující službu Service Fabric.

Před odstraněním musí být služba vytvořena. Ve výchozím nastavení service fabric se pokusí zavřít repliky služby v řádném způsobem a potom odstranit službu. Pokud však služba má problémy s řádném uzavřením repliky, operace odstranění může trvat dlouhou dobu nebo uvíznou. Pomocí volitelného příznaku ForceRemove přeskočíte ladnou posloupnost zavření a důrazně odstraníte službu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --service-id [Povinné] | Identita služby. Toto ID je obvykle úplný název služby\:bez schématu URI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název služby\:je "fabric /myapp/app1/svc1", identita\~služby\~by "myapp app1 svc1" v 6.0+ a "myapp/app1/svc1" v předchozích verzích. |
| --force-remove | Odeberte aplikaci nebo službu Service Fabric vynuceně, aniž byste museli projít sekvencí řádného vypnutí. Tento parametr lze použít k vynucené odstranění aplikace nebo služby, pro které je vypršení časového limitu z důvodu problémů v kódu služby, které brání řádnému uzavření replik. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-service-deployed-type"></a>SfcTL služba nasazena typu
Získá informace o zadaném typu služby aplikace nasazené na uzlu v clusteru Service Fabric.

Získá seznam obsahující informace o konkrétním typu služby z aplikací nasazených na uzlu v clusteru Service Fabric. Odpověď zahrnuje název typu služby, jeho stav registrace, balíček kódu, který ji zaregistroval, a ID aktivace balíčku služby. Každá položka představuje jednu aktivaci typu služby, rozlišenou Podle ID aktivace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --název uzlu [Povinné] | Název uzlu |
| --název typu služby [Povinné] | Určuje název typu služby Fabric služby. |
| --service-manifest-name --service-manifest-name --service-manifest-name --service | Název manifestu služby pro filtrování seznamu informací o typu nasazené služby. Pokud je zadán, odpověď bude obsahovat pouze informace o typech služeb, které jsou definovány v tomto manifestu služby. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-service-deployed-type-list"></a>SFCTL služba nasazený-typ seznamu
Získá seznam obsahující informace o typech služeb z aplikací nasazených na uzlu v clusteru Service Fabric.

Získá seznam obsahující informace o typech služeb z aplikací nasazených na uzlu v clusteru Service Fabric. Odpověď zahrnuje název typu služby, jeho stav registrace, balíček kódu, který ji zaregistroval, a ID aktivace balíčku služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --název uzlu [Povinné] | Název uzlu |
| --service-manifest-name --service-manifest-name --service-manifest-name --service | Název manifestu služby pro filtrování seznamu informací o typu nasazené služby. Pokud je zadán, odpověď bude obsahovat pouze informace o typech služeb, které jsou definovány v tomto manifestu služby. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-service-description"></a>popis služby sfctl
Získá popis existující služby Service Fabric služby.

Získá popis existující služby Service Fabric služby. Služba musí být vytvořena před získáním jejího popisu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-service-get-container-logs"></a>sfctl služba get-kontejner-protokoly
Získá protokoly kontejneru pro kontejner nasazený v uzlu Service Fabric.

Získá protokoly kontejneru pro kontejner nasazený na uzlu Service Fabric pro daný balíček kódu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --code-package-name [Povinné] | Název balíčku kódu zadaný v manifestu služby registrovaný jako součást typu aplikace v clusteru Service Fabric. |
| --název uzlu [Povinné] | Název uzlu |
| --service-manifest-name [Povinné] | Název manifestu služby registrovaný jako součást typu aplikace v clusteru Service Fabric. |
| --předchozí | Určuje, zda chcete získat protokoly kontejnerů z ukončených/nedoručených kontejnerů instance balíčku kódu. |
| --ocas | Počet řádků, které mají být zobrazovány z konce protokolů. Výchozí hodnota je 100. 'all' pro zobrazení úplných protokolů. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-service-health"></a>sfctl zdravotní stav služby
Získá stav zadané služby Service Fabric.

Získá informace o stavu zadané služby. Pomocí filtru EventsHealthStateFilter můžete filtrovat kolekci událostí stavu vykazovanou ve službě na základě stavu. Pomocí funkce PartitionsHealthStateFilter můžete filtrovat kolekci vrácených oddílů. Pokud zadáte službu, která neexistuje v úložišti stavu, tento požadavek vrátí chybu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --service-id [Povinné] | Identita služby. Toto ID je obvykle úplný název služby\:bez schématu URI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název služby\:je "fabric /myapp/app1/svc1", identita\~služby\~by "myapp app1 svc1" v 6.0+ a "myapp/app1/svc1" v předchozích verzích. |
| --events-health-state-filter | Umožňuje filtrování kolekce HealthEvent objekty vrácené na základě stavu. Možné hodnoty pro tento parametr zahrnují tetinovou hodnotu jednoho z následujících stavů. Jsou vráceny pouze události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaného stavu. Pokud není zadán, jsou vráceny všechny položky. Hodnoty stavu jsou výčty založené na příznaku, takže hodnota může být kombinací těchto hodnot získaných pomocí bitového operátoru "OR". Například Pokud je zadaný hodnota 6 pak jsou vráceny všechny události s HealthState hodnotu OK (2) a Upozornění (4).  <br> - Výchozí - Výchozí hodnota. Odpovídá jakékoli HealthState. Hodnota je nula.  <br> - Žádný - filtr, který neodpovídá žádné hodnotě HealthState. Používá se k vrácení žádné výsledky na dané kolekci stavů. Hodnota je 1.  <br> - Ok - Filtr, který odpovídá vstupu s hodnotou HealthState Ok. Hodnota je 2.  <br> - Upozornění - filtr, který odpovídá vstupu s HealthState hodnota Upozornění. Hodnota je 4.  <br> - Chyba - filtr, který odpovídá vstupu s Hodnotou HealthState Error. Hodnota je 8.  <br> - All - Filtr, který odpovídá vstupu s libovolnou hodnotou HealthState. Hodnota je 65535. |
| --vyloučit-zdravotní statistiky | Označuje, zda by měly být vráceny statistiky stavu jako součást výsledku dotazu. False ve výchozím nastavení. Statistiky ukazují počet podřízených entit ve stavu Ok, Upozornění a Chyba. |
| --oddíly-stav-stav-filtr | Umožňuje filtrování objektů stavu oddílů vrácených ve výsledku dotazu stavu služby na základě jejich stavu. Možné hodnoty pro tento parametr zahrnují tetinovou hodnotu jednoho z následujících stavů. Jsou vráceny pouze oddíly, které odpovídají filtru. Všechny oddíly se používají k vyhodnocení agregovaného stavu. Pokud není zadán, jsou vráceny všechny položky. Hodnoty stavu jsou výčty založené na příznaku, takže hodnota může být kombinací těchto hodnot získaných pomocí bitového operátoru "OR". Například pokud je zadaný hodnota 6 pak stav oddílů s HealthState hodnotu OK (2) a upozornění (4) budou vráceny.  <br> - Výchozí - Výchozí hodnota. Odpovídá jakékoli HealthState. Hodnota je nula.  <br> - Žádný - filtr, který neodpovídá žádné hodnotě HealthState. Používá se k vrácení žádné výsledky na dané kolekci stavů. Hodnota je 1.  <br> - Ok - Filtr, který odpovídá vstupu s hodnotou HealthState Ok. Hodnota je 2.  <br> - Upozornění - filtr, který odpovídá vstupu s HealthState hodnota Upozornění. Hodnota je 4.  <br> - Chyba - filtr, který odpovídá vstupu s Hodnotou HealthState Error. Hodnota je 8.  <br> - All - Filtr, který odpovídá vstupu s libovolnou hodnotou HealthState. Hodnota je 65535. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-service-info"></a>sfctl informace o službě
Získá informace o konkrétní služby, které patří do aplikace Service Fabric.

Vrátí informace o zadané službě, která patří k zadané aplikaci Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
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

## <a name="sfctl-service-list"></a>sfctl seznam služeb
Získá informace o všech službách, které patří do aplikace určené ID aplikace.

Vrátí informace o všech službách, které patří do aplikace určené ID aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --continuation-token | Parametr tokenu pokračování se používá k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API, pokud se výsledky ze systému nevejdou do jediné odpovědi. Když je tato hodnota předána dalšímu volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak token pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódována adresou URL. |
| --název typu služby | Název typu služby používaný k filtrování služeb, na které se má dotazovat. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-service-manifest"></a>Manifest služby sfctl
Získá manifest popisující typ služby.

Získá manifest popisující typ služby. Odpověď obsahuje manifest služby XML jako řetězec.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název typu aplikace [Povinné] | Název typu aplikace. |
| --verze typu aplikace [Povinné] | Verze typu aplikace. |
| --service-manifest-name [Povinné] | Název manifestu služby registrovaný jako součást typu aplikace v clusteru Service Fabric. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-service-package-deploy"></a>sfctl service package-deploy
Stáhne balíčky přidružené k určenému manifestu služby do mezipaměti bitové kopie v zadaném uzlu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název typu aplikace [Povinné] | Název manifestu aplikace pro odpovídající manifest požadované služby. |
| --verze typu aplikace [Povinné] | Verze manifestu aplikace pro odpovídající manifest požadované služby. |
| --název uzlu [Povinné] | Název uzlu |
| --service-manifest-name [Povinné] | Název manifestu služby přidružený k balíčkům, které budou staženy. |
| --sdílená politika | JSON kódovaný seznam zásad sdílení. Každý prvek zásad sdílení se skládá z "název" a "obor". Název odpovídá názvu kódu, konfigurace nebo datového balíčku, který má být sdílen. Obor může být buď "Žádný", "Vše", "Kód", "Config" nebo "Data". |
| --časový čas -t | Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-service-package-health"></a>sfctl service package-health sfctl service package-health sfctl service package-health sfc
Získá informace o stavu balíčku služby pro konkrétní aplikaci nasazenou pro uzel service fabric a aplikace.

Získá informace o stavu balíčku služby pro konkrétní aplikaci nasazenou v uzlu Service Fabric. Pomocí filtru EventsHealthStateFilter můžete volitelně filtrovat kolekci objektů HealthEvent vykazovaných v balíčku nasazených služeb na základě stavu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --název uzlu [Povinné] | Název uzlu |
| --service-package-name [Povinné] | Název balíčku služby. |
| --events-health-state-filter | Umožňuje filtrování kolekce HealthEvent objekty vrácené na základě stavu. Možné hodnoty pro tento parametr zahrnují tetinovou hodnotu jednoho z následujících stavů. Jsou vráceny pouze události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaného stavu. Pokud není zadán, jsou vráceny všechny položky. Hodnoty stavu jsou výčty založené na příznaku, takže hodnota může být kombinací těchto hodnot získaných pomocí bitového operátoru "OR". Například Pokud je zadaný hodnota 6 pak jsou vráceny všechny události s HealthState hodnotu OK (2) a Upozornění (4).  <br> - Výchozí - Výchozí hodnota. Odpovídá jakékoli HealthState. Hodnota je nula.  <br> - Žádný - filtr, který neodpovídá žádné hodnotě HealthState. Používá se k vrácení žádné výsledky na dané kolekci stavů. Hodnota je 1.  <br> - Ok - Filtr, který odpovídá vstupu s hodnotou HealthState Ok. Hodnota je 2.  <br> - Upozornění - filtr, který odpovídá vstupu s HealthState hodnota Upozornění. Hodnota je 4.  <br> - Chyba - filtr, který odpovídá vstupu s Hodnotou HealthState Error. Hodnota je 8.  <br> - All - Filtr, který odpovídá vstupu s libovolnou hodnotou HealthState. Hodnota je 65535. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-service-package-info"></a>sfctl servisní balíček-info
Získá seznam balíčků služeb nasazených v uzlu Service Fabric odpovídající přesně zadaný název.

Vrátí informace o balíčcích služeb nasazených v uzlu Service Fabric pro danou aplikaci. Tyto výsledky jsou balíčky služeb, jejichž název přesně odpovídá názvu balíčku služby zadanému jako parametr.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --název uzlu [Povinné] | Název uzlu |
| --service-package-name [Povinné] | Název balíčku služby. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-service-package-list"></a>seznam balíčků služeb sfctl
Získá seznam balíčků služeb nasazených v uzlu Service Fabric.

Vrátí informace o balíčcích služeb nasazených v uzlu Service Fabric pro danou aplikaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --název uzlu [Povinné] | Název uzlu |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-service-recover"></a>sfctl služba obnovit
Označuje clusteru Service Fabric, že by se měl pokusit obnovit zadanou službu, která je aktuálně zablokovaná ve ztrátě kvora.

Označuje clusteru Service Fabric, že by se měl pokusit obnovit zadanou službu, která je aktuálně zablokovaná ve ztrátě kvora. Tato operace by měla být provedena pouze v případě, že je známo, že repliky, které jsou dole nelze obnovit. Nesprávné použití tohoto rozhraní API může způsobit potenciální ztrátu dat.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-service-report-health"></a>sfctl service report-health
Odešle zprávu o stavu služby Service Fabric.

Hlásí stav zadané služby Service Fabric. Sestava musí obsahovat informace o zdroji sestavy stavu a vlastnosti, na které je hlášena. Sestava je odeslána službě Service Fabric gateway Service, která předá do úložiště stavu. Sestava může být přijata bránou, ale odmítnuta úložištěm stavu po dalším ověření. Úložiště stavu může například odmítnout sestavu z důvodu neplatného parametru, jako je zastaralé pořadové číslo. Chcete-li zjistit, zda byla sestava použita v úložišti stavu, zkontrolujte, zda se sestava zobrazuje v událostech stavu služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --health-property [Povinné] | Vlastnost informací o stavu. <br><br> Entita může mít sestavy stavu pro různé vlastnosti. Vlastnost je řetězec a není pevný výčet, aby reportér flexibilitu kategorizovat podmínku stavu, který aktivuje sestavu. Například reportér s SourceId "LocalWatchdog" můžete sledovat stav dostupného disku na uzlu, tak to může hlásit "AvailableDisk" vlastnost na tomto uzlu. Stejný reportér může sledovat připojení uzlu, takže může hlásit vlastnost "Připojení" na stejném uzlu. V úložišti stavu jsou tyto sestavy považovány za samostatné události stavu pro zadaný uzel. Spolu s SourceId vlastnost jednoznačně identifikuje informace o stavu. |
| --stav -state [Povinné] | Možné hodnoty\: zahrnují "Neplatné", Ok, "Upozornění", "Chyba", Neznámý. |
| --service-id [Povinné] | Identita služby. <br><br> Obvykle se jedná o úplný název služby\:bez schématu identifikátoru URI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " . Například pokud název služby\:je 'fabric /myapp/app1/svc1', identita\~služby\~by 'myapp app1 svc1' v 6.0+ a 'myapp/app1/svc1' v předchozích verzích. |
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

## <a name="sfctl-service-resolve"></a>sfctl služba vyřešit
Vyřešte oddíl Service Fabric.

Vyřešit oddíl služby Service Fabric získat koncové body replikslužby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --service-id [Povinné] | Identita služby. Toto ID je obvykle úplný název služby\:bez schématu URI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název služby\:je "fabric /myapp/app1/svc1", identita\~služby\~by "myapp app1 svc1" v 6.0+ a "myapp/app1/svc1" v předchozích verzích. |
| --partition-key-type | Typ klíče pro oddíl. Tento parametr je vyžadován, pokud je schéma oddílu pro službu Int64Range nebo Named. Možné hodnoty jsou následující. - None (1) - Označuje, že parametr PartitionKeyValue není zadán. To platí pro oddíly s partitioning schéma jako Singleton. Toto je výchozí hodnota. Hodnota je 1. - Int64Range (2) - Označuje, že PartitionKeyValue parametr je klíč oddílu int64. To platí pro oddíly s partitioning schéma jako Int64Range. Hodnota je 2. - Named (3) - Označuje, že partitionKeyValue parametr je název oddílu. To platí pro oddíly s partitioning schéma jako pojmenované. Hodnota je 3. |
| --partition-key-value | Klíč oddílu. To je nutné, pokud je schéma oddílu pro službu Int64Range nebo Named. Toto není ID oddílu, ale spíše hodnota celého klíče nebo název ID oddílu. Například pokud vaše služba používá oddíly v rozsahu od 0 do 10, pak partitionKeyValue by celé číslo v této oblasti. Popis služby dotazu zobrazíte oblast nebo název. |
| --předchozí-rsp-verze | Hodnota v poli Version odpovědi, která byla přijata dříve. To je nutné, pokud uživatel ví, že výsledek, který byl dříve získal, je zastaralý. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-service-type-list"></a>seznam typů služby sfctl
Získá seznam obsahující informace o typech služeb, které jsou podporovány zřízeného typu aplikace v clusteru Service Fabric.

Získá seznam obsahující informace o typech služeb, které jsou podporovány zřízeného typu aplikace v clusteru Service Fabric. Zadaný typ aplikace musí existovat. V opačném případě je vrácen stav 404.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název typu aplikace [Povinné] | Název typu aplikace. |
| --verze typu aplikace [Povinné] | Verze typu aplikace. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-service-update"></a>aktualizace služby sfctl
Aktualizuje zadanou službu pomocí daného popisu aktualizace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --service-id [Povinné] | Identita služby. Obvykle se jedná o úplný název služby\:bez schématu identifikátoru URI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název služby\:je 'fabric /myapp/app1/svc1', identita\~služby\~by 'myapp app1 svc1' v 6.0+ a 'myapp/app1/svc1' v předchozích verzích. |
| --omezení | Omezení umístění jako řetězec. Omezení umístění jsou logické výrazy na vlastnosti uzlu a umožňují omezit službu na konkrétní uzly na základě požadavků na službu. Chcete-li například umístit službu na uzly,\: kde je modrá NodeType, zadejte následující "NodeColor == blue". |
| --korelační služba | Název cílové služby, se kterou chcete korelovat. |
| --korelace | Korelujte službu s existující službou pomocí spřažení zarovnání. |
| --počet instancí | Počet instancí. To platí pouze pro služby bez státní příslušnosti. |
| --load-metrics | JSON kódovaný seznam metrik používaných při vyrovnávání zatížení mezi uzly. |
| --min-replika-set-size | Minimální velikost sady replik jako číslo. To platí pouze pro stavové služby. |
| --náklady na přesun | Určuje náklady na přesun služby. Možné hodnoty\: jsou "Nula", "Nízká", "Střední", Vysoká, Velmi vysoká. |
| --seznam zásad umístění | JSON kódovaný seznam zásad umístění pro službu a všechny přidružené názvy domén. Zásady mohou být\: `NonPartiallyPlaceService`jednu nebo více z , `PreferPrimaryDomain`, `RequireDomain`. `RequireDomainDistribution` |
| --kvorum-ztráta-čekání | Maximální doba trvání v sekundách, po kterou může být oddíl ve stavu ztráty kvora. To platí pouze pro stavové služby. |
| --replika-restart-čekání | Doba trvání v sekundách mezi cestou repliky dolů a vytvořením nové repliky. To platí pouze pro stavové služby. |
| --škálování-zásady | JSON kódovaný seznam zásad škálování pro tuto službu. |
| --service-placement-time | Doba, po kterou repliky může zůstat InBuild před hlášení, že sestavení je zablokovaný. To platí pouze pro stavové služby. |
| --stand-by-replica-keep | Maximální doba trvání v sekundách, pro které budou repliky pohotovostního režimu udržovány před odebráním. To platí pouze pro stavové služby. |
| --stavové | Označuje, že cílová služba je stavová služba. |
| --bez státní příslušnosti | Označuje, že cílová služba je bezstavová služba. |
| --target-replica-set-size --target-replica-set-size --target-replica-set-size --target | Cílová replika nastavit velikost jako číslo. To platí pouze pro stavové služby. |
| --časový čas -t | Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) cli service fabric.
- Naučte se používat funkce CLI service fabric pomocí [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).
