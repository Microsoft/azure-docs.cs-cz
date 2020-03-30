---
title: Časté otázky týkající se zotavení po havárii virtuálního počítače Azure pomocí Azure Site Recovery
description: Tento článek odpovídá na běžné otázky týkající se zotavení po havárii virtuálního počítače Azure při použití Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 3d71301534d56ef8eca68951c8c9f9a1570b3a6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282252"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Časté otázky: Zotavení po havárii Azure-Azure

Tento článek odpovídá na běžné otázky týkající se zotavení po havárii virtuálních počítačích Azure do jiné oblasti Azure pro při použití [Azure Site Recovery](site-recovery-overview.md).

## <a name="general"></a>Obecné

### <a name="how-is-site-recovery-priced"></a>Jak je cena site recovery?

Projděte si [ceny Azure Site Recovery pro virtuální počítače](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Jak funguje bezplatná úroveň pro Azure Site Recovery?

Každá instance, která je chráněná azure site recovery je zdarma pro prvních 31 dnů ochrany. Po uplynutí této doby je ochrana pro každou instanci za ceny v [azure site recovery pro virtuální počítače Azure](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Budou mi během prvních 31 dní účtovány další poplatky za Azure?

Ano. I když azure site recovery je zdarma během prvních 31 dnů chráněné instance, může vám vzniknout poplatky za Azure Storage, úložiště transakce a přenosy dat. Obnovené virtuální počítač může také vynakládat poplatky za výpočetní prostředky Azure. Získejte úplné podrobnosti o cenách v [cenách Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery).

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Jaké jsou doporučené postupy pro zotavení po havárii virtuálních počítačů Azure?

1. [Principy architektury Azure-to-Azure](azure-to-azure-architecture.md)
1. [Kontrola podporovaných a nepodporovaných konfigurací](azure-to-azure-support-matrix.md)
1. [Nastavení zotavení po havárii pro virtuální počítače Azure](azure-to-azure-how-to-enable-replication.md)
1. [Spuštění testovacího převzetí služeb při selhání](azure-to-azure-tutorial-dr-drill.md)
1. [Převzetí služeb při selhání a vrácení služeb při selhání do primární oblasti](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>Jak je zajištěna kapacita v cílovém regionu?

Tým site recovery a tým pro správu kapacity Azure plánují dostatečnou kapacitu infrastruktury. Když spustíte převzetí služeb při selhání, týmy také pomáhají zajistit, že instance virtuálních počítače, které jsou chráněné site recovery, se nasadí do cílové oblasti.

## <a name="replication"></a>Replikace

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Můžu replikovat virtuální počítače povolené šifrováním disku Azure?

Ano. Site Recovery podporuje zotavení po havárii virtuálních počítačů, které mají povoleno azure disk ové šifrování. Když povolíte replikaci, Azure zkopíruje všechny požadované klíče šifrování disku a tajné klíče ze zdrojové oblasti do cílové oblasti v uživatelském kontextu. Pokud nemáte příslušná oprávnění, správce zabezpečení může ke kopírování klíčů a tajných kódů použít skript.

- Site Recovery podporuje Azure Disk Encryption pro virtuální počítače Azure, které jsou se systémem Windows.
- Site Recovery podporuje Azure Disk Encryption verze 0.1, který má schéma, které vyžaduje Azure Active Directory (Azure AD). Site Recovery také podporuje verzi 1.1, která nevyžaduje Azure AD. [Další informace o schématu rozšíření pro šifrování disku Azure](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema).
  - Pro šifrování disku Azure verze 1.1 je potřeba použít virtuální počítače s Windows se spravovanými disky.
  - [Přečtěte si další informace](azure-to-azure-how-to-enable-replication-ade-vms.md) o povolení replikace pro šifrované virtuální chody.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Můžu virtuální počítače replikovat do jiného předplatného?

Ano, virtuální počítače Azure můžete replikovat do jiného předplatného v rámci stejného klienta Azure AD.

Nakonfigurujte zotavení po [havárii napříč předplatnými](https://azure.microsoft.com/blog/cross-subscription-dr) výběrem jiného předplatného v době replikace.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Můžu replikovat virtuální počítače Azure připnuté zónou do jiné oblasti?

Ano, virtuální [chody připnuté zónou](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) můžete replikovat do jiné oblasti.

### <a name="can-i-exclude-disks"></a>Mohu vyloučit disky?

Ano, disky můžete vyloučit v době ochrany pomocí prostředí PowerShell. Další informace naleznete v tématu [jak vyloučit disky z replikace](azure-to-azure-exclude-disks.md).

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Můžu do replikovaných virtuálních počítačů přidat nové disky a povolit jejich replikaci?

Ano, přidávání nových disků do replikovaných virtuálních počítačů a povolení replikace pro ně je podporované pro virtuální počítače Azure se spravovanými disky. Když přidáte nový disk do virtuálního počítače Azure, který je povolený pro replikaci, stav replikace pro virtuální počítač zobrazí upozornění. Toto upozornění uvádí, že jeden nebo více disků na virtuálním počítači jsou k dispozici pro ochranu. Replikaci pro přidané disky můžete povolit.

- Pokud povolíte ochranu pro přidané disky, upozornění zmizí po počáteční replikaci.
- Pokud nepovolíte replikaci disku, můžete upozornění zavřít.
- Pokud převezmete služby při selhání virtuálního počítače, který má povolený přidaný disk a replikace, existují replikační body. V bodech replikace se zobrazí disky, které jsou k dispozici pro obnovení.

Řekněme například, že virtuální počítače má jeden disk a přidáte nový. Před přidáním disku může existovat replikační bod, který byl vytvořen. Tento replikační bod ukáže, že se skládá z "1 ze 2 disků."

Site Recovery nepodporuje "hot remove" disku z replikovaného virtuálního počítače. Pokud odeberete disk virtuálního počítače, budete muset zakázat a znovu povolit replikaci pro virtuální počítače.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak často se můžu replikovat do Azure?

Replikace je nepřetržitá, když replikujete virtuální počítače Azure do jiné oblasti Azure. Další informace najdete v tématu [architektura replikace Azure-Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>Můžu replikovat virtuální počítače v rámci oblasti? Potřebuji tuto funkci k migraci virtuálních stránek.

K replikaci virtuálních počítačů v rámci oblasti nelze použít řešení pro obnovení disku Azure-Azure.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Můžu replikovat instance virtuálních počítače do libovolné oblasti Azure?

Pomocí site recovery můžete replikovat a obnovit virtuální chod mezi libovolnými dvěma oblastmi v rámci stejného geografického clusteru. Geografické clustery jsou definovány s ohledem na latenci dat a suverenitu. Další informace naleznete v [matici podpory oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)obnovení webu .

### <a name="does-site-recovery-require-internet-connectivity"></a>Vyžaduje site recovery připojení k internetu?

Ne, Site Recovery nevyžaduje připojení k internetu. Ale to vyžaduje přístup k adresám URL obnovení webu a rozsahy IP adres, jak je uvedeno v [sítích v azure vyobnovení po havárii virtuálního počítače](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>Můžu replikovat aplikaci, která má samostatnou skupinu prostředků pro samostatné vrstvy?

Ano, můžete replikovat aplikaci a zachovat konfiguraci zotavení po havárii v samostatné skupině prostředků.

Například pokud vaše aplikace má každou vrstvu aplikace, databáze a web v samostatné skupině prostředků, pak budete muset vybrat [průvodce replikace](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) třikrát chránit všechny vrstvy. Site Recovery bude replikovat tyto tři vrstvy do tří různých skupin prostředků.

## <a name="replication-policy"></a>Zásady replikace

### <a name="what-is-a-replication-policy"></a>Co je zásada replikace?

Zásady replikace definují nastavení pro historii uchovávání bodů obnovení. Zásady také definuje frekvenci snímků konzistentních s aplikací. Ve výchozím nastavení azure site recovery vytvoří nové zásady replikace s výchozím nastavením:

- 24 hodin pro retenční historii bodů zotavení.
- 60 minut pro frekvenci snímků konzistentních s aplikací.

[Další informace o nastavení replikace](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Co je bod obnovení konzistentní s havárií?

Bod obnovení konzistentní s selháním má data na disku, jako byste během snímku vytáhli napájecí kabel ze serveru. Bod obnovení konzistentní selhání neobsahuje nic, co bylo v paměti při pořízení snímku.

Dnes většina aplikací může dobře obnovit z snímků konzistentních selhání. Bod obnovení konzistentní s havárií je obvykle dostačující pro operační systémy a aplikace bez databáze, jako jsou souborové servery, servery DHCP a tiskové servery.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Jaká je frekvence generování bodů obnovení konzistentních při selhání?

Obnovení webu vytvoří bod obnovení konzistentní selhání každých 5 minut.

### <a name="what-is-an-application-consistent-recovery-point"></a>Co je bod obnovení konzistentní s aplikací?

Body obnovení konzistentní s aplikací jsou vytvářeny ze snímků konzistentních s aplikací. Body obnovení konzistentní s aplikací zachycují stejná data jako snímky konzistentní s havárií a zároveň zachycují data v paměti a všechny transakce v procesu.

Vzhledem k jejich další obsah, aplikace konzistentní snímky jsou nejvíce zapojeny a trvat nejdéle. Doporučujeme body obnovení konzistentní s aplikací pro databázové operační systémy a aplikace, jako je SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Jaký je dopad bodů obnovení konzistentní chod aplikace na výkon aplikace?

Body obnovení konzistentní s aplikací zachycují všechna data v paměti a v procesu. Vzhledem k tomu, že body obnovení zachycují tato data, vyžadují rámec, jako je služba Stínová kopie svazku v systému Windows, aby aplikaci udála. Pokud je proces zachytávání častý, může ovlivnit výkon, když je úloha již obsazena. Nedoporučujeme používat nízké frekvence pro body obnovení konzistentní s aplikací pro úlohy bez databáze. I pro databázové zatížení stačí 1 hodina.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Jaká je minimální četnost generování bodů obnovení konzistentní v aplikaci?

Obnovení webu můžete vytvořit bod obnovení konzistentní s aplikací s minimální frekvencí 1 hodinu.

### <a name="how-are-recovery-points-generated-and-saved"></a>Jak se body obnovení generují a ukládají?

Chcete-li pochopit, jak site recovery generuje body obnovení, podívejme se na příklad zásadreplikace. Tato zásada replikace má bod obnovení s 24hodinovým retenčním oknem a snímkem frekvence konzistentního s aplikací 1 hodinu.

Obnovení webu vytvoří bod obnovení konzistentní selhání každých 5 minut. Tuto frekvenci nemůžete změnit. Za poslední hodinu si můžete vybrat z 12 bodů konzistentních při selhání a 1 bodu konzistentního s aplikací. Jak čas postupuje, site recovery prořezává všechny body obnovení za poslední hodinu a uloží pouze 1 bod obnovení za hodinu.

Následující snímek obrazovky ilustruje příklad. Na snímku obrazovky:

- Během poslední hodiny jsou body obnovy s frekvencí 5 minut.
- Po uplynutí poslední hodiny udržuje site recovery pouze 1 bod obnovení.

   ![Seznam vygenerovaných bodů obnovení](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Jak daleko zpět mohu obnovit?

Nejstarší bod obnovení, který můžete použít, je 72 hodin.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Mám replikační politiku 24 hodin. Co se stane, pokud problém zabrání obnovení webu ve generování bodů obnovení po dobu delší než 24 hodin? Budou mé předchozí body zotavení ztraceny?

Ne, Site Recovery zachová všechny vaše předchozí body obnovení. Depending on the recovery points' retention window, Site Recovery replaces the oldest point only if it generates new points. Z důvodu problému obnovení webu nemůže generovat žádné nové body obnovení. Dokud nebudou k dispozici nové body obnovení, všechny staré body zůstanou po dosažení okna uchovávání.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Po povolení replikace na virtuálním počítači, jak změnit zásady replikace?

Přejděte **na** > **zásady replikace infrastruktury****infrastruktury obnovení lokality** > . Vyberte zásadu, kterou chcete upravit, a uložte změny. Všechny změny budou platit pro všechny existující replikace příliš.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Jsou všechny body obnovení úplnou kopií virtuálního virtuálního soudu nebo rozdílu?

První bod obnovení, který je generován má úplnou kopii. Všechny po sobě jdoucí body obnovení mají rozdílové změny.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Zvyšuje prodloužení doby uchovávání bodů obnovení náklady na úložiště?

Ano, pokud produmíte dobu uchovávání z 24 hodin na 72 hodin, site recovery uloží body obnovení na dalších 48 hodin. Za přidaný čas se účtují poplatky za úložiště. Například jeden bod obnovení může mít rozdílové změny 10 GB s náklady na GB 0,16 USD za měsíc. Dodatečné poplatky by byly $ 1.60 × 48 za měsíc.

## <a name="multi-vm-consistency"></a>Konzistence více virtuálních mís

### <a name="what-is-multi-vm-consistency"></a>Co je konzistence více virtuálních virtuálních bylin?

Konzistence více virtuálních počítačů zajišťuje, že bod obnovení je konzistentní ve všech replikovaných virtuálních počítačích.

Site Recovery poskytuje možnost **konzistence více virtuálních počítačů,** která vytvoří replikační skupinu všech počítačů.

Když převezmete služby při selhání virtuálních počítačů, budou mít sdílené body obnovení konzistentní s selháním a konzistentní s aplikací.

Projděte si kurz, který [umožní konzistenci více virtuálních virtuálních montovek](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm).

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Můžu selhat přes jeden virtuální počítač v rámci replikační skupiny konzistence více virtuálních počítačů?

Když vyberete možnost **konzistence více virtuálních počítačů,** oznamujete, že aplikace má závislost na všech virtuálních počítačích ve skupině. Převzetí služeb při selhání jednoho virtuálního počítače není povoleno.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Kolik virtuálních počítačů můžu replikovat jako součást replikační skupiny konzistence více virtuálních počítačů?

V replikační skupině můžete replikovat 16 virtuálních počítačů.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Kdy mám povolit konzistenci více virtuálních virtuálních mís?

Vzhledem k tomu, že konzistence více virtuálních zařízení je náročná na procesor, povolení může ovlivnit výkon pracovního vytížení. Konzistenci více virtuálních počítačů používejte pouze v případě, že počítače spouštějí stejné úlohy a potřebujete konzistenci napříč více počítači. Například pokud máte dvě instance SERVERU SQL a dva webové servery v aplikaci, měli byste mít konzistenci více virtuálních zařízení pouze pro instance serveru SQL Server.

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>Můžete přidat již replikující virtuální hod do replikační skupiny?

Virtuální ho virtuálního serveru můžete přidat do nové replikační skupiny při povolení replikace. Virtuální ho virtuálního serveru můžete také přidat do existující replikační skupiny a současně povolit replikaci. Již replikující se virtuální virtuální počítače však nelze přidat do nové replikační skupiny nebo existující replikační skupiny.

## <a name="failover"></a>Převzetí služeb při selhání

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Jak je zajištěna kapacita v cílové oblasti pro virtuální počítače Azure?

Tým site recovery a tým pro správu kapacity Azure plánují dostatečnou kapacitu infrastruktury. Když spustíte převzetí služeb při selhání, týmy také pomáhají zajistit, že instance virtuálních počítače, které jsou chráněné site recovery, se nasadí do cílové oblasti.

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?

Převzetí služeb při selhání není automatické. Převzetí služeb při selhání můžete spustit jediným kliknutím na portálu nebo můžete pomocí [prostředí PowerShell](azure-to-azure-powershell.md) aktivovat převzetí služeb při selhání.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>Mohu po převzetí služeb při selhání zachovat veřejnou IP adresu?

Veřejnou IP adresu produkční aplikace nelze po převzetí služeb při selhání zachovat.

Když navedete úlohu jako součást procesu převzetí služeb při selhání, musíte k tomuto prostředku přiřadit prostředek veřejné IP služby Azure. Prostředek veřejné IP adresy Azure musí být k dispozici v cílové oblasti. Veřejný IP prostředek Azure můžete přiřadit ručně nebo ho můžete automatizovat pomocí plánu obnovení. Přečtěte si, jak [nastavit veřejné IP adresy po převzetí služeb při selhání](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>Mohu si během převzetí služeb při selhání ponechat privátní IP adresu?

Ano, můžete si ponechat soukromou IP adresu. Ve výchozím nastavení při povolení zotavení po havárii pro virtuální počítače Azure, site recovery vytvoří cílové prostředky na základě nastavení zdrojových prostředků. Pro virtuální počítače Azure nakonfigurované se statickými IP adresami se Site Recovery pokusí zřídit stejnou IP adresu pro cílový virtuální počítač, pokud se nepoužívá.
Informace o [zachování IP adres během převzetí služeb při selhání](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>Proč je serveru po převzetí služeb při selhání přiřazena nová adresa IP?

Obnovení webu se pokusí poskytnout adresu IP v době převzetí služeb při selhání. Pokud tuto adresu přebírá jiný virtuální počítač, site recovery nastaví jako cíl další dostupnou IP adresu.

Další informace o [nastavení mapování sítě a adresování IP adres pro virtuální sítě](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Co jsou nejnovější body obnovení **(nejnižší RPO)?**

**Možnost Nejnovější (nejnižší RPO)** nejprve zpracuje všechna data, která byla odeslána do obnovení webu. Poté, co služba zpracuje data, vytvoří bod obnovení pro každý virtuální virtuální ms před převzetím služeb při selhání do virtuálního provozu. Tato možnost poskytuje nejnižší cíl bodu obnovení (RPO). Virtuální ms vytvořený po převzetí služeb při selhání má všechna data replikovaná do obnovení lokality od doby, kdy se aktivuje převzetí služeb při selhání.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Mají poslední body obnovení **(nejnižší RPO)** vliv na rto převzetí služeb při selhání?

Ano. Obnovení webu zpracovává všechna nevyřízená data před převzetím selhání, takže tato možnost má vyšší cíl doby obnovení (RTO) ve srovnání s jinými možnostmi.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Co znamená **nejnovější zpracovaná** možnost v bodech obnovení?

**Nejnovější zpracovaná** možnost selže přes všechny virtuální chody v plánu na nejnovější bod obnovení, který site recovery zpracovány. Pokud chcete zobrazit nejnovější bod obnovení pro konkrétní virtuální počítače, zkontrolujte **nejnovější body obnovení** v nastavení virtuálních počítače. Tato možnost poskytuje nízké RTO, protože žádný čas strávený zpracování nezpracovaných dat.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Co se stane, když v primární oblasti dojde k neočekávanému výpadku?

Po výpadku můžete spustit převzetí služeb při selhání. Obnovení lokality nepotřebuje připojení z primární oblasti k převzetí služeb při selhání.

### <a name="what-is-an-rto-of-a-vm-failover"></a>Co je rto převzetí služeb při selhání virtuálního vana?

Obnova webu má [RTO SLA 2 hodiny](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Ve většině času však obnovení webu selže přes virtuální počítače během několika minut. RTO můžete vypočítat tak, že přejdete na úlohy převzetí služeb při selhání, které ukazují čas, který trval navádění virtuálního trhu. Plán obnovení RTO naleznete v další části.

## <a name="recovery-plans"></a>Plány obnovení

### <a name="what-is-a-recovery-plan"></a>Co je plán obnovy?

Plán obnovení v obnovení webu orchestruje obnovení převzetí služeb při selhání virtuálních zařízení. Pomáhá, aby bylo obnovení konzistentně přesné, opakovatelné a automatizované. Plán obnovení řeší následující potřeby:

- Definování skupiny virtuálních počítačů, které společně přepojit převzetí služeb při selhání
- Definování závislostí mezi virtuálními počítači tak, aby aplikace přišla přesně
- Automatizace obnovení spolu s vlastními ručními akcemi k dosažení jiných úkolů, než je převzetí služeb při selhání virtuálních počítačů

Další informace [o vytváření plánů obnovení](site-recovery-create-recovery-plans.md).

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Jak se provádí sekvencování v plánu obnovy?

V plánu obnovení můžete vytvořit více skupin pro dosažení sekvencování. Každá skupina převezme služby při selhání najednou. Virtuální počítače, které jsou součástí stejné skupiny převzetí služeb při selhání společně, následovaný jinou skupinou. Informace o tom, jak modelovat aplikaci pomocí plánu obnovení, naleznete v tématu [O plánech obnovení](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Jak najdu RTO plánu obnovy?

Chcete-li zkontrolovat RTO plánu obnovení, proveďte zkušební převzetí služeb při selhání pro plán obnovení a přejděte na **úlohy obnovení webu**.
V následujícím příkladu naleznete úlohu **SAPTestRecoveryPlan**. Úloha trvala 8 minut a 59 sekund, než se převzetí služeb při selhání všech virtuálních počítačů a provést zadané akce.

![Seznam úloh obnovení webu](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Je možné do plánu obnovení přidat runbooky automatizace?

Ano, runbooky Azure Automation můžete integrovat do plánu obnovení. Další informace o [přidávání runbooků Azure Automation](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Opětovné protekce a navrácení služeb po obnovení

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Selhal jsem z primární oblasti do oblasti zotavení po havárii. Jsou virtuální počítače v oblasti zotavení po Havárii chráněné automaticky?

Ne. Když [přejdete přes virtuální](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) počítače Azure z jedné oblasti do druhé, virtuální počítače spustit v oblasti zotavení po Havárii v nechráněném stavu. Chcete-li obnovit virtuální chod do primární oblasti, je třeba [znovu chránit](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) virtuální chod v sekundární oblasti.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Replikuje v době opětovné ochrany obnovení lokality úplná data ze sekundární oblasti do primární oblasti?

To záleží na situaci. Pokud existuje zdrojový virtuální modul oblasti, budou synchronizovány pouze změny mezi zdrojovým diskem a cílovým diskem. Site Recovery vypočítá rozdíly porovnáním disků a pak přenáší data. Tento proces obvykle trvá několik hodin. Další informace o tom, co se stane během opětovného protekce, najdete v tématu [opětovné obsazovat převzetí počítače Azure přes instance virtuálních](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection)zařízení Azure do primární oblasti .

### <a name="how-much-time-does-it-take-to-fail-back"></a>Jak dlouho trvá navrácení služeb po obnovení?

Po opětovném protekci trvá navrácení služeb po obnovení trvá přibližně stejné množství času, které trvá převzetí služeb při selhání z primární oblasti do sekundární oblasti.

## <a name="capacity"></a><a name="capacity"></a>Kapacita

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Jak je zajištěna kapacita v cílové oblasti pro virtuální počítače Azure?

Tým site recovery a tým pro správu kapacity Azure plánují dostatečnou kapacitu infrastruktury. Když spustíte převzetí služeb při selhání, týmy také pomáhají zajistit, že instance virtuálních počítače, které jsou chráněné site recovery, se nasadí do cílové oblasti.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Funguje obnovení webu s rezervovanými instancemi?

Ano, můžete zakoupit [rezervované virtuální počítače Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/) v oblasti zotavení po havárii a operace převzetí služeb při selhání služby Site Recovery je budou používat. Není nutná žádná další konfigurace.

## <a name="security"></a>Zabezpečení

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Posílají se replikační data do služby Site Recovery?

Ne, Site Recovery nezachycuje replikovaná data a nemá žádné informace o tom, co běží na virtuálních počítačích. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.

Obnova webu je iso 27001:2013, 27018, HIPAA a DPA certifikované. Služba prochází hodnoceními SOC2 a FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>Šifruje Site Recovery replikaci?

Ano, šifrování v přenosu i [šifrování v klidovém stavu v Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) jsou podporované.

## <a name="next-steps"></a>Další kroky

- [Projděte si požadavky na podporu Azure.](azure-to-azure-support-matrix.md)
- [Nastavte replikaci Azure-to-Azure](azure-to-azure-tutorial-enable-replication.md).
- Pokud máte otázky po přečtení tohoto článku, zveřejněte je na [fóru Služby Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).
