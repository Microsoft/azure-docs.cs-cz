---
title: Cluster CLI- sfctl struktury azure service fabric
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu clusterů.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 007ad6f59f0ce304db579f4faa1bb95611a93a37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906148"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Vyberte, spravujte a provozujte clustery Service Fabric.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| verze kódu | Získá seznam verzí kódu prostředků infrastruktury, které jsou zřízeny v clusteru Service Fabric. |
| config-verze | Získá seznam konfiguračníverze prostředků prostředků, které jsou zřízeny v clusteru Service Fabric. |
| Zdraví | Získá stav clusteru Service Fabric. |
| manifest | Získejte manifest clusteru Service Fabric. |
| operace-zrušit | Zruší operaci poruchy vyvolané uživatelem. |
| seznam operací | Získá seznam operací selhání vyvolané uživatelem filtrované podle zadaný vstup. |
| Poskytování | Zřízení kódu nebo konfigurační balíčky clusteru Service Fabric. |
| obnovit systém | Označuje clusteru Service Fabric, že by se měl pokusit obnovit systémové služby, které jsou aktuálně uvízlé ve ztrátě kvora. |
| zpráva-zdraví | Odešle zprávu o stavu clusteru Service Fabric. |
| Vyberte | Připojí se ke koncovému bodu clusteru Service Fabric. |
| show-připojení | Ukažte, ke kterému clusteru Service Fabric je tato instance sfctl připojena. |
| zrušení poskytnutí | Zrušení zřízení kódu nebo konfiguračních balíčků clusteru Service Fabric. |
| Upgrade | Začněte inovovat kód nebo konfigurační verzi clusteru Service Fabric. |
| obnovení upgradu | Připojte upgrade clusteru na další doménu upgradu. |
| vrácení upgradu | Vraťte zpět upgrade clusteru Service Fabric. |
| stav upgradu | Získá průběh aktuální ho upgradu clusteru. |
| aktualizace upgradu | Aktualizujte parametry upgradu upgradu upgradu clusteru Service Fabric. |

## <a name="sfctl-cluster-code-versions"></a>verze kódu clusteru sfctl
Získá seznam verzí kódu prostředků infrastruktury, které jsou zřízeny v clusteru Service Fabric.

Získá seznam informací o verze kódu prostředků infrastruktury, které jsou zřízeny v clusteru. Parametr CodeVersion lze volitelně filtrovat výstup pouze na tuto konkrétní verzi.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --verze kódu | Verze produktu Service Fabric. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-config-versions"></a>Konfigurační verze clusteru sfctl
Získá seznam konfiguračníverze prostředků prostředků, které jsou zřízeny v clusteru Service Fabric.

Získá seznam informací o konfigurační verze prostředků prostředků, které jsou zřízeny v clusteru. Parametr ConfigVersion lze volitelně filtrovat výstup pouze na konkrétní verzi.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --config-version --config-version --config-version --con | Konfigurační verze service fabric. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-health"></a>sfctl stav clusteru
Získá stav clusteru Service Fabric.

Pomocí filtru EventsHealthStateFilter můžete filtrovat kolekci událostí stavu vykazovaných v clusteru na základě stavu. Podobně použijte NodesHealthStateFilter a ApplicationsHealthStateFilter filtrovat kolekci uzlů a aplikací vrácených na základě jejich agregovaného stavu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --aplikace-stav-filtr | Umožňuje filtrování objektů stavu aplikace vrácených ve výsledku dotazu na stav clusteru na základě jejich stavu. Možné hodnoty pro tento parametr zahrnují celá hodnota získaná z členů nebo bitové operace na členy výčtu HealthStateFilter. Jsou vráceny pouze aplikace, které odpovídají filtru. Všechny aplikace se používají k vyhodnocení agregovaného stavu. Pokud není zadán, jsou vráceny všechny položky. Hodnoty stavu jsou výčty založené na příznaku, takže hodnota může být kombinací těchto hodnot získaných pomocí bitového operátoru "OR". Například pokud je zadaný hodnota 6 pak stav aplikace s HealthState hodnotu OK (2) a upozornění (4) jsou vráceny.  <br> - Výchozí - Výchozí hodnota. Odpovídá jakékoli HealthState. Hodnota je nula.  <br> - Žádný - filtr, který neodpovídá žádné hodnotě HealthState. Používá se k vrácení žádné výsledky na dané kolekci stavů. Hodnota je 1.  <br> - Ok - Filtr, který odpovídá vstupu s hodnotou HealthState Ok. Hodnota je 2.  <br> - Upozornění - filtr, který odpovídá vstupu s HealthState hodnota Upozornění. Hodnota je 4.  <br> - Chyba - filtr, který odpovídá vstupu s Hodnotou HealthState Error. Hodnota je 8.  <br> - All - Filtr, který odpovídá vstupu s libovolnou hodnotou HealthState. Hodnota je 65535. |
| --events-health-state-filter | Umožňuje filtrování kolekce HealthEvent objekty vrácené na základě stavu. Možné hodnoty pro tento parametr zahrnují tetinovou hodnotu jednoho z následujících stavů. Jsou vráceny pouze události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaného stavu. Pokud není zadán, jsou vráceny všechny položky. Hodnoty stavu jsou výčty založené na příznaku, takže hodnota může být kombinací těchto hodnot získaných pomocí bitového operátoru "OR". Například Pokud je zadaný hodnota 6 pak jsou vráceny všechny události s HealthState hodnotu OK (2) a Upozornění (4).  <br> - Výchozí - Výchozí hodnota. Odpovídá jakékoli HealthState. Hodnota je nula.  <br> - Žádný - filtr, který neodpovídá žádné hodnotě HealthState. Používá se k vrácení žádné výsledky na dané kolekci stavů. Hodnota je 1.  <br> - Ok - Filtr, který odpovídá vstupu s hodnotou HealthState Ok. Hodnota je 2.  <br> - Upozornění - filtr, který odpovídá vstupu s HealthState hodnota Upozornění. Hodnota je 4.  <br> - Chyba - filtr, který odpovídá vstupu s Hodnotou HealthState Error. Hodnota je 8.  <br> - All - Filtr, který odpovídá vstupu s libovolnou hodnotou HealthState. Hodnota je 65535. |
| --vyloučit-zdravotní statistiky | Označuje, zda by měly být vráceny statistiky stavu jako součást výsledku dotazu. False ve výchozím nastavení. Statistiky ukazují počet podřízených entit ve stavu Ok, Upozornění a Chyba. |
| --include-system-application-health-statistics | Označuje, zda by měly\:statistiky stavu obsahovat statistiky stavu prostředků fabric /System application. False ve výchozím nastavení. Pokud includeSystemApplicationHealthStatistics je nastavena na hodnotu true, statistiky\:stavu zahrnují entity, které patří do prostředků fabric /System aplikace. V opačném případě výsledek dotazu zahrnuje statistiky stavu pouze pro uživatelské aplikace. Statistiky stavu musí být zahrnuty ve výsledku dotazu pro tento parametr, který má být použit. |
| --uzly-stav-stav-filtr | Umožňuje filtrování objektů stavu uzlu vrácených ve výsledku dotazu na stav clusteru na základě jejich stavu. Možné hodnoty pro tento parametr zahrnují tetinovou hodnotu jednoho z následujících stavů. Jsou vráceny pouze uzly, které odpovídají filtru. Všechny uzly se používají k vyhodnocení agregovaného stavu. Pokud není zadán, jsou vráceny všechny položky. Hodnoty stavu jsou výčty založené na příznaku, takže hodnota může být kombinací těchto hodnot získaných pomocí bitového operátoru "OR". Například pokud zadaný hodnota je 6 pak stav uzlů s HealthState hodnotu OK (2) a upozornění (4) jsou vráceny.  <br> - Výchozí - Výchozí hodnota. Odpovídá jakékoli HealthState. Hodnota je nula.  <br> - Žádný - filtr, který neodpovídá žádné hodnotě HealthState. Používá se k vrácení žádné výsledky na dané kolekci stavů. Hodnota je 1.  <br> - Ok - Filtr, který odpovídá vstupu s hodnotou HealthState Ok. Hodnota je 2.  <br> - Upozornění - filtr, který odpovídá vstupu s HealthState hodnota Upozornění. Hodnota je 4.  <br> - Chyba - filtr, který odpovídá vstupu s Hodnotou HealthState Error. Hodnota je 8.  <br> - All - Filtr, který odpovídá vstupu s libovolnou hodnotou HealthState. Hodnota je 65535. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-manifest"></a>manifest clusteru sfctl
Získejte manifest clusteru Service Fabric.

Získejte manifest clusteru Service Fabric. Manifest clusteru obsahuje vlastnosti clusteru, které zahrnují různé typy uzlů v clusteru, konfigurace zabezpečení, selhání a topologie domény upgradu atd. Tyto vlastnosti jsou určeny jako součást souboru ClusterConfig.JSON při nasazování samostatného clusteru. Většina informací v manifestu clusteru je však generována interně prostředků infrastruktury služeb během nasazení clusteru v jiných scénářích nasazení (například při použití portálu Azure). Obsah manifestu clusteru je pouze pro informační účely a od uživatelů se neočekává závislost na formátu obsahu souboru nebo jeho interpretaci.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-operation-cancel"></a>sfctl operace clusteru-zrušit
Zruší operaci poruchy vyvolané uživatelem.

Následující chybová pravidla spustí operace s chybami,\: které mohou být zrušeny pomocí CancelOperation StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Pokud je síla false, zadaná operace indukovaná uživatelem bude řádně zastavena a vyčištěna.  Pokud je síla true, příkaz bude přerušena a některé vnitřní stav může zůstat pozadu.  Zadání síly jako true by mělo být používáno opatrně. Volání tohoto rozhraní API s vynucenou nastavenou na hodnotu true není povoleno, dokud toto rozhraní API již nebylo voláno na stejném testovacím příkazu s vynucenou hodnotou nastavenou na hodnotu false first, nebo pokud testovací příkaz již nemá OperationState of OperationState.RollingBack. Vysvětlení\: OperationState.RollingBack znamená, že systém bude nebo je vyčištění stavu vnitřního systému způsobené spuštěním příkazu.  Nebude obnovit data, pokud testovací příkaz byl způsobit ztrátu dat.  Například pokud zavoláte StartDataLoss pak volání tohoto rozhraní API, systém bude pouze vyčistit vnitřní stav ze spuštění příkazu. Neobnoví data cílového oddílu, pokud příkaz postupoval dostatečně daleko, aby způsobil ztrátu dat. Důležitá\: poznámka, pokud je toto rozhraní API vyvoláno force==true, vnitřní stav může zůstat pozadu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --operation-id [Povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  To je předán do odpovídající rozhraní API GetProgress. |
| --síla | Označuje, zda má být řádně vrátit zpět a vyčistit stav vnitřního systému upravené spuštěním operace vyvolané uživatelem. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-operation-list"></a>seznam operací clusteru sfctl
Získá seznam operací selhání vyvolané uživatelem filtrované podle zadaný vstup.

Získá seznam operací selhání vyvolané uživatelem filtrované zadaným vstupem.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --stav-filtr | Slouží k filtrování na OperationsState pro operace vyvolané uživatelem. - 65535 - vyberte Vše - 1 - vyberte Běh - 2 - vyberte RollingBack - 8 - vyberte Dokončeno - 16 - vyberte Chyby - 32 - vyberte Zrušeno - 64 - vyberte ForceCancelled.  Výchozí\: hodnota 65535. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |
| --typ-filtr | Slouží k filtrování na OperationType pro operace vyvolané uživatelem. - 65535 - vyberte vše - 1 - vyberte PartitionDataLoss. - 2 - vyberte PartitionQuorumLoss. - 4 - vyberte PartitionRestart. - 8 - vyberte NodeTransition.  Výchozí\: hodnota 65535. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-provision"></a>sfctl clusteru ustanovení
Zřízení kódu nebo konfigurační balíčky clusteru Service Fabric.

Ověřte a zřiďte kód nebo konfigurační balíčky clusteru Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --cluster-manifest-file-path --cluster-manifest-path --cluster-manifest-path --cluster-manifest-path | Cesta k souboru manifestu clusteru. |
| --cesta k souboru kódu | Cesta k souboru balíčku kódu clusteru. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-recover-system"></a>sfctl cluster recover-system
Označuje clusteru Service Fabric, že by se měl pokusit obnovit systémové služby, které jsou aktuálně uvízlé ve ztrátě kvora.

Označuje clusteru Service Fabric, že by se měl pokusit obnovit systémové služby, které jsou aktuálně uvízlé ve ztrátě kvora. Tato operace by měla být provedena pouze v případě, že je známo, že repliky, které jsou dole nelze obnovit. Nesprávné použití tohoto rozhraní API může způsobit potenciální ztrátu dat.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-report-health"></a>sfctl clusteru zpráva-stav
Odešle zprávu o stavu clusteru Service Fabric.

Odešle zprávu o stavu clusteru Service Fabric. Sestava musí obsahovat informace o zdroji sestavy stavu a vlastnosti, na které je hlášena. Sestava je odeslána do uzlu brány Service Fabric, který předá do úložiště stavu. Sestava může být přijata bránou, ale odmítnuta úložištěm stavu po dalším ověření. Úložiště stavu může například odmítnout sestavu z důvodu neplatného parametru, jako je zastaralé pořadové číslo. Chcete-li zjistit, zda byla sestava použita v úložišti stavu, spusťte GetClusterHealth a zkontrolujte, zda se sestava zobrazí v části HealthEvents.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --health-property [Povinné] | Vlastnost informací o stavu. <br><br> Entita může mít sestavy stavu pro různé vlastnosti. Vlastnost je řetězec a není pevný výčet, aby reportér flexibilitu kategorizovat podmínku stavu, který aktivuje sestavu. Například reportér s SourceId "LocalWatchdog" můžete sledovat stav dostupného disku na uzlu, tak to může hlásit "AvailableDisk" vlastnost na tomto uzlu. Stejný reportér může sledovat připojení uzlu, takže může hlásit vlastnost "Připojení" na stejném uzlu. V úložišti stavu jsou tyto sestavy považovány za samostatné události stavu pro zadaný uzel. Spolu s SourceId vlastnost jednoznačně identifikuje informace o stavu. |
| --stav -state [Povinné] | Možné hodnoty\: zahrnují "Neplatné", Ok, "Upozornění", "Chyba", Neznámý. |
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

## <a name="sfctl-cluster-select"></a>výběr clusteru sfctl
Připojí se ke koncovému bodu clusteru Service Fabric.

Pokud se připojujete k zabezpečenému clusteru, zadejte absolutní cestu k certifikátu (.crt) a souboru klíče (.key) nebo k jedinému souboru s oběma (.pem). Nezadávejte obojí. Pokud se připojujete k zabezpečenému clusteru, můžete také zadat absolutní cestu k souboru svazku certifikační autority nebo adresáři důvěryhodných certifikátů certifikační autority.  Neexistuje žádné připojení ke clusteru bez spuštění tohoto příkazu jako první, včetně připojení k localhost. Pro připojení k místnímu clusteru však není vyžadován žádný explicitní koncový bod.  Pokud používáte certifikát podepsaný svým držitelem nebo jiný certifikát, který není podepsán známou certifikační autoritou, předejte parametr --ca, abyste zajistili, že ověření projde. Pokud ne v produkčním clusteru, chcete-li obejít ověření na straně klienta (užitečné pro podepsané vlastní nebo nedobře známé certifikační autority), použijte možnost --no-verify. I když je to možné, nedoporučuje se pro produkční clustery. V opačném případě může dojít k chybě ověření certifikátu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --aad | K ověřování použijte službu Azure Active Directory. |
| --ca | Absolutní cesta k adresáři certifikátů certifikační autority, která se považuje za platný soubor sady CA nebo soubor svazku certifikační autority. Pokud používáte adresář certifikátů `c_rehash <directory>` certifikační autority, je nutné nejprve spustit dodaný openssl, aby bylo možné vypočítat hodnota hash certifikátů a vytvořit příslušné symbolické odkazy. Používá se k ověření, že certifikát vrácený clusterem je platný. |
| --certifikát | Absolutní cesta k souboru klientského certifikátu. |
| --koncový bod | Adresa URL koncového bodu clusteru, včetně portu a předpony HTTP nebo HTTPS. Koncový bod bude obvykle vypadat podobně\:jako https //<vaše url>\:19080. Pokud není uveden žádný koncový bod,\:bude výchozí\:http //localhost 19080.  Výchozí\: \:http //localhost\:19080. |
| --klíč | Absolutní cesta k souboru klíče klientského certifikátu. |
| --no-verify | Zakázat ověřování certifikátů při\: použití protokolu HTTPS, všimněte si, že se jedná o nezabezpečenou možnost a nemělby být používán pro produkční prostředí. |
| --pem | Absolutní cesta ke klientským certifikátům jako soubor u.pem. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-show-connection"></a>sfctl cluster show-connection
Ukažte, ke kterému clusteru Service Fabric je tato instance sfctl připojena.

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-unprovision"></a>zrušení zřízení clusteru sfctl
Zrušení zřízení kódu nebo konfiguračních balíčků clusteru Service Fabric.

Je podporována zrušení zřízení kódu a konfigurace samostatně.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --verze kódu | Verze balíčku kódu clusteru. |
| --config-version --config-version --config-version --con | Verze manifestu clusteru. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-upgrade"></a>upgrade clusteru sfctl
Začněte inovovat kód nebo konfigurační verzi clusteru Service Fabric.

Ověřte zadané parametry upgradu a začněte inovovat kód nebo konfigurační verzi clusteru Service Fabric, pokud jsou parametry platné.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --app-health-map --app-health-map --app-health-map --app | JSON kódované slovník párů název aplikace a maximální procento není v pořádku před vyvolání chyby. |
| --app-type-health-map --app-type-health-map --app-type-health-map --app | JSON kódované slovník párů názvu typu aplikace a maximální procento není v pořádku před vyvolání chyby. |
| --verze kódu | Verze kódu clusteru. |
| --config-version --config-version --config-version --con | Verze konfigurace clusteru. |
| --delta-health-evaluation | Umožňuje hodnocení rozdílového stavu, nikoli absolutní vyhodnocení stavu po dokončení každé domény upgradu. |
| --delta-unhealthy-uzly | Maximální povolené procento snížení stavu uzlů povolené během upgradů clusteru.  Výchozí\: hodnota 10. <br><br> Rozdíl se měří mezi stavem uzlů na začátku upgradu a stavem uzlů v době vyhodnocení stavu. Kontrola se provádí po každém dokončení upgradu upgradu upgradu upgradu, abyste se ujistili, že globální stav clusteru je v rámci tolerovaných limitů. |
| --selhání akce | Možné hodnoty\: zahrnují "Neplatné", "Rollback", 'Manual'. |
| --force-restart | Procesy jsou během upgradu vynuceně restartovány, i když se nezměnila verze kódu. <br><br> Upgrade pouze změní konfiguraci nebo data. |
| --retry kontroly stavu | Doba mezi pokusy o provedení kontroly stavu, pokud aplikace nebo cluster není v pořádku. |
| --zdravotní kontrola-stabilní | Doba, po kterou musí aplikace nebo cluster zůstat v pořádku, než upgrade přejde na další doménu upgradu. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. |
| --kontrola stavu-čekání | Doba čekání po dokončení upgradu domény před zahájením procesu kontroly stavu. |
| --replika-set-check-timeout | Maximální doba, po kterou je třeba zablokovat zpracování upgradovací domény a zabránit ztrátě dostupnosti v případě neočekávaných problémů. <br><br> Po vypršení tohoto časového limitu bude zpracování domény upgradu pokračovat bez ohledu na problémy se ztrátou dostupnosti. Časový rozsah je resetován na začátku každé domény upgradu. Platné hodnoty jsou mezi 0 a 42949672925 včetně. |
| --rolling-upgrade-mode -rolling -upgrade-mode --rolling-upgrade-mode --rolling | Možné hodnoty\: zahrnují "Neplatné", "UnmonitoredAuto", "UnmonitoredManual", "Monitored".  Výchozí\: UnmonitoredAuto. |
| --časový čas -t | Výchozí\: 60. |
| --nezdravé aplikace | Maximální povolené procento nefunkčních aplikací před oznámením chyby. <br><br> Chcete-li například povolit 10 % aplikací není v pořádku, tato hodnota by 10. Procento představuje maximální tolerované procento aplikací, které mohou být nefunkční, než je cluster považován za chybný. Pokud je procento respektováno, ale existuje alespoň jedna aplikace není v pořádku, stav je vyhodnocen jako upozornění. Vypočítá se vydělením počtu aplikací není v pořádku na celkový počet instancí aplikace v clusteru, s výjimkou aplikací typů aplikací, které jsou zahrnuty v ApplicationTypeHealthPolicyMap. Výpočtem zaokrouhluje nahoru tolerovat jednu chybu na malý počet aplikací. |
| --nezdravé uzly | Maximální povolené procento uzlů není v pořádku před oznámením chyby. <br><br> Chcete-li například povolit 10 % uzlů, které mají být nefunkční, tato hodnota by byla 10. Procento představuje maximální tolerované procento uzlů, které mohou být nefunkční, než je cluster považován za chybný. Pokud je procento respektováno, ale existuje alespoň jeden uzel není v pořádku, stav je vyhodnocen jako upozornění. Procento se vypočítá vydělením počtu uzlů není v pořádku nad celkový počet uzlů v clusteru. Výpočtem zaokrouhluje nahoru tolerovat jednu poruchu na malý počet uzlů. Ve velkých clusterech budou některé uzly vždy mimo nebo mimo pro opravy, takže toto procento by mělo být nakonfigurováno tak, aby to tolerovalo. |
| --upgrade-domain-delta-unhealthy-uzly --upgrade-domain-delta-unhealthy-nodes --upgrade-domain-delta-unhealthy-nodes -- | Maximální povolené procento stavu uzlů upgradu povolenou během upgradu clusteru.  Výchozí\: hodnota 15. <br><br> Rozdíl se měří mezi stavem uzlů domény upgradu na začátku upgradu a stavem uzlů domény upgradu v době vyhodnocení stavu. Kontrola se provádí po každém dokončení upgradu upgradu upgradu upgradu pro všechny dokončené domény upgradu, abyste se ujistili, že stav upgradovacích domén je v rámci tolerovaných limitů. |
| --upgrade-domain-timeout | Doba, po kterou musí být každá upgradovací doména dokončena před provedením akce FailureAction. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. |
| --upgrade-timeout | Doba, po kterou má být dokončena celková inovace, než je provedena akce Selhání. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. |
| --upozornění jako chyba | Označuje, zda jsou upozornění považována za chyby se stejnou závažností. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-upgrade-resume"></a>sfctl cluster upgrade-resume
Připojte upgrade clusteru na další doménu upgradu.

V případě potřeby přejděte na další upgradovací doménu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --upgrade-domain [Povinné] | Další upgradovací doména pro tento upgrade clusteru. |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-upgrade-rollback"></a>vrácení zpět v clusteru sfctl
Vraťte zpět upgrade clusteru Service Fabric.

Vraťte zpět kód nebo upgrade konfigurace clusteru Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-upgrade-status"></a>stav upgradu clusteru sfctl
Získá průběh aktuální ho upgradu clusteru.

Získá aktuální průběh probíhající ho upgradu clusteru. Pokud aktuálně neprobíhá žádný upgrade, získejte poslední stav předchozího upgradu clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-upgrade-update"></a>aktualizace upgradu clusteru sfctl
Aktualizujte parametry upgradu upgradu upgradu clusteru Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --app-health-map --app-health-map --app-health-map --app | JSON kódované slovník párů název aplikace a maximální procento není v pořádku před vyvolání chyby. |
| --app-type-health-map --app-type-health-map --app-type-health-map --app | JSON kódované slovník párů názvu typu aplikace a maximální procento není v pořádku před vyvolání chyby. |
| --delta-health-evaluation | Umožňuje hodnocení rozdílového stavu, nikoli absolutní vyhodnocení stavu po dokončení každé domény upgradu. |
| --delta-unhealthy-uzly | Maximální povolené procento snížení stavu uzlů povolené během upgradů clusteru.  Výchozí\: hodnota 10. <br><br> Rozdíl se měří mezi stavem uzlů na začátku upgradu a stavem uzlů v době vyhodnocení stavu. Kontrola se provádí po každém dokončení upgradu upgradu upgradu upgradu, abyste se ujistili, že globální stav clusteru je v rámci tolerovaných limitů. |
| --selhání akce | Možné hodnoty\: zahrnují "Neplatné", "Rollback", 'Manual'. |
| --force-restart | Procesy jsou během upgradu vynuceně restartovány, i když se nezměnila verze kódu. <br><br> Upgrade pouze změní konfiguraci nebo data. |
| --retry kontroly stavu | Doba mezi pokusy o provedení kontroly stavu, pokud aplikace nebo cluster není v pořádku. |
| --zdravotní kontrola-stabilní | Doba, po kterou musí aplikace nebo cluster zůstat v pořádku, než upgrade přejde na další doménu upgradu. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. |
| --kontrola stavu-čekání | Doba čekání po dokončení upgradu domény před zahájením procesu kontroly stavu. |
| --replika-set-check-timeout | Maximální doba, po kterou je třeba zablokovat zpracování upgradovací domény a zabránit ztrátě dostupnosti v případě neočekávaných problémů. <br><br> Po vypršení tohoto časového limitu bude zpracování domény upgradu pokračovat bez ohledu na problémy se ztrátou dostupnosti. Časový rozsah je resetován na začátku každé domény upgradu. Platné hodnoty jsou mezi 0 a 42949672925 včetně. |
| --rolling-upgrade-mode -rolling -upgrade-mode --rolling-upgrade-mode --rolling | Možné hodnoty\: zahrnují "Neplatné", "UnmonitoredAuto", "UnmonitoredManual", "Monitored".  Výchozí\: UnmonitoredAuto. |
| --časový čas -t | Výchozí\: 60. |
| --nezdravé aplikace | Maximální povolené procento nefunkčních aplikací před oznámením chyby. <br><br> Chcete-li například povolit 10 % aplikací není v pořádku, tato hodnota by 10. Procento představuje maximální tolerované procento aplikací, které mohou být nefunkční, než je cluster považován za chybný. Pokud je procento respektováno, ale existuje alespoň jedna aplikace není v pořádku, stav je vyhodnocen jako upozornění. Vypočítá se vydělením počtu aplikací není v pořádku na celkový počet instancí aplikace v clusteru, s výjimkou aplikací typů aplikací, které jsou zahrnuty v ApplicationTypeHealthPolicyMap. Výpočtem zaokrouhluje nahoru tolerovat jednu chybu na malý počet aplikací. |
| --nezdravé uzly | Maximální povolené procento uzlů není v pořádku před oznámením chyby. <br><br> Chcete-li například povolit 10 % uzlů, které mají být nefunkční, tato hodnota by byla 10. Procento představuje maximální tolerované procento uzlů, které mohou být nefunkční, než je cluster považován za chybný. Pokud je procento respektováno, ale existuje alespoň jeden uzel není v pořádku, stav je vyhodnocen jako upozornění. Procento se vypočítá vydělením počtu uzlů není v pořádku nad celkový počet uzlů v clusteru. Výpočtem zaokrouhluje nahoru tolerovat jednu poruchu na malý počet uzlů. Ve velkých clusterech budou některé uzly vždy mimo nebo mimo pro opravy, takže toto procento by mělo být nakonfigurováno tak, aby to tolerovalo. |
| --upgrade-domain-delta-unhealthy-uzly --upgrade-domain-delta-unhealthy-nodes --upgrade-domain-delta-unhealthy-nodes -- | Maximální povolené procento stavu uzlů upgradu povolenou během upgradu clusteru.  Výchozí\: hodnota 15. <br><br> Rozdíl se měří mezi stavem uzlů domény upgradu na začátku upgradu a stavem uzlů domény upgradu v době vyhodnocení stavu. Kontrola se provádí po každém dokončení upgradu upgradu upgradu upgradu pro všechny dokončené domény upgradu, abyste se ujistili, že stav upgradovacích domén je v rámci tolerovaných limitů. |
| --upgrade-domain-timeout | Doba, po kterou musí být každá upgradovací doména dokončena před provedením akce FailureAction. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. |
| --upgrade-druh | Možné hodnoty\: zahrnují "Neplatné", "Rolling", "Rolling_ForceRestart".  Výchozí\: postupné. |
| --upgrade-timeout | Doba, po kterou má být dokončena celková inovace, než je provedena akce Selhání. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nezdaří, pak je interpretován jako číslo představující celkový počet milisekund. |
| --upozornění jako chyba | Označuje, zda jsou upozornění považována za chyby se stejnou závažností. |

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