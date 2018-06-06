---
title: Azure Service Fabric rozhraní příkazového řádku - sfctl cluster | Microsoft Docs
description: Popisuje příkazy sfctl clusteru Service Fabric rozhraní příkazového řádku.
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
ms.openlocfilehash: 60f3f74778f0fb32677c3b87b3140131ccd37bea
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763625"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Vyberte, Správa a provoz clusterů Service Fabric.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| verze kódu | Získá seznam prostředků infrastruktury verze kódu, které jsou zřízené v clusteru Service Fabric. |
| Konfigurace verze | Získá seznam prostředků infrastruktury verze konfigurace, které jsou zřízené v clusteru Service Fabric. |
| stav | Získá stav clusteru Service Fabric. |
| Manifest | Získáte manifestu clusteru Service Fabric. |
| operace zrušení | Selhání vyvolané uživatel operaci zruší. |
| seznam operací | Získá seznam operací vyvolané uživatele selhání filtrovaná podle zadaného vstupu. |
| Zřizování | Zřídit balíčky kód nebo konfigurace clusteru Service Fabric. |
| obnovení systému | Do clusteru Service Fabric označuje, že mají pokusit o obnovení systémových služeb, které jsou aktuálně zablokované ve ztrátě kvora. |
| Sestava stavu | Odešle zprávu o stavu na cluster Service Fabric. |
| vybrat | Připojí se ke clusteru koncový bod Service Fabric. |
| Zrušení zajišťování | Zrušit zřízení balíčky kód nebo konfigurace clusteru Service Fabric. |
| upgrade | Spusťte upgrade verze kódu nebo konfigurace clusteru Service Fabric. |
| obnovení upgradu | Proveďte upgrade clusteru přejde k další upgradovací doméně. |
| vrácení upgradu | Upgrade clusteru Service Fabric se vrátit zpět. |
| Stav upgradu | Získá průběh aktuální upgrade clusteru. |
| upgrade aktualizace | Aktualizujte upgradu parametry upgradu clusteru Service Fabric. |

## <a name="sfctl-cluster-code-versions"></a>sfctl clusteru kódu – verze
Získá seznam prostředků infrastruktury verze kódu, které jsou zřízené v clusteru Service Fabric.

Získá seznam informací o prostředků infrastruktury verze kódu, které jsou zřízené v clusteru. Parametr CodeVersion lze volitelně provede filtrování výstupu pouze tento konkrétní verzi.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --verze kódu | Verze produktu Service Fabric. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-cluster-config-versions"></a>Konfigurace clusteru sfctl – verze
Získá seznam prostředků infrastruktury verze konfigurace, které jsou zřízené v clusteru Service Fabric.

Získá seznam informací o fabric config verze, které jsou zřízené v clusteru. Parametr ConfigVersion lze volitelně provede filtrování výstupu pouze tento konkrétní verzi.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Konfigurace verze | Konfigurace verze Service Fabric. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-cluster-health"></a>sfctl stavu clusteru
Získá stav clusteru Service Fabric.

Získá stav clusteru Service Fabric. EventsHealthStateFilter použijte k filtrování kolekce událostí stavu hlášených v clusteru podle stavu. Podobně, použijte NodesHealthStateFilter a ApplicationsHealthStateFilter k filtrování kolekce uzlů a aplikací vrátil na základě jejich agregovaného stavu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --aplikace stavu stavu filtru | Umožňuje filtrování vrácených ve výsledku dotazu stavu clusteru na základě jejich stavu objektů stavu stavu aplikace. Možné hodnoty pro tento parametr patří získaný od členů nebo bitové operace u členů výčtu HealthStateFilter celočíselnou hodnotu. Vrátí se pouze aplikace, které odpovídají filtru. Všechny aplikace se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadaný, jsou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnotou může být kombinací tyto hodnoty získané pomocí bitový operátor 'OR'. Například pokud zadaná hodnota je 6 stavu aplikací s hodnotou elementu HealthState OK (2) a upozornění (4), jsou vráceny.  <br> -Výchozí – výchozí hodnota. Vyhledá všechny stav HealthState. Hodnota je nula.  <br> -None - filtr, který se neshoduje se žádnou hodnotu stavu HealthState. Použít cílem vrátit žádné výsledky v dané kolekci stavů. Hodnota je 1.  <br> -Ok - filtrujte, aby odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Upozornění - filtr, hodnota odpovídá vstup k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který odpovídá vstup s hodnotou elementu HealthState chyby. Hodnota je 8.  <br> -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --události stavu stavu filtru | Umožňuje filtrování vrácených objektů HealthEvent kolekce na základě stavu. Možné hodnoty pro tento parametr patří celočíselná hodnota jednoho z následujících stavů. Se vrátí jenom události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadaný, jsou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnotou může být kombinací tyto hodnoty získané pomocí bitový operátor 'OR'. Například pokud zadaná hodnota je 6 všechny události s hodnotou elementu HealthState OK (2) a upozornění (4), jsou vráceny.  <br> -Výchozí – výchozí hodnota. Vyhledá všechny stav HealthState. Hodnota je nula.  <br> -None - filtr, který se neshoduje se žádnou hodnotu stavu HealthState. Použít cílem vrátit žádné výsledky v dané kolekci stavů. Hodnota je 1.  <br> -Ok - filtrujte, aby odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Upozornění - filtr, hodnota odpovídá vstup k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který odpovídá vstup s hodnotou elementu HealthState chyby. Hodnota je 8.  <br> -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --statistiky vyloučení stavu | Určuje, zda má být vrácen stav statistiky jako součást výsledků dotazu. Chcete-li hodnotu false ve výchozím nastavení. Statistiku zobrazují počet podřízených entit ve stavu Ok, upozornění a chyby. |
| --zahrnují system--stavu statistik aplikace | Určuje, zda statistiky stavu by měla obsahovat prostředky infrastruktury \: /System statistik stavu aplikace. Chcete-li hodnotu false ve výchozím nastavení. Pokud je IncludeSystemApplicationHealthStatistics nastavená na hodnotu true, stav statistiky zahrnují entitami, které patří k prostředkům infrastruktury \: /System aplikace. Výsledek dotazu obsahuje, jinak hodnota stavu statistiky pouze pro uživatele aplikace. Statistiky stavu musí být součástí výsledek dotazu pro tento parametr má být použita. |
| --uzly stavu stavu filtru | Umožňuje filtrování vrácených ve výsledku dotazu stavu clusteru na základě jejich stavu uzlu stavu stavu objektů. Možné hodnoty pro tento parametr patří celočíselná hodnota jednoho z následujících stavů. Vrátí se pouze uzly, které odpovídají filtru. Všechny uzly se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadaný, jsou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnotou může být kombinací tyto hodnoty získané pomocí bitový operátor 'OR'. Například pokud zadaná hodnota je 6 stav uzlů s hodnotou elementu HealthState OK (2) a upozornění (4), jsou vráceny.  <br> -Výchozí – výchozí hodnota. Vyhledá všechny stav HealthState. Hodnota je nula.  <br> -None - filtr, který se neshoduje se žádnou hodnotu stavu HealthState. Použít cílem vrátit žádné výsledky v dané kolekci stavů. Hodnota je 1.  <br> -Ok - filtrujte, aby odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Upozornění - filtr, hodnota odpovídá vstup k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který odpovídá vstup s hodnotou elementu HealthState chyby. Hodnota je 8.  <br> -Všechny - filtr, který odpovídá vstup s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-cluster-manifest"></a>sfctl manifestu clusteru
Získáte manifestu clusteru Service Fabric.

Získáte manifestu clusteru Service Fabric. V manifestu clusteru obsahuje vlastnosti clusteru, které obsahují různé typy uzlů v clusteru, konfigurace zabezpečení, chyby a topologií domény upgradu atd. Tyto vlastnosti jsou zadaný jako součást souboru ClusterConfig.JSON soubor při nasazení samostatného clusteru. Ale většinu informací v manifestu clusteru je generované interně službou service fabric během nasazení clusteru v jiných scénářích nasazení (například při použití portálu Azure). Obsah manifestu clusteru je pouze informativní charakter a uživatelé se nepředpokládá trvat závislost na formát obsah souboru nebo jeho interpretace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-cluster-operation-cancel"></a>operace clusteru sfctl-zrušit
Selhání vyvolané uživatel operaci zruší.

Následující rozhraní API spustit selhání operací, které může být zrušena pomocí CancelOperation: StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Pokud platnost hodnotu false, pak zadaná uživatele vyvolané operace se elegantně zastaví a vyčistit.  Je-li vynutit hodnotu true, bude příkaz přerušena a některé vnitřní stav může být ponecháno.  Zadání force jako true, musí být použit s pozor. Dokud toto rozhraní API již byla volána na příkaz Testovací force nastaveným na hodnotu false první, nebo pokud příkaz test již OperationState OperationState.RollingBack není povolené volající toto rozhraní API force nastaveným na hodnotu true. 

Vysvětlení\: OperationState.RollingBack prostředky, které systém bude/čistí interní systému o stavu způsobené provádění příkazu. Pokud byl příkaz test způsobí ztrátu dat, se nebude obnovit data.  Například pokud jste volání StartDataLoss pak volat toto rozhraní API, systém bude pouze vyčistit vnitřní stav ze spuštění příkazu. Cílový oddíl dat neobnoví Pokud příkaz zvýšily dostatečně způsobí ztrátu dat. 

> [!NOTE]
> Pokud toto rozhraní API je možné vynutit == true, vnitřní stav může být ponecháno.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id operace [vyžaduje] | Identifikátor GUID, který identifikuje volání toto rozhraní API.  To je předán do odpovídající GetProgress API. |
| --Vynutit | Označuje, zda řádně vrátit zpět a vyčištění stavu interní systému upraveném provádění operace vyvolané uživatele. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-cluster-operation-list"></a>operace clusteru sfctl – seznam
Získá seznam operací vyvolané uživatele selhání filtrovaná podle zadaného vstupu.

Získá seznam operací vyvolané uživatele selhání filtrovaná podle zadaného vstupu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --Filtr stavu | Použít pro filtrování je OperationState pro uživatele vyvolané operace. <br> 65535 - Vybrat vše <br> 1 - vyberte spuštěná <br> 2 – vyberte vrácení <br>8 - vybrat dokončeno <br>16 – vyberte Faulted <br>32 - vybrat zrušeno <br>64 - vyberte ForceCancelled.  <br>Výchozí\: 65535. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |
| – Typ filtru | Použít k filtrování na typ operace pro uživatele vyvolané operace. <br> 65535 - Vybrat vše <br> 1 - vyberte PartitionDataLoss. <br> 2 – vyberte PartitionQuorumLoss. <br> 4 - vyberte PartitionRestart. <br> 8 – vyberte NodeTransition.  <br> Výchozí\: 65535. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-cluster-provision"></a>zřízení clusteru sfctl
Zřídit balíčky kód nebo konfigurace clusteru Service Fabric.

Ověřte a zřídit balíčky kód nebo konfigurace clusteru Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --cluster-manifest-file-path | Cesta k souboru manifestu clusteru. |
| --cestu souboru kódu | Cesta souboru clusteru kód balíčku. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-cluster-recover-system"></a>sfctl clusteru pro obnovení systému
Do clusteru Service Fabric označuje, že mají pokusit o obnovení systémových služeb, které jsou aktuálně zablokované ve ztrátě kvora.

Do clusteru Service Fabric označuje, že mají pokusit o obnovení systémových služeb, které jsou aktuálně zablokované ve ztrátě kvora. Tuto operaci musí provést pouze v případě se ví, že repliky, které jsou vypnutí nelze obnovit. Nesprávné použití toto rozhraní API může způsobit ztrátu dat.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-cluster-report-health"></a>Sestava clusteru sfctl-stavu
Odešle zprávu o stavu na cluster Service Fabric.

Sestava musí obsahovat informace o zdroji této sestavy stavu a vlastnosti, na kterém je zaznamenána. Sestava je odeslána do Service Fabric uzlu brány, který předává do úložiště stavu. Sestava může přijal bránou, ale byl odmítnut úložiště zdravotní po doplňující ověření. Úložiště stavu může například odmítnout sestavy z důvodu neplatný parametr, jako je zastaralé pořadové číslo. Pokud chcete zobrazit, zda sestava byla použita v health store, zkontrolujte, že sestavy se zobrazí v HealthEvents clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --stavu – vlastnost [vyžaduje] | Vlastnost s informacemi o stavu. <br><br> Entita může mít sestavy stavu pro různé vlastnosti. Vlastnost je řetězec a pevnou výčtu umožňuje flexibilitu ohlašování zařadit do kategorií stavu podmínku, která aktivuje sestavy. Ohlašování s ID zdroje "LocalWatchdog" například můžete monitorovat stav je k dispozici disk na uzlu, takže ho mohou zasílat zprávy o "AvailableDisk" vlastnost v tomto uzlu. Stejné ohlašování můžete monitorovat připojení k uzlu, takže ho mohou zasílat zprávy o vlastnost "Připojení" ve stejném uzlu. V health store tyto sestavy jsou považovány za samostatné stavu události pro určeného uzlu. Společně s ID zdroje vlastnost jednoznačně identifikuje informace o stavu. |
| --stavu [vyžaduje] | Možné hodnoty patří\: 'Neplatný', 'Ok', 'Upozornění', "Chyba", "Neznámý". |
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

## <a name="sfctl-cluster-select"></a>Vyberte sfctl clusteru
Připojí se ke clusteru koncový bod Service Fabric.

Pokud připojení k zabezpečení clusteru, zadejte absolutní cestu k certifikátu (CRT) a soubor klíče (s příponou Key) nebo jeden soubor s obou (.pem). Nezadávejte i. Volitelně Pokud připojení k zabezpečení clusteru, zadejte také absolutní cestu k souboru s kompletem sady certifikační Autority nebo adresáře certifikátů důvěryhodné certifikační Autority.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --koncový bod [vyžaduje] | Clusteru adresu URL koncového bodu, včetně port a předponu HTTP nebo HTTPS. |
| --aad | Pro ověřování pomocí služby Azure Active Directory. |
| --ca | Absolutní cesta do adresáře certifikátů certifikační Autority zacházet s jako platná nebo soubor kompletu certifikační Autority. |
| --certifikátu. | Absolutní cesta k souboru certifikátu klienta. |
| – klíč | Absolutní cesta k souboru klíče certifikátu klienta. |
| – Ověřte ne | Zakázat ověřování pro certifikáty při použití protokolu HTTPS, Všimněte si\: Toto je nezabezpečené možnost a by se neměla používat pro provozní prostředí. |
| --pem | Absolutní cesta k klientský certifikát jako soubor .pem. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-cluster-unprovision"></a>unprovision sfctl clusteru
Zrušit zřízení balíčky kód nebo konfigurace clusteru Service Fabric.

Zrušit zřízení balíčky kód nebo konfigurace clusteru Service Fabric. Podporuje se samostatně zrušit zřízení kódu a konfigurace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --verze kódu | Verze balíčku kódu clusteru. |
| – Konfigurace verze | Verze manifestu clusteru. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-cluster-upgrade"></a>upgrade clusteru sfctl
Spusťte upgrade verze kódu nebo konfigurace clusteru Service Fabric.

Ověření upgradu zadaných parametrů a spusťte upgrade verze kódu nebo konfigurace clusteru Service Fabric, pokud parametry jsou platné.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --app-health-map | Slovník JSON kódovaný dvojici název aplikace a maximální procento není v pořádku, než se vyvolá chybu. |
| --app-type-health-map | Slovník JSON kódovaný dvojici název typu aplikace a maximální procento není v pořádku, než se vyvolá chybu. |
| --verze kódu | Verze kódu clusteru. |
| – Konfigurace verze | Verze konfigurace clusteru. |
| --vyhodnocení stavu rozdílů | Umožňuje vyhodnocení stavu rozdílové místo zkušební verzi absolutní stavu po dokončení každé upgradované domény. |
| --rozdílů není v pořádku uzly | Maximální povolené procento uzlů snížení stavu během upgradu clusteru povoleny.  Výchozí\: 10. <br><br> Mezi stav uzlů na začátku upgradu a stav uzlů v době vyhodnocení stavu se měří delta. Kontrola se provádí po dokončení upgradu každé upgradované domény a ujistěte se, že je globální stav clusteru. povolená omezení. |
| --selhání akce | Možné hodnoty patří\: "Neplatná',"Vrácení","Ruční". |
| --Vynutit restartování | Vynuťte restartování. |
| --stavu. Zkontrolujte opakování | Časový limit opakování kontroly stavu se měří v milisekundách. |
| --stavu. Zkontrolujte nestabilním | Stav zkontrolujte stabilní doba v milisekundách. |
| --health-check-wait | Doba čekání kontroly stavu se měří v milisekundách. |
| --repliky set kontrola-časový limit | Upgrade repliky nastavit časový limit kontroly, které měří v sekundách. |
| --vrácení režimu upgradu | Možné hodnoty patří\: "Neplatná', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitorované'.  Výchozí\: UnmonitoredAuto. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |
| --není v pořádku – aplikace | Maximální povolené procento žádostí, není v pořádku, než ohlásí chybu. <br><br> Například povolit 10 % aplikací jako chybný, tato hodnota je 10. Procento představuje maximální procento. povolená aplikací, které může být není v pořádku, než clusteru považován za chybu. Pokud je dodržena procento, ale existuje alespoň jedna aplikace není v pořádku, stav budou vyhodnocené jako varování. To se vypočítá jako podíl počtu není v pořádku aplikace přes celkový počet instancí aplikace v clusteru, s výjimkou aplikace typy aplikací, které jsou součástí ApplicationTypeHealthPolicyMap. Výpočet zaokrouhlí na tolerovat selhání jednoho na malý počet aplikací. |
| --není v pořádku uzly | Maximální povolené procento není v pořádku uzlů než ohlásí chybu. <br><br> Například povolit 10 % uzlů jako chybný, tato hodnota je 10. Procento představuje maximální procento. povolená uzlů, které může být není v pořádku, než clusteru považován za chybu. Pokud je dodržena procento, ale je alespoň jeden uzel a není v pořádku, stav budou vyhodnocené jako varování. Procentuální se vypočítá jako podíl počtu uzlů není v pořádku přes celkový počet uzlů v clusteru. Výpočet zaokrouhlí na tolerovat jeden selhání na malém počtu uzlů. Ve velkých clusterech některé uzly bude vždy mimo provoz nebo se pro opravy, takže toto procento by měl být nakonfigurovaný pro tolerovat možnost, která. |
| --upgrade-domain-delta-unhealthy-nodes | Maximální povolené procento uzly domény upgradu snížení stavu během upgradu clusteru povoleny.  Výchozí\: 15. <br><br> Mezi uzly domény upgradu na začátku upgrade stav a stav uzly domény upgradu v době vyhodnocení stavu se měří delta. Kontrola se provádí po dokončení upgradu každé upgradované domény pro všechny dokončení upgradu domén a ujistěte se, že stav upgradu domény nacházela v rozsahu. povolená. |
| --upgrade-domain-timeout | Časový limit domény upgradu se měří v milisekundách. |
| – upgrade vypršení časového limitu | Časový limit upgradu se měří v milisekundách. |
| --upozornění jako chyba | Upozornění jsou zachází se stejnou závažnost jako chyby. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-cluster-upgrade-resume"></a>upgrade clusteru sfctl-obnovení
Proveďte upgrade clusteru přejde k další upgradovací doméně.

Podle potřeby proveďte clusteru kód nebo konfigurace upgradu Přesun na další upgradovací doméně.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --upgradu domény [vyžaduje] | Další upgradovací doméně pro tento upgrade clusteru. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-cluster-upgrade-rollback"></a>upgrade clusteru sfctl-vrácení
Upgrade clusteru Service Fabric se vrátit zpět.

Vrácení upgradu kódu nebo konfigurace clusteru Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-cluster-upgrade-status"></a>upgrade clusteru sfctl-status
Získá průběh aktuální upgrade clusteru.

Získá aktuální průběh upgradu probíhající clusteru. Pokud žádné upgrade právě probíhá, získáte poslední stav předchozí upgrade clusteru.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| – ladění | Zvýšit protokolování podrobností zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --výstup -o | Výstupní formát.  Povolené hodnoty\: formát json, jsonc, tabulce, tsv.  Výchozí\: json. |
| --dotazu | Řetězec dotazu JMESPath. V tématu http\://jmespath.org/ Další informace a příklady. |
| -verbose | Zvýšit protokolování podrobností. Použití – ladění pro úplné ladění protokoly. |

## <a name="sfctl-cluster-upgrade-update"></a>upgrade clusteru sfctl-aktualizace
Aktualizujte upgradu parametry upgradu clusteru Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --app-health-map | Slovník JSON kódovaný dvojici název aplikace a maximální procento není v pořádku, než se vyvolá chybu. |
| --app-type-health-map | Slovník JSON kódovaný dvojici název typu aplikace a maximální procento není v pořádku, než se vyvolá chybu. |
| --vyhodnocení stavu rozdílů | Umožňuje vyhodnocení stavu rozdílové místo zkušební verzi absolutní stavu po dokončení každé upgradované domény. |
| --rozdílů není v pořádku uzly | Maximální povolené procento uzlů snížení stavu během upgradu clusteru povoleny.  Výchozí\: 10. <br><br> Mezi stav uzlů na začátku upgradu a stav uzlů v době vyhodnocení stavu se měří delta. Kontrola se provádí po dokončení upgradu každé upgradované domény a ujistěte se, že je globální stav clusteru. povolená omezení. |
| --selhání akce | Možné hodnoty patří\: "Neplatná',"Vrácení","Ruční". |
| --Vynutit restartování | Vynuťte restartování. |
| --stavu. Zkontrolujte opakování | Časový limit opakování kontroly stavu se měří v milisekundách. |
| --stavu. Zkontrolujte nestabilním | Stav zkontrolujte stabilní doba v milisekundách. |
| --health-check-wait | Doba čekání kontroly stavu se měří v milisekundách. |
| --repliky set kontrola-časový limit | Upgrade repliky nastavit časový limit kontroly, které měří v sekundách. |
| --vrácení režimu upgradu | Možné hodnoty patří\: "Neplatná', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitorované'.  Výchozí\: UnmonitoredAuto. |
| časový limit – -t | Server časový limit v sekundách.  Výchozí\: 60. |
| --není v pořádku – aplikace | Maximální povolené procento žádostí, není v pořádku, než ohlásí chybu. <br><br> Například povolit 10 % aplikací jako chybný, tato hodnota je 10. Procento představuje maximální procento. povolená aplikací, které může být není v pořádku, než clusteru považován za chybu. Pokud je dodržena procento, ale existuje alespoň jedna aplikace není v pořádku, stav budou vyhodnocené jako varování. To se vypočítá jako podíl počtu není v pořádku aplikace přes celkový počet instancí aplikace v clusteru, s výjimkou aplikace typy aplikací, které jsou součástí ApplicationTypeHealthPolicyMap. Výpočet zaokrouhlí na tolerovat selhání jednoho na malý počet aplikací. |
| --není v pořádku uzly | Maximální povolené procento není v pořádku uzlů než ohlásí chybu. <br><br> Například povolit 10 % uzlů jako chybný, tato hodnota je 10. Procento představuje maximální procento. povolená uzlů, které může být není v pořádku, než clusteru považován za chybu. Pokud je dodržena procento, ale je alespoň jeden uzel a není v pořádku, stav budou vyhodnocené jako varování. Procentuální se vypočítá jako podíl počtu uzlů není v pořádku přes celkový počet uzlů v clusteru. Výpočet zaokrouhlí na tolerovat jeden selhání na malém počtu uzlů. Ve velkých clusterech některé uzly bude vždy mimo provoz nebo se pro opravy, takže toto procento by měl být nakonfigurovaný pro tolerovat možnost, která. |
| --upgrade-domain-delta-unhealthy-nodes | Maximální povolené procento uzly domény upgradu snížení stavu během upgradu clusteru povoleny.  Výchozí\: 15. <br><br> Mezi uzly domény upgradu na začátku upgrade stav a stav uzly domény upgradu v době vyhodnocení stavu se měří delta. Kontrola se provádí po dokončení upgradu každé upgradované domény pro všechny dokončení upgradu domén a ujistěte se, že stav upgradu domény nacházela v rozsahu. povolená. |
| --upgrade-domain-timeout | Časový limit domény upgradu se měří v milisekundách. |
| – typ upgradu | Možné hodnoty patří\: "Neplatná', 'Vrácení', 'Rolling_ForceRestart'.  Výchozí\: vrácení. |
| – upgrade vypršení časového limitu | Časový limit upgradu se měří v milisekundách. |
| --upozornění jako chyba | Upozornění jsou zachází se stejnou závažnost jako chyby. |

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