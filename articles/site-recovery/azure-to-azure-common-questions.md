---
title: Běžné dotazy týkající se zotavení po havárii virtuálních počítačů Azure pomocí Azure Site Recovery
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se zotavení po havárii virtuálního počítače Azure pomocí Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 051e8b0add7cf7ab2c4cb2f02ed4e33ea9a23c9b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75973807"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Běžné otázky: zotavení po havárii z Azure do Azure

Tento článek obsahuje odpovědi na běžné dotazy týkající se zotavení po havárii virtuálních počítačů Azure do jiné oblasti Azure pomocí [Site Recovery](site-recovery-overview.md).


## <a name="general"></a>Obecné

### <a name="how-is-site-recovery-priced"></a>Jak se Site Recovery cena?
Přečtěte si informace o [cenách Azure Site Recovery](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) .
### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Jak funguje úroveň Free pro práci v rámci Azure Site Recovery?
Každá instance chráněná Azure Site Recovery je prvních 31 dní ochrany zadarmo. Od 32. dne dál se ochrana instance účtuje podle sazeb uvedených nahoře.
### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Budu se mi během prvních 31 dní účtovat nějaké další poplatky Azure?
Ano, i když je Azure Site Recovery během prvních 31 dní chráněné instance zadarmo, můžou vám být účtovány poplatky za Azure Storage, transakce úložiště a přenosy dat. U obnoveného virtuálního počítače se můžou účtovat taky poplatky za výpočty Azure. Získejte podrobné informace [o cenách](https://azure.microsoft.com/pricing/details/site-recovery)

### <a name="where-can-i-find-best-practices-for-azure-vm-disaster-recovery"></a>Kde najdu osvědčené postupy pro zotavení po havárii virtuálních počítačů Azure?
1. [Principy architektury Azure-to-Azure](azure-to-azure-architecture.md)
2. [Zkontrolujte podporované a nepodporované konfigurace](azure-to-azure-support-matrix.md)
3. [Nastavení zotavení po havárii pro virtuální počítače Azure](azure-to-azure-how-to-enable-replication.md)
4. [Spuštění testovacího převzetí služeb při selhání](azure-to-azure-tutorial-dr-drill.md)
5. [Převzetí služeb při selhání a navrácení služeb po obnovení do primární oblasti](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-guaranteed-in-the-target-region"></a>Jak je v cílové oblasti zaručená kapacita?
Tým Site Recovery spolupracuje s týmem Azure Capacity Management k naplánování dostatečné kapacity infrastruktury a pomůže zajistit, aby se virtuální počítače chráněné pomocí Site Recovery pro úspěšně nasadily cílovou oblast při zahájení převzetí služeb při selhání.

## <a name="replication"></a>Replikace

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Můžu replikovat virtuální počítače povolené prostřednictvím služby Azure Disk Encryption?

Ano, Site Recovery podporuje zotavení po havárii virtuálních počítačů s povoleným službou Azure Disk Encryption (ADE). Pokud povolíte replikaci, všechny požadované šifrovací klíče disku a tajné klíče se zkopírují ze zdrojové oblasti do cílové oblasti v kontextu uživatele. Pokud nemáte příslušná oprávnění, skript připravený k použití může být předán správci zabezpečení, aby mohl zkopírovat klíče a tajné kódy.

- Site Recovery podporuje ADE pro virtuální počítače Azure s Windows.
- Site Recovery podporuje ADE verze 0,1 se schématem používajícím Azure Active Directory (AAD) a verzí 1,1 bez AAD. [Další informace](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schemata).
- ADE verze 1,1: virtuální počítače s Windows musí používat spravované disky.
- [Přečtěte si další informace](azure-to-azure-how-to-enable-replication-ade-vms.md) o povolení replikace pro šifrované virtuální počítače.



### <a name="can-i-replicate-vms-to-another-subscription"></a>Můžu replikovat virtuální počítače do jiného předplatného?
Ano, virtuální počítače Azure můžete replikovat do jiného předplatného v rámci stejného tenanta služby Azure AD.
Konfigurace DR [v rámci předplatných](https://azure.microsoft.com/blog/cross-subscription-dr) je jednoduchá. V době replikace můžete vybrat jiné předplatné.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Můžu replikovat virtuální počítače Azure připojené k zóně do jiné oblasti?
Ano, [virtuální počítače připnuté do zóny můžete replikovat](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) do jiné oblasti.

### <a name="can-i-exclude-disks"></a>Můžu vyloučit disky?

Ano, disky můžete v době ochrany vyloučit pomocí prostředí PowerShell. Další informace najdete v [článku](azure-to-azure-exclude-disks.md) .

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Můžu do replikovaných virtuálních počítačů přidávat nové disky a pro ně povolit replikaci?

Ano, to je podporováno pro virtuální počítače Azure se spravovanými disky. Když do virtuálního počítače Azure, který je povolen pro replikaci, přidáte nový disk, stav replikace virtuálního počítače zobrazí upozornění s poznámkou toho, že jeden nebo více disků na virtuálním počítači je k dispozici pro ochranu. Replikaci můžete povolit pro přidané disky.
- Pokud povolíte ochranu pro přidané disky, bude upozornění po počáteční replikaci zmizí.
- Pokud se rozhodnete Nepovolit replikaci pro disk, můžete vybrat možnost Zavřít upozornění.
- Při převzetí služeb při selhání virtuálního počítače, ke kterému přidáte disk a pro něj povolíte replikaci, budou body replikace zobrazovat disky, které jsou k dispozici pro obnovení. Například pokud má virtuální počítač jeden disk a přidáte nový, body replikace, které byly vytvořeny před přidáním disku, zobrazí, že bod replikace se skládá z "1 z 2 disků".

Site Recovery nepodporuje "Hot Remove" disku z replikovaného virtuálního počítače. Pokud odeberete disk virtuálního počítače, musíte zakázat a znovu povolit replikaci virtuálního počítače.


### <a name="how-often-can-i-replicate-to-azure"></a>Jak často je možné replikovat do Azure?
Replikace je nepřetržitá, když provádíte replikaci virtuálních počítačů Azure do jiné oblasti Azure. Další informace najdete v tématu [Architektura replikace z Azure do Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-to-migrate-vms"></a>Můžu replikovat virtuální počítače v nějaké oblasti? Potřebuji k migraci virtuálních počítačů.
Řešení zotavení po havárii z Azure do Azure nemůžete použít k replikaci virtuálních počítačů v rámci jedné oblasti.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>Můžu replikovat virtuální počítače do jakékoli oblasti Azure?
Pomocí Site Recovery můžete replikovat a obnovovat virtuální počítače mezi dvěma oblastmi v rámci stejného geografického clusteru. Geografické clustery jsou definovány s latencí a suverenitou dat. Další informace najdete v tématu [matice podpory Site Recovery oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support).

### <a name="does-site-recovery-require-internet-connectivity"></a>Vyžaduje Site Recovery připojení k Internetu?

Ne, Site Recovery nevyžaduje připojení k Internetu. Ale vyžaduje přístup k adresám URL Site Recovery a rozsahům IP adres, jak je uvedeno v [tomto článku](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

### <a name="can-i-replicate-the-application-having-separate-resource-group-for-separate-tiers"></a>Můžu replikovat aplikaci, která má samostatnou skupinu prostředků pro samostatné úrovně?
Ano, můžete replikovat aplikaci a zachovat konfiguraci zotavení po havárii v samostatné skupině prostředků.
Pokud máte například aplikaci s každou aplikací vrstev, databází a webem v samostatné skupině prostředků, musíte kliknout na [Průvodce replikací](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) třikrát a chránit tak všechny úrovně. Site Recovery budou replikovat tyto tři vrstvy ve třech různých skupinách prostředků.

## <a name="replication-policy"></a>Zásady replikace

### <a name="what-is-a-replication-policy"></a>Co je zásada replikace?
Definuje nastavení pro historii uchovávání bodů obnovení a četnosti snímků konzistentních vzhledem k aplikacím. Ve výchozím nastavení Azure Site Recovery vytvoří novou zásadu replikace s výchozími nastaveními:

* 24 hodin pro historii uchovávání bodů obnovení.
* 60 minut pro četnost snímků konzistentních vzhledem k aplikacím.

[Další informace](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Co je bod obnovení konzistentní vzhledem k selháním?
Bod obnovení konzistentní vzhledem k selháním představuje data na disku, jako kdyby došlo k chybě virtuálního počítače nebo napájecí kabel byl získán ze serveru v době pořízení snímku. Neobsahuje vše, co bylo v paměti při pořízení snímku.

V současné době se většina aplikací může zotavit i z snímků konzistentních vzhledem k chybě. Bod obnovení konzistentní vzhledem k selháním je obvykle dostačující pro operační systémy bez databáze a aplikace jako souborové servery, servery DHCP a tiskové servery.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Jaká je četnost generování bodu obnovení konzistentního vzhledem k chybě?
Site Recovery vytvoří bod obnovení konzistentní vzhledem k selháním každých 5 minut.

### <a name="what-is-an-application-consistent-recovery-point"></a>Co je bod obnovení konzistentní vzhledem k aplikacím?
Body obnovení konzistentní vzhledem k aplikacím se vytvářejí z snímků konzistentních vzhledem k aplikacím. Body obnovení konzistentní vzhledem k aplikacím zachycují stejná data jako snímky konzistentní s chybou, a to s přidáním všech dat v paměti a všech probíhajících transakcí.
Vzhledem k jejich nadbytečnému obsahu jsou nejvhodnější snímky konzistentní vzhledem k aplikacím a jejich provedení bude trvat nejdéle. Pro databázové operační systémy a aplikace, jako je například SQL Server, doporučujeme body obnovení konzistentní vzhledem k aplikacím.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Jaký je dopad bodů obnovení konzistentních vzhledem k aplikacím na výkon aplikace?
V případě, že body obnovení konzistentní vzhledem k aplikacím zachytí všechna data v paměti a v procesu, vyžaduje rozhraní, jako je VSS ve Windows k uvedení aplikace do nečinnosti. V případě, že to funguje velmi často, může mít dopad na výkon, pokud je zatížení již velmi zaneprázdněné. Obvykle se navrhuje, aby nepoužívala nízkou frekvenci pro body obnovení konzistentní vzhledem k aplikacím pro úlohy jiné než databáze a dokonce i pro zatížení databáze 1 hodina.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Jaká je minimální frekvence generování bodu obnovení konzistentního vzhledem k aplikacím?
Site Recovery může vytvořit bod obnovení konzistentní vzhledem k aplikacím s minimální frekvencí během 1 hodiny.

### <a name="how-are-recovery-points-generated-and-saved"></a>Jak se vygenerovaly a ukládají body obnovení?
Abychom porozuměli tomu, jak Site Recovery generuje body obnovení, podíváme se na příklad zásad replikace, které mají Interval uchování bodu obnovení 24 hodin a snímkování frekvence konzistentní vzhledem k aplikaci 1 hodina.

Site Recovery vytvoří bod obnovení konzistentní vzhledem k selháním každých 5 minut. Uživatel nemůže tuto frekvenci změnit. Takže za poslední 1 hodinu bude mít uživatel 12 bodů konzistentních z havárie a 1 bod konzistentní vzhledem k aplikacím pro výběr. V průběhu času Site Recovery vyřadí všechny body obnovení za poslední 1 hodinu a uloží pouze 1 bod obnovení za hodinu.

Příklad ukazuje následující snímek obrazovky. Na snímku obrazovky:

1. Pro čas kratší než poslední 1 hodina existují body obnovení s frekvencí 5 minut.
2. Po dobu delší než poslední 1 hodina Site Recovery zachovává jenom 1 bod obnovení.

   ![Seznam generovaných bodů obnovení](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Jak daleko zpátky můžu obnovit?
Nejstarší bod obnovení, který můžete použít, je 72 hodin.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Co se stane, když mám zásadu replikace 24 hodin a problém zabrání Site Recovery generování bodů obnovení po dobu delší než 24 hodin? Ztratí se předchozí body obnovení?
Ne, Site Recovery bude uchovávat všechny předchozí body obnovení. V závislosti na časovém intervalu pro uchovávání bodů obnovení v tomto případě Site Recovery nahrazuje nejstarší bod pouze v případě, že existuje generace nových bodů. V takovém případě nedojde k žádnému novému vygenerování nového bodu obnovení kvůli nějakému problému, ale všechny staré body zůstanou po dosažení okna uchování beze změny.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Jak se po replikaci na virtuálním počítači povolí replikace, jak změním zásady replikace?
Přejít do > **Site Recovery trezoru** Site Recovery **Zásady replikace** > **infrastruktury** . Vyberte zásadu, kterou chcete upravit, a uložte změny. Všechny změny se projeví i u všech stávajících replikací.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Má všechny body obnovení úplnou kopii virtuálního počítače nebo rozdílu?
První vygenerovaný bod obnovení má úplnou kopii. Všechny úspěšné body obnovení mají rozdílové změny.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Zvyšuje doba uchování bodů obnovení náklady na úložiště?
Ano. Pokud zvýšíte dobu uchovávání dat z 24 hodin na 72 hodin, Site Recovery budou body obnovení po dobu dalších 48 hodin ukládat. Přidaný čas účtuje poplatky za úložiště. Pokud má například jeden bod obnovení rozdílové změny 10 GB a cena za GB je $0,16 za měsíc, budou dodatečné poplatky $1,6 * 48 za měsíc.

## <a name="multi-vm-consistency"></a>Konzistence s více virtuálními počítači

### <a name="what-is-multi-vm-consistency"></a>Co je konzistence více virtuálních počítačů?
Znamená to, že je bod obnovení konzistentní napříč všemi replikovanými virtuálními počítači.
Site Recovery poskytuje možnost "konzistence s více virtuálními počítači", a když ji vyberete, vytvoří replikační skupinu pro replikaci všech počítačů, které jsou součástí skupiny.
Všechny virtuální počítače budou mít při převzetí služeb při selhání sdílené body obnovení konzistentní se selháním a konzistentní vzhledem k aplikacím.
Projděte si kurz a [Povolte konzistenci pro více virtuálních počítačů](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm).

### <a name="can-i-failover-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Můžu převzít služby při selhání jednoho virtuálního počítače v rámci replikační skupiny konzistence s více virtuálními počítači?
Výběrem možnosti konzistence s více virtuálními počítači zjistíte, že aplikace bude závislá na všech virtuálních počítačích v rámci skupiny. V jednom případě převzetí služeb při selhání jednoho virtuálního počítače se proto nepovoluje.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Kolik virtuálních počítačů je možné replikovat jako součást replikační skupiny konzistence s více virtuálními počítači?
Do replikační skupiny můžete replikovat 16 virtuálních počítačů dohromady.

### <a name="when-should-i-enable-multi-vm-consistency-"></a>Kdy mám povolit konzistenci pro více virtuálních počítačů?
Vzhledem k tomu, že se jedná o náročné zatížení procesoru, může mít vliv na výkon s více virtuálními počítači. Měla by se používat jenom v případě, že počítače používají stejnou úlohu a potřebujete konzistenci napříč několika počítači. Pokud máte například dvě instance SQL Server a dva webové servery v aplikaci, měli byste mít konzistenci s více virtuálními počítači pouze pro instance SQL Server.


## <a name="failover"></a>Převzetí služeb při selhání

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Jak je zajištěna kapacita v cílové oblasti pro virtuální počítače Azure?
Tým Site Recovery spolupracuje s týmem Azure Capacity Management k naplánování dostatečné kapacity infrastruktury, aby bylo možné zajistit, aby se virtuální počítače s povoleným zotavením po havárii v cílové oblasti v případě iniciování převzetí služeb při selhání úspěšně nasadily.

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?

Převzetí služeb při selhání není automatické. Převzetí služeb při selhání můžete zahájit jediným kliknutím na portálu, nebo můžete pomocí [PowerShellu](azure-to-azure-powershell.md) aktivovat převzetí služeb při selhání.

### <a name="can-i-retain-a-public-ip-address-after-failover"></a>Můžu po převzetí služeb při selhání zachovat veřejnou IP adresu?

Veřejnou IP adresu produkční aplikace nelze po převzetí služeb při selhání uchovat.
- Úlohy, které se zadají jako součást procesu převzetí služeb při selhání, musí mít přiřazený prostředek veřejné IP adresy Azure, který je dostupný v cílové oblasti.
- Můžete to provést ručně nebo automatizovat pomocí plánu obnovení.
- Přečtěte si, jak [nastavit veřejné IP adresy po převzetí služeb při selhání](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).  

### <a name="can-i-retain-a-private-ip-address-during-failover"></a>Můžu během převzetí služeb při selhání zachovat privátní IP adresu?
Ano, můžete zachovat privátní IP adresu. Ve výchozím nastavení se při povolování zotavení po havárii pro virtuální počítače Azure Site Recovery vytvoří cílové prostředky na základě nastavení zdrojového prostředku. – U virtuálních počítačů Azure nakonfigurovaných pomocí statických IP adres se Site Recovery pokusí zřídit stejnou IP adresu pro cílový virtuální počítač, pokud se nepoužívá.
Přečtěte si informace o [zachování IP adres během převzetí služeb při selhání](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-failover-why-is-the-server-assigned-a-new-ip-address"></a>Proč je po převzetí služeb při selhání Server přiřazená nová IP adresa?

Site Recovery se pokusí zadat IP adresu v době převzetí služeb při selhání. Pokud se tato adresa převezme v jiném virtuálním počítači, Site Recovery nastaví další dostupnou IP adresu jako cíl.
Přečtěte si další informace o [Nastavení mapování sítě a adresování IP pro virtuální sítě](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Co jsou **nejnovější body obnovení (nejnižší RPO)** ?
Možnost **nejnovější (nejnižší RPO)** nejprve zpracovává všechna data, která byla odeslána do služby Site Recovery, aby bylo možné vytvořit bod obnovení pro každý virtuální počítač před tím, než dojde k převzetí služeb při selhání. Tato možnost poskytuje nejnižší cíl bodu obnovení (RPO), protože virtuální počítač vytvořený po převzetí služeb při selhání má všechna replikovaná data, která se Site Recovery při aktivaci převzetí služeb při selhání.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Mají dopad na RTO pro převzetí služeb při selhání **nejnovější (nejnižší RPO)** body obnovení?
Ano. Site Recovery zpracovává všechna nevyřízená data před převzetím služeb při selhání, takže tato možnost má vyšší cíl času obnovení (RTO) ve srovnání s jinými možnostmi.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Co znamená **poslední zpracovaná** možnost v bodech obnovení?
**Poslední zpracovaná** možnost převezme všechny virtuální počítače v plánu na nejnovější bod obnovení, který Site Recovery zpracoval. Chcete-li zobrazit nejnovější bod obnovení pro konkrétní virtuální počítač, zkontrolujte v nastavení virtuálního počítače **nejnovější body obnovení** . Tato možnost poskytuje nízkou RTO, protože nestráví zpracováním nezpracovaných dat žádného času.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Co se stane, když v naší primární oblasti dojde k neočekávanému výpadku?
Po výpadku můžete aktivovat převzetí služeb při selhání. Site Recovery nepotřebuje připojení z primární oblasti, aby se provedlo převzetí služeb při selhání.

### <a name="what-is-a-rto-of-a-vm-failover-"></a>Co je RTO pro převzetí služeb virtuálního počítače při selhání?
Site Recovery [RTO SLA o 2 hodiny](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Ve většině případů ale Site Recovery převzetí služeb při selhání virtuálních počítačů během několika minut. RTO můžete vypočítat tak, že na úlohy převzetí služeb při selhání zobrazíte čas potřebný k uvedení virtuálního počítače. RTO pro plán obnovení najdete níže v části.

## <a name="recovery-plans"></a>Plány obnovení

### <a name="what-is-a-recovery-plan"></a>Co je plán obnovení?
Plán obnovení v Site Recovery orchestruje obnovení virtuálních počítačů v převzetí služeb při selhání. Pomáhá zajistit konzistentní obnovení, opakovatelnost a automatizované obnovení. Plán obnovení řeší pro uživatele následující požadavky:

- Definování skupiny virtuálních počítačů, u kterých dojde k převzetí služeb při selhání
- Definování závislostí mezi virtuálními počítači, aby se aplikace přesně vystavila
- Automatizace obnovení spolu s vlastními ručními akcemi pro zajištění jiných úloh než převzetí služeb při selhání virtuálních počítačů

[Přečtěte si další informace](site-recovery-create-recovery-plans.md) o plánech obnovení.

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Jak se dosáhne sekvencování v plánu obnovení?

V plánu obnovení můžete vytvořit více skupin, abyste mohli sekvencování dosáhnout. U každé skupiny dojde v jednom okamžiku k převzetí služeb při selhání. Virtuální počítače, které jsou součástí stejné skupiny, se převezmou společně a další skupina. Informace o modelování aplikace pomocí plánu obnovení najdete v tématu [o plánech obnovení](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Jak můžu najít RTO plánu obnovení?
Pokud chcete zkontrolovat RTO plánu obnovení, proveďte test převzetí služeb při selhání pro plán obnovení a přejít na **Site Recovery úlohy**.
V následujícím příkladu úloha s názvem SAPTestRecoveryPlan trvala 8 minut a 59 sekund pro převzetí služeb při selhání všemi virtuálními počítači a provádění zadaných akcí.

![Seznam úloh Site Recovery](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Můžu do plánu obnovení přidat Runbooky Automation?
Ano, do svého plánu obnovení můžete integrovat Azure Automation Runbooky. [Další informace](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Znovu naochrana a navrácení služeb po obnovení

### <a name="after-a-failover-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Po převzetí služeb při selhání z primární oblasti do oblasti zotavení po havárii jsou virtuální počítače v oblasti DR chráněné automaticky?
Ne. Při [převzetí služeb při selhání](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) virtuálních počítačů Azure z jedné oblasti do druhé se virtuální počítače spustí v oblasti zotavení po havárii v nechráněném stavu. K navrácení služeb virtuálních počítačů do primární oblasti je potřeba znovu nastavit [ochranu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) virtuálních počítačů v sekundární oblasti.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>V době ochrany Site Recovery replikuje úplná data ze sekundární oblasti do primární oblasti?
Záleží na situaci. Pokud třeba virtuální počítač zdrojové oblasti existuje, synchronizují se jenom změny mezi zdrojovým diskem a cílovým diskem. Site Recovery vypočítá rozdíly porovnáním disků a poté převede data. Tento proces obvykle trvá několik hodin. Další informace o tom, co se děje během opětovné ochrany, najdete v tématu věnovaném opětovné [ochraně virtuálních počítačů Azure při selhání do primární oblasti]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Jak dlouho trvá navrácení služeb po obnovení?
Po reochraně je doba navrácení služeb po obnovení obecně podobná času, který byl potřeba pro převzetí služeb při selhání z primární oblasti do sekundární oblasti.

## <a name="capacity"></a>Klíčivost

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Jak je zajištěna kapacita v cílové oblasti pro virtuální počítače Azure?
Tým Site Recovery spolupracuje s týmem Azure Capacity Management k naplánování dostatečné kapacity infrastruktury, aby bylo možné zajistit, aby se virtuální počítače s povoleným zotavením po havárii v cílové oblasti úspěšně nasadily při zahájení převzetí služeb při selhání.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Pracuje Site Recovery s rezervovanými instancemi?
Ano, můžete si koupit [instance rezerv](https://azure.microsoft.com/pricing/reserved-vm-instances/) v oblasti zotavení po havárii a Site Recovery v nich tyto operace převzetí služeb při selhání budou používat. </br> Je potřeba žádná další konfigurace.


## <a name="security"></a>Zabezpečení

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Posílají se replikační data do služby Site Recovery?
Ne, Site Recovery nezachycují replikovaná data a nemá žádné informace o tom, co na vašich virtuálních počítačích běží. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.  
Site Recovery je ISO 27001:2013, 27018, HIPAA, DPA Certified a je v procesu SOC2 a FedRAMP JAB Assessments.

### <a name="does-site-recovery-encrypt-replication"></a>Šifruje Site Recovery replikaci?
Ano, podporuje se šifrování v [klidovém](https://docs.microsoft.com/azure/storage/storage-service-encryption) provozu i šifrování v Azure.

## <a name="next-steps"></a>Další kroky
* [Zkontrolujte](azure-to-azure-support-matrix.md) požadavky na podporu.
* [Nastavit](azure-to-azure-tutorial-enable-replication.md) Replikace z Azure do Azure.
- Pokud máte dotazy i po přečtení tohoto článku, publikujte je na [fóru Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).
