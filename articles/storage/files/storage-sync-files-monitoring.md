---
title: Monitorování synchronizace souborů Azure | Dokumenty společnosti Microsoft
description: Jak sledovat Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ac09f9b59bc6f47adc9311cc910352c1a0d73b5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699286"
---
# <a name="monitor-azure-file-sync"></a>Sledování služby Synchronizace souborů Azure

Azure File Sync slouží k centralizaci sdílených složek vaší organizace v souborech Azure a současně zachovávejte flexibilitu, výkon a kompatibilitu místního souborového serveru. Synchronizace souborů Azure transformuje Windows Server na rychlou mezipaměť sdílené složky Azure. K místnímu přístupu k datům můžete použít libovolný protokol, který je k dispozici v systému Windows Server, včetně protokolů SMB, NFS a FTPS. Můžete mít tolik cache, kolik potřebujete po celém světě.

Tento článek popisuje, jak sledovat nasazení synchronizace souborů Azure pomocí Azure Monitor, Storage Sync Service a Windows Server.

V současné době jsou k dispozici následující možnosti monitorování.

## <a name="azure-monitor"></a>Azure Monitor

Azure [Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) slouží k zobrazení metrik a konfiguraci výstrah pro synchronizaci, vrstvení v cloudu a připojení k serveru.  

### <a name="metrics"></a>Metriky

Metriky pro Azure File Sync jsou ve výchozím nastavení povolené a odesílají se do Azure Monitoru každých 15 minut.

Pokud chcete zobrazit metriky Azure File Sync v Azure Monitoru, vyberte typ prostředku **Služby synchronizace úložiště.**

V Azure Monitoru jsou k dispozici následující metriky pro Azure File Sync:

| Název metriky | Popis |
|-|-|
| Synchronizované bajty | Velikost přenesených dat (nahrávání a stahování).<br><br>Jednotka: Bajty<br>Typ agregace: Součet<br>Použitelné dimenze: Název koncového bodu serveru, Směr synchronizace, Název skupiny synchronizace |
| Vyvolání vrstvení cloudu | Velikost stažených dat.<br><br>**Poznámka:** Tato metrika bude v budoucnu odebrána. Pomocí metriky velikosti vyvolání vrstvení cloudu můžete sledovat velikost stažených dat.<br><br>Jednotka: Bajty<br>Typ agregace: Součet<br>Použitelná dimenze: Název serveru |
| Velikost vyvolání vrstvení v cloudu | Velikost stažených dat.<br><br>Jednotka: Bajty<br>Typ agregace: Součet<br>Použitelná dimenze: Název serveru, Název skupiny synchronizace |
| Velikost vyvolání vrstvení v cloudu podle aplikace | Velikost dat stažených aplikací.<br><br>Jednotka: Bajty<br>Typ agregace: Součet<br>Použitelná dimenze: Název aplikace, Název serveru, Název skupiny synchronizace |
| Propustnost odvolání vrstvení v cloudu | Velikost propustnost vyvolání dat.<br><br>Jednotka: Bajty<br>Typ agregace: Součet<br>Použitelná dimenze: Název serveru, Název skupiny synchronizace |
| Nesynchronizované soubory | Počet souborů, které se nedaří synchronizovat.<br><br>Jednotka: Počet<br>Typ agregace: Součet<br>Použitelné dimenze: Název koncového bodu serveru, Směr synchronizace, Název skupiny synchronizace |
| Synchronizované soubory | Počet přenesených souborů (upload a download).<br><br>Jednotka: Počet<br>Typ agregace: Součet<br>Použitelné dimenze: Název koncového bodu serveru, Směr synchronizace, Název skupiny synchronizace |
| Stav online serveru | Počet prezenčních signálů přijatých ze serveru.<br><br>Jednotka: Počet<br>Typ agregace: Maximum<br>Použitelná dimenze: Název serveru |
| Výsledek synchronizace relace | Výsledek synchronizace (1 = úspěšná synchronizace relace; 0 = neúspěšná relace synchronizace)<br><br>Jednotka: Počet<br>Typy agregace: Maximum<br>Použitelné dimenze: Název koncového bodu serveru, Směr synchronizace, Název skupiny synchronizace |

### <a name="alerts"></a>Výstrahy

Pokud chcete nakonfigurovat výstrahy ve službě Azure Monitor, vyberte službu synchronizace úložiště a pak vyberte [metriku Synchronizace souborů Azure,](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) která se má použít pro výstrahu.  

V následující tabulce jsou uvedeny některé ukázkové scénáře, které je třeba sledovat, a správná metrika, kterou se má pro výstrahu použít:

| Scénář | Metrika, která se má použít pro výstrahu |
|-|-|
| Stav koncového bodu serveru na portálu = Chyba | Výsledek synchronizace relace |
| Soubory se nedaří synchronizovat se serverem nebo cloudovým koncovým bodem | Nesynchronizované soubory |
| Registrovanému serveru se nedaří komunikovat se službou Synchronizace úložiště. | Stav online serveru |
| Velikost vyvolání vrstvení cloudu překročila 500 GiB za den  | Velikost vyvolání vrstvení v cloudu |

Další informace o konfiguraci výstrah v Azure Monitoru najdete v [tématu Přehled výstrah v Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="storage-sync-service"></a>Služba synchronizace úložiště

Chcete-li zobrazit registrované stav serveru, stav koncového bodu serveru a metriky, přejděte na službu Synchronizace úložiště na webu Azure Portal. Stav registrovaného serveru můžete zobrazit v okně **Registrované servery** a stavu koncového bodu serveru v okně **Skupiny synchronizace.**

### <a name="registered-server-health"></a>Stav registrovaného serveru

- Pokud je stav **Registrovaný server** **online**, server úspěšně komunikuje se službou.
- Pokud se stav **Registrovaného serveru** **zobrazuje offline**, ověřte, zda je spuštěn proces Sledování synchronizace úložiště (AzureStorageSyncMonitor.exe) na serveru. Pokud je server za bránou firewall nebo proxy serverem, nakonfigurujete bránu firewall a proxy server v [tomto článku.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy)

### <a name="server-endpoint-health"></a>Stav koncového bodu serveru

- Stav koncového bodu serveru na portálu je založen na událostech synchronizace, které jsou zaznamenány v protokolu událostí telemetrie na serveru (ID 9102 a 9302). Pokud se relace synchronizace nezdaří z důvodu přechodné chyby, jako je například chyba zrušena, synchronizace může stále vypadat v pořádku na portálu, dokud aktuální relace synchronizace probíhá. ID události 9302 se používá k určení, zda jsou soubory použity. Další informace naleznete v [tématu synchronizace stavu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) a [průběh synchronizace](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Pokud portál zobrazuje chybu synchronizace, protože synchronizace neprobíhá, přečtěte si pokyny v dokumentaci k [řešení potíží.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)

### <a name="metric-charts"></a>Metrické grafy

- Následující metrické grafy jsou viditelné na portálu Služby synchronizace úložiště:

  | Název metriky | Popis | Název čepele |
  |-|-|-|
  | Synchronizované bajty | Velikost přenesených dat (nahrávání a stahování) | Skupina Synchronizace, koncový bod serveru |
  | Vyvolání vrstvení cloudu | Velikost stažených dat | Registrované servery |
  | Nesynchronizované soubory | Počet souborů, které se nedaří synchronizovat | Koncový bod serveru |
  | Synchronizované soubory | Počet přenesených souborů (upload a download) | Skupina Synchronizace, koncový bod serveru |
  | Stav online serveru | Počet prezenčních signálů přijatých ze serveru | Registrované servery |

- Další informace najdete v tématu [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Grafy na portálu služby Synchronizace úložiště mají časový rozsah 24 hodin. Chcete-li zobrazit různé časové rozsahy nebo dimenze, použijte Azure Monitor.

## <a name="windows-server"></a>Windows Server

V systému Windows Server můžete zobrazit vrstvení cloudu, registrovaný server a stav synchronizace.

### <a name="event-logs"></a>Protokoly událostí

Pomocí protokolu událostí telemetrie na serveru můžete monitorovat stav registrovaného serveru, synchronizace a vrstvení v cloudu. Protokol událostí telemetrie je umístěn v Prohlížeči událostí v části *Aplikace a služby\Microsoft\FileSync\Agent*.

Stav synchronizace:

- ID události 9102 je zaznamenána po dokončení relace synchronizace. Pomocí této události můžete určit, zda jsou relace synchronizace úspěšné (**HResult = 0**) a zda existují chyby synchronizace pro položce. Další informace naleznete v dokumentaci k [chybám stavu synchronizace](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) a [za položku.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)

  > [!Note]  
  > Někdy synchronizační relace se nezdaří celkově nebo mají nenulový PerItemErrorCount. Však stále dopředu pokrok a některé soubory úspěšně synchronizovat. Můžete to vidět v použitých polích, jako je AppliedFileCount, AppliedDirCount, AppliedTombstoneCount a AppliedSizeBytes. V těchto polích se najdete v tom, jak velká část relace byla úspěšná. Pokud se v řadě zobrazí více synchronizačních relací a mají rostoucí počet použitých, podejte čas synchronizace, abyste to zkusili znovu před otevřením lístku podpory.

- ID události 9302 je zaznamenána každých 5 až 10 minut, pokud je aktivní synchronizace relace. Pomocí této události můžete určit, zda aktuální relace synchronizace probíhá (**AppliedItemCount > 0**). Pokud synchronizace neprovádí pokrok, relace synchronizace by měla nakonec selhat a id události 9102 bude zaznamenána s chybou. Další informace naleznete v dokumentaci k [průběhu synchronizace](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Stav registrovaného serveru:

- ID události 9301 je zaznamenána každých 30 sekund, když server dotazuje službu pro úlohy. Pokud GetNextJob skončí se **stavem = 0**, server je schopen komunikovat se službou. Pokud GetNextJob dokončí s chybou, podívejte se na [řešení potíží dokumentace](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) pokyny.

Stav vrstvení cloudu:

- Chcete-li sledovat aktivitu vrstvení na serveru, použijte id události 9003, 9016 a 9029 v protokolu událostí telemetrie, který je umístěn v Prohlížeči událostí v části *Aplikace a služby\Microsoft\FileSync\Agent*.

  - ID události 9003 poskytuje distribuci chyb pro koncový bod serveru. Například: Celkový počet chyb a ErrorCode. Jedna událost je zaznamenána na kód chyby.
  - ID události 9016 poskytuje výsledky ghostingu pro svazek. Příklad: Procento volného místa je, Počet souborů ghosted v relaci a počet souborů se nezdařilo ghost.
  - ID události 9029 poskytuje informace o relaci ghostingu pro koncový bod serveru. Příklad: Počet pokusů o pokus o soubory v relaci, Počet souborů vrstvených v relaci a Počet souborů, které již byly vrstveny.
  
- Chcete-li sledovat aktivitu odvolání na serveru, použijte id události 9005, 9006, 9009 a 9059 v protokolu událostí telemetrie, který je umístěn v Prohlížeči událostí v části *Aplikace a služby\Microsoft\FileSync\Agent*.

  - ID události 9005 poskytuje spolehlivost vyvolání pro koncový bod serveru. Příklad: Celkový počet jedinečných souborů, ke kterým byl přistupován, a Celkový počet jedinečných souborů s neúspěšným přístupem.
  - ID události 9006 poskytuje distribuci chyb y vyvolání pro koncový bod serveru. Například: Celkový počet neúspěšných požadavků a ErrorCode. Jedna událost je zaznamenána na kód chyby.
  - ID události 9009 poskytuje informace o relaci odvolání pro koncový bod serveru. Například: DurationSeconds, CountFilesRecallSucceeded a CountFilesRecallFailed.
  - ID události 9059 poskytuje distribuci odvolání aplikace pro koncový bod serveru. Například: ShareId, Název aplikace a TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Čítače výkonu

K monitorování aktivity synchronizace použijte čítače výkonu synchronizace Azure File Sync na serveru.

Chcete-li zobrazit čítače výkonu Synchronizace souborů Azure na serveru, otevřete nástroj Sledování výkonu (Perfmon.exe). Čítače najdete pod objekty **AFS Bytes Transferred** a **AFS Sync Operations.**

Následující čítače výkonu pro Azure File Sync jsou k dispozici v sledování výkonu:

| Objekt výkonu\Název čítače | Popis |
|-|-|
| Přenesené bajty AFS\Stažené bajty/s | Počet stažených bajtů za sekundu. |
| Přenesené bajty AFS\Nahrané bajty/s | Počet bajtů nahraných za sekundu. |
| Přenesené bajty AFS\Celkový počet bajtů/s | Celkový počet bajtů za sekundu (nahrávání a stahování). |
| Operace synchronizace AFS\Stažené synchronizační soubory/s | Počet stažených souborů za sekundu. |
| Operace synchronizace AFS\Nahrané synchronizační soubory/s | Počet souborů nahraných za sekundu. |
| Operace synchronizace AFS\Operace úplného souboru synchronizace/s | Celkový počet synchronizovaných souborů (nahrávání a stahování). |

## <a name="next-steps"></a>Další kroky
- [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
- [Zvažte nastavení brány firewall a proxy serveru.](storage-sync-files-firewall-and-proxy.md)
- [Nasazení Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)
- [Řešení problémů se Synchronizací souborů Azure](storage-sync-files-troubleshoot.md)
- [Nejčastější dotazy k souborům Azure](storage-files-faq.md)
