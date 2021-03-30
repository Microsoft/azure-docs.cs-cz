---
title: Azure Service Fabric CLI – vytváření sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro Docker Compose aplikace.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: e84b35ff3dfa7be0c848a4d7b906c622d9b88914
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257350"
---
# <a name="sfctl-compose"></a>sfctl compose
Vytvářejte, odstraňujte a spravujte aplikace Docker Compose.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| vytvoření | Vytvoří Service Fabric vytváření nasazení. |
| list | Získá seznam nasazení vytváření vytvořená v clusteru Service Fabric. |
| remove | Odstraní existující Service Fabric vytvoření nasazení z clusteru. |
| status | Načte informace o nasazení Service Fabricho vytváření. |
| upgrade | Spustí upgrade nasazení vytváření v clusteru Service Fabric. |
| upgrade – vrácení zpět | Spustí vrácení upgradu nasazení do sestavení v clusteru Service Fabric. |
| upgrade – stav | Získá podrobnosti o posledním upgradu provedeném na tomto Service Fabric nasazení. |

## <a name="sfctl-compose-create"></a>Vytvoření sfctl
Vytvoří Service Fabric vytváření nasazení.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Deployment-Name [povinné] | Název nasazení. |
| --File-Path [povinné] | Cesta k cílovému souboru Docker Compose. |
| --šifrované – Pass | Namísto zobrazení výzvy pro heslo registru kontejneru použijte již zašifrovanou frázi Pass. |
| --má-Pass | Zobrazí výzvu k zadání hesla k registru kontejneru. |
| --Timeout-t | Výchozí hodnota je \: 60. |
| --user | Uživatelské jméno pro připojení k registru kontejnerů. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-compose-list"></a>sfctl vytvořit seznam
Získá seznam nasazení vytváření vytvořená v clusteru Service Fabric.

Získá stav nasazení vytváření, která byla vytvořena nebo vytvořena v procesu Service Fabric clusteru. Odpověď obsahuje název, stav a další podrobnosti o nasazeních s vytvářením. Pokud se seznam nasazení nevejde na stránku, vrátí se jedna stránka výsledků a také token pro pokračování, který se dá použít k získání další stránky.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --pokračování-token | Parametr tokenu pokračování slouží k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API v případě, že se výsledky ze systému nevejdou do jediné odpovědi. Když se tato hodnota předává do dalšího volání rozhraní API, vrátí rozhraní API další sadu výsledků. Pokud nejsou k dispozici žádné další výsledky, token pokračování neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódovaná v adrese URL. |
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

## <a name="sfctl-compose-remove"></a>sfctl vytváření a odstraňování
Odstraní existující Service Fabric vytvoření nasazení z clusteru.

Odstraní existující nasazení Service Fabricho vytváření.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Deployment-Name [povinné] | Identita nasazení. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-compose-status"></a>sfctl vytváření stavu
Načte informace o nasazení Service Fabricho vytváření.

Vrátí stav nasazení vytváření, který byl vytvořen nebo vytvořen v procesu vytváření v clusteru Service Fabric a jehož název odpovídá parametru zadanému jako parametr. Odpověď obsahuje název, stav a další podrobnosti o nasazení.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Deployment-Name [povinné] | Identita nasazení. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-compose-upgrade"></a>sfctl vytváření upgradu
Spustí upgrade nasazení vytváření v clusteru Service Fabric.

Ověří zadané parametry upgradu a spustí upgrade nasazení, pokud jsou parametry platné.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Deployment-Name [povinné] | Název nasazení. |
| --File-Path [povinné] | Cesta k cílovému souboru sestavení Docker. |
| --Default-svc-Type-map-Health | Slovník kódovaný ve formátu JSON, který popisuje zásady stavu používané k vyhodnocení stavu služeb. |
| --šifrované – Pass | Namísto zobrazení výzvy pro heslo registru kontejneru použijte již zašifrovanou frázi Pass. |
| --Chyba – akce | Možné hodnoty zahrnují \: "neplatné", "Rollback", "Manual". |
| --Force-restart | Procesy jsou během upgradu vynuceně restartovány i v případě, že se verze kódu nezměnila. <br><br> Upgrade pouze změní konfiguraci nebo data. |
| --má-Pass | Zobrazí výzvu k zadání hesla k registru kontejneru. |
| --Health-Check-opakování | Doba mezi pokusy o provedení kontrol stavu, pokud aplikace nebo cluster není v pořádku. |
| --Health-Check-stabilní | Doba, po kterou musí aplikace nebo cluster zůstat v pořádku, než bude upgrade pokračovat na další upgradovací doménu. <br><br> Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. |
| --Health-Check-Wait | Doba, po kterou se má po dokončení upgradu domény počkat, než se spustí proces kontroly stavu. |
| --replice-set-check | Maximální doba, po kterou se má blokovat zpracování upgradovací domény a zabránit ztrátě dostupnosti, pokud dojde k neočekávaným problémům. <br><br> Po vypršení časového limitu bude zpracování upgradovací domény pokračovat bez ohledu na problémy se ztrátou dostupnosti. Časový limit se resetuje na začátku každé upgradovací domény. Platné hodnoty jsou mezi 0 a 42949672925 včetně. |
| --SVC-typ-mapa-stav | Seznam objektů kódovaných pomocí JSON, které popisují zásady stavu používané k vyhodnocení stavu různých typů služeb. |
| --Timeout-t | Výchozí hodnota je \: 60. |
| --není v pořádku – aplikace | Maximální povolené procento nezdravých aplikací před Nahlášením chyby. <br><br> Například pokud chcete, aby 10% aplikací bylo ve špatném stavu, bude tato hodnota 10. Procento představuje maximální povolený procentuální podíl aplikací, které mohou být chybné, než se cluster bude považovat za chybu. Pokud je dodrženo procento, ale existuje aspoň jedna poškozená aplikace, stav se vyhodnotí jako varování. To se počítá vydělením počtu nezdravých aplikací nad celkovým počtem instancí aplikace v clusteru. |
| --upgrade-Domain – vypršel časový limit. | Doba, po kterou musí být každá upgradovací doména dokončena před provedením FailureAction. <br><br> Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. |
| --upgrade-druh | Výchozí \: vracení. |
| --upgrade-režim | Možné hodnoty zahrnují \: "Invalid", "UnmonitoredAuto", "UnmonitoredManual", "Monitored".  Výchozí \: UnmonitoredAuto. |
| --upgrade – časový limit | Doba, po kterou musí být celkový upgrade dokončen před provedením FailureAction. <br><br> Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. |
| --user | Uživatelské jméno pro připojení k registru kontejnerů. |
| --Warning-as-Error | Označuje, zda jsou upozornění zpracována se stejnou závažností jako chyby. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-compose-upgrade-rollback"></a>upgrade sfctlování – vrácení zpět
Spustí vrácení upgradu nasazení do sestavení v clusteru Service Fabric.

Vrátit zpět upgrade nasazení Service fabricu

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Deployment-Name [povinné] | Identita nasazení. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-compose-upgrade-status"></a>upgrade sfctl pro psaní – stav
Získá podrobnosti o posledním upgradu provedeném na tomto Service Fabric nasazení.

Vrátí informace o stavu upgradu nasazení vytváření společně s podrobnostmi, které pomáhají při ladění problémů s výkonem aplikací.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Deployment-Name [povinné] | Identita nasazení. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

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
