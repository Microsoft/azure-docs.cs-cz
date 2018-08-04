---
title: Aplikace Azure Service Fabric CLI - sfctl | Dokumentace Microsoftu
description: Popisuje příkazy Service Fabric CLI sfctl aplikace.
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
ms.openlocfilehash: 40ec204f105b32c8b7d9e2dda6f6f3c3023b2d44
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495454"
---
# <a name="sfctl-application"></a>sfctl application
Vytvářet, odstraňovat a spravovat aplikace a typy aplikací.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| vytvoření | Vytvoří aplikaci Service Fabric pomocí zadaný popis. |
| delete | Odstraní stávající aplikace Service Fabric. |
| nasazení | Získá informace o aplikaci nasazenou na uzlu Service Fabric. |
| Stav nasazení | Získá informace o stavu aplikace nasazené na uzlu Service Fabric. |
| nasazení seznamu | Získá seznam aplikací, které jsou nasazené na uzlu Service Fabric. |
| stav | Získá stav aplikace service fabric. |
| informace | Získá informace o aplikaci Service Fabric. |
| Seznam | Získá seznam aplikací vytvořených v clusteru Service Fabric, které se shodují se zadanými filtry. |
| načítání | Získá načíst informace o aplikaci Service Fabric. |
| Manifest | Získá manifest popisující typ aplikace. |
| Zřizování | Předpisy nebo registrech typ aplikace Service Fabric s pomocí balíčku .sfpkg v externím úložišti nebo pomocí balíčku aplikace do úložiště imagí clusteru. |
| Stav sestavy | Odešle zprávu o stavu v aplikaci Service Fabric. |
| type | Získá seznam typů aplikací v clusteru Service Fabric odpovídající zadanému názvu. |
| seznam typů | Získá seznam typů aplikací v clusteru Service Fabric. |
| zrušení zajišťování | Odebere nebo zruší registraci typu aplikace Service Fabric z clusteru. |
| upgradovat | Začne se upgradovat aplikace v clusteru Service Fabric. |
| Upgrade resume | Obnoví upgrade aplikace v clusteru Service Fabric. |
| vrácení upgradu zpět | Spustí se vracení zpět aktuálně probíhající upgrade aplikace v clusteru Service Fabric. |
| Stav upgradu | Načte podrobnosti pro upgrade na nejnovější provést u této aplikace. |
| nahrání | Zkopírujte balíček aplikace Service Fabric do úložiště imagí. |

## <a name="sfctl-application-create"></a>Vytvoření aplikace sfctl
Vytvoří aplikaci Service Fabric pomocí zadaný popis.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| [povinný] název – aplikace | Název aplikace, včetně "fabric\:' schéma identifikátoru URI. |
| [povinné] Typ – aplikace | Název typu aplikace nalezena v manifestu aplikace. |
| [povinné] verze – aplikace | Verze typu aplikace, jak jsou definovány v manifestu aplikace. |
| – maximální počet uzlů | Maximální počet uzlů, ve kterém se Service Fabric rezervaci kapacity pro tuto aplikaci. Všimněte si, že to neznamená, že služby této aplikace se umístí na všechny tyto uzly. |
| --metriky | JSON kódovaný seznam popisů metriky kapacity aplikace. Metriky je definován jako název přidružené sady kapacity pro každý uzel, který aplikace existuje na. |
| – minimální počet uzlů | Minimální počet uzlů, ve kterém se Service Fabric rezervaci kapacity pro tuto aplikaci. Všimněte si, že to neznamená, že služby této aplikace se umístí na všechny tyto uzly. |
| --Parametry | JSON kódovaný seznam parametrů aplikace: přepsání použít při vytváření aplikace. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-application-delete"></a>sfctl application delete
Odstraní stávající aplikace Service Fabric.

Aplikace musí být vytvořeny, aby se Dal odstranit. Odstraňuje se aplikace se odstraní všechny služby, které jsou součástí této aplikace. Ve výchozím nastavení Service Fabric se pokusí zavřete repliky služby řádné způsobem a pak odstraňte službu. Ale pokud služba je potíže řádně zavření repliky, operace odstranění může trvat dlouhou dobu nebo zablokuje. Použijte volitelný příznak ForceRemove vynuceně odstranit aplikace a všech jejích služeb a přeskočit řádné zavření sekvence.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
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

## <a name="sfctl-application-deployed"></a>sfctl nasazené aplikace
Získá informace o aplikaci nasazenou na uzlu Service Fabric.

Tento dotaz vrátí informace o aplikaci system, pokud je zadané ID aplikace pro aplikaci systému. Výsledky zahrnovat nasazené aplikace v aktivní, aktivace a stahování stavy. Tento dotaz vyžaduje, aby název uzlu odpovídá uzlu v clusteru. Dotaz selže, pokud název zadaný uzel neodkazuje na žádné uzly aktivní Service Fabric v clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --[povinný] název uzlu | Název uzlu. |
| --zahrnují stav stavu | Zahrnují stav entity. Pokud má parametr hodnotu false, nebo není zadaný, je vrácen stav "Neznámý". Při přechodu nastavenou na hodnotu true, dotaz paralelně uzlu a stav systému předtím, než se sloučí výsledky. V důsledku toho dotazu je nákladnější a může trvat delší dobu. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-application-deployed-health"></a>nasazení aplikace sfctl-health
Získá informace o stavu aplikace nasazené na uzlu Service Fabric.

Získá informace o stavu aplikace nasazené na uzlu Service Fabric. Pomocí EventsHealthStateFilter volitelně filtruje pro kolekci objektů HealthEvent hlášené pro nasazenou aplikaci na základě stavu. Pomocí DeployedServicePackagesHealthStateFilter volitelně filtruje pro děti DeployedServicePackageHealth na základě stavu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --[povinný] název uzlu | Název uzlu. |
| --Deployed-Service-Packages-Health-State-Filter | Umožňuje filtrování nasazená služba balíček stavu stavu objektů vrácené ve výsledku dotazu Stav nasazení aplikace na základě jejich stavu. Možné hodnoty pro tento parametr obsahovat celočíselnou hodnotu některého z následujících stavů. Pouze nasazení služby, které jsou vráceny balíčky, které odpovídají filtru. Všechny nasazené balíčky služeb se používají k vyhodnocení agregovaný stav nasazených aplikací. Pokud není zadán, budou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota může být kombinací těchto hodnot, pomocí bitový operátor "OR". Například pokud zadaná hodnota je 6 stav balíčky služeb s hodnotou elementu HealthState OK (2) a upozorněním (4), jsou vráceny.  <br> – Výchozí – výchozí hodnota. Odpovídá jakékoli stav HealthState. Hodnota je nula.  <br> -Žádný - filtr, který se pravděpodobně neshoduje s žádnou hodnotu stavu HealthState. Použít, aby nevracela žádné výsledky na dané kolekce stavů. Hodnota je 1.  <br> -Ok – umožňuje filtrovat, že odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Warning - filtr, že hodnota vstupu odpovídá k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který se shoduje s hodnotou elementu HealthState Chyba vstupu. Hodnota je 8.  <br> -All - filtr, který odpovídá s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --události stavu stavu filtru | Umožňuje filtrování vrácených objektů HealthEvent kolekce na základě stavu. Možné hodnoty pro tento parametr obsahovat celočíselnou hodnotu některého z následujících stavů. Vrátí se jenom události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadán, budou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota může být kombinací těchto hodnot, pomocí bitový operátor "OR". Například pokud zadaná hodnota je 6 všechny události s hodnotou elementu HealthState OK (2) a upozorněním (4), jsou vráceny.  <br> – Výchozí – výchozí hodnota. Odpovídá jakékoli stav HealthState. Hodnota je nula.  <br> -Žádný - filtr, který se pravděpodobně neshoduje s žádnou hodnotu stavu HealthState. Použít, aby nevracela žádné výsledky na dané kolekce stavů. Hodnota je 1.  <br> -Ok – umožňuje filtrovat, že odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Warning - filtr, že hodnota vstupu odpovídá k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který se shoduje s hodnotou elementu HealthState Chyba vstupu. Hodnota je 8.  <br> -All - filtr, který odpovídá s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --exclude statistiky stavu | Určuje, zda má být vrácen statistik stavu jako součást výsledků dotazu. Ve výchozím nastavení False. Statistiky ukazují počet podřízených entit ve stavu Ok, upozornění a chyby. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-application-deployed-list"></a>nasazení aplikace sfctl-list
Získá seznam aplikací, které jsou nasazené na uzlu Service Fabric.

Získá seznam aplikací, které jsou nasazené na uzlu Service Fabric. Výsledky neobsahují informace o aplikacích nasazený systém, pokud explicitně dotázali podle ID. Výsledky zahrnovat nasazené aplikace v aktivní, aktivace a stahování stavy. Tento dotaz vyžaduje, aby název uzlu odpovídá uzlu v clusteru. Dotaz selže, pokud název zadaný uzel neodkazuje na žádné uzly aktivní Service Fabric v clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinný] název uzlu | Název uzlu. |
| --token pro pokračování | Parametr tokenu pokračování slouží k získání další sadu výsledků. Token pro pokračování se neprázdná hodnota je zahrnutý v odpovědi rozhraní API, když výsledky ze systému se nevejdou do odpověď o jedné. Když je tato hodnota předána na další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak pokračovací token neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódování URL. |
| --zahrnují stav stavu | Zahrnují stav entity. Pokud má parametr hodnotu false, nebo není zadaný, je vrácen stav "Neznámý". Při přechodu nastavenou na hodnotu true, dotaz paralelně uzlu a stav systému předtím, než se sloučí výsledky. V důsledku toho dotazu je nákladnější a může trvat delší dobu. |
| – maximální počet výsledků | Maximální počet výsledků, které má být vrácena jako součást stránkové dotazy. Tento parametr definuje horní mez počtu výsledky. Výsledky se vrátí, může být nižší než zadané maximální počet výsledků, pokud se nevejdou do zprávy podle omezení velikosti maximální počet zpráv definované v konfiguraci. Pokud tento parametr je nula, nebo není zadán, obsahuje stránkovaného dotazu tolik výsledky nejdříve, který se vejde v návratové zprávě. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-application-health"></a>sfctl stavu aplikace
Získá stav aplikace service fabric.

Vrátí stav stavu aplikace service fabric. Odpověď hlásí stav Ok, chybě nebo upozornění. Pokud entita nebyla nalezena v úložišti stavů, vrátí chyba.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| – nasazení aplikace – stavu – stav – filtrování | Umožňuje filtrování nasazené aplikace stavu stavu objektů vrácené ve výsledku dotazu stavu aplikace na základě jejich stavu. Možné hodnoty pro tento parametr obsahovat celočíselnou hodnotu některého z následujících stavů. Vrátí se pouze nasazené aplikace, které odpovídají filtru. Všechny nasazené aplikace se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadán, budou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota může být kombinací těchto hodnot, pomocí bitový operátor "OR". Například pokud zadaná hodnota je 6 stav nasazených aplikací s hodnotou elementu HealthState OK (2) a upozorněním (4), jsou vráceny.  <br> – Výchozí – výchozí hodnota. Odpovídá jakékoli stav HealthState. Hodnota je nula.  <br> -Žádný - filtr, který se pravděpodobně neshoduje s žádnou hodnotu stavu HealthState. Použít, aby nevracela žádné výsledky na dané kolekce stavů. Hodnota je 1.  <br> -Ok – umožňuje filtrovat, že odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Warning - filtr, že hodnota vstupu odpovídá k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který se shoduje s hodnotou elementu HealthState Chyba vstupu. Hodnota je 8.  <br> -All - filtr, který odpovídá s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --události stavu stavu filtru | Umožňuje filtrování vrácených objektů HealthEvent kolekce na základě stavu. Možné hodnoty pro tento parametr obsahovat celočíselnou hodnotu některého z následujících stavů. Vrátí se jenom události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadán, budou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota může být kombinací těchto hodnot, pomocí bitový operátor "OR". Například pokud zadaná hodnota je 6 všechny události s hodnotou elementu HealthState OK (2) a upozorněním (4), jsou vráceny.  <br> – Výchozí – výchozí hodnota. Odpovídá jakékoli stav HealthState. Hodnota je nula.  <br> -Žádný - filtr, který se pravděpodobně neshoduje s žádnou hodnotu stavu HealthState. Použít, aby nevracela žádné výsledky na dané kolekce stavů. Hodnota je 1.  <br> -Ok – umožňuje filtrovat, že odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Warning - filtr, že hodnota vstupu odpovídá k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který se shoduje s hodnotou elementu HealthState Chyba vstupu. Hodnota je 8.  <br> -All - filtr, který odpovídá s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --exclude statistiky stavu | Určuje, zda má být vrácen statistik stavu jako součást výsledků dotazu. Ve výchozím nastavení False. Statistiky ukazují počet podřízených entit ve stavu Ok, upozornění a chyby. |
| --služby health stavu filtru | Umožňuje filtrování stavu objektů služby stavu vrácené ve výsledku dotazu stavu služby na základě jejich stavu. Možné hodnoty pro tento parametr obsahovat celočíselnou hodnotu některého z následujících stavů. Jsou vráceny pouze služby, které odpovídají filtru. Všechny služby se používá k vyhodnocení agregovaný stav v pořádku. Pokud není zadán, budou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota může být kombinací těchto hodnot, pomocí bitový operátor "OR". Například pokud zadaná hodnota je 6 pak stav služeb s hodnotou elementu HealthState OK (2) a upozorněním (4) vrátí se.  <br> – Výchozí – výchozí hodnota. Odpovídá jakékoli stav HealthState. Hodnota je nula.  <br> -Žádný - filtr, který se pravděpodobně neshoduje s žádnou hodnotu stavu HealthState. Použít, aby nevracela žádné výsledky na dané kolekce stavů. Hodnota je 1.  <br> -Ok – umožňuje filtrovat, že odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Warning - filtr, že hodnota vstupu odpovídá k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který se shoduje s hodnotou elementu HealthState Chyba vstupu. Hodnota je 8.  <br> -All - filtr, který odpovídá s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-application-info"></a>informace o aplikaci sfctl
Získá informace o aplikaci Service Fabric.

Vrátí informace o aplikaci, který byl vytvořen nebo se právě vytváří v clusteru Service Fabric a jejichž název odpovídá zadanému jako parametr. Odpověď obsahuje název, typ, stav, parametry a další podrobnosti o aplikaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --Parametry výjimek aplikace | Příznak, který určuje, zda parametry aplikace se vyloučí z výsledku. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-application-list"></a>seznam aplikací sfctl
Získá seznam aplikací vytvořených v clusteru Service Fabric, které se shodují se zadanými filtry.

Získá informace o aplikacích, které byly vytvořeny nebo právě probíhá vytváření v Service Fabric cluster a shodují se zadanými filtry. Odpověď obsahuje název, typ, stav, parametry a další podrobnosti o aplikaci. Pokud aplikace se nevejdou na stránce, vrátí se jednotlivé stránky výsledků a token pro pokračování, které můžete použít k získání na další stránku. Filtry položek ApplicationTypeName a ApplicationDefinitionKindFilter nelze zadat ve stejnou dobu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – aplikace definice typ filtru | Používají k filtrování podle ApplicationDefinitionKind, což je mechanismus, který se používá k definování aplikace Service Fabric.  <br> – Výchozí – výchozí hodnota, která má stejnou funkci jako výběr "All". Hodnota je 0.  <br> -All - filtr, který odpovídá s libovolnou hodnotou ApplicationDefinitionKind. Hodnota je 65535.  <br> -ServiceFabricApplicationDescription – filtr, který se shoduje s hodnotou ApplicationDefinitionKind ServiceFabricApplicationDescription vstup. Hodnota je 1.  <br> -Compose - filtr, který odpovídá vstupní hodnotou ApplicationDefinitionKind Compose. Hodnota je 2. |
| – Název typu aplikace | Název typu aplikace použít k filtrování aplikace, které chcete dotázat. Tato hodnota by neměla obsahovat verzi typu aplikace. |
| --token pro pokračování | Parametr tokenu pokračování slouží k získání další sadu výsledků. Token pro pokračování se neprázdná hodnota je zahrnutý v odpovědi rozhraní API, když výsledky ze systému se nevejdou do odpověď o jedné. Když je tato hodnota předána na další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak pokračovací token neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódování URL. |
| --Parametry výjimek aplikace | Příznak, který určuje, zda parametry aplikace se vyloučí z výsledku. |
| – maximální počet výsledků | Maximální počet výsledků, které má být vrácena jako součást stránkové dotazy. Tento parametr definuje horní mez počtu výsledky. Výsledky se vrátí, může být nižší než zadané maximální počet výsledků, pokud se nevejdou do zprávy podle omezení velikosti maximální počet zpráv definované v konfiguraci. Pokud tento parametr je nula, nebo není zadán, obsahuje stránkovaného dotazu tolik výsledky nejdříve, který se vejde v návratové zprávě. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-application-load"></a>sfctl zatížení aplikace
Získá načíst informace o aplikaci Service Fabric.

Vrátí informace o načtení o aplikaci, který byl vytvořen nebo se právě vytváří v clusteru Service Fabric a jejichž název odpovídá zadanému jako parametr. Odpověď obsahuje název, minimální počet uzlů, maximální počet uzlů, počet uzlů, které aktuálně zabírá aplikace a aplikace načíst metriky informace o aplikaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-application-manifest"></a>sfctl manifest aplikace
Získá manifest popisující typ aplikace.

Odpověď obsahuje XML manifestu aplikace jako řetězec.

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

## <a name="sfctl-application-provision"></a>sfctl zřizování aplikací
Předpisy nebo registrech typ aplikace Service Fabric s pomocí balíčku .sfpkg v externím úložišti nebo pomocí balíčku aplikace do úložiště imagí clusteru.

Zřídí typ aplikace Service Fabric s clusterem. To se dá vytvořit instance nové aplikace. Operace zřízení můžete provést buď v balíčku aplikace zadaná relativePathInImageStore, nebo pomocí identifikátoru URI externí .sfpkg. Pokud – zřízení externí není nastavený, tento příkaz bude očekávat, že image store zřizování.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – aplikace package--identifikátor uri pro stažení | Cesta k balíčku aplikace ".sfpkg" odkud balíček aplikace můžete stáhnout pomocí protokolů HTTP nebo HTTPS. <br><br> Externí typ zřizování úložiště pouze. Balíček aplikace mohou být uloženy v externím úložišti, který poskytuje operace GET a stáhněte soubor. Podporované protokoly jsou HTTP a HTTPS, a cesta musí umožňovat přístup pro čtení. |
| ---Typ sestavení cesta aplikace | Pro zřízení pouze úložiště typu bitové kopie. Relativní cesta k balíčku aplikace v úložišti imagí zadaný během operace předchozího odeslání. |
| – Název typu aplikace | Externí typ zřizování úložiště pouze. Název typu aplikace představuje název typu aplikace, které jsou součástí manifestu aplikace. |
| --verzi typu aplikace. | Externí typ zřizování úložiště pouze. Verze typu aplikace představuje verzi jazyka aplikace typ nalezen v manifestu aplikace. |
| --externí zřizování | Umístění, kde můžete registrovat nebo zřídit balíčku aplikace. Označuje, že poskytování je pro balíček aplikace, která se předtím nahrála do externího úložiště. Balíček aplikace končí *.sfpkg rozšíření. |
| --no-wait | Určuje, zda zřizování by měla probíhat asynchronně. <br><br> Pokud je nastavena na hodnotu true, vrátí operace zřízení po přijetí žádosti systém a operace zřízení pokračuje bez jakékoli časový limit. Výchozí hodnota je false. U velkých balíčků aplikací doporučujeme nastavit hodnotu na hodnotu true. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-application-report-health"></a>Stav sestavy aplikace sfctl
Odešle zprávu o stavu v aplikaci Service Fabric.

Sestavy stavu zadané aplikace Service Fabric. Sestava může obsahovat informace o zdroji sestava stavu a na kterém je uvedena vlastnost. Sestava je odeslána do Service Fabric gateway aplikace, který se předává k úložišti stavů. Sestava může být přijal brány, ale odmítnuté úložiště stavu po další ověření. Úložiště stavu může třeba odmítnout sestavy z důvodu neplatného parametru, jako jsou zastaralé pořadové číslo. Pokud chcete zobrazit, zda byla použita sestava v health store, získat stav aplikace a zkontrolujte, zda je sestava zobrazena.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. <br><br> Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "prostředků infrastruktury\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| – Stav – vlastnost [povinné] | Vlastnost s informacemi o stavu. <br><br> Entita může mít sestav o stavu pro různé vlastnosti. Vlastnost je řetězec a oprava výčet umožňují flexibilní reportérka ke kategorizaci podmínku stavu, který spouští sestavu. Například reportérka s ID zdroje "LocalWatchdog" můžete monitorovat stav disku v uzlu, tak ho může hlásit vlastnost "AvailableDisk" v tomto uzlu. Stejné reportérka můžete monitorovat připojení uzlu, takže ho může hlásit vlastnost "Připojení" ve stejném uzlu. V health store tyto sestavy jsou považovány za události samostatné stavu pro zadaný uzel. Spolu s ID zdroje vlastnost jednoznačně identifikuje informací o stavu. |
| – Stav [povinné] | Možné hodnoty zahrnují\: "Neplatný", "Ok", "Upozorňující", "Chyba", "Neznámý". |
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

## <a name="sfctl-application-type"></a>Typ aplikace sfctl
Získá seznam typů aplikací v clusteru Service Fabric odpovídající zadanému názvu.

Vrátí informace o typech aplikací, které jsou zřízené nebo se právě zřizuje v clusteru Service Fabric. Tyto výsledky se typů aplikací, jejíž název odpovídá přesně je zadán jako parametr a které jsou v souladu s parametry, které daný dotaz. Každá verze vrácena jako jeden typ aplikace se vrátí všechny verze typu aplikace odpovídající název typu aplikace. Odpověď obsahuje název, verze, stav a další podrobnosti o typu aplikace. Toto je stránkovaného dotazu, což znamená, není-li všechny typy aplikací vejde na stránku jednotlivé stránky výsledků je vrácena a token pro pokračování, které můžete použít k získání na další stránku. Například pokud existují 10 typy aplikací, ale na stránce odpovídá pouze první tři aplikace typy nebo maximální počet výsledků je nastavena na hodnotu 3, 3 je vráceny. Pro přístup k rest výsledky, načtení další stránky pomocí token pro pokračování vrácený v dalším dotazu. Prázdný pokračovací token je vrácena, pokud nejsou žádné další stránky.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – aplikace type-name [povinné] | Název typu aplikace. |
| --verzi typu aplikace. | Verze typu aplikace. |
| --token pro pokračování | Parametr tokenu pokračování slouží k získání další sadu výsledků. Token pro pokračování se neprázdná hodnota je zahrnutý v odpovědi rozhraní API, když výsledky ze systému se nevejdou do odpověď o jedné. Když je tato hodnota předána na další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak pokračovací token neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódování URL. |
| --Parametry výjimek aplikace | Příznak, který určuje, zda parametry aplikace se vyloučí z výsledku. |
| – maximální počet výsledků | Maximální počet výsledků, které má být vrácena jako součást stránkové dotazy. Tento parametr definuje horní mez počtu výsledky. Výsledky se vrátí, může být nižší než zadané maximální počet výsledků, pokud se nevejdou do zprávy podle omezení velikosti maximální počet zpráv definované v konfiguraci. Pokud tento parametr je nula, nebo není zadán, obsahuje stránkovaného dotazu tolik výsledky nejdříve, který se vejde v návratové zprávě. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-application-type-list"></a>sfctl aplikace – seznam typů
Získá seznam typů aplikací v clusteru Service Fabric.

Vrátí informace o typech aplikací, které jsou zřízené nebo se právě zřizuje v clusteru Service Fabric. Každá verze typu aplikace, je vrácena jako typ jednu aplikaci. Odpověď obsahuje název, verze, stav a další podrobnosti o typu aplikace. Toto je stránkovaného dotazu, což znamená, není-li všechny typy aplikací vejde na stránku jednotlivé stránky výsledků je vrácena a token pro pokračování, které můžete použít k získání na další stránku. Například pokud existují 10 typy aplikací, ale na stránce odpovídá pouze první tři aplikace typy nebo maximální počet výsledků je nastavena na hodnotu 3, 3 je vráceny. Pro přístup k rest výsledky, načtení další stránky pomocí token pro pokračování vrácený v dalším dotazu. Prázdný pokračovací token je vrácena, pokud nejsou žádné další stránky.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| ---Typ filtr definice aplikace typ | Používají k filtrování podle ApplicationTypeDefinitionKind, což je mechanismus, který se používá k definování typ aplikace Service Fabric.  <br> – Výchozí – výchozí hodnota, která má stejnou funkci jako výběr "All". Hodnota je 0.  <br> -All - filtr, který odpovídá s libovolnou hodnotou ApplicationTypeDefinitionKind. Hodnota je 65535.  <br> -ServiceFabricApplicationPackage – filtr, který se shoduje s hodnotou ApplicationTypeDefinitionKind ServiceFabricApplicationPackage vstup. Hodnota je 1.  <br> -Compose - filtr, který odpovídá vstupní hodnotou ApplicationTypeDefinitionKind Compose. Hodnota je 2. |
| --token pro pokračování | Parametr tokenu pokračování slouží k získání další sadu výsledků. Token pro pokračování se neprázdná hodnota je zahrnutý v odpovědi rozhraní API, když výsledky ze systému se nevejdou do odpověď o jedné. Když je tato hodnota předána na další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak pokračovací token neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódování URL. |
| --Parametry výjimek aplikace | Příznak, který určuje, zda parametry aplikace se vyloučí z výsledku. |
| – maximální počet výsledků | Maximální počet výsledků, které má být vrácena jako součást stránkové dotazy. Tento parametr definuje horní mez počtu výsledky. Výsledky se vrátí, může být nižší než zadané maximální počet výsledků, pokud se nevejdou do zprávy podle omezení velikosti maximální počet zpráv definované v konfiguraci. Pokud tento parametr je nula, nebo není zadán, obsahuje stránkovaného dotazu tolik výsledky nejdříve, který se vejde v návratové zprávě. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-application-unprovision"></a>sfctl application provision
Odebere nebo zruší registraci typu aplikace Service Fabric z clusteru.

Tuto operaci lze provést pouze v případě byly odstraněny všechny instance aplikace typu aplikace. Jakmile se registrace typu aplikace, vytvářet žádné nové instance aplikace pro tento typ konkrétní aplikaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – aplikace type-name [povinné] | Název typu aplikace. |
| --– verzi typu aplikace – [povinné] | Verze typu aplikace, jak jsou definovány v manifestu aplikace. |
| --async-parameter | Příznak označující, zda unprovision by měla probíhat asynchronně. Pokud je nastavena na hodnotu true, vrátí unprovision operace při přijetí žádosti systém a operace unprovision pokračuje bez jakékoli časový limit. Výchozí hodnota je false. Doporučujeme však ji nastavíte na hodnotu true pro velkých balíčků aplikací, které byly zřízeny. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-application-upgrade"></a>upgrade aplikace sfctl
Začne se upgradovat aplikace v clusteru Service Fabric.

Parametry upgradu aplikace zadaný ověří a začne se upgradovat aplikace, pokud jsou platné parametry. Všimněte si, že upgrade popis nahradí existující popis aplikace. To znamená, že pokud nejsou zadány parametry, stávající parametry na aplikacích, které se přepíše seznamu prázdné parametry. Výsledkem by se aplikace používají výchozí hodnoty parametrů z manifestu aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. <br><br> Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "prostředků infrastruktury\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| – verze aplikace [povinné] | Cílová verze aplikace. |
| --parametry [povinné] | Seznam JSON kódovaný parametr aplikace funkci přepsání hodnot a použijí při upgradu aplikace. |
| --default-service-health-policy | JSON kódovaný specifikace zásad stavu, ve výchozím nastavení používá k vyhodnocení stavu daného typu služby. |
| --selhání akce | Akce, který se má provést při upgradu na monitorované pomocí monitorování zjistí monitorování porušení zásad nebo stavu zásad. |
| --force restartování | Vynuceně restartování procesů během upgradu, i když nedošlo ke změně verze kódu. |
| – Stav –--časový limit opakování kontroly | Množství času opakovat hodnocení stavu, pokud aplikace nebo clusteru není v pořádku před selhání akce je provedena. Měří v milisekundách.  Výchozí\: PT0H10M0S. |
| --stavu zaškrtnutí stabilní trvání | Množství času, aplikace nebo clusteru musí zůstane v dobrém stavu před provedením upgradu k další upgradovací doméně. Měří v milisekundách.  Výchozí\: PT0H2M0S. |
| – stav –--Čekání na kontrolu | Množství času po dokončení upgradu domény před použitím zásad stavu. Měří v milisekundách.  Výchozí\: 0. |
| --max-unhealthy-apps | Maximální povolené procento poškozené nasazené aplikace. Vyjádřené číslem v rozmezí 0 až 100. |
| --režimu | Režim, který slouží k monitorování stavu během upgradu se zajištěním provozu.  Výchozí\: UnmonitoredAuto. |
| --repliky sady kontrola-časový limit | Maximální množství času blokování zpracování logických sítí a zabránit ztrátě dostupnosti, když dochází k neočekávaným problémům. Měří v sekundách. |
| --service-health-policy | JSON kódovaný mapování zásad stavu typ služby za název typu služby. Mapa je prázdný jako výchozí. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |
| --upgrade-domain-timeout | Množství času každé domény upgradu musí dokončit před provedením FailureAction. Měří v milisekundách.  Výchozí\: P10675199DT02H48M05.4775807S. |
| --upgrade vypršení časového limitu | Množství času celkové upgrade musí dokončit před provedením FailureAction. Měří v milisekundách.  Výchozí\: P10675199DT02H48M05.4775807S. |
| --upozornění jako chyby | Upozornění vyhodnocení stavu se stejným závažností považovat za chyby. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-application-upgrade-resume"></a>upgrade aplikace sfctl-obnovit
Obnoví upgrade aplikace v clusteru Service Fabric.

Obnoví nemonitorované aplikace Service Fabric ruční upgrade. Service Fabric se upgraduje jednu upgradovací doménu najednou. Pro nemonitorované ručních upgradech po dokončení upgradu domény, Service Fabric počká abyste mohli volat toto rozhraní API než budete pokračovat k další upgradovací doméně.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --upgrade-domain-name [povinné] | Název upgradovací doména, do kterého chcete pokračovat v upgradu. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-application-upgrade-rollback"></a>upgrade aplikace sfctl – vrácení
Spustí se vracení zpět aktuálně probíhající upgrade aplikace v clusteru Service Fabric.

Spustí vrácení zpět aktuální aplikaci upgradovat předchozí verzi. Toto rozhraní API jde použít jenom k vrácení aktuální v průběhu upgradu, který je Posunutí vpřed na novou verzi. Pokud aplikace není v tuto chvíli upgraduje pomocí rozhraní API StartApplicationUpgrade ho upgradovat na požadovanou verzi, včetně vrácení zpět na předchozí verzi.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-application-upgrade-status"></a>upgrade aplikace sfctl-status
Načte podrobnosti pro upgrade na nejnovější provést u této aplikace.

Vrátí informace o stavu nejnovější inovace aplikací spolu s podrobnostmi k ladění problémů se stavem žádosti o podporu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-application-upload"></a>sfctl nahrávání aplikace
Zkopírujte balíček aplikace Service Fabric do úložiště imagí.

Volitelně můžete zobrazte průběh nahrávání pro každý soubor v balíčku. Nahrát průběh posílá `stderr`.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Cesta [povinné] | Cesta k balíčku místní aplikace. |
| --imagestore řetězec | Cílové image ukládání pro nahrání balíčku aplikace.  Výchozí\: fabric\:ImageStore. |
| --show průběh | Zobrazení průběhu nahrávání souboru pro velké balíčky. |

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
