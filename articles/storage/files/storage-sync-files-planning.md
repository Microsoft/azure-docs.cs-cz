---
title: Plánování nasazení Azure File Sync | Microsoft Docs
description: Naplánujte nasazení pomocí Azure File Sync služby, která vám umožní ukládat do mezipaměti několik sdílených složek Azure na místním nebo cloudovém virtuálním počítači s Windows serverem.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 876a96f579bff8d30e454e927054a951734f44ba
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441095"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Plánování nasazení Synchronizace souborů Azure

:::row:::
    :::column:::
        [![Rozhovor a ukázka Představujeme Azure File Sync – kliknutím zahrajete.](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Azure File Sync je služba, která umožňuje ukládat do mezipaměti několik sdílených složek Azure v místním nebo cloudovém virtuálním počítači s Windows serverem. 
        
        Tento článek vás seznámí s Azure File Sync koncepty a funkcemi. Až budete s Azure File Sync obeznámeni, zvažte použití [Průvodce nasazením Azure File Sync](storage-sync-files-deployment-guide.md) a vyzkoušejte si tuto službu.        
    :::column-end:::
:::row-end:::

Soubory budou uloženy v cloudu ve [sdílených složkách Azure](storage-files-introduction.md). Sdílené složky Azure můžete použít dvěma způsoby: přímým připojením těchto sdílených složek Azure (SMB) bez serveru nebo ukládáním do mezipaměti sdílených složek Azure v místním prostředí pomocí Azure File Sync. Zvolená volba nasazení změní aspekty, které je potřeba vzít v úvahu při plánování nasazení. 

- **Přímé připojení sdílené složky Azure**: vzhledem k tomu, že soubory Azure poskytují přístup přes protokol SMB, můžete sdílené složky Azure připojit místně nebo v cloudu pomocí standardního klienta SMB, který je dostupný v systémech Windows, MacOS a Linux. Vzhledem k tomu, že sdílené složky Azure jsou bez serveru, nasazení pro produkční scénáře nevyžaduje správu souborového serveru nebo zařízení NAS. To znamená, že nemusíte instalovat softwarové opravy ani odpínat fyzické disky. 

- **Ukládání sdílené složky Azure do mezipaměti v místním prostředí pomocí Azure File Sync**: Azure File Sync umožňuje centralizovat sdílené složky ve vaší organizaci ve službě soubory Azure a zachovat tak flexibilitu, výkon a kompatibilitu místního souborového serveru. Azure File Sync transformuje místní (nebo cloudový) Windows Server na rychlou mezipaměť sdílené složky Azure. 

## <a name="management-concepts"></a>Koncepty správy
Nasazení Azure File Sync má tři základní objekty správy:

- **Sdílená složka Azure**: sdílená složka Azure je cloudová sdílená složka bez serveru, která poskytuje *koncový bod cloudu* relace Azure File Sync Sync. K souborům ve sdílené složce Azure se dá získat přímý přístup pomocí protokolu SMB nebo protokolu REST, ale doporučujeme, abyste k souborům v mezipaměti Windows serveru měli přístup hlavně v případě, že se sdílená složka Azure používá s Azure File Sync. Důvodem je to, že Azure Files dnes chybí účinný mechanismus pro detekci změn, jako je třeba Windows Server, takže změny ve sdílené složce Azure budou trvat déle, než se rozšíří zpátky na koncové body serveru.
- **Koncový bod serveru**: cesta na Windows serveru, která se synchronizuje do sdílené složky Azure. Může se jednat o konkrétní složku na svazku nebo na kořen svazku. Pokud se jejich obory názvů nepřekrývají, můžou existovat na stejném svazku více koncových bodů serveru.
- **Skupina synchronizace**: objekt definující relaci synchronizace mezi **koncovým bodem cloudu**nebo sdílenou složkou Azure a koncovým bodem serveru. Koncové body v rámci skupiny synchronizace se mezi sebou synchronizují. Pokud máte například dvě různé sady souborů, které chcete spravovat pomocí Azure File Sync, vytvořte dvě skupiny synchronizace a přidejte do každé skupiny synchronizace různé koncové body.

### <a name="azure-file-share-management-concepts"></a>Koncepty správy Azure File Share
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Koncepty správy Azure File Sync
Skupiny synchronizace se nasazují do **služby synchronizace úložiště**, což jsou objekty nejvyšší úrovně, které registrují servery pro použití s Azure File Sync a obsahují vztahy skupiny synchronizace. Prostředek služby synchronizace úložiště je partnerským vztahem prostředku účtu úložiště a je možné ho podobně nasadit do skupin prostředků Azure. Služba synchronizace úložiště může vytvořit skupiny synchronizace, které obsahují sdílené složky Azure mezi několika účty úložiště a více registrovanými servery Windows.

Než budete moct vytvořit skupinu synchronizace v rámci služby synchronizace úložiště, musíte nejdřív zaregistrovat Windows Server se službou synchronizace úložiště. Tím se vytvoří **registrovaný objekt serveru** , který představuje vztah důvěryhodnosti mezi serverem nebo clusterem a službou synchronizace úložiště. Pokud chcete zaregistrovat službu synchronizace úložiště, musíte nejdřív na server nainstalovat agenta Azure File Sync. Jednotlivé servery nebo clustery je možné zaregistrovat jenom v jedné službě synchronizace úložiště.

Skupina synchronizace obsahuje jeden koncový bod cloudu nebo sdílenou složku Azure a nejméně jeden koncový bod serveru. Objekt koncového bodu serveru obsahuje nastavení, která konfigurují schopnost **vrstvení cloudu** , která poskytuje možnosti ukládání do mezipaměti Azure File Sync. Aby bylo možné synchronizovat se sdílenou složkou Azure, musí být účet úložiště obsahující sdílenou složku Azure ve stejné oblasti Azure jako služba synchronizace úložiště.

### <a name="management-guidance"></a>Pokyny pro správu
Při nasazování Azure File Sync doporučujeme:

- Nasazení sdílených složek Azure 1:1 se sdílenými složkami systému Windows. Objekt koncového bodu serveru poskytuje skvělou míru flexibility při nastavení topologie synchronizace na straně serveru relace synchronizace. Chcete-li zjednodušit správu, nastavte cestu koncového bodu serveru tak, aby odpovídala cestě sdílené složky systému Windows. 

- Použijte co nejmenší počet služeb synchronizace úložiště. Tím se zjednoduší Správa, pokud máte skupiny synchronizace, které obsahují více koncových bodů serveru, protože systém Windows Server lze zaregistrovat pouze do jedné služby synchronizace úložiště. 

- Při nasazování sdílených složek Azure věnujte pozornost omezením IOPS účtu úložiště. V ideálním případě byste namapovali sdílené složky 1:1 s účty úložiště. to ale nemusí být vždycky možné kvůli různým omezením a omezením, a to jak z vaší organizace, tak z Azure. Pokud není možné mít v jednom účtu úložiště nasazenou jenom jednu sdílenou složku, zvažte, které sdílené složky budou vysoce aktivní a které akcie budou méně aktivní, aby se zajistilo, že sdílené složky nejžhavějších se nebudou ukládat do stejného účtu úložiště společně.

## <a name="windows-file-server-considerations"></a>Požadavky na souborový server systému Windows
Pokud chcete povolit funkci synchronizace na Windows serveru, musíte nainstalovat Azure File Sync agenta ke stažení. Agent Azure File Sync poskytuje dvě hlavní součásti: `FileSyncSvc.exe` , službu systému Windows na pozadí, která je odpovědná za sledování změn v koncových bodech serveru a zahájení synchronizace relací, a `StorageSync.sys` , filtr systému souborů, který umožňuje vytvoření vrstvy cloudu a rychlé zotavení po havárii.  

### <a name="operating-system-requirements"></a>Požadavky na operační systém
Azure File Sync se podporuje s následujícími verzemi Windows serveru:

| Verze | Podporované SKU | Podporované možnosti nasazení |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter, Standard a IoT | Úplné a základní |
| Windows Server 2016 | Datacenter, Standard a Storage Server | Úplné a základní |
| Windows Server 2012 R2 | Datacenter, Standard a Storage Server | Úplné a základní |

Budoucí verze Windows serveru budou přidány při jejich vydávání.

> [!Important]  
> Doporučujeme, aby všechny servery, které používáte se službou, Azure File Sync aktuální s nejnovějšími aktualizacemi web Windows Update. 

### <a name="minimum-system-resources"></a>Minimální systémové prostředky
Azure File Sync vyžaduje server (fyzický nebo virtuální) s minimálně jedním PROCESORem a minimálně 2 GiB paměti.

> [!Important]  
> Pokud server běží na virtuálním počítači s povolenou dynamickou pamětí, měl by být virtuální počítač nakonfigurovaný minimálně na 2048 MiB paměti.

Pro většinu produkčních úloh nedoporučujeme konfigurovat Azure File Sync Sync Server jenom s minimálními požadavky. Další informace najdete v tématu [Doporučené systémové prostředky](#recommended-system-resources) .

### <a name="recommended-system-resources"></a>Doporučené systémové prostředky
Stejně jako u jakékoli serverové funkce nebo aplikace se požadavky na systémové prostředky pro Azure File Sync určují v rozsahu nasazení. větší nasazení na serveru vyžaduje větší systémové prostředky. Pro Azure File Sync se škála určuje podle počtu objektů v koncových bodech serveru a změn v datové sadě. Jeden server může mít koncové body serveru ve více skupinách synchronizace a počet objektů uvedených v následujících tabulkách účtů pro úplný obor názvů, ke kterému je server připojený. 

Například koncový bod serveru A s 10 000 000 objekty a koncovým bodem serveru B s 10 000 000 objekty = 20 000 000 objekty. Pro tento příklad nasazení doporučujeme, abyste 8 procesorů, 16 GiB paměti pro stabilní stav a (Pokud je to možné) 48 GiB paměti pro počáteční migraci.
 
Data oboru názvů jsou ukládána v paměti z důvodů výkonu. Vzhledem k tomu, že větší obory názvů vyžadují více paměti, aby bylo možné udržet dobrý výkon a více změn vyžaduje více PROCESORů pro zpracování. 
 
V následující tabulce jsme poskytovali jak velikost oboru názvů, tak i převod na kapacitu pro typické sdílené složky pro obecné účely, kde Průměrná velikost souboru je 512 KiB. Pokud jsou velikosti souborů menší, zvažte přidání další paměti pro stejnou velikost kapacity. Založte konfiguraci paměti na velikost oboru názvů.

| Velikost oboru názvů – soubory & adresářů (miliony)  | Typická kapacita (TiB)  | Jádra procesoru  | Doporučená paměť (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (počáteční synchronizace)/2 (Typická četnost změn)      |
| 5        | 2.3     | 2        | 16 (počáteční synchronizace)/4 (Typická četnost změn)    |
| 10       | 4,7     | 4        | 32 (počáteční synchronizace)/8 (Typická četnost změn)   |
| 30       | 14,0    | 8        | 48 (počáteční synchronizace)/16 (Typická četnost změn)   |
| 50       | 23,3    | 16       | 64 (počáteční synchronizace)/32 (Typická četnost změn)  |
| 100 *     | 46,6    | 32       | 128 (počáteční synchronizace)/32 (Typická četnost změn)  |

\*Synchronizace více než 100 000 000 souborů & adresářů v tuto chvíli není doporučena. Toto je částečný limit na základě našich testovaných prahových hodnot. Další informace najdete v tématu [škálovatelnost a cíle výkonnosti souborů Azure](storage-files-scale-targets.md#azure-file-sync-scale-targets).

> [!TIP]
> Počáteční synchronizace oboru názvů je náročná operace a doporučujeme přidělit více paměti, dokud nebude dokončena počáteční synchronizace. To se nevyžaduje, ale může urychlit počáteční synchronizaci. 
> 
> Typická četnost změn je 0,5% změny oboru názvů za den. Pro vyšší úrovně změn zvažte přidání dalších PROCESORů. 

- Místně připojený svazek formátovaný pomocí systému souborů NTFS.

### <a name="evaluation-cmdlet"></a>Rutina vyhodnocení
Před nasazením Azure File Sync byste měli vyhodnotit, jestli je kompatibilní s vaším systémem pomocí rutiny Azure File Sync vyhodnocení. Tato rutina kontroluje potenciální problémy se systémem souborů a datovou sadou, jako jsou například nepodporované znaky nebo Nepodporovaná verze operačního systému. Jeho kontroly pokrývají většinu, ale ne všechny funkce uvedené níže. Doporučujeme, abyste si pečlivě přečetli zbytek této části a zajistili tak plynulé nasazení. 

Rutinu vyhodnocení se dá nainstalovat tak, že nainstalujete modul AZ PowerShell, který se dá nainstalovat podle pokynů uvedených tady: [instalace a konfigurace Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Využití  
Nástroj pro vyhodnocení můžete vyvolat několika různými způsoby: můžete provádět kontroly systému, datové sady nebo obojí. Jak provést kontrolu systému i datovou sadu: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Testování pouze vaší datové sady:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Testování jenom požadavků na systém:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name> -SkipNamespaceChecks
```
 
Zobrazení výsledků ve formátu CSV:
```powershell
$validation = Invoke-AzStorageSyncCompatibilityCheck C:\DATA
$validation.Results | Select-Object -Property Type, Path, Level, Description, Result | Export-Csv -Path C:\results.csv -Encoding utf8
```

### <a name="file-system-compatibility"></a>Kompatibilita systému souborů
Azure File Sync se podporuje jenom na přímo připojených svazcích NTFS. Přímo připojené úložiště nebo DAS na Windows serveru znamená, že operační systém Windows Server vlastní systém souborů. DAS se dá poskytnout prostřednictvím fyzicky připojeného disku k souborovému serveru a připojení virtuálních disků k virtuálnímu počítači souborového serveru (například k virtuálnímu počítači hostovanému technologií Hyper-V) nebo i přes ISCSI.

Podporovány jsou pouze svazky NTFS. ReFS, FAT, FAT32 a jiné systémy souborů se nepodporují.

V následující tabulce je uveden stav spolupráce funkcí systému souborů NTFS: 

| Příznak | Stav podpory | Poznámky |
|---------|----------------|-------|
| Seznamy ACL | Plně podporováno | Windows – volitelné seznamy řízení přístupu se uchovávají Azure File Sync a vynutily Windows Server na koncových bodech serveru. Seznamy ACL je taky možné vyhovět při přímém připojení sdílené složky Azure, ale to vyžaduje další konfiguraci. Další informace najdete v [části Identita](#identity) . |
| Pevné odkazy | Přeskočeno | |
| Symbolické odkazy | Přeskočeno | |
| Přípojné body | Částečně podporováno | Přípojné body můžou být kořenem koncového bodu serveru, ale pokud se nacházejí v oboru názvů koncového bodu serveru, přeskočí se. |
| Spojení | Přeskočeno | Například systém souborů DFS (Distributed File System) DfrsrPrivate a DFSRoots složky. |
| Body rozboru | Přeskočeno | |
| Komprese NTFS | Plně podporováno | |
| Zhuštěné soubory | Plně podporováno | Synchronní soubory se synchronizují (nejsou blokované), ale synchronizují se s cloudem jako úplný soubor. Pokud se obsah souboru změní v cloudu (nebo na jiném serveru), soubor už nebude po stažení změny zhuštěný. |
| Alternativní datové proudy (INZERÁTy) | Zachované, ale nesynchronizované | Například klasifikační značky vytvořené infrastrukturou klasifikace souborů se nesynchronizují. Existující klasifikační značky souborů na všech koncových bodech serveru jsou ponechány beze změny. |

<a id="files-skipped"></a>Azure File Sync se také přeskočí některé dočasné soubory a systémové složky:

| Soubor nebo složka | Poznámka |
|-|-|
| pagefile.sys | Soubor specifický pro systém |
| Desktop.ini | Soubor specifický pro systém |
| miniatury. DB | Dočasný soubor pro miniatury |
| ehThumbs. DB | Dočasný soubor pro miniatury multimédií |
| ~$\*.\* | Dočasný soubor Office |
| \*. tmp | Dočasný soubor |
| \*.laccdb | Soubor zámků Access DB|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Soubor interní synchronizace|
| \\Informace o svazku systému | Složka specifická pro svazek |
| $RECYCLE. BIN| Složka |
| \\SyncShareState | Složka pro synchronizaci |

### <a name="failover-clustering"></a>Clustering s podporou převzetí služeb při selhání
Clustering s podporou převzetí služeb při selhání ve Windows serveru podporuje Azure File Sync pro možnost nasazení souborový server pro obecné použití. Clustering s podporou převzetí služeb při selhání není podporován na Souborový server se škálováním na více systémů pro data aplikací (SOFS) nebo na sdílených svazcích clusteru (CSV).

> [!Note]  
> Aby synchronizace fungovala správně, musí být na každém uzlu v clusteru s podporou převzetí služeb při selhání nainstalovaný agent Azure File Sync.

### <a name="data-deduplication"></a>Odstranění duplicitních dat
**Windows Server 2016 a Windows Server 2019**   
Odstranění duplicitních dat se podporuje u svazků s povoleným vytvářením vrstev cloudu v systémech Windows Server 2016 a Windows Server 2019. Povolení odstranění duplicitních dat u svazku s povoleným vrstvou cloudu umožňuje ukládat do mezipaměti více souborů bez nutnosti zajistit další úložiště. 

Když je u svazku s povoleným vrstvou cloudu povolené odstranění duplicitních dat, bude v umístění koncového bodu serveru na základě nastavení zásad cloudu vyčištěné duplicitní soubory ve stejném umístění. Jakmile budou optimalizované soubory odstranění duplicit vrstveny, úloha uvolňování paměti při odstranění duplicitních dat se automaticky spustí, aby se uvolní místo na disku, a to odebráním nepotřebných bloků dat, na které už neodkazuje jiné soubory na svazku.

Všimněte si, že úspory svazku se vztahují jenom na server. vaše data ve sdílené složce Azure nebudou Odstraněná duplicitovaná.

> [!Note]  
> Aby bylo možné podporovat odstranění duplicitních dat u svazků s povoleným vrstvou cloudu, které jsou povoleny v systému Windows Server 2019, musí být nainstalována služba Windows Update [KB4520062](https://support.microsoft.com/help/4520062) a vyžaduje se Azure File Sync agenta verze 9.0.0.0 nebo novější.

**Windows Server 2012 R2**  
Azure File Sync nepodporuje odstranění duplicitních dat a vrstvení cloudu na stejném svazku na Windows Serveru 2012 R2. Pokud je u svazku povolené odstranění duplicitních dat, musí být vrstva cloudu zakázaná. 

**Poznámky**
- Pokud se odstranění duplicitních dat nainstaluje před instalací agenta Azure File Sync, vyžaduje se restart k podpoře odstranění duplicitních dat a vrstvení cloudu na stejném svazku.
- Pokud je odstranění duplicitních dat u svazku po povolení vrstvení cloudu povolené, bude úloha optimalizace prvotního odstranění duplicit optimalizovat soubory na svazku, které ještě nejsou vrstvené, a bude mít následující dopad na vrstvení cloudu:
    - Zásada volného místa bude pokračovat v souborech vrstev podle volného místa na svazku pomocí nástroje heatmapu.
    - Zásada data přeskočí vrstvení souborů, které mohly být jinak způsobilé pro vrstvení z důvodu úlohy optimalizace odstranění duplicitních dat při přístupu k souborům.
- V případě probíhajících úloh optimalizace odstranění duplicit se vrstvení cloudu se zásadami data po nastavení [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) odstranění duplicitních dat zpozdí, pokud soubor ještě není vrstvený. 
    - Příklad: Pokud je nastavení MinimumFileAgeDays sedm dní a zásady pro datové vrstvy cloudu jsou nastavené na 30 dní, zásada data bude mít soubory na úrovni po 37 dnech.
    - Poznámka: když je soubor vrstvený Azure File Sync, úloha optimalizace odstranění duplicit soubor přeskočí.
- Pokud je server se systémem Windows Server 2012 R2 s nainstalovaným agentem Azure File Sync upgradován na Windows Server 2016 nebo Windows Server 2019, je nutné provést následující kroky, aby bylo možné podporovat odstranění duplicitních dat a vrstvení cloudu na stejném svazku:  
    - Odinstalujte agenta Azure File Sync pro Windows Server 2012 R2 a restartujte server.
    - Stáhněte agenta Azure File Sync pro novou verzi serverového operačního systému (Windows Server 2016 nebo Windows Server 2019).
    - Nainstalujte agenta Azure File Sync a restartujte server.  
    
    Poznámka: nastavení konfigurace Azure File Sync na serveru se uchovávají při odinstalaci a opětovné instalaci agenta.

### <a name="distributed-file-system-dfs"></a>Systém souborů DFS (Distributed File System) (DFS)
Azure File Sync podporuje interoperabilitu s obory názvů DFS (DFS-N) a Replikace DFS (DFS-R).

**Obory názvů DFS (DFS-n)**: Azure File Sync se plně podporují na serverech DFS-N. Agenta Azure File Sync můžete nainstalovat na jeden nebo více členů DFS-N a synchronizovat data mezi koncovými body serveru a koncovým bodem cloudu. Další informace najdete v tématu [Přehled oborů názvů DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replikace DFS (DFS-r)**: vzhledem k tomu, že DFS-r a Azure File Sync jsou obě řešení replikace, doporučujeme ve většině případů nahradit DFS-r Azure File Sync. Existuje však několik scénářů, kdy chcete použít DFS-R a Azure File Sync společně:

- Migrujete z nasazení systému souborů DFS-R do nasazení Azure File Sync. Další informace najdete v tématu [migrace nasazení replikace DFS (DFS-R) do Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Ne každý místní server, který potřebuje kopii dat souboru, se může připojit přímo k Internetu.
- Servery větví konsolidují data na jeden hub Server, pro který chcete použít Azure File Sync.

Pro Azure File Sync a DFS-R pro práci vedle sebe:

1. Na svazcích s replikovanými složkami DFS-R musí být zakázané Azure File Sync vrstvení cloudu.
2. Koncové body serveru by se neměly konfigurovat v replikačních složkách jen pro čtení DFS-R.

Další informace najdete v tématu [přehled replikace DFS](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
Použití nástroje Sysprep na serveru s nainstalovaným agentem Azure File Sync není podporováno a může vést k neočekávaným výsledkům. Instalace agenta a registrace serveru by se měly vyskytnout po nasazení image serveru a dokončení zkrácené instalace nástroje Sysprep.

### <a name="windows-search"></a>Windows Search
Pokud je na koncovém bodu serveru povolené vrstvení cloudu, soubory, které jsou vrstveny, se přeskočí a neindexují služba Windows Search. Soubory bez vrstev jsou indexovány správně.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Další řešení pro správu hierarchických úložišť (HSM)
S Azure File Sync by se neměla používat žádná další řešení HSM.

## <a name="identity"></a>Identita
Azure File Sync pracuje se standardní identitou založenou na službě AD bez jakéhokoli speciálního nastavení nad rámec nastavení synchronizace. Pokud používáte Azure File Sync, obecně se předpokládá, že většina přístupů projde servery Azure File Sync pro ukládání do mezipaměti, nikoli prostřednictvím sdílené složky Azure. Vzhledem k tomu, že se koncové body serveru nacházejí v systému Windows Server a systém Windows Server podporuje seznamy řízení přístupu (ACL) ve stylu služby AD a systému Windows po dlouhou dobu, není nic nutné, než zajistíte, aby souborové servery systému Windows zaregistrované ve službě synchronizace úložiště byly připojeny Azure File Sync budou ukládat do souborů ve sdílené složce Azure seznamy ACL a budou se replikovat do všech koncových bodů serveru.

I když změny provedené přímo ve sdílené složce Azure budou trvat déle, než se synchronizují s koncovými body serveru ve skupině synchronizace, možná budete chtít taky zajistit, abyste ve sdílené složce mohli vyhovět i oprávněním služby AD přímo v cloudu. Abyste to mohli udělat, musíte účet úložiště připojit k místní službě AD, stejně jako v případě, že jsou souborové servery Windows připojené k doméně. Další informace o tom, jak se doména připojuje k vašemu účtu úložiště ke službě Active Directory vlastněné zákazníkem, najdete v tématu [Přehled služby Azure Files Active Directory](storage-files-active-directory-overview.md).

> [!Important]  
> K úspěšnému nasazení Azure File Sync není potřeba připojení k doméně vašeho účtu úložiště ke službě Active Directory. Toto je naprosto volitelný krok, který umožňuje sdílené složce Azure vymáhat místní seznamy ACL, když uživatelé připojí sdílenou složku Azure přímo.

## <a name="networking"></a>Sítě
Agent Azure File Sync komunikuje se službou synchronizace úložiště a sdílenou složkou souborů Azure pomocí protokolu Azure File Sync REST a protokolu REST, přičemž oba používají protokol HTTPS přes port 443. Protokol SMB se nikdy nepoužívá k nahrávání nebo stahování dat mezi vaším Windows serverem a sdílenou složkou souborů Azure. Vzhledem k tomu, že většina organizací povoluje přenos přes protokol HTTPS přes port 443, jako požadavek na navštívení většiny webů, není obvykle nutné nasazovat Azure File Sync speciální síťovou konfiguraci.

Na základě zásad vaší organizace nebo jedinečných regulativních požadavků můžete vyžadovat přísnější komunikaci s Azure, a proto Azure File Sync pro konfiguraci sítě nabízí několik mechanismů. Na základě vašich požadavků můžete:

- Synchronizace tunelu a nahrávání souborů/stahování dat prostřednictvím ExpressRoute nebo Azure VPN. 
- Využijte Azure Files a síťové funkce Azure, jako jsou koncové body služby a soukromé koncové body.
- Nakonfigurujte Azure File Sync pro podporu proxy serveru ve vašem prostředí.
- Omezí síťovou aktivitu z Azure File Sync.

Další informace o Azure File Sync a sítích najdete v tématu [informace o Azure File Syncch sítích](storage-sync-files-networking-overview.md).

## <a name="encryption"></a>Šifrování
Při použití Azure File Sync existují tři různé vrstvy šifrování, které je třeba vzít v úvahu: šifrování v úložišti Windows serveru v klidovém režimu, šifrování mezi agentem Azure File Sync a Azure a šifrování ve zbývající části dat ve sdílené složce Azure. 

### <a name="windows-server-encryption-at-rest"></a>Šifrování Windows serveru v klidovém umístění 
Existují dvě strategie šifrování dat na Windows serveru, které pracují obecně s Azure File Sync: šifrování pod systémem souborů tak, aby byl systém souborů a všechna zapsaná data zašifrovaná, a šifrování v rámci samotného formátu souboru. Tyto metody se vzájemně nevylučují. v případě potřeby je lze použít společně, protože účel šifrování je jiný.

K zajištění šifrování pod systémem souborů poskytuje Windows Server doručenou poštu nástroje BitLocker. BitLocker je plně transparentní pro Azure File Sync. Hlavním důvodem pro použití šifrovacího mechanismu jako BitLockeru je zabránit fyzickému exfiltrace dat z místního datacentra tím, že někdo ukrást disky a zabrání zkušebnímu načtení neautorizovaného čtení/zápisu do vašich dat. Další informace o nástroji BitLocker najdete v tématu [Přehled nástroje BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview).

Produkty třetích stran, které fungují podobně jako BitLocker, v tom, že se nacházejí pod svazkem NTFS, by měly podobně zcela transparentně pracovat s Azure File Sync. 

Druhým hlavním způsobem šifrování dat je šifrovat datový proud souboru, když aplikace soubor uloží. Některé aplikace mohou provádět nativně, ale obvykle to není případ. Příkladem metody pro šifrování datového proudu souboru je Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS. Hlavním důvodem pro použití šifrovacího mechanismu jako AIP/RMS je zabránit tomu, aby se data exfiltrace z vaší sdílené složky nakopírovala na alternativní umístění, třeba na jednotku Flash, nebo ji pošlete na neoprávněnou osobu. Když je datový proud souboru zašifrovaný jako součást formátu souboru, bude tento soubor dál šifrovaný ve sdílené složce Azure. 

Azure File Sync spolupracuje se systémem souborů NTFS (EFS Encrypted File System) nebo s šifrovacími řešeními třetích stran, která se nachází nad systémem souborů, ale pod datovým proudem souboru. 

### <a name="encryption-in-transit"></a>Šifrování během přenosu

> [!NOTE]
> Služba Azure File Sync odstraní podporu TLS 1.0 a 1,1 na 1. srpna 2020. Všechny podporované verze agenta Azure File Sync již ve výchozím nastavení používají protokol TLS 1.2. Pokud je na vašem serveru zakázaný protokol TLS 1.2 nebo se používá proxy server, může dojít k použití starší verze protokolu TLS. Pokud používáte proxy server, doporučujeme, abyste zkontrolovali konfiguraci proxy serveru. Azure File Sync oblasti služeb přidané po 5/1/2020 budou podporovat pouze TLS 1.2 a podpora protokolu TLS 1.0 a 1,1 bude odebrána z existujících oblastí od 1. srpna 2020.  Další informace najdete v [Průvodci odstraňováním potíží](storage-sync-files-troubleshoot.md#tls-12-required-for-azure-file-sync).

Agent Azure File Sync komunikuje se službou synchronizace úložiště a sdílenou složkou Azure pomocí protokolu Azure File Sync REST a protokolu REST, přičemž oba vždy používají protokol HTTPS přes port 443. Azure File Sync neodesílá nešifrované požadavky přes HTTP. 

Účty úložiště Azure obsahují přepínač pro vyžadování šifrování při přenosu, který je ve výchozím nastavení povolený. I když je přepínač na úrovni účtu úložiště zakázaný, to znamená, že je možné používat nešifrovaná připojení ke sdíleným složkám Azure, Azure File Sync se pořád pro přístup ke sdílené složce používaly jenom šifrované kanály.

Hlavním důvodem Zakázání šifrování při přenosu pro účet úložiště je podpora starší verze aplikace, kterou je třeba spustit ve starším operačním systému, jako je Windows Server 2008 R2 nebo starší distribuce systému Linux, a to přímo do sdílené složky Azure. Pokud starší verze aplikace mluví s mezipamětí Windows serveru sdílené složky, přepnutí tohoto nastavení nebude mít žádný vliv. 

Důrazně doporučujeme, abyste zajistili, že je povolené šifrování dat při přenosu.

Další informace o šifrování v přenosu najdete v tématu [vyžadování zabezpečeného přenosu ve službě Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-file-share-encryption-at-rest"></a>Šifrování sdílené složky Azure v klidovém formátu
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Úrovně úložiště
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Povolit standardní sdílené složky pro rozsah až 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Regionální dostupnost
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Dostupnost oblasti služby Azure File Sync
Azure File Sync je k dispozici v následujících oblastech:

| Cloud Azure | Zeměpisná oblast | Oblast Azure | Kód oblasti |
|-------------|-------------------|--------------|-------------|
| Public | Asie | Východní Asie | `eastasia` |
| Public | Asie | Southeast Asia | `southeastasia` |
| Public | Austrálie | Austrálie – východ | `australiaeast` |
| Public | Austrálie | Australia Southeast | `australiasoutheast` |
| Public | Brazílie | Brazil South | `brazilsouth` |
| Public | Kanada | Střední Kanada | `canadacentral` |
| Public | Kanada | Kanada – východ | `canadaeast` |
| Public | Evropa | Severní Evropa | `northeurope` |
| Public | Evropa | West Europe | `westeurope` |
| Public | Francie | Francie – střed | `francecentral` |
| Public | Francie | Francie – jih * | `francesouth` |
| Public | Indie | Indie – střed | `centralindia` |
| Public | Indie | Indie – jih | `southindia` |
| Public | Japonsko | Japan East | `japaneast` |
| Public | Japonsko | Japonsko – západ | `japanwest` |
| Public | Jižní Korea | Jižní Korea – střed | `koreacentral` |
| Public | Jižní Korea | Jižní Korea – jih | `koreasouth` |
| Public | Jižní Afrika | Jižní Afrika – sever | `southafricanorth` |
| Public | Jižní Afrika | Jižní Afrika – západ * | `southafricawest` |
| Public | Spojené arabské emiráty | Spojené arabské emiráty střed * | `uaecentral` |
| Public | Spojené arabské emiráty | Spojené arabské emiráty sever | `uaenorth` |
| Public | UK | Spojené království – jih | `uksouth` |
| Public | UK | Spojené království – západ | `ukwest` |
| Public | USA | Střední USA | `centralus` |
| Public | USA | East US | `eastus` |
| Public | USA | USA – východ 2 | `eastus2` |
| Public | USA | USA – středosever | `northcentralus` |
| Public | USA | Středojižní USA | `southcentralus` |
| Public | USA | USA – středozápad | `westcentralus` |
| Public | USA | USA – západ | `westus` |
| Public | USA | Západní USA 2 | `westus2` |
| US Gov | USA | USA (Gov) – Arizona | `usgovarizona` |
| US Gov | USA | USA (Gov) – Texas | `usgovtexas` |
| US Gov | USA | USA (Gov) – Virginia | `usgovvirginia` |

Azure File Sync podporuje synchronizaci jenom se sdílenou složkou Azure, která je ve stejné oblasti jako služba synchronizace úložiště.

U oblastí označených hvězdičkami musíte kontaktovat podporu Azure a požádat o přístup k Azure Storage v těchto oblastech. Tento postup je popsaný v [tomto dokumentu](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="redundancy"></a>Redundance
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> Redundantní úložiště geograficky redundantních a geografických zón mají schopnost ručně převzetí služeb při selhání úložiště do sekundární oblasti. Doporučujeme, abyste to nepoužívali mimo případ havárie při použití Azure File Sync z důvodu zvýšené pravděpodobnosti ztráty dat. V případě havárie, kde byste chtěli iniciovat ruční převzetí služeb při selhání, budete muset otevřít případ podpory s Microsoftem a získat Azure File Sync pokračovat v synchronizaci se sekundárním koncovým bodem.

## <a name="migration"></a>Migrace
Máte-li existující souborový server systému Windows, Azure File Sync lze přímo nainstalovat na místě, aniž by bylo nutné přesunovat data na nový server. Pokud plánujete migrovat na nový souborový server Windows jako součást přijetí Azure File Sync, je k dispozici několik možných přístupů k přesunu dat:

- Vytvořte koncové body serveru pro starou sdílenou složku a novou sdílenou složku a umožněte Azure File Sync synchronizovat data mezi koncovými body serveru. Výhodou tohoto přístupu je, že se velmi snadno přestává odebírat úložiště na novém souborovém serveru, protože Azure File Sync podporuje tvorbu cloudových vrstev. Až budete připraveni, můžete koncové uživatele vyjmout do sdílené složky na novém serveru a odebrat koncový bod serveru staré sdílené složky.

- Vytvořte koncový bod serveru pouze na novém souborovém serveru a zkopírujte data do původní sdílené složky pomocí `robocopy` . V závislosti na topologii sdílených složek na novém serveru (kolik sdílených složek máte na každém svazku, jak uvolnit jednotlivé svazky atd.) možná budete muset dočasně zřídit další úložiště, protože se očekává, že `robocopy` z původního serveru na váš nový server v místním datovém centru se dokončí rychleji než Azure File Sync přesunou data do Azure.

K migraci dat do nasazení Azure File Sync je taky možné použít Data Box. Ve většině případů zákazníci chtějí použít Data Box k ingestování dat, protože mají za to, že budou zvyšovat rychlost nasazení, nebo protože bude pomáhat s omezeními s omezenou šířkou pásma. I když použití Data Box k ingestování dat do nasazení Azure File Sync sníží využití šířky pásma, bude pravděpodobně rychlejší pro většinu scénářů, abyste mohli provádět online nahrávání dat prostřednictvím jedné z výše popsaných metod. Další informace o tom, jak pomocí Data Box ingestovat data do nasazení Azure File Sync, najdete v tématu [migrace dat do Azure File Sync s Azure Data box](storage-sync-offline-data-transfer.md).

Při migraci dat do nového nasazení Azure File Sync vzdálení zákazníci se systémem Windows kopírují data přímo do sdílené složky Azure místo na jejich souborové servery. I když Azure File Sync identifikuje všechny nové soubory ve sdílené složce Azure a synchronizuje je zpátky do sdílených složek Windows, je to všeobecně mnohem pomalejší než načítání dat prostřednictvím souborového serveru Windows. Při používání nástrojů pro kopírování Azure, jako je AzCopy, je důležité použít nejnovější verzi. V [tabulce nástroje pro kopírování souborů](storage-files-migration-overview.md#file-copy-tools) najdete přehled nástrojů pro kopírování v Azure, abyste měli jistotu, že můžete kopírovat všechna důležitá metadata souboru, jako jsou například časová razítka a seznamy ACL.

## <a name="antivirus"></a>Antivirus
Vzhledem k tomu, že antivirová práce funguje, hledá známý škodlivý kód, antivirový produkt může způsobit odvolání vrstvených souborů, což má za následek vysoké náklady na výstup. Ve verzích 4,0 a vyšších Azure File Sync agenta mají vrstvený soubor FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS nastaven atribut zabezpečení systému Windows. Doporučujeme, abyste se od dodavatele softwaru dozvěděli, jak nakonfigurovat jejich řešení tak, aby přeskočilo čtení souborů s touto sadou atributů (mnoho z nich provede automaticky). 

Interní antivirová řešení Microsoftu, Windows Defender a System Center Endpoint Protection (SCEP), automaticky přeskočí čtení souborů s nastavením tohoto atributu. Otestovali jsme je a zjistili jsme jeden malý problém: Když přidáte server do existující skupiny synchronizace, soubory menší než 800 bajtů se odvolají (stáhnou) na novém serveru. Tyto soubory zůstanou na novém serveru a nebudou vrstveny, protože nesplňují požadavek na velikost vrstev (>64 KB).

> [!Note]  
> Dodavatelé antivirové ochrany můžou zkontrolovat kompatibilitu mezi jejich produktem a Azure File Sync pomocí [Azure File Sync sady testů kompatibility antivirové ochrany](https://www.microsoft.com/download/details.aspx?id=58322), která je k dispozici ke stažení na webu Microsoft Download Center.

## <a name="backup"></a>Backup 
Pokud je zapnutá vrstva cloudu, neměla by se používat řešení, která přímo zálohují koncový bod serveru nebo virtuální počítač, na kterém je umístěný koncový bod serveru. Vrstvení cloudu způsobí, že se jenom podmnožina vašich dat uloží na koncový bod serveru s úplnou datovou sadou, která je umístěná ve sdílené složce Azure. V závislosti na použitém řešení zálohování se vrstvené soubory buď přeskočí a nezálohují (protože mají nastaven atribut FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS), nebo se budou znovu volat na disk a výsledkem jsou poplatky za vysoké náklady na výstup. K přímému zálohování sdílené složky Azure doporučujeme použít řešení zálohování v cloudu. Další informace najdete v tématech [o zálohování sdílených složek Azure nebo o](https://docs.microsoft.com/azure/backup/azure-file-share-backup-overview?toc=/azure/storage/files/toc.json) tom, jak se obraťte na poskytovatele služby Backup, aby bylo možné zjistit, jestli podporují zálohování sdílených složek Azure.

Pokud dáváte přednost použití místního řešení zálohování, měli byste zálohy provádět na serveru ve skupině synchronizace, která má zakázanou vrstvu v cloudu. Při provádění obnovení použijte možnosti obnovení na úrovni svazku nebo souboru. Soubory obnovené pomocí možnosti obnovení na úrovni souborů budou synchronizovány do všech koncových bodů ve skupině synchronizace a stávající soubory budou nahrazeny verzí obnovenou ze zálohy.  Obnovení na úrovni svazku nebude nahrazovat novější verze souborů ve sdílené složce Azure nebo v jiných koncových bodech serveru.

> [!Note]  
> Úplné obnovení systému (BMR) může způsobit neočekávané výsledky a aktuálně se nepodporuje.

> [!Note]  
> Ve verzi 9 agenta Azure File Sync se teď na svazcích, na kterých je povolená vrstva cloudu, podporují snímky služby Stínová kopie svazku (včetně karty předchozí verze). Je však nutné povolit kompatibilitu předchozí verze prostřednictvím prostředí PowerShell. [Přečtěte si, jak](storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service).

## <a name="azure-file-sync-agent-update-policy"></a>Zásady aktualizace agenta Synchronizace souborů Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Další kroky
* [Zvážení brány firewall a nastavení proxy serveru](storage-sync-files-firewall-and-proxy.md)
* [Plánování nasazení Azure Files](storage-files-planning.md)
* [Nasazení služby Soubory Azure](storage-files-deployment-guide.md)
* [Nasazení Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)
* [Sledování služby Synchronizace souborů Azure](storage-sync-files-monitoring.md)
