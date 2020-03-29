---
title: Uzel CLI- sfctl azure service fabric
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu uzlů clusteru.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 5881e6485003abd4fd23a7f6d06a428e768c00fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905878"
---
# <a name="sfctl-node"></a>sfctl node
Spravujte uzly, které tvoří cluster.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| přepsání parametrů konfigurace přidání | Přidá seznam přepsání konfigurace v zadaném uzlu. |
| Zakázat | Deaktivujte uzel clusteru Service Fabric se zadaným záměrem deaktivace. |
| Povolit | Aktivujte uzel clusteru Service Fabric, který je aktuálně deaktivován. |
| přepsání konfigurace | Získá seznam přepsání konfigurace na určeném uzlu. |
| Zdraví | Získá stav uzlu Service Fabric. |
| Info | Získá informace o konkrétním uzlu v clusteru Service Fabric. |
| list | Získá seznam uzlů v clusteru Service Fabric. |
| načítání | Získá informace o zatížení uzlu Service Fabric. |
| odstranit lokální změny konfigurace | Odebere přepsání konfigurace v zadaném uzlu. |
| odstranit stav | Upozorní service fabric, že trvalý stav na uzlu byl trvale odebrán nebo ztracen. |
| zpráva-zdraví | Odešle zprávu o stavu v uzlu Service Fabric. |
| restart | Restartuje uzel clusteru Service Fabric. |
| Přechod | Spustí nebo zastaví uzel clusteru. |
| stav přechodu | Získá průběh operace zahájené pomocí StartNodeTransition. |

## <a name="sfctl-node-add-configuration-parameter-overrides"></a>přepsání přídavných parametrů uzlu sfctl
Přidá seznam přepsání konfigurace v zadaném uzlu.

Toto rozhraní API umožňuje přidání všech existujících přepsání konfigurace na zadaném uzlu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --config-parameter-override-list [Povinné] | Popis pro přidání seznamu přepsání konfigurace. |
| --název uzlu [Povinné] | Název uzlu |
| --síla | Vynutit přepsání konfigurace na určených uzlech. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-node-disable"></a>sfctl uzel zakázat
Deaktivujte uzel clusteru Service Fabric se zadaným záměrem deaktivace.

Deaktivujte uzel clusteru Service Fabric se zadaným záměrem deaktivace. Jakmile deaktivace probíhá, záměr deaktivace může být zvýšena, ale není snížena (například uzel, který je deaktivován s záměrem Pause lze dále deaktivovat restartovat, ale ne naopak. Uzly mohou být znovu aktivovány pomocí operace Aktivovat uzel kdykoli po jejich deaktivaci. Pokud deaktivace není dokončena, zruší se deaktivace. Uzel, který přejde dolů a vrátí se nahoru, zatímco deaktivovaná bude stále nutné znovu aktivovat před služby budou umístěny na tomto uzlu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název uzlu [Povinné] | Název uzlu |
| --deaktivace-záměr | Popisuje záměr nebo důvod deaktivace uzlu. Možné hodnoty jsou následující. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-node-enable"></a>sfctl uzel povolit
Aktivujte uzel clusteru Service Fabric, který je aktuálně deaktivován.

Aktivuje uzel clusteru Service Fabric, který je aktuálně deaktivován. Po aktivaci se uzel opět stane životaschopným cílem pro umístění nových replik a všechny deaktivované repliky zbývající na uzlu budou znovu aktivovány.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-node-get-configuration-overrides"></a>sfctl uzel získat-configuration-přepsání
Získá seznam přepsání konfigurace na určeném uzlu.

Toto rozhraní API umožňuje získání všech existujících přepsání konfigurace na zadaném uzlu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-node-health"></a>sfctl uzel zdraví
Získá stav uzlu Service Fabric.

Získá stav uzlu Service Fabric. Pomocí filtru EventsHealthStateFilter můžete filtrovat kolekci událostí stavu vykazovanou v uzlu na základě stavu. Pokud uzel, který zadáte podle názvu neexistuje v úložišti stavu, vrátí chyba.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název uzlu [Povinné] | Název uzlu |
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

## <a name="sfctl-node-info"></a>informace o uzlu sfctl
Získá informace o konkrétním uzlu v clusteru Service Fabric.

Odpověď obsahuje název, stav, ID, stav, dobu provozu a další podrobnosti o uzlu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-node-list"></a>seznam uzlů sfctl
Získá seznam uzlů v clusteru Service Fabric.

Odpověď obsahuje název, stav, ID, stav, dobu obslužnosti a další podrobnosti o uzlech.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --continuation-token | Parametr tokenu pokračování se používá k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API, pokud se výsledky ze systému nevejdou do jediné odpovědi. Když je tato hodnota předána dalšímu volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak token pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódována adresou URL. |
| --max-výsledky | Maximální počet výsledků, které mají být vráceny jako součást stránkovaných dotazů. Tento parametr definuje horní mez počtu vrácených výsledků. Vrácené výsledky mohou být menší než zadané maximální výsledky, pokud se nevejdou do zprávy podle omezení maximální velikosti zprávy definované v konfiguraci. Pokud je tento parametr nula nebo není zadán, stránkovaný dotaz obsahuje co nejvíce výsledků, které se vejdou do vrácené zprávy. |
| --node-status-filter | Umožňuje filtrování uzlů na základě NodeStatus. Budou vráceny pouze uzly, které odpovídají zadané hodnotě filtru. Hodnota filtru může být jedna z následujících.  Výchozí\: výchozí nastavení. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-node-load"></a>zatížení uzlu sfctl
Získá informace o zatížení uzlu Service Fabric.

Načte informace o zatížení uzlu Service Fabric pro všechny metriky, které mají zatížení nebo kapacitu definované.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-node-remove-configuration-overrides"></a>sfctl uzel remove-configuration-overrides
Odebere přepsání konfigurace v zadaném uzlu.

Toto rozhraní API umožňuje odebrání všech existujících přepsání konfigurace na určeném uzlu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-node-remove-state"></a>sfctl uzel remove-state
Upozorní service fabric, že trvalý stav na uzlu byl trvale odebrán nebo ztracen.

To znamená, že není možné obnovit trvalý stav tohoto uzlu. K tomu obvykle dochází, pokud byl pevný disk vymazán nebo pokud dojde k chybě pevného disku. Aby byla tato operace úspěšná, musí být uzel vypnutý. Tato operace umožňuje Service Fabric vědět, že repliky v tomto uzlu již neexistují a že Service Fabric by měl přestat čekat na tyto repliky vrátit nahoru. Nespouštějte tuto rutinu, pokud stav na uzlu nebyl odebrán a uzel může vrátit zpět s jeho stav neporušený. Počínaje Service Fabric 6.5, chcete-li použít toto rozhraní API pro počáteční uzly, změňte počáteční uzly na běžné (nesemenné) uzly a potom vyvolat toto rozhraní API k odebrání stavu uzlu. Pokud cluster běží v Azure, po přejde uzel osiva, Service Fabric se pokusí změnit na uzel bez osiva automaticky. Chcete-li k tomu dojít, ujistěte se, že počet uzlů bez osiva v typu primárníuzel není menší než počet uzly down seed. V případě potřeby přidejte další uzly do typu primárního uzlu, abyste toho dosáhli. Pro samostatný cluster, pokud down seed uzel se neočekává, že se vrátí nahoru s jeho stavu\:beze změny, odeberte uzel z clusteru, viz https //docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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

## <a name="sfctl-node-report-health"></a>sfctl uzel zpráva-zdraví
Odešle zprávu o stavu v uzlu Service Fabric.

Hlásí stav zadaného uzlu Service Fabric. Sestava musí obsahovat informace o zdroji sestavy stavu a vlastnosti, na které je hlášena. Sestava je odeslána do uzlu brány Service Fabric, který předá do úložiště stavu. Sestava může být přijata bránou, ale odmítnuta úložištěm stavu po dalším ověření. Úložiště stavu může například odmítnout sestavu z důvodu neplatného parametru, jako je zastaralé pořadové číslo. Chcete-li zjistit, zda byla sestava použita v úložišti stavu, zkontrolujte, zda se sestava zobrazí v části HealthEvents.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --health-property [Povinné] | Vlastnost informací o stavu. <br><br> Entita může mít sestavy stavu pro různé vlastnosti. Vlastnost je řetězec a není pevný výčet, aby reportér flexibilitu kategorizovat podmínku stavu, který aktivuje sestavu. Například reportér s SourceId "LocalWatchdog" můžete sledovat stav dostupného disku na uzlu, tak to může hlásit "AvailableDisk" vlastnost na tomto uzlu. Stejný reportér může sledovat připojení uzlu, takže může hlásit vlastnost "Připojení" na stejném uzlu. V úložišti stavu jsou tyto sestavy považovány za samostatné události stavu pro zadaný uzel. Spolu s SourceId vlastnost jednoznačně identifikuje informace o stavu. |
| --stav -state [Povinné] | Možné hodnoty\: zahrnují "Neplatné", Ok, "Upozornění", "Chyba", Neznámý. |
| --název uzlu [Povinné] | Název uzlu |
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

## <a name="sfctl-node-restart"></a>restart uzlu sfctl
Restartuje uzel clusteru Service Fabric.

Restartuje uzel clusteru Service Fabric, který je již spuštěn.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název uzlu [Povinné] | Název uzlu |
| --create-fabric-dump | Zadejte True, chcete-li vytvořit výpis procesu uzlu prostředků infrastruktury. Toto je malá a velká písmena.  Výchozí\: false. |
| --node-instance-id | ID instance cílového uzlu. Pokud je zadáno ID instance, uzel bude restartován pouze v případě, že odpovídá aktuální instanci uzlu. Výchozí hodnota "0" by odpovídala libovolnému ID instance. ID instance lze získat pomocí dotazu get uzlu.  Výchozí\: hodnota 0. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-node-transition"></a>přechod uzlu sfctl
Spustí nebo zastaví uzel clusteru.

Spustí nebo zastaví uzel clusteru.  Uzel clusteru je proces, nikoli samotná instance operačního systému.  Chcete-li spustit uzel, předavažte v "Start" pro parametr NodeTransitionType. Chcete-li uzel zastavit, předejte příkaz Stop pro parametr NodeTransitionType. Toto rozhraní API spustí operaci – když rozhraní API vrátí uzel pravděpodobně ještě nedokončilpřechod. Volání GetNodeTransitionProgress se stejným OperationId získat průběh operace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --node-instance-id [Povinné] | ID instance uzlu cílového uzlu. To lze určit prostřednictvím rozhraní GETNodeInfo API. |
| --název uzlu [Povinné] | Název uzlu |
| --typ přechodu uzlu [Povinné] | Označuje typ přechodu, který má být provést.  NodeTransitionType.Start spustí zastavený uzel. NodeTransitionType.Stop zastaví uzel, který je nahoru. |
| --operation-id [Povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  To je předán do odpovídající rozhraní API GetProgress. |
| --stop-duration-in-seconds [Povinné] | Doba trvání v sekundách, chcete-li zachovat uzel zastaven.  Minimální hodnota je 600, maximální je 14400.  Po uplynutí této doby se uzel automaticky vrátí nahoru. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-node-transition-status"></a>sfctl uzel přechod-status
Získá průběh operace zahájené pomocí StartNodeTransition.

Získá průběh operace spuštěna s StartNodeTransition pomocí zapředpokladu OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název uzlu [Povinné] | Název uzlu |
| --operation-id [Povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  To je předán do odpovídající rozhraní API GetProgress. |
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
- [Setup](service-fabric-cli.md) the Service Fabric CLI.
- Naučte se používat funkce CLI service fabric pomocí [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).