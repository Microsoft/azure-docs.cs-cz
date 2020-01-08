---
title: Plánování nasazení Azure File Sync | Microsoft Docs
description: Zjistěte, co je potřeba vzít v úvahu při plánování nasazení souborů Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c81f06d924a0ba871115e0ae0164d61449855263
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665267"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Plánování nasazení Synchronizace souborů Azure
Pomocí Azure File Sync můžete centralizovat sdílené složky ve vaší organizaci ve službě soubory Azure a zároveň udržet flexibilitu, výkon a kompatibilitu místního souborového serveru. Synchronizace souborů Azure transformuje Windows Server na rychlou mezipaměť sdílené složky Azure. Pro místní přístup k datům můžete použít libovolný protokol, který je dostupný na Windows serveru, včetně SMB, NFS a FTPS. Můžete mít tolik mezipamětí, kolik potřebujete po celém světě.

Tento článek popisuje důležité informace o nasazení Azure File Sync. Doporučujeme také, abyste si přečetli téma [plánování pro nasazení souborů Azure](storage-files-planning.md). 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-file-sync-terminology"></a>Azure File Sync terminologie
Než se dostanete k podrobnostem o plánování nasazení Azure File Sync, je důležité pochopit terminologii.

### <a name="storage-sync-service"></a>Služba synchronizace úložiště
Služba synchronizace úložiště je prostředek Azure na nejvyšší úrovni pro Azure File Sync. Prostředek služby synchronizace úložiště je partnerským vztahem prostředku účtu úložiště a je možné ho podobně nasadit do skupin prostředků Azure. Vyžaduje se samostatný prostředek nejvyšší úrovně z prostředku účtu úložiště, protože služba synchronizace úložiště může vytvářet relace synchronizace s více účty úložiště prostřednictvím více skupin synchronizace. Předplatné může mít nasazených víc prostředků služby synchronizace úložiště.

### <a name="sync-group"></a>Skupina synchronizace
Skupina synchronizace definuje topologii synchronizace sady souborů. Koncové body v rámci skupiny synchronizace se mezi sebou synchronizují. Pokud máte například dvě různé sady souborů, které chcete spravovat pomocí Azure File Sync, vytvořte dvě skupiny synchronizace a přidejte do každé skupiny synchronizace různé koncové body. Služba synchronizace úložiště může hostovat tolik skupin synchronizace, kolik potřebujete.  

### <a name="registered-server"></a>Registrovaný Server
Registrovaný objekt serveru představuje vztah důvěryhodnosti mezi serverem (nebo clusterem) a službou synchronizace úložiště. V případě potřeby můžete zaregistrovat libovolný počet serverů do instance služby synchronizace úložiště. Server (nebo cluster) se ale dá zaregistrovat jenom v jedné službě synchronizace úložiště.

### <a name="azure-file-sync-agent"></a>Agent Azure File Sync
Agent Synchronizace souborů Azure je balíček ke stažení, který umožňuje synchronizaci Windows Serveru se sdílenou složkou Azure. Agent Azure File Sync má tři hlavní součásti: 
- **FileSyncSvc. exe**: služba na pozadí, která zodpovídá za sledování změn v koncových bodech serveru a pro zahájení synchronizace relací do Azure.
- **StorageSync. sys**: filtr systému souborů Azure File Sync, který zodpovídá za vrstvení souborů do souborů Azure (Pokud je povolená vrstva cloudu).
- **Rutiny pro správu PowerShellu**: rutiny PowerShellu, které slouží k interakci s poskytovatelem prostředků Microsoft. StorageSync Azure. Můžete je najít v následujících umístěních (výchozí):
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Koncový bod serveru
Koncový bod serveru představuje konkrétní umístění na zaregistrovaném serveru, například složku na svazku serveru. Pokud se jejich obory názvů nepřekrývají (například `F:\sync1` a `F:\sync2`), můžou existovat na stejném svazku více koncových bodů serveru. Zásady cloudových vrstev můžete nakonfigurovat individuálně pro každý koncový bod serveru. 

Koncový bod serveru můžete vytvořit pomocí přípojný bod. Všimněte si, že mountpoints v rámci koncového bodu serveru se přeskočí.  

Na systémovém svazku můžete vytvořit koncový bod serveru, ale pokud to uděláte, existují dvě omezení:
* Vrstvení cloudu se nedá povolit.
* Rychlé obnovení oboru názvů (kde systém rychle zapne celý obor názvů a pak začne vracet obsah) se neprovede.


> [!Note]  
> Jsou podporovány pouze nevýměnné svazky.  Jednotky mapované ze vzdálené sdílené složky nejsou podporovány pro cestu koncového bodu serveru.  Kromě toho může být koncový bod serveru umístěný na svazku systému Windows, i když se vrstva cloudu nepodporuje na systémovém svazku.

Pokud přidáte umístění serveru, které má existující sadu souborů jako koncový bod serveru do skupiny synchronizace, budou tyto soubory sloučeny s dalšími soubory, které již jsou na jiných koncových bodech ve skupině synchronizace.

### <a name="cloud-endpoint"></a>Koncový bod cloudu
Koncový bod cloudu je sdílená složka Azure, která je součástí skupiny synchronizace. Celá sdílená složka Azure se synchronizuje a sdílená složka Azure může být členem jenom jednoho koncového bodu cloudu. Proto může být sdílená složka Azure členem jenom jedné skupiny synchronizace. Pokud přidáte sdílenou složku Azure, která obsahuje existující sadu souborů jako koncový bod cloudu pro skupinu synchronizace, existující soubory se sloučí s dalšími soubory, které už jsou na jiných koncových bodech ve skupině synchronizace.

> [!Important]  
> Azure File Sync podporuje přímé provádění změn ve sdílené složce Azure. Všechny změny provedené ve sdílené složce Azure se ale nejdřív musí zjistit pomocí úlohy zjišťování změn Azure File Sync. Úloha detekce změn se iniciuje pro koncový bod cloudu jenom jednou za 24 hodin. Kromě toho změny provedené ve sdílené složce Azure přes protokol REST neaktualizují čas poslední změny protokolu SMB a nebudou se zobrazovat jako změny v synchronizaci. Další informace najdete v [nejčastějších dotazech k souborům Azure](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Vrstvení cloudu 
Vrstvení cloudu je volitelná funkce Azure File Sync, ve které jsou často používané soubory ukládány do mezipaměti místně na serveru, zatímco všechny ostatní soubory jsou vrstveny do souborů Azure na základě nastavení zásad. Další informace najdete v tématu [Principy vrstvení cloudu](storage-sync-cloud-tiering.md).

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Azure File Sync požadavky na systém a interoperabilita 
Tato část popisuje Azure File Sync požadavky na systém agenta a interoperabilitu s funkcemi a rolemi Windows serveru a řešeními třetích stran.

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
    Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
Zobrazení výsledků ve formátu CSV:
```powershell
    $errors = Invoke-AzStorageSyncCompatibilityCheck […]
    $errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="system-requirements"></a>Systémové požadavky
- Server, na kterém je spuštěná jedna z následujících verzí operačního systému:

    | Verze | Podporované SKU | Podporované možnosti nasazení |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | Datacenter a Standard | Úplné a základní |
    | Windows Server 2016 | Datacenter a Standard | Úplné a základní |
    | Windows Server 2012 R2 | Datacenter a Standard | Úplné a základní |
    | Windows Server IoT 2019 pro úložiště| Datacenter a Standard | Úplné a základní |
    | Windows Storage Server 2016| Datacenter a Standard | Úplné a základní |
    | Windows Storage Server 2012 R2| Datacenter a Standard | Úplné a základní |

    Budoucí verze Windows serveru budou přidány při jejich vydávání.

    > [!Important]  
    > Doporučujeme, aby všechny servery, které používáte se službou, Azure File Sync aktuální s nejnovějšími aktualizacemi web Windows Update. 

- Server, který má minimálně 2 GiB paměti.

    > [!Important]  
    > Pokud server běží na virtuálním počítači s povolenou dynamickou pamětí, měl by být virtuální počítač nakonfigurovaný s minimální 2048 MiB paměti.
    
- Místně připojený svazek formátovaný pomocí systému souborů NTFS.

### <a name="file-system-features"></a>Funkce systému souborů

| Funkce | Stav podpory | Poznámky |
|---------|----------------|-------|
| Seznamy ACL | Plně podporováno | Seznamy řízení přístupu systému Windows jsou zachovány Azure File Sync a jsou vynutily Windows Server na koncových bodech serveru. Seznamy řízení přístupu (ACL) Windows nejsou (zatím) podporované soubory Azure, pokud se k souborům dostanete přímo v cloudu. |
| Pevné odkazy | Přeskočeno | |
| Symbolické odkazy | Přeskočeno | |
| Přípojné body | Částečně podporováno | Přípojné body můžou být kořenem koncového bodu serveru, ale pokud se nacházejí v oboru názvů koncového bodu serveru, přeskočí se. |
| Spojení | Přeskočeno | Například systém souborů DFS (Distributed File System) DfrsrPrivate a DFSRoots složky. |
| Body rozboru | Přeskočeno | |
| Komprese NTFS | Plně podporováno | |
| Zhuštěné soubory | Plně podporováno | Synchronní soubory se synchronizují (nejsou blokované), ale synchronizují se s cloudem jako úplný soubor. Pokud se obsah souboru změní v cloudu (nebo na jiném serveru), soubor už nebude po stažení změny zhuštěný. |
| Alternativní datové proudy (INZERÁTy) | Zachované, ale nesynchronizované | Například klasifikační značky vytvořené infrastrukturou klasifikace souborů se nesynchronizují. Existující klasifikační značky souborů na všech koncových bodech serveru jsou ponechány beze změny. |

> [!Note]  
> Podporovány jsou pouze svazky NTFS. ReFS, FAT, FAT32 a jiné systémy souborů se nepodporují.

### <a name="files-skipped"></a>Vynechané soubory

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
| Informace o svazcích \\systému | Složka specifická pro svazek |
| $RECYCLE. BIN| Složka |
| \\SyncShareState | Složka pro synchronizaci |

### <a name="failover-clustering"></a>Clustering s podporou převzetí služeb při selhání
Clustering s podporou převzetí služeb při selhání ve Windows serveru podporuje Azure File Sync pro možnost nasazení souborový server pro obecné použití. Clustering s podporou převzetí služeb při selhání není podporován na Souborový server se škálováním na více systémů pro data aplikací (SOFS) nebo na sdílených svazcích clusteru (CSV).

> [!Note]  
> Aby synchronizace fungovala správně, musí být na každém uzlu v clusteru s podporou převzetí služeb při selhání nainstalovaný agent Azure File Sync.

### <a name="data-deduplication"></a>Odstranění duplicitních dat
**Windows server 2016 a Windows server 2019**   
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

**Obory názvů DFS (DFS-n)** : Azure File Sync se plně podporují na serverech DFS-N. Agenta Azure File Sync můžete nainstalovat na jeden nebo více členů DFS-N a synchronizovat data mezi koncovými body serveru a koncovým bodem cloudu. Další informace najdete v tématu [Přehled oborů názvů DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replikace DFS (DFS-r)** : vzhledem k tomu, že DFS-r a Azure File Sync jsou obě řešení replikace, doporučujeme ve většině případů nahradit DFS-r Azure File Sync. Existuje však několik scénářů, kdy chcete použít DFS-R a Azure File Sync společně:

- Migrujete z nasazení systému souborů DFS-R do nasazení Azure File Sync. Další informace najdete v tématu [migrace nasazení replikace DFS (DFS-R) do Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Ne každý místní server, který potřebuje kopii dat souboru, se může připojit přímo k Internetu.
- Servery větví konsolidují data na jeden hub Server, pro který chcete použít Azure File Sync.

Pro Azure File Sync a DFS-R pro práci vedle sebe:

1. Na svazcích s replikovanými složkami DFS-R musí být zakázané Azure File Sync vrstvení cloudu.
2. Koncové body serveru by se neměly konfigurovat v replikačních složkách jen pro čtení DFS-R.

Další informace najdete v tématu [přehled replikace DFS](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Nástroj Sysprep
Použití nástroje Sysprep na serveru s nainstalovaným agentem Azure File Sync není podporováno a může vést k neočekávaným výsledkům. Instalace agenta a registrace serveru by se měly vyskytnout po nasazení image serveru a dokončení zkrácené instalace nástroje Sysprep.

### <a name="windows-search"></a>Windows Search
Pokud je na koncovém bodu serveru povolené vrstvení cloudu, soubory, které jsou vrstveny, se přeskočí a neindexují služba Windows Search. Soubory bez vrstev jsou indexovány správně.

### <a name="antivirus-solutions"></a>Antivirová řešení
Vzhledem k tomu, že antivirová práce funguje, prohledává soubory pro známý škodlivý kód, antivirový produkt může způsobit odvolání vrstvených souborů. Ve verzích 4,0 a vyšších Azure File Sync agenta mají vrstvený soubor FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS nastaven atribut zabezpečení systému Windows. Doporučujeme, abyste se od dodavatele softwaru dozvěděli, jak nakonfigurovat jejich řešení tak, aby přeskočilo čtení souborů s touto sadou atributů (mnoho z nich provede automaticky). 

Interní antivirová řešení Microsoftu, Windows Defender a System Center Endpoint Protection (SCEP), automaticky přeskočí čtení souborů s nastavením tohoto atributu. Otestovali jsme je a zjistili jsme jeden malý problém: Když přidáte server do existující skupiny synchronizace, soubory menší než 800 bajtů se odvolají (stáhnou) na novém serveru. Tyto soubory zůstanou na novém serveru a nebudou vrstveny, protože nesplňují požadavek na velikost vrstev (> 64 KB).

> [!Note]  
> Dodavatelé antivirové ochrany můžou zkontrolovat kompatibilitu mezi jejich produktem a Azure File Sync pomocí [Azure File Sync sady testů kompatibility antivirové ochrany](https://www.microsoft.com/download/details.aspx?id=58322), která je k dispozici ke stažení na webu Microsoft Download Center.

### <a name="backup-solutions"></a>Řešení zálohování
Podobně jako u antivirových řešení můžou řešení zálohování způsobit odvolání vrstvených souborů. K zálohování sdílené složky Azure místo místního záložního produktu doporučujeme použít řešení zálohování v cloudu.

Pokud používáte místní řešení zálohování, měli byste zálohy provádět na serveru ve skupině synchronizace, která má zakázanou vrstvu v cloudu. Při provádění obnovení použijte možnosti obnovení na úrovni svazku nebo souboru. Soubory obnovené pomocí možnosti obnovení na úrovni souborů budou synchronizovány do všech koncových bodů ve skupině synchronizace a stávající soubory budou nahrazeny verzí obnovenou ze zálohy.  Obnovení na úrovni svazku nebude nahrazovat novější verze souborů ve sdílené složce Azure nebo v jiných koncových bodech serveru.

> [!Note]  
> Úplné obnovení systému (BMR) může způsobit neočekávané výsledky a aktuálně se nepodporuje.

> [!Note]  
> Ve verzi 9 agenta Azure File Sync se teď na svazcích, na kterých je povolená vrstva cloudu, podporují snímky služby Stínová kopie svazku (včetně karty předchozí verze). Je však nutné povolit kompatibilitu předchozí verze prostřednictvím prostředí PowerShell. [Zjistěte jak](storage-files-deployment-guide.md).

### <a name="encryption-solutions"></a>Řešení šifrování
Podpora šifrovacích řešení závisí na způsobu jejich implementace. Azure File Sync je známo, že funguje:

- Šifrování nástrojem BitLocker
- Azure Information Protection, Azure Rights Management Services (Azure RMS) a Active Directory RMS

Azure File Sync je známo, že nepracuje s:

- Systém souborů EFS (Encrypted File System)

Obecně platí, Azure File Sync by měla podporovat interoperabilitu s řešeními šifrování, která se nachází pod systémem souborů, jako je například BitLocker, a s řešeními, která jsou implementována ve formátu souboru, jako je například Azure Information Protection. Pro řešení, která se nachází nad systémem souborů (třeba NTFS EFS), se neudělala žádná zvláštní interoperabilita.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Další řešení pro správu hierarchických úložišť (HSM)
S Azure File Sync by se neměla používat žádná další řešení HSM.

## <a name="region-availability"></a>Regionální dostupnost
Azure File Sync je k dispozici pouze v následujících oblastech:

| Region (Oblast) | Umístění Datacenter |
|--------|---------------------|
| Austrálie – východ | Nový Jižní Wales |
| Austrálie – jihovýchod | Victoria |
| Brazílie – jih | Sao Paulo – stát |
| Střední Kanada | Toronto |
| Východní Kanada | Québec |
| Střední Indie | Puné |
| Střední USA | Iowa |
| Východní Asie | Hongkong – zvláštní správní oblast |
| Východní USA | Virginie |
| USA – východ 2 | Virginie |
| Francie – střed | Paříž |
| Francie – jih * | Marseille |
| Korea – střed | Soul |
| Korea – jih | Pusan |
| Japonsko – východ | Tokio, Saitama |
| Japonsko – západ | Ósaka |
| Středoseverní USA | Illinois |
| Severní Evropa | Irsko |
| Jižní Afrika – sever | Johannesburg |
| Jižní Afrika – západ * | Kapské město |
| Středojižní USA | Texas |
| Jižní Indie | Čennaj |
| Jihovýchodní Asie | Singapur |
| Spojené království – jih | Londýn |
| Velká Británie – západ | Cardiff |
| US Gov – Arizona | Arizona |
| US Gov – Texas | Texas |
| USA – Virginie | Virginie |
| Spojené arabské emiráty – sever | Dubaj |
| Spojené arabské emiráty střed * | Abú Zabí |
| Západní Evropa | Nizozemsko |
| Středozápadní USA | Wyoming |
| Západní USA | Kalifornie |
| Západní USA 2 | Washington |

Azure File Sync podporuje synchronizaci jenom se sdílenou složkou Azure, která je ve stejné oblasti jako služba synchronizace úložiště.

U oblastí označených hvězdičkami musíte kontaktovat podporu Azure a požádat o přístup k Azure Storage v těchto oblastech. Tento postup je popsaný v [tomto dokumentu](https://azure.microsoft.com/global-infrastructure/geographies/).

### <a name="azure-disaster-recovery"></a>Zotavení po havárii Azure
Kvůli ochraně před ztrátou oblasti Azure Azure File Sync integrace s možností [redundance redundantního úložiště](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS). GRS Storage funguje pomocí asynchronního blokování replikace mezi úložištěm v primární oblasti, se kterým obvykle pracujete, a úložištěm v spárované sekundární oblasti. V případě havárie, která způsobí, že se oblast Azure dočasně nebo trvale převede do režimu offline, bude Microsoft převzetí služeb při selhání úložiště do spárované oblasti. 

> [!Warning]  
> Pokud používáte sdílenou složku Azure jako koncový bod cloudu v účtu úložiště GRS, neměli byste iniciovat převzetí služeb při selhání účtu úložiště. Pokud to uděláte, synchronizace přestane fungovat a v případě nově vrstvených souborů může dojít i k neočekávané ztrátě dat. V případě ztráty oblasti Azure spustí Microsoft převzetí služeb při selhání účtu úložiště způsobem, který je kompatibilní s Azure File Sync.

Pro podporu integrace převzetí služeb při selhání mezi geograficky redundantním úložištěm a Azure File Sync jsou všechny oblasti Azure File Sync párovány s sekundární oblastí, která odpovídá sekundární oblasti používané v úložišti. Tyto páry jsou následující:

| Primární oblast      | Spárovaná oblast      |
|---------------------|--------------------|
| Austrálie – východ      | Austrálie – jihovýchod|
| Austrálie – jihovýchod | Austrálie – východ     |
| Brazílie – jih        | Středojižní USA   |
| Střední Kanada      | Východní Kanada        |
| Východní Kanada         | Střední Kanada     |
| Střední Indie       | Jižní Indie        |
| Střední USA          | Východ USA 2          |
| Východní Asie           | Jihovýchodní Asie     |
| Východní USA             | Západní USA            |
| Východ USA 2           | Střední USA         |
| Francie – střed      | Francie – jih       |
| Francie – jih        | Francie – střed     |
| Japonsko – východ          | Japonsko – západ         |
| Japonsko – západ          | Japonsko – východ         |
| Korea – střed       | Korea – jih        |
| Korea – jih         | Korea – střed      |
| Severní Evropa        | Západní Evropa        |
| Středoseverní USA    | Středojižní USA   |
| Jižní Afrika – sever  | Jižní Afrika – západ  |
| Jižní Afrika – západ   | Jižní Afrika – sever |
| Středojižní USA    | Středoseverní USA   |
| Jižní Indie         | Střední Indie      |
| Jihovýchodní Asie      | Východní Asie          |
| Spojené království – jih            | Velká Británie – západ            |
| Velká Británie – západ             | Spojené království – jih           |
| US Gov – Arizona      | US Gov – Texas       |
| USA – Iowa         | USA – Virginie    |
| USA – Virginie      | US Gov – Texas       |
| Západní Evropa         | Severní Evropa       |
| Středozápadní USA     | Západní USA 2          |
| Západní USA             | Východní USA            |
| Západní USA 2           | Středozápadní USA    |

## <a name="azure-file-sync-agent-update-policy"></a>Zásady aktualizace agenta Synchronizace souborů Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="recommended-azure-file-sync-machine-configuration"></a>Doporučená konfigurace Azure File Sync počítačů

Požadavky na Azure File Sync počítače se určují podle počtu objektů v oboru názvů a změn v datové sadě. Jeden server může být připojen k více skupinám synchronizace a počet objektů uvedených v následujících tabulkách účtů pro úplný obor názvů, ke kterému je server připojen. Například koncový bod serveru A s 10 000 000 objekty a koncovým bodem serveru B s 10 000 000 objekty = 20 000 000 objekty. Pro tento příklad nasazení doporučujeme 8CPU, 16GiB paměti pro stálý stav a (Pokud je to možné) 48GiB paměti pro počáteční migraci.
 
Data oboru názvů jsou ukládána v paměti z důvodů výkonu. Vzhledem k tomu, že větší obory názvů vyžadují více paměti, aby bylo možné udržet dobrý výkon a více změn vyžaduje více PROCESORů pro zpracování. 
 
V následující tabulce jsme poskytovali jak velikost oboru názvů, tak i převod na kapacitu pro typické sdílené složky pro obecné účely, kde Průměrná velikost souboru je 512KiB. Pokud jsou velikosti souborů menší, zvažte přidání další paměti pro stejnou velikost kapacity. Založte konfiguraci paměti na velikost oboru názvů.

| Velikost oboru názvů – soubory & adresářů (miliony)  | Typická kapacita (TiB)  | Jádra procesoru  | Doporučená paměť (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (počáteční synchronizace)/2 (Typická četnost změn)      |
| 5        | 2.3     | 2        | 16 (počáteční synchronizace)/4 (Typická četnost změn)    |
| 10       | 4.7     | 4        | 32 (počáteční synchronizace)/8 (Typická četnost změn)   |
| 30       | 14,0    | 8        | 48 (počáteční synchronizace)/16 (Typická četnost změn)   |
| 50       | 23,3    | 16       | 64 (počáteční synchronizace)/32 (Typická četnost změn)  |
| 100 *     | 46,6    | 32       | 128 (počáteční synchronizace)/32 (Typická četnost změn)  |

\*více než 100 000 000 souborů & adresářů se v tuto chvíli nepodporuje. Toto je měkký limit.

> [!TIP]
> Počáteční synchronizace oboru názvů je náročná operace a doporučujeme přidělit více paměti, dokud nebude dokončena počáteční synchronizace. To se nevyžaduje, ale může urychlit počáteční synchronizaci. 
> 
> Typická četnost změn je 0,5% změny oboru názvů za den. Pro vyšší úrovně změn zvažte přidání dalších PROCESORů. 

## <a name="next-steps"></a>Další kroky
* [Zvážení brány firewall a nastavení proxy serveru](storage-sync-files-firewall-and-proxy.md)
* [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
* [Nasazení souborů Azure](storage-files-deployment-guide.md)
* [Nasazení Synchronizace souborů Azure](storage-sync-files-deployment-guide.md)
* [Monitorování Synchronizace souborů Azure](storage-sync-files-monitoring.md)
