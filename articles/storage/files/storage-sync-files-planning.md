---
title: Plánování nasazení synchronizace souborů Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, co je třeba vzít v úvahu při plánování nasazení souborů Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0684f626553946619a0db2cd895df39576bd17b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255116"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Plánování nasazení Synchronizace souborů Azure
[Soubory Azure](storage-files-introduction.md) lze nasadit dvěma hlavními způsoby: přímým připojením sdílených složek Azure bez serveru nebo ukládáním sdílených složek Azure místně pomocí Azure File Sync. Kterou možnost nasazení zvolíte, změní věci, které je třeba zvážit při plánování nasazení. 

- **Přímé připojení sdílené složky Azure**: Vzhledem k tomu, že soubory Azure poskytují přístup s smb, můžete připojit sdílené složky Azure místně nebo v cloudu pomocí standardního klienta SMB dostupného ve Windows, macOS a Linuxu. Vzhledem k tomu, že sdílené složky Azure jsou bez serveru, nasazení pro produkční scénáře nevyžaduje správu souborového serveru nebo zařízení NAS. To znamená, že nemusíte používat softwarové opravy ani vyměňovat fyzické disky. 

- **Sdílení souborů Azure v mezipaměti místně pomocí Azure File Sync**: Azure File Sync umožňuje centralizovat sdílené složky vaší organizace v souborech Azure a současně zachovat flexibilitu, výkon a kompatibilitu místního souborového serveru. Azure File Sync transformuje místní (nebo cloudový) Windows Server do rychlé mezipaměti sdílené složky Azure. 

Tento článek řeší především aspekty nasazení pro nasazení Azure File Sync. Pokud chcete naplánovat nasazení sdílených složek Azure, které mají být přímo připojeny místním nebo cloudovým klientem, přečtěte si část [Plánování nasazení souborů Azure](storage-files-planning.md).

## <a name="management-concepts"></a>Koncepce řízení
Nasazení Azure File Sync má tři základní objekty správy:

- **Sdílená složka Azure**: Sdílená složka Azure je sdílená složka cloudového souboru bez serveru, která poskytuje *koncový bod cloudu* relace synchronizace souborů Azure. K souborům ve sdílené složce Azure se dá přistupovat přímo pomocí protokolu SMB nebo FileREST, i když doporučujeme především přistupovat k souborům prostřednictvím mezipaměti Windows Serveru, když se sdílená složka Azure používá s Azure File Sync. Důvodem je, že soubory Azure dnes postrádá efektivní mechanismus zjišťování změn, jako je Windows Server má, takže změny sdílené složky Azure přímo bude nějakou dobu trvat, než se rozšíří zpět na koncové body serveru.
- **Koncový bod serveru**: Cesta na Windows Serveru, která se synchronizuje se sdílenou složkou Azure. Může se jedná o určitou složku na svazku nebo v kořenovém adresáři svazku. Více koncových bodů serveru může existovat na stejném svazku, pokud se jejich obory názvů nepřekrývají.
- **Skupina synchronizace**: Objekt, který definuje vztah synchronizace mezi **koncovým bodem cloudu**nebo sdílenou složkou Azure a koncovým bodem serveru. Koncové body v rámci skupiny synchronizace se mezi sebou synchronizují. Pokud například máte dvě odlišné sady souborů, které chcete spravovat pomocí Azure File Sync, vytvoříte dvě skupiny synchronizace a přidáte různé koncové body do každé skupiny synchronizace.

### <a name="azure-file-share-management-concepts"></a>Koncepty správy sdílení souborů Azure
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Koncepty správy synchronizace souborů Azure
Skupiny synchronizace se nasazují do **služby Synchronizace úložiště**, což jsou objekty nejvyšší úrovně, které registrují servery pro použití s Azure File Sync a obsahují vztahy skupiny synchronizace. Prostředek služby Synchronizace úložiště je partnerem prostředku účtu úložiště a podobně se dá nasadit do skupin prostředků Azure. Služba synchronizace úložiště může vytvářet skupiny synchronizace, které obsahují sdílené složky Azure napříč více účty úložiště a více registrovanými servery Windows.

Před vytvořením skupiny synchronizace ve službě Synchronizace úložiště je nutné nejprve zaregistrovat systém Windows Server pomocí služby Synchronizace úložiště. Tím se vytvoří registrovaný objekt **serveru,** který představuje vztah důvěryhodnosti mezi serverem nebo clusterem a službou synchronizace úložiště. Chcete-li zaregistrovat službu synchronizace úložiště, musíte nejprve nainstalovat agenta Azure File Sync na server. Jednotlivého serveru nebo clusteru lze najednou zaregistrovat pouze u jedné služby synchronizace úložiště.

Skupina synchronizace obsahuje jeden koncový bod cloudu nebo sdílenou složku Azure a alespoň jeden koncový bod serveru. Objekt koncového bodu serveru obsahuje nastavení, která konfigurují funkci **vrstvení cloudu,** která poskytuje funkci ukládání do mezipaměti Azure File Sync. Aby bylo možné synchronizovat se sdílenou složkou Azure, musí být účet úložiště obsahující sdílenou složku Azure ve stejné oblasti Azure jako služba synchronizace úložiště.

### <a name="management-guidance"></a>Vedení řízení
Při nasazování Azure File Sync doporučujeme:

- Nasazení sdílené složky Azure 1:1 se sdílenými složkami windows. Objekt koncového bodu serveru poskytuje velkou míru flexibility při nastavení topologie synchronizace na straně serveru relace synchronizace. Chcete-li zjednodušit správu, vytvořte cestu ke koncovému bodu serveru odpovídající cestě ke sdílené složce systému Windows. 

- Používejte co nejméně služeb synchronizace úložiště. Tím se zjednoduší správa, pokud máte skupiny synchronizace, které obsahují více koncových bodů serveru, protože systém Windows Server lze zaregistrovat pouze do jedné služby synchronizace úložiště najednou. 

- Při nasazování sdílených složek Azure je třeba věnovat pozornost omezením viops účtu úložiště. V ideálním případě byste mapovat sdílení souborů 1:1 s účty úložiště, ale to nemusí být vždy možné z důvodu různých omezení a omezení, a to jak z vaší organizace, tak z Azure. Pokud není možné mít pouze jednu sdílenou složku nasazenou v jednom účtu úložiště, zvažte, které sdílené složky budou vysoce aktivní a které sdílené složky budou méně aktivní, aby se zajistilo, že nejžhavější sdílené složky nebudou vloženy do stejného účtu úložiště společně.

## <a name="windows-file-server-considerations"></a>Důležité informace o souborovém serveru systému Windows
Chcete-li povolit možnostsynchronizace v systému Windows Server, je nutné nainstalovat agenta Azure File Sync ke stažení. Agent Azure File Sync poskytuje dvě `FileSyncSvc.exe`hlavní součásti: , službu windows na pozadí, která je `StorageSync.sys`zodpovědná za sledování změn na koncových bodech serveru a zahájení synchronizace relací, a , filtr systému souborů, který umožňuje vrstvení cloudu a rychlé zotavení po havárii.  

### <a name="operating-system-requirements"></a>Požadavky na operační systém
Azure File Sync je podporovaná následujícími verzemi Windows Serveru:

| Version | Podporované skUs | Podporované možnosti nasazení |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datové centrum, standard a IoT | Úplné a jádro |
| Windows Server 2016 | Server datového centra, standardu a úložiště | Úplné a jádro |
| Windows Server 2012 R2 | Server datového centra, standardu a úložiště | Úplné a jádro |

Budoucí verze systému Windows Server budou přidány po jejich vydání.

> [!Important]  
> Doporučujeme udržovat všechny servery, které používáte s Azure File Sync aktuální s nejnovější aktualizace ze služby Windows Update. 

### <a name="minimum-system-resources"></a>Minimální systémové prostředky
Azure File Sync vyžaduje server, fyzický nebo virtuální, s alespoň jedním procesorem a minimálně 2 GiB paměti.

> [!Important]  
> Pokud je server spuštěn ve virtuálním počítači s povolenou dynamickou pamětí, virtuální počítač by měl být nakonfigurován s minimálně 2048 MiB paměti.

U většiny produkčních úloh nedoporučujeme konfigurovat synchronizační server Synchronizace souborů Azure pouze s minimálními požadavky. Další informace naleznete [v části Doporučené systémové prostředky.](#recommended-system-resources)

### <a name="recommended-system-resources"></a>Doporučené systémové prostředky
Stejně jako všechny funkce nebo aplikace serveru, požadavky na systémové prostředky pro Azure File Sync jsou určeny škálování nasazení; větší nasazení na serveru vyžadují větší systémové prostředky. Pro Azure File Sync škálování je určena počtem objektů v koncových bodech serveru a konve v datové sadě. Jeden server může mít koncové body serveru ve více skupinách synchronizace a počet objektů uvedených v následující tabulce účty pro celý obor názvů, který je připojen k serveru. 

Například koncový bod serveru A s 10 miliony objektů + koncový bod serveru B s 10 miliony objektů = 20 milionů objektů. Pro tento příklad nasazení doporučujeme 8 procesorů, 16 GiB paměti pro ustálený stav a (pokud je to možné) 48 GiB paměti pro počáteční migraci.
 
Data oboru názvů jsou uložena v paměti z důvodů výkonu. Z tohoto důvodu větší obory názvů vyžadují více paměti pro udržení dobrého výkonu a více změn vyžaduje více procesoru ke zpracování. 
 
V následující tabulce jsme poskytli jak velikost oboru názvů, tak převod na kapacitu pro typické sdílené složky pro obecné účely, kde je průměrná velikost souboru 512 KiB. Pokud jsou velikosti souborů menší, zvažte přidání další paměti pro stejnou kapacitu. Založte konfiguraci paměti na velikosti oboru názvů.

| Velikost oboru názvů - soubory & adresářů (miliony)  | Typická kapacita (TiB)  | Jádra procesoru  | Doporučená paměť (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (počáteční synchronizace)/ 2 (typická konve)      |
| 5        | 2.3     | 2        | 16 (počáteční synchronizace)/ 4 (typická konve)    |
| 10       | 4.7     | 4        | 32 (počáteční synchronizace)/ 8 (typická konve)   |
| 30       | 14.0    | 8        | 48 (počáteční synchronizace)/ 16 (typická konve)   |
| 50       | 23.3    | 16       | 64 (počáteční synchronizace)/ 32 (typická konve)  |
| 100*     | 46.6    | 32       | 128 (počáteční synchronizace)/ 32 (typická konve)  |

\*Synchronizace více než 100 milionů souborů & adresářů se v tuto chvíli nedoporučuje. Jedná se o měkký limit založený na našich testovaných prahových hodnotách. Další informace najdete [v tématu Azure Files scalability and performance targets](storage-files-scale-targets.md#azure-file-sync-scale-targets).

> [!TIP]
> Počáteční synchronizace oboru názvů je intenzivní operace a doporučujeme alokovat více paměti, dokud nebude počáteční synchronizace dokončena. To není nutné, ale může urychlit počáteční synchronizaci. 
> 
> Typické změny je 0,5 % oboru názvů, který se mění za den. Pro vyšší úrovně konve, zvažte přidání další procesoru. 

- Místně připojený svazek formátovaný systémem souborů NTFS.

### <a name="evaluation-cmdlet"></a>Rutina vyhodnocení
Před nasazením Azure File Sync, měli byste vyhodnotit, zda je kompatibilní s vaším systémem pomocí rutiny vyhodnocení Synchronizace souborů Azure. Tato rutina kontroluje možné problémy se systémem souborů a datovou sadou, například nepodporované znaky nebo nepodporovanou verzi operačního systému. Jeho kontroly pokrývají většinu, ale ne všechny níže uvedené funkce; Doporučujeme, abyste si pečlivě přečetli zbytek této části, abyste zajistili, že vaše nasazení proběhne hladce. 

Rutinu vyhodnocení lze nainstalovat instalací modulu Az PowerShell, který lze nainstalovat podle pokynů zde: [Instalace a konfigurace prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Využití  
Nástroj pro vyhodnocení můžete vyvolat několika různými způsoby: můžete provádět systémové kontroly, kontroly datové sady nebo obojí. Provedení kontrol systému i datové sady: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Chcete-li otestovat pouze datovou sadu:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Pouze testování požadavků na systém:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
Chcete-li zobrazit výsledky v CSV:
```powershell
$errors = Invoke-AzStorageSyncCompatibilityCheck […]
$errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="file-system-compatibility"></a>Kompatibilita systému souborů
Azure File Sync se podporuje jenom na přímo připojených svazcích NTFS. Přímé připojené úložiště nebo DAS v systému Windows Server znamená, že systém souborů vlastní operační systém Windows Server. DAS lze poskytnout prostřednictvím fyzického připojení disků k souborovému serveru, připojení virtuálních disků k virtuálnímu počítači souborového serveru (například virtuální hod u hyper-V) nebo dokonce prostřednictvím ISCSI.

Podporovány jsou pouze svazky NTFS. Systémy ReFS, FAT, FAT32 a další systémy souborů nejsou podporovány.

V následující tabulce je uveden stav interop funkcí systému souborů NTFS: 

| Funkce | Stav podpory | Poznámky |
|---------|----------------|-------|
| Seznamy ACL | Plně podporováno | Seznamy řízení přístupu ve stylu Windows jsou zachovány službou Azure File Sync a jsou vynuceny systémem Windows Server na koncových bodech serveru. Počet ACL lze také vynutit při přímém připojení sdílené složky Azure, ale to vyžaduje další konfiguraci. Další informace naleznete v [části Identita.](#identity) |
| Pevné odkazy | Přeskočen | |
| Symbolické odkazy | Přeskočen | |
| Přípojné body | Částečně podporováno | Přípojné body mohou být kořenem koncového bodu serveru, ale jsou přeskočeny, pokud jsou obsaženy v oboru názvů koncového bodu serveru. |
| Křižovatek | Přeskočen | Například složky Distributed File System DfrsrPrivate a DFSRoots. |
| Body rozboru | Přeskočen | |
| Komprese NTFS | Plně podporováno | |
| Řídké soubory | Plně podporováno | Řídké soubory se synchronizují (nejsou blokovány), ale synchronizují se do cloudu jako úplný soubor. Pokud se obsah souboru změní v cloudu (nebo na jiném serveru), soubor již není při stahování změny řídký. |
| Alternativní datové proudy (ADS) | Zachováno, ale nesynchronizované | Například klasifikační značky vytvořené infrastrukturou klasifikace souborů nejsou synchronizovány. Existující značky klasifikace na souborech na každém koncovém bodu serveru zůstanou nedotčeny. |

<a id="files-skipped"></a>Azure File Sync také přeskočí některé dočasné soubory a systémové složky:

| Soubor/složka | Poznámka |
|-|-|
| pagefile.sys | Soubor specifický pro systém |
| Desktop.ini | Soubor specifický pro systém |
| Bravo | Dočasný soubor pro miniatury |
| ehthumbs.db | Dočasný soubor pro miniatury médií |
| ~$\*.\* | Dočasný soubor office |
| \*Tmp | Dočasný soubor |
| \*.laccdb | Zamykací soubor Access DB|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Interní soubor synchronizace|
| \\Informace o systémovém svazku | Složka specifická pro svazek |
| $RECYCLE. Přihrádky| Složka |
| \\SyncShareState | Složka pro synchronizaci |

### <a name="failover-clustering"></a>Clustering s podporou převzetí služeb při selhání
Clustering s podporou převzetí služeb při selhání systému Windows Server podporuje azure file sync pro možnost nasazení "Souborový server pro obecné použití". Clustering s podporou převzetí služeb při selhání není podporován na souborovém serveru s horizontálním navýšením kapacity pro data aplikací (SOFS) nebo na clusterovaných sdílených svazcích (CSV).

> [!Note]  
> Agent Azure File Sync musí být nainstalovaný na každém uzlu v clusteru s podporou převzetí služeb při selhání, aby synchronizace fungovala správně.

### <a name="data-deduplication"></a>Odstranění duplicitních dat
**Windows Server 2016 a Windows Server 2019**   
Odstranění duplicit dat je podporováno na svazcích s povoleným vrstvením v cloudu v systémech Windows Server 2016 a Windows Server 2019. Povolení odstranění duplicit dat na svazku s povoleným vrstvením v cloudu vám umožní ukládat do mezipaměti více souborů v místním prostředí, aniž byste museli zřídit další úložiště. 

Pokud je na svazku s povolenou vrstvením v cloudu povolena odstranění duplicit, budou soubory optimalizované pro Dedup v umístění koncového bodu serveru vrstvené jako normální soubor na základě nastavení zásad vrstvení cloudu. Jakmile dedup optimalizované soubory byly vrstvené, data odstranění uvolňování paměti úlohy se spustí automaticky uvolnit místo na disku odebráním zbytečné bloky, které jsou již odkazuje jiné soubory na svazku.

Všimněte si, že úspory svazků platí pouze pro server; vaše data ve sdílené složce Azure nebudou naváděna.

> [!Note]  
> Chcete-li podporovat odstranění duplicit dat na svazcích s povoleným vrstvením cloudu v systému Windows Server 2019, musí být nainstalována aktualizace Systému Windows [KB4520062](https://support.microsoft.com/help/4520062) a je vyžadován agent Azure File Sync verze 9.0.0.0 nebo novější.

**Windows Server 2012 R2**  
Azure File Sync nepodporuje odstranění duplicit dat a vrstvení cloudu na stejném svazku v systému Windows Server 2012 R2. Pokud je na svazku povolena odstranění duplicit dat, musí být vrstvení cloudu zakázáno. 

**Poznámky**
- Pokud se před instalací agenta Azure File Sync nainstaluje odstranění duplicitních dat, je nutné restartovat počítač pro podporu odstranění duplicit dat a vrstvení cloudu na stejném svazku.
- Pokud je na svazku povolena odstranění duplicit po povolení vrstvení cloudu, počáteční úloha optimalizace odstranění duplicit optimalizuje soubory na svazku, které ještě nejsou vrstvené, a bude mít následující dopad na vrstvení cloudu:
    - Zásady volného místa budou pokračovat v vrstvě souborů podle volného místa na svazku pomocí heatmap.
    - Zásady data přeskočí vrstvení souborů, které mohly být jinak způsobilé pro vrstvení z důvodu úlohy optimalizace deduplikace přístup k souborům.
- Pro probíhající úlohy optimalizace odstranění duplicit, vrstvení cloudu s zásadou data se zpozdí nastavení [Minimální](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) fileduplikace dat, pokud soubor ještě není vrstvený. 
    - Příklad: Pokud je minimální fileagedays nastavení sedm dní a zásady data vrstvení cloudu je 30 dní, zásady data bude vrstvení soubory po 37 dnech.
    - Poznámka: Jakmile je soubor vrstvený pomocí Azure File Sync, úloha optimalizace odstranění duplicit soubor přeskočí.
- Pokud je server se systémem Windows Server 2012 R2 s nainstalovaným agentem synchronizace souborů Azure upgradován na Windows Server 2016 nebo Windows Server 2019, je nutné provést následující kroky pro podporu odstranění duplicit dat a vrstvení cloudu na stejném svazku:  
    - Odinstalujte agenta Azure File Sync pro Windows Server 2012 R2 a restartujte server.
    - Stáhněte si agenta Azure File Sync pro novou verzi operačního systému serveru (Windows Server 2016 nebo Windows Server 2019).
    - Nainstalujte agenta Azure File Sync a restartujte server.  
    
    Poznámka: Nastavení konfigurace synchronizace souborů Azure na serveru se zachovají, když je agent odinstalován a přeinstalován.

### <a name="distributed-file-system-dfs"></a>Distribuovaný systém souborů (DFS)
Azure File Sync podporuje interop s dfs obory názvů (DFS-N) a dfs replikace (DFS-R).

**Obory názvů DFS (DFS-N):** Synchronizace souborů Azure je plně podporována na serverech DFS-N. Agenta Azure File Sync můžete nainstalovat do jednoho nebo více členů DFS-N a synchronizovat data mezi koncovými body serveru a koncovým bodem cloudu. Další informace naleznete v tématu [Dfs Namespaces overview](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replikace distribuovaného systému souborů (DFS-R):** Vzhledem k tomu, že dfs-r a synchronizace souborů Azure jsou obě řešení replikace, ve většině případů doporučujeme nahradit DFS-R azure file sync. Existuje však několik scénářů, kde byste chtěli použít DFS-R a Azure File Sync společně:

- Migrujete z nasazení DFS-R do nasazení Azure File Sync. Další informace najdete [v tématu Migrace nasazení služby Replikace distribuovaného systému souborů (DFS-R) do synchronizace souborů Azure](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Ne každý místní server, který potřebuje kopii dat souboru, lze připojit přímo k Internetu.
- Pobočkové servery konsolidují data na jeden centrální server, pro který chcete použít Azure File Sync.

Pro Azure File Sync a DFS-R pracovat vedle sebe:

1. Vrstvení cloudu Azure File Sync musí být zakázáno na svazcích s replikovanými složkami DFS-R.
2. Koncové body serveru by neměly být konfigurovány ve složkách replikace jen pro čtení dfs-R.

Další informace naleznete v tématu [Přehled služby Replikace distribuovaného systému souborů](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
Použití programu sysprep na serveru s nainstalovaným agentem Azure File Sync není podporováno a může vést k neočekávaným výsledkům. Instalace agenta a registrace serveru by měla proběhnout po nasazení bitové kopie serveru a dokončení miniinstalace programu sysprep.

### <a name="windows-search"></a>Windows Search
Pokud je vrstvení cloudu povoleno na koncovém bodu serveru, soubory, které jsou vrstvené jsou přeskočeny a nejsou indexovány službou Windows Search. Soubory bez vrstvených jsou správně indexovány.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Další řešení správy hierarchických úložišť (HSM)
S Azure File Sync by se neměla používat žádná jiná řešení modulu zabezpečení.

## <a name="identity"></a>Identita
Azure File Sync funguje s vaší standardní identitou založenou na službě AD bez jakéhokoli speciálního nastavení nad rámec nastavení synchronizace. Když používáte Azure File Sync, obecné očekávání je, že většina přístupů projít servery azure file sync ukládání do mezipaměti, nikoli prostřednictvím sdílené složky Azure. Vzhledem k tomu, že koncové body serveru jsou umístěny v systému Windows Server a systém Windows Server již velmi dlouhou dobu podporuje seznamy ACL ve stylu služby AD a Windows, není zapotřebí nic kromě zajištění, že souborové servery Windows registrované ve službě Storage Sync Service jsou připojeny k doméně. Azure File Sync bude ukládat seznamy ACL na soubory ve sdílené složce Azure a bude je replikovat do všech koncových bodů serveru.

I když změny provedené přímo ve sdílené složce Azure bude trvat déle synchronizovat s koncovými body serveru ve skupině synchronizace, můžete také chtít zajistit, že můžete vynutit oprávnění služby AD na sdílené složky souboru přímo v cloudu také. Chcete-li to provést, musíte doložit svůj účet úložiště k místnímu službě AD, stejně jako to, jak jsou vaše souborové servery Windows připojeny k doméně. Další informace o připojení účtu úložiště k službě Active Directory vlastněné zákazníkem najdete v [tématu Přehled služby Azure Files Active Directory](storage-files-active-directory-overview.md).

> [!Important]  
> K úspěšnému nasazení Azure File Sync není nutné připojit váš účet úložiště ke službě Active Directory. Jedná se o přísně volitelný krok, který umožňuje sdílené složce Azure vynutit místní akly, když uživatelé připojit sdílené složky Azure přímo.

## <a name="networking"></a>Síťové služby
Agent Synchronizace souborů Azure komunikuje s vaší službou synchronizace úložiště a sdílenou sponou souborů Azure pomocí protokolu Azure File Sync REST a protokolu FileREST, které vždy používají protokol HTTPS přes port 443. SMB se nikdy nepoužívá k nahrávání nebo stahování dat mezi windows serverem a sdílenou složkou Azure. Vzhledem k tomu, že většina organizací povoluje provoz https přes port 443, jako požadavek na návštěvu většiny webů se obvykle nevyžaduje konfigurace speciální sítě k nasazení Azure File Sync.

Na základě zásad vaší organizace nebo jedinečných regulačních požadavků můžete vyžadovat restriktivnější komunikaci s Azure, a proto Azure File Sync poskytuje několik mechanismů pro konfiguraci sítí. Na základě vašich požadavků můžete:

- Synchronizace tunelů a nahrávání a stahování souborů přes ExpressRoute nebo Azure VPN. 
- Využijte Azure Files a Azure Networking funkce, jako jsou koncové body služby a privátní koncové body.
- Nakonfigurujte Azure File Sync tak, aby podporoval váš proxy server ve vašem prostředí.
- Omezení síťové aktivity z Azure File Sync.

Další informace o konfiguraci síťových funkcí Azure File Sync najdete v následujících tématech:
- [Nastavení proxy a brány firewall Synchronizace souborů Azure](storage-sync-files-firewall-and-proxy.md)
- [Zajištění Azure File Sync je dobrý soused ve vašem datovém centru](storage-sync-files-server-registration.md)

## <a name="encryption"></a>Šifrování
Při použití Azure File Sync, existují tři různé vrstvy šifrování, které je třeba zvážit: šifrování v úložišti v klidovém stavu Windows Serveru, šifrování při přenosu mezi agentem Azure File Sync a Azure a šifrování v ostatních vašich datech ve sdílené složce Azure. 

### <a name="windows-server-encryption-at-rest"></a>Šifrování systému Windows Server v klidovém stavu 
Existují dvě strategie pro šifrování dat na Windows Server, které fungují obecně s Azure File Sync: šifrování pod systémsouborů tak, aby systém souborů a všechna data zapsaná do něj je šifrována a šifrování v rámci samotného formátu souboru. Tyto metody se vzájemně nevylučují; mohou být použity společně, pokud je to žádoucí, protože účel šifrování je jiný.

Systém Windows Server poskytuje schránku doručené pošty nástroje BitLocker, aby zajistil šifrování pod systémem souborů. Nástroj BitLocker je pro Azure File Sync plně transparentní. Hlavním důvodem použití šifrovacího mechanismu, jako je nástroj BitLocker, je zabránit fyzické exfiltraci dat z místního datového centra někým, kdo krade disky, a zabránit bočnímu načtení neoprávněného operačního systému k provádění neautorizovaných čtení nebo zápisů do vašich dat. Další informace o nástrojem BitLocker naleznete v [tématu Přehled nástroje BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview).

Produkty třetích stran, které fungují podobně jako nástroj BitLocker v tom, že sedí pod svazkem NTFS, by podobně měly pracovat plně transparentně s Azure File Sync. 

Další hlavní metodou šifrování dat je šifrování datového proudu souboru při uložení souboru aplikací. Některé aplikace mohou to nativně, ale to obvykle není tento případ. Příkladem metody šifrování datového proudu souboru je Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS. Hlavním důvodem použití šifrovacího mechanismu, jako je AIP/RMS, je zabránit exfiltraci dat ze sdílené položky lidmi, kteří je kopírují do alternativních umístění, jako je na flash disk nebo je podají e-mailem neoprávněným osobám. Když je datový proud souboru zašifrován jako součást formátu souboru, bude tento soubor nadále šifrován ve sdílené složce Azure. 

Azure File Sync nespolupracuje se systémem souborů NTFS Encrypted File System (NTFS EFS) nebo šifrovacími řešeními třetích stran, která se nenachází nad systémem souborů, ale pod datovým proudem souboru. 

### <a name="encryption-in-transit"></a>Šifrování během přenosu
Agent Azure File Sync komunikuje s vaší službou synchronizace úložiště a sdílenou sponou souborů Azure pomocí protokolu Azure File Sync REST a protokolu FileREST, které vždy používají protokol HTTPS přes port 443. Azure File Sync neodesílá nešifrované požadavky přes protokol HTTP. 

Účty úložiště Azure obsahují přepínač, který vyžaduje šifrování při přenosu, který je ve výchozím nastavení povolený. I v případě, že přepínač na úrovni účtu úložiště je zakázáno, což znamená, že nešifrovaná připojení k vašim sdíleným složek maškarní, Azure File Sync bude stále používat pouze šifrované kanály pro přístup ke sdílené složce.

Primárnídůvod pro zakázání šifrování při přenosu pro účet úložiště je podpora starší aplikace, která musí být spuštěna ve starším operačním systému, jako je například Windows Server 2008 R2 nebo starší distribuce Linuxu, přímo ve složce Azure. Pokud starší aplikace hovoří s mezipamětí systému Windows Server sdílené složky, přepnutí tohoto nastavení nebude mít žádný vliv. 

Důrazně doporučujeme zajistit, aby bylo povoleno šifrování dat při přenosu.

Další informace o šifrování při přenosu najdete v [tématu vyžadování zabezpečeného přenosu v úložišti Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-file-share-encryption-at-rest"></a>Šifrování sdílené složky Azure v klidovém stavu
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Vrstvy úložiště
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Povolit standardní sdílené složky span až 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Regionální dostupnost
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Dostupnost oblasti synchronizace souborů Azure
Azure File Sync je k dispozici v následujících oblastech:

| Cloud Azure | Geografická oblast | Oblast Azure | Kód oblasti |
|-------------|-------------------|--------------|-------------|
| Public | Asie | Východní Asie | `eastasia` |
| Public | Asie | Jihovýchodní Asie | `southeastasia` |
| Public | Austrálie | Austrálie – východ | `australiaeast` |
| Public | Austrálie | Austrálie – jihovýchod | `australiasoutheast` |
| Public | Brazílie | Brazílie – jih | `brazilsouth` |
| Public | Kanada | Střední Kanada | `canadacentral` |
| Public | Kanada | Kanada – východ | `canadaeast` |
| Public | Evropa | Severní Evropa | `northeurope` |
| Public | Evropa | Západní Evropa | `westeurope` |
| Public | Francie | Francie – střed | `francecentral` |
| Public | Francie | Francie – jih* | `francesouth` |
| Public | Indie | Indie – střed | `centralindia` |
| Public | Indie | Indie – jih | `southindia` |
| Public | Japonsko | Japonsko – východ | `japaneast` |
| Public | Japonsko | Japonsko – západ | `japanwest` |
| Public | Jižní Korea | Jižní Korea – střed | `koreacentral` |
| Public | Jižní Korea | Jižní Korea – jih | `koreasouth` |
| Public | Jižní Afrika | Jižní Afrika – sever | `southafricanorth` |
| Public | Jižní Afrika | Jižní Afrika – západ* | `southafricawest` |
| Public | Spojené arabské emiráty | SAE Central* | `uaecentral` |
| Public | Spojené arabské emiráty | SAE Sever | `uaenorth` |
| Public | UK | Spojené království – jih | `uksouth` |
| Public | UK | Spojené království – západ | `ukwest` |
| Public | USA | USA – střed | `centralus` |
| Public | USA | USA – východ | `eastus` |
| Public | USA | USA – východ 2 | `eastus2` |
| Public | USA | USA – středosever | `northcentralus` |
| Public | USA | USA – středojih | `southcentralus` |
| Public | USA | USA – středozápad | `westcentralus` |
| Public | USA | USA – západ | `westus` |
| Public | USA | USA – západ 2 | `westus2` |
| US Gov | USA | USA (Gov) – Arizona | `usgovarizona` |
| US Gov | USA | USA (Gov) – Texas | `usgovtexas` |
| US Gov | USA | USA (Gov) – Virginia | `usgovvirginia` |

Azure File Sync podporuje synchronizaci jenom se sdílenou složkou Azure, která je ve stejné oblasti jako služba synchronizace úložiště.

V oblastech označených hvězdičkami musíte kontaktovat podporu Azure a požádat o přístup k Úložišti Azure v těchto oblastech. Proces je popsán v [tomto dokumentu](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="redundancy"></a>Redundance
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> Geograficky redundantní a geograficky zónově redundantní úložiště mají možnost ručního úložiště s podporou převzetí služeb při selhání do sekundární oblasti. Doporučujeme, abyste to nedělali mimo havárii, když používáte Azure File Sync z důvodu zvýšené pravděpodobnosti ztráty dat. V případě havárie, kde chcete zahájit ruční převzetí služeb při selhání úložiště, budete muset otevřít případ podpory s Microsoftem získat Azure File Sync obnovit synchronizaci se sekundárním koncovým bodem.

## <a name="migration"></a>Migrace
Pokud máte existující souborový server Windows, Azure File Sync může být přímo nainstalován na místě, bez nutnosti přesunout data na nový server. Pokud plánujete migraci na nový souborový server Windows jako součást přijetí Azure File Sync, existuje několik možných přístupů k přesunutí dat přes:

- Vytvořte koncové body serveru pro starou sdílenou složku a novou sdílenou složku a nechte Azure File Sync synchronizovat data mezi koncovými body serveru. Výhodou tohoto přístupu je, že je velmi snadné přeplatit úložiště na novém souborovém serveru, protože Azure File Sync je cloud tiering vědomi. Až budete připraveni, můžete přeříznout koncové uživatele na sdílenou složku na novém serveru a odebrat koncový bod serveru staré sdílené složky.

- Vytvořte koncový bod serveru pouze na novém souborovém serveru a `robocopy`zkopírujte data ze staré sdílené složky pomocí . V závislosti na topologii sdílených složek na novém serveru (kolik sdílených složek máte na každém svazku, jak je každý `robocopy` svazek volný atd.), možná budete muset dočasně zřídit další úložiště, protože se očekává, že ze starého serveru na nový server v místním datovém centru se dokončí rychleji než Azure File Sync přesune data do Azure.

Data Box je taky možné použít k migraci dat do nasazení Azure File Sync. Ve většině případů, kdy zákazníci chtějí používat data box k ingestování dat, dělají to proto, že si myslí, že to zvýší rychlost jejich nasazení nebo protože to pomůže s omezenými scénáři šířky pásma. I když je pravda, že použití datové schránky k ingestování dat do nasazení Azure File Sync sníží využití šířky pásma, bude pravděpodobně rychlejší pro většinu scénářů pokračovat v nahrávání online dat prostřednictvím jedné z metod popsaných výše. Další informace o tom, jak používat datovou schránku k ingestování dat do nasazení Azure File Sync, najdete [v tématu Migrace dat do Azure File Sync s Azure Data Box](storage-sync-offline-data-transfer.md).

Běžnou chybou, kterou zákazníci dělají při migraci dat do nového nasazení Azure File Sync, je kopírování dat přímo do sdílené složky Azure, nikoli na svých souborových serverech s Windows. Přestože Azure File Sync identifikuje všechny nové soubory ve sdílené složce Azure a synchronizuje je zpět do sdílených složek Windows, je to obecně podstatně pomalejší než načítání dat prostřednictvím souborového serveru Windows. Mnoho nástrojů pro kopírování Azure, jako je například AzCopy, mají další nevýhodu není kopírování všech důležitých metadat souboru, jako jsou časová razítka a AcLs.

## <a name="antivirus"></a>Antivirus
Vzhledem k tomu, že antivirový program funguje tak, že vyhledává známé škodlivé kódy, může antivirový produkt způsobit vyvolání vrstvených souborů. Ve verzích 4.0 a vyšší agent Azure File Sync mají vrstvené soubory nastavený atribut Zabezpečené Windows FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS nastaveny. Doporučujeme konzultovat s dodavatelem softwaru, abyste se dozvěděli, jak nakonfigurovat jejich řešení pro přeskočení čtení souborů s touto sadou atributů (mnoho z nich to dělá automaticky). 

Interní antivirová řešení společnosti Microsoft, Windows Defender a System Center Endpoint Protection (SCEP), oba automaticky přeskakují čtení souborů, které mají tento atribut nastaven. Testovali jsme je a identifikovali jeden menší problém: když přidáte server do existující skupiny synchronizace, soubory menší než 800 bajtů jsou odvolány (staženy) na novém serveru. Tyto soubory zůstanou na novém serveru a nebudou vrstvené, protože nesplňují požadavek na velikost vrstvení (>64 kb).

> [!Note]  
> Dodavatelé antivirového softwaru mohou kontrolovat kompatibilitu mezi svým produktem a azure synchronizací souborů pomocí [sady Azure File Sync Antivirus Compatibility Test Suite](https://www.microsoft.com/download/details.aspx?id=58322), která je k dispozici ke stažení na webu Microsoft Download Center.

## <a name="backup"></a>Zálohování 
Stejně jako antivirová řešení mohou záložní řešení způsobit odvolání vrstvených souborů. Doporučujeme použít cloudové řešení zálohování k zálohování sdílené složky Azure namísto místního zálohovacího produktu.

Pokud používáte místní řešení zálohování, zálohy by měly být prováděny na serveru ve skupině synchronizace, která má zakázáno vrstvení cloudu. Při provádění obnovení použijte možnosti obnovení na úrovni svazku nebo souboru. Soubory obnovené pomocí možnosti obnovení na úrovni souboru budou synchronizovány se všemi koncovými body ve skupině synchronizace a existující soubory budou nahrazeny verzí obnovenou ze zálohy.  Obnovení na úrovni svazku nenahradí novější verze souborů ve sdílené složce Azure nebo jiných koncových bodech serveru.

> [!Note]  
> Obnovení holého kovu (BMR) může způsobit neočekávané výsledky a není aktuálně podporováno.

> [!Note]  
> S verzí 9 agenta Synchronizace souborů Azure jsou teď snímky VSS (včetně karty Předchozí verze) podporované na svazcích, které mají povolené vrstvení v cloudu. Je však nutné povolit kompatibilitu předchozí verze prostřednictvím prostředí PowerShell. [Zjistěte jak](storage-files-deployment-guide.md).

## <a name="azure-file-sync-agent-update-policy"></a>Zásady aktualizace agenta Synchronizace souborů Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Další kroky
* [Zvažte nastavení brány firewall a proxy serveru.](storage-sync-files-firewall-and-proxy.md)
* [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
* [Nasazení služby Soubory Azure](storage-files-deployment-guide.md)
* [Nasazení Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)
* [Monitorování Synchronizace souborů Azure](storage-sync-files-monitoring.md)