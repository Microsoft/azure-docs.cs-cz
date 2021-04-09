---
title: Cluster Azure Service Fabric CLI – sfctl
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu clusterů.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 05132e4c7e644e681e2e4cfdedaa2b04a066ebeb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86259960"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Umožňuje vybrat, spravovat a provozovat clustery Service Fabric.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| verze kódu | Načte seznam verzí kódu prostředků infrastruktury, které jsou zřízené ve Service Fabricm clusteru. |
| konfigurace – verze | Načte seznam verzí konfigurace prostředků infrastruktury, které jsou zřízené v clusteru Service Fabric. |
| nezávadnost | Získá stav clusteru Service Fabric. |
| manifest | Získejte manifest Service Fabricho clusteru. |
| operace – zrušení | Zruší operaci s chybou, která byla vyvolaný uživatelem. |
| operace – seznam | Získá seznam uživatelem vyvolaných operací selhání filtrovaných pomocí poskytnutého vstupu. |
| penzijní | Zřídit kód nebo konfigurační balíčky Service Fabricho clusteru. |
| obnovení systému | Určuje Cluster Service Fabric, který by se měl pokusit obnovit systémové služby, které jsou aktuálně zablokované ve ztrátě kvora. |
| Sestava – stav | Odešle zprávu o stavu v clusteru Service Fabric. |
| vybrali | Připojí se ke koncovému bodu Service Fabric clusteru. |
| Zobrazit připojení | Zobrazit, ke kterému Service Fabric clusteru je tato instance sfctl připojena. |
| zrušit zřízení | Zrušení zřízení kódu nebo konfiguračních balíčků Service Fabricho clusteru. |
| upgrade | Spusťte upgrade kódu nebo konfigurační verze Service Fabricho clusteru. |
| upgrade – obnovení | Proveďte upgrade clusteru na další upgradovací doménu. |
| upgrade – vrácení zpět | Vraťte zpět upgrade Service Fabricho clusteru. |
| upgrade – stav | Získá průběh aktuálního upgradu clusteru. |
| upgrade – aktualizace | Aktualizujte parametry upgradu Service Fabric upgradu clusteru. |

## <a name="sfctl-cluster-code-versions"></a>sfctl kódu clusteru – verze
Načte seznam verzí kódu prostředků infrastruktury, které jsou zřízené ve Service Fabricm clusteru.

Načte seznam informací o verzích kódů prostředků infrastruktury, které jsou zřízené v clusteru. Parametr CodeVersion lze použít k volitelnému filtrování výstupu pouze na konkrétní verzi.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --Code-Version | Verze produktu Service Fabric. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-config-versions"></a>sfctl konfigurace clusteru – verze
Načte seznam verzí konfigurace prostředků infrastruktury, které jsou zřízené v clusteru Service Fabric.

Načte seznam informací o verzích konfigurace prostředků infrastruktury, které jsou zřízené v clusteru. Parametr ConfigVersion lze použít k volitelnému filtrování výstupu pouze na konkrétní verzi.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --config-Version | Konfigurační verze Service Fabric. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-health"></a>stav clusteru sfctl
Získá stav clusteru Service Fabric.

Pomocí EventsHealthStateFilter můžete filtrovat kolekci událostí stavu hlášených v clusteru na základě stavu. Podobně použijte NodesHealthStateFilter a ApplicationsHealthStateFilter k filtrování kolekce uzlů a aplikací vrácených na základě jejich agregovaného stavu.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --Applications-Health-State-Filter | Umožňuje filtrování objektů stavu aplikace vrácených ve výsledku dotazu na stav clusteru na základě jejich stavu. Možné hodnoty pro tento parametr zahrnují celočíselnou hodnotu získanou ze členů nebo bitové operace na členech výčtu HealthStateFilter. Vrátí se pouze aplikace, které odpovídají filtru. Všechny aplikace se používají k vyhodnocení agregovaného stavu. Pokud tento parametr nezadáte, vrátí se všechny položky. Hodnoty stavu jsou výčty založené na příznak, takže hodnota by mohla být kombinací těchto hodnot získaných pomocí bitového operátoru OR. Například pokud je zadaná hodnota 6, pak se vrátí stav aplikací s hodnotou OK (2) a upozornění (4).  <br> -Výchozí-výchozí hodnota. Odpovídá jakémukoli elementu. Hodnota je nula.  <br> -None-Filter, který neodpovídá žádné hodnotě elementu. Používá se k tomu, aby se v dané kolekci stavů nevracely žádné výsledky. Hodnota je 1.  <br> -OK – filtr, který odpovídá zadanému vstupu s hodnotou podstavu OK. Hodnota je 2.  <br> -Warning-Filter, který odpovídá vstupu s upozorněním na podstavovou hodnotu. Hodnota je 4.  <br> – Filtr chyb, který odpovídá zadanému vstupu s chybou hodnoty elementu stav Hodnota je 8.  <br> -All – filtr, který odpovídá zadanému vstupu s jakoukoli hodnotou elementu. Hodnota je 65535. |
| --Events – stav – filtr | Umožňuje filtrovat kolekci objektů HealthEvent vrácených na základě stavu. Možné hodnoty pro tento parametr zahrnují celočíselnou hodnotu jednoho z následujících stavů. Vrátí se pouze události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaného stavu. Pokud tento parametr nezadáte, vrátí se všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota by mohla být kombinací těchto hodnot získána pomocí bitového operátoru OR. Pokud je například zadaná hodnota 6, budou vráceny všechny události s hodnotou ' OK (2) a upozornění (4).  <br> -Výchozí-výchozí hodnota. Odpovídá jakémukoli elementu. Hodnota je nula.  <br> -None-Filter, který neodpovídá žádné hodnotě elementu. Používá se k tomu, aby se v dané kolekci stavů nevracely žádné výsledky. Hodnota je 1.  <br> -OK – filtr, který odpovídá zadanému vstupu s hodnotou podstavu OK. Hodnota je 2.  <br> -Warning-Filter, který odpovídá vstupu s upozorněním na podstavovou hodnotu. Hodnota je 4.  <br> – Filtr chyb, který odpovídá zadanému vstupu s chybou hodnoty elementu stav Hodnota je 8.  <br> -All – filtr, který odpovídá zadanému vstupu s jakoukoli hodnotou elementu. Hodnota je 65535. |
| --Exclude-Health-Statistics | Určuje, zda mají být v rámci výsledku dotazu vráceny statistiky stavu. Výchozí hodnota je false. Statistika zobrazuje počet podřízených entit ve stavu OK, varování a chyba. |
| --include-System-Application-Health-Statistics | Určuje, jestli má Statistika stavu zahrnovat \: statistiku stavu aplikace prostředků infrastruktury/. Výchozí hodnota je false. Pokud je IncludeSystemApplicationHealthStatistics nastavené na true, Statistika stavu zahrnuje entity, které patří do aplikace Fabric \: /. V opačném případě výsledek dotazu zahrnuje statistiky stavu pouze pro uživatelské aplikace. Aby se tento parametr mohl použít, musí být do výsledku dotazu zahrnuté statistiky stavu. |
| --Nodes-stav – filtr | Umožňuje filtrování objektů stavu uzlu vrácených ve výsledku dotazu na stav clusteru na základě jejich stavu. Možné hodnoty pro tento parametr zahrnují celočíselnou hodnotu jednoho z následujících stavů. Vrátí se pouze uzly, které odpovídají filtru. Všechny uzly slouží k vyhodnocení agregovaného stavu. Pokud tento parametr nezadáte, vrátí se všechny položky. Hodnoty stavu jsou výčty založené na příznak, takže hodnota by mohla být kombinací těchto hodnot získaných pomocí bitového operátoru OR. Například pokud je zadaná hodnota 6, potom se vrátí stav uzlů s hodnotou OK (2) a upozornění (4).  <br> -Výchozí-výchozí hodnota. Odpovídá jakémukoli elementu. Hodnota je nula.  <br> -None-Filter, který neodpovídá žádné hodnotě elementu. Používá se k tomu, aby se v dané kolekci stavů nevracely žádné výsledky. Hodnota je 1.  <br> -OK – filtr, který odpovídá zadanému vstupu s hodnotou podstavu OK. Hodnota je 2.  <br> -Warning-Filter, který odpovídá vstupu s upozorněním na podstavovou hodnotu. Hodnota je 4.  <br> – Filtr chyb, který odpovídá zadanému vstupu s chybou hodnoty elementu stav Hodnota je 8.  <br> -All – filtr, který odpovídá zadanému vstupu s jakoukoli hodnotou elementu. Hodnota je 65535. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-manifest"></a>manifest clusteru sfctl
Získejte manifest Service Fabricho clusteru.

Získejte manifest Service Fabricho clusteru. Manifest clusteru obsahuje vlastnosti clusteru, které zahrnují různé typy uzlů v clusteru, konfigurace zabezpečení, selhání a upgrady doménových topologií atd. Tyto vlastnosti jsou zadány jako součást ClusterConfig.JSv souboru při nasazení samostatného clusteru. Většina informací v manifestu clusteru se ale generuje interně Service Fabric během nasazování clusteru v jiných scénářích nasazení (například při použití Azure Portal). Obsah manifestu clusteru slouží pouze k informativním účelům a uživatelé neočekávají závislost na formátu obsahu souboru nebo jeho výkladu.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-operation-cancel"></a>operace clusteru sfctl – zrušit
Zruší operaci s chybou, která byla vyvolaný uživatelem.

Následující rozhraní API spustí operace selhání, které mohou být zrušeny pomocí CancelOperation \: StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Pokud je hodnota Force false, zavedená operace vyvolaný uživatelem se řádně zastaví a vyčistí.  Pokud je hodnota true, příkaz se zruší a některé interní stavy můžou zůstat na pozadí.  Je třeba zadat vynutit jako true, aby se opatrně používala. Volání tohoto rozhraní API s vynucenou nastavenou na hodnotu true není povoleno, dokud toto rozhraní API již není voláno na stejném příkazu testu s vynuceným nastavením na hodnotu false, nebo pokud již testovací příkaz nemá OperationState OperationState. RollingBack. Vyjasnění \: OperationState. RollingBack znamená, že systém bude nebo bude vyčistit vnitřní stav systému způsobený provedením příkazu.  Nebude data obnovovat, pokud by testovací příkaz způsobil ztrátu dat.  Například pokud voláte StartDataLoss a pak zavoláte toto rozhraní API, systém vyčistí pouze vnitřní stav ze spuštění příkazu. Neobnoví data cílového oddílu, pokud příkaz provedl dostatečně daleko, aby způsobil ztrátu dat. Důležité upozornění: \:  Pokud je toto rozhraní API vyvoláno s silou = = true, může být vnitřní stav ponechán na pozadí.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --operace-ID [povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  Toto se předává do odpovídajícího rozhraní API getprogress. |
| --Force | Označuje, jestli se má řádně vrátit a vyčistit stav interního systému, který se změnil spuštěním operace vyvolané uživatelem. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-operation-list"></a>operace clusteru sfctl – seznam
Získá seznam uživatelem vyvolaných operací selhání filtrovaných pomocí poskytnutého vstupu.

Získá seznam uživatelem vyvolaných operací selhání filtrovaných pomocí poskytnutého vstupu.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --State-Filter | Používá se k filtrování OperationState pro operace, které byly získány uživatelem. -65535-vybrat vše-1-vybrat spuštění – 2-vybrat RollingBack-8-vybrat dokončeno-16-vyberte možnost chyba-32-vybrat zrušeno-64-vybrat ForceCancelled.  Výchozí hodnota je \: 65535. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |
| --Type-Filter | Slouží k filtrování typem operace OperationType pro operace, které byly získány uživatelem. -65535-vybrat vše-1-vybrat PartitionDataLoss. -2 – vyberte PartitionQuorumLoss. -4 Vyberte PartitionRestart. -8 – vyberte NodeTransition.  Výchozí hodnota je \: 65535. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-provision"></a>zřízení clusteru sfctl
Zřídit kód nebo konfigurační balíčky Service Fabricho clusteru.

Ověří a zřídí balíčky kódu nebo konfigurace Service Fabricho clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --cluster-manifest-File-Path | Cesta k souboru manifestu clusteru |
| --Code-File-Path | Cesta k souboru balíčku kódu clusteru |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-recover-system"></a>sfctl cluster Recovery-System
Určuje Cluster Service Fabric, který by se měl pokusit obnovit systémové služby, které jsou aktuálně zablokované ve ztrátě kvora.

Určuje Cluster Service Fabric, který by se měl pokusit obnovit systémové služby, které jsou aktuálně zablokované ve ztrátě kvora. Tato operace by měla být provedena pouze v případě, že je známo, že nelze obnovit repliky, které jsou mimo provoz. Nesprávné použití tohoto rozhraní API může způsobit ztrátu dat.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-report-health"></a>Sestava clusteru sfctl – stav
Odešle zprávu o stavu v clusteru Service Fabric.

Odešle zprávu o stavu v clusteru Service Fabric. Sestava musí obsahovat informace o zdroji sestavy stavu a vlastnosti, na které je hlášena. Sestava se odešle do uzlu Service Fabric brány, který se přepošle na Health Store. Tuto sestavu může přijmout brána, ale Health Store po dodatečném ověření odmítnuta. Health Store například může zprávu odmítat z důvodu neplatného parametru, jako je například zastaralé pořadové číslo. Pokud chcete zjistit, jestli se sestava použila v Health Store, spusťte GetClusterHealth a zkontrolujte, jestli se sestava zobrazuje v části HealthEvents.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
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

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-select"></a>Výběr clusteru sfctl
Připojí se ke koncovému bodu Service Fabric clusteru.

Pokud se připojujete k zabezpečenému clusteru, zadejte absolutní cestu k certifikátu (. CRT) a soubor klíče (. Key) nebo jeden soubor s oběma (. pem). Nezadávejte obojí. Pokud budete chtít připojení k zabezpečenému clusteru, můžete taky zadat absolutní cestu k souboru nebo adresáři certifikačního úřadu CA.  Neexistují žádné připojení ke clusteru, aniž by bylo třeba spustit tento příkaz, včetně připojení k místnímu hostiteli. Pro připojení k místnímu clusteru ale není nutný žádný explicitní koncový bod.  Pokud používáte certifikát podepsaný svým držitelem nebo jiný certifikát, který není podepsaný známou certifikační autoritou, předejte parametr--CA, abyste zajistili, že ověření proběhlo úspěšně. Pokud není v produkčním clusteru, pokud chcete obejít ověřování na straně klienta (užitečné pro podepsaný nebo neznámou známou certifikační autoritu), použijte možnost--No-ověření. I když je to možné, nedoporučuje se pro produkční clustery. V opačném případě může dojít k chybě ověření certifikátu.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --AAD | Pro ověřování použijte Azure Active Directory. |
| --CA | Absolutní cesta k adresáři certifikátů certifikační autority, která bude považovat za platný nebo soubor sady prostředků CA. Pokud používáte adresář certifikátů CA, které `c_rehash <directory>` poskytuje OpenSSL, musí se nejdřív spustit, aby se vypočítaly hodnoty hash certifikátů a vytvořily příslušné odkazy na symbolické hodnoty. Slouží k ověření, zda je certifikát vrácený clusterem platný. |
| --CERT | Absolutní cesta k souboru klientského certifikátu. |
| – koncový bod | Adresa URL koncového bodu clusteru, včetně předpony port a HTTP nebo HTTPS. Koncový bod obvykle bude vypadat jako https \: //<adresa url>\: 19080. Pokud není zadán žádný koncový bod, bude ve výchozím nastavení nastaveno http \: //localhost \: 19080.  Výchozí \: http \: //localhost \: 19080. |
| --klíč | Absolutní cesta k souboru klíče klientského certifikátu. |
| --bez ověřování | Zakázat ověřování pro certifikáty při použití protokolu HTTPS. Upozorňujeme, \: že se jedná o nezabezpečenou možnost a neměla by se používat pro produkční prostředí. |
| --PEM | Absolutní cesta ke klientskému certifikátu, jako soubor. pem. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-show-connection"></a>zobrazení clusteru sfctl – připojení
Zobrazit, ke kterému Service Fabric clusteru je tato instance sfctl připojena.

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-unprovision"></a>zrušení zřízení clusteru sfctl
Zrušení zřízení kódu nebo konfiguračních balíčků Service Fabricho clusteru.

Je podporováno zrušení zajišťování kódu a konfigurace samostatně.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --Code-Version | Verze balíčku kódu clusteru |
| --config-Version | Verze manifestu clusteru |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-upgrade"></a>upgrade clusteru sfctl
Spusťte upgrade kódu nebo konfigurační verze Service Fabricho clusteru.

Ověřte zadané parametry upgradu a v případě, že jsou parametry platné, spusťte upgrade kódu nebo konfigurační verze Service Fabricho clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --App-Health-map | Slovník kódovaný v kódování JSON párů názvů aplikací a maximální procento není v pořádku před vyvoláním chyby. |
| --Typ aplikace-typ-mapa | Slovník kódovaný ve formátu JSON páry názvu typu aplikace a maximální procento není v pořádku před vyvoláním chyby. |
| --Code-Version | Verze kódu clusteru |
| --config-Version | Verze konfigurace clusteru |
| --Delta – vyhodnocení stavu | Povolí rozdílové vyhodnocení stavu místo absolutního vyhodnocení stavu po dokončení jednotlivých upgradovacích domén. |
| --Delta-není v pořádku – uzly | Maximální povolené procento povoleného snížení stavu uzlů během upgradů clusteru.  Výchozí hodnota \: 10. <br><br> Rozdíl je měřen mezi stavem uzlů na začátku upgradu a stavem uzlů v době hodnocení stavu. Tato kontrolu proběhne po dokončení upgradu každé domény upgradu, aby se zajistilo, že globální stav clusteru je v rámci povolených limitů. |
| --Chyba – akce | Možné hodnoty zahrnují \: "neplatné", "Rollback", "Manual". |
| --Force-restart | Procesy jsou během upgradu vynuceně restartovány i v případě, že se verze kódu nezměnila. <br><br> Upgrade pouze změní konfiguraci nebo data. |
| --Health-Check-opakování | Doba mezi pokusy o provedení kontrol stavu, pokud aplikace nebo cluster není v pořádku. |
| --Health-Check-stabilní | Doba, po kterou musí aplikace nebo cluster zůstat v pořádku, než bude upgrade pokračovat na další upgradovací doménu. <br><br> Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. |
| --Health-Check-Wait | Doba, po kterou se má po dokončení upgradu domény počkat, než se spustí proces kontroly stavu. |
| --replice-set-check-timeout | Maximální doba, po kterou se má blokovat zpracování upgradovací domény a zabránit ztrátě dostupnosti, pokud dojde k neočekávaným problémům. <br><br> Po vypršení časového limitu bude zpracování upgradovací domény pokračovat bez ohledu na problémy se ztrátou dostupnosti. Časový limit se resetuje na začátku každé upgradovací domény. Platné hodnoty jsou mezi 0 a 42949672925 včetně. |
| --Kumulovaný-upgrade – režim | Možné hodnoty zahrnují \: "Invalid", "UnmonitoredAuto", "UnmonitoredManual", "Monitored".  Výchozí \: UnmonitoredAuto. |
| --Timeout-t | Výchozí hodnota je \: 60. |
| --není v pořádku – aplikace | Maximální povolené procento nezdravých aplikací před Nahlášením chyby. <br><br> Například pokud chcete, aby 10% aplikací bylo ve špatném stavu, bude tato hodnota 10. Procento představuje maximální povolený procentuální podíl aplikací, které mohou být chybné, než se cluster bude považovat za chybu. Pokud je dodrženo procento, ale existuje aspoň jedna poškozená aplikace, stav se vyhodnotí jako varování. To se počítá vydělením počtu nezdravých aplikací nad celkovým počtem instancí aplikace v clusteru, kromě aplikací typů aplikací, které jsou součástí ApplicationTypeHealthPolicyMap. Výpočet se zaokrouhlí na jednu neúspěch u malého počtu aplikací. |
| – není v pořádku – uzly | Maximální povolené procento uzlů, které nejsou v pořádku, před odesláním chyby. <br><br> Pokud například chcete, aby 10% uzlů bylo v nesprávném stavu, bude tato hodnota 10. Procento představuje maximální povolený procentuální podíl uzlů, které mohou být chybné, než se cluster bude považovat za chybu. Pokud je dodrženo procento, ale existuje alespoň jeden uzel, který není v pořádku, je stav vyhodnocen jako upozornění. Procentuální hodnota se vypočítá vydělením počtu uzlů, které nejsou v pořádku, na celkový počet uzlů v clusteru. Výpočet se zaokrouhlí na jednu neúspěch na malých číslech uzlů. Ve velkých clusterech se u některých uzlů vždycky odeberou nebo odeberou opravy, takže toto procento by mělo být nakonfigurované na tolerování. |
| --upgrade-Domain-rozdíl-není v pořádku – uzly | Maximální povolené procento nesprávného snížení stavu uzlů domény upgradu během upgradů clusteru.  Výchozí hodnota je \: 15. <br><br> Rozdíl se měří mezi stavem uzlů upgradovací domény na začátku upgradu a stavu uzlů upgradovací domény v době hodnocení stavu. Tato kontrolu proběhne po dokončení upgradu každé domény upgradu pro všechny dokončené domény upgradu, aby se zajistilo, že stav domén upgradu spadá do dovolených omezení. |
| --upgrade-Domain – vypršel časový limit. | Doba, po kterou musí být každá upgradovací doména dokončena před provedením FailureAction. <br><br> Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. |
| --upgrade – časový limit | Doba, po kterou musí být celkový upgrade dokončen před provedením FailureAction. <br><br> Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. |
| --Warning-as-Error | Označuje, zda jsou upozornění zpracována se stejnou závažností jako chyby. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-upgrade-resume"></a>upgrade clusteru sfctl – obnovení
Proveďte upgrade clusteru na další upgradovací doménu.

Pokud je to vhodné, změňte kód clusteru nebo upgradujte na další upgradovací doménu.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --upgrade-doména [požadováno] | Další upgradovací doména pro tento upgrade clusteru. |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-upgrade-rollback"></a>upgrade clusteru sfctl – vrácení zpět
Vraťte zpět upgrade Service Fabricho clusteru.

Vraťte zpět upgrade kódu nebo konfigurace Service Fabricho clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-upgrade-status"></a>upgrade clusteru sfctl – stav
Získá průběh aktuálního upgradu clusteru.

Získá aktuální průběh probíhajícího upgradu clusteru. Pokud v tuto chvíli neprobíhá upgrade, získejte poslední stav předchozího upgradu clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-cluster-upgrade-update"></a>upgrade clusteru sfctl – aktualizace
Aktualizujte parametry upgradu Service Fabric upgradu clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --App-Health-map | Slovník kódovaný v kódování JSON párů názvů aplikací a maximální procento není v pořádku před vyvoláním chyby. |
| --Typ aplikace-typ-mapa | Slovník kódovaný ve formátu JSON páry názvu typu aplikace a maximální procento není v pořádku před vyvoláním chyby. |
| --Delta – vyhodnocení stavu | Povolí rozdílové vyhodnocení stavu místo absolutního vyhodnocení stavu po dokončení jednotlivých upgradovacích domén. |
| --Delta-není v pořádku – uzly | Maximální povolené procento povoleného snížení stavu uzlů během upgradů clusteru.  Výchozí hodnota \: 10. <br><br> Rozdíl je měřen mezi stavem uzlů na začátku upgradu a stavem uzlů v době hodnocení stavu. Tato kontrolu proběhne po dokončení upgradu každé domény upgradu, aby se zajistilo, že globální stav clusteru je v rámci povolených limitů. |
| --Chyba – akce | Možné hodnoty zahrnují \: "neplatné", "Rollback", "Manual". |
| --Force-restart | Procesy jsou během upgradu vynuceně restartovány i v případě, že se verze kódu nezměnila. <br><br> Upgrade pouze změní konfiguraci nebo data. |
| --Health-Check-opakování | Doba mezi pokusy o provedení kontrol stavu, pokud aplikace nebo cluster není v pořádku. |
| --Health-Check-stabilní | Doba, po kterou musí aplikace nebo cluster zůstat v pořádku, než bude upgrade pokračovat na další upgradovací doménu. <br><br> Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. |
| --Health-Check-Wait | Doba, po kterou se má po dokončení upgradu domény počkat, než se spustí proces kontroly stavu. |
| --replice-set-check-timeout | Maximální doba, po kterou se má blokovat zpracování upgradovací domény a zabránit ztrátě dostupnosti, pokud dojde k neočekávaným problémům. <br><br> Po vypršení časového limitu bude zpracování upgradovací domény pokračovat bez ohledu na problémy se ztrátou dostupnosti. Časový limit se resetuje na začátku každé upgradovací domény. Platné hodnoty jsou mezi 0 a 42949672925 včetně. |
| --Kumulovaný-upgrade – režim | Možné hodnoty zahrnují \: "Invalid", "UnmonitoredAuto", "UnmonitoredManual", "Monitored".  Výchozí \: UnmonitoredAuto. |
| --Timeout-t | Výchozí hodnota je \: 60. |
| --není v pořádku – aplikace | Maximální povolené procento nezdravých aplikací před Nahlášením chyby. <br><br> Například pokud chcete, aby 10% aplikací bylo ve špatném stavu, bude tato hodnota 10. Procento představuje maximální povolený procentuální podíl aplikací, které mohou být chybné, než se cluster bude považovat za chybu. Pokud je dodrženo procento, ale existuje aspoň jedna poškozená aplikace, stav se vyhodnotí jako varování. To se počítá vydělením počtu nezdravých aplikací nad celkovým počtem instancí aplikace v clusteru, kromě aplikací typů aplikací, které jsou součástí ApplicationTypeHealthPolicyMap. Výpočet se zaokrouhlí na jednu neúspěch u malého počtu aplikací. |
| – není v pořádku – uzly | Maximální povolené procento uzlů, které nejsou v pořádku, před odesláním chyby. <br><br> Pokud například chcete, aby 10% uzlů bylo v nesprávném stavu, bude tato hodnota 10. Procento představuje maximální povolený procentuální podíl uzlů, které mohou být chybné, než se cluster bude považovat za chybu. Pokud je dodrženo procento, ale existuje alespoň jeden uzel, který není v pořádku, je stav vyhodnocen jako upozornění. Procentuální hodnota se vypočítá vydělením počtu uzlů, které nejsou v pořádku, na celkový počet uzlů v clusteru. Výpočet se zaokrouhlí na jednu neúspěch na malých číslech uzlů. Ve velkých clusterech se u některých uzlů vždycky odeberou nebo odeberou opravy, takže toto procento by mělo být nakonfigurované na tolerování. |
| --upgrade-Domain-rozdíl-není v pořádku – uzly | Maximální povolené procento nesprávného snížení stavu uzlů domény upgradu během upgradů clusteru.  Výchozí hodnota je \: 15. <br><br> Rozdíl se měří mezi stavem uzlů upgradovací domény na začátku upgradu a stavu uzlů upgradovací domény v době hodnocení stavu. Tato kontrolu proběhne po dokončení upgradu každé domény upgradu pro všechny dokončené domény upgradu, aby se zajistilo, že stav domén upgradu spadá do dovolených omezení. |
| --upgrade-Domain – vypršel časový limit. | Doba, po kterou musí být každá upgradovací doména dokončena před provedením FailureAction. <br><br> Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. |
| --upgrade-druh | Možné hodnoty zahrnují \: ' invalid ', ' válcování ', ' Rolling_ForceRestart '.  Výchozí \: vracení. |
| --upgrade – časový limit | Doba, po kterou musí být celkový upgrade dokončen před provedením FailureAction. <br><br> Je nejprve interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepovede, interpretuje se jako číslo představující celkový počet milisekund. |
| --Warning-as-Error | Označuje, zda jsou upozornění zpracována se stejnou závažností jako chyby. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](./scripts/sfctl-upgrade-application.md).
