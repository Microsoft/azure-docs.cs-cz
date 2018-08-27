---
title: Plánování nasazení služby Azure File Sync | Dokumentace Microsoftu
description: Zjistěte, co vzít v úvahu při plánování nasazení služby soubory Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 1f75317882e803a40df065377ef75f8b6b753898
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918375"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Plánování nasazení Synchronizace souborů Azure
Azure File Sync umožňuje centralizovat sdílené složky organizace ve službě soubory Azure, při zachování flexibility, výkonu a kompatibility s místními souborového serveru. Azure File Sync transformuje serveru systému Windows na rychlou mezipaměť sdílené složky Azure. Můžete použít jakýkoli protokol dostupný ve Windows serveru pro přístup k datům místně, včetně SMB, NFS a FTPS. Můžete mít libovolný počet mezipamětí po celém světě potřebujete.

Tento článek popisuje důležité informace týkající se nasazení služby Azure File Sync. Doporučujeme vám, že si také přečíst [plánování nasazení služby soubory Azure](storage-files-planning.md). 

## <a name="azure-file-sync-terminology"></a>Terminologie služby Azure File Sync
Před získáním na podrobné informace o plánování nasazení služby Azure File Sync, je důležité, abyste rozuměli technologiím.

### <a name="storage-sync-service"></a>Služba synchronizace úložiště
Služba synchronizace úložiště je prostředek Azure nejvyšší úrovně pro Azure File Sync. Zdroj služby synchronizace úložiště je partnerské zařízení prostředek účtu úložiště a podobně je možné nasadit do skupin prostředků Azure. Distinct prostředku nejvyšší úrovně z prostředek účtu úložiště není nutná, protože služba synchronizace úložiště můžete vytvořit synchronizační relace s více účty úložiště pomocí více skupin synchronizace. Předplatné může mít více prostředky služby synchronizace úložiště, které jsou nasazené.

### <a name="sync-group"></a>Skupina synchronizace
Skupina synchronizace definuje topologie synchronizace pro sadu souborů. Koncové body v rámci skupiny synchronizace se udržovat synchronizované mezi sebou. Pokud máte například dvě různé sady souborů, které chcete spravovat pomocí služby Azure File Sync, by vytvořit dvě skupiny synchronizace a přidání různých koncových bodů do všech skupin synchronizace. Služba synchronizace úložiště může být hostitelem libovolný počet skupin synchronizace podle potřeby.  

### <a name="registered-server"></a>Zaregistrovaný server
Objekt registrovaný server představuje vztah důvěryhodnosti mezi serverem (nebo clusteru) a služba synchronizace úložiště. Může zaregistrovat libovolný počet serverů do instance služby synchronizace úložiště má. Ale serveru (nebo v clusteru) lze dokument zaregistrovat u pouze jedna služba synchronizace úložiště po jednom.

### <a name="azure-file-sync-agent"></a>Agenta Azure File Sync
Agenta Azure File Sync je ke stažení balíčku, který umožňuje synchronizovat se sdílenými složkami Azure Windows serveru. Agenta Azure File Sync má tři hlavní komponenty: 
- **FileSyncSvc.exe**: na pozadí služby Windows, který je zodpovědný za monitorování změn na koncové body serveru a pro inicializaci relace synchronizace do Azure.
- **StorageSync.sys**: filtr systému souborů Azure File Sync, který je zodpovědný za vrstvení soubory do služby soubory Azure (když cloudu ovládání datových vrstev je povolené).
- **Rutiny Powershellu pro správu**: rutiny Powershellu, které slouží k interakci s poskytovatelem prostředků Microsoft.StorageSync Azure. Můžete je vyhledat v následujících umístěních (výchozí):
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Koncový bod serveru
Koncový bod serveru představuje určitého umístění na registrovaném serveru, jako je například složka svazku serveru. Více koncových bodů serveru může existovat v jednom svazku, pokud své obory názvů nemusí být stejné (například `F:\sync1` a `F:\sync2`). Můžete nakonfigurovat zásady vrstvení cloudu jednotlivě pro každý koncový bod serveru. 

Můžete vytvořit koncový bod serveru prostřednictvím přípojný bod. Mějte na paměti, přípojné body v rámci koncový bod serveru se přeskočí.  

Můžete vytvořit koncový bod serveru na systémovém svazku, ale pokud tak učiníte, existují dvě omezení:
* Cloud není možné ovládání datových vrstev.
* Neprovádí se obor názvů rychlé obnovení (systém rychle přináší dolů celý obor názvů a pak spustí stránkám znovu vyvolat obsah).


> [!Note]  
> Jsou podporovány pouze nevyměnitelné svazky.  Jednotky mapovat ze vzdálené sdílené složky se nepodporují pro cestu koncového bodu serveru.  Kromě toho může být umístěna koncový bod serveru na Windows, ale cloudu systémový svazek ovládání datových vrstev se nepodporuje na systémovém svazku.

Pokud chcete přidat umístění serveru, který má existující sadu souborů jako koncový bod serveru ke skupině synchronizace, tyto soubory jsou sloučeny s další soubory, které již jsou v jiné koncové body ve skupině synchronizace.

### <a name="cloud-endpoint"></a>Koncový bod cloudu.
Koncový bod cloudu je sdílené složky Azure, který je součástí skupiny synchronizace. Sdílené složky synchronizace celého souboru Azure a sdílené složky Azure můžou být členy koncového bodu pouze jeden cloud. Proto se sdílenými složkami Azure může být členem jenom jedné skupiny synchronizace. Pokud chcete přidat sdílenou složku Azure, který má existující sadu souborů jako koncový bod cloudu ke skupině synchronizace, existující soubory jsou sloučeny s další soubory, které již jsou v jiné koncové body ve skupině synchronizace.

> [!Important]  
> Azure File Sync podporuje provádění změn přímo do sdílené složky Azure. Však všechny změny provedené na sdílenou složku Azure nejprve mají být zjišťované úlohou detekce změn v Azure File Sync. Pro koncový bod cloudu jenom jednou za 24 hodin se spustí úloha zjišťování změn. Kromě toho změny provedené do sdílené složky Azure přes protokol REST neaktualizuje SMB čas poslední změny a se projeví jako změnu synchronizace. Další informace najdete v tématu [soubory Azure – nejčastější dotazy](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Vrstvení cloudu 
Vrstvení cloudu je volitelná funkce služby Azure File Sync ve kterém zřídka používané nebo přistupovat soubory větší než 64 KiB velikosti může být rozvrstvena do služby soubory Azure. Pokud se vrstvený soubor, filtr systému souborů Azure File Sync (StorageSync.sys) nahradí soubor místně ukazatel nebo spojovacím bodem. Bod rozboru představuje adresu URL k souboru ve službě soubory Azure. Vrstvený soubor má atribut "v režimu offline" nastavit v systému souborů NTFS, aby aplikace jiných výrobců můžete identifikovat vrstvené soubory. Když uživatel otevře vrstvených sdílených, Azure File Sync bezproblémově vrátí data souboru z soubory Azure bez uživatele nepotřebuje vědět, že soubor se neukládá místně v systému. Tato funkce je také správu hierarchických úložišť (HSM).

> [!Important]  
> Cloud ovládání datových vrstev se nepodporuje pro koncové body serveru u svazků systému Windows.

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Požadavky na systém Azure File Sync a vzájemná funkční spolupráce 
Tato část popisuje požadavky na systém pro agenta Azure File Sync a vzájemná funkční spolupráce s funkcí Windows serveru a role a řešení třetích stran.

### <a name="system-requirements"></a>Systémové požadavky
- Serveru se systémem Windows Server 2012 R2 nebo Windows serveru 2016 

    | Verze | Podporované skladové položky | Možnosti podporovaného nasazení |
    |---------|----------------|------------------------------|
    | Windows Server 2016 | Datacenter a Standard | Úplné (server s uživatelským rozhraním) |
    | Windows Server 2012 R2 | Datacenter a Standard | Úplné (server s uživatelským rozhraním) |

    Budoucí verze Windows serveru se přidají při jejich vydání. Starší verze Windows mohou být přidány na základě zpětné vazby uživatelů.

- Server s minimálně 2GB paměti

    > [!Important]  
    > Pokud server běží na virtuálním počítači s povolenou dynamickou paměť, musí být virtuální počítač nakonfigurovaný s minimální hodnotě 2 048 MB paměti.
    
- Místně připojený svazek naformátovaný systémem souborů NTFS

> [!Important]  
> Doporučujeme všechny servery, které používáte s Azure File Sync aktuální s nejnovějšími aktualizacemi ze služby Windows Update. 

### <a name="file-system-features"></a>Funkce systému souborů
| Funkce | Podpora stavu | Poznámky |
|---------|----------------|-------|
| Seznamy řízení přístupu (ACL) | Plně podporované. | Seznamy ACL Windows jsou zachovány Azure File Sync a jsou vynucována ve Windows serveru na koncové body serveru. Seznamy ACL Windows (dosud nejsou) nepodporuje soubory Azure, pokud soubory jsou přístupné přímo v cloudu. |
| Pevné odkazy | Přeskočené | |
| Symbolické odkazy | Přeskočené | |
| Přípojné body | Částečně podporované | Přípojné body, může být kořenový koncový bod serveru, ale jejich se přeskočí, pokud jsou obsaženy v oboru názvů koncový bod serveru. |
| Spojení | Přeskočené | Například Distributed File System DfrsrPrivate a DFSRoots složek. |
| Body rozboru | Přeskočené | |
| Komprese NTFS | Plně podporované. | |
| Zhuštěné soubory | Plně podporované. | Synchronizace zhuštěných souborů (nejsou blokované), ale jejich synchronizaci do cloudu jako úplný soubor. Pokud obsah souboru změnit v cloudu (nebo na jiném serveru), soubor již není zhuštěné po stažení změny. |
| Alternativní datové proudy (reklamy) | Zachovány, ale není synchronizovaný | Například klasifikační značky, které jsou vytvořené pomocí infrastruktury klasifikace souborů nejsou synchronizovány. Existující značky klasifikace souborů na všech koncové body serveru nedotčené. |

> [!Note]  
> Podporují se jenom svazky systému souborů NTFS. ReFS, FAT, FAT32 a jiné systémy souborů nejsou podporovány.

### <a name="files-skipped"></a>Soubory přeskočen
| Soubor nebo složku | Poznámka |
|-|-|
| Desktop.ini | Specifické pro systém souborů |
| ethumbs.db$ | Dočasný soubor pro miniatury |
| ~$\*.\* | Dočasný soubor Office |
| \*TMP | Dočasný soubor |
| \*.laccdb | Zamykací soubor Access DB|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Interní synchronizace souboru|
| \\Informací o systémovém svazku | Složka specifická pro svazek |
| $RECYCLE. KOŠ| Složka |
| \\SyncShareState | Složka pro synchronizaci |

### <a name="failover-clustering"></a>Clustering převzetí služeb při selhání
Windows Server Failover Clustering je podporována službou Azure File Sync možnost nasazení "Souborový Server pro obecné použití". Clustering převzetí služeb při selhání se nepodporuje na "Horizontální navýšení kapacity souborový Server pro data aplikací" (SOFS) nebo na Clusterované sdílené svazky (CSV).

> [!Note]  
> Musí být agent Azure File Sync nainstalovaný na všech uzlech v clusteru převzetí služeb při selhání pro synchronizaci správně fungovat.

### <a name="data-deduplication"></a>Odstranění duplicitních dat
U svazků, které nemají povolené vrstvení cloudu Azure File Sync podporuje Windows Server povoleným odstraněním duplicitních dat se na svazku. V současné době nepodporujeme vzájemná funkční spolupráce mezi Azure File Sync s povoleno vrstvení cloudu a odstraňování duplicit.

### <a name="distributed-file-system-dfs"></a>Systém souborů DFS (DFS)
Azure File Sync podporuje zprostředkovatel komunikace s objekty s obory názvů DFS (DFS-N) a replikace DFS (DFS-R) počínaje [agenta Azure File Sync 1.2](https://go.microsoft.com/fwlink/?linkid=864522).

**Obory názvů DFS (DFS-N)**: na servery systému souborů DFS-N se plně podporuje Azure File Sync. Můžete nainstalovat agenta Azure File Sync na jeden nebo více členů systému souborů DFS-N, synchronizaci dat mezi koncové body serveru a koncový bod cloudu. Další informace najdete v tématu [přehledu oborů názvů DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replikace DFS (DFS-R)**: protože systému souborů DFS-R a Azure File Sync jsou obě řešení replikace, ve většině případů, doporučujeme nahradit systému souborů DFS-R Azure File Sync. Existuje však několik scénářů, kdy je vhodné používat společně systému souborů DFS-R a Azure File Sync:

- Migrujete ze systému souborů DFS-R nasazení k nasazení služby Azure File Sync. Další informace najdete v tématu [migrace replikace DFS (DFS-R) nasazení do Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Ne každá na místním serveru, která potřebuje kopii data souborů můžete připojené přímo k Internetu.
- Větev servery slučování dat do jedním centrálním serverem, pro kterou chcete použít Azure File Sync.

Azure File Sync a systému souborů DFS-R fungovat vedle sebe:

1. Azure File Sync cloudových vrstev musí být deaktivovány svazky systému souborů DFS-R replikované složky.
2. Koncové body serveru nemůže být konfigurována pro složky jen pro čtení replikace DFS-R.

Další informace najdete v tématu [Přehled replikace DFS](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Nástroj Sysprep
Pomocí nástroje sysprep na serveru, který má nainstalovaného agenta Azure File Sync není podporováno a může vést k neočekávaným výsledkům. Instalace a server registrace agenta se budou objevovat po nasazení bitové kopie serveru a dokončení zkrácené instalace nástroje sysprep.

### <a name="windows-search"></a>Windows Search
Pokud cloud na koncovém bodu serveru vrstvení je povoleno, soubory, které proběhne jsou vynechány a není indexované podle Windows Search. Bez vrstvené soubory jsou správně indexovány.

### <a name="antivirus-solutions"></a>Antivirová řešení
Protože antivirové ochrany v programu funguje tak, že prohledávání souborů známých škodlivý kód, může způsobit antivirový odvolání vrstvené soubory. Vzhledem k tomu, že vrstvené soubory mít nastaven atribut "offline", doporučujeme konzultaci s dodavatelem softwaru se naučíte konfigurovat jejich řešení Chcete-li přeskočit čtení offline soubory. 

Následující řešení zaručeně podporují, přeskakuje se offline soubory:

- [Program Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus)
    - Program Windows Defender automaticky přeskočí čtení souborů, které mají nastaven atribut offline. Jsme otestovali Defender a identifikovat jeden menší problém: Když přidáte server do existující skupiny synchronizace, soubory menší než 800 bajtů se odvolání (Stáhnout) na novém serveru. Tyto soubory zůstanou na novém serveru a nebude vrstvený, protože nesplňují požadavek vrstvení velikost (> 64 kb).
- [System Center Endpoint Protection (SCEP)](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus)
    - SCEP funguje stejně jako Defender; viz výše
- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [Zabezpečení koncových bodů McAfee](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (viz "Vše, co potřebujete k prohledávání" na stránce 90 PDF)
- [Kaspersky Anti-Virus](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Řešení zálohování
Jako jsou antivirové řešení řešení pro zálohování může způsobit odvolání vrstvené soubory. Doporučujeme použít zálohování sdílené složky Azure místo produktu pro zálohování místních řešení cloudového zálohování.

Pokud používáte místní záložní řešení, je třeba provést zálohování na serveru ve skupině synchronizace, který má zakázaný vrstvení cloudu. Při obnovování souborů v rámci umístění koncového bodu serveru, použijte možnost obnovení na úrovni souboru. Obnovit soubory se budou synchronizovat na všechny koncové body ve skupině synchronizace a existující soubory nahradí verzi obnovení ze zálohy.

> [!Note]  
> S ohledem na aplikace, na úrovni svazku a úplné možnosti obnovení (BMR) může vést k neočekávaným výsledkům a nejsou aktuálně podporovány. Obnovení, tyto možnosti budou podporované v budoucích vydáních.

### <a name="encryption-solutions"></a>Řešení šifrování
Podpora pro řešení šifrování závisí na tom, jak jsou implementované. Azure File Sync je známo, že pracovat:

- Šifrování nástroje BitLocker
- Azure Information Protection, Azure Rights Management Services (Azure RMS) a Active Directory RMS

Azure File Sync se ví, že pracovat:

- Systém souborů (EFS Encrypting File) zašifrované systémem souborů NTFS

Azure File Sync obecně by měly podporovat interoperabilitu s řešeními šifrování, které se nacházejí pod systému souborů, jako je například BitLocker a s řešeními, které jsou implementovány ve formátu souboru, jako je Azure Information Protection. Řešení, které se nacházejí nahoře systému souborů (jako je systém souborů EFS systému souborů NTFS) nebyly provedeny žádné speciální vzájemná funkční spolupráce.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Jiná řešení správy hierarchických úložišť (HSM)
Pomocí služby Azure File Sync je třeba použít jiná řešení modulu hardwarového zabezpečení.

## <a name="region-availability"></a>Dostupnost v oblastech
Azure File Sync je k dispozici pouze v těchto oblastech:

| Oblast | Umístění datového centra |
|--------|---------------------|
| Austrálie – východ | Nový Jižní Wales |
| Austrálie – jihovýchod | Victoria |
| Kanada – střed | Toronto |
| Kanada – východ | Québec |
| Střed Indie | Puné |
| USA – střed | Iowa |
| Východní Asie | Hongkong |
| USA – východ | Virginie |
| USA – východ 2 | Virginie |
| Severní Evropa | Irsko |
| Indie – jih | Čennaj |
| Jihovýchodní Asie | Singapur |
| Velká Británie – jih | Londýn |
| Spojené království – západ | Cardiff |
| Západní Evropa | Nizozemsko |
| USA – západ | Kalifornie |

Azure File Sync podporuje synchronizaci pouze u sdílené složky Azure, který je ve stejné oblasti jako služba synchronizace úložiště.

### <a name="azure-disaster-recovery"></a>Zotavení po havárii Azure
Pokud chcete chránit před ztrátou oblasti Azure, Azure File Sync se integruje s [redundance geograficky redundantní úložiště](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) možnost (GRS). Úložiště GRS funguje s použitím bloku asynchronní replikaci mezi úložiště v primární oblasti, pomocí kterého budete obvykle pracovat, a úložiště ve spárovaném sekundární oblasti. V případě havárie, což způsobí, že určitá oblast Azure pro go dočasně nebo trvale offline Microsoft převezme úložiště spárovaných oblastí. 

Pro podporu integrace převzetí služeb při selhání mezi geograficky redundantní úložiště a Azure File Sync, jsou všechny oblasti Azure File Sync spárovaná s sekundární oblasti, která odpovídá sekundární oblasti používané službou storage. Tyto páry jsou následující:

| Primární oblast      | Spárovaná oblast      |
|---------------------|--------------------|
| Austrálie – východ      | Austrálie – jihovýchod |
| Austrálie – jihovýchod | Austrálie – východ     |
| Kanada – střed      | Kanada – východ        |
| Kanada – východ         | Kanada – střed     |
| Střed Indie       | Indie – jih        |
| USA – střed          | Východní USA 2          |
| Východní Asie           | Jihovýchodní Asie     |
| USA – východ             | USA – západ            |
| Východ USA 2           | USA – střed         |
| Severní Evropa        | Západní Evropa        |
| Indie – jih         | Střed Indie      |
| Jihovýchodní Asie      | Východní Asie          |
| Velká Británie – jih            | Spojené království – západ            |
| Spojené království – západ             | Velká Británie – jih           |
| Západní Evropa         | Severní Evropa       |
| USA – západ             | USA – východ            |

## <a name="azure-file-sync-agent-update-policy"></a>Zásady aktualizace agenta Synchronizace souborů Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Další postup
* [Zvažte nastavení brány firewall a proxy](storage-sync-files-firewall-and-proxy.md)
* [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
* [Nasazení služby soubory Azure](storage-files-deployment-guide.md)
* [Nasazení služby Azure File Sync](storage-sync-files-deployment-guide.md)
