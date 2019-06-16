---
title: Běžné dotazy týkající se VMware na zotavení po havárii Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Získejte odpovědi na běžné dotazy týkající se zotavení po havárii místních virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 05/30/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 59be8e0585f0bedcafc868ee42f5113509c9c4ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66417771"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Běžné dotazy týkající se VMware pro replikaci Azure

Tento článek obsahuje odpovědi na běžné dotazy, ke kterým může při nasazení zotavení po havárii virtuálních počítačů (VM) v místním VMware do Azure.

## <a name="general"></a>Obecné

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Co musím udělat pro zotavení po havárii virtuálního počítače VMware?

[Další informace o součástech zahrnutých](vmware-azure-architecture.md) zotavení po havárii virtuálních počítačů VMware.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Můžete pomocí Site Recovery můžete migrovat virtuální počítače VMware do Azure?

Ano. Kromě nastavení úplné zotavení po havárii pro virtuální počítače VMware pomocí Site Recovery, můžete také použít Site Recovery k migraci místních virtuálních počítačů VMware do Azure. V tomto scénáři replikovat místní virtuální počítače VMware do Azure Storage. Pak převezme služby při selhání z místního Azure. Po převzetí služeb při selhání jsou vaše aplikace a úlohy dostupné a spuštěné na virtuálních počítačích Azure. Proces je podobný jako při zakládání úplné havárii, s tím rozdílem, že při migraci můžete nemůžete navracet služby po obnovení z Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Oprávnění k vytvoření virtuálních počítačů musí Můj účet Azure?

Pokud si nejste správce předplatného, máte oprávnění replikace, které potřebujete. Pokud si nejste správce, potřebujete oprávnění k provedení těchto akcí:

- Vytvoření virtuálního počítače Azure v prostředku skupiny a virtuální sítě, kterou zadáte při konfiguraci Site Recovery.
- Zápis do vybraného účtu úložiště nebo spravovaný disk, na základě vaší konfigurace.

[Další informace](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) o požadovaných oprávněních.

### <a name="what-applications-can-i-replicate"></a>Jaké aplikace můžou replikovat?

Můžete replikovat jakoukoli aplikaci nebo úlohu spuštěnou na virtuálním počítači VMware, který splňuje [požadavky na replikaci](vmware-physical-azure-support-matrix.md#replicated-machines).

- Site Recovery podporuje replikaci se sledováním aplikací, tak, aby aplikace, můžete převzetí služeb při selhání a zpět do použitelného stavu se nezdařilo.
- Site Recovery se integruje s aplikacemi Microsoftu, například SharePoint, Exchange, Dynamics, SQL Server a Active Directory. Je také úzce spolupracuje s předními dodavateli včetně Oracle, SAP, IBM a Red Hat.

[Další informace](site-recovery-workload.md) o ochraně úloh.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Můžete použít licenci hostovaného operačního systému serveru v Azure?

Ano, můžete použít Microsoft Software Assurance zákazníci [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) uložit na licenční náklady na pro počítače s Windows serverem, které se migrují do Azure nebo použijte Azure pro zotavení po havárii.

## <a name="security"></a>Zabezpečení

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Jaký přístup k serverům VMware potřebuje Site Recovery?

Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Nastavení virtuálního počítače VMware s konfiguračním serveru Site Recovery.
- Automatické zjišťování virtuálních počítačů pro replikaci.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Jaký přístup pro virtuální počítače VMware potřebuje Site Recovery?

- Pokud chcete replikovat, musí mít virtuální počítač VMware službu Site Recovery Mobility nainstalovaná a spuštěná. Nástroj můžete nasadit ručně nebo můžete určit, že Site Recovery proveďte nabízenou instalaci služby při povolení replikace pro virtuální počítač.
- Během replikace virtuálních počítačů komunikovat s využitím Site Recovery následujícím způsobem:
    - Virtuální počítače komunikovat s konfiguračním serverem na port HTTPS 443 pro správu replikace.
    - Virtuální počítače odesílají data do procesového serveru na portu HTTPS 9443. (Toto nastavení můžete změnit.)
    - Pokud povolíte konzistenci pro více virtuálních počítačů, virtuálních počítačů vzájemně komunikovat přes port 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>Replikační data odeslaná do Site Recovery?

Ne, Site Recovery nezachycuje replikovaná data se nepodporuje a nemá žádné informace o co běží na virtuálních počítačích. Replikovaná data se přemisťují mezi hypervisory VMware a Azure Storage. Site Recovery nemá schopnost tato data zachytit. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.  

Site Recovery má certifikaci ISO 27001: 2013 a 27018, HIPAA, DPA a. Probíhá SOC2 a FedRAMP JAB.

## <a name="pricing"></a>Ceny

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Výpočet přibližné náklady pro zotavení po havárii VMware

Použití [cenové kalkulačky](https://aka.ms/asr_pricing_calculator) odhadnout náklady při používání Site Recovery.

Podrobný odhad nákladů, spusťte nástroj Plánovač nasazení pro [VMware](https://aka.ms/siterecovery_deployment_planner) a použít [sestavy odhadu nákladů](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Existuje rozdíl nákladů mezi replikace do úložiště nebo přímo na spravované disky?

Spravované disky se účtují trošku jinak z účtů úložiště. [Další informace](https://azure.microsoft.com/pricing/details/managed-disks/) o cenách služby managed Disks.

## <a name="mobility-service"></a>Služba Mobility

### <a name="where-can-i-find-the-mobility-service-installers"></a>Kde najdu instalace služby Mobility?

Instalační programy jsou ve složce %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository na konfiguračním serveru.

## <a name="how-do-i-install-the-mobility-service"></a>Jak nainstalovat službu Mobility?

Na každém virtuálním počítači, který chcete replikovat nainstalujte službu pomocí jedné z několika metod:

- [Nabízená instalace](vmware-physical-mobility-service-overview.md#push-installation)
- [Ruční instalace](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) z uživatelského rozhraní nebo prostředí PowerShell
- Nasazení pomocí nástroje pro nasazení, jako [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Spravované disky

### <a name="where-does-site-recovery-replicate-data-to"></a>Kde Site Recovery replikovat data?

Site Recovery replikuje místní virtuální počítače VMware a fyzických serverů do managed disks v Azure.

- Procesový server Site Recovery zapisuje protokoly replikace do účtu úložiště mezipaměti v cílové oblasti.
- Tyto protokoly se používají k vytvoření bodů obnovení na Azure managed disks, které mají předponu **asrseeddisk**.
- Pokud dojde k převzetí služeb při selhání, vyberete bod obnovení slouží k vytvoření nového spravovaného disku cíl. Tento spravovaný disk je připojený k virtuálnímu počítači v Azure.
- Virtuální počítače, které byly dřív replikoval do účtu úložiště (před březnem 2019) nejsou ovlivněny.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Můžete replikovat nové počítače do účtů úložiště?

Ne. Počínaje březnem 2019 na webu Azure Portal, můžete replikovat do Azure jenom spravované disky.

Replikace nových virtuálních počítačů na účet úložiště je k dispozici pouze s použitím prostředí PowerShell nebo rozhraní REST API (verze 2018-01-10 nebo 2016-08-10).

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Jaké jsou výhody replikaci na spravované disky?

[Zjistěte, jak](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery zjednodušuje zotavení po havárii se spravovanými disky.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Můžete změnit typ spravovaného disku, jakmile je chráněný počítač?

Ano, můžete snadno [změnit typ spravovaného disku](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) pro probíhající replikace. Před změnou typu, ujistěte se, že žádný sdílený přístupový podpis vygenerování adresy URL na spravovaný disk:

1. Přejděte **spravovaného disku** prostředků na portálu Azure portal a zkontrolujte, jestli máte banner URL sdíleného přístupového podpisu **přehled** okno.
1. Pokud se nachází na banner, vyberte ji a zrušit probíhající export.
1. Změňte typ disku během několika minut. Pokud změníte typ spravovaného disku, počkejte body čerstvé obnovení Azure Site Recovery vygenerovat.
1. Pomocí nové body obnovení pro všechny testovací převzetí služeb při selhání nebo převzetí služeb při selhání v budoucnu.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Můžete přepínat replikace z spravované disky do nespravované disky?

Ne. Přepnutí ze spravovaného do nespravovaného kódu není podporováno.

## <a name="replication"></a>Replikace

### <a name="what-are-the-replicated-vm-requirements"></a>Jaké jsou požadavky na replikovaných virtuálních počítačů?

[Další informace](vmware-physical-azure-support-matrix.md#replicated-machines) o požadavky na podporu pro virtuální počítače VMware a fyzické servery.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak často můžete replikovat do Azure?

Při replikaci virtuálních počítačů VMware do Azure je průběžné replikace.

### <a name="can-i-extend-replication"></a>Je možné rozšířit replikaci?

Rozšířená nebo zřetězená replikace není podporována. Žádost o tuto funkci [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Můžete provést offline počáteční replikaci?

Offline replikace se nepodporuje. Žádost o tuto funkci [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>Co je asrseeddisk?

Pro každý zdrojový disk data se replikují na spravovaný disk v Azure. Tento disk má předponu **asrseeddisk**. Uloží kopii zdrojový disk a všechny snímky bodu obnovení.

### <a name="can-i-exclude-disks-from-replication"></a>Můžete vyloučit disky z replikace?

Ano, můžete vyloučit disky.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Můžete replikovat virtuální počítače, které obsahují dynamické disky?

Dynamické disky se dají replikovat. Disk s operačním systémem musí být základní disk.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Pokud budu používat skupiny replikace konzistenci, můžu přidat nový virtuální počítač do existující skupiny replikace?

Ano, můžete přidat nové virtuální počítače do existující skupiny replikace, když povolíte replikaci pro ně. Ale:

- Virtuální počítač nelze přidat do existující skupiny replikace po replikaci.
- Nejde vytvořit replikační skupinu pro stávající virtuální počítače.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Můžete upravit virtuální počítače, které se replikují přidáním nebo změnou velikosti disků?

Pro replikaci VMware do Azure můžete upravit velikost disku. Pokud chcete přidat nové disky, musíte přidat disk a znovu povolit ochranu virtuálního počítače.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Můžu migrovat místní počítače do nového systému vCenter Server bez dopadu na probíhající replikaci?

Ne. Změna serveru VMware Vcenter nebo migrace bude mít vliv na probíhající replikaci. Nastavení Site Recovery do nového systému vCenter Server a znovu ji povolte pro počítače.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Můžete replikovat do mezipaměti nebo cílový účet úložiště, který má virtuální síť (s branami firewall Azure) nakonfigurovanou na něm?

Ne, Site Recovery nepodporuje replikaci do Azure Storage ve virtuálních sítích.

## <a name="component-upgrade"></a>Upgrade součásti

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Moje verze serveru agenta nebo konfigurace služby Mobility je starý, a tento upgrade se nezdařil. Co mám udělat?

Site Recovery se řídí model podpory N-4. [Další informace](https://aka.ms/asr_support_statement) o tom, jak upgradovat z velmi staré verze.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Kde lze najít v poznámkách k verzi a kumulativní aktualizace pro Azure Site Recovery?

[Další informace o nových aktualizacích](site-recovery-whats-new.md), a [získat informace o kumulativní](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Kde najdu informace o upgradu pro zotavení po havárii do Azure?

[Další informace o upgradu](https://aka.ms/asr_vmware_upgrades).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Je potřeba restartovat zdrojové počítače pro každý upgrade?

Restart je doporučená, ale není povinné pro každý upgrade. [Další informace](https://aka.ms/asr_vmware_upgrades).

## <a name="configuration-server"></a>Konfigurační server

### <a name="what-does-the-configuration-server-do"></a>Co dělá konfiguračního serveru?

Konfigurační server běží v místním součásti Site Recovery, včetně:

- Konfigurační server sám. Server koordinuje komunikaci mezi místní komponenty a Azure a spravuje replikaci dat.
- Procesový server, který funguje jako replikační brána. Tento server:
    1. Přijímá data replikace.
    2. Optimalizuje data pomocí ukládání do mezipaměti, komprese a šifrování.
    3. Odešle data do služby Azure Storage.
  Procesový server také provádí nabízenou instalaci služby Mobility na virtuální počítače a provádí automatické zjišťování místních virtuálních počítačů VMware.
- Hlavní cílový server, který zpracovává replikační data během navrácení služeb po obnovení z Azure.

[Další informace](vmware-azure-architecture.md) o součásti konfiguračního serveru a procesů.

### <a name="where-do-i-set-up-the-configuration-server"></a>Kde můžu nastavit konfigurační server?

Potřebujete jeden vysoce dostupný místní VMware virtuálního počítače pro konfigurační server. Pro zotavení po havárii fyzického serveru instalaci konfiguračního serveru na fyzický počítač.

### <a name="what-do-i-need-for-the-configuration-server"></a>Co musím udělat pro konfigurační server?

Zkontrolujte [požadavky](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Můžete ručně nastavit konfigurační server, namísto použití šablony?

Doporučujeme vám [vytvoření virtuálního počítače konfiguračního serveru](vmware-azure-deploy-configuration-server.md) pomocí nejnovější verze šablony virtuálního počítače formát OVF (Open). Pokud nemůžete použít šablonu (například, pokud nemáte přístup k serveru VMware), [Stáhnout](physical-azure-set-up-source.md) instalační soubor z portálu a nastavte konfigurační server.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Konfigurační server možná replikace do více než jedné oblasti?

Ne. Pokud chcete replikovat do více než jedné oblasti, musíte konfigurační server v jednotlivých oblastech.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Můžete hostovat konfigurační server v Azure?

I když je možné, virtuálnímu počítači Azure s konfiguračním serverem by potřebují komunikovat s virtuálními počítači a na místní infrastrukturu VMware. Tato komunikace zvyšuje latenci a má vliv na probíhající replikaci.

### <a name="how-do-i-update-the-configuration-server"></a>Jak můžu aktualizovat konfigurační server?

[Přečtěte si](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) jak aktualizovat konfigurační server.

- Informace o nejnovější aktualizaci najdete na [stránku aktualizace Azure](https://azure.microsoft.com/updates/?product=site-recovery).
- Nejnovější verzi si můžete stáhnout z portálu. Nebo si můžete stáhnout nejnovější verzi konfigurační server přímo [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Pokud více než čtyři verze starší než aktuální verze je verze, najdete v článku [podporují příkaz](https://aka.ms/asr_support_statement) pokyny k upgradu.

### <a name="should-i-back-up-the-configuration-server"></a>Byste zálohovat konfigurační server?

Doporučujeme pravidelných naplánovaných záloh konfiguračního serveru.

- Pro úspěšné navrácení služeb po obnovení musí existovat virtuální počítač se při navrácení služeb obnoví databázi konfiguračního serveru.
- Konfigurační server musí být spuštěn a v připojeném stavu.
- [Další informace](vmware-azure-manage-configuration-server.md) o běžných úloh správy konfigurace serveru.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Když mám jsem nastavíte konfigurační server, můžete I stáhnout a nainstalovat MySQL ručně?

Ano. Stáhněte si MySQL a umístěte ho do složky C:\Temp\ASRSetup. Pak nainstalujte ručně. Když nastavíte konfigurační server virtuálního počítače a přijměte podmínky, MySQL bude uvedeno **už nainstalovaná** v **stáhnout a nainstalovat**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Můžete vyhnout stahování MySQL ale nechat nainstalovat Site Recovery?

Ano. Stáhněte instalační program MySQL a umístěte ho do složky C:\Temp\ASRSetup. Když nastavíte konfigurační server VM, přijměte podmínky a vyberte **stáhnout a nainstalovat**. Portál použije instalační program, který jste přidali k instalaci MySQL.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Můžete použít konfigurační server virtuálního počítače na něco jiného?

Ne. Virtuální počítač používejte pouze pro konfigurační server.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Můžete naklonovat konfiguračního serveru a použít ho k orchestraci?

Ne. Abyste předešli problémům s registrace nastavení čerstvé konfiguračního serveru.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Můžu změnit trezor, ve které je zaregistrovaný konfigurační server?

Ne. Trezor je spojen s konfiguračním serverem, nelze změnit. [Přečtěte si](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) o registraci konfiguračního serveru pomocí jiného trezoru.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Můžete použít stejný konfigurační server pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů?

Ano, ale mějte na paměti, že fyzický počítač by bylo možné provést obnovení pouze virtuální počítač VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Kde lze stáhnout přístupové heslo pro konfigurační server?

[Přečtěte si](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) stažení přístupové heslo.

### <a name="where-can-i-download-vault-registration-keys"></a>Kde lze stáhnout registrační klíče trezoru?

V trezoru služby Recovery Services vyberte **konfigurační servery** v **infrastruktura Site Recovery** > **spravovat**. Potom v **servery**vyberte **stáhnout registrační klíč** ke stažení souboru s přihlašovacími údaji.

## <a name="process-server"></a>Procesový server

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Proč mi nejde vybrat procesový server při povolit replikaci?

Aktualizace pro verze 9,24 a novější zobrazování [stavu procesového serveru, když povolíte replikaci](vmware-azure-enable-replication.md#enable-replication). Tato funkce pomáhá zabránit omezování procesový server a minimalizovat použití není v pořádku procesových serverů.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Jak aktualizovat procesový server na verzi 9.24 nebo vyšší pro přesné zdravotních informací?

Počínaje [verze 9,24](service-updates-how-to.md#links-to-currently-supported-update-rollups), k označení stavu procesového serveru se přidaly další výstrahy. [Aktualizovat součásti Site Recovery na verzi 9.24 nebo novější] (service-updates-how-to.md#links-to-currently-supported-update-rollups tam) tak, aby všechny výstrahy jsou generovány.

## <a name="failover-and-failback"></a>Převzetí služeb při selhání a navrácení služeb po obnovení

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Můžete použít místní procesový server navrácení služeb po obnovení?

Důrazně doporučujeme vytvořit procesový server v Azure pro účely navrácení služeb po obnovení, aby se zabránilo latenci přenosu dat. Kromě toho v případě, že oddělené zdrojové síti virtuálních počítačů s Azure máte sítí na konfiguračním serveru, je nutné použít procesový server vytvořený v Azure pro navrácení služeb po obnovení.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Můžete zachovat IP adresu na převzetí služeb při selhání?

Ano, můžete zachovat IP adresu na převzetí služeb při selhání. Ujistěte se, že zadáte cílovou IP adresu v **výpočty a síť** nastavení pro virtuální počítač před převzetí služeb při selhání. Také vypněte počítače v okamžiku převzetí služeb při selhání aby nedocházelo ke konfliktům IP adresa během navrácení služeb po obnovení.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Můžete změnit cílovou velikost virtuálního počítače nebo typ virtuálního počítače před převzetí služeb při selhání?

Ano, můžete kdykoli před převzetí služeb při selhání změnit typy a velikosti virtuálního počítače. Na portálu pro použití **výpočty a síť** nastavení pro replikovaný virtuální počítač.

### <a name="how-far-back-can-i-recover"></a>Jak daleko mohou obnovit?

Nejstarší bod obnovení, které můžete použít pro replikaci z VMware do Azure, je 72 hodin.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Jak získám přístup virtuálních počítačů Azure po převzetí služeb při selhání?

Po převzetí služeb při selhání můžete přístup k virtuálním počítačům Azure přes zabezpečené internetové připojení, přes síť site-to-site VPN nebo přes Azure ExpressRoute. Pro připojení, musíte připravit několik věcí. [Další informace](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>Jsou převzetím služeb při selhání data odolná?

Služba Azure je pro odolnost navržena. Site Recovery je navržena pro převzetí služeb při selhání do sekundárního datacentra Azure, podle potřeb Azure smlouvu o úrovni služeb (SLA). Pokud dojde k převzetí služeb při selhání, zajišťujeme, že metadata a trezory zůstávají ve stejné geografické oblasti, kterou jste zvolili pro svůj trezor.

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?

[Převzetí služeb při selhání](site-recovery-failover.md) neprobíhá automaticky. Začnete tím, že jeden výběr na portálu převzetí služeb při selhání, nebo můžete použít [Powershellu](/powershell/module/az.recoveryservices) k aktivaci převzetí služeb při selhání.

### <a name="can-i-fail-back-to-a-different-location"></a>Můžu navrátit služby zpět do jiného umístění?

Ano. Pokud převzetí služeb při selhání do Azure, můžete navrátit služby zpět do jiného umístění Pokud původní není k dispozici. [Další informace](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Proč potřebujete síť VPN nebo ExpressRoute s privátní partnerské vztahy pro navrácení služeb po obnovení?

Když převezmete služby zpět z Azure, se kopírují data z Azure zpět na vaše místní virtuální počítač a soukromý přístup je povinný.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>Můžu změnit velikost virtuálního počítače Azure po převzetí služeb při selhání?

Ne, nelze změnit velikost nebo typ cílový virtuální počítač po převzetí služeb.

## <a name="automation-and-scripting"></a>Automatizací a skriptováním

### <a name="can-i-set-up-replication-with-scripting"></a>Můžete nastavit replikaci pomocí skriptování?

Ano. Pomocí rozhraní Rest API, Powershellu nebo sady Azure SDK můžete automatizovat postupy workflow Site Recovery. [Další informace](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Výkon a kapacita

### <a name="can-i-throttle-replication-bandwidth"></a>Je možné omezovat šířku pásma replikace?

Ano. [Další informace](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Další postup

- [Kontrola](vmware-physical-azure-support-matrix.md) požadavky na podporu.
- [Nastavit](vmware-azure-tutorial.md) replikaci z VMware do Azure replikace.
