---
title: Běžné otázky týkající se migrace Azure Migrate serveru
description: Získejte odpovědi na běžné otázky týkající se použití migrace serveru Azure Migrate k migraci počítačů.
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: b62110e6b2ce97cdd80ed91ee4b1e75d119c7c7d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180689"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migrace Azure Migrate serveru: běžné otázky

Tento článek obsahuje odpovědi na běžné dotazy k nástroji Azure Migrate: Server Migration Tool. Pokud máte další otázky, ověřte tyto prostředky:

- [Obecné otázky](resources-faq.md) týkající se Azure Migrate
- Dotazy týkající se [zařízení Azure Migrate](common-questions-appliance.md)
- Dotazy týkající [se vizualizace zjišťování, hodnocení a závislostí](common-questions-discovery-assessment.md)
- Získání otázek zodpovězených ve [fóru Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="does-azure-migrate-convert-uefi-based-machines-to-bios-based-machines-and-migrate-them-to-azure-as-azure-generation-1-vms"></a>Převádí Azure Migrate počítače založené na UEFI na počítače se systémem BIOS a migrovat je do Azure jako virtuální počítače Azure generace 1?
Azure Migrate: Nástroj pro migraci serveru migruje všechny počítače založené na rozhraní UEFI do Azure jako virtuální počítače Azure generace 2. Už nepodporujeme převod virtuálních počítačů založených na rozhraní UEFI na virtuální počítače se systémem BIOS. Všimněte si, že všechny počítače se systémem BIOS se migrují do Azure jenom jako virtuální počítače Azure generace 1.

## <a name="how-can-i-migrate-uefi-based-machines-to-azure-as-azure-generation-1-vms"></a>Jak můžu migrovat počítače založené na rozhraní UEFI do Azure jako virtuální počítače Azure generace 1?
Azure Migrate: Nástroj pro migraci serveru migruje počítače založené na UEFI do Azure jako virtuální počítače Azure generace 2. Pokud je chcete migrovat na virtuální počítače Azure Generation 1, před spuštěním replikace převeďte typ spouštění na systém BIOS a pak pomocí nástroje Azure Migrate: Server pro migraci proveďte migraci do Azure.
 
## <a name="which-operating-systems-are-supported-for-migration-of-uefi-based-machines-to-azure"></a>Které operační systémy se podporují pro migraci počítačů založených na rozhraní UEFI do Azure?

| **Operační systémy podporované pro počítače založené na rozhraní UEFI** | **VMware do Azure bez agentů**                                                                                                             | **Hyper-V bez agentů do Azure** | **VMware, fyzické a další cloudy založené na agentech do Azure** |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ | ---------------------------------------------------------- |
| Windows Server 2019, 2016, 2012 R2, 201                 | Y                                                                                                                                         | Y                              | Y                                                          |
| Windows 10 pro, Windows 10 Enterprise                   | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 15 SP1                     | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 12 SP4                     | Y                                                                                                                                         | Y                              | Y                                                          |
| Ubuntu Server 16,04, 18,04, 19,04, 19,10                | Y                                                                                                                                         | Y                              | Y                                                          |
| RHEL 8,1, 8,0, 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x        | Y<br>                 _RHEL 8. x vyžaduje [ruční přípravu](./prepare-for-migration.md#linux-machines) ._   | Y                              | Y                                                          |
| Cent OS 8,1, 8,0, 7,7, 7,6, 7,5, 7,4, 6. x               | Y<br>_Cent OS 8. x vyžaduje [ruční přípravu](./prepare-for-migration.md#linux-machines) ._ | Y                              | Y                                                          |
| Oracle Linux 7,7, 7,7-CI                                |  Y                                                                                                                                        | Y                              | Y                                                          |

## <a name="can-i-use-the-recovery-services-vault-created-by-azure-migrate-for-disaster-recovery-scenarios"></a>Můžu použít trezor služby Recovery Services vytvořený Azure Migrate pro scénáře zotavení po havárii?
Pro scénáře zotavení po havárii nedoporučujeme používat trezor služby Recovery Services vytvořený Azure Migrate. V takovém případě může dojít k selhání spuštění replikace v Azure Migrate. 

## <a name="where-should-i-install-the-replication-appliance-for-agent-based-migrations"></a>Kde mám nainstalovat zařízení replikace pro migrace založené na agentech?

Zařízení replikace by se mělo nainstalovat na vyhrazený počítač. Zařízení replikace by se nemělo nainstalovat na zdrojový počítač, který chcete replikovat, nebo na zařízení pro zjišťování a hodnocení Azure Migrate, které jste mohli nainstalovat dřív. Další podrobnosti najdete v tomto [kurzu](./tutorial-migrate-physical-virtual-machines.md) .

## <a name="how-can-i-migrate-my-aws-ec2-instances-to-azure"></a>Jak můžu migrovat své instance AWS EC2 do Azure?

V tomto [článku](./tutorial-migrate-aws-virtual-machines.md) najdete postup zjišťování, vyhodnocení a migrace instancí AWS EC2 do Azure.

## <a name="can-i-migrate-aws-vms-running-amazon-linux-operating-system"></a>Můžu migrovat virtuální počítače AWS s operačním systémem Amazon Linux?

Virtuální počítače se spuštěným Amazon Linuxem se nedají migrovat tak, jak jsou, protože se v systému Amazon Linux OS podporuje jenom AWS.
Pokud chcete migrovat úlohy běžící v Amazon Linux, můžete aktivovat virtuální počítač CentOS/RHEL v Azure a migrovat zatížení běžící na počítači s AWS Linux pomocí relevantního přístupu k migraci úloh. Například v závislosti na zatížení můžou být pro podporu migrace k dispozici nástroje specifické pro úlohu, například pro databáze nebo nástroje pro nasazení v případě webových serverů.

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Jaké geografické oblasti jsou podporovány pro migraci pomocí Azure Migrate?

Projděte si podporované oblasti pro [veřejný cloud](migrate-support-matrix.md#supported-geographies-public-cloud) a [cloud pro státní správu](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-regions"></a>Můžeme použít stejný projekt Azure Migrate k migraci do několika oblastí?

I když můžete vytvořit posouzení pro více oblastí v projektu Azure Migrate, můžete použít jeden Azure Migrate projekt k migraci serverů pouze do jedné oblasti Azure. Pro každou oblast, na kterou potřebujete migrovat, můžete vytvořit další Azure Migrate projekty.

- V případě migrací VMware bez agentů je cílová oblast po povolení první replikace uzamčena.
- V případě migrací na základě agentů (VMware, fyzické servery a servery z jiných cloudů) je cílová oblast po kliknutí na portál na portálu při nastavování zařízení replikace uzamčena.
- U migrací Hyper-V bez agentů je cílová oblast po kliknutí na portál na portálu při nastavování zprostředkovatele replikace Hyper-V zamknutá.

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-subscriptions"></a>Můžeme použít stejný projekt Azure Migrate k migraci do několika předplatných? 

Ano, můžete migrovat na více předplatných ve stejné cílové oblasti pro Azure Migrate projekt. Můžete vybrat cílové předplatné a zároveň povolit replikaci pro počítač nebo sadu počítačů. Cílová oblast je pevně daná při první replikaci pro migrace VMware bez agentů a během instalace agenta Hyper-V pro migrace založené na agentech a migrace Hyper-V v tomto pořadí.

## <a name="what-are-the-migration-options-in-azure-migrate-server-migration"></a>Jaké jsou možnosti migrace v Azure Migrate: Migrace serveru?

Azure Migrate: Nástroj pro migraci serveru nabízí dvě možnosti, jak provádět migrace vašich zdrojových serverů a virtuálních počítačů do Azure – migrace bez agenta a migrace na základě agenta.

Bez ohledu na zvolenou možnost migrace je prvním krokem migrace serveru pomocí migrace do Azure: Migrace serveru slouží k povolení replikace serveru. Tím se provede počáteční replikace dat z virtuálního počítače nebo serveru do Azure. Po dokončení počáteční replikace se naváže průběžná replikace (probíhající rozdílová synchronizace), která migruje přírůstková data do Azure. Jakmile operace dosáhne fáze rozdílové synchronizace, můžete se kdykoli rozhodnout migrovat na Azure.  

Tady je několik důležitých informací, které je potřeba vzít v úvahu při rozhodování o možnosti migrace.

**Migrace bez agentů** nevyžadují nasazení softwaru (agentů) na zdrojové virtuální počítače nebo servery, které migrujete. Možnost bez agenta orchestruje replikaci integrací s funkcemi poskytovanými zprostředkovatelem virtualizace.
Možnosti replikace bez agenta jsou k dispozici pro [virtuální počítače VMware](./tutorial-migrate-vmware.md) a [virtuální počítače Hyper-V](./tutorial-migrate-hyper-v.md).

**Migrace založené na agentech** vyžadují instalaci Azure Migrate softwaru (agentů) na zdrojové virtuální počítače nebo počítače, které se mají migrovat. Možnost založená na agentech se nespoléhá na virtualizační platformu pro funkce replikace a je proto možné ji použít s jakýmkoli serverem s architekturou x86/x64 a verzí operačního systému podporovaným metodou replikace založenou na agentech.

Možnost migrace na základě agentů se dá použít pro [virtuální počítače VMware](./tutorial-migrate-vmware-agent.md), [virtuální počítače Hyper-V](./tutorial-migrate-physical-virtual-machines.md), [fyzické servery](./tutorial-migrate-physical-virtual-machines.md), [virtuální počítače běžící na AWS](./tutorial-migrate-aws-virtual-machines.md), virtuální počítače běžící na GCP nebo virtuální počítače, které běží na jiném zprostředkovateli virtualizace. Migrace na základě agenta považuje vaše počítače za účely migrace za fyzické servery.

I když migrace bez agentů nabízí další pohodlí a jednoduchost prostřednictvím možností replikace na základě agenta pro podporované scénáře (VMWare a Hyper-V), možná budete chtít zvážit použití scénáře založeného na agentovi v následujících případech použití:

- Omezené prostředí IOPS: replikace bez agenta používá snímky a spotřebovává IOPS a šířku pásma úložiště. Metodu migrace založenou na agentech doporučujeme, pokud ve vašem prostředí dojde k omezením úložiště/IOPS.
- Pokud nemáte vCenter Server, můžete s virtuálními počítači VMware nakládat jako s fyzickými servery a používat pracovní postup migrace založený na agentovi.

Pokud se chcete dozvědět víc, přečtěte si tento [článek](./server-migrate-overview.md) a porovnejte možnosti migrace pro migrace VMware.

## <a name="how-does-agentless-migration-work"></a>Jak funguje migrace bez agentů?

Azure Migrate: Migrace serveru poskytuje možnosti replikace bez agenta pro migraci virtuálních počítačů VMware a virtuálních počítačů Hyper-V se systémem Windows nebo Linux. Nástroj také poskytuje další možnost replikace na základě agenta pro servery s Windows a Linux, které se dají použít k migraci fyzických serverů a virtuálních počítačů x86/x64 na VMware, Hyper-V, AWS, GCP atd. Možnost replikace na základě agentů vyžaduje instalaci softwaru agenta na server nebo virtuální počítač, který se migruje. v takovém případě nemusíte na virtuálních počítačích samostatně instalovat žádný software, což nabízí další pohodlí a jednoduchost prostřednictvím možnosti replikace založené na agentech.

Možnost replikace bez agenta funguje pomocí mechanismů poskytovaných zprostředkovatelem virtualizace (VMware, Hyper-V). V případě virtuálních počítačů VMware používá mechanismus replikace bez agenta k replikaci dat z disků virtuálních počítačů snímky VMware a technologii VMware pro sledování bloků změn. Tento mechanismus je podobný jako ten, který používá mnoho zálohovacích produktů. V případě virtuálních počítačů Hyper-V nevyužívá mechanismus replikace bez agenta snímky virtuálních počítačů a funkci sledování změn repliky technologie Hyper-V k replikaci dat z disků virtuálních počítačů.

Když je replikace nakonfigurovaná pro virtuální počítač, prochází se nejprve fází prvotní replikace. Během počáteční replikace se pořídí snímek virtuálního počítače a na spravovaných discích ve vašem předplatném se replikuje úplná kopie dat z disků snímku. Po dokončení počáteční replikace virtuálního počítače přejde proces replikace do fáze přírůstkové replikace (rozdílové replikace). Ve fázi přírůstkové replikace se změny dat, k nimž došlo od posledního dokončeného cyklu replikace, pravidelně replikují a aplikují na spravované disky repliky, takže se replikace synchronizuje se změnami provedenými ve virtuálním počítači. V případě virtuálních počítačů VMware se k udržení přehledu změn mezi replikačními cykly používá technologie VMware pro sledování bloků. Na začátku cyklu replikace se pořídí snímek virtuálního počítače a změněné sledování bloku se používá k získání změn mezi aktuálním snímkem a posledním úspěšně replikovaným snímkem. Tímto způsobem se musí replikovat jenom data, která se změnila od posledního dokončeného cyklu replikace, aby se replikace pro virtuální počítač udržovala synchronizovaná. Na konci každého cyklu replikace se snímek uvolní a pro virtuální počítač se provede konsolidace snímků. Podobně platí, že v případě virtuálních počítačů Hyper-v se používá modul Sledování změn repliky technologie Hyper-V k udržení přehledu o změnách mezi po sobě jdoucích replikačních cyklů.
Při provádění operace migrace na virtuálním počítači repliky máte možnost vypnout místní virtuální počítač a provést jednu finální přírůstkovou replikaci, aby se zajistila nulová ztráta dat. Při provádění možnosti migrace se k vytvoření virtuálního počítače v Azure použijí spravované disky repliky odpovídající virtuálnímu počítači.

Informace o tom, jak začít, najdete v kurzech [migrace bez agentů VMware](./tutorial-migrate-vmware.md) a [migrace bez agenta Hyper-V](./tutorial-migrate-hyper-v.md) .

## <a name="how-does-agent-based-migration-work"></a>Jak funguje migrace založená na agentech?

Kromě možností migrace bez agenta pro virtuální počítače VMware a virtuální počítače Hyper-V poskytuje nástroj pro migraci serveru možnost migrace na základě agenta pro migraci serverů se systémem Windows a Linux, které běží na fyzických serverech nebo běží jako virtuální počítače x86/x64 na VMware, Hyper-V, AWS, Google Cloud Platform atd.

Metoda migrace založená na agentovi používá software agenta nainstalovaný na serveru, který se migruje, a replikuje data serveru do Azure. Proces replikace využívá architekturu snižování zátěže, ve které agent přenáší replikační data na vyhrazený replikační Server, který se nazývá zařízení replikace nebo konfigurační server (nebo na procesový Server se škálováním na více instancí). [Přečtěte si další informace](./agent-based-migration-architecture.md) o tom, jak funguje možnost migrace na základě agenta. 

Poznámka: zařízení replikace se liší od zařízení pro zjišťování Azure Migrate a musí být nainstalované na samostatném nebo vyhrazeném počítači.

## <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>Návody měřiče šířky pásma pro moje migrace?

Šířka pásma pro replikaci dat do Azure závisí na řadě faktorů a na tom, jak rychle může místní Azure Migrate zařízení číst a replikovat data do Azure. Replikace má dvě fáze: počáteční replikace a rozdílová replikace.

Když se replikace spustí pro virtuální počítač, proběhne počáteční cyklus replikace, při které se replikují úplné kopie disků. Po dokončení počáteční replikace budou cykly přírůstkové replikace (rozdílové cykly) pravidelně naplánovány k přenosu všech změn, ke kterým došlo od posledního replikačního cyklu.

### <a name="agentless-vmware-vm-migration"></a>Migrace virtuálních počítačů VMware bez agentů

Požadavek na šířku pásma můžete vyřešit na základě objemu dat, která je potřeba přesunout do vlny a času, ve kterém byste chtěli dokončit počáteční replikaci (v ideálním případě byste chtěli počáteční replikaci provést). bylo dokončeno alespoň 3-4 dní před aktuálním oknem migrace, abyste měli dostatek času na provedení testu před aktuálním oknem a za účelem zachování minimálního výpadku v průběhu okna).

Můžete odhadnout šířku pásma nebo dobu potřebnou pro migraci virtuálních počítačů VMware bez agenta pomocí následujícího vzorce:

Doba, po kterou se má dokončit počáteční replikace = {velikost disků (nebo použitá velikost, je-li k dispozici) × 0,7 (za předpokladu, že je k dispozici 30% průměr komprese – konzervativní odhad)}/Bandwidth

### <a name="agent-based-vmware-vm-migration"></a>Migrace virtuálních počítačů VMware na základě agentů

Pro metodu replikace založenou na agentovi může Plánovač nasazení pomáhat profilovat prostředí pro změny dat a předpovídat potřebný požadavek na šířku pásma. Pokud se chcete dozvědět víc, podívejte se na tento [článek](./agent-based-migration-architecture.md#plan-vmware-deployment). 

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Návody omezení replikace při používání zařízení Azure Migrate pro bezagentskou replikaci VMware?  

Můžete omezit pomocí NetQosPolicy. Příklad:

AppNamePrefix pro použití v NetQosPolicy je "GatewayWindowsService.exe". Můžete vytvořit zásadu na zařízení Azure Migrate, abyste omezili provoz replikace ze zařízení tím, že vytvoříte zásadu, jako je tato:

New-NetQosPolicy-Name "ThrottleReplication"-AppPathNameMatchCondition "GatewayWindowsService.exe"-ThrottleRateActionBitsPerSecond 1 MB

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>Jak se data přenáší z prostředí Prem do Azure? Je před přenosem zašifrovaný?

Zařízení Azure Migrate v případě replikace bez agenta komprimuje data a šifruje před odesláním. Data se přenáší přes protokol HTTPS přes zabezpečený komunikační kanál a používají TLS 1,2 nebo novější. Navíc Azure Storage automaticky šifruje vaše data při jejich trvalém ukládání do cloudu (šifrování v klidovém prostředí).  

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Jak četnost změn ovlivňuje replikaci bez agentů?

Vzhledem k tomu, že replikace bez agentů je přeložena v datech, je *vzor* změn důležitější než *frekvence četnosti* změn. Když se znovu zapíše a znovu vytvoří soubor, míra nebude mít žádný vliv. Vzor, ve kterém je zápis všech ostatních sektorů, ale způsobuje vysokou ztrátu v dalším cyklu. Vzhledem k tomu, že minimalizujeme objem přenášených dat, umožňujíme, aby data byla přeložena co nejvíce, než naplánujeme další cyklus.

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Jak často je naplánováno replikační cyklus?

Vzorec k naplánování dalšího cyklu replikace je (předchozí čas cyklu/2) nebo jedna hodina, podle toho, co je vyšší.

Pokud třeba virtuální počítač pro rozdílový cyklus trvá čtyři hodiny, další cyklus se naplánuje za dvě hodiny, a ne za příští hodinu. Proces se liší od počáteční replikace, když je první cyklus rozdílu naplánován okamžitě.

## <a name="how-do-i-migrate-windows-server-2003-running-on-vmwarehyper-v-to-azure"></a>Návody migrujete Windows Server 2003 běžící na VMware/Hyper-V do Azure?

[Rozšířená podpora Windows serveru 2003](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) skončila 14. července 2015.  Tým podpory Azure bude dál pomáhat při řešení problémů, které se týkají spuštění Windows serveru 2003 v Azure. Tato podpora je však omezená na problémy, které nevyžadují řešení potíží nebo opravy na úrovni operačního systému.
Doporučený postup je migrace vašich aplikací do instancí Azure, na kterých běží novější verze Windows serveru, abyste měli jistotu, že budete efektivně využívat flexibilitu a spolehlivost cloudu Azure.

Pokud se ale stále rozhodnete migrovat Windows Server 2003 do Azure, můžete použít nástroj Azure Migrate: Server pro migraci, pokud je Windows Server VIRTUÁLNÍm počítačem, který běží na VMware nebo Hyper-V tento článek, abyste mohli připravit počítače s [Windows serverem 2003 pro migraci](./prepare-windows-server-2003-migration.md).

## <a name="what-is-the-difference-between-the-test-migration-and-migrate-operations"></a>Jaký je rozdíl mezi operacemi testování migrace a migrace?

Test migrace nabízí způsob, jak testovat a ověřovat migrace před samotnou migrací. Testovací migrace funguje tak, že vám umožní vytvořit testovací kopie replikačních virtuálních počítačů v prostředí izolovaného prostoru (sandbox) v Azure. Prostředí izolovaného prostoru (sandbox) je vymezené testovací virtuální sítí, kterou zadáte. Operace testování migrace je nerušivá a aplikace pokračuje v běhu ve zdroji a přitom umožňují provádět testy klonované kopie v izolovaném prostředí izolovaného prostoru (sandbox). V případě potřeby můžete provést několik testů, aby bylo možné migraci ověřit, provádět testování aplikací a řešit případné problémy před samotnou migrací.

## <a name="will-windows-server-2008-and-2008-r2-be-supported-in-azure-after-migration"></a>Budou se v Azure po migraci podporovat Windows Server 2008 a 2008 R2?

Můžete migrovat místní servery Windows Server 2008 a 2008 R2 na virtuální počítače Azure a získat rozšířené aktualizace zabezpečení na tři roky po skončení dat podpory bez dalších poplatků nad náklady na provozování virtuálního počítače. K migraci úloh Windows Server 2008 a 2008 R2 můžete použít nástroj Azure Migrate: Server pro migraci.

## <a name="is-there-a-rollback-option-for-azure-migrate"></a>Existuje možnost vrácení zpět pro Azure Migrate?

Pomocí možnosti test Migration můžete ověřit funkčnost aplikace a výkon v Azure. Můžete provést libovolný počet testovacích migrací a po dokončení operace testování migrace může provést konečnou migraci. Migrace testu nemá vliv na místní počítač, který zůstává v provozu a pokračuje v replikaci, dokud neprovedete vlastní migraci. Pokud došlo k chybám při UAT testování migrace, můžete se rozhodnout odložit konečnou migraci a zachovat zdrojový virtuální počítač nebo Server běžící a replikovat do Azure. Po vyřešení chyb můžete pokusit poslední migraci znovu.  
Poznámka: Jakmile provedete konečnou migraci do Azure a místní zdrojový počítač byl vypnutý, nebudete moct vrátit zpátky z Azure do místního prostředí.

## <a name="can-i-select-the-virtual-network-and-subnet-to-use-for-test-migrations"></a>Můžu vybrat Virtual Network a podsíť, které se mají použít pro testovací migrace?

Můžete vybrat Virtual Network pro testovací migrace. Podsíť se automaticky vybere v závislosti na následující logice:

- Pokud byla při povolování replikace Zadaná cílová podsíť (jiná než výchozí) jako vstup, Azure Migrate určí prioritu pomocí podsítě se stejným názvem v Virtual Network vybrané pro testovací migraci.
- Pokud se nenalezne podsíť se stejným názvem, Azure Migrate vybere první dostupnou podsíť podle abecedy, která není bránou/Application Gateway/firewall/bastionu podsíť.

## <a name="why-is-the-test-migration-button-disabled-for-my-server"></a>Proč je tlačítko test migrace pro můj server zakázané?

Tlačítko test migrace může být v zakázaném stavu v následujících scénářích:

- Migraci testu nemůžete zahájit, dokud se nedokončí počáteční replikace (IR) pro virtuální počítač. Tlačítko test migrace bude zakázáno až do dokončení procesu IR. Pokud je virtuální počítač ve fázi rozdílové synchronizace, můžete provést migraci testu.
- Tlačítko může být zakázáno, pokud byla migrace testu již dokončena, ale pro tento virtuální počítač nebylo provedeno vyčištění migrace testu. Proveďte vyčištění migrace testu a zkuste operaci zopakovat.

## <a name="what-happens-if-i-dont-clean-up-my-test-migration"></a>Co se stane, když nečistím testem své migrace?

Testovací migrace simuluje skutečnou migraci vytvořením testovacího virtuálního počítače Azure pomocí replikovaných dat. Server se nasadí s bodem v čase kopírování replikovaných dat do cílové skupiny prostředků (vybraný při povolování replikace) s příponou-test. Testovací migrace jsou určené k ověřování funkčnosti serveru, aby se minimalizovaly problémy při migraci. Pokud testovací migrace neprovádí čištění po testování, testovací virtuální počítač bude i nadále běžet v Azure a účtuje se za ně poplatky. Pokud chcete vyčistit test migrace, v počítači otevřete zobrazení replikační počítače a na počítači použijte akci vyčistit test migrace.

## <a name="can-i-migrate-active-directory-domain-controllers-using-azure-migrate"></a>Můžu migrovat řadiče domény služby Active Directory pomocí Azure Migrate?

Nástroj pro migraci serveru je nezávislá aplikací a funguje pro většinu aplikací. Při migraci serveru pomocí nástroje pro migraci serveru jsou spolu s ním migrovány všechny aplikace nainstalované na serveru. U některých aplikací ale může být pro migraci vhodnější alternativní metody migrace než migrace serveru.  V případě služby Active Directory v případě hybridních prostředí, kde je místní lokalita připojená k prostředí Azure, můžete svůj adresář do Azure zvětšit přidáním dalších řadičů domény v Azure a nastavením replikace Active Directory. Pokud migrujete do izolovaného prostředí v Azure, které vyžaduje vlastní řadiče domény (nebo testování aplikací v prostředí izolovaného prostoru), můžete migrovat servery pomocí nástroje pro migraci serveru.

## <a name="what-happens-if-i-dont-stop-replication-after-migration"></a>Co se stane, když po migraci nezastavím replikaci?

Po zastavení replikace nástroj Azure Migrate: Server Migration Tool vyčistí spravované disky v předplatném, které byly vytvořeny pro replikaci. Pokud po migraci nezastavíte replikaci, budou se vám nadále účtovat poplatky za tyto disky. Zastavení replikace nebude mít vliv na disky připojené k počítačům, které už byly migrovány.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Potřebuji pro migraci virtuálních počítačů VMware Server VMware vCenter?

Pokud chcete [migrovat virtuální počítače VMware](server-migrate-overview.md) pomocí agenta VMware nebo migrace bez agentů, musí být hostitelé ESXi, na kterých jsou virtuální počítače umístěné, spravované pomocí vCenter Server. Pokud nemáte vCenter Server, můžete migrovat virtuální počítače VMware tak, že je migrujete jako fyzické servery. [Přečtěte si další informace](migrate-support-matrix-physical-migration.md).

## <a name="can-i-upgrade-my-os-while-migrating"></a>Můžu upgradovat operační systém při migraci?

Azure Migrate: Nástroj pro migraci serveru podporuje jenom migrace podobně jako v současnosti. Nástroj nepodporuje upgrade verze operačního systému během migrace. Migrovaný počítač bude mít stejný operační systém jako zdrojový počítač.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliances"></a>Nasadil (a) jsem dva zařízení pro zjišťování virtuálních počítačů v mém vCenter Server. Při pokusu o migraci virtuálních počítačů se ale zobrazí jenom virtuální počítače, které odpovídají jednomu z těchto zařízení.

Pokud je nastavených více zařízení, virtuální počítače v zadaných účtech vCenter se nesmí překrývat. Scénář zjišťování s takovým překrýváním se nepodporuje.

## <a name="how-do-i-know-if-my-vm-was-successfully-migrated"></a>Návody zjistit, jestli se můj virtuální počítač úspěšně migrovali?

Po úspěšné migraci virtuálního počítače nebo serveru můžete virtuální počítač zobrazit a spravovat na stránce Virtual Machines. Připojte se k migrovanému virtuálnímu počítači a ověřte.
Případně můžete zkontrolovat stav úlohy pro operaci, abyste zkontrolovali, jestli se migrace úspěšně dokončila. Pokud se zobrazí nějaké chyby, vyřešte je a zkuste operaci migrace zopakovat.

## <a name="can-i-consolidate-multiple-source-vms-into-one-vm-while-migrating"></a>Můžu při migraci konsolidovat víc zdrojových virtuálních počítačů do jednoho virtuálního počítače?

Podpora možností migrace serveru Azure Migrate, jako je například migrace jako v současnosti. V rámci migrace nepodporujeme slučování serverů ani upgrade operačního systému. 

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Vliv replikace bez agentů na servery VMware?

Replikace bez agentů vede k nějakým dopadům na výkon VMware vCenter Server a VMware ESXi hostitelů. Vzhledem k tomu, že replikace bez agenta používá snímky, spotřebovává v úložišti IOPS, takže se vyžaduje některá šířka pásma úložiště IOPS. Pokud máte ve svém prostředí omezení úložiště nebo IOPs, nedoporučujeme používat replikaci bez agentů.


## <a name="next-steps"></a>Další kroky

Přečtěte si [přehled Azure Migrate](migrate-services-overview.md).