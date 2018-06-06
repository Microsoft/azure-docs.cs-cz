---
title: Azure Service Fabric rozhraní příkazového řádku sfctl uzlem | Microsoft Docs
description: Popisuje příkazy Service Fabric rozhraní příkazového řádku sfctl uzlu.
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
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: fb8a310a131938e95f3d21b3962dbbd1944a57ed
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763421"
---
# <a name="sfctl-node"></a>sfctl node
Spravujte uzly, které vytvoří cluster.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| zakázat | Deaktivace uzlu clusteru Service Fabric se záměrem zadaný deaktivace. |
| povolit | Aktivujte uzel clusteru Service Fabric, který je právě deaktivována. |
| stav | Získá stav uzlu Service Fabric. |
| informace | Získá informace o konkrétním uzlu v clusteru Service Fabric. |
| Seznam | Získá seznam uzlů v clusteru Service Fabric. |
| načítání | Získá informace o zatížení Service Fabric uzlu. |
| odebrat stavu | Service Fabric upozorní, že trvalého stavu na uzlu trvale odstraněn nebo ztráty. |
| Sestava stavu | Odešle zprávu o stavu na uzlu Service Fabric. |
| Restartování | Restartování uzlu clusteru Service Fabric. |
| Přechod | Spuštění nebo zastavení uzlu clusteru. |
| Přechod stavu | Získá průběh operace pomocí StartNodeTransition spuštěna. |

## <a name="sfctl-node-disable"></a>zakázat sfctl uzlu
Deaktivace uzlu clusteru Service Fabric se záměrem zadaný deaktivace.

Deaktivace uzlu clusteru Service Fabric se záměrem zadaný deaktivace. Po deaktivaci v průběhu záměr deaktivace může být vyšší, ale není snížena (například uzel, který je deaktivována se záměrem pozastavení může být deaktivované další s Restart, ale ne naopak. Uzly může znovu aktivovat pomocí aktivace operaci uzlu kdykoli po jejich deaktivováno. Pokud deaktivaci není kompletní, tato akce zruší deaktivaci. Uzel, který přestane fungovat a zobrazí zpět při deaktivováno bude stále potřeba znovu aktivovat před služby budou umístěny v tomto uzlu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název uzlu [vyžaduje] | Název uzlu. |
| --záměr deaktivace | Popisuje záměr nebo důvod deaktivace uzlu. Možné hodnoty jsou následující. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-node-enable"></a>Povolit sfctl uzlu
Aktivujte uzel clusteru Service Fabric, který je právě deaktivována.

Aktivuje uzel clusteru Service Fabric, který je právě deaktivována. Po aktivaci uzlu se znovu stane přijatelná cíle pro umístění nové repliky, a bude znovu aktivovat, všechny deaktivované repliky zbývající na uzlu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název uzlu [vyžaduje] | Název uzlu. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-node-health"></a>stav uzlu sfctl
Získá stav uzlu Service Fabric.

Získá stav uzlu Service Fabric. EventsHealthStateFilter použijte k filtrování kolekce události stavu ohlášeny uzel podle stavu. Pokud uzel, který určíte podle názvu v health store neexistuje, tento příkaz vrátí chybu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název uzlu [vyžaduje] | Název uzlu. |
| --události stavu stavu filtru | Umožňuje filtrování vrácených objektů HealthEvent kolekce na základě stavu. Možné hodnoty pro tento parametr patří celočíselná hodnota jednoho z následujících stavů. Se vrátí jenom události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadaný, jsou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnotou může být kombinací tyto hodnoty získané pomocí bitový operátor 'OR'. Například pokud zadaná hodnota je 6 všechny události s hodnotou elementu HealthState OK (2) a upozornění (4), jsou vráceny.  <br> -Výchozí – výchozí hodnota. Vyhledá všechny stav HealthState. Hodnota je nula.  <br> -None - filtr, který se neshoduje se žádnou hodnotu stavu HealthState. Použít cílem vrátit žádné výsledky v dané kolekci stavů. Hodnota je 1.  <br> -Ok - filtrujte, aby odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Upozornění - filtr, hodnota odpovídá vstup k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který odpovídá vstup s hodnotou elementu HealthState chyby. Hodnota je 8.  <br> -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-node-info"></a>informace o sfctl uzlu
Získá informace o konkrétním uzlu v clusteru Service Fabric.

Získá informace o konkrétním uzlu v clusteru Service Fabric. Odpověď obsahuje název, stav, ID, stavu, provozu a další podrobnosti o příslušném uzlu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název uzlu [vyžaduje] | Název uzlu. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-node-list"></a>seznam uzlů sfctl
Získá seznam uzlů v clusteru Service Fabric.

Získá seznam uzlů v clusteru Service Fabric. Odpověď obsahuje název, stav, ID, stavu, provozu a další podrobnosti o příslušném uzlu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --token pokračování | Parametr token pokračování slouží k získání další sadu výsledků. Token pokračování s hodnotou neprázdný je zahrnutý v odpovědi rozhraní API, když výsledky ze systému nelze uložit do odpověď o jedné. Pokud je tato hodnota předaná pro další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud nejsou žádné další výsledky, pak token pro pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódovaná adresou URL. |
| --uzlu filtru stavu | Umožňuje filtrování podle NodeStatus uzly. Pouze uzly, které jsou odpovídající hodnotě zadaný filtr, bude vrácen. Hodnota filtru může být jedna z následujících akcí.  Výchozí\: výchozí. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-node-load"></a>sfctl uzlu zatížení
Získá informace o zatížení Service Fabric uzlu.

Načte informace zatížení Service Fabric uzlu pro všechny metriky, které mají zatížení nebo kapacity definované.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název uzlu [vyžaduje] | Název uzlu. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-node-remove-state"></a>sfctl remove stav uzlu
Service Fabric upozorní, že trvalého stavu na uzlu trvale odstraněn nebo ztráty.

Service Fabric upozorní, že trvalého stavu na uzlu trvale odstraněn nebo ztráty.  To znamená, že není možné obnovit trvalý stav tohoto uzlu. K tomu obvykle dochází, pokud pevném disku bylo vymazáno čisté, nebo pokud dojde k chybě na pevný disk. Uzel musí být mimo provoz pro tato operace proběhla úspěšně. Tato operace umožňuje Service Fabric repliky na tomto uzlu již neexistuje, a že Service Fabric by se měla zastavit čeká na tyto repliky spolu zpět. Tato rutina nespustí, pokud stav na uzlu není odebraná a uzlu můžete spolu zpět stav beze změn.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název uzlu [vyžaduje] | Název uzlu. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-node-report-health"></a>uzel sfctl sestavy stavu
Odešle zprávu o stavu na uzlu Service Fabric.

Sestava stavu určeného uzlu Service Fabric. Sestava musí obsahovat informace o zdroji této sestavy stavu a vlastnosti, na kterém je zaznamenána. Sestava je odeslána do Service Fabric uzlu brány, který předává do úložiště stavu. Sestava může přijal bránou, ale byl odmítnut úložiště zdravotní po doplňující ověření. Úložiště stavu může například odmítnout sestavy z důvodu neplatný parametr, jako je zastaralé pořadové číslo. Pokud chcete zjistit, zda sestava byla použita v health store, zkontrolujte, že sestava se zobrazí v části HealthEvents.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --stavu – vlastnost [vyžaduje] | Vlastnost s informacemi o stavu. <br><br> Entita může mít sestavy stavu pro různé vlastnosti. Vlastnost je řetězec a pevnou výčtu umožňuje flexibilitu ohlašování zařadit do kategorií stavu podmínku, která aktivuje sestavy. Ohlašování s ID zdroje "LocalWatchdog" například můžete monitorovat stav je k dispozici disk na uzlu, takže ho mohou zasílat zprávy o "AvailableDisk" vlastnost v tomto uzlu. Stejné ohlašování můžete monitorovat připojení k uzlu, takže ho mohou zasílat zprávy o vlastnost "Připojení" ve stejném uzlu. V health store tyto sestavy jsou považovány za samostatné stavu události pro určeného uzlu. Společně s ID zdroje vlastnost jednoznačně identifikuje informace o stavu. |
| --stavu [vyžaduje] | Možné hodnoty patří\: 'Neplatný', 'Ok', 'Upozornění', "Chyba", "Neznámý". |
| – Název uzlu [vyžaduje] | Název uzlu do sestavy. |
| – id zdroje [vyžaduje] | Název zdroje, který identifikuje součásti klienta nebo sledovací zařízení nebo systému, která generuje informace o stavu. |
| – Popis | Popis informací o stavu. <br><br> Reprezentuje textové použít k přidání lidského čtení informací o sestavě. Maximální délka řetězce pro popis je 4 096 znaků. Pokud zadaný text je delší, bude automaticky zkrácen. Pokud zkrácen, poslední znaky popis obsahovat značku "[Truncated]" a velikost celkový řetězce je 4 096 znaků. Přítomnost značky naznačuje pro uživatele, že zkrácení došlo k chybě. Upozorňujeme, že pokud zkrácen, popis má méně než 4 096 znaků z původního řetězce. |
| --okamžitou | Příznak, který indikuje, zda mají být sestavy odesílány okamžitě. <br><br> Sestava stavu posílá bránu Service Fabric aplikace, který předává do úložiště stavu. Pokud Immediate je nastaven na hodnotu true, zpráva se odešle okamžitě z brány protokolu HTTP k úložišti stavů, bez ohledu na nastavení klienta fabric, které používá aplikace brány protokolu HTTP. To je užitečné pro kritické sestavy, které by měly být odeslány co nejdříve. V závislosti na načasování a jiných podmínek odesílat sestavy může stále selžou, například pokud je uzavřen bráně HTTP nebo zprávy není kontaktovat bránu. Pokud Immediate nastavena na hodnotu false, sestavy se odesílá podle nastavení stavu klienta z brány v protokolu HTTP. Proto se bude zpracovat v dávce závislosti na konfiguraci HealthReportSendInterval. Toto je doporučená nastavení, protože umožňuje stavu klienta k optimalizaci zpráv, které mají stav úložiště, jakož i zpracování sestavy stavu vykazování stavu. Ve výchozím nastavení nejsou odesílány okamžitě sestavy. |
| --odebrat Pokud platnost | Hodnota, která určuje, zda sestava je po jeho vypršení odebrat z health store. <br><br> Pokud je nastaven na hodnotu true, sestavy se odebere z health store po vypršení platnosti. Pokud je nastaven na hodnotu false, sestava je považovat za chybu, pokud vypršela platnost. Hodnota této vlastnosti je ve výchozím nastavení hodnota false. Když klienti pravidelně zprávy, nastavují by měl RemoveWhenExpired NEPRAVDA (výchozí). Tímto způsobem je zpravodaje, která se vyskytují problémy (například zablokování) a nemůžete hlásit entity je vyhodnocován v chybě, když vyprší platnost sestava stavu. Označí entitu, že je ve stavu chyby příznakem. |
| --pořadové číslo | Pořadové číslo pro tuto sestavu stavu jako číselných řetězců. <br><br> Pořadové číslo sestavy se používají ve store stavu ke zjišťování zastaralých sestavy. Pokud není zadáno, je číslo sekvence automaticky generovaný klientem stavu při přidání sestavy. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |
| Hodnota ttl-- | Doba trvání, pro který je tato sestava stavu platný. Toto pole pro zadání trvání používá ve formátu ISO 8601. <br><br> Když klienti pravidelně zprávy, se musí odesílat zprávy s četností vyšší než hodnota time to live. Pokud klienti zprávy na přechod, nastavují TTL k nekonečné. Když vyprší platnost hodnota time to live, událost stavu, který obsahuje informace o stavu je buď odebrán z health store, pokud je RemoveWhenExpired hodnotu true, nebo vyhodnocen na chyby, pokud RemoveWhenExpired false. Pokud není zadaný, hodnota time to live výchozí hodnoty na nekonečnou hodnotu. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-node-restart"></a>restartování uzlu sfctl
Restartování uzlu clusteru Service Fabric.

Restartování uzlu clusteru Service Fabric, která je již spuštěn.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název uzlu [vyžaduje] | Název uzlu. |
| – Vytvoření výpisu prostředků infrastruktury | Zadejte hodnotu PRAVDA, chcete-li vytvořit výpis procesu uzlu prostředků infrastruktury. Toto je malá a velká písmena.  Výchozí\: False. |
| – id instance uzlu | ID instance cílový uzel. Pokud je zadané instance ID restartování uzlu pouze v případě, že se shoduje s aktuální instancí třídy uzlu. Výchozí hodnota 0, odpovídá žádné ID instance. Instance ID lze získat pomocí get uzlu dotazu.  Výchozí\: 0. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-node-transition"></a>Přechod sfctl uzlu
Spuštění nebo zastavení uzlu clusteru.

Spuštění nebo zastavení uzlu clusteru.  Uzel clusteru je proces, není instanci OS sám sebe.  Pro spuštění uzlu, předejte "Start" pro parametr NodeTransitionType. K zastavení uzlu, předejte "Stop" pro parametr NodeTransitionType. Toto rozhraní API spustí operaci – když rozhraní API vrátí že uzlu nemusí dokončení přechodu ještě. Volání GetNodeTransitionProgress s stejné OperationId zobrazíte průběh operace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --– instance-id uzlu [vyžaduje] | ID instance uzlu cílový uzel. To se dá určit prostřednictvím rozhraní API GetNodeInfo. |
| – Název uzlu [vyžaduje] | Název uzlu. |
| --uzlu přechod type [vyžaduje] | Určuje typ přechodu k provedení.  NodeTransitionType.Start začne zastaven uzlu. NodeTransitionType.Stop zastaví uzel, který je v provozu. |
| – id operace [vyžaduje] | Identifikátor GUID, který identifikuje volání toto rozhraní API.  To je předán do odpovídající GetProgress API. |
| --stop-doba trvání v sekund, které [vyžadují] | Doba trvání v sekundách, aby uzlu zastavena.  Minimální hodnota je 600, maximální se 14400.  Po vypršení této doby uzlu bude automaticky spuštěna znovu. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-node-transition-status"></a>uzel sfctl přechod – stav
Získá průběh operace pomocí StartNodeTransition spuštěna.

Získá průběh operace začít s StartNodeTransition pomocí zadané OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název uzlu [vyžaduje] | Název uzlu. |
| – id operace [vyžaduje] | Identifikátor GUID, který identifikuje volání toto rozhraní API.  To je předán do odpovídající GetProgress API. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="next-steps"></a>Další postup
- [Instalační program](service-fabric-cli.md) Service Fabric rozhraní příkazového řádku.
- Další informace o použití pomocí Service Fabric rozhraní příkazového řádku [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).