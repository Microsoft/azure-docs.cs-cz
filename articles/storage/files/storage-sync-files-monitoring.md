---
title: Azure File Sync monitorování | Microsoft Docs
description: Přečtěte si, jak monitorovat nasazení Azure File Sync pomocí Azure Monitor, služby synchronizace úložiště a Windows serveru.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/28/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1ef24522f688c5ae1176630a2f370cd7ee7c3cd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448058"
---
# <a name="monitor-azure-file-sync"></a>Sledování služby Synchronizace souborů Azure

Pomocí Azure File Sync můžete centralizovat sdílené složky ve vaší organizaci ve službě soubory Azure a zároveň udržet flexibilitu, výkon a kompatibilitu místního souborového serveru. Synchronizace souborů Azure transformuje Windows Server na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít jakýkoli protokol dostupný ve Windows Serveru, včetně SMB, NFS a FTPS. Můžete mít tolik mezipamětí, kolik potřebujete po celém světě.

Tento článek popisuje, jak monitorovat nasazení Azure File Sync pomocí Azure Monitor, služby synchronizace úložiště a Windows serveru.

V této příručce jsou uvedené následující scénáře: 
- Zobrazit Azure File Sync metriky v Azure Monitor.
- Vytvoří výstrahy v Azure Monitor k proaktivnímu upozorňování na kritické podmínky.
- Zobrazte stav nasazení Azure File Sync pomocí Azure Portal.
- Jak používat protokoly událostí a čítače výkonu na serverech Windows k monitorování stavu nasazení Azure File Sync. 

## <a name="azure-monitor"></a>Azure Monitor

Pomocí [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) můžete zobrazit metriky a nakonfigurovat výstrahy pro synchronizaci, vrstvení cloudu a připojení k serveru.  

### <a name="metrics"></a>Metriky

Metriky pro Azure File Sync jsou ve výchozím nastavení povolené a odesílají se Azure Monitor každých 15 minut.

**Jak zobrazit metriky Azure File Sync v Azure Monitor**
1. Přejděte do **služby synchronizace úložiště** v **Azure Portal** a klikněte na **metriky**.
2. Klikněte na rozevírací seznam **metrika** a vyberte metriku, kterou chcete zobrazit.

![Snímek obrazovky Azure File Sync metriky](media/storage-sync-files-troubleshoot/file-sync-metrics.png)

V Azure Monitor jsou k dispozici následující metriky pro Azure File Sync:

| Název metriky | Description |
|-|-|
| Synchronizované bajty | Velikost přenesených dat (nahrávání a stahování).<br><br>Jednotka: bajtů<br>Typ agregace: součet<br>Použitelné dimenze: název koncového bodu serveru, směr synchronizace, název skupiny synchronizace |
| Stažení vrstvení cloudu | Velikost vrácených dat.<br><br>**Poznámka**: Tato metrika bude v budoucnu odebrána. K monitorování velikosti vrácených dat použijte metriku velikosti volání ve vrstvách cloudu.<br><br>Jednotka: bajtů<br>Typ agregace: součet<br>Platná dimenze: název serveru |
| Velikost odvolání při vyvolání vrstvy cloudu | Velikost vrácených dat.<br><br>Jednotka: bajtů<br>Typ agregace: součet<br>Platná dimenze: název serveru, název skupiny synchronizace |
| Velikost odvolání při vrstvení cloudu podle aplikace | Velikost dat vrácených aplikací<br><br>Jednotka: bajtů<br>Typ agregace: součet<br>Platná dimenze: název aplikace, název serveru, název skupiny synchronizace |
| Propustnost volání při navracení cloudových vrstev | Velikost propustnosti odvolání dat<br><br>Jednotka: bajtů<br>Typ agregace: součet<br>Platná dimenze: název serveru, název skupiny synchronizace |
| Soubory se nesynchronizují | Počet souborů, které se nedaří synchronizovat.<br><br>Jednotka: počet<br>Typ agregace: součet<br>Použitelné dimenze: název koncového bodu serveru, směr synchronizace, název skupiny synchronizace |
| Synchronizované soubory | Počet přenesených souborů (Odeslat a stáhnout).<br><br>Jednotka: počet<br>Typ agregace: součet<br>Použitelné dimenze: název koncového bodu serveru, směr synchronizace, název skupiny synchronizace |
| Online stav serveru | Počet prezenčních signálů přijatých ze serveru.<br><br>Jednotka: počet<br>Typ agregace: maximum<br>Platná dimenze: název serveru |
| Výsledek relace synchronizace | Výsledek relace synchronizace (1 = úspěšná relace synchronizace; 0 = nezdařená relace synchronizace)<br><br>Jednotka: počet<br>Typy agregace: maximum<br>Použitelné dimenze: název koncového bodu serveru, směr synchronizace, název skupiny synchronizace |

### <a name="alerts"></a>Výstrahy

Výstrahy proaktivně upozorňují na skutečnost, že jsou ve vašich datech monitorování zjištěny důležité podmínky. Další informace o konfiguraci výstrah v Azure Monitor najdete v tématu [Přehled výstrah v Microsoft Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

**Jak vytvořit výstrahy pro Azure File Sync**

1. V **Azure Portal**přejdete do **služby synchronizace úložiště** . 
2. V části monitorování klikněte na **výstrahy** a pak klikněte na **+ nové pravidlo výstrahy**.
3. Klikněte na **vybrat podmínku** a zadejte pro tuto výstrahu následující informace: 
    - **Metrika**
    - **Název dimenze**
    - **Logika výstrahy**
4. Klikněte na **Vybrat skupinu akcí** a přidejte do ní skupinu akcí (E-mail, SMS atd.), a to buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.
5. Vyplňte **Podrobnosti výstrahy** , jako je **název pravidla výstrahy**, **Popis** a **závažnost**.
6. Kliknutím na **vytvořit pravidlo výstrahy** vytvořte výstrahu.  

V následující tabulce jsou uvedeny příklady scénářů, které je třeba monitorovat, a správnou metriku pro použití výstrahy:

| Scénář | Metrika, která se má použít pro upozornění |
|-|-|
| Stav koncového bodu serveru ukazuje na portálu chybu. | Výsledek relace synchronizace |
| Neúspěšné synchronizace souborů na koncový bod serveru nebo cloudu | Soubory se nesynchronizují |
| U registrovaného serveru se nedaří komunikovat se službou synchronizace úložiště. | Online stav serveru |
| Velikost volání vrstvení cloudu překročila 500GiB za den.  | Velikost odvolání při vyvolání vrstvy cloudu |

Pokyny, jak vytvořit výstrahy pro tyto scénáře, naleznete v části [Příklady výstrah](#alert-examples) .

## <a name="storage-sync-service"></a>Služba synchronizace úložiště

Chcete-li zobrazit stav nasazení Azure File Sync v **Azure Portal**, přejděte do **služby synchronizace úložiště** a k dispozici jsou následující informace:

- Stav registrovaného serveru
- Stav koncového bodu serveru
    - Soubory se nesynchronizují
    - Aktivita synchronizace
    - Efektivita vrstev cloudu
    - Nevrstvení souborů
    - Chyby odvolání
- Metriky

### <a name="registered-server-health"></a>Stav registrovaného serveru

Chcete-li zobrazit **registrovaný stav serveru** na portálu, přejděte do části **registrované servery** **služby synchronizace úložiště**.

![Snímek obrazovky stavu registrovaných serverů](media/storage-sync-files-troubleshoot/file-sync-registered-servers.png)

- Pokud je stav **registrovaného serveru** **online**, server úspěšně komunikuje se službou.
- Pokud je stav **registrovaného serveru** **zobrazený v režimu offline**, proces monitorování synchronizace úložiště (AzureStorageSyncMonitor.exe) není spuštěný nebo Server nemůže získat přístup ke službě Azure File Sync. Pokyny najdete v [dokumentaci k řešení problémů](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#server-endpoint-noactivity) .

### <a name="server-endpoint-health"></a>Stav koncového bodu serveru

Pokud chcete zobrazit stav **koncového bodu serveru** na portálu, přejděte do části **skupiny synchronizace** **služby synchronizace úložiště** a vyberte **skupinu synchronizace**.

![Snímek obrazovky stavu koncového bodu serveru](media/storage-sync-files-troubleshoot/file-sync-server-endpoint-health.png)

- Aktivita **stav koncového bodu serveru** a **synchronizace** na portálu je založena na událostech synchronizace, které jsou zaznamenány do protokolu událostí TELEMETRIE na serveru (ID 9102 a 9302). Pokud relace synchronizace selže kvůli přechodné chybě, například k chybě, bude koncový bod **serveru na portálu** stále zobrazen, pokud aktuální relace synchronizace probíhá (soubory jsou aplikovány). Událost s ID 9302 je událost průběhu synchronizace a ID události 9102 se protokoluje po dokončení relace synchronizace.  Další informace najdete v tématu [synchronizace stavu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) a [průběhu synchronizace](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session). Pokud stav koncového bodu serveru zobrazuje **chybu** nebo **žádná aktivita**, přečtěte si pokyny v [dokumentaci k řešení potíží](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) .
- Počet **souborů, které nejsou synchronizované** na portálu, vychází z ID události 9121, které se zaznamená do protokolu událostí telemetrie na serveru. Tato událost se zaznamená do protokolu pro každou položku chyby, jakmile se relace synchronizace dokončí. Chcete-li vyřešit chyby jednotlivých položek, přečtěte si téma [návody v tématu, zda existují konkrétní soubory nebo složky, které se nesynchronizují?](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).
- Pokud chcete na portálu zobrazit **efektivitu vrstev cloudu** , přejděte do části **Vlastnosti koncového bodu serveru** a přejděte do části **vrstvení cloudu** . Data poskytnutá pro efektivitu vrstvení cloudu vycházejí z ID události 9071, které se zaznamená do protokolu událostí telemetrie na serveru. Další informace najdete v tématu [Přehled vrstvení cloudu](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering).
- Pokud chcete zobrazit soubory, které **nejsou vrstvení** a **navrácení chyb** na portálu, přejděte do části **Vlastnosti koncového bodu serveru** a přejděte do části **vrstvení cloudu** . **Soubory, které nejsou vrstvení** , jsou založené na id události 9003, které se zaznamená do protokolu událostí telemetrie na serveru, a **chyby odvolání** vycházejí z ID události 9006. Chcete-li prozkoumat soubory, které se nedaří navrátit do vrstvy nebo se odvolat, přečtěte si téma [Postup řešení potíží se soubory, které se nepodařilo](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#how-to-troubleshoot-files-that-fail-to-tier) [vyzvat a jak řešit problémy se selháním](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#how-to-troubleshoot-files-that-fail-to-be-recalled).

### <a name="metric-charts"></a>Grafy metrik

- Na portálu služby synchronizace úložiště se mohou zobrazit následující grafy metrik:

  | Název metriky | Description | Název okna |
  |-|-|-|
  | Synchronizované bajty | Velikost přenesených dat (nahrávání a stahování) | Skupina synchronizace, koncový bod serveru |
  | Stažení vrstvení cloudu | Velikost vrácených dat | Registrované servery |
  | Soubory se nesynchronizují | Počet souborů, které se nedaří synchronizovat | Koncový bod serveru |
  | Synchronizované soubory | Počet přenesených souborů (Odeslat a stáhnout) | Skupina synchronizace, koncový bod serveru |
  | Online stav serveru | Počet prezenčních signálů přijatých ze serveru | Registrované servery |

- Další informace najdete v tématu [Azure monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Grafy na portálu služby synchronizace úložiště mají časový rozsah 24 hodin. Chcete-li zobrazit různé časové rozsahy nebo rozměry, použijte Azure Monitor.

## <a name="windows-server"></a>Windows Server

Na **Windows serveru** s nainstalovaným agentem Azure File Sync můžete zobrazit stav koncových bodů serveru na tomto serveru pomocí **protokolů událostí** a **čítačů výkonu**.

### <a name="event-logs"></a>Protokoly událostí

Pomocí protokolu událostí telemetrie na serveru můžete monitorovat stav zaregistrovaných serverů, synchronizace a vrstvení cloudu. Protokol událostí telemetrie se nachází v Prohlížeč událostí v části *aplikace a Services\Microsoft\FileSync\Agent*.

Synchronizovat stav

- Po dokončení relace synchronizace se protokoluje událost s ID 9102. Tuto událost použijte k určení, jestli jsou relace synchronizace úspěšné (**HRESULT = 0**), a pokud dojde k chybám synchronizace pro jednotlivé položky (**PerItemErrorCount**). Další informace najdete v dokumentaci ke [stavu synchronizace](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) a  [chybách jednotlivých položek](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) .

  > [!Note]  
  > V některých případech se relace synchronizace nezdařila celkem nebo má nenulovou PerItemErrorCount. Pořád se ale dál dostanou a některé soubory se úspěšně synchronizují. Můžete to vidět v použitých polích, jako jsou AppliedFileCount, AppliedDirCount, AppliedTombstoneCount a AppliedSizeBytes. Tato pole vám sdělí, kolik relací bylo úspěšně dokončeno. Pokud vidíte, že se více relací synchronizace nedaří na řádku a že mají rostoucí počet použitých hodnot, podělte čas synchronizace, aby se mohl pokusit znovu před otevřením lístku podpory.

- Událost s ID 9121 se zaznamená do protokolu pro každou položku chyby, jakmile se relace synchronizace dokončí. Tuto událost použijte k určení počtu souborů, u kterých se synchronizace s touto chybou nezdařila (**PersistentCount** a **TransientCount**). Trvalá chyba pro jednotlivé položky by se měla prozkoumat v tématu [návody zjistit, jestli nejsou nějaké konkrétní soubory nebo složky, které se nesynchronizují?](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

- Událost s ID 9302 se protokoluje každých 5 až 10 minut, pokud dojde k aktivní relaci synchronizace. Tuto událost použijte k určení, kolik položek se má synchronizovat (**TotalItemCount**), počet položek synchronizovaných tak daleko (**AppliedItemCount**) a počet položek, které se nezdařily z důvodu chyby pro jednotlivé položky (**PerItemErrorCount**). Pokud synchronizace neprovádí postup (**AppliedItemCount = 0**), relace synchronizace nakonec selže a ID události 9102 se zaprotokoluje s chybou. Další informace najdete v [dokumentaci průběh synchronizace](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Stav registrovaného serveru

- Událost s ID 9301 se protokoluje každých 30 sekund, když server dotazuje službu pro úlohy. Pokud se GetNextJob dokončí se **stavem = 0**, server může komunikovat se službou. Pokud GetNextJob skončí s chybou, najdete pokyny v [dokumentaci k řešení problémů](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#server-endpoint-noactivity) .

Stav vrstvení cloudu

- Pokud chcete monitorovat aktivitu vrstvení na serveru, použijte ID události 9003, 9016 a 9029 v protokolu událostí telemetrie, který je umístěný v Prohlížeč událostí v části *aplikace a Services\Microsoft\FileSync\Agent*.

  - Událost s ID 9003 poskytuje distribuci chyb pro koncový bod serveru. Příklad: celkový počet chyb a kód chyby. Jedna událost je protokolována podle kódu chyby.
  - ID události 9016 poskytuje duplikování výsledků pro svazek. Například: volné místo v procentech je, počet souborů, které byly v relaci opuštěné, a počet souborů, jejichž počet se nezdařil.
  - Událost s ID 9029 poskytuje duplikaci informací o relaci koncového bodu serveru. Například: počet souborů, které se v relaci pokoušely, počet souborů vrstvených v relaci a počet souborů, které jsou již vrstveny.
  
- Chcete-li monitorovat aktivitu odvolání na serveru, použijte ID události 9005, 9006, 9009, 9059 a 9071 v protokolu událostí telemetrie, který je umístěn v Prohlížeč událostí v části *aplikace a Services\Microsoft\FileSync\Agent*.

  - Událost s ID 9005 poskytuje spolehlivost odvolání pro koncový bod serveru. Například: celkový přístup k jedinečným souborům a celkový počet jedinečných souborů s neúspěšným přístupem.
  - Událost s ID 9006 poskytuje distribuci chyb odvolání pro koncový bod serveru. Například: celkový počet neúspěšných požadavků a kód chyby. Jedna událost je protokolována podle kódu chyby.
  - Událost s ID 9009 poskytuje informace o odvolání relace pro koncový bod serveru. Například: DurationSeconds, CountFilesRecallSucceeded a CountFilesRecallFailed.
  - Událost s ID 9059 poskytuje distribuci odvolání aplikace pro koncový bod serveru. Například: ShareId, název aplikace a TotalEgressNetworkBytes.
  - ID události 9071 poskytuje efektivitu cloudových vrstev pro koncový bod serveru. Například: TotalDistinctFileCountCacheHit, TotalDistinctFileCountCacheMiss, TotalCacheHitBytes a TotalCacheMissBytes.

### <a name="performance-counters"></a>Čítače výkonu

K monitorování synchronizační aktivity použijte Azure File Sync čítače výkonu na serveru.

Chcete-li zobrazit Azure File Sync čítače výkonu na serveru, spusťte nástroj sledování výkonu (Perfmon.exe). Čítače můžete najít v části **přenesené bajty AFS** a objekty **operace synchronizace AFS** .

V nástroji Sledování výkonu jsou k dispozici následující čítače výkonu pro Azure File Sync:

| Název Object\Counter výkonu | Description |
|-|-|
| Bajty AFS Transferred\Downloaded bajty/s | Počet stažených bajtů za sekundu |
| Bajty AFS Transferred\Uploaded bajty/s | Počet odeslaných bajtů za sekundu |
| Bajty AFS Transferred\Total bajty/s | Celkem bajtů za sekundu (nahrávání a stahování). |
| Soubory Operations\Downloaded synchronizace AFS Sync/s | Počet stažených souborů za sekundu |
| Soubory Operations\Uploaded synchronizace AFS Sync/s | Počet odeslaných souborů za sekundu |
| Operace synchronizace souborů Operations\Total synchronizace AFS za sekundu | Celkový počet synchronizovaných souborů (nahrání a stažení). |

## <a name="alert-examples"></a>Příklady výstrah
V této části najdete příklady výstrah pro Azure File Sync.

  > [!Note]  
  > Pokud vytvoříte výstrahu a je příliš hlučná, upravte prahovou hodnotu a logiku výstrahy.
  
### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>Jak vytvořit výstrahu v případě, že stav koncového bodu serveru zobrazuje chybu na portálu

1. V **Azure Portal**přejděte do příslušné **služby synchronizace úložiště**. 
2. Přejděte do části **monitorování** a klikněte na **výstrahy**. 
3. Kliknutím na **+ nové pravidlo výstrahy** vytvořte nové pravidlo výstrahy. 
4. Kliknutím na **vybrat podmínku**nakonfigurujte podmínku.
5. V okně **Konfigurovat logiku signálu** klikněte v části Název signálu na **výsledek synchronizace relace** .  
6. Vyberte následující konfiguraci dimenzí: 
    - Název dimenze: **název koncového bodu serveru**  
    - Podnikatel **=** 
    - Hodnoty dimenzí: **všechny aktuální a budoucí hodnoty**  
7. Přejděte do **logiky výstrah** a proveďte následující: 
    - Prahová hodnota nastavená na **static** 
    - Operátor: **menší než** 
    - Typ agregace: **Maximum**  
    - Prahová hodnota: **1** 
    - Vyhodnocováno podle: členitosti agregace = **24 hodin** | Frekvence vyhodnocení = **každou hodinu** 
    - Klikněte na **Hotovo.** 
8. Kliknutím na **Vybrat skupinu akcí** přidejte skupinu akcí (E-mail, SMS atd.) k výstraze buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.
9. Vyplňte **Podrobnosti výstrahy** , jako je **název pravidla výstrahy**, **Popis** a **závažnost**.
10. Klikněte na **Vytvořit pravidlo upozornění**. 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>Jak vytvořit výstrahu, pokud se soubory nedaří synchronizovat se serverem nebo koncovým bodem cloudu

1. V **Azure Portal**přejděte do příslušné **služby synchronizace úložiště**. 
2. Přejděte do části **monitorování** a klikněte na **výstrahy**. 
3. Kliknutím na **+ nové pravidlo výstrahy** vytvořte nové pravidlo výstrahy. 
4. Kliknutím na **vybrat podmínku**nakonfigurujte podmínku.
5. V okně **Konfigurovat logiku signálu** klikněte v části Název signálu na **soubory, které se nesynchronizují** .  
6. Vyberte následující konfiguraci dimenzí: 
     - Název dimenze: **název koncového bodu serveru**  
     - Podnikatel **=** 
     - Hodnoty dimenzí: **všechny aktuální a budoucí hodnoty**  
7. Přejděte do **logiky výstrah** a proveďte následující: 
     - Prahová hodnota nastavená na **static** 
     - Operátor: je **větší než** 
     - Typ agregace: **celkem**  
     - Prahová hodnota: **100** 
     - Vyhodnoceno v závislosti na: členitosti agregace = **5 minut** | Frekvence vyhodnocení = **každých 5 minut** 
     - Klikněte na **Hotovo.** 
8. Kliknutím na **Vybrat skupinu akcí** přidejte skupinu akcí (E-mail, SMS atd.) k výstraze buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.
9. Vyplňte **Podrobnosti výstrahy** , jako je **název pravidla výstrahy**, **Popis** a **závažnost**.
10. Klikněte na **Vytvořit pravidlo upozornění**. 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>Jak vytvořit výstrahu v případě, že se zaregistrovaným serverem nedaří komunikovat se službou synchronizace úložiště

1. V **Azure Portal**přejděte do příslušné **služby synchronizace úložiště**. 
2. Přejděte do části **monitorování** a klikněte na **výstrahy**. 
3. Kliknutím na **+ nové pravidlo výstrahy** vytvořte nové pravidlo výstrahy. 
4. Kliknutím na **vybrat podmínku**nakonfigurujte podmínku.
5. V okně **Konfigurovat logiku signálu** klikněte v části Název signálu na **stav online server** .  
6. Vyberte následující konfiguraci dimenzí: 
     - Název dimenze: **název serveru**  
     - Podnikatel **=** 
     - Hodnoty dimenzí: **všechny aktuální a budoucí hodnoty**  
7. Přejděte do **logiky výstrah** a proveďte následující: 
     - Prahová hodnota nastavená na **static** 
     - Operátor: **menší než** 
     - Typ agregace: **Maximum**  
     - Prahová hodnota (v bajtech): **1** 
     - Vyhodnoceno na základě členitosti agregace = **1 hodina** | Frekvence vyhodnocení = **každých 30 minut** 
        - Všimněte si, že metriky se odesílají do Azure Monitor každých 15 až 20 minut. Nenastavujte **četnost vyhodnocení** na méně než 30 minut (vygeneruje falešná upozornění).
     - Klikněte na **Hotovo.** 
8. Kliknutím na **Vybrat skupinu akcí** přidejte skupinu akcí (E-mail, SMS atd.) k výstraze buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.
9. Vyplňte **Podrobnosti výstrahy** , jako je **název pravidla výstrahy**, **Popis** a **závažnost**.
10. Klikněte na **Vytvořit pravidlo upozornění**. 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>Jak vytvořit výstrahu v případě, že velikost volání vrstev cloudu překročila 500GiB za den

1. V **Azure Portal**přejděte do příslušné **služby synchronizace úložiště**. 
2. Přejděte do části **monitorování** a klikněte na **výstrahy**. 
3. Kliknutím na **+ nové pravidlo výstrahy** vytvořte nové pravidlo výstrahy. 
4. Kliknutím na **vybrat podmínku**nakonfigurujte podmínku.
5. V okně **Konfigurovat logiku signálu** klikněte v části Název signálu na možnost **navrácení úrovně cloudu na velikost** .  
6. Vyberte následující konfiguraci dimenzí: 
     - Název dimenze: **název serveru**  
     - Podnikatel **=** 
     - Hodnoty dimenzí: **všechny aktuální a budoucí hodnoty**  
7. Přejděte do **logiky výstrah** a proveďte následující: 
     - Prahová hodnota nastavená na **static** 
     - Operátor: je **větší než** 
     - Typ agregace: **celkem**  
     - Prahová hodnota (v bajtech): **67108864000** 
     - Vyhodnocováno podle: členitosti agregace = **24 hodin** | Frekvence vyhodnocení = **každou hodinu** 
    - Klikněte na **Hotovo.** 
8. Kliknutím na **Vybrat skupinu akcí** přidejte skupinu akcí (E-mail, SMS atd.) k výstraze buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.
9. Vyplňte **Podrobnosti výstrahy** , jako je **název pravidla výstrahy**, **Popis** a **závažnost**.
10. Klikněte na **Vytvořit pravidlo upozornění**. 

## <a name="next-steps"></a>Další kroky
- [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
- [Zvážení brány firewall a nastavení proxy serveru](storage-sync-files-firewall-and-proxy.md)
- [Nasazení Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)
- [Řešit problémy se Synchronizací souborů Azure](storage-sync-files-troubleshoot.md)
- [Nejčastější dotazy k souborům Azure](storage-files-faq.md)
