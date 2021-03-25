---
title: Běžné dotazy týkající se zotavení po havárii virtuálních počítačů Azure pomocí Azure Site Recovery
description: Tento článek obsahuje odpovědi na běžné dotazy týkající se zotavení po havárii virtuálních počítačů Azure při použití Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 11/03/2019
ms.topic: conceptual
ms.openlocfilehash: 5309fd60640c45ade42bab4c5727cf1f0a8d9d70
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025471"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Běžné dotazy: Zotavení po havárii Azure do Azure

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se zotavení po havárii virtuálních počítačů Azure do jiné oblasti Azure pomocí služby [Azure Site Recovery](site-recovery-overview.md) .

## <a name="general"></a>Obecné

### <a name="how-is-site-recovery-priced"></a>Jak se Site Recovery cena?

Přečtěte si informace o [nákladech](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) na zotavení po havárii virtuálních počítačů Azure.

### <a name="how-does-the-free-tier-work"></a>Jak funguje úroveň Free?

Každá instance, která je chráněná pomocí Site Recovery, je po dobu prvních 31 dní ochrany zadarmo. Po uplynutí této doby se ochrana pro každou instanci vyhodnotí v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/site-recovery/). Náklady můžete odhadnout pomocí [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/?service=site-recovery).

### <a name="do-i-incur-other-azure-charges-in-the-first-31-days"></a>Účtují se za prvních 31 dnů další poplatky za Azure?

Ano. I když je během prvních 31 dní chráněné instance Azure Site Recovery volná, můžou se vám účtovat poplatky za Azure Storage, transakce úložiště a datové přenosy. U obnoveného virtuálního počítače můžou být účtovány i poplatky za výpočetní prostředky Azure. G

### <a name="how-do-i-get-started-with-azure-vm-disaster-recovery"></a>Návody začít s zotavením po havárii virtuálního počítače Azure?

1. [Pochopení](azure-to-azure-architecture.md) architektury zotavení po havárii virtuálního počítače Azure
2. [Zkontrolujte](azure-to-azure-support-matrix.md) požadavky na podporu.
3. [Nastavte](azure-to-azure-how-to-enable-replication.md) zotavení po havárii pro virtuální počítače Azure.
4. [Spuštění postupu zotavení po havárii](azure-to-azure-tutorial-dr-drill.md) s testovacím převzetím služeb při selhání.
5. [Spusťte úplné převzetí služeb při selhání](azure-to-azure-tutorial-failover-failback.md) do sekundární oblasti Azure.
6. [Navrácení služeb po obnovení](azure-to-azure-tutorial-failback.md) ze sekundární oblasti do primární oblasti.

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Jak zajistíme kapacitu v cílové oblasti?

Tým Site Recovery a tým pro správu kapacity Azure naplánují dostatečnou kapacitu infrastruktury. Když spustíte převzetí služeb při selhání, týmy také pomůžou zajistit, aby byly instance virtuálních počítačů chráněné Site Recovery nasazovat do cílové oblasti.

## <a name="replication"></a>Replikace

### <a name="can-i-replicate-vms-with-disk-encryption"></a>Můžu replikovat virtuální počítače se šifrováním disku?

Ano. Site Recovery podporuje zotavení po havárii virtuálních počítačů s povoleným Azure Disk Encryption (ADE). Když povolíte replikaci, Azure zkopíruje všechny požadované šifrovací klíče disku a tajné klíče ze zdrojové oblasti do cílové oblasti v kontextu uživatele. Pokud nemáte požadovaná oprávnění, správce zabezpečení může ke kopírování klíčů a tajných kódů použít skript.

- Site Recovery podporuje ADE pro virtuální počítače Azure s Windows.
- Site Recovery podporuje:
    - ADE verze 0,1, která má schéma, které vyžaduje Azure Active Directory (Azure AD).
    - ADE verze 1,1, která nevyžaduje Azure AD Pro verzi 1,1 musí mít virtuální počítače s Windows Azure spravované disky.
    - [Další informace](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema). o schématech rozšíření.

[Přečtěte si další informace](azure-to-azure-how-to-enable-replication-ade-vms.md) o povolení replikace pro šifrované virtuální počítače.

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>Můžu vybrat účet Automation z jiné skupiny prostředků?

Když povolíte Site Recovery ke správě aktualizací pro rozšíření služby mobility spuštěné na replikovaných virtuálních počítačích Azure, nasadí globální Runbook (používaný službami Azure) prostřednictvím účtu Azure Automation. Můžete použít účet Automation, který Site Recovery vytvoří, nebo vybrat, jestli se má použít existující účet Automation. 

V současné době můžete na portálu vybrat pouze účet Automation ve stejné skupině prostředků jako trezor. Můžete vybrat účet Automation z jiné skupiny prostředků pomocí PowerShellu. [Další informace](azure-to-azure-autoupdate.md#enable-automatic-updates).

### <a name="if-i-use-a-customer-automation-account-thats-not-in-the-vault-resource-group-can-i-delete-the-default-runbook"></a>Pokud používám účet služby Automation pro zákazníky, který není ve skupině prostředků trezoru, můžu výchozí Runbook odstranit?

Ano, pokud ho nepotřebujete, můžete ho odstranit. 

### <a name="can-i-replicate-vms-to-another-subscription"></a>Můžu virtuální počítače replikovat do jiného předplatného?

Ano, virtuální počítače Azure můžete replikovat do libovolného předplatného v rámci stejného tenanta služby Azure AD. Když pro virtuální počítače povolíte zotavení po havárii, zobrazí se ve výchozím nastavení cílové předplatné, které je ve zdrojovém virtuálním počítači. Můžete upravit cílové předplatné a další nastavení (například skupinu prostředků a virtuální síť) se naplní automaticky z vybraného předplatného.

### <a name="can-i-replicate-vms-in-an-availability-zone-to-another-region"></a>Můžu replikovat virtuální počítače v zóně dostupnosti do jiné oblasti?

Ano, virtuální počítače můžete replikovat v zónách dostupnosti do jiné oblasti Azure. 

### <a name="can-i-replicate-non-zone-vms-to-a-zone-within-the-same-region"></a>Můžu replikovat virtuální počítače, které nejsou zónou, do zóny ve stejné oblasti? 

Tato podpora není na portálu podporována. K tomu můžete použít REST API nebo PowerShell.

### <a name="can-i-replicate-zoned-vms-to-a-different-zone-in-the-same-region"></a>Je možné replikovat virtuální počítače v zóně do jiné zóny ve stejné oblasti?

Podpora je omezená na několik oblastí. [Další informace](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

### <a name="can-i-exclude-disks-from-replication"></a>Můžu vyloučit disky z replikace?

Ano, disky můžete vyloučit při nastavování replikace pomocí PowerShellu. [Další informace](azure-to-azure-exclude-disks.md).

### <a name="can-i-replicate-new-disks-added-to-replicated-vms"></a>Můžu replikovat nové disky přidané do replikovaných virtuálních počítačů?

U replikovaných virtuálních počítačů se spravovanými disky můžete přidat nové disky a povolit pro ně replikaci. Když přidáte nový disk, replikovaný virtuální počítač zobrazí zprávu s upozorněním, že jeden nebo více disků na virtuálním počítači je k dispozici pro ochranu. 

- Pokud povolíte replikaci pro přidané disky, upozornění zmizí po počáteční replikaci.
- Pokud nechcete pro disk povolit replikaci, můžete upozornění zavřít.
- Pokud při selhání dojde k převzetí služeb virtuálního počítače pomocí přidaných disků, body replikace zobrazí disky dostupné k obnovení. Pokud například přidáte druhý disk k virtuálnímu počítači s jedním diskem, zobrazí se bod replikace, který byl vytvořen před přidáním, jako "1 z 2 disků".

Site Recovery nepodporuje "Hot Remove" disků z replikovaného virtuálního počítače. Pokud odeberete disk virtuálního počítače, budete muset zakázat a znovu povolit replikaci virtuálního počítače.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak často je možné replikovat do Azure?

Replikace je nepřetržitá při replikaci virtuálních počítačů Azure do jiné oblasti Azure. [Přečtěte si další informace](./azure-to-azure-architecture.md#replication-process) o tom, jak funguje replikace.

### <a name="can-i-replicate-virtual-machines-within-a-region"></a>Můžu replikovat virtuální počítače v nějaké oblasti? 

K replikaci disků v rámci oblasti nemůžete použít Site Recovery.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Můžu replikovat instance virtuálních počítačů do jakékoli oblasti Azure?

Virtuální počítače můžete replikovat a obnovovat mezi dvěma oblastmi v rámci stejného geografického clusteru. Geografické clustery jsou definovány s latencí a suverenitou dat. [Přečtěte si další informace](./azure-to-azure-support-matrix.md#region-support) o podpoře oblastí.

### <a name="does-site-recovery-need-internet-connectivity"></a>Vyžaduje Site Recovery připojení k Internetu?

Ne, ale virtuální počítače potřebují přístup k Site Recovery adres URL a rozsahy IP adres. [Další informace](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-tiered-across-resource-groups"></a>Můžu replikovat aplikaci vrstvenou napříč skupinami prostředků?

Ano, aplikaci můžete replikovat a v samostatné skupině prostředků zachovat konfiguraci zotavení po havárii.

Pokud například aplikace obsahuje tři úrovně (aplikace/databáze/Web) v různých skupinách prostředků, musíte replikaci třikrát povolit, aby se chránily všechny úrovně. Site Recovery replikují tři úrovně do tří různých skupin prostředků.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>Můžu přesouvat účty úložiště mezi skupinami prostředků?

Ne, to není podporováno. Pokud omylem přesunete účty úložiště do jiné skupiny prostředků a odstraníte původní skupinu prostředků, můžete vytvořit novou skupinu prostředků se stejným názvem, jako má stará skupina prostředků, a potom účet úložiště přesunout do této skupiny prostředků.

## <a name="replication-policy"></a>Zásady replikace

### <a name="what-is-a-replication-policy"></a>Co je zásada replikace?

Zásady replikace definují historii uchovávání bodů obnovení a četnost snímků konzistentních vzhledem k aplikacím.  Site Recovery vytvoří výchozí zásadu replikace následujícím způsobem:

- Zachovat body obnovení po dobu 24 hodin.
- Vezměte snímky konzistentní vzhledem k aplikacím každé čtyři hodiny.

[Přečtěte si další informace](azure-to-azure-how-to-enable-replication.md#customize-target-resources) o nastavení replikace.

### <a name="whats-a-crash-consistent-recovery-point"></a>Co je bod obnovení konzistentní vzhledem k selháním?

Bod obnovení konzistentní vzhledem k chybám obsahuje data na disku, jako kdyby jste ze serveru během snímku vyžádali napájecí kabel. Neobsahuje vše, co bylo v paměti při pořízení snímku.

V současné době se většina aplikací může zotavit i z snímků konzistentních vzhledem k chybě. Bod obnovení konzistentní vzhledem k selháním je obvykle dostatečný pro operační systémy, které nejsou databázemi, a aplikace, jako jsou souborové servery, servery DHCP a tiskové servery.

Site Recovery automaticky vytvoří bod obnovení konzistentní vzhledem k selháním každých pět minut.

### <a name="whats-an-application-consistent-recovery-point"></a>Co je bod obnovení konzistentní vzhledem k aplikacím?

Body obnovení konzistentní vzhledem k aplikacím se vytvářejí z snímků konzistentních vzhledem k aplikacím. Zachycují stejná data jako snímky konzistentní vzhledem k selháním a navíc zachytí data v paměti a všechny probíhající transakce.

Vzhledem k dodatečnému obsahu jsou nejdůležitější snímky konzistentní vzhledem k aplikacím a vybírají nejdelší dobu. Pro databázové operační systémy doporučujeme body obnovení konzistentní vzhledem k aplikacím a aplikace, jako je například SQL Server. Pro Windows se snímky konzistentní vzhledem k aplikacím používají služba Stínová kopie svazku (VSS).

### <a name="do-app-consistent-recovery-points-impact-performance"></a>Má to vliv na výkon bodů obnovení konzistentních vzhledem k aplikacím?

 Vzhledem k tomu, že body obnovení konzistentní vzhledem k aplikacím zachytí veškerá data v paměti a procesu, jsou-li často zachycena, může to mít vliv na výkon, pokud je zatížení již zaneprázdněno. Nedoporučujeme zachytit příliš často pro úlohy, které nejsou databázemi. I pro databázové úlohy by měla být jedna hodina dostatečná.

### <a name="whats-the-minimum-frequency-for-generating-app-consistent-recovery-points"></a>Jaká je minimální frekvence pro generování bodů obnovení konzistentních vzhledem k aplikacím?

Site Recovery může vytvořit body obnovení konzistentní vzhledem k aplikacím s minimální frekvencí jedné hodiny.

### <a name="can-i-enable-app-consistent-replication-for-linux-vms"></a>Můžu pro virtuální počítače se systémem Linux povolit replikaci konzistentní vzhledem k aplikacím?

Ano. Agent mobility pro Linux podporuje vlastní skripty pro konzistenci aplikací. Agent používá vlastní skript s předplatným a možností po něm. [Další informace](site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

### <a name="how-are-recovery-points-generated-and-saved"></a>Jak se vygenerovaly a ukládají body obnovení?

Abychom porozuměli tomu, jak Site Recovery generuje body obnovení, použijte příklad. 

- Zásada replikace zachovává body obnovení po dobu 24 hodin a bere snímek frekvence konzistentní vzhledem k aplikacím každou hodinu.
- Site Recovery vytvoří bod obnovení konzistentní vzhledem k selháním každých pět minut. Tuto frekvenci nemůžete změnit.
- Site Recovery vyřadí body obnovení po jedné hodině a ušetří tak bod za hodinu.

Proto si za poslední hodinu můžete vybrat z 12 bodů konzistentních z havárie a jednoho bodu konzistentního vzhledem k aplikacím, jak je znázorněno na obrázku.

   ![Seznam generovaných bodů obnovení](./media/azure-to-azure-common-questions/recovery-points.png)

### <a name="how-far-back-can-i-recover"></a>Jak daleko zpátky můžu obnovit?

Nejstarší bod obnovení, který můžete použít, je 72 hodin.

### <a name="what-happens-if-site-recovery-cant-generate-recovery-points-for-more-than-24-hours"></a>Co se stane, když Site Recovery nemůže generovat body obnovení po dobu delší než 24 hodin? 

Pokud máte zásady replikace 24 hodin a Site Recovery nemůžou generovat body obnovení po dobu delší než 24 hodin, zůstanou staré body obnovení. Site Recovery nahradí nejstarší bod pouze v případě, že generuje nové body. Až budou nové body obnovení, všechny staré body zůstanou i po dosažení okna pro uchovávání informací.

### <a name="can-i-change-the-replication-policy-after-replication-is-enabled"></a>Můžu po povolení replikace změnit zásady replikace?

Ano. V trezoru >   >  **Zásady replikace** Site Recovery infrastruktury, vyberte a upravte zásady. Změny se projeví i u stávajících zásad.

### <a name="are-all-recovery-points-a-complete-vm-copy"></a>Mají všechny body obnovení úplnou kopii virtuálního počítače?

První vygenerovaný bod obnovení má úplnou kopii. Po sobě jdoucí body obnovení mají rozdílové změny.

### <a name="do-increases-in-recovery-point-retention-increase-storage-costs"></a>Zvyšují se náklady na úložiště v uchovávání bodů obnovení?

Ano. Například Pokud zvýšíte dobu uchovávání z 24 hodin na 72, Site Recovery uloží body obnovení pro další 48 hodiny. Přidaný čas má za následek změnu úložiště. Jenom jako příklad, pokud jeden bod obnovení má rozdílové změny 10 GB, za GB $0,16 za měsíc a další poplatky by byly $1,60 × 48 měsíčně.

## <a name="multi-vm-consistency"></a>Konzistence s více virtuálními počítači

### <a name="what-is-multi-vm-consistency"></a>Co je konzistence více virtuálních počítačů?

Konzistence s více virtuálními počítači zajišťuje, aby body obnovení byly konzistentní napříč replikovanými virtuálními počítači.

- Pokud povolíte konzistenci pro více virtuálních počítačů, Site Recovery vytvoří replikační skupinu všech počítačů s povolenou možností. 
- Při převzetí služeb při selhání v počítačích v replikační skupině mají sdílené body obnovení konzistentní s chybou a konzistentní vzhledem k aplikacím.

[Naučte](azure-to-azure-tutorial-enable-replication.md#enable-replication) se, jak povolit konzistenci pro více virtuálních počítačů.

### <a name="can-i-fail-over-a-single-vm-in-a-replication-group"></a>Můžu převzít služby při selhání jednoho virtuálního počítače v replikační skupině?

No. Pokud povolíte konzistenci pro víc virtuálních počítačů, odvodí se, že aplikace bude závislá na všech virtuálních počítačích v replikační skupině, a jedno převzetí služeb virtuálního počítače není povolené.

### <a name="how-many-vm-can-i-replicate-together-in-a-group"></a>Kolik virtuálních počítačů je možné replikovat společně ve skupině?

Do replikační skupiny můžete replikovat 16 virtuálních počítačů najednou.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Kdy mám povolit konzistenci pro více virtuálních počítačů?

Konzistence s více virtuálními počítači je náročná na výkon procesoru a povolení může ovlivnit výkon úloh. Povolte pouze v případě, že virtuální počítače spouštějí stejnou úlohu a potřebujete konzistenci napříč více počítači. Pokud máte například dvě instance SQL Server a dva webové servery v aplikaci, povolte konzistenci více virtuálních počítačů pouze pro instance SQL Server.

### <a name="can-i-add-a-replicating-vm-to-a-replication-group"></a>Můžu do replikační skupiny přidat replikační virtuální počítač?

Pokud povolíte replikaci pro virtuální počítač, můžete ji přidat do nové skupiny replikace nebo do existující skupiny. Nelze přidat virtuální počítač, který je již replikován do skupiny. 
 
## <a name="failover"></a>Převzetí služeb při selhání

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Jak zajistíme kapacitu v cílové oblasti?

Tým Site Recovery týmu a Azure Capacity Management pro plánování dostatečné kapacity infrastruktury. Když spustíte převzetí služeb při selhání, týmy také pomůžou zajistit, aby se instance virtuálních počítačů, které jsou chráněné Site Recovery, mohly nasadit do cílové oblasti.

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?

Převzetí služeb při selhání není automatické. Převzetí služeb při selhání můžete spustit jediným kliknutím na portálu nebo pomocí  [PowerShellu](azure-to-azure-powershell.md) aktivovat převzetí služeb při selhání.

### <a name="can-i-keep-a-public-ip-address-after--failover"></a>Můžu po převzetí služeb při selhání zachovat veřejnou IP adresu?

Po převzetí služeb při selhání nebudete mít veřejnou IP adresu pro produkční aplikaci.

Když v rámci procesu převzetí služeb při selhání zavoláte úlohu, musíte k ní přiřadit prostředek veřejné IP adresy Azure. Prostředek musí být k dispozici v cílové oblasti. Prostředek veřejné IP adresy Azure můžete přiřadit ručně nebo ho můžete automatizovat pomocí plánu obnovení. [Přečtěte si](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan) , jak nastavit veřejné IP adresy po převzetí služeb při selhání.

### <a name="can-i-keep-a-private-ip-address-after-failover"></a>Můžu po převzetí služeb při selhání zachovat soukromou IP adresu?

Ano. Když pro virtuální počítače Azure povolíte zotavení po havárii, Site Recovery v závislosti na nastavení zdrojového prostředku vytvoří cílové prostředky. U virtuálních počítačů Azure nakonfigurovaných pomocí statických IP adres se Site Recovery pokusí zřídit stejnou IP adresu pro cílový virtuální počítač, pokud se nepoužívá.
[Přečtěte si další informace o](site-recovery-retain-ip-azure-vm-failover.md) udržování IP adres po převzetí služeb při selhání.

### <a name="why-is-a-vm-assigned-a-new-ip-address-after-failover"></a>Proč virtuální počítač po převzetí služeb při selhání přiřadil novou IP adresu?

Site Recovery se pokusí zadat IP adresu v době převzetí služeb při selhání. Pokud jiný virtuální počítač používá tuto adresu, Site Recovery nastaví další dostupnou IP adresu jako cíl.

[Přečtěte si další informace o](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms) nastavení mapování sítě a adresování IP pro virtuální sítě.

### <a name="whats-the-latest-recovery-point"></a>Co je to *nejnovější* bod obnovení?

*Nejnovější (nejnižší)* možnost bodu obnovení má následující možnosti:

1. Nejprve zpracuje všechna data, která byla odeslána do Site Recovery.
2. Jakmile služba zpracuje data, vytvoří bod obnovení pro každý virtuální počítač, než dojde k převzetí služeb virtuálního počítače při selhání. Tato možnost poskytuje nejnižší cíl bodu obnovení (RPO).
3. Virtuální počítač vytvořený po převzetí služeb při selhání obsahuje všechna data replikovaná do Site Recovery, od okamžiku aktivace převzetí služeb při selhání.

### <a name="do-latest-recovery-points-impact-failover-rto"></a>Mají *nejnovější* body obnovení vliv na převzetí služeb při selhání RTO?

Ano. Site Recovery zpracovává všechna nevyřízená data před převzetím služeb při selhání, takže tato možnost má vyšší cíl času obnovení (RTO) než jiné možnosti.

### <a name="whats-the-latest-processed-recovery-option"></a>Jaká je nejnovější možnost pro *zpracované* obnovení?

*Poslední zpracovaná* možnost má následující možnosti:

1. Dojde k převzetí služeb při selhání u všech virtuálních počítačů do nejnovějšího bodu obnovení zpracovaného Site Recovery. Tato možnost poskytuje nízkou RTO, protože nestráví zpracováním nezpracovaných dat žádného času.

### <a name="what-if-theres-an-unexpected-outage-in-the-primary-region"></a>Co když v primární oblasti dojde k neočekávanému výpadku?

Můžete spustit převzetí služeb při selhání. Site Recovery nepotřebuje k převzetí služeb při selhání připojení z primární oblasti.

### <a name="what-is-the-rto-of-a-vm-failover"></a>Jaké jsou RTOy převzetí služeb virtuálního počítače při selhání?

Site Recovery RTO SLA po [dvou hodinách](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Ve většině případů Site Recovery při selhání virtuálních počítačů během několika minut. Pokud chcete vypočítat RTO, Projděte si úlohu převzetí služeb při selhání, která zobrazuje čas potřebný k uvedení virtuálního počítače. 

## <a name="recovery-plans"></a>Plány obnovení

### <a name="whats-a-recovery-plan"></a>Co je plán obnovení?

[Plán obnovení](site-recovery-create-recovery-plans.md) v Site Recovery orchestruje převzetí služeb při selhání a obnovení virtuálních počítačů. Pomáhá zajistit konzistentně přesný, opakující se a automatizované obnovení. Provádí následující akce:

- Definuje skupinu virtuálních počítačů, u kterých dojde k převzetí služeb při selhání.
- Definuje závislosti mezi virtuálními počítači, takže se aplikace přesně objeví.
- Automatizuje obnovení s možností vlastních ručních akcí pro jiné úlohy než převzetí služeb při selhání virtuálního počítače. 


### <a name="how-does-sequencing-work"></a>Jak sekvencování funguje?

V plánu obnovení můžete vytvořit více skupin virtuálních počítačů pro řazení. Skupiny se převezmou v jednom okamžiku, aby se virtuální počítače, které jsou součástí stejné skupiny, převzaly společně. [Další informace](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Jak můžu najít RTO plánu obnovení?

Pokud chcete zkontrolovat RTO plánu obnovení, proveďte test převzetí služeb při selhání pro plán obnovení. V části **úlohy Site Recovery** zkontrolujte dobu trvání testovacího převzetí služeb při selhání. Na ukázkovém snímku obrazovky trvalo **SAPTestRecoveryPlan** úlohy testovacího převzetí služeb při selhání 8 minut a 59 sekund.

![Vypíše úlohy, které zobrazují dobu trvání testovacího převzetí služeb při selhání pro RTO.](./media/azure-to-azure-common-questions/recovery-plan-rto.png)

### <a name="can-i-add-automation-runbooks-to-recovery-plans"></a>Můžu přidat Runbooky Automation do plánů obnovení?

Ano. [Další informace](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Znovu naochrana a navrácení služeb po obnovení

### <a name="after-failover-are-vms-in-the-secondary-region-protected-automatically"></a>Po převzetí služeb při selhání jsou virtuální počítače v sekundární oblasti chráněné automaticky? 

No. Při převzetí služeb při selhání virtuálních počítačů z jedné oblasti do druhé se virtuální počítače spustí v cílové oblasti zotavení po havárii v nechráněném stavu. Pokud chcete znovu nastavit [ochranu](./azure-to-azure-how-to-reprotect.md) virtuálních počítačů v sekundární oblasti, Povolte replikaci zpátky do primární oblasti.

### <a name="when-i-reprotect-is-all-data-replicated-from-the-secondary-region-to-primary"></a>Při opětovném zapnutí ochrany jsou všechna data replikovaná ze sekundární oblasti do primární? 

Záleží na tom. Pokud virtuální počítač oblasti zdroje existuje, synchronizují se jenom změny mezi zdrojovým diskem a cílovým diskem. Site Recovery porovná disky s tím, co se liší, a pak data přenáší. Tento proces obvykle trvá několik hodin. [Další informace](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection).

### <a name="how-long-does-it-take-fail-back"></a>Jak dlouho trvá navrácení služeb po obnovení?

Po obnovení bude navrácení služeb po obnovení trvat přibližně stejnou dobu, jakou trvala převzetí služeb při selhání z primární oblasti do sekundární oblasti.

## <a name="capacity"></a><a name="capacity"></a>Kapacita

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Jak zajistíme kapacitu v cílové oblasti?

Tým Site Recovery týmu a Azure Capacity Management plánuje dostatečnou kapacitu infrastruktury. Když spustíte převzetí služeb při selhání, týmy také pomůžou zajistit, aby se instance virtuálních počítačů, které jsou chráněné Site Recovery, mohly nasadit do cílové oblasti.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Pracuje Site Recovery s rezervovanými instancemi?

Ano, můžete si koupit [rezervované virtuální počítače Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/) v oblasti zotavení po havárii a Site Recovery je použít k převzetí služeb při selhání. Není nutná žádná další konfigurace.

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
