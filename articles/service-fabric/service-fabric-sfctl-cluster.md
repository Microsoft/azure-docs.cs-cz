---
title: Cluster Azure Service Fabric CLI - sfctl | Dokumentace Microsoftu
description: Popisuje příkazy Service Fabric CLI sfctl clusteru.
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
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 0d9ee24e9ab104fb554033802507f78fcbf38fc3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55170925"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Vyberte, spravovat a provozovat clustery Service Fabric.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| Kód verze | Získá seznam prostředků infrastruktury verze kódu, které jsou zřízené v clusteru Service Fabric. |
| verze konfigurace | Získá seznam fabric config verze, které jsou zřízené v clusteru Service Fabric. |
| zdravotnictví | Získá stav clusteru Service Fabric. |
| Manifest | Získejte manifestu clusteru Service Fabric. |
| zrušit operaci | Chyby vyvolané uživatel operaci zruší. |
| seznam operací | Získá seznam operací chybu způsobenou uživatele filtrované podle zadaného vstupu. |
| Zřizování | Zřízení balíčky kódu nebo konfigurace clusteru Service Fabric. |
| obnovení systému | Do clusteru Service Fabric označuje, že by měl pokusí o zotavení systému služby, které jsou aktuálně zablokované ve ztrátě kvora. |
| report-health | Odešle zprávu o stavu v clusteru Service Fabric. |
| vybrat | Se připojí ke koncovému bodu clusteru Service Fabric. |
| show-connection | Zobrazit clusteru Service Fabric, které tato instance sfctl je připojen k. |
| zrušení zajišťování | Zrušení zřízení balíčky kódu nebo konfigurace clusteru Service Fabric. |
| upgradovat | Spuštění upgradu verze kódu nebo konfigurace clusteru Service Fabric. |
| upgrade-resume | Ujistěte se, přejít k další upgradovací doméně upgradu clusteru. |
| upgrade-rollback | Upgrade clusteru Service Fabric se vrátit zpět. |
| upgrade-status | Získá průběh aktuální upgradu clusteru. |
| upgrade-update | Aktualizujte parametry upgradu upgrade clusteru Service Fabric. |

## <a name="sfctl-cluster-code-versions"></a>sfctl cluster code-versions
Získá seznam prostředků infrastruktury verze kódu, které jsou zřízené v clusteru Service Fabric.

Získá seznam informací o prostředcích infrastruktury, které verze kódu, které jsou zřízené v clusteru. Parametr CodeVersion je možné volitelně filtruje výstup na tuto konkrétní verzi.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --code-version | Verze produktu Service Fabric. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-cluster-config-versions"></a>sfctl cluster config-versions
Získá seznam fabric config verze, které jsou zřízené v clusteru Service Fabric.

Získá seznam informací o prostředcích infrastruktury, které verze konfigurace, které jsou zřízené v clusteru. Parametr ConfigVersion je možné volitelně filtruje výstup na tuto konkrétní verzi.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --config-version | Konfigurace verze platformy Service Fabric. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-cluster-health"></a>sfctl stavu clusteru
Získá stav clusteru Service Fabric.

EventsHealthStateFilter použijte k filtrování kolekce událostí stavu hlášené pro cluster podle stavu. Podobně, použít NodesHealthStateFilter a ApplicationsHealthStateFilter k filtrování kolekce uzlů a aplikace, které jsou vráceny na základě jejich agregované stavu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --applications-health-state-filter | Umožňuje filtrování objekty stavu stavu aplikací vrácené ve výsledku dotazu stav clusteru na základě jejich stavu. Možné hodnoty pro tento parametr obsahovat celočíselnou hodnotu od členů nebo bitové operace u členů výčtu HealthStateFilter. Jsou vráceny pouze aplikace, které odpovídají filtru. Všechny aplikace se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadán, budou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota může být kombinací těchto hodnot pomocí bitový operátor "OR". Například pokud zadaná hodnota je 6 stav aplikací s hodnotou elementu HealthState OK (2) a upozorněním (4), jsou vráceny.  <br> – Výchozí – výchozí hodnota. Odpovídá jakékoli stav HealthState. Hodnota je nula.  <br> -Žádný - filtr, který se pravděpodobně neshoduje s žádnou hodnotu stavu HealthState. Použít, aby nevracela žádné výsledky na dané kolekce stavů. Hodnota je 1.  <br> -Ok – umožňuje filtrovat, že odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Warning - filtr, že hodnota vstupu odpovídá k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který se shoduje s hodnotou elementu HealthState Chyba vstupu. Hodnota je 8.  <br> -All - filtr, který odpovídá s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --events-health-state-filter | Umožňuje filtrování vrácených objektů HealthEvent kolekce na základě stavu. Možné hodnoty pro tento parametr obsahovat celočíselnou hodnotu některého z následujících stavů. Vrátí se jenom události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadán, budou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota může být kombinací těchto hodnot, pomocí bitový operátor "OR". Například pokud zadaná hodnota je 6 všechny události s hodnotou elementu HealthState OK (2) a upozorněním (4), jsou vráceny.  <br> – Výchozí – výchozí hodnota. Odpovídá jakékoli stav HealthState. Hodnota je nula.  <br> -Žádný - filtr, který se pravděpodobně neshoduje s žádnou hodnotu stavu HealthState. Použít, aby nevracela žádné výsledky na dané kolekce stavů. Hodnota je 1.  <br> -Ok – umožňuje filtrovat, že odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Warning - filtr, že hodnota vstupu odpovídá k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který se shoduje s hodnotou elementu HealthState Chyba vstupu. Hodnota je 8.  <br> -All - filtr, který odpovídá s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --exclude statistiky stavu | Určuje, zda má být vrácen statistik stavu jako součást výsledků dotazu. Ve výchozím nastavení False. Statistiky ukazují počet podřízených entit ve stavu Ok, upozornění a chyby. |
| --include-system-application-health-statistics | Určuje, zda by měl obsahovat statistik stavu prostředků infrastruktury \: /System statistik stavu aplikace. Ve výchozím nastavení False. Pokud IncludeSystemApplicationHealthStatistics je nastavena na hodnotu true, stav statistické údaje obsahují entity, které patří do prostředků infrastruktury \: /System aplikace. V opačném případě výsledek dotazu obsahuje statistik stavu pouze pro uživatele aplikace. Statistika stavu musí být součástí výsledku dotazu pro tento parametr má být použita. |
| --nodes-health-state-filter | Umožňuje filtrování uzlu stavu stavu objektů vrácené ve výsledku dotazu stav clusteru na základě jejich stavu. Možné hodnoty pro tento parametr obsahovat celočíselnou hodnotu některého z následujících stavů. Jsou vráceny pouze uzly, které odpovídají filtru. Všechny uzly se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadán, budou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota může být kombinací těchto hodnot pomocí bitový operátor "OR". Například pokud zadaná hodnota je 6 stav uzlů s hodnotou elementu HealthState OK (2) a upozorněním (4), jsou vráceny.  <br> – Výchozí – výchozí hodnota. Odpovídá jakékoli stav HealthState. Hodnota je nula.  <br> -Žádný - filtr, který se pravděpodobně neshoduje s žádnou hodnotu stavu HealthState. Použít, aby nevracela žádné výsledky na dané kolekce stavů. Hodnota je 1.  <br> -Ok – umožňuje filtrovat, že odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Warning - filtr, že hodnota vstupu odpovídá k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který se shoduje s hodnotou elementu HealthState Chyba vstupu. Hodnota je 8.  <br> -All - filtr, který odpovídá s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-cluster-manifest"></a>sfctl manifestu clusteru
Získejte manifestu clusteru Service Fabric.

Získejte manifestu clusteru Service Fabric. Manifest clusteru obsahuje vlastnosti clusteru, které obsahují různé typy uzlů v clusteru, konfigurace zabezpečení, odolnost a upgradovací doména topologie atd. Tyto vlastnosti jsou určeny jako součást souboru ClusterConfig.JSON při nasazení samostatného clusteru. Ale většinu informací v manifestu clusteru je generované interně službou service fabric během nasazování clusteru v jiných scénářích nasazení (například při použití webu Azure portal). Obsah manifestu clusteru, jsou pouze k informačním účelům a uživatelé se nepředpokládá zavést závislost na formát obsah souboru nebo jeho interpretace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-cluster-operation-cancel"></a>zrušení operace clusteru sfctl
Chyby vyvolané uživatel operaci zruší.

Následující rozhraní API spustit selhání operací, které mohou být zrušeny pomocí CancelOperation\: StartDataLoss StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Pokud platnost hodnotu false, pak zadané uživatele vyvolané operace se elegantně zastaví a vyčištění.  Pokud platnost má hodnotu true, příkaz se přeruší a mohou být zanechány některé vnitřní stav.  Platnost zadáte jako hodnotu true by měla být používána opatrně. Volající toto rozhraní API s platnost nastavenou na hodnotu true není povolena, dokud toto rozhraní API již byla volána na stejný příkaz test s platnost nastavenou na hodnotu false první, nebo pokud příkaz test už má OperationState OperationState.RollingBack. 

Vysvětlení\: OperationState.RollingBack prostředky, které systém bude/čistí interního systému stavu způsobené provádění příkazu.  Pokud byl příkaz test způsobit ztrátu dat ho nebudou data obnovit.  Například pokud volání StartDataLoss pak volat toto rozhraní API, systém bude pouze vyčistit vnitřní stav spuštění příkazu. Ji nebude možné obnovit cílový oddíl dat, pokud příkaz provedení určité dostatečně daleko způsobit ztrátu dat. 

> [!NOTE]
> Pokud toto rozhraní API se vyvolala s platnost == true, vnitřní stav mohou být zanechány.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id operace [povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  To je předán do odpovídajícího GetProgress rozhraní API. |
| --force | Označuje, zda řádně vrátit zpět a vyčištění stav vnitřní systému upravit spuštěním operace vyvolané uživatele. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-cluster-operation-list"></a>operace clusteru sfctl-list
Získá seznam operací chybu způsobenou uživatele filtrované podle zadaného vstupu.

Získá seznam operací chybu způsobenou uživatele filtrované podle zadaného vstupu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Filtr stavu | Používají k filtrování podle OperationState pro operace vyvolané uživatele. <br> 65535 - Vybrat vše <br> 1 - vybrat spuštění <br> 2 – Výběr vrácení <br>8 – vyberte dokončeno <br>16 – vyberte Faulted <br>32 - vybrat zrušeno <br>64 - vyberte ForceCancelled.  <br>Výchozí\: 65535. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |
| --Filtr typu | Použít k filtrování na typ operace pro uživatele vyvolané operace. <br> 65535 - Vybrat vše <br> 1 - vyberte PartitionDataLoss. <br> 2 – Výběr PartitionQuorumLoss. <br> 4 – vyberte PartitionRestart. <br> 8 – vyberte NodeTransition.  <br> Výchozí\: 65535. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-cluster-provision"></a>zřízení clusteru sfctl
Zřízení balíčky kódu nebo konfigurace clusteru Service Fabric.

Ověření a zřiďte balíčky kódu nebo konfigurace clusteru Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --cluster-manifest-file-path | Cesta k souboru manifestu clusteru. |
| --code-file-path | Cluster kód cesta k souboru balíčku. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-cluster-recover-system"></a>sfctl cluster pro obnovení systému
Do clusteru Service Fabric označuje, že by měl pokusí o zotavení systému služby, které jsou aktuálně zablokované ve ztrátě kvora.

Do clusteru Service Fabric označuje, že by měl pokusí o zotavení systému služby, které jsou aktuálně zablokované ve ztrátě kvora. Tato operace by měla provést pouze v případě je známo, že není možné obnovit repliky, které jsou vypnuté. Nesprávné použití tohoto rozhraní API může způsobit ztrátu dat.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-cluster-report-health"></a>Stav sestavy clusteru sfctl
Odešle zprávu o stavu v clusteru Service Fabric.

Sestava může obsahovat informace o zdroji sestava stavu a na kterém je uvedena vlastnost. Sestava je odeslána do Service Fabric uzel brány, která je směruje k úložišti stavů. Sestava může být přijal brány, ale odmítnuté úložiště stavu po další ověření. Úložiště stavu může třeba odmítnout sestavy z důvodu neplatného parametru, jako jsou zastaralé pořadové číslo. Pokud chcete zjistit, zda byla použita sestava v health store, zkontrolujte, zda je sestava zobrazena v HealthEvents clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Stav – vlastnost [povinné] | Vlastnost s informacemi o stavu. <br><br> Entita může mít sestav o stavu pro různé vlastnosti. Vlastnost je řetězec a oprava výčet umožňují flexibilní reportérka ke kategorizaci podmínku stavu, který spouští sestavu. Například reportérka s ID zdroje "LocalWatchdog" můžete monitorovat stav disku v uzlu, tak ho může hlásit vlastnost "AvailableDisk" v tomto uzlu. Stejné reportérka můžete monitorovat připojení uzlu, takže ho může hlásit vlastnost "Připojení" ve stejném uzlu. V health store tyto sestavy jsou považovány za události samostatné stavu pro zadaný uzel. Spolu s ID zdroje vlastnost jednoznačně identifikuje informací o stavu. |
| – Stav [povinné] | Možné hodnoty zahrnují\: "Neplatný", "Ok", "Upozorňující", "Chyba", "Neznámý". |
| – id zdroje [povinné] | Název zdroje, který identifikuje klienta/sledovacího zařízení/systémové součásti, které vygenerovalo informací o stavu. |
| – Popis | Popis informací o stavu. <br><br> Představuje libovolný text pro přidání lidské čitelné informace o sestavě. Maximální délka řetězce popis je 4096 znaků. Pokud zadaný řetězec je delší, bude automaticky zkrácen. Při zkráceno, poslední znaky popis obsahovat značky "[zkrátila]" a celkový počet řetězec velikost je 4 096 znaků. Výskyt značky určuje uživatelům této zkrácení došlo k chybě. Všimněte si, že při zkráceno, popis má menší než 4096 znaků z původního řetězce. |
| --okamžité | Příznak, který udává, zda sestavy by měl být odesílány okamžitě. <br><br> Sestava stavu posílá do Service Fabric gateway aplikace, který se předává k úložišti stavů. Pokud okamžité nastavená na hodnotu true, zpráva se odešle okamžitě ze brána protokolu HTTP k úložišti stavů, bez ohledu na nastavení klienta fabric, které používá brána aplikace HTTP. To je užitečné pro kritické zprávy, které by měly být odeslány co nejdříve. V závislosti na načasování a jiných podmínek odesílat sestavy může stále nezdaří, pokud bránu HTTP je uzavřený nebo zprávy nelze navázat spojení s bránou. Okamžité je nastavený na hodnotu false, sestava se odesílá podle nastavení stavu klienta ze brána protokolu HTTP. Proto bude možné dávce závislosti na konfiguraci HealthReportSendInterval. Toto je doporučené nastavení, protože umožňuje stavu klienta k optimalizaci zdraví, vytváření sestav zprávy k úložišti stavů, stejně jako zpracování sestavy stavu. Ve výchozím nastavení nejsou odesílány sestavy okamžitě. |
| – vypršela platnost odebrat v případě | Hodnota, která označuje, zda sestava se odebere z health store, když jeho platnost vyprší. <br><br> Je-li nastavena hodnota true, sestava se odebere z health store po vypršení její platnosti. Pokud je nastaven na hodnotu false, sestava je považováno za chybu, pokud vypršela platnost. Hodnota této vlastnosti je ve výchozím nastavení hodnotu false. Když klienti pravidelně hlásit, nastavují by měl RemoveWhenExpired false (výchozí). Tímto způsobem je osoby podávající hlášení dochází k problémům (například zablokování) a nemůže oznamovat entity je vyhodnocena v chybě, když vyprší platnost sestava stavu. Označí entitu jako patřící do chybového stavu. |
| --pořadové číslo | Pořadové číslo pro tuto sestavu stavu jako číselný řetězec. <br><br> Pořadové číslo sestav se používá v úložišti stavů ke zjišťování zastaralých sestavy. Pokud není zadán, je číslo sekvence automaticky generované klientem stavu při přidání sestavy. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |
| --ttl | Doba trvání, pro kterou je tato sestava stavu platná. Toto pole používá formát ISO8601 pro zadání dobu trvání. <br><br> Když klienti pravidelně hlásit, odesílají by zprávy s frekvencí vyšší než hodnota time to live. Pokud klienti nenahlásí na přechod, nastavují time to live nekonečno. Když vyprší čas TTL, událost stavu, který obsahuje informace o stavu je odebrán z health store, pokud je RemoveWhenExpired hodnotu true, nebo vyhodnocovány v chybě, pokud RemoveWhenExpired false. Pokud není zadaný, čas TTL výchozí hodnoty na nekonečnou hodnotu. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-cluster-select"></a>Vyberte cluster sfctl
Se připojí ke koncovému bodu clusteru Service Fabric.

Pokud připojení k zabezpečenému clusteru, zadejte absolutní cestu k certifikátu (CRT) a soubor klíče (.key) nebo jeden soubor s oběma (.pem). Nezadávejte oba. Volitelně Pokud připojení k zabezpečenému clusteru se také zadejte absolutní cestu k souboru sady certifikační Autority nebo adresář certifikátů důvěryhodné certifikační Autority. Pokud používáte adresář certifikáty CA certs `c_rehash <directory>` poskytované OpenSSL musí být nejprve spustit k výpočtu hodnoty hash certifikátu a vytvořit odpovídající symbolické odkazy.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --koncových bodů [povinné] | Adresa URL koncového bodu, včetně portu a protokolu HTTP nebo HTTPS předpony clusteru. |
| --aad | Použití Azure Active Directory pro ověřování. |
| --ca | Absolutní cesta k adresáři certifikáty certifikační Autority bude považovat za platné nebo soubor kompletu certifikační Autority. |
| -cert | Absolutní cesta k souboru klientského certifikátu. |
| --klíč | Absolutní cesta k souboru klíče certifikátu klienta. |
| --no – ověření stavu | Zakázat ověřování certifikátů při použití protokolu HTTPS, mějte na paměti\: to nezabezpečené možnost by se neměl používat pro produkční prostředí. |
| --pem | Absolutní cesta na klientský certifikát jako soubor .pem. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-cluster-show-connection"></a>sfctl cluster show-connection
Zobrazit clusteru Service Fabric, které tato instance sfctl je připojen k.

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-cluster-unprovision"></a>sfctl unprovision clusteru
Zrušení zřízení balíčky kódu nebo konfigurace clusteru Service Fabric.

Podporuje se zrušit zřízení kódu a konfigurace samostatně.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --code-version | Balíček verze kódu clusteru. |
| --config-version | Verze manifestu clusteru. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-cluster-upgrade"></a>upgrade clusteru sfctl
Spuštění upgradu verze kódu nebo konfigurace clusteru Service Fabric.

Ověření upgradu zadaných parametrů a spusťte upgradu verze kódu nebo konfigurace clusteru Service Fabric, pokud jsou platné parametry.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --app-health-map | JSON kódovaný slovník párů název aplikace a maximální procento před vyvoláním chyby není v pořádku. |
| --app-type-health-map | JSON kódovaný slovník párů název typu aplikace a maximální procento před vyvoláním chyby není v pořádku. |
| --code-version | Verze kódu clusteru. |
| --config-version | Verze konfigurace clusteru. |
| --delta-health-evaluation | Umožňuje vyhodnocování stavu rozdílové namísto vyhodnocování absolutní stavu po dokončení každého upgradovací doméně. |
| --delta poškozené uzly | Maximální povolené procento uzlů snížení stav povoleno během upgradu clusteru.  Výchozí\: 10. <br><br> Delta měří stav uzlů na začátku upgradu a stav uzlů v době vyhodnocení stavu. Kontrola se provádí po dokončení upgradu každý upgradovací doména k Ujistěte se, že globální stav clusteru je v mezích limitů přípustný. |
| --selhání akce | Možné hodnoty zahrnují\: "Neplatný", "Vrácení zpět", "Ruční". |
| --force restartování | Procesy se během upgradu vynuceně restartují, i v případě, že nedošlo ke změně verze kódu. <br><br> Upgrade pouze změny konfigurace nebo data. |
| --health-check-retry | Dlouhá doba mezi pokusy o provádění kontroly stavu, pokud aplikace nebo clusteru není v pořádku. |
| --stabilní verze stavu zaškrtnutí | Množství času, aplikace nebo clusteru musí zůstane v dobrém stavu před provedením upgradu k další upgradovací doméně. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepodaří, je interpretován jako číslo představující počet milisekund. |
| --health-check-wait | Doba čekání po dokončení upgradu domény před zahájením stavu kontroluje procesu. |
| --replica-set-check-timeout | Maximální množství času blokování zpracování logických sítí a zabránit ztrátě dostupnosti, když dochází k neočekávaným problémům. <br><br> Když tento časový limit vyprší platnost, zpracování upgradovací doména bude pokračovat bez ohledu na problémy s dostupností ztráty. Časový limit se resetuje na začátku každé upgradovací doméně. Platné hodnoty jsou 0 až 42949672925 (včetně). |
| --rolling-upgrade-mode | Možné hodnoty zahrnují\: "Neplatný", "UnmonitoredAuto", "UnmonitoredManual", "Sledované".  Výchozí\: UnmonitoredAuto. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |
| --unhealthy-applications | Maximální povolené procento žádostí, není v pořádku, před ohlášení chyby. <br><br> Například pokud chcete povolit 10 % aplikací v dobrém stavu, tato hodnota by 10. Procento představuje přípustný maximální procento aplikací, které může být není v pořádku, než clusteru se považuje za chybu. Pokud procento respektován, ale existuje alespoň jedna aplikace není v pořádku, stav se vyhodnotí jako upozornění. To se vypočítá jako podíl počtu poškozené aplikace přes celkový počet instancí aplikace v clusteru, s výjimkou aplikací typy aplikací, které jsou součástí ApplicationTypeHealthPolicyMap. Výpočet zaokrouhlí tento údaj tolerovat selhání jednoho na malý počet aplikací. |
| --poškozené uzly | Maximální povolené procento poškozené uzly před ohlášení chyby. <br><br> Například pokud chcete povolit 10 % uzly v dobrém stavu, tato hodnota by 10. Procento představuje přípustný maximální procento uzlů, které může být není v pořádku, než clusteru se považuje za chybu. Pokud procento respektován, ale existuje nejméně jeden uzel není v pořádku, stav se vyhodnotí jako upozornění. Procentuální se vypočítá jako podíl počtu uzlů v pořádku za celkový počet uzlů v clusteru. Výpočet zaokrouhlí tento údaj tolerovat selhání jednoho na malý počet uzlů. Ve velkých clusterech některé uzly bude vždy dolů nebo navýšení kapacity pro opravy, tak tolerovat, který by měl být nakonfigurovaný toto procento. |
| --upgrade-domain-delta-unhealthy-nodes | Maximální povolené procento uzlů upgradovací domény snížení stav povoleno během upgradu clusteru.  Výchozí\: 15. <br><br> Delta měří stav uzlů upgradovací domény na začátku upgradu a stav uzlů upgradovací doména v době vyhodnocení stavu. Kontrola se provádí po dokončení upgradu všech upgradovací doména pro všechny dokončení upgradovacích domén, abyste měli jistotu, že je stav upgradovacích domén v rámci přípustný omezení. |
| --upgrade-domain-timeout | Množství času každé domény upgradu musí dokončit před provedením FailureAction. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepodaří, je interpretován jako číslo představující počet milisekund. |
| --upgrade-timeout | Množství času celkové upgrade musí dokončit před provedením FailureAction. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepodaří, je interpretován jako číslo představující počet milisekund. |
| --warning-as-error | Určuje, zda jsou upozornění zpracována stejným závažností jako chyby. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-cluster-upgrade-resume"></a>upgrade clusteru sfctl-obnovit
Ujistěte se, přejít k další upgradovací doméně upgradu clusteru.

Přechodem clusteru kódu nebo konfigurace upgradu na k další upgradovací doméně v případě potřeby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --upgrade domény [povinné] | Další upgradovací doméně pro tento upgrade clusteru. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-cluster-upgrade-rollback"></a>upgrade clusteru sfctl – vrácení
Upgrade clusteru Service Fabric se vrátit zpět.

Vrácení kódu nebo konfigurace upgrade clusteru Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-cluster-upgrade-status"></a>upgrade clusteru sfctl-status
Získá průběh aktuální upgradu clusteru.

Získá aktuální průběh upgradu probíhající clusteru. Pokud právě probíhá žádný upgrade, získáte poslední stav předchozí upgradu clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-cluster-upgrade-update"></a>upgrade clusteru sfctl – aktualizace
Aktualizujte parametry upgradu upgrade clusteru Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --app-health-map | JSON kódovaný slovník párů název aplikace a maximální procento před vyvoláním chyby není v pořádku. |
| --app-type-health-map | JSON kódovaný slovník párů název typu aplikace a maximální procento před vyvoláním chyby není v pořádku. |
| --delta-health-evaluation | Umožňuje vyhodnocování stavu rozdílové namísto vyhodnocování absolutní stavu po dokončení každého upgradovací doméně. |
| --delta poškozené uzly | Maximální povolené procento uzlů snížení stav povoleno během upgradu clusteru.  Výchozí\: 10. <br><br> Delta měří stav uzlů na začátku upgradu a stav uzlů v době vyhodnocení stavu. Kontrola se provádí po dokončení upgradu každý upgradovací doména k Ujistěte se, že globální stav clusteru je v mezích limitů přípustný. |
| --selhání akce | Možné hodnoty zahrnují\: "Neplatný", "Vrácení zpět", "Ruční". |
| --force restartování | Procesy se během upgradu vynuceně restartují, i v případě, že nedošlo ke změně verze kódu. <br><br> Upgrade pouze změny konfigurace nebo data. |
| --health-check-retry | Dlouhá doba mezi pokusy o provádění kontroly stavu, pokud aplikace nebo clusteru není v pořádku. |
| --stabilní verze stavu zaškrtnutí | Množství času, aplikace nebo clusteru musí zůstane v dobrém stavu před provedením upgradu k další upgradovací doméně. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepodaří, je interpretován jako číslo představující počet milisekund. |
| --health-check-wait | Doba čekání po dokončení upgradu domény před zahájením stavu kontroluje procesu. |
| --replica-set-check-timeout | Maximální množství času blokování zpracování logických sítí a zabránit ztrátě dostupnosti, když dochází k neočekávaným problémům. <br><br> Když tento časový limit vyprší platnost, zpracování upgradovací doména bude pokračovat bez ohledu na problémy s dostupností ztráty. Časový limit se resetuje na začátku každé upgradovací doméně. Platné hodnoty jsou 0 až 42949672925 (včetně). |
| --rolling-upgrade-mode | Možné hodnoty zahrnují\: "Neplatný", "UnmonitoredAuto", "UnmonitoredManual", "Sledované".  Výchozí\: UnmonitoredAuto. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |
| --unhealthy-applications | Maximální povolené procento žádostí, není v pořádku, před ohlášení chyby. <br><br> Například pokud chcete povolit 10 % aplikací v dobrém stavu, tato hodnota by 10. Procento představuje přípustný maximální procento aplikací, které může být není v pořádku, než clusteru se považuje za chybu. Pokud procento respektován, ale existuje alespoň jedna aplikace není v pořádku, stav se vyhodnotí jako upozornění. To se vypočítá jako podíl počtu poškozené aplikace přes celkový počet instancí aplikace v clusteru, s výjimkou aplikací typy aplikací, které jsou součástí ApplicationTypeHealthPolicyMap. Výpočet zaokrouhlí tento údaj tolerovat selhání jednoho na malý počet aplikací. |
| --poškozené uzly | Maximální povolené procento poškozené uzly před ohlášení chyby. <br><br> Například pokud chcete povolit 10 % uzly v dobrém stavu, tato hodnota by 10. Procento představuje přípustný maximální procento uzlů, které může být není v pořádku, než clusteru se považuje za chybu. Pokud procento respektován, ale existuje nejméně jeden uzel není v pořádku, stav se vyhodnotí jako upozornění. Procentuální se vypočítá jako podíl počtu uzlů v pořádku za celkový počet uzlů v clusteru. Výpočet zaokrouhlí tento údaj tolerovat selhání jednoho na malý počet uzlů. Ve velkých clusterech některé uzly bude vždy dolů nebo navýšení kapacity pro opravy, tak tolerovat, který by měl být nakonfigurovaný toto procento. |
| --upgrade-domain-delta-unhealthy-nodes | Maximální povolené procento uzlů upgradovací domény snížení stav povoleno během upgradu clusteru.  Výchozí\: 15. <br><br> Delta měří stav uzlů upgradovací domény na začátku upgradu a stav uzlů upgradovací doména v době vyhodnocení stavu. Kontrola se provádí po dokončení upgradu všech upgradovací doména pro všechny dokončení upgradovacích domén, abyste měli jistotu, že je stav upgradovacích domén v rámci přípustný omezení. |
| --upgrade-domain-timeout | Množství času každé domény upgradu musí dokončit před provedením FailureAction. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepodaří, je interpretován jako číslo představující počet milisekund. |
| --upgrade – typ | Možné hodnoty zahrnují\: "Neplatný", "Se zajištěním provozu", "Rolling_ForceRestart".  Výchozí\: se zajištěním provozu. |
| --upgrade-timeout | Množství času celkové upgrade musí dokončit před provedením FailureAction. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepodaří, je interpretován jako číslo představující počet milisekund. |
| --warning-as-error | Určuje, zda jsou upozornění zpracována stejným závažností jako chyby. |

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