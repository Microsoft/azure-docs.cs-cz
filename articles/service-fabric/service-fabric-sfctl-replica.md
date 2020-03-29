---
title: Replika struktury cli infrastruktury azure service fabric
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu replik.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f6ad0b4c08ac8d710340fe654a068d0a3804e58f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905802"
---
# <a name="sfctl-replica"></a>sfctl replica
Spravujte repliky, které patří do oddílů služby.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| Nasazeny | Získá podrobnosti o repliky nasazené v uzlu Service Fabric. |
| nasazený seznam | Získá seznam replik nasazených v uzlu Service Fabric. |
| Zdraví | Získá stav repliky stavové služby Service Fabric nebo bezstavové instance služby. |
| Info | Získá informace o repliku oddílu Service Fabric. |
| list | Získá informace o repliky oddílu služby Service Fabric služby. |
| remove | Odebere repliku služby spuštěnou v uzlu. |
| zpráva-zdraví | Odešle zprávu o stavu repliky service fabric. |
| restart | Restartuje repliku služby trvalé služby spuštěné v uzlu. |

## <a name="sfctl-replica-deployed"></a>nasazená replika sfctl
Získá podrobnosti o repliky nasazené v uzlu Service Fabric.

Získá podrobnosti o repliky nasazené v uzlu Service Fabric. Informace zahrnují druh služby, název služby, aktuální provoz služby, aktuální čas data zahájení operace služby, ID oddílu, ID repliky/instance, hlášené zatížení a další informace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název uzlu [Povinné] | Název uzlu |
| --partition-id [Povinné] | Identita oddílu. |
| --replika-id [Povinné] | Identifikátor repliky. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-replica-deployed-list"></a>nasazený seznam repliky sfctl
Získá seznam replik nasazených v uzlu Service Fabric.

Získá seznam obsahující informace o repliky nasazené v uzlu Service Fabric. Mezi informace patří ID oddílu, ID repliky, stav repliky, název služby, název typu služby a další informace. Pomocí parametrů dotazu PartitionId nebo ServiceManifestName vraťte informace o nasazených replikách odpovídajících zadaným hodnotám pro tyto parametry.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --id aplikace [Povinné] | Identita aplikace. Obvykle se jedná o úplný název aplikace\:bez schématu IDENTIFIKÁTORURI struktury . Počínaje verzí 6.0 jsou hierarchické názvy\~odděleny znakem " " . Například pokud název aplikace je\:"fabric /myapp/app1", identita aplikace\~by "myapp app1" v 6.0+ a "myapp/app1" v předchozích verzích. |
| --název uzlu [Povinné] | Název uzlu |
| --partition-id | Identita oddílu. |
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

## <a name="sfctl-replica-health"></a>sfctl stav repliky
Získá stav repliky stavové služby Service Fabric nebo bezstavové instance služby.

Získá stav repliky service fabric. Pomocí filtru EventsHealthStateFilter můžete filtrovat kolekci událostí stavu vykazovanou v replice na základě stavu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --partition-id [Povinné] | Identita oddílu. |
| --replika-id [Povinné] | Identifikátor repliky. |
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

## <a name="sfctl-replica-info"></a>sfctl replika info
Získá informace o repliku oddílu Service Fabric.

Odpověď zahrnuje ID, roli, stav, stav, název uzlu, dobu provozu a další podrobnosti o replice.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --partition-id [Povinné] | Identita oddílu. |
| --replika-id [Povinné] | Identifikátor repliky. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-replica-list"></a>seznam replik sfctl
Získá informace o repliky oddílu služby Service Fabric služby.

Koncový bod GetReplicas vrátí informace o replikách zadaného oddílu. Odpověď zahrnuje ID, roli, stav, stav, název uzlu, dobu provozu a další podrobnosti o replice.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --partition-id [Povinné] | Identita oddílu. |
| --continuation-token | Parametr tokenu pokračování se používá k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API, pokud se výsledky ze systému nevejdou do jediné odpovědi. Když je tato hodnota předána dalšímu volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak token pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódována adresou URL. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-replica-remove"></a>sfctl replika odebrat
Odebere repliku služby spuštěnou v uzlu.

Toto rozhraní API simuluje selhání repliky service fabric odebráním repliky z clusteru Service Fabric. Odebrání zavře repliku, přejde repliku na roli Žádný a potom odebere všechny informace o stavu repliky z clusteru. Toto rozhraní API testuje cestu odebrání stavu repliky a simuluje trvalou cestu selhání sestavy prostřednictvím rozhraní API klienta. Upozornění – při použití tohoto rozhraní API nejsou prováděny žádné bezpečnostní kontroly. Nesprávné použití tohoto rozhraní API může vést ke ztrátě dat pro stavové služby. Kromě toho forceRemove příznak má vliv na všechny ostatní repliky hostované ve stejném procesu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název uzlu [Povinné] | Název uzlu |
| --partition-id [Povinné] | Identita oddílu. |
| --replika-id [Povinné] | Identifikátor repliky. |
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

## <a name="sfctl-replica-report-health"></a>sfctl replika sestavy-stav
Odešle zprávu o stavu repliky service fabric.

Hlásí stav zadané repliky service fabric. Sestava musí obsahovat informace o zdroji sestavy stavu a vlastnosti, na které je hlášena. Sestava je odeslána do repliky brány Service Fabric, která předá do úložiště stavu. Sestava může být přijata bránou, ale odmítnuta úložištěm stavu po dalším ověření. Úložiště stavu může například odmítnout sestavu z důvodu neplatného parametru, jako je zastaralé pořadové číslo. Chcete-li zjistit, zda byla sestava použita v úložišti stavu, spusťte stav repliky a zkontrolujte, zda se sestava zobrazí v části HealthEvents.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --health-property [Povinné] | Vlastnost informací o stavu. <br><br> Entita může mít sestavy stavu pro různé vlastnosti. Vlastnost je řetězec a není pevný výčet, aby reportér flexibilitu kategorizovat podmínku stavu, který aktivuje sestavu. Například reportér s SourceId "LocalWatchdog" můžete sledovat stav dostupného disku na uzlu, tak to může hlásit "AvailableDisk" vlastnost na tomto uzlu. Stejný reportér může sledovat připojení uzlu, takže může hlásit vlastnost "Připojení" na stejném uzlu. V úložišti stavu jsou tyto sestavy považovány za samostatné události stavu pro zadaný uzel. Spolu s SourceId vlastnost jednoznačně identifikuje informace o stavu. |
| --stav -state [Povinné] | Možné hodnoty\: zahrnují "Neplatné", Ok, "Upozornění", "Chyba", Neznámý. |
| --partition-id [Povinné] | Identita oddílu. |
| --replika-id [Povinné] | Identita oddílu. |
| --source-id [Povinné] | Název zdroje, který identifikuje klient/watchdog/systémová součást, která generovala informace o stavu. |
| --popis | Popis informací o zdravotním stavu. <br><br> Představuje volný text používaný k přidání lidsky čitelných informací o sestavě. Maximální délka řetězce pro popis je 4096 znaků. Pokud je zadaný řetězec delší, bude automaticky zkrácen. Při zkrácení poslední znaky popisu obsahují značku "[Zkráceno]" a celková velikost řetězce je 4096 znaků. Přítomnost značky označuje uživatelům, že došlo ke zkrácení. Všimněte si, že při zkrácení popis má méně než 4096 znaků z původního řetězce. |
| --okamžitá | Příznak, který označuje, zda má být sestava odeslána okamžitě. <br><br> Sestava stavu je odeslána do aplikace brány Service Fabric, která předá do úložiště stavu. Pokud immediate je nastavena na hodnotu true, sestava je odeslána okamžitě z brány HTTP do úložiště stavu, bez ohledu na nastavení klienta prostředků infrastruktury, které používá aplikace brány HTTP. To je užitečné pro kritické sestavy, které by měly být odeslány co nejdříve. V závislosti na časování a dalších podmínkách může odesílání sestavy stále nezdaří, například pokud je brána HTTP uzavřena nebo zpráva nedosáhne brány. Pokud immediate je nastavena na false, sestava je odeslána na základě nastavení klienta stavu z brány HTTP. Proto bude dávkově podle konfigurace HealthReportSendInterval. Toto je doporučené nastavení, protože umožňuje klientovi stavu optimalizovat zprávy o stavu do úložiště stavu, jakož i zpracování sestavy stavu. Ve výchozím nastavení nejsou sestavy odesílány okamžitě. |
| --remove-when-expired --remove-when-expired --remove-when-expired --remove | Hodnota, která označuje, zda je sestava odebrána z úložiště stavu po vypršení jeho platnosti. <br><br> Pokud je nastavena na hodnotu true, sestava je odebrána z úložiště stavu po vypršení jeho platnosti. Pokud je nastavena na hodnotu false, sestava je považována za chybu při vypršení platnosti. Hodnota této vlastnosti je ve výchozím nastavení false. Když klienti pravidelně hlásí, měli by nastavit RemoveWhenExpired false (výchozí). Tímto způsobem je reportér má problémy (např. zablokování) a nelze hlásit, entita je vyhodnocena při chybě při vypršení platnosti sestavy stavu. To označí entitu jako ve stavu Chyba. |
| --pořadové číslo | Pořadové číslo pro tuto sestavu stavu jako číselný řetězec. <br><br> Pořadové číslo sestavy používá úložiště stavu ke zjištění zastaralých sestav. Pokud není zadán, pořadové číslo je automaticky generovánklientem stavu při přidání sestavy. |
| --druh služby | Druh repliky služby (bezstavové nebo stavové), pro které je hlášenstav. Níže jsou uvedeny možné hodnoty\: 'bez státní příslušnosti', 'Stateful'.  Výchozí\: stav. |
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

## <a name="sfctl-replica-restart"></a>sfctl replika restart
Restartuje repliku služby trvalé služby spuštěné v uzlu.

Restartuje repliku služby trvalé služby spuštěné v uzlu. Upozornění – při použití tohoto rozhraní API nejsou prováděny žádné bezpečnostní kontroly. Nesprávné použití tohoto rozhraní API může vést ke ztrátě dostupnosti pro stavové služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --název uzlu [Povinné] | Název uzlu |
| --partition-id [Povinné] | Identita oddílu. |
| --replika-id [Povinné] | Identifikátor repliky. |
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
