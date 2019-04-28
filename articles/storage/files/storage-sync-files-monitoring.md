---
title: Monitorování Azure File Sync | Dokumentace Microsoftu
description: Jak monitorovat Azure File Sync.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c0f19e3ea4f5952ac96b589fa267a2136c85e4f3
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759463"
---
# <a name="monitor-azure-file-sync"></a>Sledování služby Synchronizace souborů Azure

Azure File Sync umožňuje centralizovat sdílené složky organizace ve službě soubory Azure, při zachování flexibility, výkonu a kompatibility s místními souborového serveru. Azure File Sync transformuje serveru systému Windows na rychlou mezipaměť sdílené složky Azure. Můžete použít jakýkoli protokol dostupný ve Windows serveru pro přístup k datům místně, včetně SMB, NFS a FTPS. Můžete mít libovolný počet mezipamětí po celém světě potřebujete.

Tento článek popisuje, jak sledovat vaše nasazení Azure File Sync s použitím webu Azure portal a Windows Server.

Následující možnosti monitorování jsou aktuálně k dispozici.

## <a name="azure-portal"></a>portál Azure

Na webu Azure Portal můžete zobrazit stav registrovaný server, stav koncového bodu serveru (synchronizace stavu) a metriky.

### <a name="storage-sync-service"></a>Služba synchronizace úložiště

Chcete-li zobrazit registrovaný server stav, stav koncového bodu serveru a metriky, přejděte na službu synchronizace úložiště na webu Azure Portal. Můžete zobrazit stav registrovaného serveru v **registrované servery** blade a server stavu koncových bodů v **synchronizovat skupiny** okno.

Registrovaný server stavu:

- Pokud **registrovaný server** stav je **Online**, server se úspěšně komunikaci se službou.
- Pokud **registrovaný server** stav je **se zobrazí v režimu Offline**, ověřte, zda je spuštěn proces monitorování synchronizace úložiště (AzureStorageSyncMonitor.exe) na serveru. Pokud je za bránou firewall nebo proxy server, přečtěte si téma [v tomto článku](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) ke konfiguraci brány firewall a proxy serveru.

Stav koncového bodu serveru:

- Synchronizace událostí, které jsou zaznamenány v protokolu událostí Telemetrie na serveru (ID 9102 a 9302) podle stavu koncového bodu serveru na portálu. Pokud relace synchronizace selže z důvodu přechodných chyb, jako je chyba bylo zrušeno, synchronizace může stále zobrazovat v pořádku na portálu tak dlouho, dokud aktuální relace synchronizace je vidět pokrok. ID události 9302 slouží k určení, pokud soubory aplikují. Další informace najdete v tématu [synchronizace stavu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) a [synchronizovat průběh](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Pokud na portálu se zobrazí chyba synchronizace, protože synchronizace není vidět pokrok, přečtěte si článek [dokumentace k řešení problémů](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) pokyny.

Metriky:

- Zobrazit na portálu služby synchronizace úložiště jsou následující metriky:

  | Název metriky | Popis | Název okna |
  |-|-|-|
  | Bajty synchronizované | Objem dat přenášených (nahrávání a stahování) | Skupina synchronizace, koncový bod serveru |
  | Odvolání vrstvení cloudu | Velikost dat připomenout | Registrované servery |
  | Soubory se nesynchronizují | Počet souborů, které se nedaří synchronizovat | Koncový bod serveru |
  | Synchronizovat soubory | Počet souborů přenesených (nahrávání a stahování) | Skupina synchronizace, koncový bod serveru |
  | Online stav serveru | Počet prezenčních signálů přijatou ze serveru | Registrované servery |

- Další informace najdete v tématu [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Grafy na portálu služby synchronizace úložiště mají časový rozsah 24 hodin. Chcete-li zobrazit různé časové rozsahy nebo dimenze, použití Azure monitoru.

### <a name="azure-monitor"></a>Azure Monitor

Použití [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) monitorování synchronizace, vrstvení cloudu a připojení k serveru. Metriky pro Azure File Sync jsou ve výchozím nastavení povolené a jsou odeslány do Azure monitoru každých 15 minut.

Pokud chcete zobrazit metriky Azure File Sync ve službě Azure Monitor, vyberte **služby synchronizace úložiště** typ prostředku.

Tyto metriky pro Azure File Sync jsou k dispozici ve službě Azure Monitor:

| Název metriky | Popis |
|-|-|
| Bajty synchronizované | Množství přenesených dat (nahrávání a stahování).<br><br>Jednotka: B<br>Typ agregace: Součet<br>Příslušné dimenze: Server koncového bodu název, směr, synchronizace název skupiny synchronizace |
| Odvolání vrstvení cloudu | Velikost dat odvolána.<br><br>Jednotka: B<br>Typ agregace: Součet<br>Příslušné dimenze: Název serveru |
| Soubory se nesynchronizují | Počet souborů, které se nedaří synchronizovat.<br><br>Jednotka: Počet<br>Typ agregace: Součet<br>Příslušné dimenze: Server koncového bodu název, směr, synchronizace název skupiny synchronizace |
| Synchronizovat soubory | Počet souborů přenesených (nahrávání a stahování).<br><br>Jednotka: Počet<br>Typ agregace: Součet<br>Příslušné dimenze: Server koncového bodu název, směr, synchronizace název skupiny synchronizace |
| Online stav serveru | Počet prezenčních signálů přijatou ze serveru.<br><br>Jednotka: Počet<br>Typ agregace: Maximum<br>Příslušné dimenze: Název serveru |
| Výsledek relace synchronizace | Synchronizovat výsledek relace (1 = úspěšná synchronizace relace; 0 = selhání synchronizace relace)<br><br>Jednotka: Počet<br>Typ agregace: Maximum<br>Příslušné dimenze: Server koncového bodu název, směr, synchronizace název skupiny synchronizace |

## <a name="windows-server"></a>Windows Server

V systému Windows Server můžete zobrazit cloudové ovládání datových vrstev, registrovaný server a synchronizaci stavu.

### <a name="event-logs"></a>Protokoly událostí

Použijte protokol událostí Telemetrie na serveru pro monitorování registrovaný server synchronizace a stavu vrstvení cloudu. V protokolu událostí Telemetrie se nachází v prohlížeči událostí pod *aplikací a Services\Microsoft\FileSync\Agent*.

Stav synchronizace:

- Po dokončení relace synchronizace je protokolováno ID události 9102. Pomocí této události lze určit, jestli budou úspěšné relace synchronizace (**HResult = 0**) a pokud jsou jednotlivé položky chyby synchronizace. Další informace najdete v tématu [synchronizace stavu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) a [chyby za položku](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) dokumentaci.

  > [!Note]  
  > Někdy relace synchronizace celkové selhat nebo mít PerItemErrorCount nenulové. Však stále využívají postup směrem vpřed a některé soubory úspěšně synchronizovat. Vidíte to v polích použité jako AppliedFileCount, AppliedDirCount, AppliedTombstoneCount a AppliedSizeBytes. Tato pole zjistíte, jak velká část relace byla úspěšná. Je-li zobrazit více relací synchronizace selhat za sebou a mají rostoucí počet použit, zadejte čas synchronizace to chcete zkusit znovu, než otevřete lístek podpory.

- Pokud je v rámci activesyncu relace, je protokolováno ID události 9302 každých 5 až 10 minut. Pomocí této události lze zjistit, pokud aktuální relace synchronizace (**AppliedItemCount > 0**). Pokud synchronizace není vidět pokrok, relace synchronizace by nakonec selže a 9102 ID události se zaznamenají a došlo k chybě. Další informace najdete v tématu [synchronizovat průběh dokumentaci](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Registrovaný server stavu:

- Každých 30 sekund, když se server dotazuje službu pro úlohy je protokolováno ID události 9301. Pokud GetNextJob končí **stav = 0**, server je schopen komunikovat se službou. Pokud GetNextJob skončí s chybou, zkontrolujte, [dokumentace k řešení problémů](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) pokyny.

Stav vrstvení cloudu:

- K monitorování vrstvení aktivity na serveru, použijte 9003 ID události, události 9016 a 9029 v protokolu událostí Telemetrie, který je umístěn v prohlížeči událostí pod *aplikací a Services\Microsoft\FileSync\Agent*.

  - ID události 9003 poskytuje distribucí chyb pro koncový bod serveru. Příklad: Celkový počet chyb a kód chyby. Jednu událost je protokolována za kód chyby.
  - ID události 9016 poskytuje zástupných položek výsledky pro svazek. Příklad: Procento volného místa je počet souborů vytvořena zástupná položka v relaci, a počet souborů, které se nepodařilo ghost.
  - ID události 9029 poskytuje informace o relaci zástupných položek pro koncový bod serveru. Příklad: Počet souborů v této relaci, počet souborů, které se pokusil vrstvené v relaci a počet souborů, které již vrstvený.
  
- Chcete-li monitorovat aktivity spojené s vracením na serveru, použijte 9005 ID události, 9006, 9009 a 9059 v protokolu událostí Telemetrie, které se nacházejí v prohlížeči událostí v části *aplikací a Services\Microsoft\FileSync\Agent*.

  - ID události 9005 poskytuje spojené s vracením spolehlivost pro koncový bod serveru. Příklad: Celkový počet jedinečných souborů k němu přistupovat a celkový počet jedinečných souborů s neúspěšných přístupů.
  - ID události 9006 poskytuje distribuci chyby spojené s vracením pro koncový bod serveru. Příklad: Celkový počet neúspěšných požadavků a kód chyby. Jednu událost je protokolována za kód chyby.
  - ID události 9009 poskytuje informace o relaci odvolání pro koncový bod serveru. Příklad: DurationSeconds CountFilesRecallSucceeded a CountFilesRecallFailed.
  - ID události 9059 poskytuje aplikace spojené s vracením distribuce pro koncový bod serveru. Příklad: ShareId, název aplikace a TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Čítače výkonu

Použití čítačů výkonu Azure File Sync na serveru ke sledování aktivitu synchronizace.

Chcete-li zobrazit čítače výkonu Azure File Sync na serveru, otevřete sledování výkonu (Perfmon.exe). Můžete najít čítače pod **AFS bajtů přenesených** a **operace synchronizace AFS** objekty.

Následující čítače výkonu pro Azure File Sync jsou k dispozici v nástroji Sledování výkonu:

| Název Object\Counter výkonu | Popis |
|-|-|
| AFS bajtů Transferred\Downloaded bajty/s | Počet stažených bajtů za sekundu. |
| AFS bajtů Transferred\Uploaded bajty/s | Nahrát počet bajtů za sekundu. |
| AFS bajtů Transferred\Total bajty/s | Celkový počet bajtů za sekundu (nahrávání a stahování). |
| Synchronizace AFS Operations\Downloaded synchronizace souborů za sekundu | Počet souborů, které stáhnou za sekundu. |
| Synchronizace AFS Operations\Uploaded synchronizace souborů za sekundu | Počet souborů zpráv za sekundu. |
| AFS synchronizace Operations\Total synchronizace souboru operace/s | Celkový počet souborů synchronizované (nahrávání a stahování). |

## <a name="next-steps"></a>Další postup
- [Plánování nasazení služby Azure File Sync](storage-sync-files-planning.md)
- [Zvažte nastavení brány firewall a proxy](storage-sync-files-firewall-and-proxy.md)
- [Nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md)
- [Řešení potíží s Azure File Sync](storage-sync-files-troubleshoot.md)
- [Služba soubory Azure – nejčastější dotazy](storage-files-faq.md)
