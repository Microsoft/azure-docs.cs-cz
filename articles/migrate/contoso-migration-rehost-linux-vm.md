---
title: Migrujte metody opětovného hostování a opětovným hostováním místní aplikace Linux k virtuálním počítačům Azure | Microsoft Docs
description: Zjistěte, jak Contoso opětovným hostováním Linux aplikaci místně migrací virtuálních počítačů Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: 8f039884ca0ea46c128078984d6cab6fd29ac9af
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220546"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms"></a>Migrace Contoso: opětovným hostováním místní aplikace Linux na virtuálních počítačích Azure

Tento článek ukazuje, jak je Contoso opětovného hostování aplikace místní službu založenou na Linux podpory (**osTicket**), pro virtuální počítače Azure IaaS.

Tento dokument je sedmý v řadě články, které dokumentů, jak fiktivní společnosti Contoso migruje jeho místních prostředků do cloudu Microsoft Azure. Řady obsahuje základní informace a sada scénáře, která ukazuje, jak nastavit infrastrukturu migrace a spustit různé typy migrace. Scénáře růst v složitost a jsme přidali další články v čase.

**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Poskytuje přehled strategie migrace společnosti Contoso, řady článku a ukázkových aplikací, které používáme. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Popisuje, jak Contoso připraví jeho místní a infrastrukturu Azure pro migraci. Stejnou infrastrukturu se používá pro všechny scénáře migrace Contoso. | K dispozici.
[Článek 3: Vyhodnocení místních prostředků](contoso-migration-assessment.md)  | Ukazuje, jak Contoso spouští posouzení své místní aplikace SmartHotel dvouvrstvá běžící ve VMware. Jejich vyhodnocení aplikace virtuálních počítačů s [Azure migrovat](migrate-overview.md) služby a aplikace databáze systému SQL Server s [Azure databáze migrace pomocníka](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | K dispozici.
[Článek 4: Metody opětovného hostování virtuální počítače Azure a spravované Instance SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Ukazuje, jak Contoso migruje SmartHotel aplikace do Azure. Jejich migraci virtuálních počítačů front-endu aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)a databáze aplikace pomocí [migrace databáze Azure](https://docs.microsoft.com/azure/dms/dms-overview) služby migrovat do spravované Instance SQL. | K dispozici.
[Článek 5: Pro virtuální počítače Azure opětovným hostováním](contoso-migration-rehost-vm.md) | Ukazuje, jak Contoso migrovat své aplikace SmartHotel virtuální počítače pouze pomocí Site Recovery.
[Článek 6: Opětovným hostováním do Azure virtuálních počítačů a skupin dostupnosti SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Ukazuje, jak Contoso migruje SmartHotel aplikace. Site Recovery používají k migraci aplikace virtuální počítače a migraci databáze službu, kterou chcete migrovat databázi aplikace do skupiny dostupnosti SQL Server. | K dispozici.
Článek 7: Opětovným hostováním Linux aplikace na virtuálních počítačích Azure (v tomto článku) | Ukazuje, jak Contoso migruje jejich osService Linux aplikace pomocí Azure Site Recovery.
[Článek 8: Opětovným hostováním Linux aplikace pro virtuální počítače Azure a Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Ukazuje, jak Contoso migruje aplikace osService Linux, pomocí Site Recovery pro migraci virtuálních počítačů a MySQL Workbench migrace (do instance serveru MySQL Azure. | K dispozici.

V tomto článku, bude Contoso migrovat dvouvrstvé **osTicket** aplikace spuštěna v systému Linux Apache MySQL PHP (svítilna) do Azure. Aplikace virtuální počítače proběhne pomocí služby Azure Site Recovery. Pokud chcete používat tuto aplikaci open source, si můžete stáhnout z [Githubu](https://github.com/osTicket/osTicket).

## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzce spolupracuje s jejich obchodním partnerům umožní pochopit, co chtějí dosáhnout s této migrace:

- **Adresa růst podniku**: Contoso roste a výsledkem je tlak na místních systémů a infrastruktury.
- **Omezit riziko**: podpory aplikační služby je důležité pro firmy Contoso. Chtějí ho přesunout do Azure s nulové riziko.
- **Rozšíření**: nechcete použít aplikaci nyní změnit. Jednoduše chtějí zajistěte, aby byl stabilní.


## <a name="migration-goals"></a>Migrace cíle

Tým cloudu Contoso má připnutý dolů cíle pro migraci, určit nejlepší metody migrace:

- Po migraci aplikaci v Azure měli stejné možnosti výkonu jako dnes ve svém prostředí VMWare místně.  Aplikace bude stále jako kritické v cloudu jako je na místě. 
- Contoso nechce investovat do této aplikace.  Je důležité pro jejich podnikání, ale v současné podobě jednoduše chtějí bezpečně ho přesunout do cloudu.
- Contoso nechce změňte model ops pro tuto aplikaci. Chtějí pracovat s ním v cloudu stejným způsobem, že je nyní mají.
- Contoso nechce změnit funkce aplikace. Změní pouze umístění aplikace.
- Po dokončení několika migrací aplikace systému Windows, Contoso chce Naučte se používat infrastrukturu se systémem Linux v Azure.

## <a name="proposed-architecture"></a>Navrhované architektura

V tomto scénáři:

- Aplikace je vrstvené mezi dva virtuální počítače (OSTICKETWEB a OSTICKETMYSQL).
- Virtuální počítače se nenachází v hostiteli VMware ESXi **contosohost1.contoso.com** (verze 6.5).
- Spravuje prostředí VMware vCenter Server verze 6.5 (**vcenter.contoso.com**), spuštěné na virtuálním počítači.
- Contoso má místního datového centra (**contoso-datacenter**), se řadič domény místní (**contosodc1**).
- Vzhledem k tomu, že aplikace je zatížení produkční, virtuálních počítačů v Azure se bude nacházet ve skupině prostředků produkční **ContosoRG**.
- Virtuální počítače bude migrovat na primární oblasti (východní USA 2) a umístěny v produkční síť (VNET-PRODUKČNÍMU-EUS2):
    - Webové virtuálního počítače se bude nacházet v podsíti front-endu (PRODUKČNÍMU-FE – EUS2).
    - Databázi virtuálního počítače se bude nacházet v podsíti databáze (PRODUKČNÍMU-DB-EUS2).
- Místní virtuální počítače v datovém centru společnosti Contoso bude vyřazena z provozu, po dokončení migrace.

![Architektura scénáře](./media/contoso-migration-rehost-linux-vm/architecture.png) 

## <a name="migration-process"></a>Proces migrace

Contoso migrovat následujícím způsobem:

1. Jako první krok Contoso nastaví Azure a místní infrastrukturu potřebnou pro nasazení Site Recovery.
2. Jakmile připravíte Azure a místní komponenty, jejich nastavit a zapnout replikaci pro virtuální počítače.
3. Po replikace funguje, se migrovat virtuální počítače podle jejich selhání do Azure.

![Proces migrace](./media/contoso-migration-rehost-linux-vm/migration-process.png)

### <a name="azure-services"></a>Služby Azure

**Služba** | **Popis** | **Náklady**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Služba orchestruje a spravuje migrace a zotavení po havárii pro virtuální počítače Azure a místní virtuální počítače a fyzické servery.  | Při replikaci do Azure se vám neúčtují poplatky za úložiště Azure.  Virtuální počítače Azure se vytvářejí a platit poplatky, když dojde k převzetí služeb při selhání. [Další informace](https://azure.microsoft.com/pricing/details/site-recovery/) o poplatky a ceny.

 
## <a name="prerequisites"></a>Požadavky

Zde je vám (a Contoso) potřebovat pro tento scénář.

**Požadavky** | **Podrobnosti**
--- | ---
**Předplatné Azure** | Měli jste již vytvořili předplatné během časná články této série. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud používáte stávající předplatné a nejste správce, budete muset ve spolupráci s správcem můžete přiřadit oprávnění vlastníka nebo přispěvatele.<br/><br/> Pokud potřebujete podrobnější oprávnění, přečtěte si [v tomto článku](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastrukturu Azure** | Contoso nastavit jejich infrastrukturu Azure, jak je popsáno v [infrastrukturu Azure pro migraci](contoso-migration-infrastructure.md).<br/><br/> Další informace o konkrétní [sítě](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) a [úložiště](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) požadavky pro Site Recovery.
**Místní servery** | Vaše místní vCenter server musí používat verzi 5.5, 6.0 nebo 6.5<br/><br/> Hostiteli ESXi verzí 5.5, 6.0 nebo 6.5<br/><br/> Jeden nebo více virtuálních počítačů VMware na hostiteli ESXi spuštěna.
**Místní virtuální počítače** | [Zkontrolujte počítače se systémem Linux](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) jsou podporovány pro migraci pomocí Site Recovery.<br/><br/> Ověřte podporované [Souborová služba a úložiště systémů Linux](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> Virtuální počítače, musí splňovat [požadavky pro Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Kroky scénáře

Zde je, jak Azure dokončí migrace:

> [!div class="checklist"]
> * **Krok 1: Příprava Azure Site Recovery**: vytvoření účtu úložiště Azure k ukládání replikovaných dat a vytvoření trezoru služeb zotavení.
> * **Krok 2: Příprava místní VMware Site Recovery**: Příprava účty používané pro virtuální počítač zjišťování a instalaci agenta a příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání.
> * **Krok 3: Replikace virtuálních počítačů**: nastavení zdrojové a cílové prostředí migrace, vytvořte zásadu replikace a zahájení replikace virtuálních počítačů do úložiště Azure.
> * **Krok 4: Migrace virtuálních počítačů pomocí Site Recovery**: se spustit převzetí služeb při selhání a ujistěte se, vše funguje a pak spustit úplné převzetí služeb při selhání k migraci virtuálních počítačů do Azure.


## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Krok 1: Příprava Azure pro službu Site Recovery

Contoso potřebuje několik komponent Azure Site Recovery:

- Převzetí služeb při selhání virtuální síť, ve kterém jsou umístěné prostředky (Contoso použije produkční síť VNet už nasazená)
- Nový účet úložiště Azure pro uložení replikovaná data. 
- Trezor služeb zotavení v Azure.

Contoso již vytvořili virtuální síť během [nasazení infrastruktury Azure](contoso-migration-infrastructure.md), takže jednoduše vytvořit účet úložiště a trezoru.

1. Contoso vytvoří účet úložiště Azure (contosovmsacc20180528) v oblasti Východ USA 2.

    - Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.
    - Jejich pomocí standardního úložiště a replikace LRS používáte účet obecné účely.

    ![Úložiště obnovení lokality](./media/contoso-migration-rehost-linux-vm/asr-storage.png)

2. Pomocí účtu sítě a úložiště na místě Contoso vytvoření trezoru (ContosoMigrationVault) a jeho umístění **ContosoFailoverRG** skupinu prostředků, v primární oblasti Východ USA 2.

    ![Trezor služby Recovery Services](./media/contoso-migration-rehost-linux-vm/asr-vault.png)


**Potřebujete další pomoc?**

[Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) nastavení Azure Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Krok 2: Příprava místní VMware Site Recovery

Contoso připraví na místní infrastrukturu VMware následujícím způsobem:

- Vytvoření účtu v systému vCenter server nebo vSphere ESXi hostiteli, a automatizovat zjišťování virtuálních počítačů.
- Vytvořte účet, který umožňuje automatickou instalaci služby Mobility na virtuální počítače VMware, který chcete replikovat.
- Příprava místní virtuální počítače, tak, aby se mohli přihlásit k virtuální počítače Azure, když jste vytvořili po migraci.


### <a name="prepare-an-account-for-automatic-discovery"></a>Příprava účtu pro automatické zjišťování

Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Automaticky zjistit virtuální počítače. Vyžaduje se alespoň účet jen pro čtení.
- Orchestrace replikace, převzetí služeb při selhání a navrácení služeb po obnovení. Je třeba účet, který můžete spustit operací, jako je vytváření a odebrání disků a zapnutí virtuálních počítačů.

Účet contoso nastavuje následujícím způsobem:

1. Contoso vytvoří roli na úrovni vCenter.
2. Tato role contoso poté přiřadí požadovaná oprávnění.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Služba Mobility je nutné nainstalovat na virtuální počítače Linux, který je migrace Contoso:

- Site Recovery můžete provést automatické nabízené instalace této součásti, když povolíte replikaci pro virtuální počítače.
- Pro automatickou nabízenou instalaci je nutné připravit účtu, který obnovení lokality budou používat pro přístup virtuálních počítačů.
- Podrobnosti o účty jsou vstup během instalace replikace. 
- Účet může být domény nebo místní účet s oprávněními k instalaci na virtuálních počítačích.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Po převzetí služeb při selhání do Azure Contoso chce být schopni se připojit k replikovaných virtuálních počítačů v Azure. K tomu je několik věcí, které potřebují:

- Pro přístup přes internet, umožňují SSH na Linux místní virtuální počítač před migrací.  Pro Ubuntu to může být dokončený, pomocí následujícího příkazu: **Sudo výstižný get ssh, nainstalujte -y**.
- Po spuštění migrace (převzetí služeb při selhání), měli zkontrolovat **spouštění diagnostiky** zobrazíte snímek virtuálního počítače.
- Pokud to nebude fungovat, měli zkontrolovat, že virtuální počítač používá a zkontrolujte tyto [tipy pro odstraňování potíží](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Potřebujete další pomoc?**

- [Další informace o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) vytvoření a přiřazení role pro automatické zjišťování.
- [Další informace o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) vytváření účtu pro nabízenou instalaci služby Mobility.


## <a name="step-3-replicate-the-on-premises-vms"></a>Krok 3: Replikovat místní virtuální počítače

Před jejich lze migrovat webu virtuálních počítačů do Azure, nastaví Contoso a umožňuje replikace.

### <a name="set-a-protection-goal"></a>Nastavit cíl ochrany

1. V úložišti, v části název trezoru (ContosoVMVault) nastavují cílem replikace (**Začínáme** > **Site Recovery** > **připravit infrastrukturu**.
2. Určí, že jejich počítačů se nacházejí na místních, že jsou virtuální počítače VMware, a že se chcete replikovat do Azure.
    ![Cílem replikace](./media/contoso-migration-rehost-linux-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potvrďte plánování nasazení

Chcete-li pokračovat, potvrzení, že jste dokončili plánování nasazení tak, že vyberete **Ano, mám to hotové**. Contoso jsou pouze při migraci jednoho virtuálního počítače v tomto scénáři a nepotřebujete plánování nasazení.

### <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Contoso je potřeba nakonfigurovat jejich zdrojové prostředí. K tomuto účelu se stáhnout šablonu OVF a použijte jej k nasazení Site Recovery konfigurační server jako vysoce dostupný, místní virtuální počítač VMware. Po konfiguraci serveru je spuštěný a funkční, se zaregistrovat v trezoru.

Konfigurační server běží počet součástí:

- Součást konfigurace serveru, který koordinuje komunikaci mezi místními a Azure a spravuje replikaci dat.
- Procesový server, který funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure.
- Procesový server také na všechny virtuální počítače, které chcete replikovat, nainstaluje službu mobility a automaticky vyhledá místní virtuální počítače VMware.

Contoso následujícím způsobem proveďte tyto kroky:

1. Stáhnou šablony OVF **Příprava infrastruktury** > **zdroj** > **konfigurační Server**.
    
    ![Stáhnout OVF](./media/contoso-migration-rehost-linux-vm/add-cs.png)

2. Importujte šablonu do VMware vytvořte virtuální počítač a nasadit virtuální počítač.

    ![Šablona OVF](./media/contoso-migration-rehost-linux-vm/vcenter-wizard.png)

3. Při zapnutí virtuální počítač poprvé, spustí se až do instalace produktu Windows Server 2016. Přijměte licenční smlouvu a zadejte heslo správce.
4. Po dokončení instalace jako správce se přihlaste do virtuálního počítače. Při prvním přihlášení ve výchozím nastavení spustí nástroj Azure obnovení lokality konfigurace.
5. V nástroji určí název pro registraci konfigurační server v trezoru.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání připojení k přihlášení k předplatnému Azure. Přihlašovací údaje musí zajišťovat přístup k trezoru, do kterého chcete konfigurační server zaregistrovat.

    ![Zaregistrujte konfigurační server](./media/contoso-migration-rehost-linux-vm/config-server-register2.png)

7. Nástroj provede několik úloh konfigurace a pak restartuje počítač.
8. Přihlášení k počítači znovu a automaticky spustí Průvodce konfigurací serveru správy.
9. V Průvodci vyberou síťový adaptér pro příjem přenosů replikace. Toto nastavení nelze změnit po dokončení své konfigurace.
10. Výběrem předplatné, skupinu prostředků a úložiště, ve kterém zaregistrujte konfigurační server.

    ![Trezoru](./media/contoso-migration-rehost-linux-vm/cswiz1.png) 

11. Se pak stáhněte a nainstalujte MySQL Server a VMWare PowerCLI. 
12. Po ověření že zadejte plně kvalifikovaný název domény nebo IP adresa vCenter server nebo vSphere hostitele. Ponechte výchozí port a zadejte popisný název pro vCenter server.
13. Určí účet vytvořený pro automatické zjišťování a přihlašovací údaje, které se má použít k automatické instalaci služby Mobility.

    ![vCenter](./media/contoso-migration-rehost-linux-vm/cswiz2.png)

14. Po dokončení registrace na portálu Azure Contoso ověří, že konfigurační server a VMware server jsou uvedené na **zdroj** stránky v úložišti. Zjišťování může trvat 15 minut nebo déle. 
15. Site Recovery pak připojí k VMware serverů a vyhledá virtuální počítače.

### <a name="set-up-the-target"></a>Nastavit cíl

Nyní Contoso nakonfiguruje nastavení replikace na cílový.

1. V **připravit infrastrukturu** > **cíl**, vyberou nastavení cíle.
2. Site Recovery zkontroluje, že je účet úložiště Azure a sítě v zadané cílové.

### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

Po zdroje a cíle jsou nastavení, je připravena vytvořit zásadu replikace Contoso.

1. V **připravit infrastrukturu** > **nastavení replikace** > **zásady replikace** >  **vytvořit a Přidružení**, vytvoří zásadu **ContosoMigrationPolicy**.
2. Používají výchozí nastavení:
    - **Prahová hodnota RPO**: výchozí hodnotu 60 minut. Tato hodnota určuje, jak často se tvoří body obnovení. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
    - **Uchování bodu obnovení**. Výchozí 24 hodin. Tato hodnota určuje, jak dlouho je okno uchování pro každý bod obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu.
    - **Frekvence snímkování konzistentní aplikace vzhledem**. Výchozí jednu hodinu. Tato hodnota určuje četnost, v němž jsou vytvořen snímky konzistentní s aplikacemi.
 
        ![Vytvoření zásady replikace](./media/contoso-migration-rehost-linux-vm/replication-policy.png)

5. Tato zásada se automaticky přidruží ke konfiguračnímu serveru. 

    ![Přidružení zásady replikace](./media/contoso-migration-rehost-linux-vm/replication-policy2.png)

**Potřebujete další pomoc?**

- Můžete si přečíst úplné návod všechny tyto kroky v [nastavit zotavení po havárii pro místní virtuální počítače VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Podrobné pokyny jsou k dispozici vám pomohou [nastavení zdrojového prostředí](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [nasazení konfigurační server](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), a [nakonfigurujete nastavení replikace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Další informace](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) o Azure hostovaného agenta pro Linux.

**Potřebujete další pomoc?**

- Můžete si přečíst úplné návod všechny tyto kroky v [nastavit zotavení po havárii pro místní virtuální počítače VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Podrobné pokyny jsou k dispozici vám pomohou [nastavení zdrojového prostředí](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [nasazení konfigurační server](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), a [nakonfigurujete nastavení replikace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Další informace](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) o Azure hostovaného agenta pro Linux.



### <a name="enable-replication-for-osticketweb"></a>Povolení replikace pro OSTICKETWEB

Teď můžete Contoso zahájení replikace **OSTICKETWEB** virtuálních počítačů.

1. V **replikujte aplikaci** > **zdroj** > **+ replikovat** vyberou nastavení zdroje.
2. Vyberou, že chcete povolit virtuální počítače, vyberte nastavení zdroje, včetně serveru vCenter a konfigurační server.

    ![Povolení replikace](./media/contoso-migration-rehost-linux-vm/enable-replication-source.png)

3. Určí nastavení target, včetně, skupinu prostředků a virtuální síť, ve kterém virtuální počítač Azure budou umístěné po převzetí služeb při selhání a účet úložiště, ve kterém se uloží replikovaná data.

     ![Povolení replikace](./media/contoso-migration-rehost-linux-vm/enable-replication2.png)

3. Vybere contoso **OSTICKETWEB** pro replikaci. 

    - V této fázi Contoso vybere pouze **OSTICKETWEB** vzhledem k tomu, že virtuální síť a podsíť musí být vybrána, a virtuální počítače nejsou ve stejné podsíti.
    - Site Recovery automaticky nainstaluje službu Mobility, když je povolená replikace pro virtuální počítač.

    ![Povolení replikace](./media/contoso-migration-rehost-linux-vm/enable-replication3.png)

4. Ve vlastnostech virtuálních počítačů společnosti Contoso vybere účet, který je používán serverem proces automaticky instalace služby Mobility na počítači.

     ![Služba Mobility](./media/contoso-migration-rehost-linux-vm/linux-mobility.png)

5. v **nastavení replikace** > **nakonfigurujete nastavení replikace**, že zkontrolujte, jestli zásady správné replikační použité a vyberte možnost **povolit replikaci**.
6.  Budou sledovat průběh replikace **úlohy**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.



### <a name="enable-replication-for-osticketmysql"></a>Povolení replikace pro OSTICKETMYSQL

Teď můžete Contoso zahájení replikace **OSTICKETMYSQL**.

1. V **replikujte aplikaci** > **zdroj** > **+ replikovat** vyberou nastavení zdroje a cíle.
2. Vybere contoso **OSTICKETMYSQL** pro replikaci a vybere účet, který chcete použít pro instalaci služby Mobility.

    ![Povolení replikace](./media/contoso-migration-rehost-linux-vm/mysql-enable.png)

3. Contoso platí stejné zásady replikace, který byl použit pro OSTICKETWEB a umožňuje replikace.  

**Potřebujete další pomoc?**

Můžete si přečíst úplné návod všechny tyto kroky v [povolit replikaci](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Krok 4: Migrovat virtuální počítače 

Spustit rychlou contoso testovací převzetí služeb při selhání a potom migrovat virtuální počítače.

### <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Spuštěná testovací převzetí služeb pomáhá zajistit, že vše funguje podle očekávání před migrací. 

1. Contoso spustí testovací převzetí služeb k nejnovějšímu dostupnému bodu v čase (**nejnovější zpracovat**).
2. Vyberou **vypnout počítač před zahájením převzetí služeb při selhání**tak, aby Site Recovery se pokusí vypnout zdrojový virtuální počítač před spuštěním převzetí služeb při selhání. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. 
3. Testovací převzetí služeb při selhání spouští: 
    - Kontrola předpokladů postupné a zkontrolujte, zda jsou všechny podmínky, požadována pro migraci na místě.
    - Převzetí služeb při selhání tato data zpracuje, aby se mohl vytvořit virtuální počítač Azure. Pokud je vybrán poslední bod obnovení, je z dat vytvořit bod obnovení.
    - Pomocí dat zpracovaných v předchozím kroku se vytvoří virtuální počítač Azure.
3. Po dokončení převzetí služeb při selhání se zobrazí na portálu Azure repliky virtuálního počítače Azure. Jejich zkontrolujte, zda je virtuální počítač odpovídající velikost, byl připojený k síti správné, a zda je spuštěna. 
4. Po ověření, jejich vyčištění převzetí služeb při selhání a zaznamenejte a uložte jakékoli připomínky.

### <a name="create-and-customize-a-recovery-plan"></a>Vytvářet a přizpůsobovat plán obnovení

 Po ověření, že testovací převzetí služeb fungovala podle očekávání, Contoso vytvořit plán obnovení pro migraci. 

- Plán obnovení určuje pořadí, ve které převzít služby při selhání, jak budou virtuální počítače Azure zapínají v Azure.
- Vzhledem k tomu, že chcete migrovat dvouvrstvé aplikace, že budete přizpůsobit plán obnovení tak, aby data virtuálního počítače (SQLVM) spustí před front-endu (WEBVM).


1. V **plány obnovení (služba Site Recovery)** > **+ plán obnovení**, vytvoření plánu a přidejte do ní virtuální počítače.

    ![Plán obnovení](./media/contoso-migration-rehost-linux-vm/recovery-plan.png)

2. Po vytvoření plánu, vyberou se pro přizpůsobení (**plány obnovení** > **OsTicketMigrationPlan** > **přizpůsobit**.
3.  Odeberou **OSTICKETWEB** z **1. skupina: spuštění**.  To zajistí, že první akci spuštění ovlivňuje **OSTICKETMYSQL** pouze.

    ![Obnovení skupiny](./media/contoso-migration-rehost-linux-vm/recovery-group1.png)

4.  V **+ skupiny** > **přidat chráněné položky**, zvyšují **OSTICKETWEB** k **2. skupina: spuštění**.  Contoso musí tyto v různých skupinách.

    ![Obnovení skupiny](./media/contoso-migration-rehost-linux-vm/recovery-group2.png)


### <a name="migrate-the-vms"></a>Migrovat virtuální počítače


Contoso je připraven ke spuštění převzetí služeb při selhání pro plán obnovení, pro migraci virtuálních počítačů.

1. Jejich vyberte plán > **převzetí služeb při selhání**.
2.  Výběrem převzetí služeb při selhání na nejnovější bod obnovení a určit, že Site Recovery se pokuste vypnout virtuální počítač na místě před spuštěním převzetí služeb při selhání. Průběh převzetí služeb při selhání může kliknout **úlohy** stránky.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm/failover1.png)

3. Během převzetí služeb při selhání vCenter Server vysílá příkazy Zastavit dva virtuální počítače běžící v hostiteli ESXi.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-linux-vm/vcenter-failover.png)

3. Po převzetí služeb při selhání Contoso ověřte, že virtuální počítač Azure zobrazuje podle očekávání na portálu Azure.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-linux-vm/failover2.png)  

3. Po ověření virtuálního počítače v Azure, po dokončení migrace na dokončení procesu migrace pro každý virtuální počítač. Zastavení replikace pro virtuální počítač a zastaví fakturace Site Recovery pro virtuální počítač.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-linux-vm/failover3.png)


### <a name="connect-the-vm-to-the-database"></a>Připojit virtuální počítač do databáze

Jako poslední krok v procesu migrace, Contoso aktualizovat připojovací řetězec aplikace tak, aby odkazoval na databázi aplikace spuštěné v **OSTICKETMYSQL** virtuálních počítačů. 

1. Provádění připojení SSH k **OSTICKETWEB** virtuálního počítače pomocí klienta Putty nebo jiného klienta SSH. Virtuální počítač je soukromé, aby se přihlásit pomocí privátní IP adresy.

    ![připojení k databázi](./media/contoso-migration-rehost-linux-vm/db-connect.png)  

    ![připojení k databázi](./media/contoso-migration-rehost-linux-vm/db-connect2.png)  

2. Potřebují k Ujistěte se, že **OSTICKETWEB** virtuálního počítače může komunikovat s **OSTICKETMYSQL** virtuálních počítačů. Konfigurace je aktuálně pevně zakódované s 172.16.0.43 místní IP adresu.

    **Před aktualizací**
    
    ![Aktualizujte IP](./media/contoso-migration-rehost-linux-vm/update-ip1.png)  

    **Po aktualizaci**
    
    ![Aktualizujte IP](./media/contoso-migration-rehost-linux-vm/update-ip2.png) 
    
3. Se restartovat službu s **systemctl restartujte apache2**.

    ![Restartování](./media/contoso-migration-rehost-linux-vm/restart.png) 

4. Nakonec se aktualizovat záznamy DNS pro **OSTICKETWEB** a **OSTICKETMYSQL**, na jednom z řadičů domény Contoso.

    ![Aktualizovat DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

    ![Aktualizovat DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

**Potřebujete další pomoc?**

- [Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) spuštěná testovací převzetí služeb. 
- [Další informace](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) vytvoření plánu obnovení.
- [Další informace o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) přebírání služeb při selhání do Azure.

## <a name="clean-up-after-migration"></a>Vyčištění po migraci

Pomocí nástroje Migrace dokončení vrstvy osTicket aplikace běží na virtuálních počítačích Azure.

Teď Contoso je potřeba udělat některé čištění:  

- Místní virtuální počítače odeberou z inventáře vCenter.
- Místní virtuální počítače se odebere z místní úlohy zálohování.
- Jejich aktualizovat své interní dokumentaci zobrazíte nové umístění a IP adres pro OSTICKETWEB a OSTICKETMYSQL.
- Zkontrolujte všechny prostředky, které pracují s virtuálními počítači a aktualizovat všechny relevantní nastavení nebo v dokumentaci tak, aby odrážely novou konfiguraci.
- Contoso použít službu Azure migrace s mapování závislostí pro vyhodnocení pro migraci virtuálních počítačů. Microsoft Monitoring Agent a závislostí agenta instalováni pro tento účel, z virtuálního počítače musí odebrat.

## <a name="review-the-deployment"></a>Zkontrolujte nasazení

Contoso s aplikace je nyní spuštěna, musí plně zprovoznit a zabezpečit jejich novou infrastrukturu.

### <a name="security"></a>Zabezpečení

Tým zabezpečení Contoso zkontrolujte OSTICKETWEB a OSTICKETMYSQLVMs určení jakékoli problémy se zabezpečením.

- Jejich Zkontrolujte skupiny zabezpečení sítě (Nsg) pro virtuální počítače k řízení přístupu. K zajištění, že pouze povolené k aplikaci přenos dat se používají skupiny Nsg.
- Zvažují také zabezpečení dat na disky virtuálních počítačů pomocí šifrování disku a Azure KeyVault.

[Další informace](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o postupy zabezpečení pro virtuální počítače.

### <a name="backups"></a>Zálohování

Contoso zálohování dat na virtuálních počítačích pomocí služby zálohování Azure. [Další informace](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licencování a cenově optimalizace

- Po nasazení prostředků, přiřadí Contoso Azure značky definovaným během [nasazení infrastruktury Azure](contoso-migration-infrastructure.md#set-up-tagging).
- Contoso má žádné licenční problémy s jejich Ubuntu servery.
- Contoso povolíte Azure náklady na správu licencované Cloudyn, dceřiné společnosti Microsoft. Je řešení pro správu více cloudové náklady, který vám pomůže využívat a spravovat Azure a dalším prostředkům cloudu.  [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o Azure náklady na správu. 


## <a name="next-steps"></a>Další postup

V tomto článku, který jsme vám ukázal, jak migrovat Contoso vrstvené aplikace místní služby podpory na dva virtuální počítače Linux na virtuální počítače Azure IaaS, pomocí Azure Site Recovery.

V další článek v řadě ukážeme jak Contoso migrace stejné služby podpory aplikaci do Azure. Tentokrát společnost Contoso využívá Site Recovery k migraci front-endu virtuálních počítačů pro aplikaci, a jejich migrovat databázi aplikace pomocí zálohování a obnovení do Azure databáze pro databázi MySQL, pomocí nástroje workbench MySQL. [Začínáme](contoso-migration-rehost-linux-vm-mysql.md).
