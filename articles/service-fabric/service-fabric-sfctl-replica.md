---
title: Azure Service Fabric rozhraní příkazového řádku - sfctl repliky | Microsoft Docs
description: Popisuje příkazy Service Fabric rozhraní příkazového řádku sfctl repliky.
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
ms.openlocfilehash: cd09fe906f77bb06f0ac7afaa6c6cce326dbfa5c
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763574"
---
# <a name="sfctl-replica"></a>sfctl replica
Spravujte repliky, které patří do oddílů služby.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| Nasazení | Získá podrobnosti repliky nasazené na uzlu Service Fabric. |
| nasazení seznamu | Získá seznam replik, které jsou nasazeny na uzlu Service Fabric. |
| stav | Získá stav repliky stavové služby Service Fabric nebo instance bezstavové služby. |
| informace | Získá informace o repliku oddílu Service Fabric. |
| Seznam | Získá informace o replikách oddílu služby Service Fabric. |
| odebrat | Odebere repliku služby spuštěné v uzlu. |
| Sestava stavu | Odešle zprávu o stavu v replice Service Fabric. |
| Restartování | Restartuje službu repliku trvalou služby spuštěné na uzlu. |

## <a name="sfctl-replica-deployed"></a>repliky sfctl nasazení
Získá podrobnosti repliky nasazené na uzlu Service Fabric.

Získá podrobnosti repliky nasazené na uzlu Service Fabric. Informace zahrnují typ služby, název služby, aktuální operace služby, aktuální operace služby spuštění datum a čas, ID oddílu, ID repliky nebo instanci, hlášené zatížení a další informace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název uzlu [vyžaduje] | Název uzlu. |
| – id oddílu [vyžaduje] | Identita oddílu. |
| – id repliky [vyžaduje] | Identifikátor repliky. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-replica-deployed-list"></a>nasazení repliky sfctl – seznam
Získá seznam replik, které jsou nasazeny na uzlu Service Fabric.

Získá seznam obsahující informace o replikách, které jsou nasazeny na uzlu Service Fabric. Informace zahrnují ID oddílu, ID repliky, stav repliky, název služby, název typu služby a další informace. ID oddílu nebo ServiceManifestName parametry dotazu použijte k vrácení informací o nasazených repliky odpovídající zadané hodnoty pro tyto parametry.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [vyžaduje] | Identita aplikace. To je obvykle úplný název aplikace bez ' fabric\:' schéma identifikátoru URI. Od verze 6.0, hierarchické oddělených středníky s "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", identita aplikace by být "Moje aplikace\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| – Název uzlu [vyžaduje] | Název uzlu. |
| – id oddílu | Identita oddílu. |
| – název manifestu služby | Název služby manifestu zaregistrován v rámci typ aplikace v clusteru Service Fabric. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-replica-health"></a>sfctl repliky stavu
Získá stav repliky stavové služby Service Fabric nebo instance bezstavové služby.

Získá stav repliky Service Fabric. EventsHealthStateFilter použijte k filtrování kolekce události stavu ohlášeny repliky podle stavu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [vyžaduje] | Identita oddílu. |
| – id repliky [vyžaduje] | Identifikátor repliky. |
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

## <a name="sfctl-replica-info"></a>informace o sfctl repliky
Získá informace o repliku oddílu Service Fabric.

Odpověď obsahuje ID, role, stav, stav, název uzlu, provozu a další podrobnosti o repliky.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [vyžaduje] | Identita oddílu. |
| – id repliky [vyžaduje] | Identifikátor repliky. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-replica-list"></a>seznam replik sfctl
Získá informace o replikách oddílu služby Service Fabric.

Koncový bod GetReplicas vrátí informace o replik zadaný oddíl. Odpověď obsahuje ID, role, stav, stav, název uzlu, provozu a další podrobnosti o repliky.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [vyžaduje] | Identita oddílu. |
| --token pokračování | Parametr token pokračování slouží k získání další sadu výsledků. Token pokračování s hodnotou neprázdný je zahrnutý v odpovědi rozhraní API, když výsledky ze systému nelze uložit do odpověď o jedné. Pokud je tato hodnota předaná pro další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud nejsou žádné další výsledky, pak token pro pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódovaná adresou URL. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-replica-remove"></a>odebrat sfctl repliky
Odebere repliku služby spuštěné v uzlu.

Toto rozhraní API simuluje selhání Service Fabric repliky odstranit repliku z clusteru Service Fabric. Odebrání zavře repliky, přejde repliky na roli, None a potom odebere všechny informace o stavu repliky z clusteru. Toto rozhraní API testuje cesty k replice stavu odebrání a simuluje trvalé cesta sestavy odolnost prostřednictvím rozhraní API klienta. Upozornění: zde nejsou žádné kontroly zabezpečení se provedou, když se používá toto rozhraní API. Nesprávné použití toto rozhraní API může způsobit ztrátu dat pro stavové služby. Kromě toho příznak forceRemove ovlivňuje všechny repliky hostované v rámci jednoho procesu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název uzlu [vyžaduje] | Název uzlu. |
| – id oddílu [vyžaduje] | Identita oddílu. |
| – id repliky [vyžaduje] | Identifikátor repliky. |
| --remove silou | Odebrání aplikace Service Fabric nebo služby vynuceně bez průchodu přes pořadí řádné vypnutí. Tento parametr slouží k vynuceně odstranit aplikace nebo služby, pro které odstranění je řádně vypršení časového limitu z důvodu problémů v kódu služby, která zabraňuje ukončení repliky. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-replica-report-health"></a>repliky sfctl sestavy stavu
Odešle zprávu o stavu v replice Service Fabric.

Sestavy stavu zadaná replika Service Fabric. Sestava musí obsahovat informace o zdroji této sestavy stavu a vlastnosti, na kterém je zaznamenána. Sestava je odeslána do Service Fabric brány repliky, který předává do úložiště stavu. Sestava může přijal bránou, ale byl odmítnut úložiště zdravotní po doplňující ověření. Úložiště stavu může například odmítnout sestavy z důvodu neplatný parametr, jako je zastaralé pořadové číslo. Pokud chcete zjistit, zda sestava byla použita v health store, zkontrolujte, že sestava se zobrazí v části události.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --stavu – vlastnost [vyžaduje] | Vlastnost s informacemi o stavu. <br><br> Entita může mít sestavy stavu pro různé vlastnosti. Vlastnost je řetězec a pevnou výčtu umožňuje flexibilitu ohlašování zařadit do kategorií stavu podmínku, která aktivuje sestavy. Ohlašování s ID zdroje "LocalWatchdog" například můžete monitorovat stav je k dispozici disk na uzlu, takže ho mohou zasílat zprávy o "AvailableDisk" vlastnost v tomto uzlu. Stejné ohlašování můžete monitorovat připojení k uzlu, takže ho mohou zasílat zprávy o vlastnost "Připojení" ve stejném uzlu. V health store tyto sestavy jsou považovány za samostatné stavu události pro určeného uzlu. Společně s ID zdroje vlastnost jednoznačně identifikuje informace o stavu. |
| --stavu [vyžaduje] | Možné hodnoty patří\: 'Neplatný', 'Ok', 'Upozornění', "Chyba", "Neznámý". |
| – id oddílu [vyžaduje] | Identita oddílu. |
| – id repliky [vyžaduje] | Identita oddílu. |
| – id zdroje [vyžaduje] | Název zdroje, který identifikuje součásti klienta nebo sledovací zařízení nebo systému, která generuje informace o stavu. |
| – Popis | Popis informací o stavu. <br><br> Reprezentuje textové použít k přidání lidského čtení informací o sestavě. Maximální délka řetězce pro popis je 4 096 znaků. Pokud zadaný text je delší, bude automaticky zkrácen. Pokud zkrácen, poslední znaky popis obsahovat značku "[Truncated]" a velikost celkový řetězce je 4 096 znaků. Přítomnost značky naznačuje pro uživatele, že zkrácení došlo k chybě. Upozorňujeme, že pokud zkrácen, popis má méně než 4 096 znaků z původního řetězce. |
| --okamžitou | Příznak, který indikuje, zda mají být sestavy odesílány okamžitě. <br><br> Sestava stavu posílá bránu Service Fabric aplikace, který předává do úložiště stavu. Pokud Immediate je nastaven na hodnotu true, zpráva se odešle okamžitě z brány protokolu HTTP k úložišti stavů, bez ohledu na nastavení klienta fabric, které používá aplikace brány protokolu HTTP. To je užitečné pro kritické sestavy, které by měly být odeslány co nejdříve. V závislosti na načasování a jiných podmínek odesílat sestavy může stále selžou, například pokud je uzavřen bráně HTTP nebo zprávy není kontaktovat bránu. Pokud Immediate nastavena na hodnotu false, sestavy se odesílá podle nastavení stavu klienta z brány v protokolu HTTP. Proto se bude zpracovat v dávce závislosti na konfiguraci HealthReportSendInterval. Toto je doporučená nastavení, protože umožňuje stavu klienta k optimalizaci zpráv, které mají stav úložiště, jakož i zpracování sestavy stavu vykazování stavu. Ve výchozím nastavení nejsou odesílány okamžitě sestavy. |
| --odebrat Pokud platnost | Hodnota, která určuje, zda sestava je po jeho vypršení odebrat z health store. <br><br> Pokud je nastaven na hodnotu true, sestavy se odebere z health store po vypršení platnosti. Pokud je nastaven na hodnotu false, sestava je považovat za chybu, pokud vypršela platnost. Hodnota této vlastnosti je ve výchozím nastavení hodnota false. Když klienti pravidelně zprávy, nastavují by měl RemoveWhenExpired NEPRAVDA (výchozí). Tímto způsobem je zpravodaje, která se vyskytují problémy (například zablokování) a nemůžete hlásit entity je vyhodnocován v chybě, když vyprší platnost sestava stavu. Označí entitu, že je ve stavu chyby příznakem. |
| --pořadové číslo | Pořadové číslo pro tuto sestavu stavu jako číselných řetězců. <br><br> Pořadové číslo sestavy se používají ve store stavu ke zjišťování zastaralých sestavy. Pokud není zadáno, je číslo sekvence automaticky generovaný klientem stavu při přidání sestavy. |
| – Typ služby | Typ služby repliky (bezstavové nebo stateful), pro kterou je hlásí stav. Toto jsou možné hodnoty\: 'Bezstavové', 'Stateful'.  Výchozí\: Stateful. |
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

## <a name="sfctl-replica-restart"></a>sfctl repliky restartování
Restartuje službu repliku trvalou služby spuštěné na uzlu.

Restartuje službu repliku trvalou služby spuštěné na uzlu. Upozornění: zde nejsou žádné kontroly zabezpečení se provedou, když se používá toto rozhraní API. Nesprávné použití toto rozhraní API může způsobit ztrátu dostupnosti pro stavová služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Název uzlu [vyžaduje] | Název uzlu. |
| – id oddílu [vyžaduje] | Identita oddílu. |
| – id repliky [vyžaduje] | Identifikátor repliky. |
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
