---
title: Časté otázky týkající se zotavení po havárii společnosti VMware pomocí azure site recovery
description: Pomocí Azure Site Recovery najdete odpovědi na běžné otázky týkající se obnovení místních virtuálních zařízení VMware v Azure.
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: d551cef7037c0b6d7286cbb4b70d8f7a8f7f5cae
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259506"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Běžné otázky týkající se replikace z VMware do Azure

Tento článek odpovídá na běžné otázky, které se mohou stát při nasazení zotavení po havárii místních virtuálních počítačů VMware do Azure.

## <a name="general"></a>Obecné

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Co potřebuji pro zotavení po havárii virtuálního virtuálního vava v systému VMware?

[Seznamte se s součástmi, které se podílejí na](vmware-azure-architecture.md) zotavení po havárii virtuálních měn VMware.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Můžu použít Site Recovery k migraci virtuálních počítačů VMware do Azure?

Ano. Kromě použití site recovery k nastavení úplného zotavení po havárii pro virtuální počítače VMware můžete také použít Site Recovery k migraci místních virtuálních počítačů VMware do Azure. V tomto scénáři replikovat místní virtuální počítače VMware do úložiště Azure. Potom převzetí služeb při selhání z místního do Azure. Po převzetí služeb při selhání jsou vaše aplikace a úlohy dostupné a spuštěné na virtuálních počítačích Azure. Tento proces je jako nastavení úplné zotavení po havárii, s tím rozdílem, že v migraci nelze poobnovení z Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Potřebuje můj účet Azure oprávnění k vytváření virtuálních počítačů?

Pokud jste správce předplatného, máte oprávnění replikace, které potřebujete. Pokud nejste správce, potřebujete oprávnění k těmto akcím:

- Vytvořte virtuální počítač Azure ve skupině prostředků a virtuální síti, kterou zadáte při konfiguraci site recovery.
- Napište na vybraný účet úložiště nebo na spravovaný disk na základě konfigurace.

[Přečtěte si další informace](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) o požadovaných oprávněních.

### <a name="what-applications-can-i-replicate"></a>Jaké aplikace lze replikovat?

Můžete replikovat libovolnou aplikaci nebo úlohu spuštěnou na virtuálním počítači VMware, který splňuje [požadavky na replikaci](vmware-physical-azure-support-matrix.md#replicated-machines).

- Site Recovery podporuje replikaci s podporou aplikací, takže aplikace mohou být převzetí služeb při selhání a selhání zpět do inteligentního stavu.
- Site Recovery se integruje s aplikacemi společnosti Microsoft, jako je SharePoint, Exchange, Dynamics, SQL Server a Active Directory. Úzce spolupracuje také s předními dodavateli, včetně společností Oracle, SAP, IBM a Red Hat.

[Další informace](site-recovery-workload.md) o ochraně úloh.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Můžu v Azure použít licenci na server hostovaného operačního systému?

Ano, zákazníci Microsoft Software Assurance můžou využít [hybridní výhody Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) k úspoře licenčních nákladů pro počítače s Windows Server, které se migrují do Azure, nebo k využití Azure pro zotavení po havárii.

## <a name="security"></a>Zabezpečení

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Jaký přístup k serverům VMware site recovery potřebuje?

Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Nastavte virtuální hod VMware se systémem site recovery konfigurační server.
- Automaticky zjišťuje virtuální chod pro replikaci.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Jaký přístup k virtuálním virtuálním mům VMware potřebuje obnovení webu?

- Chcete-li replikovat, virtuální ho mandase VMware musí mít nainstalovanou a spuštěnou službu Mobility site recovery. Nástroj můžete nasadit ručně nebo můžete určit, že site recovery provést nabízenou instalaci služby při povolení replikace pro virtuální ho.
- Během replikace virtuální chody komunikují s site recovery takto:
    - Virtuální počítače komunikují s konfiguračním serverem na portu HTTPS 443 pro správu replikace.
    - Virtuální počítač imisit data replikace na procesní server na portu HTTPS 9443. (Toto nastavení lze upravit.)
    - Pokud povolíte konzistenci více virtuálních společností, virtuální chody vzájemně komunikují přes port 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>Jsou data replikace odesílána do obnovení sítě?

Ne, Site Recovery nezachycuje replikovaná data a nemá žádné informace o tom, co běží na virtuálních počítačích. Data replikace se vyměňují mezi hypervisory VMware a Úložištěm Azure. Site Recovery nemá schopnost tato data zachytit. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.

Site Recovery je certifikován pro ISO 27001:2013 a 27018, HIPAA a DPA. Je to v procesu SOC2 a FedRAMP JAB hodnocení.

## <a name="pricing"></a>Ceny

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Jak vypočítám přibližné poplatky za zotavení po havárii společnosti VMware?

Pomocí [cenové kalkulačky](https://aka.ms/asr_pricing_calculator) můžete odhadnout náklady při používání služby Site Recovery.

Podrobný odhad nákladů naleznete v nástroji pro plánování nasazení pro [společnost VMware](https://aka.ms/siterecovery_deployment_planner) a použijte [sestavu odhadu nákladů](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Existuje nějaký rozdíl v nákladech mezi replikací do úložiště nebo přímo na spravované disky?

Spravované disky se účtují mírně odlišně od účtů úložiště. [Přečtěte si další informace](https://azure.microsoft.com/pricing/details/managed-disks/) o cenách na spravovaném disku.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Existuje nějaký rozdíl v nákladech při replikaci na účet úložiště pro obecné účely v2?

Obvykle se zobrazí zvýšení nákladů na transakce vzniklé na účtech úložiště GPv2, protože Azure Site Recovery je transakce těžké. [Přečtěte si více](../storage/common/storage-account-upgrade.md#pricing-and-billing) pro odhad změny.

## <a name="mobility-service"></a>Služba Mobility

### <a name="where-can-i-find-the-mobility-service-installers"></a>Kde najdu instalační programy služby Mobility?

Instalační programy jsou ve složce %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository na konfiguračním serveru.

## <a name="how-do-i-install-the-mobility-service"></a>Jak nainstaluji službu Mobility?

Na každém virtuálním počítači, který chcete replikovat, nainstalujte službu jednou z několika metod:

- [Push instalace](vmware-physical-mobility-service-overview.md#push-installation)
- [Ruční instalace](vmware-physical-mobility-service-overview.md#install-the-mobility-service-using-ui) z nového prostředí nebo prostředí PowerShell
- Nasazení pomocí nástroje pro nasazení, jako je [nástroj Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Spravované disky

### <a name="where-does-site-recovery-replicate-data-to"></a>Kam obnova lokality replikuje data?

Site Recovery replikuje místní virtuální počítače VMware a fyzické servery na spravované disky v Azure.

- Server procesu obnovení lokality zapisuje protokoly replikace do účtu úložiště mezipaměti v cílové oblasti.
- Tyto protokoly se používají k vytvoření bodů obnovení na discích spravovaných Azure, které mají předponu **asrseeddisk**.
- Dojde-li k převzetí služeb při selhání, vybraný bod obnovení se použije k vytvoření nového cílového spravovaného disku. Tento spravovaný disk je připojený k virtuálnímu počítači v Azure.
- Virtuální chody, které byly dříve replikovány do účtu úložiště (před březnem 2019), nejsou ovlivněny.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Můžu replikovat nové počítače do účtů úložiště?

Ne. Počínaje březnem 2019 můžete na webu Azure Portal replikovat jenom na spravované disky Azure.

Replikace nových virtuálních účtů na účet úložiště je dostupná jenom pomocí PowerShellu nebo rozhraní REST API (verze 2018-01-10 nebo 2016-08-10).

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Jaké jsou výhody replikace na spravované disky?

[Přečtěte si, jak](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery zjednodušuje zotavení po havárii se spravovanými disky.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Mohu změnit typ spravovaného disku po ochraně počítače?

Ano, můžete snadno [změnit typ spravovaného disku](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) pro probíhající replikace. Před změnou typu se ujistěte, že na spravovaném disku není generována žádná adresa URL sdíleného přístupového podpisu:

1. Přejděte na prostředek **spravovaného disku** na webu Azure portal a zkontrolujte, jestli máte v okně **Přehled** nápis URL sdíleného přístupového podpisu.
1. Pokud je nápis k dispozici, vyberte jej, chcete-li probíhající export zrušit.
1. Změňte typ disku během několika příštích minut. Pokud změníte typ spravovaného disku, počkejte na nové body obnovení, které mají být generovány azure site recovery.
1. Nové body obnovení použijte pro jakékoli zkušební převzetí služeb při selhání nebo převzetí služeb při selhání v budoucnu.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Je možné přepnout replikaci ze spravovaných disků na nespravované disky?

Ne. Přepnutí ze spravovaného na nespravovaný není podporováno.

## <a name="replication"></a>Replikace

### <a name="what-are-the-replicated-vm-requirements"></a>Jaké jsou požadavky na replikovaný virtuální virtuální mísu?

[Přečtěte si další informace](vmware-physical-azure-support-matrix.md#replicated-machines) o požadavcích na podporu virtuálních měn VMware a fyzických serverů.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak často se můžu replikovat do Azure?

Replikace je nepřetržitá při replikaci virtuálních počítačů VMware do Azure.

### <a name="can-i-extend-replication"></a>Mohu prodloužit replikaci?

Rozšířená nebo zřetězená replikace není podporována. Požádejte o tuto funkci ve [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Mohu provést počáteční replikaci offline?

Offline replikace není podporována. Požádejte o tuto funkci ve [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>Co je asrseeddisk?

Pro každý zdrojový disk se data replikují na spravovaný disk v Azure. Tento disk má předponu **asrseeddisk**. Ukládá kopii zdrojového disku a všechny snímky bodu obnovení.

### <a name="can-i-exclude-disks-from-replication"></a>Lze vyloučit disky z replikace?

Ano, disky můžete vyloučit.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Můžu replikovat virtuální počítače, které mají dynamické disky?

Dynamické disky lze replikovat. Disk operačního systému musí být běžný.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Pokud používám replikační skupiny pro konzistenci více virtuálních uživatelů, můžu přidat nový virtuální virtuální ms do existující replikační skupiny?

Ano, nové virtuální hody můžete přidat do existující replikační skupiny, když pro ně povolíte replikaci. Nicméně:

- Virtuální ho virtuálního serveru nelze přidat do existující replikační skupiny po zahájení replikace.
- Nelze vytvořit replikační skupinu pro existující virtuální chod.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Můžu upravit virtuální počítače, které se replikují přidáním nebo změnou velikosti disků?

Pro replikaci vmware do Azure můžete upravit velikost disku zdrojových virtuálních počítačů. Pokud chcete přidat nové disky, musíte přidat disk a znovu povolit ochranu virtuálního počítače.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Můžu migrovat místní počítače na nový server vCenter, aniž by to mělo vliv na probíhající replikaci?

Ne. Změna vmware vcenter nebo migrace bude mít vliv na probíhající replikaci. Nastavte obnovení lokality s novým serverem vCenter a znovu povolte replikaci pro počítače.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Můžu replikovat do mezipaměti nebo cílového účtu úložiště, který má nakonfigurovanou virtuální síť (s bránami firewall Azure)?

Ne, Site Recovery nepodporuje replikaci do Azure Storage ve virtuálních sítích.

## <a name="component-upgrade"></a>Upgrade komponenty

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Moje verze agenta služeb mobility nebo konfiguračního serveru je stará a upgrade se nezdařil. Co mám udělat?

Obnovení lokality se řídí modelem podpory N-4. [Přečtěte si další informace](https://aka.ms/asr_support_statement) o tom, jak upgradovat z velmi starých verzí.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Kde najdu poznámky k verzi a kumulativní aktualizace pro Azure Site Recovery?

[Získejte informace o nových aktualizacích](site-recovery-whats-new.md)a [získejte souhrnné informace](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Kde najdu informace o upgradu pro zotavení po havárii do Azure?

[Přečtěte si další informace o inovaci](https://aka.ms/asr_vmware_upgrades).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Musím restartovat zdrojové počítače pro každý upgrade?

Restartování je doporučeno, ale není povinné pro každý upgrade. [Další informace](https://aka.ms/asr_vmware_upgrades).

## <a name="configuration-server"></a>Konfigurační server

### <a name="what-does-the-configuration-server-do"></a>K čemu konfigurační server dělá?

Konfigurační server spouští místní součásti site recovery, včetně:

- Samotný konfigurační server. Server koordinuje komunikaci mezi místními součástmi a Azure a spravuje replikaci dat.
- Procesový server, který funguje jako replikační brána. Tento server:
    1. Přijímá data replikace.
    2. Optimalizuje data pomocí ukládání do mezipaměti, komprese a šifrování.
    3. Odešle data do Azure Storage.
  Procesní server také provádí nabízenou instalaci služby mobility na virtuálních počítačích a provádí automatické zjišťování místních virtuálních počítačích VMware.
- Hlavní cílový server, který zpracovává data replikace během navrácení služeb po selhání z Azure.

[Přečtěte si další informace](vmware-azure-architecture.md) o součástech a procesech konfiguračního serveru.

### <a name="where-do-i-set-up-the-configuration-server"></a>Kde mám nastavit konfigurační server?

Potřebujete jeden, vysoce dostupný místní virtuální počítač VMware pro konfigurační server. Pro zotavení po havárii fyzického serveru nainstalujte konfigurační server do fyzického počítače.

### <a name="what-do-i-need-for-the-configuration-server"></a>Co potřebuji pro konfigurační server?

Zkontrolujte [požadavky](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Mohu konfigurační server nastavit ručně místo šablony?

Doporučujeme [vytvořit virtuální počítač konfiguračního serveru](vmware-azure-deploy-configuration-server.md) pomocí nejnovější verze šablony Open Virtualization Format (OVF). Pokud šablonu nemůžete použít (například pokud nemáte přístup k serveru VMware), [stáhněte](physical-azure-set-up-source.md) instalační soubor z portálu a nastavte konfigurační server.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Může se konfigurační server replikovat do více než jedné oblasti?

Ne. Chcete-li replikovat do více než jedné oblasti, potřebujete konfigurační server v každé oblasti.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Můžu v Azure hostovat konfigurační server?

I když je to možné, virtuální počítač Azure se systémem konfiguračníserver bude muset komunikovat s místní infrastrukturou VMware a virtuálními počítači. Tato komunikace přidává latenci a ovlivňuje probíhající replikaci.

### <a name="how-do-i-update-the-configuration-server"></a>Jak lze aktualizovat konfigurační server?

[Přečtěte si,](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) jak aktualizovat konfigurační server.

- Nejnovější informace o aktualizacích najdete na [stránce aktualizace Azure](https://azure.microsoft.com/updates/?product=site-recovery).
- Nejnovější verzi si můžete stáhnout z portálu. Nejnovější verzi konfiguračního serveru si můžete stáhnout přímo ze služby [Stažení softwaru](https://aka.ms/asrconfigurationserver).
- Pokud je vaše verze starší než čtyři verze, přečtěte si pokyny k upgradu v prohlášení o [podpoře.](https://aka.ms/asr_support_statement)

### <a name="should-i-back-up-the-configuration-server"></a>Mám zálohovat konfigurační server?

Doporučujeme pravidelně plánovat zálohování konfiguračního serveru.

- Pro úspěšné navrácení služeb po obnovení musí v databázi konfiguračního serveru existovat neúspěšný virtuální virtuální ms.
- Konfigurační server musí být spuštěn a v připojeném stavu.
- [Přečtěte si další informace](vmware-azure-manage-configuration-server.md) o běžných úlohách správy konfiguračního serveru.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Když nastavuji konfigurační server, mohu si MySQL stáhnout a nainstalovat ručně?

Ano. Stáhněte si MySQL a umístěte jej do složky C:\Temp\ASRSetup. Poté jej nainstalujte ručně. Když nastavíte virtuální modul konfiguračního serveru a přijmete podmínky, mysql bude uvedeno jako **již nainstalované** v **stáhnout a nainstalovat**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Mohu se vyhnout stahování MySQL, ale nechat Site Recovery nainstalovat?

Ano. Stáhněte instalační program MySQL a umístěte jej do složky C:\Temp\ASRSetup. Při nastavovat virtuální ms konfiguračního serveru přijměte podmínky a vyberte **Stáhnout a nainstalovat**. Portál bude používat instalační program, který jste přidali k instalaci MySQL.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Můžu virtuální ho konfiguračního serveru použít k něčemu jinému?

Ne. Virtuální počítače používejte jenom pro konfigurační server.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Lze klonovat konfigurační server a použít jej pro orchestraci?

Ne. Nastavte nový konfigurační server, abyste se vyhnuli problémům s registrací.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Mohu změnit úschovnu, ve které je konfigurační server registrován?

Ne. Po přidružení je přidružen k konfiguračníserver, nelze jej změnit. [Přečtěte si](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) o registraci konfiguračního serveru pomocí jiného trezoru.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Můžu použít stejný konfigurační server pro zotavení po havárii virtuálních počítačů VMware i fyzických serverů?

Ano, ale všimněte si, že fyzický počítač může být selhání zpět pouze na virtuální počítač VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Kde mohu stáhnout přístupové heslo pro konfigurační server?

[Přečtěte si,](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) jak stáhnout přístupové heslo.

### <a name="where-can-i-download-vault-registration-keys"></a>Kde si mohu stáhnout registrační klíče trezoru?

V trezoru služby Recovery Services vyberte **možnost Konfigurační servery** **ve správě infrastruktury** > obnovení**lokality**. Potom v **části Servery**vyberte **stáhnout registrační klíč** a stáhněte soubor přihlašovacích údajů úschovny.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>Lze jeden konfigurační server použít k ochraně více instancí vCenter?

Ano, jeden konfigurační server může chránit virtuální počítače napříč více vCenters.  Není omezeno, kolik instancí vCenter lze přidat na konfigurační server, ale limity pro počet virtuálních počítačů, které může jeden konfigurační server chránit, platí.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>Může jeden konfigurační server chránit více clusterů v rámci programu vCenter?

Ano, Azure Site Recovery můžete chránit virtuální počítače v různých clusterech.

## <a name="process-server"></a>Procesní server

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Proč nelze vybrat procesní server, když povolím replikaci?

Aktualizace ve verzích 9.24 a novějších nyní zobrazují [stav procesního serveru při povolení replikace](vmware-azure-enable-replication.md#enable-replication). Tato funkce pomáhá vyhnout se omezení procesu serveru a minimalizovat použití nefunkčních procesních serverů.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Jak lze aktualizovat procesní server na verzi 9.24 nebo novější, aby byly kontinuální informace o stavu?

Počínaje [verzí 9.24](service-updates-how-to.md#links-to-currently-supported-update-rollups)byly přidány další výstrahy označující stav procesového serveru. [Aktualizujte součásti site recovery na verzi 9.24 nebo novější, aby](service-updates-how-to.md#links-to-currently-supported-update-rollups) byly generovány všechny výstrahy.

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>Jak mohu zajistit vysokou dostupnost procesního serveru?

Konfigurací více než jednoho procesního serveru poskytuje návrh flexibilitu při přesunu chráněných počítačů z nefunkčního procesního serveru na pracovní procesní server. Přesun počítače z jednoho procesního serveru na jiný musí být zahájen explicitně/ručně pomocí definovaných kroků: [přesunutí virtuálních počítačů mezi procesními servery](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

## <a name="failover-and-failback"></a>Převzetí služeb při selhání a navrácení služeb po obnovení

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Můžu použít místní procesní server pro navrácení služeb po službě znovu?

Důrazně doporučujeme vytvořit procesní server v Azure pro účely navrácení služeb po obnovení, aby se zabránilo latenci přenosu dat. Navíc v případě, že jste oddělili zdrojovou síť virtuálních počítačů se sítí směřující do Azure na konfiguračním serveru, je nezbytné použít procesní server vytvořený v Azure pro navrácení služeb po službě znovu navrácení služeb po službě.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Mohu ip adresu ponechat při převzetí služeb při selhání?

Ano, IP adresu můžete ponechat při převzetí služeb při selhání. Ujistěte se, že zadáte cílovou IP adresu v **nastavení výpočetních prostředků a sítě** pro virtuální počítač před převzetím služeb při selhání. Také vypnout počítače v době převzetí služeb při selhání, aby se zabránilo konfliktům ip adres během navrácení služeb po selhání.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Můžu před převzetím služeb při selhání změnit velikost cílového virtuálního počítače nebo typ virtuálního počítače?

Ano, můžete změnit typ nebo velikost virtuálního počítače kdykoli před převzetím služeb při selhání. Na portálu použijte nastavení **Výpočetní ch a síťový** chod replikovaného virtuálního počítače.

### <a name="how-far-back-can-i-recover"></a>Jak daleko zpět mohu obnovit?

Pro VMware do Azure je nejstarší bod obnovení, který můžete použít, 72 hodin.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Jak se po převzetí služeb při selhání dostanu k virtuálním počítačům Azure?

Po převzetí služeb při selhání můžete přistupovat k virtuálním počítačům Azure přes zabezpečené připojení k internetu, přes síť VPN mezi weby nebo přes Azure ExpressRoute. Chcete-li se připojit, musíte připravit několik věcí. [Další informace](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>Je data s neúspěšným a neúspěšným převzetím zabezpečení odolná?

Služba Azure je pro odolnost navržena. Site Recovery je navržen pro převzetí služeb při selhání do sekundárního datového centra Azure, jak to vyžaduje smlouva o úrovni služeb Azure (SLA). Když dojde k převzetí služeb při selhání, zajistíme, aby vaše metadata a trezory zůstaly ve stejné geografické oblasti, kterou jste zvolili pro svůj trezor.

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?

[Převzetí služeb při selhání](site-recovery-failover.md) není automatické. Převzetí služeb při selhání zahájíte provedením jednoho výběru na portálu nebo můžete pomocí [prostředí PowerShell](/powershell/module/az.recoveryservices) aktivovat převzetí služeb při selhání.

### <a name="can-i-fail-back-to-a-different-location"></a>Mohu se vrátit na jiné místo?

Ano. Pokud jste se nezdaří přes Azure, můžete si posuzovat služby při selhání zpět do jiného umístění, pokud původní není k dispozici. [Další informace](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Proč potřebuji VPN nebo ExpressRoute se soukromým partnerským vztahem k navrácení služeb po selhání?

Když si z Azure vrátíte, data z Azure se zkopírují zpět do místního virtuálního počítače a je vyžadován soukromý přístup.


## <a name="automation-and-scripting"></a>Automatizace a skriptování

### <a name="can-i-set-up-replication-with-scripting"></a>Mohu nastavit replikaci pomocí skriptování?

Ano. Pracovní postupy obnovení webu můžete automatizovat pomocí rozhraní Rest API, Prostředí PowerShell nebo sady Azure SDK. [Další informace](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Výkon a kapacita

### <a name="can-i-throttle-replication-bandwidth"></a>Mohu omezit šířku pásma replikace?

Ano. [Další informace](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Další kroky

- [Zkontrolujte](vmware-physical-azure-support-matrix.md) požadavky na podporu.
- [Nastavit](vmware-azure-tutorial.md) Replikace VMware do Azure.
