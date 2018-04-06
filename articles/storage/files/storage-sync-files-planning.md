---
title: Plánování nasazení synchronizace souboru Azure (preview) | Microsoft Docs
description: Zjistěte, co je potřeba zvážit při plánování nasazení služby Azure Files.
services: storage
documentationcenter: ''
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 9af1a82530d6e2d694f56322b7107796df73a2d5
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Plánování nasazení synchronizace souboru Azure (preview)
Pomocí synchronizace souboru Azure (preview) můžete centralizovat vaší organizace sdílené složky v souborech Azure, zatímco flexibilitu, výkonu a kompatibility pro místní souborový server. Synchronizace služby Azure souboru transformuje na rychlé mezipaměti Azure sdílené složky systému Windows Server. Můžete použít libovolný protokol, který je k dispozici v systému Windows Server pro přístup k datům místně, včetně protokolu SMB, systém souborů NFS a FTPS. Může mít libovolný počet mezipamětí, jako je třeba po celém světě.

Tento článek popisuje důležité informace týkající se nasazení služby Azure synchronizace souboru. Doporučujeme, abyste si přečetli taky [plánování nasazení Azure Files](storage-files-planning.md). 

## <a name="azure-file-sync-terminology"></a>Terminologie služby Azure synchronizace souboru
Před získáním na podrobné informace o plánování nasazení synchronizace souboru Azure, je důležité, abyste rozuměli technologiím.

### <a name="storage-sync-service"></a>Služba synchronizace úložiště
Synchronizační služba úložiště je prostředek Azure nejvyšší úrovně pro synchronizaci souborů Azure. Úložiště synchronizační služba prostředků je partnerské zařízení prostředků účtu úložiště a můžou být podobně nasazené do skupin prostředků Azure. Prostředek odlišné nejvyšší úrovně z prostředků účtu úložiště není nutná, protože synchronizační službu úložiště můžete vytvořit synchronizační relace s více účty úložiště prostřednictvím více skupin synchronizace. Předplatné může mít několik úložiště synchronizační služba prostředků nasazení.

### <a name="sync-group"></a>Skupina synchronizace
Synchronizace skupiny definuje topologie synchronizace pro určitou sadu souborů. Koncové body v rámci synchronizace skupiny jsou synchronizovány mezi sebou. Pokud máte například dvě odlišné skupiny souborů, které chcete spravovat pomocí Azure synchronizace souborů, by vytvořit dvě synchronizace skupiny a přidat ke každé skupině synchronizaci různými koncovými body. Synchronizační služba úložiště, může hostovat libovolný počet skupin synchronizace, kolik potřebujete.  

### <a name="registered-server"></a>Zaregistrovaný server
Objekt zaregistrovaný server představuje vztah důvěryhodnosti mezi serverem (nebo clusteru) a služby úložiště Sync. Počet serverů do instance úložiště synchronizační služby podle potřeby můžete zaregistrovat. Však serveru (nebo clusteru) může být zaregistrován u pouze jeden synchronizační služby úložiště v čase.

### <a name="azure-file-sync-agent"></a>Agent Azure synchronizace souboru
Agent Azure souboru Sync je ke stažení balíčku, který umožňuje systému Windows Server bude synchronizován se sdílenou složku Azure. Agent synchronizace souboru Azure má tři hlavní součásti: 
- **FileSyncSvc.exe**: na pozadí služby systému Windows, který je zodpovědný za monitorování změn na koncové body serveru a pro zahájení relace synchronizace do Azure.
- **StorageSync.sys**: filtrem systému souborů synchronizaci souborů Azure, která je zodpovědná za vrstvení soubory k Azure Files (když cloudu vrstvení je povoleno).
- **Rutiny prostředí PowerShell pro správu**: rutiny prostředí PowerShell, který používáte k interakci s poskytovatelem prostředků Microsoft.StorageSync Azure. Můžete najít v následujících umístěních (výchozí) toto:
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Koncový bod serveru
Koncový bod serveru představuje určitého umístění na serveru pro registrované, například do složky na serveru svazku. Víc koncových bodů serveru může existovat na stejném svazku, pokud se nepřekrývají jejich obory názvů (například `F:\sync1` a `F:\sync2`). Můžete nakonfigurovat zásady vrstvení cloud jednotlivě pro každý koncový bod serveru. V současné době není možné vytvořit koncový bod serveru pro kořenový adresář svazku (například `F:\` nebo `C:\myvolume`, pokud je svazek připojen jako přípojný bod).

> [!Note]  
> Jsou podporovány pouze nevyměnitelné svazky.  Jednotky namapované ze vzdálené sdílené složky se nepodporují pro koncový bod cestu k serveru.  Kromě toho může být umístěna koncový bod serveru v systému Windows, ale cloudu systémový svazek vrstvení není podporována na systémovém svazku.

Pokud přidáte umístění serveru, která má existující sadu souborů jako koncový bod serveru do skupiny synchronizace, tyto soubory jsou sloučeny s ostatními soubory, které již jsou v dalších koncových bodů v synchronizace skupiny.

### <a name="cloud-endpoint"></a>Koncového bodu cloudu
Koncový bod cloudu je Azure sdílené složky, která je součástí skupiny synchronizace. Synchronizace sdílené složky celý soubor Azure a sdílenou složku Azure můžou být členy koncového bodu jenom k jednomu cloudu. Sdílenou složku Azure proto může být členem jenom jedné skupiny synchronizace. Pokud přidáte Azure sdílené složky, která má existující sadu souborů jako koncový bod cloudu do skupiny synchronizace, existující soubory jsou sloučeny s ostatními soubory, které již jsou v dalších koncových bodů v synchronizace skupiny.

> [!Important]  
> Synchronizace služby Azure souboru podporuje změn sdílenou složkou Azure přímo. Ale veškeré změny provedené na sdílenou složkou Azure nejprve mají být zjišťované úlohou detekce změn v Azure synchronizace souboru. Úloha zjištění změn je zahájena pro koncový bod cloudu pouze jednou za 24 hodin. Kromě toho změny sdílenou složku Azure přes protokol REST nebude aktualizovat SMB čas poslední změny a nedostupné jako změnu synchronizace. Další informace najdete v tématu [nejčastější dotazy k Azure Files](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Vrstvení cloudu 
Cloud vrstvení je volitelná funkce synchronizace souborů Azure ve kterém zřídka používají nebo k němu přistupovat soubory větší než 64 KiB velikost může být rozvrstvena k Azure Files. Při vrstveny soubor synchronizace souboru Azure filtrem systému souborů (StorageSync.sys) nahradí soubor místně na ukazatel nebo bod rozboru. Bod rozboru představuje adresu URL k souboru v Azure Files. Vrstvený soubor má atribut "offline" nastavené v systému souborů NTFS, takže aplikace jiných výrobců můžete identifikovat vrstvené soubory. Když uživatel otevře soubor vrstvené, synchronizace souboru Azure bezproblémově vrátí data souborů ze souborů Azure bez uživatele, kteří potřebují vědět, že soubor není místně uložených v systému. Tato funkce se také nazývá správu hierarchických úložišť (HSM).

> [!Important]  
> Cloud vrstvení není podporována pro koncové body serveru na svazcích systému Windows.

## <a name="azure-file-sync-interoperability"></a>Interoperabilita Azure synchronizace souboru 
Tato část obsahuje synchronizace souboru Azure Interoperabilita s funkcí Windows serveru a role a řešení třetí strany.

### <a name="supported-versions-of-windows-server"></a>Podporované verze systému Windows Server
V současné době jsou podporované verze systému Windows Server pomocí synchronizace souboru Azure:

| Verze | Podporované SKU | Možnosti podporované nasazení |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter a Standard | Úplné (server s uživatelského rozhraní) |
| Windows Server 2012 R2 | Datacenter a Standard | Úplné (server s uživatelského rozhraní) |

Budoucí verze systému Windows Server bude přidán jako jejich vydání. Starší verze systému Windows mohou být přidány podle zpětnou vazbu od uživatelů.

> [!Important]  
> Doporučujeme zachovat všechny servery, které používáte s nástrojem Azure souboru Sync aktuální s nejnovějšími aktualizacemi ze služby Windows Update. 

### <a name="file-system-features"></a>Funkce systému souborů
| Funkce | Podpora stavu | Poznámky |
|---------|----------------|-------|
| Seznamy řízení přístupu (ACL) | Plně podporovány. | Seznamy ACL systému Windows se zachovají pomocí synchronizace souboru Azure a jsou vynucována ve Windows serveru na koncové body serveru. Seznamy ACL systému Windows (dosud nejsou) nepodporuje soubory Azure, pokud soubory se k nim přistupuje přímo v cloudu. |
| Pevné odkazy | Vynecháno | |
| Symbolické odkazy | Vynecháno | |
| Přípojné body | Částečně podporována. | Přípojné body může být kořenovém koncový bod serveru, ale budou se přeskočí, pokud jsou obsaženy v oboru názvů koncový bod serveru. |
| Spojovacích bodech | Vynecháno | Například distribuované DfrsrPrivate systému souborů a DFSRoots složky. |
| Body rozboru | Vynecháno | |
| Komprese NTFS | Plně podporovány. | |
| Zhuštěných souborů | Plně podporovány. | Synchronizace zhuštěných souborů (nejsou blokována), ale jejich synchronizaci do cloudu jako celého souboru. Pokud se obsah souboru se změní v cloudu (nebo na jiném serveru), soubor je již zhuštěných po stažení změn. |
| Alternativní datové proudy (reklamy) | Zachovají, ale nejsou synchronizované | Například není synchronizovaná značky klasifikace, které jsou vytvořené pomocí infrastruktury klasifikace souborů. Existující klasifikace značky na soubory na každém serveru koncových bodů nedotčené. |

> [!Note]  
> Jsou podporovány pouze svazky systému souborů NTFS. Odolný systém souborů FAT, FAT32 a jiných systémů souborů nejsou podporovány.

### <a name="files-skipped"></a>Souborů přeskočených
| Soubor nebo složku | Poznámka |
|-|-|
| Desktop.ini | Specifické pro systém souborů |
| ethumbs.db$ | Dočasný soubor pro miniatury |
| ~$\*.\* | Dočasný soubor Office |
| \*TMP. | Dočasný soubor |
| \*.laccdb | Zamykací soubor přístup DB|
| 635D02A9D91C401B97884B82B3BCDAEA.* ||
| \\Informacemi o systémovém svazku | Složky, které jsou specifické pro svazek |
| $RECYCLE. KOŠ| Složka |
| \\SyncShareState | Složka pro synchronizaci |

### <a name="failover-clustering"></a>Clustering převzetí služeb při selhání
Windows Server Failover Clustering podporuje Azure souboru Sync pro možnost "Souborový Server pro obecné použití" nasazení. Převzetí služeb při selhání není podporováno vytváření clusterů "Souborového serveru Škálovaného na více systémů pro data aplikací" (SOFS) nebo na sdílených svazků clusteru (CSV).

> [!Note]  
> Agent synchronizace souboru Azure musí být nainstalován na všech uzlech v clusteru převzetí služeb při selhání pro synchronizaci správně fungovat.

### <a name="data-deduplication"></a>Odstranění duplicitních dat
Pro svazky, které nemají cloudu vrstvení povoleno synchronizace souboru Azure podporuje Windows Server povoleným odstraněním duplicitních dat se na svazku. V současné době nepodporujeme spolupráci mezi Azure soubor synchronizaci s cloudem vrstvení povoleno a odstranění duplicitních dat.

### <a name="distributed-file-system-dfs"></a>Distribuovaný systém souborů (DFS)
Synchronizace služby Azure souboru podporuje Interoperabilita s obory názvů DFS (DFS-N) a replikace DFS (DFS-R) počínaje [agenta synchronizace souboru Azure 1.2](https://go.microsoft.com/fwlink/?linkid=864522).

**Obory názvů DFS (DFS-N)**: synchronizace souboru Azure je plně nepodporuje na serverech systému souborů DFS-N. Agent synchronizace souboru Azure můžete nainstalovat na jeden nebo více členů DFS-N pro synchronizaci dat mezi koncovými body serveru a koncového bodu cloudu. Další informace najdete v tématu [přehled oborů názvů DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replikace distribuovaného systému souborů (DFS-R)**: protože DFS-R a synchronizace souborů Azure jsou obě řešení replikace, ve většině případů, doporučujeme nahrazení DFS-R se synchronizací souboru Azure. Existuje ale několik scénářů, kdy budete chtít využívat společně systému souborů DFS-R a synchronizace souborů Azure:

- Migrujete ze systému souborů DFS-R nasazení k nasazení služby Azure synchronizace souboru. Další informace najdete v tématu [migraci nasazení replikace distribuovaného systému souborů (DFS-R) k synchronizaci souboru Azure](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Ne každý na místním serveru, který se musí kopii dat. můžete připojené přímo k Internetu.
- Větev servery slučování dat do jednoho rozbočovače serveru, pro který chcete používat Azure souboru Sync.

Synchronizace souboru Azure a systému souborů DFS-R fungovat vedle sebe:

1. Synchronizace služby Azure souboru cloudu vrstvení musí být zakázáno na svazcích s DFS-R replikované složky.
2. Koncové body serveru by se neměla konfigurovat na složky jen pro čtení replikace DFS-R.

Další informace najdete v tématu [Přehled replikace DFS](https://technet.microsoft.com/library/jj127250).

### <a name="antivirus-solutions"></a>Antivirových řešení
Vzhledem k tomu, že antivirový funguje tak, že kontrola soubory pro známého škodlivého kódu, může způsobit antivirového produktu odvolání vrstvené souborů. Protože vrstvené soubory mají atribut "offline" nastaven, doporučujeme poradě s dodavatelem softwaru se dozvíte, jak nakonfigurovat své řešení tak, aby přeskočil čtení offline soubory. 

Následující řešení zaručeně podporují přeskočení offline soubory:

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [Zabezpečení koncového bodu McAfee](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (viz "Pouze co je třeba kontrolovat" na stránce 90 PDF)
- [Kaspersky Anti-Virus](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Řešení zálohování
Třeba antivirový řešení může dojít k řešení zálohování odvolání vrstvené souborů. Doporučujeme používat řešení cloudového zálohování k zálohování sdílenou složkou Azure místo produktu pro zálohování na místě.

### <a name="encryption-solutions"></a>Řešení pro šifrování
Podpora pro šifrování řešení závisí na tom, jak jsou implementované. Synchronizace služby Azure souboru je znám pracovat:

- Šifrování nástrojem BitLocker
- Azure Information Protection, Azure Rights Management Services (Azure RMS) a služby Active Directory RMS

Synchronizace služby Azure souboru není znám pracovat:

- Systém souborů NTFS šifrované systém souborů (EFS Encrypting File)

Obecně platí Azure souboru synchronizace by měly podporovat Interoperabilita s řešeními šifrování, které se nacházejí níže systému souborů, jako je nástroj BitLocker a u řešení, které jsou implementované ve formátu souboru, například nástroje BitLocker. Nebyly provedeny žádné speciální interoperabilita řešení, které se nacházejí výše systému souborů (např. systém souborů EFS systému souborů NTFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Jiných řešení pro správu hierarchických úložišť (HSM)
Žádné jiné řešení modulu hardwarového zabezpečení se má použít s Azure synchronizace souboru.

## <a name="region-availability"></a>Dostupnost v oblastech
Synchronizace služby Azure souboru je k dispozici pouze v následujících oblastech ve verzi preview:

| Oblast | Umístění datového centra |
|--------|---------------------|
| Austrálie – východ | Nový Jižní Wales |
| Střední Kanada | Toronto |
| Východní Kanada | Québec |
| Střed USA | Iowa |
| Východní Asie | Hongkong |
| Východ USA | Virginie |
| Východní US2 | Virginie |
| Severní Evropa | Irsko |
| Jihovýchodní Asie | Singapur |
| Spojené království – jih | Londýn |
| Západní Evropa | Nizozemsko |
| Západní USA | Kalifornie |

Ve verzi preview podporujeme synchronizuje pouze s Azure sdílenou, který je ve stejné oblasti jako službu úložiště synchronizace.

## <a name="azure-file-sync-agent-update-policy"></a>Zásady aktualizace agenta Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Další postup
* [Zvažte nastavení brány firewall a proxy](storage-sync-files-firewall-and-proxy.md)
* [Plánování nasazení Azure Files](storage-files-planning.md)
* [Nasazení Azure souborů](storage-files-deployment-guide.md)
* [Nasazení Azure File synchronizace](storage-sync-files-deployment-guide.md)
