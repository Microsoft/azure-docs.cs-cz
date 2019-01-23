---
title: 'Nejčastější dotazy: Zotavení po havárii Azure do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu'
description: Tento článek shrnuje běžné otázky při nastavování zotavení po havárii virtuálních počítačů Azure do jiné oblasti Azure pomocí Azure Site Recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: a277e392acb8587e05bb78d1d8dacce40bf91f56
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449550"
---
# <a name="common-questions-azure-to-azure-replication"></a>Nejčastější dotazy: Replikace z Azure do Azure

Tento článek obsahuje odpovědi na běžné dotazy týkající se nasazení zotavení po havárii (DR) virtuálních počítačů Azure do jiné oblasti Azure pomocí Azure Site Recovery. Pokud po přečtení tohoto článku máte dotazy, zveřejněte na [fórum služby Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Obecné
### <a name="how-is-site-recovery-priced"></a>Jak se účtuje Site Recovery?
Kontrola [ceny za Azure Site Recovery](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) podrobnosti.

### <a name="what-are-the-best-practices-for-configuring-site-recovery-on-azure-vms"></a>Co jsou osvědčené postupy pro konfiguraci Site Recovery na virtuálních počítačích Azure?
1. [Vysvětlení architektury Azure do Azure](azure-to-azure-architecture.md)
2. [Zkontrolujte podporované a nepodporované konfigurace](azure-to-azure-support-matrix.md)
3. [Nastavení zotavení po havárii pro virtuální počítače Azure](azure-to-azure-how-to-enable-replication.md)
4. [Spuštění testovacího převzetí služeb při selhání](azure-to-azure-tutorial-dr-drill.md)
5. [Převzetí služeb při selhání a navrácení služeb po obnovení do primární oblasti](azure-to-azure-tutorial-failover-failback.md)

## <a name="replication"></a>Replikace

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Můžete replikovat virtuální počítače prostřednictvím Azure disk encryption?
Ano, je možné replikovat. Přečtěte si článek [virtuální počítače do jiné oblasti Azure s podporou replikace Azure disk encryption](azure-to-azure-how-to-enable-replication-ade-vms.md). Azure Site Recovery v současné době podporuje pouze virtuální počítače Azure, běží operační systém Windows a povoleného pro šifrování s aplikací Azure Active Directory (Azure AD).

### <a name="can-i-replicate-vms-to-another-subscription"></a>Můžete replikovat virtuální počítače do jiného předplatného?
Ano, můžete replikovat virtuální počítače Azure do jiného předplatného ve stejném tenantovi Azure AD.
Konfigurace zotavení po Havárii [napříč předplatnými](https://azure.microsoft.com/blog/cross-subscription-dr) je jednoduché. Můžete vybrat jiné předplatné v době replikace.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Můžete replikovat připnuté zóny virtuálních počítačů Azure do jiné oblasti?
Ano, můžete [replikace virtuálních počítačů připnuté zóny](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) do jiné oblasti.

### <a name="can-i-exclude-disks"></a>Vyloučení disků

Ano, můžete vyloučit disky v době provedení ochrany pomocí prostředí PowerShell. Další informace najdete v tématu [pokyny k Powershellu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine).

### <a name="how-often-can-i-replicate-to-azure"></a>Jak často můžete replikovat do Azure?
Replikace je souvislý při replikaci virtuálních počítačů Azure do jiné oblasti Azure. Další informace najdete v tématu [architektury pro replikaci Azure do Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-to-migrate-vms"></a>Můžete replikovat virtuální počítače v rámci oblasti? Můžu potřebovat k migraci virtuálních počítačů.
Nelze použít jako řešení zotavení po Havárii Azure do Azure pro replikaci virtuálních počítačů v rámci oblasti.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>Můžete replikovat virtuální počítače do libovolné oblasti Azure?
Pomocí služby Site Recovery můžete replikovat a obnovovat virtuální počítače mezi všechny dvou oblastí ve stejné zeměpisné clusteru. Geografické clustery jsou definovány s latencí dat a suverenitu v úvahu. Další informace najdete v tématu Site Recovery [systém podpory replikace z oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support).

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery vyžaduje připojení k Internetu?

Ne, Site Recovery nevyžaduje připojení k Internetu. Ale vyžaduje přístup k adresám URL služby Site Recovery a IP rozsahy, jak je uvedeno v [v tomto článku](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

## <a name="replication-policy"></a>Zásady replikace

### <a name="what-is-a-replication-policy"></a>Co jsou zásady replikace?
Definuje nastavení historie uchovávání bodů obnovení a frekvenci snímků konzistentních s aplikací. Ve výchozím nastavení Azure Site Recovery vytvoří novou zásadu replikace s výchozím nastavením:

* po dobu 24 hodin historie uchovávání bodů obnovení.
* 60 minut, než se frekvence snímků konzistentní vzhledem k.

[Další informace](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Co je bod obnovení konzistentní při selhání?
Bod obnovení konzistentní při selhání představuje data na disku, jako kdyby došlo k chybě virtuální počítač nebo napájecí kabel byl stažen ze serveru v době pořízení snímku. Neměl by zahrnovat cokoli, co byl v paměti při pořízení snímku. 

V současné době většina aplikací můžete obnovit také ze snímků konzistentních při chybě. Bod obnovení konzistentní při selhání je dostatečně obvykle ne databáze operační systémy a aplikace jako souborové servery, servery DHCP a tiskových serverů.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Co je frekvence generování bod obnovení konzistentní při selhání?
Site Recovery vytvoří bod obnovení konzistentní při selhání každých 5 minut.

### <a name="what-is-an-application-consistent-recovery-point"></a>Co je bod obnovení konzistentní? 
Body obnovení konzistentní vzhledem k aplikaci vytvořené ze snímků konzistentních s aplikací. Konzistentní snímky zachycují stejná data jako snímky konzistentní při selhání, a uveďte všechna data v paměti a všechny probíhající transakce. 

Kvůli další obsah snímky konzistentními se nejvíce podílejí a trvat nejdéle provádět. Doporučujeme, abyste body obnovení konzistentní pro databázi operačních systémů a aplikací, jako je SQL Server.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Co je minimální frekvenci generování bod obnovení konzistentní s aplikací?
Site Recovery můžete vytvoří bod obnovení konzistentní s minimální frekvenci za 1 hodinu.

### <a name="how-are-recovery-points-generated-and-saved"></a>Jak se body obnovení vygenerovat a uložit?
K pochopení, jak Site Recovery vytvoří body obnovení, Pojďme se na příklad zásady replikace, který má bodu okno uchování 24 hodin a snímek konzistentní vzhledem k frekvenci 1 hodina.

Site Recovery vytvoří bod obnovení konzistentní při selhání každých 5 minut. Uživatel nemůže změnit četnost odesílání. Tak za poslední 1 hodinu, uživatel bude mít 12 konzistentní při selhání můžete vybírat z bodu body a konzistentní vzhledem k 1. V průběhu času, Site Recovery vyřadí všechny obnovení ukazuje za poslední 1 hodinu a ukládá pouze 1 obnovení bodu za hodinu.

Následující snímek obrazovky ukazuje příklad. Na snímku obrazovky:

1. Dobu menší než poslední 1 hodinu existují body obnovení s četností 5 minut.
2. Pro čas za poslední 1 hodinu nastavení uchovává Site Recovery bodu obnovení pouze 1.

  ![Seznam bodů obnovení vygenerované](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Jak daleko mohou obnovit?
Nejstarší bod obnovení, který vám pomůže je 72 hodin.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Co se stane, když mám zásady replikace 24 hodin a problém brání Site Recovery z generování body obnovení po dobu více než 24 hodin? Budou Moje předchozí body obnovení ztraceny?
Ne, Site Recovery budete mít všechna vaše předchozí body obnovení. V závislosti na interval uchovávání bodů obnovení, 24 hodin. v takovém případě Site Recovery nahrazuje nejstarší bod pouze v případě, že je generování nových bodů. V takovém případě nebudou žádné nový bod obnovení, které jsou generované kvůli problému, staré body zůstane zachován beze změny Jakmile dosáhneme okno uchování.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Po povolení replikace na virtuálním počítači, jak změním zásady replikace? 
Přejděte na **trezor Site Recovery** > **infrastruktura Site Recovery** > **zásady replikace**. Vyberte zásadu, kterou chcete upravit a uložit změny. Všechny změny se uplatní na všechny stávající replikace příliš. 

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Všechny body obnovení jsou úplnou kopii virtuálního počítače nebo rozdíl?
První bod obnovení, který je generován má úplnou kopii. Všechny body obnovení po sobě jdoucích mít rozdílové změny.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Zvýší náklady na úložiště prodloužení doby uchovávání bodů obnovení?
Ano. Pokud zvýšíte dobu uchování 24 hodin na 72 hodin, Site Recovery se uloží body obnovení pro další 48 hodin. Další čas budou účtovat poplatky za úložiště. Například pokud bod obnovení jeden má rozdílové změny na 10 GB a cena za GB je 0.16 $ za měsíc, bude účtovat další poplatky $1.6 * 48 za měsíc.

## <a name="multi-vm-consistency"></a>Konzistence vzhledem k více virtuálním počítačům 

### <a name="what-is-multi-vm-consistency"></a>Co je konzistence více virtuálních počítačů?
Znamená to, a ujistěte se, že je bod obnovení konzistentní vzhledem k aplikacím na všech replikovaných virtuálních počítačů.
Site Recovery poskytuje možnost "Konzistenci,", která při výběru, vytvoří skupiny replikace do replikovat všechny počítače najednou, které jsou součástí skupiny.
Všechny virtuální počítače budou mít sdílené body obnovení konzistentní při selhání a konzistentní při jejich selhání.
Projděte si kurz k [povolit konzistenci](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication).

### <a name="can-i-failover-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Je možné převzetí služeb při selhání jednoho virtuálního počítače v replikační skupině konzistence více virtuálních počítačů?
Výběrem možnosti "Konzistence více virtuálních počítačů", můžete se s informacemi o tom, že aplikace obsahuje závislost na všechny virtuální počítače v rámci skupiny. Převzetí služeb při selhání na jeden virtuální počítač proto není povolen. 

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Kolik virtuálních počítačů můžete replikovat jako součást skupiny replikace konzistence více virtuálních počítačů?
Můžete replikovat 16 virtuální počítače do společné replikační skupiny.

### <a name="when-should-i-enable-multi-vm-consistency-"></a>Pokud by měl povolit konzistence více virtuálních počítačů?
Protože je intenzivní nároky na procesor, povolení konzistence více virtuálních počítačů může ovlivnit výkon úloh. To by měla sloužit pouze v případě, že počítačích běží stejná úloha a potřebujete konzistenci napříč několika počítači. Například pokud máte dvě instance systému SQL Server a dva webové servery v aplikaci, byste měli mít konzistenci pro pouze instance systému SQL Server.


## <a name="failover"></a>Převzetí služeb při selhání

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?

Převzetí služeb při selhání není automatické. Spuštění převzetí služeb při selhání jedním kliknutím na portálu, nebo můžete použít [Powershellu](azure-to-azure-powershell.md) k aktivaci převzetí služeb při selhání. 

### <a name="can-i-retain-a-public-ip-address-after-failover"></a>Můžete zachovat veřejné IP adresy po převzetí služeb při selhání?

Veřejnou IP adresu produkční aplikace *nelze uchovávat v převzetí služeb při selhání*. Úlohy, které aktivují jako součást procesu převzetí služeb při selhání musíte být přiřazeni veřejný IP prostředek Azure, který je k dispozici v cílové oblasti. Můžete provést tento krok ručně, nebo Automatizace prostřednictvím plánu obnovení. Přiřazení veřejné IP adresy pomocí plán obnovení, najdete v článku [nastavení veřejné IP adresy po převzetí služeb při selhání](https://docs.microsoft.com/azure/site-recovery/concepts-public-ip-address-with-site-recovery#public-ip-address-assignment-using-recovery-plan).  

### <a name="can-i-retain-a-private-ip-address-during-failover"></a>Můžete zachovat privátní IP adresy během převzetí služeb při selhání?
Ano, můžete zachovat privátní IP adresu. Ve výchozím nastavení když povolíte zotavení po Havárii pro virtuální počítače Azure Site Recovery vytvoří cílové prostředky na základě zdroje prostředků nastavení. Pro Azure: virtuální počítače nakonfigurované se statickými IP adresami Site Recovery se pokusí zřízení stejnou IP adresu pro cílový virtuální počítač, pokud není používán. Pokud chcete zachovat privátní IP adresou v různých podmínkách, najdete v článku [zachovat IP adresy během převzetí služeb při selhání](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-failover-the-server-doesnt-have-the-same-ip-address-as-the-source-vm-why-is-it-assigned-a-new-ip-address"></a>Po převzetí služeb při selhání server nemá stejnou IP adresu jako zdrojový virtuální počítač. Proč se ho přiřadit novou IP adresu?

Site Recovery se pokusí zadat IP adresu v okamžiku převzetí služeb při selhání. Pokud jiného virtuálního počítače trvá, které řeší, Site Recovery nastaví další dostupnou IP adresu jako cíl. Úplné vysvětlení způsobu, jakým Site Recovery zpracovává adresování, najdete v článku [nastavit mapování sítě a IP adresování virtuálních sítí](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-network-mapping#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Co jsou **nejnovější verzi (nejnižší cíl bodu obnovení)** body obnovení?
**Nejnovější verzi (nejnižší cíl bodu obnovení)** možnost nejprve zpracuje všechna data, ke které byl odeslán do služby Site Recovery k vytvoření bodu obnovení pro každý virtuální počítač před přebírání služeb při selhání do něj. Tato možnost poskytuje nejnižší cíl bodu obnovení (RPO), protože virtuální počítač vytvořen po převzetí služeb při selhání se všechna data do Site Recovery replikovala při aktivaci převzetí služeb při selhání.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Proveďte **nejnovější verzi (nejnižší cíl bodu obnovení)** body obnovení mít vliv na převzetí služeb při selhání RTO?
Ano. Site Recovery zpracovává všechny čekající data před selháním, proto tato možnost nemá vyšší plánovaná doba obnovení (RTO) než jiné možnosti.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Co dělá **nejnovější zpracovaný** možnost v rámci obnovování bodů průměr?
**Poslední zpracovaná položka** možnost selže, všechny virtuální počítače v plánu obnovy nejnovější bod obnovení lokality zpracovat. Pokud chcete zobrazit nejnovější obnovení bodu pro konkrétní virtuální počítač, zkontrolujte **nejnovější body obnovení** v nastavení virtuálního počítače. Tato možnost poskytuje nízké hodnoty RTO, protože se neztrácí žádný čas zpracováním nezpracovaných dat.

### <a name="if-im-replicating-between-two-azure-regions-what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Pokud replikuji mezi dvěma oblastmi Azure, co se stane, když můj primární oblasti dojde k nečekanému výpadku?
Můžete aktivovat převzetí služeb při selhání po výpadek. Site Recovery nepotřebuje připojení z primární oblasti provést převzetí služeb při selhání.

### <a name="what-is-a-rto-of-a-virtual-machine-failover-"></a>Co je RTO převzetí služeb při selhání virtuálního počítače?
Site Recovery obsahuje [RTO smlouva SLA na úrovni 2 hodiny](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Ale ve většině případů, Site Recovery převzetí služeb při selhání virtuálních počítačů během několika minut. Můžete vypočítat RTO tak, že přejdete převzetí služeb při selhání úlohy, která ukazuje čas, jakou trvalo a zobrazte si virtuální počítač. Obnovení plánování RTO, přečtěte si níže část. 

## <a name="recovery-plan"></a>Plán obnovení

### <a name="what-is-a-recovery-plan"></a>Co je plán obnovení?
Plán obnovení ve službě Site Recovery orchestruje převzetí služeb při selhání virtuálních počítačů. Umožňuje konzistentně přesné, opakovatelných a automatizovaných provést obnovení. Plán obnovení řeší následující požadavky pro uživatele:

- Definuje skupinu virtuálních počítačů, které společně převezmou služby při
- Definování závislosti mezi virtuálními počítači, takže aplikace se zobrazí přesně
- Automatizace obnovení spolu s vlastní ručně prováděné akce k dosažení úloh než převzetí služeb při selhání virtuálních počítačů

[Další informace](site-recovery-create-recovery-plans.md) plány obnovení.

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Jak se dosahuje sekvencování v plánu obnovení?

V plánu obnovení můžete vytvořit více skupin, abyste dosáhli sekvencování. Každá skupina převezme služby při selhání v jednom okamžiku. Virtuální počítače, které jsou součástí stejné skupiny selhání přes společně, za nímž následuje jinou skupinu. Zjistěte, jak model aplikace s použitím plánu obnovení, najdete v článku [plány obnovení](recovery-plan-overview.md#model-apps). 

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Jak zjistím RTO plánu obnovení?
Pokud chcete zkontrolovat RTO plán obnovení, proveďte testovací převzetí služeb při selhání pro plán obnovení a přejděte na **úlohy Site Recovery**.
V následujícím příkladu trvalo úlohu nazvanou SAPTestRecoveryPlan 8 minut a 59 sekund k převzetí služeb při selhání všech virtuálních počítačů a provádět konkrétní akce.

![Seznam úlohy Site Recovery](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Můžete přidat runbooky služby automation do plánu obnovení?
Ano, můžete integrovat runbooků Azure Automation do plánu obnovení. [Další informace](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Opětovného nastavování ochrany a navrácení služeb po obnovení 

### <a name="after-a-failover-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Po převzetí služeb při selhání z primární oblasti do oblasti pro zotavení po havárii jsou virtuální počítače v oblasti zotavení po Havárii automaticky chráněná?
Ne. Pokud jste [převzetí služeb při selhání](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) virtuálních počítačů Azure z jedné oblasti do druhé a spouštění virtuálních počítačů v oblasti zotavení po Havárii v nechráněném stavu. Chcete-li navrácení služeb po obnovení virtuálních počítačů do primární oblasti, budete muset [znovunastavení ochrany](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) virtuální počítače v sekundární oblasti.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Během opětovného nastavování ochrany, Site Recovery replikovat kompletní data ze sekundární oblasti do primární oblasti?
To závisí na situace. Například pokud zdrojové oblasti virtuální počítač existuje, jsou synchronizovány pouze změny mezi zdrojový disk a cílový disk. Site Recovery počítá srovnáním disky rozdíly a pak ji přesune data. Tento proces obvykle trvá několik hodin. Další informace o tom, co se stane během opětovného nastavování ochrany najdete v tématu [zpětné replikace převzetí služeb při selhání virtuálních počítačů Azure do primární oblasti]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Kolik času udělá provést navrácení služeb po obnovení?
Množství času pro navrácení služeb po obnovení po opětovného nastavování ochrany, je obvykle podobný čas potřebný pro převzetí služeb při selhání z primární oblasti do sekundární oblasti. 

## <a name="security"></a>Zabezpečení
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Posílají se replikační data do služby Site Recovery?
Ne, Site Recovery nepodporuje nezachycuje replikovaná data a nemá žádné informace o co běží na virtuálních počítačích. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.  
Site Recovery je ISO 27001: 2013, 27018, HIPAA, DPA certifikace a probíhá SOC2 a FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>Šifruje Site Recovery replikaci?
Ano, jak šifrování během přenosu a [šifrování v Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) jsou podporovány.

## <a name="next-steps"></a>Další postup
* [Kontrola](azure-to-azure-support-matrix.md) požadavky na podporu.
* [Nastavit](azure-to-azure-tutorial-enable-replication.md) replikací z Azure do Azure.
