---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: aa7ddb75017a532b436b9a5cfc71d1a7c2832cb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77179056"
---
Tento článek vysvětluje, jak naplánovat zálohování a zotavení po havárii (DR) virtuálních počítačů IaaS (VM) a disků v Azure. Tento dokument se vztahuje na spravované i nespravované disky.

Nejprve se podíváme na integrované možnosti odolnosti proti chybám na platformě Azure, které pomáhají chránit před místními chybami. Pak diskutujeme o scénářích katastrof, které nejsou plně pokryty integrovanými funkcemi. Ukazujeme také několik příkladů scénářů úloh, kde lze použít různé aspekty zálohování a zotavení po havárii. Poté zkontrolujeme možná řešení pro zotavení po havárii disků IaaS.

## <a name="introduction"></a>Úvod

Platforma Azure používá různé metody redundance a odolnosti proti chybám, které pomáhají chránit zákazníky před selháním lokalizovaného hardwaru. Místní selhání mohou zahrnovat problémy s počítačem serveru Azure Storage, který ukládá část dat pro virtuální disk nebo selhání ssd nebo hdd na tomto serveru. K takovým selháním izolované hardwarové součásti může dojít během běžných operací.

Platforma Azure je navržena tak, aby byla odolná vůči těmto selháním. Velké havárie může mít za následek selhání nebo nedostupnost mnoha serverů úložiště nebo dokonce celé datové centrum. I když vaše virtuální počítače a disky jsou obvykle chráněné před lokalizovanými chybami, další kroky jsou nezbytné k ochraně úlohy před rozsáhlými katastrofickými chybami, jako je například závažná havárie, která může ovlivnit váš virtuální počítač a disky.

Kromě možnosti selhání platformy může dojít k problémům s aplikací nebo daty zákazníka. Například nová verze aplikace může nechtěně provést změnu dat, která způsobí, že k přerušení. V takovém případě můžete chtít vrátit aplikaci a data do předchozí verze, která obsahuje poslední známý dobrý stav. To vyžaduje udržování pravidelných záloh.

Pro zotavení po havárii místní, musíte zálohovat vaše virtuální počítače IaaS disky do jiné oblasti.

Než se podíváme na možnosti zálohování a zotavení po havárii, pojďme rekapitulovat několik metod, které jsou k dispozici pro zpracování lokalizovaných selhání.

### <a name="azure-iaas-resiliency"></a>Odolnost azure IaaS

*Odolnost proti chybám* odkazuje na toleranci pro normální selhání, ke kterým dochází v hardwarových součástech. Odolnost proti chybám je schopnost zotavit se z chyb a nadále fungovat. Nejedná se o způsoby, jak zabránit selhání, ale o reakci na selhání způsobem, který zabraňuje výpadkům nebo ztrátě dat. Cílem odolnosti proti chybám je obnovení plně funkčního stavu aplikace co nejdříve po selhání. Virtuální počítače a disky Azure jsou navržené tak, aby byly odolné vůči běžným hardwarovým chybám. Podívejme se, jak platforma Azure IaaS poskytuje tuto odolnost proti chybám.

Virtuální počítač se skládá hlavně ze dvou částí: výpočetní server a trvalé disky. Obě ovlivňují odolnost virtuálního počítače proti chybám.

Pokud hostitelský server Azure pro výpočetní výkon, ve kterém je umístěn váš virtuální počítač, dojde k selhání hardwaru, což je vzácné, Azure je navržený tak, aby automaticky obnovil virtuální počítač na jiném serveru. Pokud tento scénář restartuje počítač a virtuální počítač se vrátí po určité době. Azure automaticky detekuje takové selhání hardwaru a provede obnovení, aby zajistil, že virtuální počítač zákazníka bude k dispozici co nejdříve.

Pokud jde o disky IaaS, odolnost dat je důležitá pro platformu trvalého úložiště. Zákazníci Azure mají důležité obchodní aplikace spuštěné na IaaS a závisí na trvalost dat. Azure navrhuje ochranu pro tyto disky IaaS se třemi redundantními kopiemi dat, která jsou uložena místně. Tyto kopie poskytují vysokou odolnost proti místním selháním. Pokud jedna z hardwarových součástí, která obsahuje disk selže, váš virtuální počítač není ovlivněna, protože existují dvě další kopie pro podporu požadavků disku. Funguje to dobře, i když dvě různé hardwarové součásti, které podporují disk selhat ve stejnou dobu (což je vzácné). 

Chcete-li zajistit, že budete vždy udržovat tři repliky, Azure Storage automaticky vytvoří novou kopii dat na pozadí, pokud jedna ze tří kopií přestane být k dispozici. Proto by nemělo být nutné používat RAID s disky Azure pro odolnost proti chybám. Jednoduchá konfigurace RAID 0 by měla být dostatečná pro prokládání disků, pokud je to nutné, k vytvoření větších svazků.

Díky této architektuře Azure soustavně dodává odolnost na podnikové úrovni pro disky IaaS s špičkovou nulovou roční [poruchovou mírou](https://en.wikipedia.org/wiki/Annualized_failure_rate).

Lokalizované hardwarové chyby na výpočetním hostiteli nebo na platformě úložiště může někdy mít za následek dočasnou nedostupnost virtuálního počítače, který se vztahuje [azure sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/) pro dostupnost virtuálního počítače. Azure také poskytuje špičkovou sla pro jednotlivé instance virtuálních počítače, které používají Azure premium SSD.

Chcete-li chránit úlohy aplikací před prostojez z důvodu dočasné nedostupnosti disku nebo virtuálního počítače, zákazníci mohou používat [skupiny dostupnosti](../articles/virtual-machines/windows/manage-availability.md). Dva nebo více virtuálních počítačů v sadě dostupnosti poskytují redundanci pro aplikaci. Azure pak vytvoří tyto virtuální počítače a disky v samostatných doménách selhání s různými součástmi napájení, sítě a serveru.

Z důvodu těchto samostatných domén selhání lokalizované selhání hardwaru obvykle nemají vliv na více virtuálních počítačů v sadě současně. S samostatné domény selhání poskytuje vysokou dostupnost pro vaši aplikaci. Je vhodné používat sady dostupnosti, když je vyžadována vysoká dostupnost. Další část se zabývá aspektem zotavení po havárii.

### <a name="backup-and-disaster-recovery"></a>Zálohování a zotavení po havárii

Zotavení po havárii je schopnost zotavit se ze vzácných, ale velkých incidentů. Tyto incidenty zahrnují nepřechodné, rozsáhlé selhání, jako je například přerušení služby, které ovlivňuje celou oblast. Zotavení po havárii zahrnuje zálohování a archivaci dat a může zahrnovat ruční zásah, jako je například obnovení databáze ze zálohy.

Integrovaná ochrana platformy Azure proti lokalizovaným selháním nemusí plně chránit virtuální počítače nebo disky, pokud závažná katastrofa způsobí rozsáhlé výpadky. Tyto rozsáhlé výpadky zahrnují katastrofické události, například pokud je datové centrum zasaženo hurikánem, zemětřesením, požárem nebo pokud dojde k rozsáhlému selhání hardwarové jednotky. Kromě toho může dojít k chybám z důvodu problémů s aplikací nebo daty.

Chcete-li chránit úlohy IaaS před výpadky, měli byste plánovat redundanci a mít zálohy, které umožňují obnovení. Chcete-li obnovit zotavení po havárii, měli byste zálohovat v jiném geografickém umístění mimo primární lokalitu. Tento přístup pomáhá zajistit, že vaše záloha není ovlivněna stejnou událostí, která původně ovlivnila virtuální počítač nebo disky. Další informace najdete [v tématu Zotavení po havárii pro aplikace Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).

Vaše důležité informace o zotavení po havárii mohou zahrnovat následující aspekty:

- Vysoká dostupnost: Schopnost aplikace pokračovat v běhu v stavu v pořádku, bez významné prostoje. Podle *stavu v pořádku*, tento stav znamená, že aplikace reaguje a uživatelé se mohou připojit k aplikaci a pracovat s ní. Některé kritické aplikace a databáze může být nutné vždy k dispozici, i když jsou chyby v platformě. Pro tyto úlohy může být nutné naplánovat redundanci pro aplikaci, stejně jako data.

- Trvanlivost dat: V některých případech je hlavním aspektem zajištění zachování dat, pokud dojde k havárii. Proto budete pravděpodobně potřebovat zálohu dat v jiné lokalitě. Pro takové úlohy nemusí být nutné úplnou redundanci pro aplikaci, ale pouze pravidelné zálohování disků.

## <a name="backup-and-dr-scenarios"></a>Scénáře zálohování a zotavení po havárii

Podívejme se na několik typických příkladů scénářů úlohy aplikace a aspekty pro plánování zotavení po havárii.

### <a name="scenario-1-major-database-solutions"></a>Scénář 1: Hlavní databázová řešení

Zvažte produkční databázový server, jako je SQL Server nebo Oracle, který může podporovat vysokou dostupnost. Kritické produkční aplikace a uživatelé závisí na této databázi. Plán zotavení po havárii pro tento systém může být nutné podporovat následující požadavky:

- Data musí být chráněna a obnovitelná.
- Server musí být k dispozici pro použití.

Plán zotavení po havárii může vyžadovat údržbu repliky databáze v jiné oblasti jako záloha. V závislosti na požadavcích na dostupnost serveru a obnovení dat může řešení sahat od sítě replik aktivní nebo pasivní nebo pasivní až po pravidelné offline zálohování dat. Relační databáze, například SQL Server a Oracle, poskytují různé možnosti replikace. Pro SQL Server použijte [SQL Server AlwaysOn skupiny dostupnosti](https://msdn.microsoft.com/library/hh510230.aspx) pro vysokou dostupnost.

NoSQL databáze, jako mongoDB, také podporují [repliky](https://docs.mongodb.com/manual/replication/) pro redundanci. Repliky pro vysokou dostupnost se používají.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Scénář 2: Cluster redundantních virtuálních počítače

Zvažte úlohu zpracovat clusteru virtuálních disek, které poskytují redundanci a vyrovnávání zatížení. Jedním z příkladů je cluster Cassandra nasazený v oblasti. Tento typ architektury již poskytuje vysokou úroveň redundance v rámci této oblasti. Chcete-li však chránit úlohu před selháním na místní úrovni, měli byste zvážit rozložení clusteru mezi dvě oblasti nebo provádění pravidelných záloh do jiné oblasti.

### <a name="scenario-3-iaas-application-workload"></a>Scénář 3: Úloha aplikace IaaS

Podívejme se na zatížení aplikace IaaS. Tato aplikace může být například typické produkční úlohy spuštěné na virtuálním počítači Azure. Může se jedná o webový server nebo souborový server, který obsahuje obsah a další prostředky webu. Může se také jedná o vlastní vytvořenou obchodní aplikaci spuštěnou na virtuálním počítači, která ukládá svá data, prostředky a stav aplikace na disky virtuálního počítače. V takovém případě je důležité zajistit pravidelné zálohování. Frekvence zálohování by měla být založena na povaze úlohy virtuálních počítače. Například pokud aplikace běží každý den a upravuje data, pak záloha by měla být přijata každou hodinu.

Dalším příkladem je server pro vykazování, který získává data z jiných zdrojů a generuje agregované sestavy. Ztráta tohoto virtuálního počítače nebo disků může vést ke ztrátě sestav. Však může být možné znovu spustit proces vykazování a obnovit výstup. V takovém případě nemáte ve skutečnosti ztrátu dat, i v případě, že server pro vykazování je zasažen a havárii. V důsledku toho může mít vyšší úroveň tolerance pro ztrátu části dat na serveru pro vykazování. V takovém případě méně časté zálohování jsou možnost snížit náklady.

### <a name="scenario-4-iaas-application-data-issues"></a>Scénář 4: Problémy s daty aplikací IaaS

Další možností jsou problémy s daty aplikací IaaS. Zvažte aplikaci, která vypočítává, udržuje a obsluhuje důležitá komerční data, jako jsou informace o cenách. Nová verze aplikace měla softwarovou chybu, která nesprávně vypočítala ceny a poškodila existující obchodní data obsluhovaná platformou. Zde je nejlepší postup se vrátit k dřívější verzi aplikace a data. Chcete-li to povolit, pravidelně zálohujte systém.

## <a name="disaster-recovery-solution-azure-backup"></a>Řešení zotavení po havárii: Azure Backup 

[Azure Backup](https://azure.microsoft.com/services/backup/) se používá pro zálohování a zotavení po havárii a funguje se [spravovanými disky](../articles/virtual-machines/windows/managed-disks-overview.md) i s nespravovanými disky. Můžete vytvořit úlohu zálohování s časově založenými zálohami, snadným obnovením virtuálních počítače a zásadami uchovávání záloh.

Pokud používáte [disky SSD pro prémie](../articles/virtual-machines/windows/disks-types.md), [spravované disky](../articles/virtual-machines/windows/managed-disks-overview.md)nebo jiné typy disků s možností [místně redundantního úložiště,](../articles/storage/common/storage-redundancy-lrs.md) je obzvláště důležité provádět pravidelné zálohování zotavení po havárii. Azure Backup ukládá data do trezoru služeb pro obnovení pro dlouhodobé uchovávání. Zvolte [možnost geograficky redundantního úložiště](../articles/storage/common/storage-redundancy-grs.md) pro trezor služeb obnovení zálohování. Tato možnost zajišťuje, že zálohy jsou replikovány do jiné oblasti Azure pro zabezpečení před regionálními katastrofami.

Pro nespravované disky můžete použít místně redundantní typ úložiště pro disky IaaS, ale ujistěte se, že je azure backup povolena s možností geograficky redundantního úložiště pro trezor služeb pro obnovení.

> [!NOTE]
> Pokud pro nespravované disky použijete [geograficky redundantní úložiště](../articles/storage/common/storage-redundancy-grs.md) nebo [geograficky redundantní úložiště pro přístup ke čtení,](../articles/storage/common/storage-redundancy.md) budete stále potřebovat konzistentní snímky pro zálohování a zotavení po havárii. Používejte azure [backup](https://azure.microsoft.com/services/backup/) nebo [konzistentní snímky](#alternative-solution-consistent-snapshots).

 V následující tabulce je uveden souhrn řešení, která jsou k dispozici pro zotavení po havárii.

| Scénář | Automatická replikace | Řešení zotavení po havárii |
| --- | --- | --- |
| Disky SSD úrovně Premium | Místní ([místně redundantní úložiště)](../articles/storage/common/storage-redundancy-lrs.md) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Spravované disky | Místní ([místně redundantní úložiště)](../articles/storage/common/storage-redundancy-lrs.md) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Nespravované disky místně redundantního úložiště | Místní ([místně redundantní úložiště)](../articles/storage/common/storage-redundancy-lrs.md) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Nespravované disky geograficky redundantního úložiště | Průřez[(geograficky redundantní úložiště)](../articles/storage/common/storage-redundancy-grs.md) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Konzistentní snímky](#alternative-solution-consistent-snapshots) |
| Disky geograficky redundantního úložiště pro nespravovaný přístup pro čtení | Mezi oblastmi ([geograficky redundantní úložiště pro čtení](../articles/storage/common/storage-redundancy.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Konzistentní snímky](#alternative-solution-consistent-snapshots) |

Vysokou dostupnost lze nejlépe splnit pomocí spravovaných disků v sadě dostupnosti spolu s Azure Backup. Pokud používáte nespravované disky, můžete stále používat Azure Backup pro zotavení po havárii. Pokud nemůžete používat Azure Backup, pak pořizování [konzistentní snímky](#alternative-solution-consistent-snapshots), jak je popsáno v pozdější části, je alternativní řešení pro zálohování a zotavení po havárii.

Vaše volby pro vysokou dostupnost, zálohování a zotavení po havárii na úrovni aplikací nebo infrastruktury mohou být reprezentovány následujícím způsobem:

| Úroveň |   Vysoká dostupnost   | Zálohování nebo zotavení po havárii |
| --- | --- | --- |
| Aplikace | SQL Server AlwaysOn | Azure Backup |
| Infrastruktura    | Skupina dostupnosti  | Geograficky redundantní úložiště s konzistentními snímky |

### <a name="using-azure-backup"></a>Použití služby Azure Backup 

[Azure Backup](../articles/backup/backup-azure-vms-introduction.md) můžou zálohovat virtuální počítače se systémem Windows nebo Linux do trezoru služeb pro obnovení Azure. Zálohování a obnovení důležitých podnikových dat je komplikováno skutečností, že důležitá obchodní data musí být zálohována, zatímco jsou spuštěny aplikace, které data vytvářejí. 

K vyřešení tohoto problému azure backup poskytuje zálohy konzistentní s aplikacemi pro úlohy Microsoftu. Používá službu stín svazku k zajištění, že data jsou zapsána správně do úložiště. Pro virtuální počítače SIF Linux je výchozím režimem konzistence zálohování záloh zálohy konzistentní se soubory, protože Linux nemá funkce ekvivalentní službě stínů svazku jako v případě Windows. Pro počítače s Linuxem najdete [v tématu zálohování virtuálních počítačů Azure Linux konzistentní s aplikací](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

![Tok zálohování Azure][1]

Když Azure Backup zahájí úlohu zálohování v naplánovaný čas, aktivuje rozšíření zálohy nainstalované ve virtuálním počítači, aby pořizovat snímek bodu v čase. Snímek je pořízena v koordinaci se službou stín svazku získat konzistentní snímek disků ve virtuálním počítači bez nutnosti vypnout. Rozšíření zálohy ve virtuálním počítače vyprázdnění všech zápisů před přijetím konzistentní snímek všech disků. Po pořízení snímku se data přenesou službou Azure Backup do úložiště záloh. Chcete-li zefektivnit proces zálohování, služba identifikuje a přenáší pouze bloky dat, které se změnily po poslední záloze.

Chcete-li obnovit, můžete zobrazit dostupné zálohy prostřednictvím služby Azure Backup a potom zahájit obnovení. Zálohy Azure můžete vytvářet a obnovovat prostřednictvím [portálu Azure](https://portal.azure.com/), [pomocí PowerShellu](../articles/backup/backup-azure-vms-automation.md)nebo pomocí [rozhraní příkazového řádku Azure](/cli/azure/).

### <a name="steps-to-enable-a-backup"></a>Postup povolení zálohování

Pomocí následujících kroků povolte zálohování virtuálních počítačů pomocí [portálu Azure](https://portal.azure.com/). Tam je nějaká variace v závislosti na přesném scénáři. Podrobné informace najdete v dokumentaci k [zálohování Azure.](../articles/backup/backup-azure-vms-introduction.md) Azure Backup také [podporuje virtuální počítače se spravovanými disky](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Vytvoření trezoru služeb obnovení pro virtuální hosti:

    a. Na [webu Azure Portal](https://portal.azure.com/)vyhledejte **všechny prostředky** a najděte **trezory služby Recovery Services**.

    b. V nabídce **Trezory služby Recovery Services** klikněte na **Přidat** a podle pokynů vytvořte nový trezor ve stejné oblasti jako virtuální počítač. Pokud je například virtuální počítač v oblasti USA – západ, vyberte pro trezor západní USA.

1.  Ověřte replikaci úložiště pro nově vytvořený trezor. Získejte přístup k úschovně v části **trezory služby Recovery Services** a přejděte na **položku Aktualizace** > **konfigurace** > zálohování**vlastností**. Ujistěte se, že je ve výchozím nastavení vybrána možnost **geograficky redundantního úložiště.** Tato možnost zajišťuje, že úložiště je automaticky replikováno do sekundárního datového centra. Například trezor v usa – západ se automaticky replikuje do usa – východ.

1.  Nakonfigurujte zásady zálohování a vyberte virtuální ho snítezet ze stejného ui.

1.  Ujistěte se, že agent zálohování je nainstalovaný na virtuálním počítači. Pokud se váš virtuální počítač vytvoří pomocí image galerie Azure, pak je agent zálohování už nainstalovaný. V opačném případě (to znamená, pokud používáte vlastní bitovou kopii) použijte pokyny k [instalaci agenta virtuálního počítače na virtuální počítač](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent).

1.  Po dokončení předchozích kroků zálohování běží v pravidelných intervalech, jak je uvedeno v zásadách zálohování. V případě potřeby můžete spustit první zálohu ručně z řídicího panelu trezoru na webu Azure Portal.

Automatizace zálohování Azure pomocí skriptů najdete v najdete v [rutinách Prostředí PowerShell pro zálohování virtuálních počítačích](../articles/backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Kroky pro obnovení

Pokud potřebujete opravit nebo znovu vytvořit virtuální hod, můžete obnovit virtuální ho z některého z bodů obnovení zálohy v trezoru. Existuje několik různých možností pro provedení obnovení:

-   Můžete vytvořit nový virtuální počítač jako reprezentaci virtuálního počítače v okamžiku.

-   Můžete obnovit disky a pak pomocí šablony pro virtuální počítače přizpůsobit a znovu obnovit obnovený virtuální ho.

Další informace najdete v pokynech k [obnovení virtuálních počítačů pomocí portálu Azure](../articles/backup/backup-azure-arm-restore-vms.md)Portal . Tento dokument také vysvětluje konkrétní kroky pro obnovení zálohovaných virtuálních počítačů do spárovaného datového centra pomocí geograficky redundantního úložiště záloh, pokud dojde k havárii v primárním datovém centru. V takovém případě Azure Backup používá výpočetní služby ze sekundární oblasti k vytvoření obnoveného virtuálního počítače.

PowerShell můžete použít také k [vytvoření nového virtuálního počítače z obnovených disků](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Alternativní řešení: Konzistentní snímky

Pokud nemůžete používat Azure Backup, můžete implementovat vlastní mechanismus zálohování pomocí snímků. Vytváření konzistentnísnímky pro všechny disky používané virtuálním počítačem a pak replikovat tyto snímky do jiné oblasti je složité. Z tohoto důvodu Azure považuje použití služby Backup jako lepší možnost než vytváření vlastního řešení.

Pokud pro disky používáte geograficky redundantní úložiště pro čtení a geograficky redundantní úložiště, snímky se automaticky replikují do sekundárního datového centra. Pokud používáte místně redundantní úložiště pro disky, je třeba replikovat data sami. Další informace najdete [v tématu Zálohování disků virtuálních počítačů Azure-nespravované s přírůstkové](../articles/virtual-machines/windows/incremental-snapshots.md).

Snímek je reprezentace objektu v určitém okamžiku v čase. Snímek vznikne fakturace pro přírůstkovou velikost dat, která drží. Další informace naleznete [v tématu Vytvoření snímku objektu blob](../articles/storage/blobs/storage-blob-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Vytváření snímků, když je spuštěný virtuální počítače

I když můžete pořídit snímek kdykoli, pokud je spuštěn virtuální modul, je stále data datového proudu na disky. Snímky mohou obsahovat částečné operace, které byly v letu. Také pokud existuje několik disků, snímky různých disků může dojít v různých časech. Tyto scénáře mohou způsobit snímky nekoordinované. Tento nedostatek koordinace je obzvláště problematický pro prokládané svazky, jejichž soubory mohou být poškozeny, pokud byly během zálohování provedeny změny.

Chcete-li se této situaci vyhnout, musí proces zálohování implementovat následující kroky:

1.  Zmrazit všechny disky.

1.  Vyprázdnění všech čekajících zápisů.

1.  [Vytvořte snímek objektu blob](../articles/storage/blobs/storage-blob-snapshots.md) pro všechny disky.

Některé aplikace systému Windows, například SQL Server, poskytují koordinovaný mechanismus zálohování prostřednictvím služby stínování svazků k vytváření záloh konzistentních s aplikací. Na Linuxu můžete použít nástroj, jako *je fsfreeze* pro koordinaci disků. Tento nástroj poskytuje zálohy konzistentní se soubory, ale ne snímky konzistentní s aplikací. Tento proces je složitý, takže byste měli zvážit použití [azure backup](../articles/backup/backup-azure-vms-introduction.md) nebo řešení zálohování jiného výrobce, které již implementuje tento postup.

Předchozí proces má za následek kolekci koordinovaných snímků pro všechny disky virtuálního počítače, což představuje konkrétní zobrazení virtuálního počítače v čase. Toto je bod obnovení zálohy pro virtuální počítače. Proces můžete opakovat v naplánovaných intervalech a vytvořit tak pravidelné zálohování. Viz [Kopírování záloh do jiné oblasti,](#copy-the-snapshots-to-another-region) kde najdete kroky ke zkopírování snímků do jiné oblasti pro zotavení po havárii.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Vytváření snímků v době, kdy je virtuální počítače offline

Další možností vytvoření konzistentnízálohy je vypnout virtuální ho virtuálního počítače a pořizovat snímky objektů blob každého disku. Pořizování snímků objektů blob je jednodušší než koordinace snímků spuštěného virtuálního počítače, ale vyžaduje několik minut prostojů.

1. Vypněte virtuální hod.

1. Vytvořte snímek každého objektu blob virtuálního pevného disku, který trvá pouze několik sekund.

    Chcete-li vytvořit snímek, můžete použít [PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Rozhraní API úložiště Azure](https://msdn.microsoft.com/library/azure/ee691971.aspx), rozhraní API [Azure](/cli/azure/)nebo jednu z klientských knihoven Azure Storage, jako je [například knihovna klienta úložiště pro .NET](https://msdn.microsoft.com/library/azure/hh488361.aspx).

1. Spusťte virtuální hod, který ukončí prostoje. Obvykle celý proces dokončí během několika minut.

Tento proces poskytuje kolekci konzistentní snímky pro všechny disky, poskytuje bod obnovení zálohy pro virtuální počítače.

### <a name="copy-the-snapshots-to-another-region"></a>Kopírování snímků do jiné oblasti

Vytvoření snímků sám nemusí být dostatečná pro zotavení po havárii. Musíte také replikovat zálohy snímků do jiné oblasti.

Pokud pro disky používáte geograficky redundantní úložiště nebo geograficky redundantní úložiště pro čtení, budou snímky automaticky replikovány do sekundární oblasti. Před replikací může být několik minut zpoždění. Pokud primární datové centrum přejde dolů před dokončením replikace snímků, nelze získat přístup k snímky ze sekundárního datového centra. Pravděpodobnost, že je to malé.

> [!NOTE]
> Jenom disky v geograficky redundantním úložišti nebo účtu geograficky redundantního úložiště s přístupem pro čtení nechrání virtuální počítače před katastrofami. Musíte také vytvořit koordinované snímky nebo použít Azure Backup. To je nutné obnovit virtuální ho do konzistentního stavu.

Pokud používáte místně redundantní úložiště, musíte zkopírovat snímky do jiného účtu úložiště ihned po vytvoření snímku. Cíl kopírování může být místně redundantní účet úložiště v jiné oblasti, výsledkem je kopie je ve vzdálené oblasti. Snímek můžete také zkopírovat do geograficky redundantního účtu úložiště pro čtení ve stejné oblasti. V tomto případě snímek je líně replikována do vzdálené sekundární oblasti. Záloha je chráněna před katastrofami v primární lokalitě po dokončení kopírování a replikace.

Pokud chcete přírůstkové snímky pro zotavení po havárii zkopírovat efektivně, přečtěte si pokyny v [části Zálohování nespravovaných disků virtuálních počítačů Azure s přírůstkovými snímky](../articles/virtual-machines/windows/incremental-snapshots.md).

![Zálohování nespravovaných disků virtuálních počítačů Azure pomocí přírůstkových snímků][2]

### <a name="recovery-from-snapshots"></a>Obnovení ze snímků

Chcete-li načíst snímek, zkopírujte jej a vytvořte nový objekt blob. Pokud kopírujete snímek z primárního účtu, můžete snímek zkopírovat do základního objektu blob snímku. Tento proces vrátí disk na snímek. Tento proces se označuje jako podpora snímku. Pokud kopírujete zálohu snímku ze sekundárního účtu, v případě účtu geograficky redundantního úložiště pro přístup ke čtení je nutné jej zkopírovat do primárního účtu. Snímek můžete zkopírovat [pomocí prostředí PowerShell](../articles/storage/common/storage-powershell-guide-full.md) nebo pomocí nástroje AzCopy. Další informace naleznete v [tématu Přenos dat pomocí nástroje příkazového řádku AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).

Pro virtuální počítače s více disky, musíte zkopírovat všechny snímky, které jsou součástí stejného bodu koordinovaného obnovení. Po zkopírování snímků do zapisovatelných objektů BLOB virtuálního pevného disku můžete pomocí objektů BLOB znovu vytvořit virtuální počítač pomocí šablony pro virtuální počítač.

## <a name="other-options"></a>Další možnosti

### <a name="sql-server"></a>SQL Server

SQL Server spuštěný ve virtuálním počítači má vlastní integrované funkce pro zálohování databáze SQL Serveru do úložiště objektů Blob Azure nebo sdílené složky. Pokud je účet úložiště geograficky redundantní úložiště nebo geograficky redundantní úložiště pro čtení, můžete k těmto zálohám přistupovat v sekundárním datovém centru účtu úložiště v případě havárie se stejnými omezeními, jako bylo dříve popsáno. Další informace najdete v [tématu Zálohování a obnovení pro SQL Server ve virtuálních počítačích Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). Kromě zálohování a obnovení [mohou skupiny dostupnosti SQL Server AlwaysOn](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) udržovat sekundární repliky databází. Tato schopnost výrazně snižuje dobu zotavení po havárii.

## <a name="other-considerations"></a>Další aspekty

Tento článek popisuje, jak zálohovat nebo pořizovat snímky virtuálních počítačů a jejich disků pro podporu zotavení po havárii a jak tyto zálohy nebo snímky použít k obnovení dat. S modelem Azure Resource Manager mnoho lidí používá šablony k vytvoření svých virtuálních počítačů a dalších infrastruktur v Azure. Šablonu můžete použít k vytvoření virtuálního počítače, který má stejnou konfiguraci pokaždé. Pokud používáte vlastní image pro vytváření virtuálních počítačů, musíte se také ujistit, že vaše image jsou chráněné pomocí geograficky redundantního účtu úložiště pro čtení k jejich uložení.

V důsledku toho může být proces zálohování kombinací dvou věcí:

- Záloha dat (disků).
- Záloha konfigurace (šablony a vlastní obrázky).

V závislosti na zvolené možnosti zálohování bude pravděpodobně pravděpodobně muset zpracovat zálohování dat i konfigurace, nebo služba zálohování může zpracovat vše za vás.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Dodatek: Pochopení dopadu redundance dat

Pro účty úložiště v Azure existují tři typy redundance dat, které byste měli zvážit týkající se zotavení po havárii: místně redundantní, geograficky redundantní nebo geograficky redundantní s přístupem pro čtení. 

Místně redundantní úložiště uchovává tři kopie dat ve stejném datovém centru. Když virtuální počítač zapíše data, všechny tři kopie jsou aktualizovány před úspěch je vrácena volajícímu, takže víte, že jsou identické. Disk je chráněn před místními chybami, protože je nepravděpodobné, že by byly ovlivněny všechny tři kopie současně. V případě místně redundantníúložiště neexistuje žádná geografická redundance, takže disk není chráněn před katastrofickými selháními, která mohou ovlivnit celé datové centrum nebo jednotku úložiště.

Díky geograficky redundantnímu úložišti a geograficky redundantnímu úložišti s přístupem pro čtení se tři kopie dat uchovávají v primární oblasti, kterou jste vybrali. Další tři kopie dat se uchovávají v odpovídající sekundární oblasti, kterou nastaví Azure. Pokud například ukládáte data v USA – západ, budou data replikována do usa – východ. Uchovávání kopií se provádí asynchronně a mezi aktualizacemi primární a sekundární lokality je malé zpoždění. Repliky disků v sekundární lokalitě jsou konzistentní na základě jednotlivých disků (se zpožděním), ale repliky více aktivních disků nemusí být synchronizovány mezi sebou. Chcete-li mít konzistentní repliky na více discích, jsou potřeba konzistentní snímky.

Hlavní rozdíl mezi geograficky redundantním úložištěm a geograficky redundantním úložištěm pro přístup ke čtení spoje spočitaným úložištěm je, že s geograficky redundantním úložištěm pro čtení můžete kdykoli číst sekundární kopii. Pokud se jedná o problém, který vykresluje data v primární oblasti nepřístupné, tým Azure vynakládá veškeré úsilí k obnovení přístupu. Zatímco primární je mimo provoz, pokud máte povoleno geograficky redundantní úložiště pro čtení, můžete získat přístup k datům v sekundárním datovém centru. Proto pokud máte v plánu číst z repliky, zatímco primární je nepřístupný, pak pro čtení přístup geograficky redundantní úložiště by měly být považovány za.

Pokud se ukáže, že se jedná o významný výpadek, tým Azure může aktivovat geografické převzetí služeb při selhání a změnit primární položky DNS tak, aby přecšlápnout na sekundární úložiště. V tomto okamžiku, pokud máte povoleno geograficky redundantní úložiště nebo geograficky redundantní úložiště pro čtení, můžete získat přístup k datům v oblasti, která bývala sekundární. Jinými slovy, pokud je váš účet úložiště geograficky redundantní úložiště a došlo k potížím, můžete získat přístup k sekundárnímu úložišti pouze v případě, že existuje geografické převzetí služeb při selhání.

Další informace najdete v tématu [Co dělat v případě výpadku služby Azure Storage](../articles/storage/common/storage-disaster-recovery-guidance.md).

>[!NOTE] 
>Společnost Microsoft určuje, zda dojde k převzetí služeb při selhání. Převzetí služeb při selhání není řízeno pro každý účet úložiště, takže o něm nerozhodují jednotliví zákazníci. Chcete-li implementovat zotavení po havárii pro konkrétní účty úložiště nebo disky virtuálních počítačů, musíte použít techniky popsané výše v tomto článku.

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
