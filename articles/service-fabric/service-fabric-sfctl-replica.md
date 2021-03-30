---
title: Replika Azure Service Fabric CLI – sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu replik.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 3ed1ecca686471e052f07e945738ad1a49e68464
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260304"
---
# <a name="sfctl-replica"></a>sfctl replica
Spravovat repliky patřící k oddílům služeb.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| nainstalována | Získá podrobnosti o replice nasazené na uzlu Service Fabric. |
| nasazeno – seznam | Získá seznam replik nasazených na uzlu Service Fabric. |
| nezávadnost | Získá stav repliky stavové služby Service Fabric nebo instance bezstavové služby. |
| příjemce | Získá informace o replice Service Fabricho oddílu. |
| list | Získá informace o replikách oddílu Service Fabric služby. |
| remove | Odebere repliku služby běžící na uzlu. |
| Sestava – stav | Odešle zprávu o stavu repliky Service Fabric. |
| restart | Restartuje repliku služby trvalé služby spuštěné na uzlu. |

## <a name="sfctl-replica-deployed"></a>sfctl replika byla nasazena
Získá podrobnosti o replice nasazené na uzlu Service Fabric.

Získá podrobnosti o replice nasazené na uzlu Service Fabric. Tyto informace zahrnují druh služby, název služby, aktuální operaci služby, aktuální datum a čas zahájení operace služby, ID oddílu, repliku/ID instance, hlášené zatížení a další informace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Node-Name [povinné] | Název uzlu |
| --Partition-ID [povinné] | Identita oddílu |
| --Replica-ID [povinné] | Identifikátor repliky |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-replica-deployed-list"></a>nasazená replika sfctl – seznam
Získá seznam replik nasazených na uzlu Service Fabric.

Získá seznam obsahující informace o replikách nasazených na uzlu Service Fabric. Tyto informace zahrnují ID oddílu, ID repliky, stav repliky, název služby, název typu služby a další informace. Parametry dotazu PartitionId nebo ServiceManifestName použijte k vrácení informací o nasazených replikách, které odpovídají zadaným hodnotám pro tyto parametry.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --ID aplikace [povinné] | Identita aplikace Obvykle se jedná o úplný název aplikace bez \: schématu identifikátoru URI prostředků infrastruktury. Počínaje verzí 6,0 jsou hierarchické názvy odděleny \~ znakem "". Například pokud je název aplikace "Fabric \: /MyApp/app1", identita aplikace by byla "MyApp \~ app1" v 6.0 + a "MyApp/app1" v předchozích verzích. |
| --Node-Name [povinné] | Název uzlu |
| --Partition-ID | Identita oddílu |
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

## <a name="sfctl-replica-health"></a>stav repliky sfctl
Získá stav repliky stavové služby Service Fabric nebo instance bezstavové služby.

Získá stav repliky Service Fabric. Pomocí EventsHealthStateFilter můžete filtrovat kolekci událostí stavu hlášených v replice na základě stavu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Partition-ID [povinné] | Identita oddílu |
| --Replica-ID [povinné] | Identifikátor repliky |
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

## <a name="sfctl-replica-info"></a>informace o replice sfctl
Získá informace o replice Service Fabricho oddílu.

Odpověď zahrnuje ID, roli, stav, stav, název uzlu, dobu provozu a další podrobnosti o replice.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Partition-ID [povinné] | Identita oddílu |
| --Replica-ID [povinné] | Identifikátor repliky |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-replica-list"></a>seznam replik sfctl
Získá informace o replikách oddílu Service Fabric služby.

Koncový bod getreplicaes vrátí informace o replikách zadaného oddílu. Odpověď zahrnuje ID, roli, stav, stav, název uzlu, dobu provozu a další podrobnosti o replice.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Partition-ID [povinné] | Identita oddílu |
| --pokračování-token | Parametr tokenu pokračování slouží k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API v případě, že se výsledky ze systému nevejdou do jediné odpovědi. Když se tato hodnota předává do dalšího volání rozhraní API, vrátí rozhraní API další sadu výsledků. Pokud nejsou k dispozici žádné další výsledky, token pokračování neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódovaná v adrese URL. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-replica-remove"></a>odstranění repliky sfctl
Odebere repliku služby běžící na uzlu.

Toto rozhraní API simuluje selhání repliky Service Fabric odebráním repliky z clusteru Service Fabric. Odstraněním se zavře replika, převede repliku na roli None a pak z clusteru odebere všechny informace o stavu repliky. Toto rozhraní API testuje cestu pro odebrání stavu repliky a simuluje nestálou cestu chyby prostřednictvím klientských rozhraní API. Upozornění – při použití tohoto rozhraní API se neprovádí žádné kontroly zabezpečení. Nesprávné použití tohoto rozhraní API může způsobit ztrátu dat pro stavové služby. Kromě toho příznak forceRemove ovlivňuje všechny ostatní repliky hostované v rámci stejného procesu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Node-Name [povinné] | Název uzlu |
| --Partition-ID [povinné] | Identita oddílu |
| --Replica-ID [povinné] | Identifikátor repliky |
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

## <a name="sfctl-replica-report-health"></a>Sestava sfctl repliky – stav
Odešle zprávu o stavu repliky Service Fabric.

Hlásí stav zadané repliky Service Fabric. Sestava musí obsahovat informace o zdroji sestavy stavu a vlastnosti, na které je hlášena. Zpráva se odešle do repliky Service Fabric brány, která se přepošle na Health Store. Tuto sestavu může přijmout brána, ale Health Store po dodatečném ověření odmítnuta. Health Store například může zprávu odmítat z důvodu neplatného parametru, jako je například zastaralé pořadové číslo. Pokud chcete zjistit, jestli se sestava použila v Health Store, spusťte příkaz získat stav repliky a zkontrolujte, jestli se sestava zobrazuje v části HealthEvents.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Health-vlastnost [required] | Vlastnost informací o stavu. <br><br> Entita může mít sestavy o stavu pro různé vlastnosti. Vlastnost je řetězec, nikoli pevný výčet, který umožňuje, aby se v zpravodaji mohla pružně roztřídit stav stavu, který aktivuje sestavu. Například zpravodaj s SourceId "LocalWatchdog" může monitorovat stav dostupného disku v uzlu, takže může ohlásit vlastnost "AvailableDisk" v tomto uzlu. Stejné zpravodajky můžou monitorovat připojení uzlu, takže může nahlásit vlastnost "připojení" na stejném uzlu. V Health Store jsou tyto sestavy zpracovány jako samostatné události stavu pro zadaný uzel. Společně s ID zdroje (SourceId) vlastnost jednoznačně identifikuje informace o stavu. |
| --Health-State [povinné] | Možné hodnoty zahrnují \: "Invalid", "OK", "Warning", "Error", "unknown". |
| --Partition-ID [povinné] | Identita oddílu |
| --Replica-ID [povinné] | Identita oddílu |
| --Source-ID [povinné] | Název zdroje, který identifikuje součást klienta/sledovacího zařízení/systému, která vygenerovala informace o stavu. |
| --Description | Popis informací o stavu. <br><br> Představuje bezplatný text, který se používá k přidání lidských čitelných informací o sestavě. Maximální délka řetězce pro popis je 4096 znaků. Pokud je zadaný řetězec delší, automaticky se zkrátí. Při zkrácení obsahují poslední znaky popisu značku "[zkrácené]" a celková velikost řetězce je 4096 znaků. Přítomnost značky indikuje uživatelům, kterým došlo ke zkrácení. Všimněte si, že při zkrácení má popis méně než 4096 znaků z původního řetězce. |
| --Immediate | Příznak, který označuje, zda má být sestava odeslána okamžitě. <br><br> Do aplikace Service Fabric Gateway se pošle zpráva o stavu, která se přepošle na Health Store. Pokud je vlastnost Immediate nastavená na hodnotu true, pošle se z brány HTTP na Health Store zprávu hned, a to bez ohledu na nastavení klienta prostředků infrastruktury, které používá aplikace brány HTTP. To je užitečné pro kritické sestavy, které by se měly odesílat co nejdříve. V závislosti na časování a dalších podmínkách může odeslání sestavy dál selhat, například pokud je brána HTTP uzavřená nebo zpráva nemá přístup k bráně. Pokud je vlastnost Immediate nastavená na false, pošle se sestava na základě nastavení klienta stavu z brány HTTP. Proto se bude provádět dávkování podle konfigurace HealthReportSendInterval. Toto je doporučené nastavení, protože umožňuje klientovi stavu optimalizovat zprávy o stavu, které se mají Health Store, i zpracování sestavy o stavu. Ve výchozím nastavení se zprávy neodesílají okamžitě. |
| --Remove-when-vypršela platnost | Hodnota, která označuje, zda je sestava odebrána z Health Store v případě jejího platnosti. <br><br> Pokud je nastavená hodnota true, sestava se po vypršení platnosti odebere z Health Store. Pokud je nastavena hodnota false, bude sestava považována za chybu, pokud vypršela její platnost. Hodnota této vlastnosti je ve výchozím nastavení false. Při pravidelné sestavě klientů by měly být nastavené RemoveWhenExpired na hodnotu false (výchozí). Tímto způsobem má zpravodaj problémy (například zablokování) a nemůže hlásit, entita je vyhodnocena při vypršení platnosti sestavy stavu. Tím se označí entita jako v chybovém stavu. |
| --pořadové číslo | Pořadové číslo pro tuto sestavu stavu jako číselný řetězec. <br><br> Číslo sekvence sestavy používá Health Store ke zjišťování zastaralých sestav. Není-li tento parametr zadán, je číslo sekvence automaticky generováno klientem stavu při přidání sestavy. |
| --druh služby | Druh repliky služby (stavová nebo stavová), pro který je stav hlášen. Níže jsou uvedené možné hodnoty \: ' stavové ', ' stavová '.  Výchozí \: stav |
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

## <a name="sfctl-replica-restart"></a>restart repliky sfctl
Restartuje repliku služby trvalé služby spuštěné na uzlu.

Restartuje repliku služby trvalé služby spuštěné na uzlu. Upozornění – při použití tohoto rozhraní API se neprovádí žádné kontroly zabezpečení. Nesprávné použití tohoto rozhraní API může vést ke ztrátě dostupnosti pro stavové služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Node-Name [povinné] | Název uzlu |
| --Partition-ID [povinné] | Identita oddílu |
| --Replica-ID [povinné] | Identifikátor repliky |
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
