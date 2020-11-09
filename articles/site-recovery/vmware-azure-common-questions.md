---
title: Běžné dotazy týkající se zotavení po havárii VMware pomocí Azure Site Recovery
description: Získejte odpovědi na běžné dotazy týkající se zotavení po havárii místních virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 8f292e7f624b80e8e13514a714c5759d88fbe57c
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94379986"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Běžné otázky týkající se replikace z VMware do Azure

Tento článek obsahuje odpovědi na běžné dotazy, které se můžou při nasazování zotavení po havárii místních virtuálních počítačů VMware do Azure nasadit.

## <a name="general"></a>Obecné

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Co potřebuji pro zotavení po havárii virtuálních počítačů VMware?

[Přečtěte si o komponentách, které se podílejí](vmware-azure-architecture.md) na zotavení po havárii virtuálních počítačů VMware.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Můžu použít Site Recovery k migraci virtuálních počítačů VMware do Azure?

Yes. Kromě použití Site Recovery k nastavení úplného zotavení po havárii pro virtuální počítače VMware můžete použít také Site Recovery k migraci místních virtuálních počítačů VMware do Azure. V tomto scénáři replikujte místní virtuální počítače VMware do Azure Storage. Pak převezmete služby při selhání z místního prostředí do Azure. Po převzetí služeb při selhání jsou vaše aplikace a úlohy dostupné a spuštěné na virtuálních počítačích Azure. Tento proces je podobný nastavení úplného zotavení po havárii s tím rozdílem, že v migraci nemůžete navracet služby po obnovení z Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Potřebuje můj účet Azure oprávnění k vytváření virtuálních počítačů?

Pokud jste správcem předplatného, máte oprávnění k replikaci, která potřebujete. Pokud nejste správce, budete potřebovat oprávnění k provedení těchto akcí:

- Vytvořte virtuální počítač Azure ve skupině prostředků a virtuální síti, kterou určíte při konfiguraci Site Recovery.
- Zapište na vybraný účet úložiště nebo na spravovaný disk na základě vaší konfigurace.

[Přečtěte si další informace](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) o požadovaných oprávněních.

### <a name="what-applications-can-i-replicate"></a>Jaké aplikace je možné replikovat?

Můžete replikovat jakoukoli aplikaci nebo úlohu spuštěnou na virtuálním počítači VMware, který splňuje [požadavky na replikaci](vmware-physical-azure-support-matrix.md#replicated-machines).

- Site Recovery podporuje replikaci zohledňující aplikace, aby bylo možné aplikace převzít služby při selhání a návrat do inteligentního stavu.
- Site Recovery se integruje s aplikacemi Microsoftu, jako jsou SharePoint, Exchange, Dynamics, SQL Server a Active Directory. Také úzce spolupracuje s předními dodavateli, včetně Oracle, SAP, IBM a Red Hat.

[Další informace](site-recovery-workload.md) o ochraně úloh.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Můžu v Azure použít licenci na server hostovaného operačního systému?

Ano, zákazníci služby Software Assurance společnosti Microsoft můžou pomocí [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) ušetřit náklady na licencování pro počítače s Windows serverem, které jsou migrovány do Azure, nebo používat Azure k zotavení po havárii.

## <a name="security"></a>Zabezpečení

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Jaký přístup k serverům VMware potřebuje Site Recovery?

Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Nastavte virtuální počítač VMware, na kterém běží konfigurační server Site Recovery.
- Automatické zjišťování virtuálních počítačů pro replikaci.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Jaký přístup k virtuálním počítačům VMware Site Recovery potřebují?

- Aby bylo možné replikovat, musí mít virtuální počítač VMware nainstalovanou a spuštěnou službu mobility Site Recovery. Nástroj můžete nasadit ručně, nebo můžete určit, že Site Recovery provést nabízenou instalaci služby při povolení replikace pro virtuální počítač.
- Během replikace komunikují virtuální počítače s Site Recovery následujícím způsobem:
    - Virtuální počítače komunikují s konfiguračním serverem na portu HTTPS 443 pro správu replikací.
    - Virtuální počítače odesílají data replikace na procesový Server na portu HTTPS 9443. (Toto nastavení lze změnit.)
    - Pokud povolíte konzistenci pro více virtuálních počítačů, virtuální počítače spolu navzájem komunikují přes port 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>Posílají se data replikace do Site Recovery?

Ne, Site Recovery nezachycuje replikovaná data a nemá žádné informace o tom, co běží na vašich virtuálních počítačích. Data replikace se vyměňují mezi hypervisory a Azure Storage VMware. Site Recovery nemá schopnost tato data zachytit. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.

Site Recovery je certifikováno pro ISO 27001:2013 a 27018, HIPAA a DPA. Je v procesu posuzování SOC2 a FedRAMP JAB.

## <a name="pricing"></a>Ceny

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Návody vypočítat přibližné poplatky za zotavení po havárii VMware?

Pomocí [cenové kalkulačky](https://aka.ms/asr_pricing_calculator) můžete odhadnout náklady při používání Site Recovery.

Podrobný odhad nákladů získáte spuštěním nástroje Planner Deployment pro [VMware](./site-recovery-deployment-planner.md) a použitím [sestavy odhad nákladů](./site-recovery-vmware-deployment-planner-cost-estimation.md).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Je mezi replikací do úložiště nebo přímo na spravované disky nějaký rozdíl v ceně?

Spravované disky se účtují trochu jinak než účty úložiště. [Přečtěte si další informace](https://azure.microsoft.com/pricing/details/managed-disks/) o cenách spravovaného disku.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Je při replikaci do Pro obecné účelyho účtu úložiště v2 nějaký rozdíl v ceně?

Obvykle se vám bude zvyšovat náklady na transakce, které se účtují na účtech úložiště GPv2, protože Azure Site Recovery jsou transakce těžké. [Další informace získáte](../storage/common/storage-account-upgrade.md#pricing-and-billing) k odhadu změny.

## <a name="mobility-service"></a>Služba Mobility

### <a name="where-can-i-find-the-mobility-service-installers"></a>Kde můžu najít instalační programy služby mobility?

Instalační programy jsou ve složce%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository na konfiguračním serveru.

## <a name="how-do-i-install-the-mobility-service"></a>Návody nainstalovat službu mobility?

Na každém virtuálním počítači, který chcete replikovat, nainstalujte službu jedním z několika způsobů:

- [Nabízená instalace](vmware-physical-mobility-service-overview.md#push-installation)
- [Ruční instalace](vmware-physical-mobility-service-overview.md#install-the-mobility-service-using-ui) z uživatelského rozhraní nebo PowerShellu
- Nasazení pomocí nástroje pro nasazení, jako je [Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Spravované disky

### <a name="where-does-site-recovery-replicate-data-to"></a>Kam se Site Recovery replikují data?

Site Recovery replikuje místní virtuální počítače VMware a fyzické servery na spravované disky v Azure.

- Site Recovery procesový Server zapisuje protokoly replikace do účtu úložiště mezipaměti v cílové oblasti.
- Tyto protokoly slouží k vytváření bodů obnovení na discích spravovaných v Azure, které mají předponu **asrseeddisk**.
- Když dojde k převzetí služeb při selhání, použije se vybraný bod obnovení k vytvoření nového cílového spravovaného disku. Tento spravovaný disk je připojený k virtuálnímu počítači v Azure.
- Nedošlo k ovlivnění virtuálních počítačů, které byly dříve replikovány do účtu úložiště (do března 2019).

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Můžu replikovat nové počítače do účtů úložiště?

Ne. Od března 2019 můžete v Azure Portal replikovat pouze do Azure Managed disks.

Replikace nových virtuálních počítačů do účtu úložiště je k dispozici pouze pomocí prostředí PowerShell ([AZ. RecoveryServices Module verze 1.4.5](https://www.powershellgallery.com/packages/Az.RecoveryServices/1.4.5)) nebo REST API (verze 2018-01-10 nebo 2016-08-10). [Přečtěte si, jak](./vmware-azure-disaster-recovery-powershell.md) nastavit replikaci pomocí příkazů PowerShellu.

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Jaké jsou výhody replikace na spravované disky?

[Informace o tom, jak](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery zjednodušuje zotavení po havárii pomocí spravovaných disků.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Můžu po ochraně počítače změnit typ spravovaného disku?

Ano, pro průběžné replikace můžete snadno [změnit typ spravovaného disku](../virtual-machines/windows/convert-disk-storage.md) . Před změnou typu zajistěte, aby na spravovaném disku nebyla vygenerována adresa URL sdíleného přístupového podpisu:

1. Přejděte na prostředek **spravovaného disku** na Azure Portal a ověřte, jestli máte v okně **Přehled** hlavičku adresy URL sdíleného přístupového podpisu.
1. Pokud je hlavička k dispozici, vyberte ji pro zrušení probíhajícího exportu.
1. Změňte typ disku během několika následujících minut. Pokud změníte typ spravovaného disku, počkejte, než se vygeneruje nové body obnovení Azure Site Recovery.
1. V budoucnu použijte nové body obnovení pro jakékoli testovací převzetí služeb při selhání nebo převzetí služeb při selhání.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Můžu přepínat replikaci ze spravovaných disků na nespravované disky?

Ne. Přepnutí ze spravovaného na nespravované není podporováno.

## <a name="replication"></a>Replikace

### <a name="what-are-the-replicated-vm-requirements"></a>Jaké jsou požadavky na replikovaný virtuální počítač?

[Přečtěte si další informace](vmware-physical-azure-support-matrix.md#replicated-machines) o požadavcích na podporu pro virtuální počítače VMware a fyzické servery.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak často je možné replikovat do Azure?

Replikace je nepřetržitá při replikaci virtuálních počítačů VMware do Azure.

### <a name="can-i-extend-replication"></a>Je možné rozšířenou replikaci?

Rozšířená nebo zřetězená replikace není podporována. Vyžádejte si tuto funkci ve [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Můžu provést počáteční replikaci offline?

Offline replikace není podporovaná. Vyžádejte si tuto funkci ve [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>Co je asrseeddisk?

Pro každý zdrojový disk se data replikují na spravovaný disk v Azure. Tento disk má předponu **asrseeddisk**. Ukládá kopii zdrojového disku a všech snímků bodů obnovení.

### <a name="can-i-exclude-disks-from-replication"></a>Můžu vyloučit disky z replikace?

Ano, disky můžete vyloučit.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Můžu replikovat virtuální počítače s dynamickými disky?

Dynamické disky je možné replikovat. Disk s operačním systémem musí být základní disk.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Pokud používám replikační skupiny pro konzistenci s více virtuálními počítači, mohu přidat nový virtuální počítač do existující replikační skupiny?

Ano, do existující replikační skupiny můžete přidat nové virtuální počítače, když pro ně povolíte replikaci. Naopak

- Po zahájení replikace nemůžete do existující replikační skupiny přidat virtuální počítač.
- Pro existující virtuální počítače nemůžete vytvořit replikační skupinu.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Můžu upravit virtuální počítače, které se replikují, přidáním nebo změnou velikosti disků?

Pro replikaci VMware do Azure můžete změnit velikost disku zdrojových virtuálních počítačů. Pokud chcete přidat nové disky, musíte disk přidat a znovu povolit ochranu pro virtuální počítač.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Můžu migrovat místní počítače na novou vCenter Server, aniž by to ovlivnilo probíhající replikaci?

Přečtěte si naše [pokyny](vmware-azure-manage-vcenter.md#migrate-all-vms-to-a-new-server) k migraci počítačů do nového vCenter.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Můžu replikovat do mezipaměti nebo cílového účtu úložiště, který má nakonfigurovanou virtuální síť (s bránami firewall Azure)?

Ne, Site Recovery nepodporuje replikaci do Azure Storage ve virtuálních sítích.

### <a name="what-is-the-frequency-of-generation-of-crash-consistent-recovery-points"></a>Jaká je frekvence generování bodů obnovení konzistentních z havárie?

Site Recovery generuje body obnovení konzistentní vzhledem k chybě každých 5 minut.

## <a name="component-upgrade"></a>Upgrade součásti

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Moje verze agenta služeb mobility nebo konfiguračního serveru je stará a můj upgrade se nezdařil. Co mám udělat?

Site Recovery se řídí modelem podpory N-4. [Přečtěte si další informace](./service-updates-how-to.md#support-statement-for-azure-site-recovery) o tom, jak upgradovat z velmi starých verzí.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Kde najdu poznámky k verzi a kumulativní aktualizace pro Azure Site Recovery?

[Přečtěte si o nových aktualizacích](site-recovery-whats-new.md)a [Získejte souhrnné informace](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Kde najdu informace o upgradu pro zotavení po havárii do Azure?

[Přečtěte si informace o upgradu](./service-updates-how-to.md#vmware-vmphysical-server-disaster-recovery-to-azure).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Musím pro každý upgrade restartovat zdrojové počítače?

Pro každý upgrade se doporučuje restart, ale není povinný. [Přečtěte si další informace](./service-updates-how-to.md#reboot-after-mobility-service-upgrade).

## <a name="configuration-server"></a>Konfigurační server

### <a name="what-does-the-configuration-server-do"></a>Jak funguje konfigurační server?

Konfigurační server spouští místní Site Recovery komponenty, včetně:

- Samotný konfigurační server. Server koordinuje komunikaci mezi místními komponentami a Azure a spravuje replikaci dat.
- Procesový Server, který funguje jako brána replikace. Tento server:
    1. Přijímá data replikace.
    2. Optimalizuje data ukládáním do mezipaměti, kompresí a šifrováním.
    3. Odesílá data do Azure Storage.
  Procesový Server taky nabízí nabízenou instalaci služby mobility na virtuální počítače a provádí automatické zjišťování místních virtuálních počítačů VMware.
- Hlavní cílový server, který zpracovává replikační data během navrácení služeb po obnovení z Azure.

[Přečtěte si další informace](vmware-azure-architecture.md) o součástech a procesech konfiguračního serveru.

### <a name="where-do-i-set-up-the-configuration-server"></a>Kde mám nastavit konfigurační server?

Pro konfigurační server potřebujete jeden vysoce dostupný místní virtuální počítač VMware. V případě zotavení po havárii fyzického serveru nainstalujte konfigurační server na fyzický počítač.

### <a name="what-do-i-need-for-the-configuration-server"></a>Co potřebuji pro konfigurační server?

Zkontrolujte [požadavky](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Můžu ručně nastavit konfigurační server, místo abyste použili šablonu?

Doporučujeme [vytvořit virtuální počítač konfiguračního serveru](vmware-azure-deploy-configuration-server.md) pomocí nejnovější verze šablony Open Virtualization Format (OVF). Pokud šablonu nemůžete použít (například pokud nemáte přístup k serveru VMware), [Stáhněte](physical-azure-set-up-source.md) si instalační soubor z portálu a nastavte konfigurační server.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Může být konfigurační server replikován do více než jedné oblasti?

Ne. Chcete-li provést replikaci do více než jedné oblasti, budete potřebovat konfigurační server v každé oblasti.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Můžu hostovat konfigurační server v Azure?

I když je to možné, virtuální počítač Azure, na kterém běží konfigurační server, musí komunikovat s místní infrastrukturou VMware a virtuálními počítači. Tato komunikace zvyšuje latenci a ovlivňuje probíhající replikaci.

### <a name="how-do-i-update-the-configuration-server"></a>Návody aktualizovat konfigurační server?

[Přečtěte si](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) , jak aktualizovat konfigurační server.

- Nejnovější informace o aktualizaci najdete na [stránce s aktualizacemi Azure](https://azure.microsoft.com/updates/?product=site-recovery).
- Nejnovější verzi si můžete stáhnout z portálu. Nebo můžete stáhnout nejnovější verzi konfiguračního serveru přímo z [webu Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Pokud má vaše verze více než čtyři verze starší než aktuální verze, přečtěte si téma [Podpora](./service-updates-how-to.md#support-statement-for-azure-site-recovery) pro pokyny k upgradu.

### <a name="should-i-back-up-the-configuration-server"></a>Mám zálohovat konfigurační server?

Doporučujeme, abyste provedli pravidelné naplánované zálohy konfiguračního serveru.

- Pro úspěšné navrácení služeb po obnovení musí v databázi konfiguračního serveru existovat virtuální počítač, který se nezdařil zpět.
- Konfigurační server musí být spuštěný a v připojeném stavu.
- [Přečtěte si další informace](vmware-azure-manage-configuration-server.md) o běžných úlohách správy konfiguračního serveru.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Můžu při nastavování konfiguračního serveru ručně stáhnout a nainstalovat MySQL?

Yes. Stáhněte si MySQL a umístěte ho do složky C:\Temp\ASRSetup. Pak ji nainstalujte ručně. Když nastavíte virtuální počítač konfiguračního serveru a přijmete podmínky, bude MySQL uveden jako **již nainstalovaný** při **Stažení a instalaci**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Můžu se vyhnout stažení MySQL, ale nechat si ji Site Recovery nainstalovat?

Yes. Stáhněte si instalační program MySQL a umístěte ho do složky C:\Temp\ASRSetup. Při nastavování virtuálního počítače konfiguračního serveru přijměte podmínky a vyberte **Stáhnout a nainstalovat**. Portál použije instalační program, který jste přidali k instalaci MySQL.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Můžu použít virtuální počítač konfiguračního serveru pro cokoli jiného?

Ne. Virtuální počítač používejte jenom pro konfigurační server.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Můžu naklonovat konfigurační server a použít ho k orchestraci?

Ne. Nastavte nový konfigurační server, aby nedocházelo k problémům s registrací.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Můžu změnit trezor, ve kterém je konfigurační server zaregistrovaný?

Ne. Po přidružení trezoru ke konfiguračnímu serveru ho nelze změnit. [Přečtěte si](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) o registraci konfiguračního serveru pomocí jiného trezoru.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Můžu použít stejný konfigurační server pro zotavení po havárii virtuálních počítačů VMware i fyzických serverů?

Ano, ale Upozorňujeme, že fyzický počítač se může vrátit zpátky jenom na virtuální počítač VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Kde můžu stáhnout heslo pro konfigurační server?

[Přečtěte](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) si, jak si stáhněte heslo.

### <a name="where-can-i-download-vault-registration-keys"></a>Kde můžu stahovat registrační klíče trezoru?

V trezoru Recovery Services v části Správa **infrastruktury Site Recovery** vyberte **konfigurační servery**  >  **Manage**. Pak na stránce **servery** vyberte **Stáhnout registrační klíč** a Stáhněte si soubor s přihlašovacími údaji trezoru.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>Může být pro ochranu více instancí vCenter použit jediný konfigurační server?

Ano, jeden konfigurační server může chránit virtuální počítače napříč několika servery vCenter.  Neexistuje žádné omezení počtu instancí vCenter, které lze přidat na konfigurační server, ale omezení pro počet virtuálních počítačů, které může chránit jediný konfigurační server, platí pro.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>Může jeden konfigurační server chránit víc clusterů v rámci vCenter?

Ano, Azure Site Recovery mohou chránit virtuální počítače v různých clusterech.

## <a name="process-server"></a>Procesový Server

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Proč se mi při povolování replikace nedá vybrat procesový Server?

Aktualizace ve verzích 9,24 a novějším teď zobrazují [stav procesového serveru, když povolíte replikaci](vmware-azure-enable-replication.md#enable-replication). Tato funkce pomáhá zabránit omezování procesů na serveru a minimalizaci používání špatných procesových serverů.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Pro správné informace o stavu Návody aktualizovat procesový Server na verzi 9,24 nebo novější?

Od [verze 9,24](service-updates-how-to.md#links-to-currently-supported-update-rollups)byla přidána další upozornění, která označují stav procesového serveru. [Aktualizujte Site Recovery komponenty na verzi 9,24 nebo novější](service-updates-how-to.md#links-to-currently-supported-update-rollups) , aby se vygenerovaly všechny výstrahy.

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>Jak si můžu zajistit vysokou dostupnost procesového serveru?

Když nakonfigurujete víc než jeden procesový Server, návrh poskytuje flexibilitu při přesunu chráněných počítačů z poškozeného procesového serveru na pracovní procesový Server. Přesun počítače z jednoho procesového serveru do druhého se musí iniciovat explicitně nebo ručně pomocí definovaných kroků uvedených v tomto tématu: [Přesun virtuálních počítačů mezi procesovým](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)serverem.

## <a name="failover-and-failback"></a>Převzetí služeb při selhání a navrácení služeb po obnovení

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Můžu použít místní procesový Server k navrácení služeb po obnovení?

Důrazně doporučujeme vytvořit procesový Server v Azure pro účely navrácení služeb po obnovení, abyste se vyhnuli latencím přenosu dat. Pokud jste navíc v případě oddělení zdrojové sítě virtuálních počítačů s přístupem k síti Azure na konfiguračním serveru vytvořili, je nutné použít procesový server vytvořený v Azure pro navrácení služeb po obnovení.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Můžu IP adresu uchovat při převzetí služeb při selhání?

Ano, IP adresu můžete zachovat při převzetí služeb při selhání. Před převzetím služeb při selhání se ujistěte, že jste zadali cílovou IP adresu v nastaveních **výpočty a síť** virtuálního počítače. Vypněte také počítače v době převzetí služeb při selhání, aby nedocházelo ke konfliktům IP adres během navrácení služeb po obnovení.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Můžu před převzetím služeb při selhání změnit cílovou velikost virtuálního počítače nebo typ virtuálního počítače?

Ano, typ nebo velikost virtuálního počítače můžete kdykoli změnit před převzetím služeb při selhání. Na portálu použijte nastavení **výpočty a síť** pro REPLIKOVANÝ virtuální počítač.

### <a name="how-far-back-can-i-recover"></a>Jak daleko zpátky můžu obnovit?

V případě VMware do Azure je nejstarším bodem obnovení, který můžete použít, 72 hodin.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Návody přístup k virtuálním počítačům Azure po převzetí služeb při selhání

Po převzetí služeb při selhání získáte přístup k virtuálním počítačům Azure přes zabezpečené připojení k Internetu, přes síť VPN typu Site-to-site nebo přes Azure ExpressRoute. Chcete-li se připojit, je nutné připravit několik věcí. [Přečtěte si další informace](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>Nedošlo k převzetí služeb při selhání proti datům?

Služba Azure je pro odolnost navržena. Site Recovery je navržena pro převzetí služeb při selhání sekundárnímu datovému centru Azure podle požadavků smlouvy o úrovni služeb Azure (SLA). Když dojde k převzetí služeb při selhání, zajistěte, aby vaše metadata a trezory zůstaly ve stejné geografické oblasti, kterou jste zvolili pro svůj trezor.

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?

[Převzetí služeb při selhání](site-recovery-failover.md) není automatické. Převzetí služeb při selhání spustíte jediným výběrem na portálu nebo můžete pomocí [PowerShellu](/powershell/module/az.recoveryservices) aktivovat převzetí služeb při selhání.

### <a name="can-i-fail-back-to-a-different-location"></a>Můžu navrátit služby po obnovení do jiného umístění?

Yes. Pokud převezmete služby při selhání do Azure, můžete navrátit služby po obnovení do jiného umístění, pokud není k dispozici původní. [Přečtěte si další informace](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Proč potřebuji připojení VPN nebo ExpressRoute se soukromým partnerským vztahem k navrácení služeb po obnovení?

Po navrácení služeb po obnovení z Azure se data z Azure zkopírují zpátky na místní virtuální počítač a vyžaduje se privátní přístup.


## <a name="automation-and-scripting"></a>Automatizace a skriptování

### <a name="can-i-set-up-replication-with-scripting"></a>Je možné nastavit replikaci pomocí skriptování?

Yes. Site Recovery pracovní postupy můžete automatizovat pomocí rozhraní REST API, PowerShellu nebo sady Azure SDK. [Přečtěte si další informace](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Výkon a kapacita

### <a name="can-i-throttle-replication-bandwidth"></a>Je možné šířku pásma replikace omezit?

Yes. [Přečtěte si další informace](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Další kroky

- [Zkontrolujte](vmware-physical-azure-support-matrix.md) požadavky na podporu.
- [Nastavit](vmware-azure-tutorial.md) Replikace z VMware do Azure