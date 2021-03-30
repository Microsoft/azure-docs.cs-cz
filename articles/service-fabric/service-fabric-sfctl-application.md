---
title: Aplikace Azure Service Fabric CLI – sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu aplikací.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 880770345eb7d65850db322bd97d64c60b6681ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260870"
---
# <a name="sfctl-application"></a>sfctl application
Vytvářejte, odstraňujte a spravujte aplikace a typy aplikací.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| vytvoření | Vytvoří Service Fabric aplikaci pomocí zadaného popisu. |
| delete | Odstraní existující aplikaci Service Fabric. |
| nainstalována | Získá informace o aplikaci nasazené na Service Fabric uzlu. |
| nasazeno – stav | Získá informace o stavu aplikace nasazené na Service Fabric uzlu. |
| nasazeno – seznam | Načte seznam aplikací nasazených na Service Fabric uzlu. |
| nezávadnost | Získá stav aplikace Service Fabric. |
| příjemce | Načte informace o Service Fabric aplikaci. |
| list | Načte seznam aplikací vytvořených v clusteru Service Fabric, které odpovídají zadaným filtrům. |
| načítání | Načte informace o načtení Service Fabric aplikace. |
| manifest | Načte manifest popisující typ aplikace. |
| penzijní | Zřídí nebo zaregistruje typ aplikace Service Fabric s clusterem pomocí balíčku '. sfpkg ' v externím úložišti nebo pomocí balíčku aplikace v úložišti imagí. |
| Sestava – stav | Odešle zprávu o stavu aplikace Service Fabric. |
| typ | Načte seznam typů aplikací v clusteru Service Fabric, který přesně odpovídá zadanému názvu. |
| typ – seznam | Získá seznam typů aplikací v clusteru Service Fabric. |
| zrušit zřízení | Odebere nebo zruší registraci typu Service Fabric aplikace z clusteru. |
| upgrade | Spustí upgrade aplikace v clusteru Service Fabric. |
| upgrade – obnovení | Obnoví upgrade aplikace v clusteru Service Fabric. |
| upgrade – vrácení zpět | Spustí vracení zpět aktuálně probíhajícího upgradu aplikace v clusteru Service Fabric. |
| upgrade – stav | Získá podrobnosti o nejnovějším upgradu provedeném v této aplikaci. |
| nahrání | Zkopírujte balíček aplikace Service Fabric do úložiště imagí. |

## <a name="sfctl-application-create"></a>Vytvoření aplikace sfctl
Vytvoří Service Fabric aplikaci pomocí zadaného popisu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --App-Name [povinné] | Název aplikace, včetně \: schématu identifikátoru URI Fabric. |
| --Typ aplikace [povinné] | V manifestu aplikace se našel název typu aplikace. |
| --App-Version [požadováno] | Verze typu aplikace, jak je definováno v manifestu aplikace. |
| --Max-Node-Count | Maximální počet uzlů, kde Service Fabric rezervuje kapacitu pro tuto aplikaci. Všimněte si, že to neznamená, že služby této aplikace budou umístěny na všech těchto uzlech. |
| --metriky | Seznam popisů metriky kapacity aplikace v kódování JSON. Metrika se definuje jako název, který je přidružený k sadě kapacit pro každý uzel, na kterém aplikace existuje. |
| --min-Node-Count | Minimální počet uzlů, kde Service Fabric rezervuje kapacitu pro tuto aplikaci. Všimněte si, že to neznamená, že služby této aplikace budou umístěny na všech těchto uzlech. |
| --parametry | Seznam přepsání parametrů aplikace, který se má použít při vytváření aplikace, je zakódovaný do formátu JSON. |
| --Timeout-t | Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-application-delete"></a>odstranění aplikace sfctl
Odstraní existující aplikaci Service Fabric.

Aby bylo možné aplikaci odstranit, je třeba ji vytvořit. Odstraněním aplikace dojde k odstranění všech služeb, které jsou součástí této aplikace. Ve výchozím nastavení se Service Fabric pokusí zavřít repliky služby řádným způsobem a pak tuto službu odstranit. Pokud ale dojde k řádnému zavření repliky služby, operace odstranění může trvat dlouhou dobu nebo se zablokuje. Pomocí volitelného příznaku ForceRemove můžete přeskočit plynulé ukončení sekvence a vynuceně odstranit aplikaci a všechny její služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
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

## <a name="sfctl-application-deployed"></a>nasazená aplikace sfctl
Získá informace o aplikaci nasazené na Service Fabric uzlu.

Tento dotaz vrátí informace o systémové aplikaci, pokud je zadané ID aplikace pro systémovou aplikaci. Výsledky zahrnují nasazené aplikace v aktivních, aktivačních a stahovaných stavech. Tento dotaz vyžaduje, aby název uzlu odpovídal uzlu v clusteru. Dotaz se nezdařil, pokud zadaný název uzlu neukazuje na žádný aktivní Service Fabric uzel v clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Node-Name [povinné] | Název uzlu |
| --include-Health-State | Zahrňte do stavu entity. Pokud je tento parametr false nebo není zadán, je vrácen stav "Neznámý". Pokud je nastaveno na hodnotu true, dotaz projde paralelně s uzlem a službou Health System před sloučením výsledků. V důsledku toho je dotaz dražší a může trvat delší dobu. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-application-deployed-health"></a>nasazená aplikace sfctl – stav
Získá informace o stavu aplikace nasazené na Service Fabric uzlu.

Získá informace o stavu aplikace nasazené na Service Fabric uzlu. EventsHealthStateFilter můžete použít k volitelnému filtrování kolekce objektů HealthEvent hlášených v nasazené aplikaci na základě stavu. DeployedServicePackagesHealthStateFilter můžete použít k volitelnému filtrování podřízených prvků DeployedServicePackageHealth na základě stavu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Node-Name [povinné] | Název uzlu |
| --deployed-Service-Packages-Health-State-Filter | Umožňuje filtrování nasazených objektů stavu balíčku služby vrácených v důsledku nasazeného dotazu na stav aplikace na základě jejich stavu. Možné hodnoty pro tento parametr zahrnují celočíselnou hodnotu jednoho z následujících stavů. Vrátí se jenom nasazené balíčky služeb, které odpovídají filtru. Všechny nasazené balíčky služby se používají k vyhodnocení agregovaného stavu nasazené aplikace. Pokud tento parametr nezadáte, vrátí se všechny položky. Hodnoty stavu jsou výčty založené na příznak, takže hodnota může být kombinací těchto hodnot, získaná pomocí bitového operátoru OR. Například pokud je zadaná hodnota 6, pak se vrátí stav balíčků služeb s hodnotou OK (2) a upozornění (4).  <br> -Výchozí-výchozí hodnota. Odpovídá jakémukoli elementu. Hodnota je nula.  <br> -None-Filter, který neodpovídá žádné hodnotě elementu. Používá se k tomu, aby se v dané kolekci stavů nevracely žádné výsledky. Hodnota je 1.  <br> -OK – filtr, který odpovídá zadanému vstupu s hodnotou podstavu OK. Hodnota je 2.  <br> -Warning-Filter, který odpovídá vstupu s upozorněním na podstavovou hodnotu. Hodnota je 4.  <br> – Filtr chyb, který odpovídá zadanému vstupu s chybou hodnoty elementu stav Hodnota je 8.  <br> -All – filtr, který odpovídá zadanému vstupu s jakoukoli hodnotou elementu. Hodnota je 65535. |
| --Events – stav – filtr | Umožňuje filtrovat kolekci objektů HealthEvent vrácených na základě stavu. Možné hodnoty pro tento parametr zahrnují celočíselnou hodnotu jednoho z následujících stavů. Vrátí se pouze události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaného stavu. Pokud tento parametr nezadáte, vrátí se všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota by mohla být kombinací těchto hodnot získána pomocí bitového operátoru OR. Pokud je například zadaná hodnota 6, budou vráceny všechny události s hodnotou ' OK (2) a upozornění (4).  <br> -Výchozí-výchozí hodnota. Odpovídá jakémukoli elementu. Hodnota je nula.  <br> -None-Filter, který neodpovídá žádné hodnotě elementu. Používá se k tomu, aby se v dané kolekci stavů nevracely žádné výsledky. Hodnota je 1.  <br> -OK – filtr, který odpovídá zadanému vstupu s hodnotou podstavu OK. Hodnota je 2.  <br> -Warning-Filter, který odpovídá vstupu s upozorněním na podstavovou hodnotu. Hodnota je 4.  <br> – Filtr chyb, který odpovídá zadanému vstupu s chybou hodnoty elementu stav Hodnota je 8.  <br> -All – filtr, který odpovídá zadanému vstupu s jakoukoli hodnotou elementu. Hodnota je 65535. |
| --Exclude-Health-Statistics | Určuje, zda mají být v rámci výsledku dotazu vráceny statistiky stavu. Výchozí hodnota je false. Statistika zobrazuje počet podřízených entit ve stavu OK, varování a chyba. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-application-deployed-list"></a>nasazená aplikace sfctl – seznam
Načte seznam aplikací nasazených na Service Fabric uzlu.

Načte seznam aplikací nasazených na Service Fabric uzlu. Výsledky neobsahují informace o nasazených systémových aplikacích, pokud se explicitně nedotazují na podle ID. Výsledky zahrnují nasazené aplikace v aktivních, aktivačních a stahovaných stavech. Tento dotaz vyžaduje, aby název uzlu odpovídal uzlu v clusteru. Dotaz se nezdařil, pokud zadaný název uzlu neukazuje na žádný aktivní Service Fabric uzel v clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Node-Name [povinné] | Název uzlu |
| --pokračování-token | Parametr tokenu pokračování slouží k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API v případě, že se výsledky ze systému nevejdou do jediné odpovědi. Když se tato hodnota předává do dalšího volání rozhraní API, vrátí rozhraní API další sadu výsledků. Pokud nejsou k dispozici žádné další výsledky, token pokračování neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódovaná v adrese URL. |
| --include-Health-State | Zahrňte do stavu entity. Pokud je tento parametr false nebo není zadán, je vrácen stav "Neznámý". Pokud je nastaveno na hodnotu true, dotaz projde paralelně s uzlem a službou Health System před sloučením výsledků. V důsledku toho je dotaz dražší a může trvat delší dobu. |
| --Max – výsledky | Maximální počet výsledků, které mají být vráceny v rámci stránkovaných dotazů. Tento parametr definuje horní mez počtu vrácených výsledků. Vrácené výsledky mohou být menší než zadané maximální výsledky, pokud se nevejdou do zprávy podle maximální velikosti omezení velikosti zprávy definované v konfiguraci. Pokud je tento parametr nula nebo není zadán, dotaz stránkovaného obsahu obsahuje tolik výsledků, kolik jich bylo možné do návratové zprávy. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-application-health"></a>stav aplikace sfctl
Získá stav aplikace Service Fabric.

Vrátí stav stavu aplikace Service Fabric. Odpověď hlásí stav OK, chyba nebo upozornění. Pokud se entita v Health Store nenajde, vrátí se chyba.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --deployed-Applications-Health-Filter | Umožňuje filtrování objektů stavu nasazených aplikací vrácených v důsledku dotazu na stav aplikace na základě jejich stavu. Možné hodnoty pro tento parametr zahrnují celočíselnou hodnotu jednoho z následujících stavů. Vrátí se jenom nasazené aplikace, které odpovídají filtru. Všechny nasazené aplikace se používají k vyhodnocení agregovaného stavu. Pokud tento parametr nezadáte, vrátí se všechny položky. Hodnoty stavu jsou výčty založené na příznak, takže hodnota by mohla být kombinací těchto hodnot, získaná pomocí bitového operátoru OR. Například pokud je zadaná hodnota 6, pak se vrátí stav nasazených aplikací s hodnotou OK (2) a upozornění (4).  <br> -Výchozí-výchozí hodnota. Odpovídá jakémukoli elementu. Hodnota je nula.  <br> -None-Filter, který neodpovídá žádné hodnotě elementu. Používá se k tomu, aby se v dané kolekci stavů nevracely žádné výsledky. Hodnota je 1.  <br> -OK – filtr, který odpovídá zadanému vstupu s hodnotou podstavu OK. Hodnota je 2.  <br> -Warning-Filter, který odpovídá vstupu s upozorněním na podstavovou hodnotu. Hodnota je 4.  <br> – Filtr chyb, který odpovídá zadanému vstupu s chybou hodnoty elementu stav Hodnota je 8.  <br> -All – filtr, který odpovídá zadanému vstupu s jakoukoli hodnotou elementu. Hodnota je 65535. |
| --Events – stav – filtr | Umožňuje filtrovat kolekci objektů HealthEvent vrácených na základě stavu. Možné hodnoty pro tento parametr zahrnují celočíselnou hodnotu jednoho z následujících stavů. Vrátí se pouze události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaného stavu. Pokud tento parametr nezadáte, vrátí se všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota by mohla být kombinací těchto hodnot získána pomocí bitového operátoru OR. Pokud je například zadaná hodnota 6, budou vráceny všechny události s hodnotou ' OK (2) a upozornění (4).  <br> -Výchozí-výchozí hodnota. Odpovídá jakémukoli elementu. Hodnota je nula.  <br> -None-Filter, který neodpovídá žádné hodnotě elementu. Používá se k tomu, aby se v dané kolekci stavů nevracely žádné výsledky. Hodnota je 1.  <br> -OK – filtr, který odpovídá zadanému vstupu s hodnotou podstavu OK. Hodnota je 2.  <br> -Warning-Filter, který odpovídá vstupu s upozorněním na podstavovou hodnotu. Hodnota je 4.  <br> – Filtr chyb, který odpovídá zadanému vstupu s chybou hodnoty elementu stav Hodnota je 8.  <br> -All – filtr, který odpovídá zadanému vstupu s jakoukoli hodnotou elementu. Hodnota je 65535. |
| --Exclude-Health-Statistics | Určuje, zda mají být v rámci výsledku dotazu vráceny statistiky stavu. Výchozí hodnota je false. Statistika zobrazuje počet podřízených entit ve stavu OK, varování a chyba. |
| --Services-Health-State-Filter | Umožňuje filtrování objektů stavu služby vrácených v důsledku dotazu na stav služby na základě jejich stavu. Možné hodnoty pro tento parametr zahrnují celočíselnou hodnotu jednoho z následujících stavů. Vrátí se jenom služby, které odpovídají filtru. Všechny služby se používají k vyhodnocení agregovaného stavu. Pokud tento parametr nezadáte, vrátí se všechny položky. Hodnoty stavu jsou výčty založené na příznak, takže hodnota by mohla být kombinací těchto hodnot, získaná pomocí bitového operátoru OR. Pokud je zadaná hodnota například 6, pak se vrátí stav služeb s hodnotou OK (2) a upozornění (4).  <br> -Výchozí-výchozí hodnota. Odpovídá jakémukoli elementu. Hodnota je nula.  <br> -None-Filter, který neodpovídá žádné hodnotě elementu. Používá se k tomu, aby se v dané kolekci stavů nevracely žádné výsledky. Hodnota je 1.  <br> -OK – filtr, který odpovídá zadanému vstupu s hodnotou podstavu OK. Hodnota je 2.  <br> -Warning-Filter, který odpovídá vstupu s upozorněním na podstavovou hodnotu. Hodnota je 4.  <br> – Filtr chyb, který odpovídá zadanému vstupu s chybou hodnoty elementu stav Hodnota je 8.  <br> -All – filtr, který odpovídá zadanému vstupu s jakoukoli hodnotou elementu. Hodnota je 65535. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-application-info"></a>informace o aplikaci sfctl
Načte informace o Service Fabric aplikaci.

Vrátí informace o aplikaci, která byla vytvořena nebo v procesu vytváření v clusteru Service Fabric a jejíž název odpovídá parametru zadanému jako parametr. Odpověď obsahuje název, typ, stav, parametry a další podrobnosti o aplikaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Exclude-Application-Parameters | Příznak, který určuje, zda budou z výsledku vyloučeny parametry aplikace. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-application-list"></a>seznam aplikací sfctl
Načte seznam aplikací vytvořených v clusteru Service Fabric, které odpovídají zadaným filtrům.

Získá informace o aplikacích, které byly vytvořeny nebo v procesu vytváření v clusteru Service Fabric a odpovídají zadaným filtrům. Odpověď obsahuje název, typ, stav, parametry a další podrobnosti o aplikaci. Pokud se aplikace nevejdou do stránky, vrátí se jedna stránka výsledků a také token pro pokračování, který se dá použít k získání další stránky. Filtry ApplicationTypeName a ApplicationDefinitionKindFilter nelze zadat současně.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Application-Definition-druh – filtr | Slouží k filtrování ApplicationDefinitionKind, což je mechanismus používaný k definování aplikace Service Fabric.  <br> -Výchozí-výchozí hodnota, která provede stejnou funkci jako výběr možnosti vše. Hodnota je 0.  <br> -All – filtr, který odpovídá zadanému vstupu s libovolnou hodnotou ApplicationDefinitionKind Hodnota je 65535.  <br> -ServiceFabricApplicationDescription – filtr, který odpovídá zadanému vstupu s hodnotou ApplicationDefinitionKind ServiceFabricApplicationDescription. Hodnota je 1.  <br> -Napsat-Filter, který odpovídá vstupu s ApplicationDefinitionKind hodnotou. Hodnota je 2. |
| --Typ aplikace-název | Název typu aplikace, který slouží k filtrování aplikací pro dotazování. Tato hodnota by neměla obsahovat verzi typu aplikace. |
| --pokračování-token | Parametr tokenu pokračování slouží k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API v případě, že se výsledky ze systému nevejdou do jediné odpovědi. Když se tato hodnota předává do dalšího volání rozhraní API, vrátí rozhraní API další sadu výsledků. Pokud nejsou k dispozici žádné další výsledky, token pokračování neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódovaná v adrese URL. |
| --Exclude-Application-Parameters | Příznak, který určuje, zda budou z výsledku vyloučeny parametry aplikace. |
| --Max – výsledky | Maximální počet výsledků, které mají být vráceny v rámci stránkovaných dotazů. Tento parametr definuje horní mez počtu vrácených výsledků. Vrácené výsledky mohou být menší než zadané maximální výsledky, pokud se nevejdou do zprávy podle maximální velikosti omezení velikosti zprávy definované v konfiguraci. Pokud je tento parametr nula nebo není zadán, dotaz stránkovaného obsahu obsahuje tolik výsledků, kolik jich bylo možné do návratové zprávy. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-application-load"></a>zatížení aplikace sfctl
Načte informace o načtení Service Fabric aplikace.

Vrátí informace o načítání aplikace, která byla vytvořena nebo v procesu vytváření v clusteru Service Fabric a jejíž název odpovídá parametru zadanému jako parametr. Odpověď obsahuje název, minimální uzly, maximální počet uzlů, počet uzlů, které aplikace zabírá, a informace o metrikě zatížení aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-application-manifest"></a>manifest aplikace sfctl
Načte manifest popisující typ aplikace.

Odpověď obsahuje XML manifestu aplikace jako řetězec.

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

## <a name="sfctl-application-provision"></a>zřizování aplikace sfctl
Zřídí nebo zaregistruje typ aplikace Service Fabric s clusterem pomocí balíčku '. sfpkg ' v externím úložišti nebo pomocí balíčku aplikace v úložišti imagí.

Zřídí Service Fabric typ aplikace s clusterem. Zřizování se vyžaduje předtím, než bude možné vytvořit instanci nových aplikací. Operaci zřizování lze provést buď na balíčku aplikace určeném pomocí relativePathInImageStore, nebo pomocí identifikátoru URI externího objektu. sfpkg. Pokud není nastavená možnost--External-disvision, bude tento příkaz očekávat zřízení úložiště imagí.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Application-Package-Download-URI | Cesta k balíčku aplikace ". sfpkg", ze kterého lze balíček aplikace stáhnout pomocí protokolů HTTP nebo HTTPS. <br><br> Jenom pro druh zřizování externí úložiště. Balíček aplikace může být uložený v externím úložišti, které poskytuje operaci GET pro stažení souboru. Podporované protokoly jsou HTTP a HTTPS a cesta musí umožňovat přístup pro čtení. |
| --Typ aplikace-sestavení-cesta | Jenom pro úložiště imagí druh zřizování. Relativní cesta k balíčku aplikace v úložišti imagí, kterou jste zadali během operace předchozího odeslání. |
| --Typ aplikace-název | Jenom pro druh zřizování externí úložiště. Název typu aplikace představuje název typu aplikace, který byl nalezen v manifestu aplikace. |
| --Typ aplikace-verze | Jenom pro druh zřizování externí úložiště. Verze typu aplikace představuje verzi typu aplikace nalezené v manifestu aplikace. |
| --External-zřizování | Umístění, ze kterého lze zaregistrovat nebo zřídit balíček aplikace. Indikuje, že zřízení je pro balíček aplikace, který se dřív nahrál do externího úložiště. Balíček aplikace končí příponou *. sfpkg. |
| --No-Wait | Označuje, zda se má zřizování provádět asynchronně. <br><br> Pokud je nastavena hodnota true, operace zřizování vrátí, když je požadavek přijat systémem, a operace zřizování pokračuje bez časového limitu. Výchozí hodnota je False. Pro velké balíčky aplikací doporučujeme nastavit hodnotu na true. |
| --Timeout-t | Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-application-report-health"></a>Sestava aplikace sfctl – stav
Odešle zprávu o stavu aplikace Service Fabric.

Hlásí stav zadané aplikace Service Fabric. Sestava musí obsahovat informace o zdroji sestavy stavu a vlastnosti, na které je hlášena. Sestava se odešle do aplikace Service Fabric Gateway, která se přepošle na Health Store. Tuto sestavu může přijmout brána, ale Health Store po dodatečném ověření odmítnuta. Health Store například může zprávu odmítat z důvodu neplatného parametru, jako je například zastaralé pořadové číslo. Chcete-li zjistit, zda byla sestava použita v Health Store, Získejte stav aplikace a zkontrolujte, zda se zobrazila sestava.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace <br><br> Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem. Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Health-vlastnost [required] | Vlastnost informací o stavu. <br><br> Entita může mít sestavy o stavu pro různé vlastnosti. Vlastnost je řetězec, nikoli pevný výčet, který umožňuje, aby se v zpravodaji mohla pružně roztřídit stav stavu, který aktivuje sestavu. Například zpravodaj s SourceId "LocalWatchdog" může monitorovat stav dostupného disku v uzlu, takže může ohlásit vlastnost "AvailableDisk" v tomto uzlu. Stejné zpravodajky můžou monitorovat připojení uzlu, takže může nahlásit vlastnost "připojení" na stejném uzlu. V Health Store jsou tyto sestavy zpracovány jako samostatné události stavu pro zadaný uzel. Společně s ID zdroje (SourceId) vlastnost jednoznačně identifikuje informace o stavu. |
| --Health-State [povinné] | Možné hodnoty zahrnují \: "Invalid", "OK", "Warning", "Error", "unknown". |
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

## <a name="sfctl-application-type"></a>Typ aplikace sfctl
Načte seznam typů aplikací v clusteru Service Fabric, který přesně odpovídá zadanému názvu.

Vrátí informace o typech aplikací, které jsou zřízeny nebo v procesu zřizování v clusteru Service Fabric. Tyto výsledky jsou typy aplikací, jejichž název odpovídá přesně jednomu, který je zadán jako parametr a který vyhovuje daným parametrům dotazu. Vrátí se všechny verze typu aplikace, které odpovídají názvu typu aplikace. Každá verze se vrátila jako jeden typ aplikace. Odpověď obsahuje název, verzi, stav a další podrobnosti o typu aplikace. Jedná se o stránkovaný dotaz, což znamená, že pokud ne všechny typy aplikací se vejdou na stránku, vrátí se jedna stránka výsledků a také token pro pokračování, který se dá použít k získání další stránky. Pokud je například 10 typů aplikací, ale stránka odpovídá pouze prvním třem typům aplikací, nebo pokud je maximální počet výsledků nastaven na hodnotu 3, vrátí se tři. Chcete-li získat přístup ke zbytku výsledků, načtěte následné stránky pomocí vráceného tokenu pokračování v dalším dotazu. Pokud žádné následné stránky neexistují, vrátí se prázdný token pokračování.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Typ aplikace-název [povinné] | Název typu aplikace |
| --Typ aplikace-verze | Verze typu aplikace |
| --pokračování-token | Parametr tokenu pokračování slouží k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API v případě, že se výsledky ze systému nevejdou do jediné odpovědi. Když se tato hodnota předává do dalšího volání rozhraní API, vrátí rozhraní API další sadu výsledků. Pokud nejsou k dispozici žádné další výsledky, token pokračování neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódovaná v adrese URL. |
| --Exclude-Application-Parameters | Příznak, který určuje, zda budou z výsledku vyloučeny parametry aplikace. |
| --Max – výsledky | Maximální počet výsledků, které mají být vráceny v rámci stránkovaných dotazů. Tento parametr definuje horní mez počtu vrácených výsledků. Vrácené výsledky mohou být menší než zadané maximální výsledky, pokud se nevejdou do zprávy podle maximální velikosti omezení velikosti zprávy definované v konfiguraci. Pokud je tento parametr nula nebo není zadán, dotaz stránkovaného obsahu obsahuje tolik výsledků, kolik jich bylo možné do návratové zprávy. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-application-type-list"></a>Typ aplikace sfctl – seznam
Získá seznam typů aplikací v clusteru Service Fabric.

Vrátí informace o typech aplikací, které jsou zřízeny nebo v procesu zřizování v clusteru Service Fabric. Každá verze typu aplikace je vrácena jako jeden typ aplikace. Odpověď obsahuje název, verzi, stav a další podrobnosti o typu aplikace. Jedná se o stránkovaný dotaz, což znamená, že pokud ne všechny typy aplikací se vejdou na stránku, vrátí se jedna stránka výsledků a také token pro pokračování, který se dá použít k získání další stránky. Pokud je například 10 typů aplikací, ale stránka odpovídá pouze prvním třem typům aplikací, nebo pokud je maximální počet výsledků nastaven na hodnotu 3, vrátí se tři. Chcete-li získat přístup ke zbytku výsledků, načtěte následné stránky pomocí vráceného tokenu pokračování v dalším dotazu. Pokud žádné následné stránky neexistují, vrátí se prázdný token pokračování.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Typ aplikace-definice-druh – filtr | Slouží k filtrování ApplicationTypeDefinitionKind, což je mechanismus používaný k definování typu aplikace Service Fabric.  <br> -Výchozí-výchozí hodnota, která provede stejnou funkci jako výběr možnosti vše. Hodnota je 0.  <br> -All – filtr, který odpovídá zadanému vstupu s libovolnou hodnotou ApplicationTypeDefinitionKind Hodnota je 65535.  <br> -ServiceFabricApplicationPackage – filtr, který odpovídá zadanému vstupu s hodnotou ApplicationTypeDefinitionKind ServiceFabricApplicationPackage. Hodnota je 1.  <br> -Napsat-Filter, který odpovídá vstupu s ApplicationTypeDefinitionKind hodnotou. Hodnota je 2. |
| --pokračování-token | Parametr tokenu pokračování slouží k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API v případě, že se výsledky ze systému nevejdou do jediné odpovědi. Když se tato hodnota předává do dalšího volání rozhraní API, vrátí rozhraní API další sadu výsledků. Pokud nejsou k dispozici žádné další výsledky, token pokračování neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódovaná v adrese URL. |
| --Exclude-Application-Parameters | Příznak, který určuje, zda budou z výsledku vyloučeny parametry aplikace. |
| --Max – výsledky | Maximální počet výsledků, které mají být vráceny v rámci stránkovaných dotazů. Tento parametr definuje horní mez počtu vrácených výsledků. Vrácené výsledky mohou být menší než zadané maximální výsledky, pokud se nevejdou do zprávy podle maximální velikosti omezení velikosti zprávy definované v konfiguraci. Pokud je tento parametr nula nebo není zadán, dotaz stránkovaného obsahu obsahuje tolik výsledků, kolik jich bylo možné do návratové zprávy. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-application-unprovision"></a>zrušení zřízení aplikace sfctl
Odebere nebo zruší registraci typu Service Fabric aplikace z clusteru.

Tuto operaci lze provést pouze v případě, že byly odstraněny všechny instance aplikace typu aplikace. Jakmile bude typ aplikace zaregistrován, nelze pro tento konkrétní typ aplikace vytvořit žádné nové instance aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Typ aplikace-název [povinné] | Název typu aplikace |
| --Typ aplikace-verze [požadováno] | Verze typu aplikace, jak je definováno v manifestu aplikace. |
| --Async-parametr | Příznak označující, zda se má zrušit zřízení, by měl být asynchronní. Pokud je nastaveno na hodnotu true, operace zrušení zřízení vrátí, když je požadavek přijat systémem, a operace zrušení zřízení pokračuje bez časového limitu. Výchozí hodnota je False. Doporučujeme však pro velké balíčky aplikací, které byly zřízeny, nastavit na hodnotu true. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-application-upgrade"></a>Upgrade aplikace sfctl
Spustí upgrade aplikace v clusteru Service Fabric.

Ověří zadané parametry upgradu aplikace a spustí upgrade aplikace, pokud jsou parametry platné. Všimněte si, že popis upgradu nahrazuje stávající Popis aplikace. To znamená, že pokud nejsou zadány parametry, existující parametry v aplikacích budou přepsány seznamem prázdných parametrů. To by vedlo k tomu, že aplikace používá výchozí hodnotu parametrů z manifestu aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace <br><br> Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --verze aplikace [povinné] | Verze typu cílové aplikace (nalezená v manifestu aplikace) pro upgrade aplikace. |
| --parametry [povinné] | Seznam přepsání parametrů aplikace, který se má použít při upgradu aplikace, je zakódovaný do formátu JSON. |
| --Default-Service-Health-Policy | Specifikace formátu JSON pro zásady stavu, která se používá ve výchozím nastavení, aby vyhodnotila stav typu služby. |
| --Chyba – akce | Akce, která se má provést, když monitorovaný upgrade narazí na zásady monitorování nebo porušení zásad stavu. |
| --Force-restart | Vynucené restartování procesů během upgradu i v případě, že se verze kódu nezměnila. |
| --stav-check-opakování – časový limit | Doba mezi pokusy o provedení kontrol stavu, pokud aplikace nebo cluster není v pořádku.  Výchozí \: PT0H10M0S. |
| --Health-Check-ustálený – doba trvání | Doba, po kterou musí aplikace nebo cluster zůstat v pořádku, než bude upgrade pokračovat na další upgradovací doménu.  Výchozí \: PT0H2M0S. <br><br> Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. |
| --Health-Check-čekací doba | Doba, po kterou se má po dokončení upgradu domény počkat, než se spustí proces kontroly stavu.  Výchozí hodnota \: 0. |
| --Max-není v pořádku – aplikace | Maximální povolené procento nezdravých nasazených aplikací. Reprezentované jako číslo mezi 0 a 100. |
| --Mode | Režim používaný k monitorování stavu během postupného upgradu.  Výchozí \: UnmonitoredAuto. |
| --replice-set-check-timeout | Maximální doba, po kterou se má blokovat zpracování upgradovací domény a zabránit ztrátě dostupnosti, pokud dojde k neočekávaným problémům. Měří se v sekundách. |
| --Service-Health-Policy | Mapa zakódovaná pomocí formátu JSON s typem služby zásady stavu na název typu služby Mapa je prázdná jako výchozí. |
| --Timeout-t | Výchozí hodnota je \: 60. |
| --upgrade-Domain – vypršel časový limit. | Doba, po kterou musí být každá upgradovací doména dokončena před provedením FailureAction.  Výchozí \: p10675199dt02h48m 05.4775807 s. <br><br> Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. |
| --upgrade – časový limit | Doba, po kterou musí být celkový upgrade dokončen před provedením FailureAction.  Výchozí \: p10675199dt02h48m 05.4775807 s. <br><br> Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. |
| --Warning-as-Error | Označuje, zda jsou upozornění zpracována se stejnou závažností jako chyby. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-application-upgrade-resume"></a>Upgrade aplikace sfctl – obnovení
Obnoví upgrade aplikace v clusteru Service Fabric.

Obnoví nemonitorované ruční Service Fabric upgrade aplikace. Service Fabric upgraduje jednu upgradovací doménu v jednom okamžiku. Pro nemonitorované ruční upgrady po Service Fabric dokončení domény upgradu čeká na volání rozhraní API, než přejdete k další upgradovací doméně.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --upgrade-název domény [povinné] | Název upgradovací domény, ve které se má pokračovat v upgradu. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-application-upgrade-rollback"></a>Upgrade aplikace sfctl – vrácení zpět
Spustí vracení zpět aktuálně probíhajícího upgradu aplikace v clusteru Service Fabric.

Spustí vrácení aktuálního upgradu aplikace na předchozí verzi. Toto rozhraní API se dá použít jenom k vrácení aktuálního probíhajícího upgradu, který se předává zpět na novou verzi. Pokud se aplikace momentálně neupgraduje, použijte rozhraní StartApplicationUpgrade API k jejímu upgradu na požadovanou verzi, včetně návratu na předchozí verzi.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-application-upgrade-status"></a>Upgrade aplikace sfctl – stav
Získá podrobnosti o nejnovějším upgradu provedeném v této aplikaci.

Vrátí informace o stavu nejnovější inovace aplikace spolu s podrobnostmi, které pomáhají při ladění problémů se stavem aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-application-upload"></a>nahrání aplikace sfctl
Zkopírujte balíček aplikace Service Fabric do úložiště imagí.

Volitelně můžete zobrazit průběh nahrávání pro každý soubor v balíčku. Do se pošle průběh nahrávání `stderr` .

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Path [povinné] | Cesta k balíčku místní aplikace |
| --Compress | Platí pouze pro Service Fabric balíčky aplikací. Vytvořte novou složku obsahující komprimovaný balíček aplikace do výchozího umístění nebo do umístění určeného parametrem Compressed location a potom nahrajte nově vytvořenou složku. <br><br> Pokud již komprimovaný soubor vygenerovala služba sfctl, bude při nastavení tohoto příznaku přepsán. Pokud adresář není balíček aplikace, bude vrácena chyba. Je-li již komprimovaný balíček aplikace, složka bude zkopírována do umístění. Ve výchozím nastavení se nově vytvořený komprimovaný balíček aplikace odstraní po úspěšném nahrání. Pokud nahrávání není úspěšné, podle potřeby prosím vyčistěte komprimovaný balíček. Odstraněním není odebrána žádná prázdná adresářů, která mohla být vytvořena, pokud parametr komprimovaného umístění odkazuje na neexistující adresáře. |
| --komprimované – umístění | Umístění, do kterého se má umístit komprimovaný balíček aplikace <br><br> Pokud není zadané žádné umístění, komprimovaný balíček se umístí do nově vytvořené složky s názvem sfctl_compressed_temp v nadřazeném adresáři zadaném v argumentu cesta. Pokud například argument path má hodnotu C \: /FolderA/AppPkg, pak se komprimovaný balíček přidá do jazyka C \: /FolderA/sfctl_compressed_temp/apppkg. |
| --úložiště bitových kopií-řetězec | Cílové úložiště imagí, do kterého se má balíček aplikace nahrát  Výchozí \: prostředky infrastruktury \: úložiště bitových kopií. <br><br> Chcete-li nahrávat do umístění souboru, spusťte tento parametr se souborem ' file \: '. V opačném případě by měla být hodnota připojovací řetězec úložiště imagí, jako je například výchozí hodnota. |
| --Keep-Compressed | Určuje, jestli se má při úspěšném dokončení nahrávání zachovat vygenerovaný komprimovaný balíček. <br><br> Pokud není nastavená, budou se po úspěšném dokončení odstranit komprimované balíčky aplikací. Pokud odeslání nebylo úspěšné, balíček aplikace bude vždycky uložený ve výstupním adresáři pro opětovné nahrání. |
| --show-Progress | Zobrazit průběh nahrávání souborů pro velké balíčky. |
| --Timeout-t | Celkový časový limit v sekundách. Nahrávání selže a vrátí chybu po uplynutí časového limitu pro odeslání. Tento časový limit se vztahuje k celému balíčku aplikace a časové limity jednotlivých souborů budou stejné jako zbývající doba trvání časového limitu. Časový limit nezahrnuje dobu potřebnou ke komprimaci balíčku aplikace.  Výchozí hodnota je \: 300. |

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
