---
title: Monitorování Azure File Sync | Dokumentace Microsoftu
description: Jak monitorovat Azure File Sync.
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/28/2019
ms.author: jeffpatt
ms.component: files
ms.openlocfilehash: 7b03860fbcfd3380b92343575e88bd9cc7be5c13
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198997"
---
# <a name="monitor-azure-file-sync"></a>Sledování služby Synchronizace souborů Azure

Azure File Sync umožňuje centralizovat sdílené složky organizace ve službě soubory Azure, při zachování flexibility, výkonu a kompatibility s místními souborového serveru. Azure File Sync transformuje serveru systému Windows na rychlou mezipaměť sdílené složky Azure. Můžete použít jakýkoli protokol dostupný ve Windows serveru pro přístup k datům místně, včetně SMB, NFS a FTPS. Můžete mít libovolný počet mezipamětí po celém světě potřebujete.

Tento článek popisuje, jak monitorovat Azure File Sync nasazení pomocí webu Azure portal a Windows Server.

Aktuálně jsou k dispozici následující možnosti monitorování:

## <a name="azure-portal"></a>portál Azure

Na webu Azure Portal můžete zobrazit stav registrovaný server, stav koncového bodu serveru (synchronizace stavu) a metriky.

### <a name="storage-sync-service"></a>Služba synchronizace úložiště

Chcete-li zobrazit registrovaném serveru a stav koncového bodu serveru, přejděte na službu synchronizace úložiště na webu Azure Portal. Stav serveru registrované jde zobrazit v okně registrované servery. Stav koncového bodu serveru jde zobrazit v okně skupiny synchronizace.

Registrovaný Server stavu
- Pokud stav registrovaný server není Online, server úspěšně komunikaci se službou.
- Je-li stav registrovaný server se zobrazí v režimu Offline, ověřte, zda že je spuštěn proces synchronizace monitorování úložiště (AzureStorageSyncMonitor.exe) na serveru. Pokud je za bránou firewall nebo proxy server, nakonfigurujte bránu firewall a proxy server za [dokumentaci](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy).

Stav koncového bodu serveru
- Synchronizace událostí, které jsou zaznamenány v protokolu událostí Telemetrie na serveru (ID 9102 a 9302) podle stavu koncového bodu serveru na portálu. Pokud relace synchronizace selže z důvodu přechodné chyby (například Chyba zrušeno), synchronizace může nadále zobrazovat v pořádku na portálu tak dlouho, dokud aktuální relace synchronizace je vidět pokrok (9302 ID událostí se používá k určení, pokud se používají soubory). Najdete v následující dokumentaci pro další informace: [Synchronizace stavu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [synchronizovat průběh](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Pokud na portálu se zobrazí chyba synchronizace by se synchronizace není provedete průběh, zkontrolujte, [dokumentace k řešení problémů](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) pokyny.

### <a name="azure-monitor"></a>Azure Monitor

Použití [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) monitorování synchronizace, vrstvení cloudu a připojení k serveru. Metriky pro Azure File Sync jsou ve výchozím nastavení povolené a jsou odeslány do Azure monitoru každých 15 minut.

Pokud chcete zobrazit metriky Azure File Sync ve službě Azure Monitor, vyberte typ prostředku služby synchronizace úložiště.

Tyto metriky pro Azure File Sync jsou k dispozici ve službě Azure Monitor:

| Název metriky | Popis |
|-|-|
| Bajty synchronizované | Množství přenesených dat (nahrávání a stahování).<br><br>Jednotka: B<br>Typ agregace: Součet<br>Příslušné dimenze: Server koncového bodu název, směr, synchronizace název skupiny synchronizace |
| Odvolání vrstvení cloudu | Velikost dat odvolána.<br><br>Jednotka: B<br>Typ agregace: Součet<br>Příslušné dimenze: Název serveru |
| Soubory se nesynchronizují | Počet souborů, které se nedaří synchronizovat.<br><br>Jednotka: Počet<br>Typ agregace: Součet<br>Příslušné dimenze: Server koncového bodu název, směr, synchronizace název skupiny synchronizace |
| Synchronizovat soubory | Počet souborů nahrání a stažení.<br><br>Jednotka: Počet<br>Typ agregace: Součet<br>Příslušné dimenze: Server koncového bodu název, směr, synchronizace název skupiny synchronizace |
| Prezenční signál serveru | Počet prezenčních signálů přijatou ze serveru.<br><br>Jednotka: Počet<br>Typ agregace: Maximum<br>Příslušné dimenze: Název serveru |
| Výsledek relace synchronizace | Synchronizovat výsledek relace (1 = úspěšná synchronizace relace; 0 = selhání synchronizace relace)<br><br>Jednotka: Počet<br>Typ agregace: Maximum<br>Příslušné dimenze: Server koncového bodu název, směr, synchronizace název skupiny synchronizace |

## <a name="windows-server"></a>Windows Server

V systému Windows Server můžete zobrazit cloudové ovládání datových vrstev, registrovaný server a synchronizaci stavu.

### <a name="event-logs"></a>Protokoly událostí

Použijte protokol událostí Telemetrie na serveru pro monitorování registrovaný server synchronizace a stavu vrstvení cloudu. V protokolu událostí Telemetrie se nachází v umístění Applications and Services\Microsoft\FileSync\Agent v prohlížeči událostí.

Stav synchronizace
- Po dokončení relace synchronizace je protokolováno ID události 9102. Tato událost má použít k určení, pokud relace synchronizace se úspěšně dokončit (HResult = 0) a pokud jsou jednotlivé položky chyby synchronizace. Najdete v následující dokumentaci pro další informace: [Synchronizace stavu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [chyby za položku](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

  > [!Note]  
  > Někdy relace synchronizace nezdaří, celkový nebo mít nenulovou PerItemErrorCount ale stále provádět postup směrem vpřed, se některé soubory úspěšně synchronizovat. To lze zobrazit v použité pole (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount a AppliedSizeBytes), které zjistíte, jak velká část relace je úspěšné. Pokud se zobrazí více relací synchronizace za sebou, které se nedaří, ale mají rostoucí počet použit, je třeba přiřadit čas synchronizace pokusit znovu před otevřením lístku podpory.

- Pokud je v rámci activesyncu relace, je protokolováno ID události 9302 každých 5 až 10 minut. Tato událost má použít k určení, pokud aktuální relace synchronizace je vidět pokrok (AppliedItemCount > 0). Pokud synchronizace není vidět pokrok, relace synchronizace by nakonec selže a 9102 ID události se zaznamenají a došlo k chybě. Najdete v následující dokumentaci pro další informace: [Průběh synchronizace](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)

Registrovaný Server stavu
- Každých 30 sekund, když se server dotazuje službu pro úlohy je protokolováno ID události 9301. Pokud se stavem dokončení GetNextJob = 0, server je schopen komunikovat se službou. Pokud GetNextJob skončí s chybou, zkontrolujte, [dokumentace k řešení problémů](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) pokyny.

Cloud Tiering Health
- K monitorování vrstvení aktivity na serveru, použijte 9003 ID události, události 9016 a 9029 v protokolu událostí Telemetrie (nachází se v umístění Applications and Services\Microsoft\FileSync\Agent v prohlížeči událostí).

  - ID události 9003 poskytuje distribucí chyb pro koncový bod serveru. Celkový počet chyb, kód chyby, apod. Všimněte si, že se jedna událost je protokolována za kód chyby.
  - ID události 9016 poskytuje zástupných položek výsledky pro svazek. Například volného místa, které zbývá, počet souborů, které vytvořena zástupná položka v relaci, počet souborů se nezdařilo. Chcete-li ghost, atd.
  - ID události 9029 poskytuje informace o relaci zástupných položek pro koncový bod serveru. Třeba počet souborů v této relaci, počet souborů, které se pokusil vrstvami v této relaci, počet souborů, které již vrstvené atd.
  
- Pokud chcete monitorovat aktivity spojené s vracením na serveru, použijte 9005 ID události, 9006, 9009 a 9059 v protokolu událostí Telemetrie (nachází se v umístění Applications and Services\Microsoft\FileSync\Agent v prohlížeči událostí).

  - ID události 9005 poskytuje spojené s vracením spolehlivost pro koncový bod serveru. Například celkový počet jedinečných souborů přístup, celkový počet jedinečných souborů s neúspěšných přístupů atd.
  - ID události 9006 poskytuje distribuci chyby spojené s vracením pro koncový bod serveru. Celkový počet neúspěšných požadavků, kód chyby, apod. Všimněte si, že se jedna událost je protokolována za kód chyby.
  - ID události 9009 poskytuje informace o relaci odvolání pro koncový bod serveru. Například DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, atd.
  - ID události 9059 poskytuje aplikace spojené s vracením distribuce pro koncový bod serveru. Například ShareId, název aplikace a TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Čítače výkonu

Použití čítačů výkonu Azure File Sync na serveru ke sledování aktivitu synchronizace.

Zobrazení výkonu Azure File Sync čítače na serveru, spusťte sledování výkonu (Perfmon.exe) a čítače lze nalézt v AFS bajtů přenesených a operace synchronizace AFS objekty.

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
