---
title: Zálohování a zotavení po havárii pro disky IaaS na virtuálních počítačích Azure
description: Tento článek vysvětluje, jak naplánovat zálohování a zotavení po havárii virtuálních počítačů s IaaS a disků v Azure. Tento dokument pokrývá spravované i nespravované disky.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e5ae08c23748e55a8c3b75eb8fb9c112684f022e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102507902"
---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Zálohování a zotavení po havárii pro disky Azure IaaS

Tento článek vysvětluje, jak naplánovat zálohování a zotavení po havárii (DR) virtuálních počítačů s IaaS (VM) a disků v Azure. Tento dokument pokrývá spravované i nespravované disky.

Nejdřív zabereme integrované funkce odolnosti proti chybám na platformě Azure, které pomáhají chránit před místními chybami. Potom se podíváme na scénáře havárie, které nejsou plně pokryté integrovanými funkcemi. Zobrazujeme také několik příkladů scénářů úloh, které mohou být použity v různých ohledech na zálohování a zotavení po havárii. Pak si projdeme možná řešení pro zotavení po havárii IaaS disků.

## <a name="introduction"></a>Úvod

Platforma Azure využívá různé metody pro redundanci a odolnost proti chybám, které vám pomůžou ochránit zákazníky před lokalizovanými chybami hardwaru. K místním selháním můžou patřit problémy s Azure Storage serverového počítače, které ukládají část dat pro virtuální disk nebo chyby SSD nebo HDD na tomto serveru. K takovým izolovaným selháním hardwarových součástí může dojít během normálního provozu.

Platforma Azure je navržená tak, aby byla odolná vůči těmto chybám. K závažným haváriím může dojít v případě selhání nebo nedostupnosti mnoha serverů úložiště nebo dokonce celého datového centra. I když jsou virtuální počítače a disky obvykle chráněné před lokalizovanou chybou, jsou potřeba další kroky k ochraně vašich úloh před závažnými chybami v rámci různých oblastí, jako je například závažná havárie, která může ovlivnit virtuální počítač a disky.

Kromě možnosti selhání platformy mohou nastat problémy s zákaznickými aplikacemi nebo daty. Například nová verze vaší aplikace může nechtěně provést změnu dat, která způsobují přerušení. V takovém případě můžete chtít vrátit aplikaci a data do předchozí verze, která obsahuje poslední známý dobrý stav. To vyžaduje zachování pravidelného zálohování.

Pro místní zotavení po havárii musíte zálohovat disky virtuálních počítačů s IaaS do jiné oblasti.

Než se podíváme na možnosti zálohování a zotavení po havárii, pojďme Rekapitulace několik metod, které jsou k dispozici pro zpracování lokalizovaných selhání.

### <a name="azure-iaas-resiliency"></a>Odolnost Azure IaaS

*Odolnost* proti chybám označuje toleranci pro běžné chyby, ke kterým dochází v hardwarových součástech. Odolnost proti chybám je schopnost zotavení po selháních a nadále fungovat. Nejedná se o způsoby, jak zabránit selhání, ale o reakci na selhání způsobem, který zabraňuje výpadkům nebo ztrátě dat. Cílem odolnosti je obnovení plně funkčního stavu aplikace co nejdříve po selhání. Virtuální počítače a disky Azure jsou navržené tak, aby byly odolné vůči běžným hardwarovým chybám. Pojďme se podívat, jak tato odolnost zajišťuje platforma Azure IaaS.

Virtuální počítač se skládá hlavně ze dvou částí: výpočetní Server a trvalé disky. Obojí má vliv na odolnost virtuálního počítače.

Pokud se na serveru Azure COMPUTE Host, který je ve vašem VIRTUÁLNÍm počítači, narazí na selhání hardwaru, což je vzácné, Azure je navržený tak, aby virtuální počítač automaticky obnovil na jiném serveru. V takovém případě se počítač restartuje a virtuální počítač se po nějaké době vrátí. Azure automaticky detekuje takové chyby hardwaru a provádí obnovy, aby bylo možné zajistit, aby byl virtuální počítač zákazníka k dispozici co nejdříve.

V souvislosti s IaaS disky je odolnost dat důležitá pro trvalou platformu úložiště. Zákazníci Azure mají důležité obchodní aplikace běžící na IaaS a závisejí na persistenci dat. Azure Designs Protection pro tyto disky IaaS se třemi redundantními kopiemi dat, která jsou uložená místně. Tyto kopie poskytují vysokou odolnost proti místním selháním. Pokud některá z hardwarových komponent, které disk obsahuje, se nezdařila, váš virtuální počítač nebude ovlivněný, protože k podpoře požadavků na disk jsou k dispozici dvě další kopie. Funguje bez problémů i v případě, že dvě různé hardwarové součásti, které disk podporuje, se nedaří v současné době (což je zřídka). 

Aby se zajistilo, že budete vždycky udržovat tři repliky, Azure Storage automaticky vytvoří novou kopii dat na pozadí, pokud bude jedna ze tří kopií nedostupná. Proto by nemělo být nutné pro odolnost proti chybám použít RAID s disky Azure. Jednoduchá konfigurace RAID 0 by měla být dostačující pro prokládání disků, pokud je to potřeba, aby se vytvořily větší svazky.

Z důvodu této architektury dosáhla Azure jednotnou odolnost na IaaS discích na podnikové úrovni, která má za sebou špičkovou [roční četnost selhání](https://en.wikipedia.org/wiki/Annualized_failure_rate).

V případě lokalizovaných hardwarových chyb na výpočetním hostiteli nebo na platformě úložiště může někdy docházet k dočasné nedostupnosti virtuálního počítače, který je pokrytý smlouvou [SLA Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) pro dostupnost virtuálních počítačů. Azure také poskytuje špičkovou smlouvu SLA pro jednotlivé instance virtuálních počítačů, které používají Azure Premium SSD.

K ochraně aplikačních úloh před výpadky kvůli dočasné nedostupnosti disku nebo virtuálního počítače můžou zákazníci používat [skupiny dostupnosti](./availability.md). Dva nebo víc virtuálních počítačů v rámci skupiny dostupnosti zajišťují redundanci aplikace. Azure potom vytvoří tyto virtuální počítače a disky v samostatných doménách selhání s různými součástmi napájení, sítě a serveru.

Vzhledem k těmto samostatným doménám selhání neovlivní lokalizovaná hardwarová selhání obvykle více virtuálních počítačů v sadě ve stejnou dobu. Samostatné domény selhání poskytují vysokou dostupnost vaší aplikace. Je vhodné použít skupiny dostupnosti, pokud je potřeba vysoká dostupnost. V další části se dozvíte o aspektech zotavení po havárii.

### <a name="backup-and-disaster-recovery"></a>Zálohování a zotavení po havárii

Zotavení po havárii je možnost zotavení ze vzácných, ale velkých a největších incidentů. Tyto incidenty zahrnují nepřechodné a vysoce škálovatelné chyby, jako je například přerušení služby, které ovlivňuje celou oblast. Zotavení po havárii zahrnuje zálohování a archivaci dat a může zahrnovat ruční zásah, jako je například obnovení databáze ze zálohy.

Integrovaná ochrana platformy Azure proti lokalizovaným chybám nemusí plně chránit virtuální počítače a disky, pokud by závažná havárie způsobila velké výpadky. Tyto velké výpadky obsahují závažné události, jako je například, pokud je datové centrum dosaženo hurikán, zemětřesení, požárem nebo pokud dojde k selhání velké hardwarové jednotky. Kromě toho můžete narazit na chyby způsobené problémy s aplikací nebo daty.

Aby se chránily vaše úlohy IaaS před výpadky, měli byste naplánovat redundanci a mít zálohy k povolení obnovení. V případě zotavení po havárii byste měli zálohovat v jiném geografickém umístění mimo primární lokalitu. Tento přístup pomáhá zajistit, že záloha není ovlivněná stejnou událostí, která původně ovlivnila virtuální počítač nebo disky. Další informace najdete v tématu [zotavení po havárii pro aplikace Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).

Vaše aspekty zotavení po havárii mohou zahrnovat následující aspekty:

- Vysoká dostupnost: schopnost aplikace pokračovat v dobrém stavu, aniž by došlo k výraznému výpadku. V *dobrém stavu* tento stav znamená, že aplikace reaguje a uživatelé se mohou připojit k aplikaci a pracovat s ní. Některé klíčové aplikace a databáze mohou být nutné vždy k dispozici, i když dojde k selhání platformy. Pro tyto úlohy možná budete muset naplánovat redundanci aplikace i data.

- Odolnost dat: v některých případech hlavní aspekt zajišťuje, aby se data zachovala, pokud dojde k havárii. Proto může být nutné zálohovat data v jiné lokalitě. Pro takové úlohy možná nebudete pro aplikaci potřebovat úplnou redundanci, ale pouze běžné zálohy disků.

## <a name="backup-and-dr-scenarios"></a>Scénáře zálohování a zotavení po havárii

Pojďme se podívat na několik typických příkladů scénářů úloh aplikací a důležitých informací pro plánování zotavení po havárii.

### <a name="scenario-1-major-database-solutions"></a>Scénář 1: hlavní databázová řešení

Vezměte v úvahu provozní databázový server, jako je SQL Server nebo Oracle, který může podporovat vysokou dostupnost. Důležité provozní aplikace a uživatelé závisejí na této databázi. Plán zotavení po havárii pro tento systém může potřebovat podporu následujících požadavků:

- Data musí být chráněná a obnovitelná.
- Server musí být k dispozici pro použití.

Plán zotavení po havárii může vyžadovat údržbu repliky databáze v jiné oblasti jako zálohy. V závislosti na požadavcích na dostupnost serveru a obnovení dat může být řešení v rozsahu od lokality aktivní-aktivní nebo aktivní-pasivní repliky až po periodické zálohování dat v režimu offline. Relační databáze, například SQL Server a Oracle, poskytují různé možnosti pro replikaci. Pro SQL Server použijte pro zajištění vysoké dostupnosti [SQL Server skupiny dostupnosti AlwaysOn](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) .

Databáze NoSQL, jako je MongoDB, také podporují [repliky](https://docs.mongodb.com/manual/replication/) pro redundanci. Používají se repliky pro vysokou dostupnost.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Scénář 2: cluster redundantních virtuálních počítačů

Vezměte v úvahu zatížení, které zpracovává cluster virtuálních počítačů, které poskytují redundanci a vyrovnávání zatížení. Jedním z příkladů je cluster Cassandra nasazený v nějaké oblasti. Tento typ architektury již poskytuje vysokou úroveň redundance v rámci této oblasti. Chcete-li však ochránit zatížení při selhání místní úrovně, měli byste zvážit rozprostření clusteru ve dvou oblastech nebo provádění pravidelných záloh do jiné oblasti.

### <a name="scenario-3-iaas-application-workload"></a>Scénář 3: úlohy aplikace IaaS

Pojďme se podívat na úlohu aplikace IaaS. Tato aplikace může být například typickou produkční úlohou běžící na virtuálním počítači Azure. Může se jednat o webový server nebo souborový server, který uchovává obsah a další prostředky lokality. Může to být také vlastní vytvořená podniková aplikace spuštěná na virtuálním počítači, který ukládá jeho data, prostředky a stav aplikace na discích virtuálních počítačů. V takovém případě je důležité pravidelně provádět zálohování. Frekvence zálohování by měla být založená na povaze zatížení virtuálního počítače. Například pokud se aplikace každý den spustí a upraví data, zálohování by mělo být prováděno každou hodinu.

Dalším příkladem je server pro sestavy, který získává data z jiných zdrojů a generuje agregované sestavy. Ztráta těchto virtuálních počítačů nebo disků může vést ke ztrátě sestav. Může ale být možné znovu spustit proces vytváření sestav a znovu vygenerovat výstup. V takovém případě nebudete mít ve skutečnosti ztrátou dat, i když se server sestav narazí na havárie. Výsledkem je, že můžete mít vyšší úroveň tolerance pro ztrátu části dat na serveru sestav. V takovém případě méně časté zálohy představují možnost snížit náklady.

### <a name="scenario-4-iaas-application-data-issues"></a>Scénář 4: IaaS problémy s daty aplikace

Problémy s daty aplikace IaaS jsou další možností. Vezměte v úvahu aplikaci, která počítá, udržuje a obsluhuje důležitá komerční data, jako jsou informace o cenách. V nové verzi aplikace došlo k chybě softwaru, která nesprávně vypočítala ceny a poškodila stávající obchodní data obsluhovaná platformou. Tady je nejlepší akce, která se má vrátit na předchozí verzi aplikace a data. Pokud to chcete povolit, proveďte pravidelné zálohování systému.

## <a name="disaster-recovery-solution-azure-backup"></a>Řešení zotavení po havárii: Azure Backup 

[Azure Backup](https://azure.microsoft.com/services/backup/) se používá pro zálohování a zotavení po havárii a funguje se [spravovanými disky](managed-disks-overview.md) i s nespravovanými disky. Úlohu zálohování můžete vytvořit pomocí zálohování založeného na čase, snadného obnovení virtuálních počítačů a zásad uchovávání záloh.

Pokud používáte [SSD úrovně Premium](disks-types.md), [spravované disky](managed-disks-overview.md)nebo jiné typy disků s možností [místně redundantního úložiště](../storage/common/storage-redundancy.md#locally-redundant-storage) , je zvláště důležité provést pravidelná zálohování Dr. Azure Backup ukládá data do trezoru služby Recovery Services pro dlouhodobé uchovávání. Vyberte možnost [geograficky redundantního úložiště](../storage/common/storage-redundancy.md#geo-redundant-storage) pro trezor služby Recovery Services. Tato možnost zajistí, že zálohování se replikují do jiné oblasti Azure pro zajištění ochrany před místními katastrofami.

U nespravovaných disků můžete pro disky IaaS použít místně redundantní typ úložiště, ale zajistěte, aby byla pro trezor služby Recovery Services v možnosti geograficky redundantního úložiště zapnutá možnost Azure Backup.

> [!NOTE]
> Pokud pro nespravované disky použijete [geograficky redundantní úložiště](../storage/common/storage-redundancy.md#geo-redundant-storage) nebo [geograficky redundantní úložiště s přístupem pro čtení](../storage/common/storage-redundancy.md#read-access-to-data-in-the-secondary-region)  , budete pro zálohování a zotavení po havárii stále potřebovat konzistentní snímky. Použijte buď [Azure Backup](https://azure.microsoft.com/services/backup/) , nebo [konzistentní snímky](#alternative-solution-consistent-snapshots).

 Následující tabulka představuje souhrn řešení, která jsou k dispozici pro zotavení po havárii.

| Scenario | Automatická replikace | Řešení zotavení po havárii |
| --- | --- | --- |
| Disky SSD úrovně Premium | Místní ([místně redundantní úložiště](../storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Spravované disky | Místní ([místně redundantní úložiště](../storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Nespravované místně redundantní disky úložiště | Místní ([místně redundantní úložiště](../storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Nespravované geografické redundantní disky úložiště | Mezi oblastmi ([geograficky redundantní úložiště](../storage/common/storage-redundancy.md#geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Konzistentní snímky](#alternative-solution-consistent-snapshots) |
| Nespravované disky geograficky redundantního úložiště s přístupem pro čtení | Mezi oblastmi ([geograficky redundantní úložiště s přístupem pro čtení](../storage/common/storage-redundancy.md#read-access-to-data-in-the-secondary-region)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Konzistentní snímky](#alternative-solution-consistent-snapshots) |

Vysoká dostupnost se nejlépe splní pomocí spravovaných disků v sadě dostupnosti společně s Azure Backup. Pokud používáte nespravované disky, můžete i nadále používat Azure Backup pro zotavení po havárii. Pokud nemůžete použít Azure Backup a pak zabere [konzistentní snímky](#alternative-solution-consistent-snapshots), jak je popsáno v pozdější části, je alternativním řešením pro zálohování a zotavení po havárii.

Vaše volby pro vysokou dostupnost, zálohování a zotavení po havárii na úrovni aplikace nebo infrastruktury můžou být reprezentovány takto:

| Level |   Vysoká dostupnost   | Zálohování nebo zotavení po havárii |
| --- | --- | --- |
| Aplikace | SQL Server AlwaysOn | Azure Backup |
| Infrastruktura    | Skupina dostupnosti  | Geograficky redundantní úložiště s konzistentními snímky |

### <a name="using-azure-backup"></a>Použití služby Azure Backup 

[Azure Backup](../backup/backup-azure-vms-introduction.md) můžou zálohovat virtuální počítače se systémem Windows nebo Linux do trezoru služby Azure Recovery Services. Zálohování a obnova důležitých podnikových dat je složitá o skutečnost, že podniková data musí být zálohována, zatímco aplikace vytvářející data jsou spuštěny. 

Pro vyřešení tohoto problému Azure Backup poskytuje zálohy konzistentní s aplikacemi pro úlohy Microsoftu. Služba Stínová kopie svazku používá k zajištění správného zápisu dat do úložiště. U virtuálních počítačů se systémem Linux je výchozím režimem konzistence zálohování konzistentní zálohování, protože systém Linux nemá v případě systému Windows stejné funkce jako služba stínové kopie svazku. Pro počítače se systémem Linux si přečtěte téma [zálohování virtuálních počítačů Azure Linux konzistentní vzhledem k aplikacím](../backup/backup-azure-linux-app-consistent.md).

![Tok Azure Backup][1]

Když Azure Backup spustí úlohu zálohování v naplánovaném čase, aktivuje rozšíření zálohování nainstalované na virtuálním počítači, aby se pomohlo vytvořit snímek v čase. Snímek se zabere v koordinaci se službou Stínová kopie svazku, aby bylo možné zajistit konzistentní snímek disků ve virtuálním počítači, aniž by bylo nutné ho vypnout. Rozšíření zálohování na virtuálním počítači vyprázdní všechny zápisy, než zaberou konzistentní snímek všech disků. Po pořízení snímku se data přenesou Azure Backup do trezoru záloh. Aby bylo možné proces zálohování zefektivnit, služba identifikuje a přenáší pouze bloky dat, které se změnily po poslední záloze.

K obnovení můžete zobrazit dostupné zálohy prostřednictvím Azure Backup a pak zahájit obnovení. Zálohy Azure můžete vytvářet a obnovovat prostřednictvím [Azure Portal](https://portal.azure.com/), [pomocí PowerShellu](../backup/backup-azure-vms-automation.md)nebo pomocí [Azure CLI](/cli/azure/).

### <a name="steps-to-enable-a-backup"></a>Postup povolení zálohování

Pomocí následujícího postupu můžete povolit zálohování virtuálních počítačů pomocí [Azure Portal](https://portal.azure.com/). V závislosti na tom, jaký scénář máte, záleží na konkrétní situaci. Úplné podrobnosti najdete v dokumentaci k [Azure Backup](../backup/backup-azure-vms-introduction.md) . Azure Backup [podporuje také virtuální počítače se službou Managed disks](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Vytvoření trezoru služby Recovery Services pro virtuální počítač:

    a. V [Azure Portal](https://portal.azure.com/)přejděte na **všechny prostředky** a vyhledejte **Recovery Services trezory**.

    b. V nabídce **Recovery Services trezory** klikněte na **Přidat** a postupujte podle pokynů pro vytvoření nového trezoru ve stejné oblasti jako virtuální počítač. Pokud je například váš virtuální počítač v oblasti Západní USA, vyberte pro trezor Západní USA.

1.  Ověřte replikaci úložiště pro nově vytvořený trezor. Přístup k trezoru v části **Recovery Services trezory** a přejděte na **vlastnosti**  >  **záložní konfigurace**  >  **aktualizace**. Ujistěte se, že je ve výchozím nastavení vybraná možnost **geograficky redundantního úložiště** . Tato možnost zajistí, že se váš trezor automaticky replikuje do sekundárního datacentra. Například váš trezor v Západní USA je automaticky replikován do Východní USA.

1.  Nakonfigurujte zásady zálohování a vyberte virtuální počítač ze stejného uživatelského rozhraní.

1.  Ujistěte se, že je na virtuálním počítači nainstalovaný Agent zálohování. Pokud je váš virtuální počítač vytvořený pomocí Image Galerie Azure, je už nainstalovaný Agent zálohování. Jinak (tj. Pokud používáte vlastní image) použijte pokyny k [instalaci agenta virtuálního počítače na virtuální počítač](../backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent).

1.  Po dokončení předchozích kroků se zálohování spustí v pravidelných intervalech, jak je uvedeno v zásadách zálohování. V případě potřeby můžete první zálohu aktivovat ručně z řídicího panelu trezoru na Azure Portal.

Informace o automatizaci Azure Backup pomocí skriptů najdete v tématu [rutiny PowerShellu pro zálohování virtuálních počítačů](../backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Postup obnovení

Pokud potřebujete virtuální počítač opravit nebo znovu sestavit, můžete ho obnovit ze všech bodů obnovení zálohy v trezoru. K dispozici je několik různých možností, jak provést obnovení:

-   Nový virtuální počítač můžete vytvořit jako reprezentaci vašeho zálohovaného virtuálního počítače v určitém časovém okamžiku.

-   Disky můžete obnovit a pak pomocí šablony pro virtuální počítač upravit a znovu sestavit obnovený virtuální počítač.

Další informace najdete v pokynech k [obnovení virtuálních počítačů pomocí Azure Portal](../backup/backup-azure-arm-restore-vms.md). Tento dokument také vysvětluje konkrétní kroky pro obnovení zálohovaných virtuálních počítačů do spárovaného datového centra pomocí geograficky redundantního úložiště záloh, pokud dojde k havárii v primárním datacentru. V takovém případě Azure Backup používá službu COMPUTE ze sekundární oblasti k vytvoření obnoveného virtuálního počítače.

PowerShell můžete také použít k [Vytvoření nového virtuálního počítače z obnovených disků](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Alternativní řešení: konzistentní snímky

Pokud nemůžete použít Azure Backup, můžete implementovat vlastní mechanismus zálohování pomocí snímků. Vytváření konzistentních snímků pro všechny disky používané virtuálním počítačem a následné replikace těchto snímků do jiné oblasti je složité. Z tohoto důvodu Azure považuje používání služby zálohování za lepší možnost než vytváření vlastního řešení.

Pokud pro disky použijete geograficky redundantní úložiště s přístupem pro čtení/geograficky redundantní úložiště, snímky se automaticky replikují do sekundárního datacentra. Pokud používáte místně redundantní úložiště pro disky, musíte replikovat data sami. Další informace najdete v tématu [zálohování disků virtuálních počítačů nespravovaných Azure pomocí přírůstkových snímků](windows/incremental-snapshots.md).

Snímek je reprezentace objektu v určitém časovém okamžiku. U snímku dojde k fakturaci za přírůstkovou velikost dat, která uchovává. Další informace najdete v tématu [vytvoření snímku objektu BLOB](../storage/blobs/snapshots-overview.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Vytváření snímků, když je spuštěný virtuální počítač

I když můžete pořídit snímek kdykoli, pokud je virtuální počítač spuštěný, na disky se pořád streamuje data. Snímky mohou obsahovat částečné operace, které byly v letu. V případě, že je k dispozici několik disků, mohou být v různých časech k dispozici také snímky různých disků. Tyto scénáře mohou způsobit nekoordinaci snímků. Tato nedostatečná koordinace je obzvláště problematické u prokládaných svazků, jejichž soubory můžou být poškozené, pokud byly během zálohování provedeny změny.

Aby k této situaci nedocházelo, proces zálohování musí implementovat následující kroky:

1.  Zablokujte všechny disky.

1.  Vyprázdněte všechny zápisy, které čekají na vyřízení.

1.  [Vytvořte snímek objektu BLOB](../storage/blobs/snapshots-manage-dotnet.md) pro všechny disky.

Některé aplikace systému Windows, jako je například SQL Server, poskytují koordinovaný zálohovací mechanismus prostřednictvím služby stínového svazku pro vytváření záloh konzistentních vzhledem k aplikacím. V systému Linux můžete k koordinaci disků použít nástroj, jako je *fsfreeze* . Tento nástroj poskytuje zálohy konzistentní se soubory, ale ne snímky konzistentní vzhledem k aplikacím. Tento proces je složitý, proto byste měli zvážit použití [Azure Backup](../backup/backup-azure-vms-introduction.md) nebo řešení zálohování třetí strany, které už tento postup implementuje.

Výsledkem předchozího procesu je kolekce koordinovaných snímků pro všechny disky virtuálních počítačů, které představují konkrétní zobrazení daného virtuálního počítače v čase. Toto je bod obnovení zálohy virtuálního počítače. Tento postup můžete opakovat v naplánovaných intervalech a vytvořit tak periodické zálohy. Postup kopírování snímků do jiné oblasti pro nástroj DR naleznete v tématu [kopírování záloh do jiné oblasti](#copy-the-snapshots-to-another-region) .

### <a name="create-snapshots-while-the-vm-is-offline"></a>Vytváření snímků, když je virtuální počítač offline

Další možností, jak vytvořit konzistentní zálohy, je vypnout virtuální počítač a převzít snímky objektů blob na jednotlivých discích. Vytváření snímků objektů BLOB je snazší než koordinace snímků spuštěného virtuálního počítače, ale vyžaduje několik minut výpadků.

1. Vypněte virtuální počítač.

1. Vytvořte snímek pro každý objekt BLOB virtuálního pevného disku, který trvá jenom několik sekund.

    K vytvoření snímku můžete použít [PowerShell](/powershell/module/az.storage), [Azure Storage REST API](/rest/api/storageservices/Snapshot-Blob), [Azure CLI](/cli/azure/)nebo jednu z Azure Storage klientských knihoven, jako [je Klientská knihovna pro úložiště pro .NET](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

1. Spusťte virtuální počítač, který ukončí výpadek. Obvykle se celý proces dokončí během několika minut.

Tento proces vytvoří kolekci konzistentních snímků pro všechny disky a poskytne pro virtuální počítač bod obnovení zálohy.

### <a name="copy-the-snapshots-to-another-region"></a>Kopírovat snímky do jiné oblasti

Vytváření samotných snímků nemusí být pro zotavení po havárii dostatečné. Také je nutné replikovat zálohy snímků do jiné oblasti.

Pokud pro vaše disky použijete geograficky redundantní úložiště nebo geograficky redundantní úložiště s přístupem pro čtení, snímky se automaticky replikují do sekundární oblasti. Před replikací může trvat pár minut prodlevy. Pokud se primární datacentrum před dokončením replikace snímků ukončí, nebudete mít přístup ke snímkům ze sekundárního datacentra. Pravděpodobnost je malá.

> [!NOTE]
> Pouze disky v geograficky redundantním úložišti nebo účtu geograficky redundantního úložiště s přístupem pro čtení nechrání virtuální počítač před haváriemi. Musíte také vytvořit koordinované snímky nebo použít Azure Backup. To je nutné k obnovení virtuálního počítače do konzistentního stavu.

Pokud používáte místně redundantní úložiště, musíte snímky zkopírovat do jiného účtu úložiště hned po vytvoření snímku. Cíl kopírování může být místně redundantní účet úložiště v jiné oblasti, který má za následek kopírování ve vzdálené oblasti. Snímek můžete také zkopírovat do účtu geograficky redundantního úložiště s přístupem pro čtení ve stejné oblasti. V takovém případě je snímek laxně vytvářená replikován do vzdálené sekundární oblasti. Zálohování je po dokončení kopírování a replikace chráněno před katastrofami v primární lokalitě.

Pokud chcete kopírovat přírůstkové snímky pro program DR., přečtěte si pokyny v tématu [zálohování nespravovaných disků virtuálních počítačů Azure pomocí přírůstkových snímků](windows/incremental-snapshots.md).

![Zálohování nespravovaných disků virtuálních počítačů Azure pomocí přírůstkových snímků][2]

### <a name="recovery-from-snapshots"></a>Obnovení ze snímků

Chcete-li načíst snímek, zkopírujte jej a vytvořte nový objekt BLOB. Pokud kopírujete snímek z primárního účtu, můžete snímek zkopírovat do základního objektu BLOB snímku. Tento proces vrátí disk do snímku. Tento proces se označuje jako zvýšení úrovně snímku. Pokud kopírujete zálohu snímku ze sekundárního účtu, musíte v případě účtu geograficky redundantního úložiště s přístupem pro čtení zkopírovat ho do primárního účtu. Snímek můžete zkopírovat [pomocí PowerShellu](/powershell/module/az.storage) nebo pomocí nástroje AzCopy. Další informace najdete v tématu [přenos dat pomocí nástroje příkazového řádku AzCopy](../storage/common/storage-use-azcopy-v10.md).

U virtuálních počítačů s více disky je nutné zkopírovat všechny snímky, které jsou součástí stejného koordinovaného bodu obnovení. Po zkopírování snímků do zapisovatelných objektů BLOB VHD můžete pomocí objektů BLOB znovu vytvořit virtuální počítač pomocí šablony pro virtuální počítač.

## <a name="other-options"></a>Další možnosti

### <a name="sql-server"></a>SQL Server

SQL Server běžící na virtuálním počítači má své vlastní integrované funkce pro zálohování databáze SQL Server do úložiště objektů BLOB v Azure nebo do sdílené složky. Pokud je účet úložiště geograficky redundantní úložiště nebo geograficky redundantní úložiště s přístupem pro čtení, můžete k těmto zálohám přistupovat v sekundárním datovém centru účtu úložiště v případě havárie se stejnými omezeními, jak je popsáno výše. Další informace najdete v tématu [zálohování a obnovení pro SQL Server ve virtuálních počítačích Azure](../azure-sql/virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md). Kromě zálohování a obnovení můžou [SQL Server skupiny dostupnosti AlwaysOn](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md) uchovávat sekundární repliky databází. Tato možnost značně zkracuje dobu zotavení po havárii.

## <a name="other-considerations"></a>Další důležité informace

Tento článek popisuje, jak zálohovat nebo pořizovat snímky virtuálních počítačů a jejich disků pro podporu zotavení po havárii a jak tyto zálohy nebo snímky použít k obnovení dat. S modelem Azure Resource Manager používá mnoho lidí šablony k vytváření virtuálních počítačů a dalších infrastruktur v Azure. Pomocí šablony můžete vytvořit virtuální počítač, který má stejnou konfiguraci pokaždé, když. Pokud používáte vlastní image pro vytváření virtuálních počítačů, musíte se také ujistit, že jsou vaše image chráněné pomocí účtu geograficky redundantního úložiště s přístupem pro čtení, aby je bylo možné ukládat.

V důsledku toho může být proces zálohování kombinací dvou věcí:

- Zálohujte data (disky).
- Proveďte zálohu konfigurace (šablony a vlastní image).

V závislosti na vámi zvolené možnosti zálohování bude možná nutné, abyste protáhli zálohování dat i konfiguraci, nebo služba Backup Service by za vás mohla zacházet.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Příloha: porozumění dopadu redundance dat

Pro účty úložiště v Azure existují tři typy redundance dat, které byste měli zvážit v souvislosti s zotavením po havárii: místně redundantní, geograficky redundantní nebo geograficky redundantní s přístupem pro čtení. 

Místně redundantní úložiště uchovává tři kopie dat ve stejném datovém centru. Když virtuální počítač zapíše data, všechny tři kopie se aktualizují předtím, než se vrátí volajícímu, takže víte, že jsou identické. Disk je chráněný před místními chybami, protože je pravděpodobné, že všechny tři kopie jsou ovlivněny současně. V případě místně redundantního úložiště není k dispozici žádná geografická redundance, takže disk není chráněný před závažnými chybami, které mohou ovlivnit celé datové centrum nebo jednotku úložiště.

Pomocí geograficky redundantního úložiště a geograficky redundantního úložiště s přístupem pro čtení se tři kopie vašich dat uchovávají v primární oblasti, kterou si vyberete. Tři další kopie vašich dat se uchovávají v odpovídající sekundární oblasti, kterou nastaví Azure. Pokud například ukládáte data v Západní USA, replikují se data do Východní USA. Uchovávání informací o kopírování se provádí asynchronně a mezi aktualizacemi primárních a sekundárních lokalit dojde k malému zpoždění. Repliky disků v sekundární lokalitě jsou konzistentní na jednotlivých discích (s prodlevou), ale repliky několika aktivních disků nemusí být vzájemně synchronizované. Chcete-li mít konzistentní repliky na více discích, je nutné zajistit konzistentní snímky.

Hlavním rozdílem mezi geograficky redundantním úložištěm a geograficky redundantním úložištěm s přístupem pro čtení je to, že v geograficky redundantním úložišti s přístupem pro čtení si můžete sekundární kopii kdykoli přečíst. Pokud dojde k problému, který vykreslí data v primární oblasti jako nepřístupné, tým Azure provede každé úsilí obnovit přístup. I když je primární úložiště s přístupem pro čtení zapnuté geograficky redundantní úložiště, můžete získat přístup k datům v sekundárním datovém centru. Pokud tedy plánujete čtení z repliky, zatímco primární je nepřístupný, je vhodné zvážit geograficky redundantní úložiště s přístupem pro čtení.

Pokud dojde k výraznému výpadku, může tým Azure aktivovat geografické převzetí služeb při selhání a změnit primární záznamy DNS tak, aby odkazovaly na sekundární úložiště. V tomto okamžiku, pokud máte povolené geograficky redundantní úložiště nebo geograficky redundantní úložiště s přístupem pro čtení, můžete získat přístup k datům v oblasti, která se používá jako sekundární. Jinými slovy, pokud je váš účet úložiště geograficky redundantní úložiště a dojde k problému, můžete získat přístup k sekundárnímu úložišti jenom v případě, že dojde k geografickému převzetí služeb při selhání.

Další informace najdete v tématu [Co dělat v případě výpadku služby Azure Storage](../storage/common/storage-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si téma [zálohování nespravovaných disků virtuálních počítačů Azure pomocí přírůstkových snímků](linux/incremental-snapshots.md).

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png