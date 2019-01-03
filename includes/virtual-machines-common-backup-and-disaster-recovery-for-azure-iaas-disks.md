---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: luywang
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: luywang
ms.custom: include file
ms.openlocfilehash: 5c7c9938b6a0b3d2e6050940154a8dc3f114341e
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53638819"
---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Zálohování a zotavení po havárii pro disky Azure IaaS

Tento článek vysvětluje, jak naplánovat zálohování a zotavení po havárii (DR) disků v Azure a virtuální počítače IaaS (virtuální počítače). Tento dokument popisuje spravované i nespravované disky.

Nejprve probereme možnosti předdefinovanou odolnost proti chybám na platformě Azure, která pomáhá chránit před místním selháním. Pak si popíšeme scénáře po havárii není plně pokryta integrované funkce. Také vám ukážeme několik příkladů scénáře úloh, kde můžete použít jinou zálohu a aspekty zotavení po Havárii. My pak si projděte možná řešení pro zotavení po Havárii IaaS disky. 

## <a name="introduction"></a>Úvod

Platforma Azure používá různé metody pro redundanci a odolnost proti chybám k ochraně zákazníků před lokalizovanému selhání hardwaru. Místní selhání může obsahovat problémy s počítači s Azure Storage serveru, který ukládá část dat pro virtuální disk nebo selhání SSD nebo pevný disk na tomto serveru. Takové chyb na úrovni součástí izolovanými hardwarovými může dojít během normálního provozu. 

Platforma Azure je navržená chcete být odolní vůči selhání. Hlavní jiného problému ovlivňujícího může způsobit selhání nebo inaccessibility mnoho serverů úložiště nebo dokonce celé datové centrum. I když vaše virtuální počítače a disky jsou obvykle chráněny i před místním selháním, další kroky jsou nezbytné k ochraně vašich úloh z katastrofických selhání celé oblasti, jako je například větší havárie, může mít vliv na váš virtuální počítač a disky.

Kromě možnosti platformy selhání může dojít k problémům s zákaznické aplikace nebo data. Například nové verze aplikace neúmyslně provést změnu na data, která způsobí, že bude k rozdělení. V takovém případě můžete chtít vrátit zpět, aplikace a data na předchozí verzi, která obsahuje posledního známého funkčního stavu. Tento postup vyžaduje údržbu pravidelného zálohování.

Místní zotavení po havárii je třeba zálohovat disky virtuálních počítačů IaaS v jiné oblasti. 

Předtím, než se podíváme na zálohování a možnosti zotavení po Havárii, Řekněme rekapitulace několik metod k dispozici pro zpracování lokálních selhání.

### <a name="azure-iaas-resiliency"></a>Odolnost Azure IaaS

*Odolnost proti chybám* odkazuje na odchylku pro běžné chyby, ke kterým dochází v hardwarové komponenty. Odolnost proti chybám je schopnost zotavení z chyb a nadále fungovat. Nejedná se o předcházení chybám, ale odpověď na selhání způsobem, který zabraňuje výpadkům nebo ztrátě. Cílem odolnosti proti chybám je obnovení plně funkčního stavu aplikace co nejdříve po selhání. Azure virtual machines a disky jsou navržená jako odolné řešení pro běžné chyby hardwaru. Podívejme se na jak platformě Azure IaaS poskytuje tento odolnost proti chybám.

Virtuální počítač se skládá převážně ze dvou částí: serveru pro výpočetní prostředky a trvalé disky. Obě vliv na odolnost proti chybám virtuálního počítače.

Pokud dojde k selhání hardwaru, který je vzácné, hostitelský server výpočetní prostředky Azure, které jsou uloženy virtuální počítač Azure je navržená na automatickém obnovení virtuálního počítače na jiný server. Pokud tento scénář, vaše restartování počítače a virtuální počítač uvede do provozu po nějaké době. Azure automaticky rozpozná takové selhání hardwaru a spustí obnovení tak, aby pomáhá zajistit, že zákazník virtuálního počítače je k dispozici co nejdříve.

Ohledně disků IaaS odolnost dat je velmi důležité pro platformu trvalého úložiště. Zákazníci Azure máte důležitých obchodních aplikací běžících na IaaS a jsou závislé na trvalou dostupnost dat. Ochrana Azure návrhy těchto disků IaaS, s třemi redundantních kopií dat uložených v místním prostředí. Tyto kopie stanovit vysoké odolnosti proti chybám místní. Pokud jeden hardwarové součásti, které obsahuje váš disk selže, váš virtuální počítač nemá vliv, protože existují dva další kopie pro podporu požadavků na disku. Funguje správně, i v případě dvě různé hardwarové komponenty, které podporují disk selžou ve stejnou dobu (což je vzácné). 

Chcete-li zajistit, aby vždy udržovat tří replik, úložiště Azure automaticky vytvoří novou kopii dat na pozadí, pokud jeden ze tří zkopíruje přestane být k dispozici. Proto by neměl být potřeba pomocí Azure disky RAID pro odolnost proti chybám. Jednoduché RAID 0 konfigurace by mělo být dostatečné pro prokládání disků, v případě potřeby vytvořte větší svazky.

Z důvodu této architektuře Azure konzistentně dodal podnikové úrovně odolnosti pro IaaS disky se špičkovou nulovou procent [s hodinovou chybovost](https://en.wikipedia.org/wiki/Annualized_failure_rate).

Lokalizované hardwarových chyb ve výpočetní hostitele nebo na platformě úložiště může někdy výsledek v dočasné nedostupnosti virtuálního počítače, který se bude vztahovat [smlouva Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) pro dostupnost virtuálních počítačů. Azure také poskytuje špičkovou smlouvu SLA pro jednotlivé instance virtuálních počítačů, které používají Azure Premium SSD disky.

K ochraně úloh aplikací z výpadek kvůli dočasné nedostupnosti disk nebo virtuální počítač, můžete použít zákazníkům [dostupnosti](../articles/virtual-machines/windows/manage-availability.md). Dva nebo více virtuálních počítačů ve skupině dostupnosti zajistit redundanci pro aplikaci. Azure pak vytvoří tyto virtuální počítače a disky v samostatných doménách selhání pomocí různých komponent napájení, sítě a serveru. 

Z důvodu těchto samostatných doménách selhání lokalizovanému selhání hardwaru obvykle nemají vliv na několika virtuálních počítačů v sadě ve stejnou dobu. S samostatných doménách selhání poskytuje vysokou dostupnost pro vaši aplikaci. Se považuje za vhodné použít skupiny dostupnosti, když se vyžaduje vysoká dostupnost. Další část popisuje aspekty zotavení po havárii.

### <a name="backup-and-disaster-recovery"></a>Zálohování a zotavení po havárii

Zotavení po havárii je schopnost zotavení z incidentů vzácné, ale hlavní. Tyto incidenty patří selhání nepřechodných, v celosvětovém měřítku, jako je například přerušení služby, které ovlivňuje celou oblast. Zotavení po havárii zahrnuje zálohování a archivaci dat a může vyžadovat ruční zásah, například obnovení databáze ze zálohy.

Platforma Azure integrovanou ochranu před místním selháním nemusí chránit plně virtuálních počítačů/disků, případě větší havárie způsobí, že výpadků ve velkém měřítku. Tyto výpadků ve velkém měřítku obsahují katastrofickými událostmi, například pokud datové centrum dosažení hurikán, zemětřesení, fire nebo pokud dojde k selhání jednotky ve velkém měřítku hardwaru. Kromě toho může dojít k chybám způsobeným aplikace nebo data problémy.

Pomáhá chránit vaše úlohy IaaS z výpadky, by měl plánování za účelem zajištění redundance a mít k povolení obnovení zálohy. Pro zotavení po havárii měli byste se zálohovat v jiném geografickém umístění pryč z primární lokality. Tento přístup pomáhá, ujistěte se, že vaše záloha není ovlivněn stejné události, ke které původně vliv na virtuálním počítači nebo disky. Další informace najdete v tématu [zotavení po havárii pro aplikace Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).

Vaše důležité informace o zotavení po Havárii mohou zahrnovat následující aspekty:

- Vysoká dostupnost: Možnost aplikace bude moct být spuštěná v dobrém stavu, bez významnějších výpadků. Podle *stavu v pořádku*, tento stav znamená, že aplikace reaguje, a uživatelé můžou připojit k aplikaci a pracovat s ním. Některé důležité podnikové aplikace a databáze může být požádán o mít vždycky k dispozici, i v případě, že nedochází k chybám na platformě. Pro tyto úlohy může být nutné plánovat redundance pro aplikace, jakož i data.

- Odolnost dat: V některých případech je hlavní pozornost zajistit, že data se zachovají, pokud dojde k havárii. Proto může být nutné zálohu vašich dat v jiné lokalitě. U takových úloh se nemusí potřebovat plnou redundanci pro aplikace, ale pouze pravidelného zálohování disky.

## <a name="backup-and-dr-scenarios"></a>Scénářích zálohování a zotavení po Havárii

Podívejme se na několik typické příklady scénářů aplikace úlohy a důležité informace pro plánování zotavení po havárii.

### <a name="scenario-1-major-database-solutions"></a>Scénář 1: Řešení pro hlavní databázi

Vezměte v úvahu databázový server produkčního prostředí, jako je SQL Server nebo Oracle, který může podporovat vysokou dostupnost. Důležité provozní aplikace a uživatelé závisí na této databázi. Plán zotavení po havárii pro tento systém může být nutné podporovat následující požadavky:

- Data musí být chráněný a dala se obnovit.
- Server musí být k dispozici pro použití.

Plán zotavení po havárii může vyžadovat zachování repliky databáze v jiné oblasti jako záložní. V závislosti na požadavcích pro server dostupnost a obnovení dat řešení může být v rozsahu ze serveru repliky aktivní aktivní nebo aktivní pasivní do pravidelné zálohování offline dat. Relačními databázemi jako SQL Server a Oracle, poskytují různé možnosti pro replikaci. Pro SQL Server, použijte [skupin dostupnosti AlwaysOn systému SQL Server](https://msdn.microsoft.com/library/hh510230.aspx) pro zajištění vysoké dostupnosti.

Databáze NoSQL, například MongoDB, také podporují [repliky](https://docs.mongodb.com/manual/replication/) za účelem zajištění redundance. Slouží k zajištění vysoké dostupnosti replik.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Scénář 2: Cluster redundantní virtuálních počítačů

Vezměte v úvahu úloh zpracovává cluster virtuálních počítačů, které poskytují redundance a vyrovnávání zatížení. Jedním z příkladů je nasazený v oblasti clusteru Cassandra. Tento typ architektury již poskytuje vysoký stupeň redundance v dané oblasti. Chránit úlohy v případě selhání místní úrovni, byste však měli zvážit rozšíření clusteru ve dvou oblastech nebo provádění pravidelného zálohování do jiné oblasti.

### <a name="scenario-3-iaas-application-workload"></a>Scénář 3: Úlohy aplikace IaaS

Podívejme se na úlohy aplikace IaaS. Tato aplikace může být například typické produkční úlohy běžící na Virtuálním počítači Azure. Může být na webovém serveru nebo souborového serveru, která uchovává obsah a další prostředky webu. Může být také vlastními silami sestavených obchodních aplikací na virtuálním počítači, který se ukládají na discích virtuálních počítačů jeho data, prostředky a stav aplikace běží. V takovém případě je důležité, abyste měli jistotu, že provedete záloh v pravidelných intervalech. Frekvence zálohování by měl vycházet povaze zatížení virtuálních počítačů. Například aplikace se spustí každý den a změní data, pak zálohování použitou každou hodinu.

Dalším příkladem je server pro sestavy, která načte data z jiných zdrojů a vygeneruje agregované sestavy. Ztráta tohoto virtuálního počítače nebo disky může vést ke ztrátě zpráv. Nicméně je možné znovu spustit proces vytváření sestav a znovu generovat výstup. V takovém případě není nutné ve skutečnosti ke ztrátě dat, i v případě, že dosáhnete s havárii serveru pro generování sestav. V důsledku toho může mít vyšší úroveň tolerance ztráty spolu s daty na serveru pro sestavy. V takovém případě jsou méně častá zálohování můžete snížit náklady.

### <a name="scenario-4-iaas-application-data-issues"></a>Scénář 4: Problémy s dat aplikace IaaS

Problémy s dat aplikace IaaS jsou jinou možnost. Vezměte v úvahu aplikace, která vypočítá udržuje a poskytuje důležitá obchodní data, jako je například informace o cenách. Nová verze vaší aplikace měli softwaru chybu, která nesprávně vypočítán ceny a poškozený existující data commerce obsluhuje platformu. Tady nejlepším řešením je se vrátit k předchozí verzi aplikace a data. Uděláte to trvat pravidelné zálohy systému.

## <a name="disaster-recovery-solution-azure-backup"></a>Řešení zotavení po havárii: Azure Backup 

[Azure Backup](https://azure.microsoft.com/services/backup/) slouží k zálohování a zotavení po Havárii, a funguje s [spravované disky](../articles/virtual-machines/windows/managed-disks-overview.md) stejně jako [nespravované disky](../articles/virtual-machines/windows/about-disks-and-vhds.md#unmanaged-disks). Můžete vytvořit úlohu zálohování s časovou synchronizací zálohy, snadné obnovení virtuálního počítače a zásady uchovávání záloh. 

Pokud používáte [Premium SSD disky](../articles/virtual-machines/windows/premium-storage.md), [spravované disky](../articles/virtual-machines/windows/managed-disks-overview.md), nebo jiných typů disku se [místně redundantní úložiště](../articles/storage/common/storage-redundancy-lrs.md) možnost, je zvlášť důležité, aby zotavení po Havárii pravidelné zálohy. Azure Backup ukládá data do vašeho trezoru služby recovery services pro dlouhodobé uchovávání. Zvolte [geograficky redundantní úložiště](../articles/storage/common/storage-redundancy-grs.md) services – možnosti pro obnovení zálohy trezoru. Tato možnost zajistí, že zálohování se replikují do jiné oblasti Azure pomáhá chránit místní haváriích.

Pro [nespravované disky](../articles/virtual-machines/windows/about-disks-and-vhds.md#unmanaged-disks), můžete použít místně redundantní úložiště pro disky IaaS, ale ujistěte se, že Azure Backup je povolená s možností geograficky redundantní úložiště pro trezor služby recovery services.

> [!NOTE]
> Pokud používáte [geograficky redundantní úložiště](../articles/storage/common/storage-redundancy-grs.md) nebo [geograficky redundantní úložiště jen pro čtení](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) možnost pro nespravované disky, můžete stále potřebovat snímky konzistentní vzhledem k aplikacím pro zálohování a zotavení po Havárii. Použijte buď [Azure Backup](https://azure.microsoft.com/services/backup/) nebo [snímky konzistentní s](#alternative-solution-consistent-snapshots).

 V následující tabulce je uveden seznam řešení, které jsou k dispozici pro zotavení po Havárii.

| Scénář | Automatická replikace | Řešení zotavení po Havárii |
| --- | --- | --- |
| SSD disky úrovně Premium | Místní ([místně redundantní úložiště](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Managed Disks | Místní ([místně redundantní úložiště](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Nespravované disky místně redundantní úložiště | Místní ([místně redundantní úložiště](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Geograficky redundantní úložiště s nespravované disky | Mezi oblastmi ([geograficky redundantní úložiště](../articles/storage/common/storage-redundancy-grs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Snímky konzistentní vzhledem k aplikacím](#alternative-solution-consistent-snapshots) |
| Disky nespravovaného geograficky redundantní úložiště jen pro čtení | Mezi oblastmi ([geograficky redundantní úložiště jen pro čtení](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Snímky konzistentní vzhledem k aplikacím](#alternative-solution-consistent-snapshots) |

Vysoká dostupnost je nejlépe naplněny s použitím spravovaných disků ve skupině dostupnosti společně s Azure Backup. Pokud používáte nespravované disky, stále můžete Azure Backup pro zotavení po Havárii. Pokud nemůžete použít Azure Backup, následné provádění [snímky konzistentní s](#alternative-solution-consistent-snapshots), jak je popsáno v další části, je alternativní řešení pro zálohování a zotavení po Havárii.

Vaše volby pro vysokou dostupnost, zálohování a zotavení po Havárii na úrovni aplikace a infrastrukturu může být reprezentován následujícím způsobem:

| Úroveň |   Vysoká dostupnost   | Zálohování nebo zotavení po Havárii |
| --- | --- | --- |
| Aplikace | SQL Server AlwaysOn | Azure Backup |
| Infrastruktura    | Skupina dostupnosti  | Geograficky redundantní úložiště s snímky konzistentní vzhledem k aplikacím |

### <a name="using-azure-backup"></a>Pomocí služby Azure Backup 

[Azure Backup](../articles/backup/backup-azure-vms-introduction.md) může zálohovat virtuální počítače s Windows nebo Linuxem do Azure recovery services vault. Zálohování a obnovení důležitých podnikových dat je složité skutečnost, že důležitých podnikových dat musí být zálohovány při běhu aplikací generujících data. 

Chcete-li tento problém vyřešit, Azure Backup poskytuje zálohy konzistentní s aplikací pro úlohy Microsoftu. Používá službu Stínová svazku zajistit, že data jsou správně zapsána do úložiště. Pro virtuální počítače s Linuxem jsou pouze konzistentní zálohování je to možné, protože Linuxu nemá funkce odpovídající svazků služby Stínová.

![Azure Backup toku][1]

Když Azure Backup zahájí úlohu zálohování v naplánovaném čase, aktivuje rozšíření zálohování, které jsou nainstalované ve virtuálním počítači k vytvoření snímku bodu v čase. Ve spolupráci se službou Stínová svazku se pořídí snímek získat snímek konzistentní vzhledem k aplikacím z disků ve virtuálním počítači bez nutnosti ho vypnout. Rozšíření zálohování na virtuálním počítači vyprázdnění všech zápisů před pořízení konzistentního snímku všechny disky. Po pořízení snímek, data se přenesou pomocí Azure Backup do trezoru záloh. Aby proces zálohování mnohem efektivnější, služba identifikuje a přenese pouze bloky dat, které se změnily po poslední záloze.

Pokud chcete obnovit, můžete zobrazit dostupné zálohy pomocí Azure Backup a potom iniciovat obnovení. Můžete vytvořit a obnovení záloh Azure prostřednictvím [webu Azure portal](https://portal.azure.com/), [pomocí prostředí PowerShell](../articles/backup/backup-azure-vms-automation.md), nebo pomocí [rozhraní příkazového řádku Azure](/cli/azure/). 

### <a name="steps-to-enable-a-backup"></a>Postup povolení zálohování

Pomocí následujících kroků k povolení zálohování vašich virtuálních počítačů pomocí [webu Azure portal](https://portal.azure.com/). Existuje několik variant v závislosti na vašemu konkrétnímu scénáři. Odkazovat [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) úplné podrobnosti naleznete v dokumentaci. Azure Backup také [podporuje virtuální počítače se spravovanými disky](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Vytvoření trezoru služby recovery services pro virtuální počítač:

    a. V [webu Azure portal](https://portal.azure.com/), Procházet **všechny prostředky** a najít **trezory služby Recovery Services**.

    b. Na **trezory služby Recovery Services** nabídky, klikněte na tlačítko **přidat** a postupujte podle kroků pro vytvoření nového trezoru ve stejné oblasti jako virtuální počítač. Například pokud váš virtuální počítač je v oblasti západní USA, vyberte pro trezor USA – západ.

1.  Ověření úložiště replikace pro nově vytvořeného trezoru. Přístup k trezoru v části **trezory služby Recovery Services** a přejděte na **nastavení** > **konfigurace zálohování**. Zkontrolujte, **geograficky redundantní úložiště** ve výchozím nastavení je vybraná možnost. Tato možnost zajišťuje, že váš trezor budou automaticky replikovat do sekundárního datacentra. Například trezoru v oblasti západní USA budou automaticky replikovaná do USA – východ.

1.  Nakonfigurujte zásady zálohování a vyberte virtuální počítač ze stejné uživatelské rozhraní.

1.  Ujistěte se, že Backup Agent je nainstalovaný na virtuálním počítači. Pokud váš virtuální počítač pomocí image Galerie Azure Backup Agent již nainstalován. Jinak (tj. Pokud používáte vlastní image), postupujte podle pokynů k [nainstalovat agenta virtuálního počítače na virtuální počítač](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent).

1.  Ujistěte se, že virtuální počítač umožňuje připojení k síti pro službu backup na funkci. Postupujte podle pokynů pro [připojení k síti](../articles/backup/backup-azure-arm-vms-prepare.md#establish-network-connectivity).

1.  Po dokončení předchozích kroků, zálohování se spustí v pravidelných intervalech, jak je uvedeno v zásady zálohování. V případě potřeby můžete aktivovat první zálohování ručně z řídicího panelu trezoru na portálu Azure portal.

Automatizace Azure Backup pomocí skriptů, najdete v tématu [rutiny Powershellu pro zálohování virtuálních počítačů](../articles/backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Kroky pro obnovení

Pokud potřebujete opravit nebo znovu vytvořit virtuální počítač, můžete obnovit virtuální počítač z některého z body obnovení záloh v trezoru. Existuje několik různých možností při provedení obnovení:

-   Můžete vytvořit nový virtuální počítač jako reprezentace v okamžiku zálohování virtuálního počítače.

-   Můžete obnovit disky a pak pomocí šablony virtuálního počítače můžete upravit a znovu vytvořit obnovený virtuální počítač. 

Další informace najdete v pokynech k [obnovení virtuálních počítačů pomocí webu Azure portal](../articles/backup/backup-azure-arm-restore-vms.md). Tento dokument popisuje také konkrétní pokyny k obnovení zálohovaných virtuálních počítačů na spárovaném datovém centru pomocí geograficky redundantního úložiště záloh, pokud nedojde k havárii v primárním datacentru. V takovém případě Azure Backup používá výpočetní služby ze sekundární oblasti vytvořit obnovený virtuální počítač.

Můžete použít také prostředí PowerShell pro [obnovení virtuálního počítače](../articles/backup/backup-azure-arm-restore-vms.md#restore-a-vm-during-an-azure-datacenter-disaster) nebo [vytváření nového virtuálního počítače z obnovit disky](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Alternativní řešení: Snímky konzistentní vzhledem k aplikacím

Pokud nemůžete použít Azure Backup, můžete implementovat vlastní mechanismus zálohování pomocí snímků. Vytváření snímků konzistentní vzhledem k aplikacím pro všechny disky používané systémem virtuálního počítače a pak replikuje tyto snímky do jiné oblasti je složitá. Z tohoto důvodu se považuje za Azure pomocí služby Backup jako vhodnější než vytvářet vlastní řešení. 

Pokud používáte geograficky redundantní úložiště nebo geograficky redundantní úložiště jen pro čtení pro disky, snímky automaticky replikovat do sekundárního datacentra. Pokud používáte místně redundantní úložiště pro disky, je potřeba replikovat data sami. Další informace najdete v tématu [zálohování virtuálních počítačů Azure unmanaged disků s přírůstkovými snímky](../articles/virtual-machines/windows/incremental-snapshots.md).

Snímek je reprezentace objektu v konkrétním bodě v čase. Snímek s sebou nese náklady fakturace pro přírůstkové velikost dat je obsahuje. Další informace najdete v tématu [vytvořit snímek objektu blob](../articles/storage/blobs/storage-blob-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Vytvořit snímky, zatímco běží tento virtuální počítač

I když se v každém okamžiku může pořízení snímku, pokud virtuální počítač běží, je stále data streamování na disky. Snímky mohou obsahovat částečné operací, které byly za pochodu. Navíc pokud se využívá řada několik disky, snímky různých discích pravděpodobně došlo k v různých časech. Tyto scénáře může způsobit snímkům bude nekoordinovaná. Tato nedostatečná koordinace je zvláště problematický pro prokládané svazky, jejichž soubory může být poškozený, pokud se provedly změny během zálohování.

Abyste předešli této situaci, musí implementovat proces zálohování následující kroky:

1.  Ukotvit všechny disky.

1.  Vyprázdněte všechny čekající operace zápisu.

1.  [Vytvoření snímku objektu blob](../articles/storage/blobs/storage-blob-snapshots.md) pro všechny disky.

Některé aplikace pro Windows, jako je SQL Server, poskytují mechanismus koordinované zálohování pomocí služby Stínová svazku k vytvoření zálohy konzistentní s aplikací. V systému Linux, můžete použít nástroje, jako je *fsfreeze* pro koordinaci disky. Tento nástroj poskytuje zálohy konzistentní vzhledem k souboru, ale není konzistentní s aplikací snímky. Tento proces je složitá, takže byste měli zvážit použití [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) nebo řešení zálohování jiného výrobce, implementující tento postup.

Předchozí výsledky zpracování v kolekci koordinované snímků pro všechny disky virtuálního počítače, představující konkrétního bodu v čase zobrazení virtuálního počítače. Toto je bod obnovení zálohy virtuálního počítače. Opakujte proces v naplánovaných intervalech vytvářet pravidelné zálohy. Zobrazit [zkopírujte zálohy do jiné oblasti](#copy-the-snapshots-to-another-region) kroky, jak zkopírovat snímky do jiné oblasti pro zotavení po Havárii.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Vytvořit snímky, zatímco virtuální počítač je v režimu offline

Další možností vytvářet zálohy konzistentní vzhledem k aplikacím je vypnout virtuální počítač a dělat jeho snímky objektů blob v jednotlivých disků. Pořizování snímků objektů blob je snazší než koordinace snímek spuštěného virtuálního počítače, ale vyžaduje několik minut prostojů.

1. Vypněte virtuální počítač.

1. Vytvoření snímku objektu blob každý virtuální pevný disk, který zabere jenom pár sekund.

    K vytvoření snímku, můžete použít [Powershellu](../articles/storage/common/storage-powershell-guide-full.md), [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/ee691971.aspx), [rozhraní příkazového řádku Azure](/cli/azure/), nebo jednu z knihoven klienta služby Azure Storage, jako například [ Klientská knihovna pro úložiště pro .NET](https://msdn.microsoft.com/library/azure/hh488361.aspx).

1. Spuštění virtuálního počítače, které končí výpadek. Obvykle celý proces dokončí během několika minut.

Tento proces vrací kolekci snímky konzistentní pro všechny disky, poskytuje bod obnovení zálohy virtuálního počítače.

### <a name="copy-the-snapshots-to-another-region"></a>Zkopírovat snímky do jiné oblasti

Vytváření snímků samostatně nemusí být dostatečné pro zotavení po Havárii. Zálohy snímků se taky musí replikovat do jiné oblasti.

Pokud používáte geografické redundantní úložiště nebo geograficky redundantní úložiště jen pro čtení pro disky, pak snímky se replikují do sekundární oblasti automaticky. Může existovat několik minut, než prodlevy před replikace. Pokud primární datové centrum ocitne mimo provoz, před snímky dokončit, které se replikují, nelze přistupovat snímky ze sekundárního datacentra. Pravděpodobnost, že to je malý.

> [!NOTE] 
> Jenom s disky v geograficky redundantní úložiště nebo geograficky redundantní čtení nechrání účtu úložiště virtuálního počítače z jiného problému ovlivňujícího. Musíte také vytvořit koordinovat snímků nebo pomocí služby Azure Backup. Je to nutné pro obnovení virtuálního počítače do konzistentního stavu.

Pokud používáte místně redundantní úložiště, musíte zkopírovat snímky do jiného účtu úložiště ihned po vytvoření snímku. Cíl kopírování může být účet místně redundantního úložiště v jiné oblasti, výsledkem je kopie ve vzdálené oblasti. Můžete také zkopírování snímku do účtu geograficky redundantní úložiště jen pro čtení ve stejné oblasti. V takovém případě snímku laxně replikují do sekundární oblasti vzdálené. Vaše záloha je chráněný před havárií v primární lokalitě po kopírování a replikace je kompletní.

Efektivní kopírování přírůstkových snímků pro zotavení po Havárii, zkontrolujte podle pokynů v [zálohování Azure nespravované disky virtuálních počítačů s přírůstkovými snímky](../articles/virtual-machines/windows/incremental-snapshots.md).

![Zálohování Azure nespravovaných disků virtuálních počítačů s přírůstkovými snímky][2]

### <a name="recovery-from-snapshots"></a>Obnovení ze snímků

Načíst snímek, zkopírujte ho do nového objektu blob. Pokud snímek kopírujete text z primární účet, můžete zkopírovat snímek přes na základní objekt blob snímku. Tento proces obnoví disku do snímku. Tento proces se označuje jako zvyšuje se úroveň snímku. Pokud kopírujete zálohy snímku ze sekundární účet, v případě účtů geograficky redundantního úložiště jen pro čtení, musí ho zkopírujte do primárního účtu. Můžete zkopírovat snímek podle [pomocí prostředí PowerShell](../articles/storage/common/storage-powershell-guide-full.md) nebo pomocí nástroje azcopy. Další informace najdete v tématu [přenos dat pomocí nástroje příkazového řádku azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).

Pro virtuální počítače s více disky musíte zkopírovat všechny snímky, které jsou součástí stejného bodu koordinované obnovení. Po zkopírování snímky pro zápis objektů BLOB VHD, můžete použít objekty BLOB pro opětovné vytvoření virtuálního počítače pomocí šablony virtuálního počítače.

## <a name="other-options"></a>Další možnosti

### <a name="sql-server"></a>SQL Server

SQL Server běžící na virtuálním počítači má vlastní integrované funkce pro zálohování databáze SQL serveru do úložiště objektů Blob v Azure nebo do souboru složek. Pokud účet úložiště je geograficky redundantní úložiště nebo geograficky redundantní úložiště jen pro čtení, můžete v případě havárie, přistupovat pomocí stejná omezení, jak jsme uvedli tyto zálohy v účtu úložiště sekundárního datacentra. Další informace najdete v tématu [zálohování a obnovení pro SQL Server na virtuálních počítačích Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). Kromě zálohování a obnovení [skupin dostupnosti AlwaysOn serveru SQL](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) díky čemuž můžete udržovat sekundární repliky databáze. Tato funkce výrazně snižuje čas obnovení po havárii.

## <a name="other-considerations"></a>Další důležité informace

Jak zálohovat nebo pořizovat snímky virtuálních počítačů a jejich disků k podpoře zotavení po havárii a jak používat tyto zálohy nebo snímky k obnovení dat má popsané v tomto článku. S modelem Azure Resource Manageru mnoho lidí používá šablony k vytvoření své virtuální počítače a jiné infrastruktury v Azure. Šablonu můžete použít k vytvoření virtuálního počítače, který má stejnou konfiguraci pokaždé, když. Pokud používáte vlastní Image pro vytváření virtuálních počítačů, musíte také zajistíte, že váš obrázky jsou chráněné pomocí účtů geograficky redundantního úložiště jen pro čtení k jejich uložení.

V důsledku toho procesu zálohování může být kombinací dvě věci:

- Zálohování dat (disky).
- Zálohování konfigurace (šablony a vlastních imagí).

V závislosti na záložní možnost, kterou zvolíte bude pravděpodobně nutné zpracování zálohování dat a konfiguraci nebo službě zálohování může se postaráme o, pro vás.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Dodatek: Principy vlivu redundance dat

Pro účty úložiště v Azure existují tři typy pro redundanci dat, měli byste zvážit týkající se zotavení po havárii: místně redundantní, geograficky redundantní a geograficky redundantní s přístupem pro čtení. 

Místně redundantní úložiště uchovává tři kopie dat ve stejném datacentru. Když virtuální počítač zapíše data, se aktualizují všechny tři kopie před úspěchu je vrátit zpět volajícímu, abyste věděli, že jsou identické. Disk je chráněn místních selháních, protože nepravděpodobné, že jsou všechny tři kopie vliv ve stejnou dobu. V případě místně redundantní úložiště neexistuje žádný geografickou redundanci, tak na disku není chráněn z katastrofických selhání, které můžou ovlivnit celé jednotky datového centra nebo úložiště.

Geograficky redundantní úložiště a geograficky redundantní úložiště jen pro čtení zůstanou zachovány tři kopie vašich dat v primární oblasti, který je vybrán sami. Další tři kopie vašich dat uchovávaných v odpovídající sekundární oblasti, která je nastavena v Azure. Například pokud ukládáte data v oblasti západní USA, data se replikují do USA – východ. Uchování kopírování se provádí asynchronně, a je malý zpoždění mezi aktualizacemi na primárních a sekundárních lokalit. Repliky disků v sekundární lokalitě jsou konzistentní vzhledem k aplikacím na jednotlivých disků (se zpožděním), ale nemusí být vzájemná synchronizace repliky aktivní několik disků. Pokud chcete, aby repliky konzistentní vzhledem k aplikacím na několik disků, je potřeba snímky konzistentní vzhledem k aplikacím.

Hlavní rozdíl mezi geograficky redundantní úložiště a geograficky redundantní úložiště jen pro čtení je v případě geograficky redundantního úložiště jen pro čtení, můžou načítat sekundární kopii kdykoli. Pokud je nějaký problém, který vykreslí dat v primární oblast nedostupná, tým Azure snaží obnovit přístup. Zatímco primární je mimo provoz, pokud máte povolené geograficky redundantní úložiště jen pro čtení, můžete přístup k datům v sekundárním datacentru. Proto pokud budete chtít číst z repliky, zatímco primární není dostupný, pak geograficky redundantní úložiště jen pro čtení má brát.

Pokud ukázalo být významné výpadky, tým Azure může aktivovat geografické převzetí a změňte primární záznamy DNS tak, aby odkazoval do sekundárního úložiště. Pokud máte geograficky redundantní úložiště nebo geograficky redundantní úložiště jen pro čtení povolené, můžete v tuto chvíli přístup k datům v oblasti, která se používá jako sekundární. Jinými slovy Pokud váš účet úložiště je geograficky redundantní úložiště a dojde k nějakému problému, můžete přistupovat sekundárního úložiště pouze v případě, že existuje geo-převzetí služeb při selhání.

Další informace najdete v tématu [co dělat, když dojde k výpadku služby Azure Storage](../articles/storage/common/storage-disaster-recovery-guidance.md). 

>[!NOTE] 
>Microsoft se řídí, zda dojde k selhání. Převzetí služeb při selhání není řízen jeden účet úložiště, takže není rozhodnuto pomocí jednotlivých zákazníků. Implementace zotavení po havárii pro účty konkrétní úložiště nebo disky virtuálních počítačů, je nutné použít techniky popsané dříve v tomto článku.



[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
