---
title: Běžné dotazy týkající se zotavení po havárii virtuálních počítačů Azure pomocí Azure Site Recovery
description: Tento článek obsahuje odpovědi na běžné dotazy týkající se zotavení po havárii virtuálních počítačů Azure při použití Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 7bc8427a51a9931ca82155232569767f12a8e266
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87534018"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Běžné dotazy: Zotavení po havárii Azure do Azure

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se zotavení po havárii virtuálních počítačů Azure do jiné oblasti Azure, pokud používáte [Azure Site Recovery](site-recovery-overview.md).

## <a name="general"></a>Obecné

### <a name="how-is-site-recovery-priced"></a>Jak se Site Recovery cena?

Projděte si [Azure Site Recovery ceny pro virtuální počítače](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Jak funguje úroveň Free pro Azure Site Recovery?

Každá instance, která je chráněná pomocí Azure Site Recovery, je pro prvních 31 dní ochrany zadarmo. Po uplynutí této doby platí, že ochrana každé instance je sazba za [Azure Site Recovery ceny pro Azure Virtual Machines](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Během prvních 31 dnů se mi účtují nějaké další poplatky za Azure?

Ano. I když je během prvních 31 dní chráněné instance Azure Site Recovery volná, můžou se vám účtovat poplatky za Azure Storage, transakce úložiště a datové přenosy. U obnoveného virtuálního počítače můžou být účtovány i poplatky za výpočetní výkon Azure. Získejte podrobné informace o cenách za [Azure Site Recovery ceny](https://azure.microsoft.com/pricing/details/site-recovery).

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Jaké jsou osvědčené postupy pro zotavení po havárii Azure Virtual Machines?

1. [Principy architektury Azure-to-Azure](azure-to-azure-architecture.md)
1. [Zkontrolujte podporované a nepodporované konfigurace](azure-to-azure-support-matrix.md)
1. [Nastavení zotavení po havárii pro virtuální počítače Azure](azure-to-azure-how-to-enable-replication.md)
1. [Spuštění testovacího převzetí služeb při selhání](azure-to-azure-tutorial-dr-drill.md)
1. [Převzetí služeb při selhání a navrácení služeb po obnovení do primární oblasti](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>Jak je v cílové oblasti zajištěna kapacita?

Tým Site Recovery týmu a Azure Capacity Management plánuje dostatečnou kapacitu infrastruktury. Když spustíte převzetí služeb při selhání, týmy také pomůžou zajistit, aby se instance virtuálních počítačů, které jsou chráněné nástrojem Site Recovery, nasadily do cílové oblasti.

## <a name="replication"></a>Replikace

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Můžu replikovat virtuální počítače povolené prostřednictvím služby Azure Disk Encryption?

Ano. Site Recovery podporuje zotavení po havárii virtuálních počítačů, které mají povolený Azure Disk Encryption. Když povolíte replikaci, Azure zkopíruje všechny požadované šifrovací klíče disku a tajné klíče ze zdrojové oblasti do cílové oblasti v kontextu uživatele. Pokud nemáte příslušná oprávnění, správce zabezpečení může ke kopírování klíčů a tajných kódů použít skript.

- Site Recovery podporuje Azure Disk Encryption pro virtuální počítače Azure s Windows.
- Site Recovery podporuje Azure Disk Encryption verze 0,1, která má schéma, které vyžaduje Azure Active Directory (Azure AD). Site Recovery také podporuje verzi 1,1, která nevyžaduje službu Azure AD. [Přečtěte si další informace o schématu rozšíření pro Azure Disk Encryption](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema).
  - Pro Azure Disk Encryption verze 1,1 je nutné použít virtuální počítače s Windows se službou Managed disks.
  - [Přečtěte si další informace](azure-to-azure-how-to-enable-replication-ade-vms.md) o povolení replikace pro šifrované virtuální počítače.

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>Můžu vybrat účet Automation z jiné skupiny prostředků?

To se v tuto chvíli nepodporuje přes portál, ale můžete si vybrat účet Automation z jiné skupiny prostředků přes PowerShell.

### <a name="after-specifying-an-automation-account-that-is-in-a-different-resource-group-than-the-vault-am-i-permitted-to-delete-the-runbook-if-there-is-no-other-vault-to-specify"></a>Po zadání účtu Automation, který je v jiné skupině prostředků než trezor, mám povolený pokus odstranit sadu Runbook, pokud neexistuje jiný trezor pro zadání?

Vytvořená vlastní sada Runbook je nástrojem a je bezpečné ji odstranit, pokud to již není nutné.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Můžu virtuální počítače replikovat do jiného předplatného?

Ano, virtuální počítače Azure můžete replikovat do jiného předplatného v rámci stejného tenanta služby Azure AD.

V době replikace nakonfigurujte v [rámci předplatných](https://azure.microsoft.com/blog/cross-subscription-dr) zotavení po havárii a vyberte jiné předplatné.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Můžu replikovat virtuální počítače Azure připojené k zóně do jiné oblasti?

Ano, [virtuální počítače připnuté do zóny můžete replikovat](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) do jiné oblasti.

### <a name="can-i-replicate-vms-in-a-region-that-has-zones-from-non-zone-to-zonal-configuration"></a>Můžu replikovat virtuální počítače v oblasti, která má zóny z nezóny do konfigurace oblastí?

Ne, tato podpora není dnes podporována. Alternativním řešením je, že virtuální počítač můžete replikovat pomocí ASR do konfigurace oblasti v jiné oblasti a pak zakázat replikaci. V dalším kroku znovu povolte replikaci z této oblasti do původní oblasti a vyberte konfiguraci oblasti pro převzetí služeb při selhání.

### <a name="can-i-exclude-disks"></a>Můžu vyloučit disky?

Ano, disky můžete v době ochrany vyloučit pomocí prostředí PowerShell. Další informace najdete v tématu [postup vyloučení disků z replikace](azure-to-azure-exclude-disks.md).

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Můžu do replikovaných virtuálních počítačů přidávat nové disky a pro ně povolit replikaci?

Ano, přidávání nových disků do replikovaných virtuálních počítačů a povolení replikace pro virtuální počítače Azure se spravovanými disky podporuje. Když přidáte nový disk do virtuálního počítače Azure, který je povolený pro replikaci, zobrazí se stav replikace pro virtuální počítač upozornění. Toto upozornění uvádí, že jeden nebo více disků na virtuálním počítači je k dispozici pro ochranu. Replikaci můžete povolit pro přidané disky.

- Pokud povolíte ochranu pro přidané disky, bude upozornění po počáteční replikaci zmizí.
- Pokud pro disk nepovolíte replikaci, můžete upozornění zavřít.
- Pokud dojde k převzetí služeb při selhání virtuálního počítače s povoleným diskem a replikací, existují body replikace. Body replikace budou zobrazovat disky, které jsou k dispozici pro obnovení.

Řekněme například, že virtuální počítač má jeden disk a vy přidáte nový. Může existovat bod replikace, který byl vytvořen před přidáním disku. Tento bod replikace zobrazí, že se skládá z "1 z 2 disků".

Site Recovery nepodporuje "Hot Remove" disku z replikovaného virtuálního počítače. Pokud odeberete disk virtuálního počítače, musíte zakázat a znovu povolit replikaci virtuálního počítače.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak často je možné replikovat do Azure?

Replikace je nepřetržitá, když provádíte replikaci virtuálních počítačů Azure do jiné oblasti Azure. Další informace najdete v tématu [Architektura replikace z Azure do Azure](./azure-to-azure-architecture.md#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>Můžu replikovat virtuální počítače v nějaké oblasti? Potřebuji tuto funkci k migraci virtuálních počítačů.

K replikaci virtuálních počítačů v rámci jedné oblasti nemůžete použít řešení Azure-to-Azure disk Recovery.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Můžu replikovat instance virtuálních počítačů do jakékoli oblasti Azure?

Pomocí Site Recovery můžete replikovat a obnovovat virtuální počítače mezi dvěma oblastmi v rámci stejného geografického clusteru. Geografické clustery jsou definovány s latencí a suverenitou dat. Další informace najdete v tématu [matice podpory Site Recovery oblasti](./azure-to-azure-support-matrix.md#region-support).

### <a name="does-site-recovery-require-internet-connectivity"></a>Vyžaduje Site Recovery připojení k Internetu?

Ne, Site Recovery nevyžaduje připojení k Internetu. Ale vyžaduje přístup k adresám URL Site Recovery a rozsahům IP adres, jak je uvedeno v článku [sítě v zotavení po havárii virtuálních počítačů Azure](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>Můžu replikovat aplikaci, která má samostatnou skupinu prostředků pro samostatné úrovně?

Ano, můžete replikovat aplikaci a zachovat konfiguraci zotavení po havárii v samostatné skupině prostředků.

Pokud má vaše aplikace například aplikaci, databázi a web jednotlivých vrstev v samostatné skupině prostředků, je nutné vybrat [Průvodce replikací](./azure-to-azure-how-to-enable-replication.md#enable-replication) třikrát pro ochranu všech vrstev. Site Recovery budou tyto tři vrstvy replikovat do tří různých skupin prostředků.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>Můžu přesouvat účty úložiště mezi skupinami prostředků?

Ne, jedná se o nepodporovaný scénář. Pokud ale omylem přesunete účty úložiště do jiné skupiny prostředků a odstraníte původní skupinu prostředků, můžete vytvořit novou skupinu prostředků se stejným názvem jako starou skupinu prostředků a potom účet úložiště přesunout do této skupiny prostředků.

## <a name="replication-policy"></a>Zásady replikace

### <a name="what-is-a-replication-policy"></a>Co je zásada replikace?

Zásady replikace definují nastavení pro historii uchovávání bodů obnovení. Zásady také definují četnost snímků konzistentních vzhledem k aplikacím. Ve výchozím nastavení Azure Site Recovery vytvoří novou zásadu replikace s výchozími nastaveními:

- 24 hodin pro historii uchovávání bodů obnovení.
- 4 hodiny pro četnost snímků konzistentních vzhledem k aplikacím.

[Přečtěte si další informace o nastavení replikace](./azure-to-azure-tutorial-enable-replication.md#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Co je bod obnovení konzistentní vzhledem k selháním?

Bod obnovení konzistentní s chybou obsahuje data na disku, jako kdyby jste ze serveru během snímku vyžádali napájecí kabel. Bod obnovení konzistentní vzhledem k chybě neobsahuje cokoli, co bylo v paměti při pořízení snímku.

V současné době se většina aplikací může zotavit i z snímků konzistentních vzhledem k chybě. Bod obnovení konzistentní vzhledem k selháním je obvykle dostačující pro operační systémy bez databáze a aplikace jako souborové servery, servery DHCP a tiskové servery.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Jaká je četnost generování bodu obnovení konzistentního vzhledem k chybě?

Site Recovery vytvoří bod obnovení konzistentní vzhledem k selháním každých 5 minut.

### <a name="what-is-an-application-consistent-recovery-point"></a>Co je bod obnovení konzistentní vzhledem k aplikacím?

Body obnovení konzistentní vzhledem k aplikacím se vytvářejí z snímků konzistentních vzhledem k aplikacím. Body obnovení konzistentní vzhledem k aplikacím zachycují stejná data jako snímky konzistentní s chybou a zároveň zachytí data v paměti a všechny probíhající transakce.

Vzhledem k tomu, že se jedná o další obsah, jsou nejdůležitější snímky konzistentní vzhledem k aplikacím a trvat nejdéle. Pro databázové operační systémy a aplikace, jako je například SQL Server, doporučujeme body obnovení konzistentní vzhledem k aplikacím.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Jaký je dopad bodů obnovení konzistentních vzhledem k aplikacím na výkon aplikace?

Body obnovení konzistentní vzhledem k aplikacím zachytí všechna data v paměti a v procesu. Vzhledem k tomu, že body obnovení zachytí tato data, vyžadují rozhraní, jako služba Stínová kopie svazku ve Windows, aby bylo možné aplikaci neuvést. Pokud je proces zachytávání častý, může to mít vliv na výkon, pokud je zatížení už zaneprázdněné. Nedoporučujeme používat pro úlohy mimo databázi nízkou frekvenci pro body obnovení konzistentní vzhledem k aplikacím. I pro databázová zatížení je k dispozici 1 hodina.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Jaká je minimální frekvence generování bodu obnovení konzistentního vzhledem k aplikacím?

Site Recovery může vytvořit bod obnovení konzistentní vzhledem k aplikacím s minimální frekvencí 1 hodina.

### <a name="how-are-recovery-points-generated-and-saved"></a>Jak se vygenerovaly a ukládají body obnovení?

Pokud chcete pochopit, jak Site Recovery generuje body obnovení, Podívejme se na příklad zásady replikace. Tato zásada replikace má bod obnovení s časovým intervalem pro uchovávání v 24hodinovém formátu a snímkem frekvence konzistentního vzhledem k aplikacím 1 hodina.

Site Recovery vytvoří bod obnovení konzistentní vzhledem k selháním každých 5 minut. Tuto frekvenci nemůžete změnit. Za poslední hodinu můžete vybrat z 12 bodů konzistentních z havárie a 1 bodu konzistentního vzhledem k aplikacím. V průběhu času Site Recovery vyřadí všechny body obnovení za poslední hodinu a uloží pouze 1 bod obnovení za hodinu.

Příklad ukazuje následující snímek obrazovky. Na snímku obrazovky:

- Během poslední hodiny existují body obnovení s frekvencí 5 minut.
- Po uplynutí poslední hodiny zachovává Site Recovery jenom 1 bod obnovení.

   ![Seznam generovaných bodů obnovení](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Jak daleko zpátky můžu obnovit?

Nejstarší bod obnovení, který můžete použít, je 72 hodin.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Mám zásady replikace za 24 hodin. Co se stane, když problém zabrání Site Recovery generování bodů obnovení po dobu delší než 24 hodin? Ztratí se předchozí body obnovení?

Ne, Site Recovery bude uchovávat všechny předchozí body obnovení. V závislosti na okně pro uchování bodů obnovení Site Recovery nahradí nejstarší bod pouze v případě, že generuje nové body. Kvůli problému Site Recovery nemůže vygenerovat žádné nové body obnovení. Dokud nebudou k dispozici nové body obnovení, všechny staré body zůstanou po dosažení okna uchování.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Jak se po replikaci na virtuálním počítači povolí replikace, jak změním zásady replikace?

Přejít na **Site Recovery trezor**  >  **Site Recovery**  >  **Zásady replikace**infrastruktury. Vyberte zásadu, kterou chcete upravit, a uložte změny. Všechny změny se projeví i u všech stávajících replikací.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Má všechny body obnovení úplnou kopii virtuálního počítače nebo rozdílu?

První vygenerovaný bod obnovení má úplnou kopii. Všechny úspěšné body obnovení mají rozdílové změny.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Zvyšuje doba uchování bodů obnovení náklady na úložiště?

Ano, Pokud zvýšíte dobu uchovávání dat z 24 hodin na 72 hodin, Site Recovery bude body obnovení ukládat po dobu dalších 48 hodin. Přidaný čas účtuje poplatky za úložiště. Například jeden bod obnovení může mít rozdílové změny 10 GB s $0,16 za GB za měsíc. Další poplatky by byly $1,60 × 48 za měsíc.

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>Můžu povolit replikaci s konzistencí aplikací na serverech se systémem Linux?

Ano. Azure Site Recovery pro operační systém Linux podporuje vlastní skripty aplikace pro konzistenci aplikací. Vlastní skript s předchozími a post-možnostmi bude používat agent Azure Site Recovery mobility během konzistence aplikací. [Další informace](./site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

## <a name="multi-vm-consistency"></a>Konzistence s více virtuálními počítači

### <a name="what-is-multi-vm-consistency"></a>Co je konzistence více virtuálních počítačů?

Konzistence s více virtuálními počítači zajišťuje, že bod obnovení je konzistentní napříč všemi replikovanými virtuálními počítači.

Site Recovery poskytuje možnost **konzistence s více virtuálními počítači** , která vytvoří replikační skupinu všech počítačů.

Při převzetí služeb při selhání virtuálních počítačů budou mít sdílené body obnovení konzistentní se selháním a konzistentní vzhledem k aplikacím.

Projděte si kurz a [Povolte konzistenci pro více virtuálních počítačů](./azure-to-azure-tutorial-enable-replication.md#enable-replication-for-a-vm).

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Můžu převzít služby při selhání jediného virtuálního počítače ve skupině replikace konzistence pro víc virtuálních počítačů?

Když vyberete možnost **konzistence s více virtuálními počítači** , zjistíte, že aplikace bude závislá na všech virtuálních počítačích v rámci skupiny. Jedno převzetí služeb virtuálního počítače není povolené.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Kolik virtuálních počítačů je možné replikovat jako součást replikační skupiny konzistence s více virtuálními počítači?

Do replikační skupiny můžete replikovat 16 virtuálních počítačů dohromady.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Kdy mám povolit konzistenci pro více virtuálních počítačů?

Vzhledem k tomu, že konzistence s více virtuálními počítači je náročné na procesor, může to mít vliv na výkon úloh Používejte konzistenci s více virtuálními počítači jenom v případě, že počítače používají stejnou úlohu a potřebujete konzistenci napříč několika počítači. Pokud máte například dvě instance SQL Server a dva webové servery v aplikaci, měli byste mít konzistenci s více virtuálními počítači pouze pro instance SQL Server.

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>Můžete přidat už replikující virtuální počítač do replikační skupiny?
Virtuální počítač můžete přidat do nové replikační skupiny během povolování replikace. Virtuální počítač můžete také přidat do existující replikační skupiny při povolování replikace. Nemůžete ale přidat už repliku virtuálního počítače do nové replikační skupiny nebo existující replikační skupiny.
 
## <a name="failover"></a>Převzetí služeb při selhání


### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Jak je zajištěna kapacita v cílové oblasti pro virtuální počítače Azure?

Tým Site Recovery týmu a Azure Capacity Management plánuje dostatečnou kapacitu infrastruktury. Když spustíte převzetí služeb při selhání, týmy také pomůžou zajistit, aby se instance virtuálních počítačů, které jsou chráněné nástrojem Site Recovery, nasadily do cílové oblasti.

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?

Převzetí služeb při selhání není automatické. Převzetí služeb při selhání můžete spustit jediným kliknutím na portálu nebo můžete pomocí [PowerShellu](azure-to-azure-powershell.md) aktivovat převzetí služeb při selhání.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>Můžu po převzetí služeb při selhání zachovat veřejnou IP adresu?

Po převzetí služeb při selhání nemůžete zachovat veřejnou IP adresu provozní aplikace.

Když v rámci procesu převzetí služeb při selhání zavedete úlohu, musíte k ní přiřadit prostředek veřejné IP adresy Azure. Prostředek veřejné IP adresy Azure musí být k dispozici v cílové oblasti. Prostředek veřejné IP adresy Azure můžete přiřadit ručně nebo ho můžete automatizovat pomocí plánu obnovení. Přečtěte si, jak [nastavit veřejné IP adresy po převzetí služeb při selhání](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>Můžu během převzetí služeb při selhání zachovat soukromou IP adresu?

Ano, můžete zachovat privátní IP adresu. Ve výchozím nastavení se při povolování zotavení po havárii pro virtuální počítače Azure Site Recovery vytvoří cílové prostředky na základě nastavení zdrojového prostředku. U Azure Virtual Machines nakonfigurovaných pomocí statických IP adres se Site Recovery pokusí zřídit stejnou IP adresu cílového virtuálního počítače, pokud se nepoužívá.
Seznamte se [s udržováním IP adres během převzetí služeb při selhání](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>Proč je po převzetí služeb při selhání Server přiřazená nová IP adresa?

Site Recovery se pokusí zadat IP adresu v době převzetí služeb při selhání. Pokud se tato adresa převezme v jiném virtuálním počítači, Site Recovery nastaví další dostupnou IP adresu jako cíl.

Přečtěte si další informace o [Nastavení mapování sítě a adresování IP pro virtuální sítě](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Co jsou **nejnovější body obnovení (nejnižší RPO)** ?

Možnost **nejnovější (nejnižší RPO)** nejprve zpracuje všechna data, která byla odeslána do Site Recovery. Jakmile služba zpracuje data, vytvoří bod obnovení pro každý virtuální počítač, než dojde k převzetí služeb virtuálního počítače při selhání. Tato možnost poskytuje nejnižší cíl bodu obnovení (RPO). Virtuální počítač vytvořený po převzetí služeb při selhání obsahuje všechna data, která se replikují do Site Recovery od spuštění převzetí služeb při selhání.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Mají dopad na RTO pro převzetí služeb při selhání **nejnovější (nejnižší RPO)** body obnovení?

Ano. Site Recovery zpracovává všechna nevyřízená data před převzetím služeb při selhání, takže tato možnost má vyšší cíl času obnovení (RTO) ve srovnání s jinými možnostmi.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Co znamená **poslední zpracovaná** možnost v bodech obnovení?

Možnost **nejnovější zpracovaná** se u všech virtuálních počítačů v plánu převezme do nejnovějšího bodu obnovení, který Site Recovery zpracoval. Chcete-li zobrazit nejnovější bod obnovení pro konkrétní virtuální počítač, zkontrolujte v nastavení virtuálního počítače **nejnovější body obnovení** . Tato možnost poskytuje nízkou RTO, protože nestráví zpracováním nezpracovaných dat žádného času.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Co se stane, když v naší primární oblasti dojde k neočekávanému výpadku?

Po výpadku můžete aktivovat převzetí služeb při selhání. Site Recovery nepotřebuje k převzetí služeb při selhání připojení z primární oblasti.

### <a name="what-is-an-rto-of-a-vm-failover"></a>Co je RTO pro převzetí služeb virtuálního počítače při selhání?

Site Recovery [RTO SLA o 2 hodiny](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Ve většině případů ale Site Recovery převzetí služeb při selhání virtuálních počítačů během několika minut. RTO můžete vypočítat tak, že na úlohy převzetí služeb při selhání zobrazíte čas potřebný k uvedení virtuálního počítače. Informace k RTO plánu obnovení najdete v další části.

## <a name="recovery-plans"></a>Plány obnovení

### <a name="what-is-a-recovery-plan"></a>Co je plán obnovení?

Plán obnovení v Site Recovery orchestruje obnovení virtuálních počítačů v převzetí služeb při selhání. Pomáhá zajistit konzistentní obnovení, opakovatelnost a automatizované obnovení. Plán obnovení řeší následující požadavky:

- Definování skupiny virtuálních počítačů, u kterých dojde k převzetí služeb při selhání
- Definování závislostí mezi virtuálními počítači, aby se aplikace přesně vystavila
- Automatizace obnovení spolu s vlastními ručními akcemi pro zajištění jiných úloh než převzetí služeb při selhání virtuálních počítačů

Přečtěte si další informace [o vytváření plánů obnovení](site-recovery-create-recovery-plans.md).

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Jak se dosáhne sekvencování v plánu obnovení?

V plánu obnovení můžete vytvořit více skupin, abyste mohli sekvencování dosáhnout. U každé skupiny dojde v jednom okamžiku k převzetí služeb při selhání. Virtuální počítače, které jsou součástí stejné skupiny, se převezmou společně a další skupina. Informace o modelování aplikace pomocí plánu obnovení najdete v tématu [o plánech obnovení](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Jak můžu najít RTO plánu obnovení?

Pokud chcete zkontrolovat RTO plánu obnovení, proveďte test převzetí služeb při selhání pro plán obnovení a přejít na **Site Recovery úlohy**.
V následujícím příkladu se podívejte na **SAPTestRecoveryPlan**úlohy. Tato úloha trvala 8 minut a 59 sekund pro převzetí služeb při selhání všemi virtuálními počítači a zadané akce.

![Seznam úloh Site Recovery](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Můžu do plánu obnovení přidat Runbooky Automation?

Ano, do svého plánu obnovení můžete integrovat Azure Automation Runbooky. Přečtěte si další informace o [přidávání sad runbook Azure Automation](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Znovu naochrana a navrácení služeb po obnovení

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Převzetí služeb při selhání z primární oblasti do oblasti zotavení po havárii. Jsou virtuální počítače v oblasti DR chráněné automaticky?

Ne. Při [převzetí služeb při selhání](./azure-to-azure-tutorial-failover-failback.md) virtuálních počítačů Azure z jedné oblasti do druhé se virtuální počítače spustí v oblasti zotavení po havárii v nechráněném stavu. K navrácení služeb virtuálních počítačů do primární oblasti je potřeba znovu nastavit [ochranu](./azure-to-azure-how-to-reprotect.md) virtuálních počítačů v sekundární oblasti.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>V době ochrany Site Recovery replikuje úplná data ze sekundární oblasti do primární oblasti?

Záleží na situaci. Pokud virtuální počítač oblasti zdroje existuje, synchronizují se jenom změny mezi zdrojovým diskem a cílovým diskem. Site Recovery vypočítá rozdíly porovnáním disků a poté převede data. Tento proces obvykle trvá několik hodin. Další informace o tom, co se děje během opětovné ochrany, najdete v tématu o opětovném zapnutí [ochrany instancí virtuálních počítačů Azure v primární oblasti](./azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Jak dlouho trvá navrácení služeb po obnovení?

Po obnovení bude navrácení služeb po obnovení trvat přibližně stejnou dobu, než převezme služby při selhání z primární oblasti do sekundární oblasti.

## <a name="capacity"></a><a name="capacity"></a>Kapacita

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Jak je zajištěna kapacita v cílové oblasti pro virtuální počítače Azure?

Tým Site Recovery týmu a Azure Capacity Management plánuje dostatečnou kapacitu infrastruktury. Když spustíte převzetí služeb při selhání, týmy také pomůžou zajistit, aby se instance virtuálních počítačů, které jsou chráněné nástrojem Site Recovery, nasadily do cílové oblasti.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Pracuje Site Recovery s rezervovanými instancemi?

Ano, můžete si koupit [rezervované virtuální počítače Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/) v oblasti zotavení po havárii a Site Recovery tyto operace převzetí služeb při selhání budou používat. Není nutná žádná další konfigurace.

## <a name="security"></a>Zabezpečení

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Posílají se replikační data do služby Site Recovery?

Ne, Site Recovery nezachycují replikovaná data a nemá žádné informace o tom, co běží na vašich virtuálních počítačích. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.

Site Recovery je ISO 27001:2013, 27018, HIPAA a DPA Certified. Služba se přesměruje do SOC2 a hodnocení FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>Šifruje Site Recovery replikaci?

Ano, podporuje se jak šifrování, tak [šifrování v klidovém režimu v Azure](../storage/common/storage-service-encryption.md) .

## <a name="next-steps"></a>Další kroky

- [Projděte si požadavky na podporu pro Azure do Azure](azure-to-azure-support-matrix.md).
- [Nastavte replikaci z Azure do Azure](azure-to-azure-tutorial-enable-replication.md).
- Pokud máte dotazy i po přečtení tohoto článku, pošlete je na [stránce s dotazem na Microsoft Q&pro Azure Recovery Services](/answers/topics/azure-site-recovery.html).
