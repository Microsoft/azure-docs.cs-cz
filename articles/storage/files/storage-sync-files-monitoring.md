---
title: Azure File Sync monitorování | Microsoft Docs
description: Jak monitorovat Azure File Sync.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0232a0c6526d6dcdfec86dedec437c71e7e21080
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2020
ms.locfileid: "85515205"
---
# <a name="monitor-azure-file-sync"></a>Sledování služby Synchronizace souborů Azure

Pomocí Azure File Sync můžete centralizovat sdílené složky ve vaší organizaci ve službě soubory Azure a zároveň udržet flexibilitu, výkon a kompatibilitu místního souborového serveru. Synchronizace souborů Azure transformuje Windows Server na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít libovolný protokol, který je dostupný na Windows serveru, včetně SMB, NFS a FTPS. Můžete mít tolik mezipamětí, kolik potřebujete po celém světě.

Tento článek popisuje, jak monitorovat nasazení Azure File Sync pomocí Azure Monitor, služby synchronizace úložiště a Windows serveru.

V tuto chvíli jsou k dispozici následující možnosti monitorování.

## <a name="azure-monitor"></a>Azure Monitor

Pomocí [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) můžete zobrazit metriky a nakonfigurovat výstrahy pro synchronizaci, vrstvení cloudu a připojení k serveru.  

### <a name="metrics"></a>Metriky

Metriky pro Azure File Sync jsou ve výchozím nastavení povolené a odesílají se Azure Monitor každých 15 minut.

Pokud chcete v Azure Monitor zobrazit metriky Azure File Sync, vyberte typ prostředku **služby synchronizace úložiště** .

V Azure Monitor jsou k dispozici následující metriky pro Azure File Sync:

| Název metriky | Popis |
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

Pokud chcete v Azure Monitor nakonfigurovat výstrahy, vyberte službu synchronizace úložiště a pak vyberte [metriku Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) , která se má pro výstrahu použít.  

V následující tabulce jsou uvedeny příklady scénářů, které je třeba monitorovat, a správnou metriku pro použití výstrahy:

| Scénář | Metrika, která se má použít pro upozornění |
|-|-|
| Stav koncového bodu serveru na portálu = chyba | Výsledek relace synchronizace |
| Neúspěšné synchronizace souborů na koncový bod serveru nebo cloudu | Soubory se nesynchronizují |
| U registrovaného serveru se nedaří komunikovat se službou synchronizace úložiště. | Online stav serveru |
| Velikost volání vrstvení cloudu překročila 500GiB za den.  | Velikost odvolání při vyvolání vrstvy cloudu |

Další informace o konfiguraci výstrah v Azure Monitor najdete v tématu [Přehled výstrah v Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="storage-sync-service"></a>Služba synchronizace úložiště

Pokud chcete zobrazit registrovaný stav serveru, stav koncového bodu serveru a metriky, přečtěte si v Azure Portal službu synchronizace úložiště. Stav registrovaného serveru můžete zobrazit v okně **registrované servery** a stavu koncového bodu serveru v okně **synchronizace skupin** .

### <a name="registered-server-health"></a>Stav registrovaného serveru

- Pokud je stav **registrovaného serveru** **online**, server úspěšně komunikuje se službou.
- Pokud je stav **registrovaného serveru** **zobrazený v režimu offline**, ověřte, zda je spuštěn proces sledování synchronizace úložiště (AzureStorageSyncMonitor.exe) na serveru. Pokud je server za bránou firewall nebo proxy, přečtěte si [Tento článek](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) , kde můžete nakonfigurovat bránu firewall a proxy server.

### <a name="server-endpoint-health"></a>Stav koncového bodu serveru

- Stav koncového bodu serveru na portálu je založen na událostech synchronizace zaznamenaných v protokolu událostí telemetrie na serveru (ID 9102 a 9302). Pokud relace synchronizace není úspěšná kvůli přechodné chybě, třeba zrušení změn, může se synchronizace pořád zobrazovat na portálu, pokud aktuální relace synchronizace probíhá. K určení, jestli se soubory používají, se používá událost s ID 9302. Další informace najdete v tématu [synchronizace stavu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) a [průběhu synchronizace](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Pokud se v portálu zobrazuje chyba synchronizace, protože synchronizace neprobíhá, přečtěte si pokyny v [dokumentaci k řešení potíží](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) .

### <a name="metric-charts"></a>Grafy metrik

- Na portálu služby synchronizace úložiště se mohou zobrazit následující grafy metrik:

  | Název metriky | Popis | Název okna |
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

Na Windows serveru můžete zobrazit vrstvení cloudu, zaregistrovaný Server a synchronizovat stav.

### <a name="event-logs"></a>Protokoly událostí

Pomocí protokolu událostí telemetrie na serveru můžete monitorovat stav zaregistrovaných serverů, synchronizace a vrstvení cloudu. Protokol událostí telemetrie se nachází v Prohlížeč událostí v části *aplikace a Services\Microsoft\FileSync\Agent*.

Stav synchronizace:

- Po dokončení relace synchronizace se protokoluje událost s ID 9102. Tuto událost použijte k určení, jestli jsou relace synchronizace úspěšné (**HRESULT = 0**), a pokud dojde k chybám synchronizace pro jednotlivé položky. Další informace najdete v dokumentaci ke [stavu synchronizace](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) a [chybách jednotlivých položek](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) .

  > [!Note]  
  > V některých případech se relace synchronizace nezdařila celkem nebo má nenulovou PerItemErrorCount. Pořád se ale dál dostanou a některé soubory se úspěšně synchronizují. Můžete to vidět v použitých polích, jako jsou AppliedFileCount, AppliedDirCount, AppliedTombstoneCount a AppliedSizeBytes. Tato pole vám sdělí, kolik relací bylo úspěšně dokončeno. Pokud vidíte, že se více relací synchronizace nedaří na řádku a že mají rostoucí počet použitých hodnot, podělte čas synchronizace, aby se mohl pokusit znovu před otevřením lístku podpory.

- Událost s ID 9302 se protokoluje každých 5 až 10 minut, pokud dojde k aktivní relaci synchronizace. Tuto událost použijte k určení, jestli aktuální relace synchronizace provádí průběh (**AppliedItemCount > 0**). Pokud synchronizace neprobíhá, relace synchronizace by se nakonec nezdařila a událost s ID 9102 bude protokolována s chybou. Další informace najdete v [dokumentaci průběh synchronizace](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Stav registrovaného serveru:

- Událost s ID 9301 se protokoluje každých 30 sekund, když server dotazuje službu pro úlohy. Pokud se GetNextJob dokončí se **stavem = 0**, server může komunikovat se službou. Pokud GetNextJob skončí s chybou, najdete pokyny v [dokumentaci k řešení problémů](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) .

Stav vrstvení cloudu:

- Pokud chcete monitorovat aktivitu vrstvení na serveru, použijte ID události 9003, 9016 a 9029 v protokolu událostí telemetrie, který je umístěný v Prohlížeč událostí v části *aplikace a Services\Microsoft\FileSync\Agent*.

  - Událost s ID 9003 poskytuje distribuci chyb pro koncový bod serveru. Příklad: celkový počet chyb a kód chyby. Jedna událost je protokolována podle kódu chyby.
  - ID události 9016 poskytuje duplikování výsledků pro svazek. Například: volné místo v procentech je, počet souborů, které byly v relaci opuštěné, a počet souborů, jejichž počet se nezdařil.
  - Událost s ID 9029 poskytuje duplikaci informací o relaci koncového bodu serveru. Například: počet souborů, které se v relaci pokoušely, počet souborů vrstvených v relaci a počet souborů, které jsou již vrstveny.
  
- Chcete-li monitorovat aktivitu odvolání na serveru, použijte ID události 9005, 9006, 9009 a 9059 v protokolu událostí telemetrie, který je umístěn v Prohlížeč událostí v části *aplikace a Services\Microsoft\FileSync\Agent*.

  - Událost s ID 9005 poskytuje spolehlivost odvolání pro koncový bod serveru. Například: celkový přístup k jedinečným souborům a celkový počet jedinečných souborů s neúspěšným přístupem.
  - Událost s ID 9006 poskytuje distribuci chyb odvolání pro koncový bod serveru. Například: celkový počet neúspěšných požadavků a kód chyby. Jedna událost je protokolována podle kódu chyby.
  - Událost s ID 9009 poskytuje informace o odvolání relace pro koncový bod serveru. Například: DurationSeconds, CountFilesRecallSucceeded a CountFilesRecallFailed.
  - Událost s ID 9059 poskytuje distribuci odvolání aplikace pro koncový bod serveru. Například: ShareId, název aplikace a TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Čítače výkonu

K monitorování synchronizační aktivity použijte Azure File Sync čítače výkonu na serveru.

Chcete-li zobrazit Azure File Sync čítače výkonu na serveru, spusťte nástroj sledování výkonu (Perfmon.exe). Čítače můžete najít v části **přenesené bajty AFS** a objekty **operace synchronizace AFS** .

V nástroji Sledování výkonu jsou k dispozici následující čítače výkonu pro Azure File Sync:

| Název Object\Counter výkonu | Popis |
|-|-|
| Bajty AFS Transferred\Downloaded bajty/s | Počet stažených bajtů za sekundu |
| Bajty AFS Transferred\Uploaded bajty/s | Počet odeslaných bajtů za sekundu |
| Bajty AFS Transferred\Total bajty/s | Celkem bajtů za sekundu (nahrávání a stahování). |
| Soubory Operations\Downloaded synchronizace AFS Sync/s | Počet stažených souborů za sekundu |
| Soubory Operations\Uploaded synchronizace AFS Sync/s | Počet odeslaných souborů za sekundu |
| Operace synchronizace souborů Operations\Total synchronizace AFS za sekundu | Celkový počet synchronizovaných souborů (nahrání a stažení). |

## <a name="next-steps"></a>Další kroky
- [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
- [Zvážení brány firewall a nastavení proxy serveru](storage-sync-files-firewall-and-proxy.md)
- [Nasazení Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)
- [Řešení problémů se Synchronizací souborů Azure](storage-sync-files-troubleshoot.md)
- [Nejčastější dotazy k souborům Azure](storage-files-faq.md)
