---
title: Azure Service Fabric CLI – uzel sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu uzlů clusteru.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f2cf1011db37892f71bdd06f409cec1a76463507
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257156"
---
# <a name="sfctl-node"></a>sfctl node
Spravujte uzly, které tvoří cluster.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| Přidání-konfigurace-parametru-Overrides | Přidá seznam přepsání konfigurace v zadaném uzlu. |
| dezaktivovat | Deaktivuje uzel clusteru Service Fabric se zadaným záměrem deaktivace. |
| aby | Aktivujte uzel clusteru Service Fabric, který je v tuto chvíli deaktivovaný. |
| Get-Configuration-Overrides | Načte seznam přepsání konfigurace v zadaném uzlu. |
| nezávadnost | Získá stav uzlu Service Fabric. |
| příjemce | Získá informace o konkrétním uzlu v clusteru Service Fabric. |
| list | Načte seznam uzlů v clusteru Service Fabric. |
| načítání | Získá informace o načtení uzlu Service Fabric. |
| odebrat – konfigurace – přepsání | Odebere přepsání konfigurace na zadaném uzlu. |
| odebrat – stav | Upozorňuje Service Fabric, že trvalý stav na uzlu se trvale odebral nebo ztratil. |
| Sestava – stav | Odešle zprávu o stavu na uzlu Service Fabric. |
| restart | Restartuje uzel clusteru Service Fabric. |
| Přejít | Spustí nebo zastaví uzel clusteru. |
| přechod – stav | Získá průběh operace zahájené s použitím StartNodeTransition. |

## <a name="sfctl-node-add-configuration-parameter-overrides"></a>sfctl uzel Add-Configuration-Parameter-Overrides
Přidá seznam přepsání konfigurace v zadaném uzlu.

Toto rozhraní API umožňuje přidat všechna existující přepsání konfigurace na zadaný uzel.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --config-parametr-override-list [povinné] | Popis pro přidání seznamu přepsání konfigurace |
| --Node-Name [povinné] | Název uzlu |
| --Force | Vynutí přidání přepsání konfigurace na zadané uzly. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-node-disable"></a>zakázaný uzel sfctl
Deaktivuje uzel clusteru Service Fabric se zadaným záměrem deaktivace.

Deaktivuje uzel clusteru Service Fabric se zadaným záměrem deaktivace. Po deaktivaci lze záměr deaktivace zvýšit, ale ne snížit (například uzel, který je deaktivován s cílem pozastavit, může být dále po restartu dezaktivován, ale nikoli jiným způsobem. Uzly mohou být znovu aktivovány pomocí operace aktivovat uzel kdykoli po jejich deaktivaci. Pokud se deaktivace nedokončí, zrušení deaktivace se zruší. Uzel, který se rozpíná a v rámci kterého se má vrátit, se před tím, než se služby umístí na tento uzel, bude stále potřeba znovu aktivovat.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Node-Name [povinné] | Název uzlu |
| --deaktivace – záměr | Popisuje záměr nebo důvod deaktivace uzlu. Možné hodnoty jsou následující. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-node-enable"></a>Povolit uzel sfctl
Aktivujte uzel clusteru Service Fabric, který je v tuto chvíli deaktivovaný.

Aktivuje uzel clusteru Service Fabric, který je v tuto chvíli deaktivovaný. Po aktivaci se uzel znovu stane životaschopným cílem pro umístění nových replik a všechny deaktivované repliky na uzlu se znovu aktivují.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-node-get-configuration-overrides"></a>sfctl uzel Get-Configuration-Overrides
Načte seznam přepsání konfigurace v zadaném uzlu.

Toto rozhraní API umožňuje získat všechna existující přepsání konfigurace v zadaném uzlu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-node-health"></a>stav uzlu sfctl
Získá stav uzlu Service Fabric.

Získá stav uzlu Service Fabric. Pomocí EventsHealthStateFilter můžete filtrovat kolekci událostí stavu hlášených na uzlu na základě stavu. Pokud uzel, který určíte podle názvu, v Health Store neexistuje, vrátí se chyba.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Node-Name [povinné] | Název uzlu |
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

## <a name="sfctl-node-info"></a>informace o uzlu sfctl
Získá informace o konkrétním uzlu v clusteru Service Fabric.

Odpověď obsahuje název, stav, ID, stav, dobu provozu a další podrobnosti o uzlu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-node-list"></a>seznam uzlů sfctl
Načte seznam uzlů v clusteru Service Fabric.

Odpověď obsahuje název, stav, ID, stav, dobu provozu a další podrobnosti o uzlech.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --pokračování-token | Parametr tokenu pokračování slouží k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API v případě, že se výsledky ze systému nevejdou do jediné odpovědi. Když se tato hodnota předává do dalšího volání rozhraní API, vrátí rozhraní API další sadu výsledků. Pokud nejsou k dispozici žádné další výsledky, token pokračování neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódovaná v adrese URL. |
| --Max – výsledky | Maximální počet výsledků, které mají být vráceny v rámci stránkovaných dotazů. Tento parametr definuje horní mez počtu vrácených výsledků. Vrácené výsledky mohou být menší než zadané maximální výsledky, pokud se nevejdou do zprávy podle maximální velikosti omezení velikosti zprávy definované v konfiguraci. Pokud je tento parametr nula nebo není zadán, dotaz stránkovaného obsahu obsahuje tolik výsledků, kolik jich bylo možné do návratové zprávy. |
| --Node-status-Filter | Umožňuje filtrovat uzly na základě NodeStatus. Vrátí se pouze uzly, které odpovídají zadané hodnotě filtru. Hodnota filtru může být jedna z následujících.  Výchozí \: Výchozí hodnota. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-node-load"></a>zatížení uzlu sfctl
Získá informace o načtení uzlu Service Fabric.

Načte informace o načtení uzlu Service Fabric pro všechny metriky, které mají definovanou zátěž nebo kapacitu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-node-remove-configuration-overrides"></a>odebrání uzlu sfctl-konfigurace – přepsání
Odebere přepsání konfigurace na zadaném uzlu.

Toto rozhraní API umožňuje odebrat všechna existující přepsání konfigurace v zadaném uzlu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-node-remove-state"></a>sfctl uzel-stav odebrání
Upozorňuje Service Fabric, že trvalý stav na uzlu se trvale odebral nebo ztratil.

To znamená, že není možné obnovit trvalý stav tohoto uzlu. K tomu obvykle dochází v případě, že byl pevný disk vymazán nebo pokud dojde k chybě pevného disku. Aby tato operace proběhla úspěšně, musí být uzel vypnutý. Tato operace umožňuje Service Fabric zjistit, že repliky na tomto uzlu již neexistují a že Service Fabric by se měla zastavit, aby se tyto repliky staly zálohovat. Nespouštějte tuto rutinu, pokud se stav uzlu neodebral a uzel se může vrátit se stavem beze změny. Od Service Fabric 6,5, aby bylo možné použít toto rozhraní API pro uzly počáteční hodnoty, změňte uzly počátečních hodnot na běžné (nepočáteční) uzly a potom toto rozhraní API vyvolejte k odebrání stavu uzlu. Pokud je cluster spuštěn v Azure, poté, co se dokončí uzel počáteční hodnoty, Service Fabric se pokusí ho změnit na nepočáteční uzel automaticky. Aby k tomu docházelo, ujistěte se, že počet nepočátečních uzlů v primárním uzlu je nižší než počet počátečních uzlů. V případě potřeby přidejte k primárnímu typu uzlu další uzly, abyste to dosáhli. U samostatného clusteru platí, že pokud se neočekává, že uzel počátečních hodnot nezůstane k zálohování beze změny, odeberte prosím uzel z clusteru, viz protokol HTTPS \: //docs.Microsoft.com/en-US/Azure/Service-Fabric/Service-Fabric-cluster-Windows-Server-Add-Remove-Nodes.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-node-report-health"></a>Sestava uzlu sfctl – stav
Odešle zprávu o stavu na uzlu Service Fabric.

Hlásí stav zadaného uzlu Service Fabric. Sestava musí obsahovat informace o zdroji sestavy stavu a vlastnosti, na které je hlášena. Sestava se odešle do uzlu Service Fabric brány, který se přepošle na Health Store. Tuto sestavu může přijmout brána, ale Health Store po dodatečném ověření odmítnuta. Health Store například může zprávu odmítat z důvodu neplatného parametru, jako je například zastaralé pořadové číslo. Chcete-li zjistit, zda byla sestava použita v Health Store, zkontrolujte, zda se sestava zobrazuje v části HealthEvents.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Health-vlastnost [required] | Vlastnost informací o stavu. <br><br> Entita může mít sestavy o stavu pro různé vlastnosti. Vlastnost je řetězec, nikoli pevný výčet, který umožňuje, aby se v zpravodaji mohla pružně roztřídit stav stavu, který aktivuje sestavu. Například zpravodaj s SourceId "LocalWatchdog" může monitorovat stav dostupného disku v uzlu, takže může ohlásit vlastnost "AvailableDisk" v tomto uzlu. Stejné zpravodajky můžou monitorovat připojení uzlu, takže může nahlásit vlastnost "připojení" na stejném uzlu. V Health Store jsou tyto sestavy zpracovány jako samostatné události stavu pro zadaný uzel. Společně s ID zdroje (SourceId) vlastnost jednoznačně identifikuje informace o stavu. |
| --Health-State [povinné] | Možné hodnoty zahrnují \: "Invalid", "OK", "Warning", "Error", "unknown". |
| --Node-Name [povinné] | Název uzlu |
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

## <a name="sfctl-node-restart"></a>restart uzlu sfctl
Restartuje uzel clusteru Service Fabric.

Restartuje uzel neService Fabricho clusteru, který je už spuštěný.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Node-Name [povinné] | Název uzlu |
| --Create-Fabric-dump | Pokud chcete vytvořit výpis procesu uzlu Fabric, zadejte hodnotu true. Rozlišují se malá a velká písmena.  Výchozí hodnota je \: false. |
| --Node-instance-ID | ID instance cílového uzlu Je-li zadán parametr ID instance, bude uzel restartován pouze v případě, že odpovídá aktuální instanci uzlu. Výchozí hodnota "0" by odpovídala jakémukoli ID instance. ID instance se dá získat pomocí dotazu Get Node.  Výchozí hodnota \: 0. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-node-transition"></a>přechod uzlu sfctl
Spustí nebo zastaví uzel clusteru.

Spustí nebo zastaví uzel clusteru.  Uzel clusteru je proces, nikoli samotná instance operačního systému.  Chcete-li spustit uzel, předejte "Start" parametru NodeTransitionType. Chcete-li zastavit uzel, předejte "Stop" parametru NodeTransitionType. Toto rozhraní API spustí operaci – když rozhraní API vrátí uzel, možná se ještě nedokončil přechod. K získání průběhu operace zavolejte GetNodeTransitionProgress se stejným OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Node-instance-ID [povinné] | ID instance uzlu cílového uzlu. To lze určit prostřednictvím rozhraní GetNodeInfo API. |
| --Node-Name [povinné] | Název uzlu |
| --Node-přechod-typ [povinné] | Určuje typ přechodu, který má být proveden.  NodeTransitionType. Start spustí zastavený uzel. NodeTransitionType. stop zastaví uzel, který je nahoru. |
| --operace-ID [povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  Toto se předává do odpovídajícího rozhraní API getprogress. |
| --stop-doba trvání-sekund [požadováno] | Doba v sekundách, po kterou se uzel zastavil.  Minimální hodnota je 600, maximální hodnota je 14400.  Po uplynutí této doby bude uzel automaticky zálohován. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-node-transition-status"></a>přechod uzlu sfctl – stav
Získá průběh operace zahájené s použitím StartNodeTransition.

Získá průběh operace zahájené s StartNodeTransition pomocí poskytnutého OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Node-Name [povinné] | Název uzlu |
| --operace-ID [povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  Toto se předává do odpovídajícího rozhraní API getprogress. |
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
