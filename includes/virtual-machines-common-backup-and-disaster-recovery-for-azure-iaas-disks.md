
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Zálohování a zotavení po havárii pro disky Azure IaaS

Tento článek vysvětluje, jak bude vytvořen plán pro zálohování a zotavení po havárii (DR) disků v Azure a virtuální počítače IaaS (VM). Tento dokument popisuje spravovaných i nespravovaných disky.

Nejprve nabídneme možnosti integrované odolnost proti chybám v platformy Azure, která pomáhá chránit proti místní selhání. Potom probereme scénáře po havárii, není plně předmětem integrované funkce. Toto je hlavní téma řešený v tomto dokumentu. Ukážeme také několik příkladů zatížení scénáře, kde můžete použít různé zálohování a zotavení po Havárii aspekty. Potom jsme zkontrolujte možná řešení pro zotavení po Havárii IaaS disky. 

## <a name="introduction"></a>Úvod

Platformy Azure využívá různé metody pro redundanci a odolnost proti chybám k ochraně zákazníků selhání lokalizované hardwaru. Místní selhání může zahrnovat problémy s počítačem serveru Azure Storage, který ukládá část dat pro virtuální disk nebo selhání SSD nebo pevný disk na tomto serveru. Tyto součásti selhání izolované hardwaru může dojít během normálních operací. 

Platformy Azure je navržený jako odolné vůči selhání. Hlavní havárie může způsobit selhání nebo inaccessibility mnoho serverů úložiště nebo i celého datového centra. I když z lokálních selhání jsou obvykle chráněné virtuální počítače a disky, jsou nutné k ochraně vaše úlohy závažné selhání celou oblast, jako je například významnější havárie, který může ovlivnit virtuálních počítačů a disků další kroky.

Kromě možnosti selhání platformy může dojít k problémům s zákazníka aplikace nebo data. Například nová verze aplikace může nechtěně změňte data, která způsobuje, že přerušení. V takovém případě můžete chtít vrátit aplikace a data, která mají na předchozí verzi, která obsahuje poslední známého funkčního stavu. To vyžaduje údržbu pravidelných záloh.

Místní zotavení po havárii je nutné zálohovat vaše disky virtuálních počítačů IaaS v jiné oblasti. 

Předtím, než se podíváme na zálohování a zotavení po Havárii možnosti, můžeme recap několik metod k dispozici pro zpracování lokálních selhání.

### <a name="azure-iaas-resiliency"></a>Odolnost Azure IaaS

*Odolnost proti chybám* odkazuje na tolerance pro běžné chyby, ke kterým došlo v hardwarové součásti. Odolnost proti chybám je možnost obnovení v případě selhání a i nadále fungovat. Nejedná se o zamezení selhání, ale odpověď na selhání způsobem, který zabraňuje výpadku nebo ztráty dat. Cílem odolnosti proti chybám je obnovení plně funkčního stavu aplikace co nejdříve po selhání. Virtuální počítače Azure a disky jsou navržené jako odolné vůči běžné chyby hardwaru. Podíváme, jak na platformu Azure IaaS poskytuje tuto odolnost proti chybám.

Virtuální počítač se skládá především ze dvou částí: serveru výpočetní a trvalé disky. Obě ovlivnit odolnost proti chybám virtuálního počítače.

Pokud dojde k selhání hardwaru, což je taková situace vzácná, hostitelský server výpočtů Azure, kde virtuální počítač Azure je určena pro automatické obnovení virtuálního počítače na jiný server. Pokud k tomu dojde, vaše restartování počítače a virtuálního počítače zpátky po určité době. Azure automaticky zjišťuje takové selhání hardwaru a provede obnovení k zajištění, že zákazník virtuálního počítače je k dispozici co nejdříve.

Týkající se disků IaaS odolnost dat je důležité pro platformu trvalého úložiště. Zákazníci, Azure mají důležitých podnikových aplikací běžících na IaaS a jsou závislé na trvalost dat. Azure návrhů ochranu těchto disků IaaS, s třemi redundantní kopie dat, který je uložený místně. Tyto kopie zadejte vysoká odolnost proti selhání místní. Pokud jeden z hardwarové součásti, které obsahuje váš disk selže, virtuální počítač nemá vliv, protože nejsou k dispozici dvě další kopie pro podporu požadavků na disku. Funguje bez problémů, i v případě selžou dva různé hardwarové komponenty, které podporují disk ve stejnou dobu (což je velmi výjimečných). 

Chcete, aby vždy udržovat tři repliky, Azure Storage automaticky vytvoří novou kopii dat na pozadí, pokud jeden ze tří zkopíruje přestane být k dispozici. Proto by nemělo být potřeba pomocí Azure disky RAID pro odolnost proti chybám. Jednoduché RAID 0 konfigurace by mělo být dostatečné pro prokládání disků, v případě potřeby vytvořte větší svazky.

Z důvodu této architektuře Azure konzistentně vydal podnikové úrovni odolnost pro IaaS disky s špičkový nula procent [míra selhání celoročním poměru](https://en.wikipedia.org/wiki/Annualized_failure_rate).

Lokalizované hardwaru chyb ve výpočetní hostitele nebo v platformě úložiště můžete někdy výsledek v dočasné nedostupnosti virtuální počítač, který je předmětem [smlouva Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) dostupnosti virtuálních počítačů. Azure poskytuje špičkový SLA pro jeden instancí virtuálních počítačů, které používají Azure Premium Storage disky.

Aby se předešlo úlohy aplikací z výpadek kvůli dočasné nedostupnosti disk nebo virtuální počítač, zákazníci mohou používat [skupiny dostupnosti](../articles/virtual-machines/windows/manage-availability.md). Dvě nebo více virtuálních počítačů v nastavení dostupnosti poskytnout redundanci pro aplikaci. Azure potom vytvoří tyto virtuální počítače a disky v doménách selhání samostatné s různé součásti napájení, sítě a serveru. 

Z důvodu těchto domén samostatné selhání selhání lokalizované hardwaru obvykle nemají vliv na víc virtuálních počítačů v sadě ve stejnou dobu. Domény samostatné selhání poskytuje vysokou dostupnost pro vaši aplikaci. Považuje za vhodné použít skupiny dostupnosti, pokud se vyžaduje vysoká dostupnost. V další části jsou popsané aspekt obnovení po havárii.

### <a name="backup-and-disaster-recovery"></a>Zálohování a zotavení po havárii

Zotavení po havárii je možnost obnovení z výjimečných, ale hlavní, incidenty. To zahrnuje bez přechodná, celou škálování selhání, jako je například přerušení služby, které ovlivňuje celou oblast. Zotavení po havárii zahrnuje zálohování dat a archivaci a mohou zahrnovat ruční zásah, například obnovování databáze ze zálohy.

Platformy Azure integrovanou ochranu proti selhání lokalizované nemusí chránit plně virtuálních počítačů/disků, pokud významnější havárie způsobí, že ve velkém měřítku výpadků. To zahrnuje závažné události, například pokud datacentru zasažení hurikán, zemětřesení, ještě efektivněji, nebo pokud dojde selhání jednotky ve velkém měřítku hardwaru. Kromě toho může dojít k chybám způsobeným problémy dat nebo aplikace.

K ochraně vašich úloh IaaS z výpadky, by měl plánování redundance a mít zálohy za účelem umožnění obnovy. Pro zotavení po havárii měli byste zálohovat v jiném geografické umístění směrem od primární lokality. To pomáhá zajistit, že záloha není ovlivněn stejnou událost, který původně vliv virtuálního počítače nebo disků. Další informace najdete v tématu [zotavení po havárii pro aplikace Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).

Vaše zotavení po Havárii může zvážit následující aspekty:

- Vysoká dostupnost: možnost aplikace a pokračovat v činnosti ve stavu v pořádku, bez významné výpadky. Podle *stavu v pořádku*, jsme znamenat reaguje aplikace a uživatelé můžou připojit k aplikaci a pracovat s ním. Některé důležité aplikace a databáze může být potřeba vždycky být k dispozici, i v případě, že tam jsou chyby samotné platformy. Pro tyto úlohy mohou muset naplánovat redundance pro aplikace a také data.

- Odolnost data: V některých případech je hlavní pozornost zajištění uchování dat v případě havárie. Proto může být nutné zálohování dat v jiné lokalitě. Pro taková zatížení nemusí úplné redundance pro aplikace, ale pouze pravidelného zálohování disky.

## <a name="backup-and-dr-scenarios"></a>Zálohování a zotavení po Havárii scénáře

Podívejme se na několik příkladů typické scénáře zatížení aplikací a jaké jsou požadavky týkající se plánování pro zotavení po havárii.

### <a name="scenario-1-major-database-solutions"></a>Scénář 1: Hlavní databáze řešení

Vezměte v úvahu produkční databázový server, jako je SQL Server nebo Oracle, který podporuje vysokou dostupnost. Kritický produkční aplikace a uživatelé závisí na tuto databázi. Plán obnovení po havárii pro tento systém může být nutné podporovat následující požadavky:

- Data musí být chráněný a obnovitelný.
- Server musí být k dispozici pro použití.

Plán obnovení po havárii může vyžadovat zachování repliky databáze v jiné oblasti jako záložní. V závislosti na požadavcích pro dostupnost serveru a obnovení dat může řešení rozsahu stránku aktivní aktivní nebo aktivní – pasivní repliku až pravidelné zálohování offline dat. Relační databáze, například SQL Server a Oracle, nabízí různé možnosti pro replikaci. Pro systém SQL Server, použijte [skupiny dostupnosti AlwaysOn serveru SQL](https://msdn.microsoft.com/library/hh510230.aspx) pro vysokou dostupnost.

Databáze NoSQL, jako je MongoDB, také podporují [repliky](https://docs.mongodb.com/manual/replication/) redundanci. Repliky pro zajištění vysoké dostupnosti se používají.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Scénář 2: Cluster redundantní virtuální počítače

Vezměte v úvahu zatížení zpracovává cluster virtuálních počítačů, které poskytují redundanci a vyrovnávání zatížení. Jedním z příkladů je cluster Cassandra nasazené v oblasti. Tento typ architektury již poskytuje vysokou úroveň redundance v rámci této oblasti. Chránit úlohy v případě selhání místní úrovni, byste však měli zvážit šíření clusteru v rámci dvou oblastí nebo prováděním pravidelné zálohy jiné oblasti.

### <a name="scenario-3-iaas-application-workload"></a>Scénář 3: IaaS aplikace zatížení

Podívejme se na úlohy aplikace IaaS. Například to může být typické provozní úlohu spuštěnou na virtuální počítač Azure. Může být na webovém serveru nebo souborového serveru, která uchovává obsah a dalším prostředkům lokality. Může být také uživatelské obchodní aplikace běžící v virtuální počítač, který uložené prostředky, stav aplikace a jeho data na disky virtuálních počítačů. V takovém případě je důležité se ujistit, že provedete zálohy v pravidelných intervalech. Četnost zálohování by měla být založena na povaze zatížení virtuálních počítačů. Například pokud aplikace spouští každý den a mění data, zálohování by měl být bere každou hodinu.

Dalším příkladem je server pro sestavy, která získává data z jiných zdrojů a generuje souhrnné sestavy. Ztrátě tohoto virtuálního počítače nebo disků může vést ke ztrátě sestavy. Však může být možné znovu spustit proces vytváření sestav a znovu je obnovovat výstup. V takovém případě nemáte skutečně ke ztrátě dat, i v případě, že server pro sestavy se setkají s havárie. V důsledku toho může mít vyšší úroveň tolerance pro ztráty část dat na serveru sestav. V takovém případě jsou méně časté zálohování možnost, jak snížit náklady.

### <a name="scenario-4-iaas-application-data-issues"></a>Scénář 4: Problémy s dat aplikace IaaS

Další možností jsou IaaS aplikace dat problémy. Vezměte v úvahu aplikace, která vypočítá, udržuje a slouží důležitá obchodní data, například informace o cenách. Nová verze aplikace měla softwaru chybu, která nesprávně počítaný cenách a poškozený stávající data a obchodu Spojených států obsloužených platformu. Zde je nejlepším řešením vrátit na předchozí verzi aplikace a data. Chcete-li povolit, trvat pravidelné zálohy systému.

## <a name="disaster-recovery-solution-azure-backup"></a>Řešení zotavení po havárii: zálohování Azure 

[Zálohování Azure](https://azure.microsoft.com/services/backup/) slouží k zálohování a zotavení po Havárii a funguje s [discích spravovaných](../articles/virtual-machines/windows/managed-disks-overview.md) a také [nespravované disky](../articles/virtual-machines/windows/about-disks-and-vhds.md#unmanaged-disks). Můžete vytvořit úlohu zálohování s zálohy založené na čase, snadno obnovení virtuálních počítačů a zásady uchovávání záloh. 

Pokud používáte [disky úložiště Premium](../articles/virtual-machines/windows/premium-storage.md), [discích spravovaných](../articles/virtual-machines/windows/managed-disks-overview.md), nebo jiných typů disku s [místně redundantního úložiště](../articles/storage/common/storage-redundancy-lrs.md) možnost, je zvlášť důležité, aby pravidelně zotavení po Havárii zálohování. Zálohování Azure ukládá data ve vašem trezoru služeb zotavení pro dlouhodobé uchovávání. Vyberte [geograficky redundantní úložiště](../articles/storage/common/storage-redundancy-grs.md) možnost pro obnovení zálohy služby úložiště. Tato možnost zajistí, že zálohování jsou replikovány v jiné oblasti Azure pro zabezpečení z místní havárie.

Pro [nespravované disky](../articles/virtual-machines/windows/about-disks-and-vhds.md#unmanaged-disks), můžete použít typ místně redundantní úložiště pro disků IaaS, ale ujistěte se, že Azure Backup je povolena možnost geograficky redundantní úložiště pro trezor služeb zotavení.

> [!NOTE]
> Pokud použijete [geograficky redundantní úložiště](../articles/storage/common/storage-redundancy-grs.md) nebo [geograficky redundantní úložiště s přístupem pro čtení](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) možnost pro nespravovaná disky, je stále nutné snímky konzistentní s aplikacemi pro zálohování a zotavení po Havárii. Použijte buď [Azure Backup](https://azure.microsoft.com/services/backup/) nebo [snímky konzistentní](#alternative-solution-consistent-snapshots).

 V následující tabulce je uveden seznam řešení, které jsou k dispozici pro zotavení po Havárii.

| Scénář | Automatické replikace | Řešení zotavení po Havárii |
| --- | --- | --- |
| Disky úložiště Premium | Místní ([místně redundantního úložiště](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Managed Disks | Místní ([místně redundantního úložiště](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Nespravované místně redundantní úložiště s disky | Místní ([místně redundantního úložiště](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Nespravované geograficky redundantní úložiště s disky | Pro různé oblasti ([geograficky redundantní úložiště](../articles/storage/common/storage-redundancy-grs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Snímky konzistentní s aplikacemi](#alternative-solution-consistent-snapshots) |
| Disky nespravované geograficky redundantní úložiště s přístupem pro čtení | Pro různé oblasti ([geograficky redundantní úložiště s přístupem pro čtení](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Snímky konzistentní s aplikacemi](#alternative-solution-consistent-snapshots) |

Vysoká dostupnost nejlépe splníte použitím spravovaných disků ve skupině dostupnosti nastavena společně s Azure Backup. Pokud používáte nespravované disky, stále můžete zálohování Azure pro zotavení po Havárii. Pokud není možné používat Azure Backup, pak trvá [snímky konzistentní](#alternative-solution-consistent-snapshots), jak je popsáno v další části, je alternativní řešení pro zálohování a zotavení po Havárii.

Vaše volby pro vysokou dostupnost, zálohování a zotavení po Havárii aplikace nebo infrastruktury úrovni může být reprezentován následujícím způsobem:

| Úroveň |   Vysoká dostupnost   | Zálohování a zotavení po Havárii |
| --- | --- | --- |
| Aplikace | SQL Server AlwaysOn | Azure Backup |
| Infrastruktura    | Skupina dostupnosti  | Geograficky redundantní úložiště s snímky konzistentní s aplikacemi |

### <a name="using-azure-backup"></a>Pomocí zálohování Azure 

[Zálohování Azure](../articles/backup/backup-azure-vms-introduction.md) můžete zálohovat virtuální počítače s Windows nebo Linux Azure Recovery services trezoru. Zálohování a obnovení důležitých podnikových dat ztěžuje skutečnost, že důležitých podnikových dat je třeba zálohovat v době spuštění aplikace, které produkují data. 

Chcete-li vyřešit tento problém, Azure Backup poskytuje zálohování konzistentní s aplikací pro platformu Microsoft. Používá službu Stínová zajistit, že je správně zapisovat data do úložiště. Pro virtuální počítače s Linuxem je možné, pouze konzistentními soubory zálohy, protože Linux nemá funkce odpovídající službu Stínová.

![Azure Backup toku][1]

Spustí úlohu zálohování Azure Backup v naplánovaném čase spustí rozšíření zálohování nainstalovaná ve virtuálním počítači k pořízení snímku v daném okamžiku. Snímek se provede v koordinaci s službu Stínová získat konzistentního snímku disků ve virtuálním počítači bez nutnosti ho vypnout. Rozšíření zálohování ve virtuálním počítači vyprázdnění všech zápisů před pořízení konzistentního snímku všechny disky. Po pořízení snímku, se data přenáší přes Azure Backup do trezoru záloh. Chcete-li proces zálohování efektivnější, služba identifikuje a přenáší pouze bloky dat, které se změnily po posledním zálohování.

Pokud chcete obnovit, můžete zobrazit dostupné zálohy pomocí zálohování Azure a poté zahájit obnovení. Můžete vytvořit a obnovit zálohování Azure prostřednictvím [portál Azure](https://portal.azure.com/), pomocí [pomocí prostředí PowerShell](../articles/backup/backup-azure-vms-automation.md), nebo pomocí [rozhraní příkazového řádku Azure](/cli/azure/). 

### <a name="steps-to-enable-a-backup"></a>Postup povolení zálohování

Pomocí následujících kroků k povolení zálohování virtuálních počítačů pomocí [portál Azure](https://portal.azure.com/). Není k dispozici různé varianty v závislosti na přesný scénář. Odkazovat [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) dokumentace pro úplné podrobnosti. Azure také zálohování [podporuje virtuální počítače s spravované disky](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Vytvoření trezoru služeb zotavení pro virtuální počítač:

    a. V [portál Azure](https://portal.azure.com/), Procházet **všechny prostředky** a najít **trezory služeb zotavení**.

    b. Na **trezory služeb zotavení** nabídky, klikněte na tlačítko **přidat** a postupujte podle kroků pro vytvoření nového trezoru ve stejné oblasti jako virtuální počítač. Například pokud virtuální počítač je v oblasti západní USA, vyberte západní USA trezoru.

2.  Ověření úložiště replikace pro nově vytvořený trezor. Přístup k trezoru pod **trezory služeb zotavení** a přejděte na **nastavení** > **konfigurace zálohování**. Ujistěte se, **geograficky redundantní úložiště** ve výchozím nastavení je vybraná možnost. To zajistí, že je váš trezor automaticky replikována do sekundárního datacentra. Například trezoru v západní USA se automaticky replikují do východní USA.

3.  Nakonfigurujte zásady zálohování a vyberte virtuální počítač ze stejné uživatelské rozhraní.

4.  Zkontrolujte, zda že je nainstalovaný na Virtuálním počítači agenta zálohování. Pokud váš virtuální počítač je vytvořen pomocí image Galerie Azure, Backup Agent již nainstalován. Jinak (tj. Pokud používáte vlastní image), postupujte podle pokynů k [nainstalujte agenta virtuálního počítače na virtuálním počítači](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent-on-the-virtual-machine).

5.  Ujistěte se, že virtuální počítač umožňuje připojení k síti pro službu zálohování funkce. Postupujte podle pokynů pro [připojení k síti](../articles/backup/backup-azure-arm-vms-prepare.md#establish-network-connectivity).

6.  Po dokončení předchozích kroků, zálohování se spustí v pravidelných intervalech podle zásady zálohování. V případě potřeby můžete aktivovat první zálohy ručně na řídicím panelu trezoru na portálu Azure.

Automatizace Azure Backup pomocí skriptů, najdete v části [rutiny prostředí PowerShell pro zálohování virtuálních počítačů](../articles/backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Kroky pro obnovení

Pokud potřebujete opravit nebo znovu vytvořit virtuální počítač, můžete obnovit virtuální počítač z jakýchkoli bodů obnovení záloh v trezoru. Existuje několik různých možností při provedení obnovení:

-   Můžete vytvořit nový virtuální počítač jako reprezentace v okamžiku zálohy virtuálního počítače.

-   Můžete obnovit disky a pak pomocí šablony virtuálního počítače upravit a znovu vytvořit obnoveným virtuálním Počítačem. 

Další informace najdete v tématu pokynů [obnovení virtuálních počítačů pomocí portálu Azure](../articles/backup/backup-azure-arm-restore-vms.md). Tento dokument popisuje také konkrétní kroky pro obnovení zálohy virtuálních počítačů pomocí vaší geograficky redundantní úložiště záloh, pokud dojde k havárii na primární datové centrum spárovaném datovém centru. V takovém případě Azure Backup používá výpočetní služba ze sekundární oblasti k vytvoření obnovený virtuální počítač.

Můžete také pomocí prostředí PowerShell pro [obnovení virtuálního počítače](../articles/backup/backup-azure-arm-restore-vms.md#restore-a-vm-during-an-azure-datacenter-disaster) nebo [vytváření nového virtuálního počítače z obnovit disky](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Alternativní řešení: snímky konzistentní s aplikacemi

Pokud není možné používat Azure Backup, můžete implementovat vlastní zálohovací mechanismus pomocí snímků. Vytváření snímky konzistentní s aplikacemi pro všechny disky, které používá virtuální počítač a pak replikuje tyto snímky jiné oblasti je složitý. Z tohoto důvodu zvažuje Azure pomocí služby zálohování jako vhodnější než vytváření vlastní řešení. 

Pokud používáte geograficky redundantní úložiště nebo geograficky redundantní úložiště s přístupem pro čtení pro disky, snímky jsou automaticky replikovat do sekundárního datacentra. Pokud používáte místně redundantního úložiště pro disky, budete muset replikovat data. Další informace najdete v tématu [zálohování virtuálních počítačů Azure nespravované disků se snímky přírůstkové](../articles/virtual-machines/windows/incremental-snapshots.md).

Snímek je reprezentace objektu v určitém bodě v čase. Snímek způsobuje fakturace pro přírůstkové velikost dat je blokování. Další informace najdete v tématu [vytvoření snímku blob](../articles/storage/blobs/storage-blob-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Vytvořit snímky, když běží virtuální počítač

I když v každém okamžiku může pořízení snímku, pokud je virtuální počítač spuštěný, je stále data vysílá na discích a snímky mohou obsahovat částečné operace, které byly v cestě. Navíc pokud existují několik disků související se situací, snímky různých disků může dojít v různých časech. To znamená, že tyto snímky nemusí být koordinované. To je obzvláště problematické pro prokládané svazky, jejichž souborů může být poškozená, pokud se během zálohování provedeny změny.

Abyste předešli této situaci, proces zálohování musí implementovat následující kroky:

1.  Freeze – všechny disky.

2.  Dojde k vyprázdnění všech zápisů čekající na vyřízení.

3.  [Vytvoření snímku blob](../articles/storage/blobs/storage-blob-snapshots.md) pro všechny disky.

Některé aplikace systému Windows, jako je SQL Server, zadejte koordinované zálohovací mechanismus pomocí služby Stínová svazku vytvořit zálohování konzistentní s aplikací. V systému Linux můžete použít nástroje, jako je fsfreeze pro spolupráci disky. Tento nástroj zajišťuje konzistentní soubor zálohy, ale není snímky konzistentní s aplikacemi. Tento proces je složité, aby měli byste zvážit použití [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) nebo řešení zálohování jiného výrobce, které již implementuje tento postup.

Předchozí výsledky zpracování v kolekci koordinované snímků pro všechny disky virtuálních počítačů, představující konkrétní zobrazení v okamžiku virtuálního počítače. Toto je bod obnovení zálohy pro virtuální počítač. Proces lze opakovat v naplánovaných intervalech k vytvoření pravidelné zálohy. V tématu [zkopírujte zálohy do jiné oblasti](#copy-the-snapshots-to-another-region) kroky, jak zkopírovat snímky do jiné oblasti pro zotavení po Havárii.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Vytvářet snímky, zatímco virtuální počítač v režimu offline

Další možnost vytvářet konzistentní zálohy je vypnutí virtuálního počítače a pořízení snímků blob každého disku. Pořizování snímků objektu blob je jednodušší než koordinace snímky spuštění virtuálního počítače, ale vyžaduje to pár minut výpadku.

1. Vypněte virtuální počítač.

2. Vytvoření snímku každý objekt blob virtuálního pevného disku, který přebírá jenom pár sekund.

    Pro vytvoření snímku, můžete použít [prostředí PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [REST API pro Azure Storage](https://msdn.microsoft.com/library/azure/ee691971.aspx), [rozhraní příkazového řádku Azure](/cli/azure/), nebo jeden z knihovny klienta Azure Storage, jako například [ Klientská knihovna pro úložiště pro .NET](https://msdn.microsoft.com/library/azure/hh488361.aspx).

3. Spusťte virtuální počítač, který končí výpadek. Obvykle se celý proces dokončení během několika minut.

Tento proces vypočítá kolekce snímky konzistentní s aplikacemi pro všechny disky, poskytuje bod obnovení zálohy pro virtuální počítač.

### <a name="copy-the-snapshots-to-another-region"></a>Zkopírujte snímky do jiné oblasti

Vytváření snímků samostatně nemusí být dostatečné pro zotavení po Havárii. Zálohy snímků musí také replikovat do jiné oblasti.

Pokud používáte geograficky redundantní úložiště nebo geograficky redundantní úložiště s přístupem pro čtení pro disky, snímky jsou replikovat do sekundární oblasti automaticky. Může být několik minut prodlevy před replikace. Pokud primární datové centrum ocitne mimo provoz, než snímky dokončit replikace, nemáte přístup snímky ze sekundárního datacentra. Pravděpodobnost, že to je malá.

> [!NOTE] 
> Jenom s disky v geograficky redundantní úložiště nebo čtení geograficky redundantní účet úložiště nechrání virtuálního počítače z havárie. Také musíte vytvořit koordinované snímky nebo používat Azure Backup. To je potřebné k obnovení virtuálního počítače do konzistentního stavu.

Pokud používáte místně redundantní úložiště, musíte zkopírovat snímky na jiný účet úložiště ihned po vytvoření snímku. Cíl kopírování může být účet místně redundantního úložiště v jiné oblasti, což vede k se kopie v oblasti vzdálené. Můžete také zkopírovat snímku na účet geograficky redundantní úložiště s přístupem pro čtení ve stejné oblasti. V takovém případě snímku líné replikují do vzdáleného sekundární oblast. Zálohování je chráněn z havárie v primární lokalitě po kopírování a se replikace nedokončí.

Pokud chcete zkopírovat vaší přírůstkové snímky pro zotavení po Havárii efektivně, zkontrolujte podle pokynů v [zálohování Azure nespravované disky virtuálních počítačů s přírůstkové snímky](../articles/virtual-machines/windows/incremental-snapshots.md).

![Zálohování Azure nespravované disky virtuálních počítačů s přírůstkové snímky][2]

### <a name="recovery-from-snapshots"></a>Obnovení ze snímků

Načíst snímek, zkopírujte jej, aby nový objekt blob. Pokud kopírujete snímek z primární účet, můžete zkopírovat snímku přes na základní objekt blob snímku. Tento proces obnoví disku do snímku. Tento proces se označuje jako povýšení snímku. Pokud kopírujete zálohy snímku pomocí jiného účtu, v případě účtu přístup pro čtení geograficky redundantní úložiště, zkopírujte jej do primární účet. Můžete zkopírovat snímek pomocí [pomocí prostředí PowerShell](../articles/storage/common/storage-powershell-guide-full.md) nebo pomocí nástroje azcopy. Další informace najdete v tématu [přenos dat pomocí nástroje příkazového řádku azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).

Pro virtuální počítače s více disky musíte zkopírovat všechny snímky, které jsou součástí stejného bodu koordinované obnovení. Po zkopírování snímky s možností zápisu objektů BLOB VHD, můžete použít objekty BLOB k opětovnému vytvoření virtuálního počítače pomocí šablony pro virtuální počítač.

## <a name="other-options"></a>Další možnosti

### <a name="sql-server"></a>SQL Server

SQL Server běžící ve virtuálním počítači má svou vlastní integrované funkce zálohovat databázi SQL serveru do úložiště objektů Blob v Azure nebo souboru sdílet. Pokud účet úložiště je geograficky redundantní úložiště nebo geograficky redundantní úložiště s přístupem pro čtení, dostanete v případě havárie, tyto zálohy v účtu úložiště sekundárního datacentra s stejná omezení jako výše popsané. Další informace najdete v tématu [zálohování a obnovení pro SQL Server na virtuálních počítačích Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). Kromě toho zálohovat a obnovit [skupiny dostupnosti AlwaysOn serveru SQL](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) můžete udržovat sekundární repliky databáze. To významně snižuje dobu obnovení po havárii.

## <a name="other-considerations"></a>Další důležité informace

Tento článek je popsané, jak zálohovat nebo pořizovat snímky virtuálních počítačů a jejich disky k podpoře zotavení po havárii a postup používání těch, které pokud chcete obnovit svá data. Spousta lidí s modelem Azure Resource Manager používá šablony k vytvoření jejich virtuální počítače a další infrastruktury v Azure. Chcete-li vytvořit virtuální počítač, který má stejnou konfiguraci pokaždé, když můžete použít šablonu. Pokud používáte vlastní Image pro vytváření virtuálních počítačů, je rovněž třeba ověřit ochrany obrázků s použitím účtu přístup pro čtení geograficky redundantní úložiště pro uložení je.

V důsledku toho procesu zálohování může být kombinací dvě věci:

- Zálohujte data (disky).
- Proveďte zálohu konfigurace (šablony a vlastních bitových kopií).

V závislosti na možnost zálohování, který zvolíte možná budete muset zpracování zálohování dat a konfigurace nebo služby zálohování může zpracovávat veškerý který pro vás.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Dodatek: Porozumění vlivu redundanci dat

Pro účty úložiště v Azure existují tři typy redundanci dat, které byste měli uvažovat o zotavení po havárii: místně redundantní, geograficky redundantní nebo geograficky redundantní s přístupem pro čtení. 

Místně redundantní úložiště uchovává tři kopie dat ve stejném datovém centru. Když virtuální počítač zapíše data, jsou aktualizovány všechny tři kopie před úspěchu je vrácen volajícímu, abyste věděli, že jsou identické. Disk je chráněný před místní selhání, protože je velmi nepravděpodobné, že jsou všechny tři kopie ovlivnění ve stejnou dobu. V případě místně redundantní úložiště neexistuje žádný geografická redundance, takže disk není chráněn před závažné chyby, které můžou ovlivnit celé jednotky datacenter nebo úložiště.

Geograficky redundantní úložiště a geograficky redundantní úložiště s přístupem pro čtení jsou uchovány tři kopie dat v primární oblasti, kde je vámi zvolené. Další tři kopie dat jsou uchovány v odpovídající sekundární oblasti, která se nastavuje pomocí Azure. Například pokud data uchováváte v západní USA, data se replikují do východní USA. Uchování kopie probíhá asynchronně a existuje malé zpoždění mezi aktualizace primárních a sekundárních lokalit. Repliky disků v sekundární lokalitě nejsou konzistentní u jednotlivých disků (s odloženým), ale nemusí být repliky více disků active vzájemně synchronizována. Pokud chcete, aby konzistentní repliky na více disků, je potřeba snímky konzistentní.

Hlavní rozdíl mezi geograficky redundantním úložištěm a geograficky redundantní úložiště s přístupem pro čtení je s geograficky redundantní úložiště s přístupem pro čtení, může číst sekundární kopie kdykoli. Pokud dojde k problému, který vykreslí data v primární oblasti nedostupné, tým Azure neustále snaží obnovíte přístup. Zatímco primární je mimo provoz, pokud máte přístup pro čtení geograficky redundantní úložiště s povoleným, můžete přístup k datům v sekundárního datacentra. Proto pokud máte v plánu číst z repliky, zatímco primární je nedostupná, pak geograficky redundantní úložiště s přístupem pro čtení měli zvážit.

Pokud se jím být významné výpadku, tým Azure může aktivovat selhání geograficky a změňte primární záznamy DNS tak, aby odkazovaly do sekundárního úložiště. Pokud máte geograficky redundantní úložiště nebo přístup pro čtení geograficky redundantní úložiště s povoleným, můžete v tomto okamžiku přístupu k datům v oblasti, která používá jako sekundární. Jinými slovy Pokud váš účet úložiště je geograficky redundantní úložiště a došlo k potížím, můžete přistupovat sekundární úložiště jenom v případě, že je geo-převzetí služeb při selhání.

Další informace najdete v tématu [co dělat, když dojde k výpadku Azure Storage](../articles/storage/common/storage-disaster-recovery-guidance.md). 

>[!NOTE] 
>Microsoft se řídí, zda dojde k selhání. Převzetí služeb při selhání není řízený na účet úložiště, takže není rozhodnuto pomocí jednotliví zákazníci. Implementace zotavení po havárii pro účty určité úložiště nebo disky virtuálního počítače, je nutné použít techniky popsané v tomto článku.



[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
