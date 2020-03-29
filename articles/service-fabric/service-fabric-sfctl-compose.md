---
title: Azure Service Fabric CLI- sfctl compose
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro aplikace Docker Compose.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1e40ca4e3c5ec8b7566646aa7ef723bd4c9e45a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906129"
---
# <a name="sfctl-compose"></a>sfctl compose
Vytvářejte, odstraňujte a spravujte aplikace Docker Compose.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| vytvoření | Vytvoří service fabric sestavit nasazení. |
| list | Získá seznam komponování nasazení vytvořených v clusteru Service Fabric. |
| remove | Odstraní existující service fabric compose nasazení z clusteru. |
| status | Získá informace o nasazení fabric služby. |
| Upgrade | Spustí upgrade nasazení compose v clusteru Service Fabric. |
| vrácení upgradu | Spustí vrácení upgradu nasazení komponování v clusteru Service Fabric. |
| stav upgradu | Získá podrobnosti o nejnovější upgrade provádí na této služby fabric sestavit nasazení. |

## <a name="sfctl-compose-create"></a>sfctl compose vytvořit
Vytvoří service fabric sestavit nasazení.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název nasazení [Povinné] | Název nasazení. |
| --cesta k souboru [Povinné] | Cesta k cílovému souboru Docker Compose. |
| --šifrovaný průchod | Místo zobrazení výzvy k zadání hesla registru kontejneru použijte již šifrovanou heslem. |
| --has-pass | Zobrazí výzvu k zadání hesla do registru kontejneru. |
| --časový čas -t | Výchozí\: 60. |
| --user | Uživatelské jméno pro připojení k registru kontejneru. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-compose-list"></a>sfctl seznam komponů
Získá seznam komponování nasazení vytvořených v clusteru Service Fabric.

Získá stav o komponování nasazení, které byly vytvořeny nebo v procesu vytváření v clusteru Service Fabric. Odpověď obsahuje název, stav a další podrobnosti o nasazení komponování. Pokud se seznam nasazení nevejde na stránku, je vrácena jedna stránka výsledků a token pokračování, který lze použít k získání další stránky.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --continuation-token | Parametr tokenu pokračování se používá k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API, pokud se výsledky ze systému nevejdou do jediné odpovědi. Když je tato hodnota předána dalšímu volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak token pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódována adresou URL. |
| --max-výsledky | Maximální počet výsledků, které mají být vráceny jako součást stránkovaných dotazů. Tento parametr definuje horní mez počtu vrácených výsledků. Vrácené výsledky mohou být menší než zadané maximální výsledky, pokud se nevejdou do zprávy podle omezení maximální velikosti zprávy definované v konfiguraci. Pokud je tento parametr nula nebo není zadán, stránkovaný dotaz obsahuje co nejvíce výsledků, které se vejdou do vrácené zprávy. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-compose-remove"></a>sfctl compose odstranit
Odstraní existující service fabric compose nasazení z clusteru.

Odstraní existující service fabric sestavit nasazení.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název nasazení [Povinné] | Identita nasazení. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-compose-status"></a>sfctl stav komponování
Získá informace o nasazení fabric služby.

Vrátí stav nasazení komponování, které bylo vytvořeno nebo právě vytvářeno v clusteru Service Fabric a jehož název odpovídá názvu zadanému jako parametr. Odpověď obsahuje název, stav a další podrobnosti o nasazení.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název nasazení [Povinné] | Identita nasazení. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-compose-upgrade"></a>sfctl compose upgrade
Spustí upgrade nasazení compose v clusteru Service Fabric.

Ověří zadané parametry upgradu a spustí upgrade nasazení, pokud jsou parametry platné.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název nasazení [Povinné] | Název nasazení. |
| --cesta k souboru [Povinné] | Cesta k cílovému souboru dockerového komponu. |
| --default-svc-type-health-map --default-svc-type-health-map --default-svc-type-health-map --default | JSON kódovaný slovník, který popisuje zásady stavu používané k vyhodnocení stavu služeb. |
| --šifrovaný průchod | Místo zobrazení výzvy k zadání hesla registru kontejneru použijte již šifrovanou heslem. |
| --selhání akce | Možné hodnoty\: zahrnují "Neplatné", "Rollback", 'Manual'. |
| --force-restart | Procesy jsou během upgradu vynuceně restartovány, i když se nezměnila verze kódu. <br><br> Upgrade pouze změní konfiguraci nebo data. |
| --has-pass | Zobrazí výzvu k zadání hesla do registru kontejneru. |
| --retry kontroly stavu | Doba mezi pokusy o provedení kontroly stavu, pokud aplikace nebo cluster není v pořádku. |
| --zdravotní kontrola-stabilní | Doba, po kterou musí aplikace nebo cluster zůstat v pořádku, než upgrade přejde na další doménu upgradu. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. |
| --kontrola stavu-čekání | Doba čekání po dokončení upgradu domény před zahájením procesu kontroly stavu. |
| --replika-set-check | Maximální doba, po kterou je třeba zablokovat zpracování upgradovací domény a zabránit ztrátě dostupnosti v případě neočekávaných problémů. <br><br> Po vypršení tohoto časového limitu bude zpracování domény upgradu pokračovat bez ohledu na problémy se ztrátou dostupnosti. Časový rozsah je resetován na začátku každé domény upgradu. Platné hodnoty jsou mezi 0 a 42949672925 včetně. |
| --svc-type-health-map --svc-type-health-map --svc-type-health-map --s | JSON kódovaný seznam objektů, které popisují zásady stavu slouží k vyhodnocení stavu různých typů služeb. |
| --časový čas -t | Výchozí\: 60. |
| --nezdravé aplikace | Maximální povolené procento nefunkčních aplikací před oznámením chyby. <br><br> Chcete-li například povolit 10 % aplikací není v pořádku, tato hodnota by 10. Procento představuje maximální tolerované procento aplikací, které mohou být nefunkční, než je cluster považován za chybný. Pokud je procento respektováno, ale existuje alespoň jedna aplikace není v pořádku, stav je vyhodnocen jako upozornění. Vypočítá se vydělením počtu aplikací není v pořádku celkový počet instancí aplikace v clusteru. |
| --upgrade-domain-timeout | Doba, po kterou musí být každá upgradovací doména dokončena před provedením akce FailureAction. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. |
| --upgrade-druh | Výchozí\: postupné. |
| --upgrade-mode | Možné hodnoty\: zahrnují "Neplatné", "UnmonitoredAuto", "UnmonitoredManual", "Monitored".  Výchozí\: UnmonitoredAuto. |
| --upgrade-timeout | Doba, po kterou má být dokončena celková inovace, než je provedena akce Selhání. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. |
| --user | Uživatelské jméno pro připojení k registru kontejneru. |
| --upozornění jako chyba | Označuje, zda jsou upozornění považována za chyby se stejnou závažností. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl skládat upgrade-rollback
Spustí vrácení upgradu nasazení komponování v clusteru Service Fabric.

Vrácení zpět infrastruktury služby vytvořit upgrade nasazení.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název nasazení [Povinné] | Identita nasazení. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl komponovat stav upgradu
Získá podrobnosti o nejnovější upgrade provádí na této služby fabric sestavit nasazení.

Vrátí informace o stavu upgradu nasazení compose spolu s podrobnostmi pro podporu ladění problémů se stavem aplikace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název nasazení [Povinné] | Identita nasazení. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

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