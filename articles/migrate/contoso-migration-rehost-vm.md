---
title: Opětovným hostováním aplikace Contoso pomocí migrace na virtuálních počítačích Azure s Azure Site Recovery | Microsoft Docs
description: Zjistěte, jak opětovným hostováním místní aplikace a služby s navýšení a shift migrací do Azure pro migraci místního počítače pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/30/2018
ms.author: raynew
ms.openlocfilehash: 11b5e2a408d3ba514753f3510b36fce02470c6e9
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825097"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Migrace Contoso: opětovným hostováním místní aplikace na virtuálních počítačích Azure


Tento článek ukazuje, jak Contoso opětovným hostováním jejich SmartHotel aplikaci v Azure, migrací aplikace virtuálních počítačů na virtuálních počítačích Azure.


Tento dokument je jedním z řady články, které ukazují, jak fiktivní společnosti Contoso migraci svých místních prostředků do cloudu Microsoft Azure. Řada obsahuje základní informace a scénáře, které ilustrují nastavení infrastruktury a migrace, vyhodnocování místních prostředků pro migraci a s určitými typy migrace. Scénáře růst v složitost a přidáme další články v čase.


**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
Článek 1: Přehled | Poskytuje přehled strategie migrace společnosti Contoso, řady článku a ukázkových aplikací, které používáme. | K dispozici.
Článek 2: Nasazení infrastruktury Azure | Popisuje, jak Contoso připraví jeho místní a infrastrukturu Azure pro migraci. Stejnou infrastrukturu se používá pro všechny scénáře migrace Contoso. | K dispozici.
Článek 3: Vyhodnocení místních prostředků  | Ukazuje, jak Contoso spouští posouzení své místní aplikace SmartHotel dvouvrstvá běžící ve VMware. Jejich vyhodnocení aplikace virtuálních počítačů s [Azure migrovat](migrate-overview.md) služby a aplikace databáze systému SQL Server s [Azure databáze migrace pomocníka](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | K dispozici.
Článek 4: Refaktorovat (navýšení a shift) na virtuálních počítačích Azure a instanci SQL spravované (v tomto článku) | Ukazuje, jak Contoso migruje SmartHotel aplikace do Azure. Jejich migraci virtuálních počítačů front-endu aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)a databáze aplikace pomocí [migrace databáze Azure](https://docs.microsoft.com/azure/dms/dms-overview) služby migrovat do spravované Instance SQL. | K dispozici.
Článek 5: Refaktorovat (navýšení a shift) na virtuálních počítačích Azure (v tomto článku) | Ukazuje, jak Contoso migrovat své aplikace SmartHotel virtuální počítače pouze pomocí Site Recovery.
Článek 6: Refaktorovat (navýšení a shift) na virtuálních počítačích Azure a skupin dostupnosti SQL Server | Ukazuje, jak Contoso migruje SmartHotel aplikace. Site Recovery používají k migraci aplikace virtuální počítače a migraci databáze službu, kterou chcete migrovat databázi aplikace do skupiny dostupnosti SQL Server. | K dispozici.
Článek 7: Refaktorovat (navýšení a shift) na virtuálních počítačích Azure a MySQL serveru Azure | Ukazuje, jak Contoso migruje SmartHotel aplikace virtuálních počítačů pomocí Site Recovery a MySQL Workbench migrace (zálohování a obnovení) do instance serveru MySQL Azure. | K dispozici.

V tomto článku bude Contoso migrovat dvouvrstvá Windows. NET SmartHotel aplikace běžící na virtuálních počítačích VMware, do Azure. Pokud chcete používat tuto aplikaci, je poskytována jako s otevřeným zdrojem a si můžete stáhnout z [githubu](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzce spolupracuje s jejich obchodním partnerům umožní pochopit, co chtějí dosáhnout s této migrace:

- **Adresa růst podniku**: Contoso roste a výsledkem je tlak na svých místních systémů a infrastruktury.
- **Omezit riziko**: The SmartHotel aplikace je důležité pro firmy Contoso. Chtějí ho přesunout do Azure s nulové riziko.
- **Rozšíření**: Contoso nechce upravit aplikaci. Jednoduše chtějí zajistěte, aby byl stabilní.


## <a name="migration-goals"></a>Migrace cíle

Tým cloudu Contoso má připnuli dolů cíle pro migraci. Tyto cíle se používají k určení nejlepší metody migrace:

- Po migraci aplikaci v Azure měli stejné možnosti výkonu jako dnes v prostředí VMware.  Aplikace bude stále jako kritické v cloudu jako je na místě. 
- Contoso nechce investovat do této aplikace.  Je důležité pro jejich podnikání, ale v současné podobě jednoduše chtějí bezpečně ho přesunout do cloudu.
- Contoso nechce změňte model ops pro tuto aplikaci. Chtějí pracovat s ním v cloudu stejným způsobem, že je nyní mají.
- Contoso není chcete změnit jakékoli funkce aplikace. Změní pouze umístění aplikace.

## <a name="proposed-architecture"></a>Navrhované architektura

Zde je aktuální prostředí

- Aplikace je vrstvené mezi dva virtuální počítače (**WEBVM** a **SQLVM**).
- Virtuální počítače se nenachází v hostiteli VMware ESXi **contosohost1.contoso.com** (verze 6.5).
- Spravuje prostředí VMware vCenter Server verze 6.5 (**vcenter.contoso.com**), spuštěné na virtuálním počítači.
- Contoso má místní datacenter (contoso-datacenter), s řadič domény místní (**contosodc1**).
- Místní virtuální počítače v datovém centru společnosti Contoso bude vyřazena z provozu, po dokončení migrace.

![Architektura scénáře](./media/contoso-migration-rehost-vm/architecture.png) 

## <a name="migration-process"></a>Proces migrace

Contoso, se bude migrovat na front-endu aplikace a databáze virtuálních počítačů k virtuálním počítačům Azure pomocí Site Recovery:

- Jako první krok budou budete Příprava a nastavit součásti Azure Site Recovery a příprava místní infrastruktury VMware.
- Už mají jejich [infrastrukturu Azure](contoso-migration-infrastructure.md) na místě, takže právě potřebují přidat několik komponent Azure speciálně pro Site Recovery.
- S všechno připravené můžou spustit replikaci virtuálních počítačů.
– Po povolení replikace a práci, se migrovat virtuální počítač po selhání do Azure.

![Proces migrace](./media/contoso-migration-rehost-vm/migraton-process.png) 



### <a name="azure-services"></a>Služby Azure

**Služba** | **Popis** | **Náklady**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Služba orchestruje a spravuje migrace a zotavení po havárii pro virtuální počítače Azure a místní virtuální počítače a fyzické servery.  | Při replikaci do Azure se vám neúčtují poplatky za úložiště Azure.  Virtuální počítače Azure se vytvářejí a platit poplatky, když dojde k převzetí služeb při selhání. [Další informace](https://azure.microsoft.com/pricing/details/site-recovery/) o poplatky a ceny.


## <a name="prerequisites"></a>Požadavky

Zde je, co vám (a Contoso) musí spustit v tomto scénáři.

**Požadavky** | **Podrobnosti**
--- | ---
**Předplatné Azure** | Měli jste již vytvořili předplatné během časná články této série. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud používáte stávající předplatné a nejste správce, budete muset ve spolupráci s správcem můžete přiřadit oprávnění vlastníka nebo přispěvatele.<br/><br/> Pokud potřebujete podrobnější oprávnění, přečtěte si [v tomto článku](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastrukturu Azure** | [Zjistěte, jak](contoso-migration-infrastructure.md) Contoso nastavení infrastruktury Azure.<br/><br/> Další informace o konkrétní [sítě](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) a [úložiště](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) požadavky pro Site Recovery.
**Místní servery** | Místní, že servery vCenter server musí používat verzi 5.5, 6.0 nebo 6.5<br/><br/> Hostitelích ESXi by měl běžet verzi 5.5, 6.0 nebo 6.5<br/><br/> Jeden nebo více virtuálních počítačů VMware by měl běžet na hostiteli ESXi.
**Místní virtuální počítače** | Virtuální počítače, musí splňovat [požadavky pro Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Kroky scénáře

Tady je způsob Contoso spuštění migrace:

> [!div class="checklist"]
> * **Krok 1: Příprava Azure Site Recovery**: uživatel vytvořit účet úložiště Azure pro uložení replikovaná data a trezoru služeb zotavení.
> * **Krok 2: Příprava místní VMware Site Recovery**: Příprava účty pro virtuální počítač zjišťování a instalaci agenta a příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání.
> * **Krok 3: Replikace virtuálních počítačů**: nastavení replikace a zahájení replikace virtuálních počítačů do úložiště Azure.
> * **Krok 4: Migrace virtuálních počítačů pomocí Site Recovery**: se spustit převzetí služeb při selhání a ujistěte se, vše funguje a pak spustit úplné převzetí služeb při selhání k migraci virtuálních počítačů do Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Krok 1: Příprava Azure pro službu Site Recovery

Tady jsou Azure součásti Contoso potřebuje k migraci virtuálních počítačů do Azure:

- Virtuální síť, ve kterém virtuální počítače Azure budou umístěné když jste vytvořili během převzetí služeb při selhání.
- Účet úložiště Azure pro uložení replikovaná data. 
- Trezor služeb zotavení v Azure.

Nastavené tyto následujícím způsobem:

1. Contoso již nastavení sítě můžete použít pro obnovení lokality při jejich [nasadit infrastrukturu Azure](contoso-migration-infrastructure.md)

    - Aplikace SmartHotel je produkční aplikace a virtuální počítače se budou migrovat do produkční sítě Azure (VNET-PRODUKČNÍMU-EUS2) v primární oblasti Východ USA 2.
    - Oba virtuální počítače budou umístěny ve skupině prostředků ContosoRG, který se používá pro produkční prostředky.
    - Front-endu aplikace virtuálních počítačů (WEBVM) bude migrovat do podsítě front-endu (PRODUKČNÍMU-FE – EUS2), v produkční sítě.
    - Databáze aplikace virtuálních počítačů (SQLVM) bude migrovat do databáze podsítě (PRODUKČNÍMU-DB-EUS2), v produkční sítě.

2. Contoso vytvoří účet úložiště Azure (contosovmsacc20180528) v primární oblasti.
    - Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.
    - Účet pro obecné účely, používají se standardní úložiště a LRS replikace. 

    ![Úložiště obnovení lokality](./media/contoso-migration-rehost-vm/asr-storage.png)

3. Pomocí účtu sítě a úložiště na místě Contoso nyní vytvoří trezoru služeb zotavení (ContosoMigrationVault) a umístí jej ve skupině prostředků ContosoFailoverRG v primární oblasti Východ USA 2.

    ![Trezor služby Recovery Services](./media/contoso-migration-rehost-vm/asr-vault.png)

**Potřebujete další pomoc?**

[Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) nastavení Azure Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Krok 2: Příprava místní VMware Site Recovery

Tady je co Contoso připraví na místě:

- Účet v systému vCenter server nebo vSphere ESXi hostiteli, a automatizovat zjišťování virtuálních počítačů.
- Účet, který umožňuje automatickou instalaci služby Mobility na virtuálních počítačích VMware. 
- Místní nastavení virtuálního počítače tak, aby Contoso může připojit k replikované virtuální počítače Azure po převzetí služeb při selhání.


### <a name="prepare-an-account-for-automatic-discovery"></a>Příprava účtu pro automatické zjišťování

Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Automaticky zjistit virtuální počítače. 
- Orchestraci replikace, převzetí služeb při selhání a navrácení služeb po obnovení pro virtuální počítače.
- Vyžaduje se alespoň účet jen pro čtení. Účet bude schopen spuštění operací, jako je například vytváření a odebrání disků a zapnutí virtuálních počítačů.

Účet contoso nastavuje následujícím způsobem:

1. Uživatel na úrovni vCenter vytvořit role.
2. Tato role se přiřadíte požadovaná oprávnění.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Služba Mobility musí být nainstalovaná na každém virtuálním počítači.

- Site Recovery můžete provést automatické nabízené instalace služby Mobility, když je povolená replikace virtuálních počítačů.
- Účet je povinný, tak, aby Site Recovery můžete přístup k virtuálním počítačům na nabízenou instalaci. Tento účet zadáte při replikaci.
- Účet může být v doméně nebo místní, s oprávněními k instalaci na virtuálních počítačích.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Po převzetí služeb při selhání Contoso chce připojit k virtuálním počítačům Azure. K tomu, že udělejte před migrací:

1. Pro přístup přes internet se:

 - Povolení protokolu RDP pro virtuální počítač na místě před převzetí služeb při selhání
 - Ujistěte se, že pro přidána pravidla TCP a UDP **veřejné** profilu.
 - Zkontrolujte, zda je povolen protokol RDP v **brány Windows Firewall** > **povolené aplikace** pro všechny profily.
 
2. Pro přístup prostřednictvím sítě site-to-site VPN že:

 - Povolení protokolu RDP na místním počítači.
 - Povolit RDP v **brány Windows Firewall** -> **povolené aplikace a funkce**, pro **domény a privátní** sítě.
 - Nastavit zásada SAN operačního systému na virtuální počítač místní **OnlineAll**.

Kromě toho při jejich spuštění převzetí služeb při selhání je muset zkontrolujte následující:

- Měla by existovat žádné Windows aktualizace čekající na vyřízení ve virtuálním počítači při spuštění převzetí služeb při selhání. Pokud existují, nebudou moci přihlásit do virtuálního počítače, až do dokončení aktualizace.
- Po převzetí služeb při selhání, můžete zkontrolovat **spouštění diagnostiky** zobrazíte snímek virtuálního počítače. Pokud to nepomůže, by měl ověřit, že virtuální počítač používá a zkontrolujte tyto [tipy pro odstraňování potíží](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Potřebujete další pomoc?**

- [Další informace o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) vytvoření a přiřazení role pro automatické zjišťování.
- [Další informace o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) vytváření účtu pro nabízenou instalaci služby Mobility.


## <a name="step-3-replicate-the-on-premises-vms"></a>Krok 3: Replikovat místní virtuální počítače

Ještě před jejich spuštěním migrace do Azure, Contoso je potřeba nastavit a zapnout replikaci.

### <a name="set-a-replication-goal"></a>Nastavte cíle replikace

1. V úložišti, v části název trezoru (ContosoVMVault) vyberou cílem replikace (**Začínáme** > **Site Recovery** > **připravit infrastrukturu** .
2. Určí, že jejich počítačů se nacházejí na místních, běžící ve VMware a replikaci do Azure.

    ![Cíl replikace](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potvrďte plánování nasazení

Chcete-li pokračovat, potvrzení, že dokončil plánování nasazení tak, že vyberete **Ano, mám to hotové**. V tomto scénáři Contoso migrujete pouze dva virtuální počítače a není nutné plánování nasazení.


### <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Contoso je potřeba nakonfigurovat jejich zdrojové prostředí. K tomuto účelu se stáhnout šablonu OVF a použijte jej k nasazení Site Recovery konfigurační server jako vysoce dostupný, místní virtuální počítač VMware. Po konfiguraci serveru je spuštěný a funkční, se zaregistrovat v trezoru tuto.

Konfigurační server běží počet součástí:

- Součást konfigurace serveru, který koordinuje komunikaci mezi místními a Azure a spravuje replikaci dat.
- Procesový server, který funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure.
- Procesový server také na všechny virtuální počítače, které chcete replikovat, nainstaluje službu mobility a automaticky vyhledá místní virtuální počítače VMware.



Contoso následujícím způsobem proveďte tyto kroky:

1. V úložišti, stáhnou šablony OVF **Příprava infrastruktury** > **zdroj** > **konfigurační Server**.
    
    ![Stáhnout OVF](./media/contoso-migration-rehost-vm/add-cs.png)

2. Šablona se naimportovat do VMware vytvoření a nasazení virtuálního počítače.

    ![Šablona OVF](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3.  Při zapnutí virtuální počítač poprvé, spustí se až do instalace produktu Windows Server 2016. Přijměte licenční smlouvu a zadejte heslo správce.
4. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce. Při prvním přihlášení ve výchozím nastavení spustí nástroj Azure obnovení lokality konfigurace.
5. V nástroji určí název zaregistrujte konfigurační server v trezoru.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure. Po navázání připojení k přihlášení k předplatnému Azure. Přihlašovací údaje musí mít přístup k úložišti, ve kterém se budete zaregistrujte konfigurační server.

    [Zaregistrujte konfigurační server](./media/contoso-migration-rehost-vm/config-server-register2.png)

7. Nástroj provede několik úloh konfigurace a pak restartuje počítač.
8. Přihlášení k počítači znovu a automaticky spustí Průvodce konfigurací serveru správy.
9. V Průvodci vyberou síťový adaptér pro příjem přenosů replikace. Toto nastavení nelze změnit po dokončení své konfigurace.
10. Výběrem předplatné, skupinu prostředků a úložiště, ve kterém zaregistrujte konfigurační server.
        ![Trezoru](./media/contoso-migration-rehost-vm/cswiz1.png) 

10. Jejich stáhněte a nainstalujte MySQL Server a VMWare PowerCLI. 
11. Po ověření že zadejte plně kvalifikovaný název domény nebo IP adresa vCenter server nebo vSphere hostitele. Ponechte výchozí port a zadejte popisný název pro server v Azure.
12. Určí účet vytvořený pro automatické zjišťování a přihlašovací údaje, které se používají k automatické instalaci služby Mobility. Pro počítače s Windows že účet potřebuje oprávnění místního správce na virtuálních počítačích.

    ![vCenter](./media/contoso-migration-rehost-vm/cswiz2.png)

7. Po dokončení registrace na portálu Azure Contoso dvojité ověří, že konfigurační server a VMware server jsou uvedené na **zdroj** stránky v úložišti. Zjišťování může trvat 15 minut nebo déle. 
8. Site Recovery pak připojí k VMware servery pomocí zadané nastavení a vyhledá virtuální počítače.

### <a name="set-up-the-target"></a>Nastavit cíl

Nyní Contoso určuje nastavení replikace na cílový.

1. V **připravit infrastrukturu** > **cíl**, vyberou nastavení cíle.
2. Site Recovery zkontroluje, že je účet úložiště Azure a sítě v zadané cílové umístění.

### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

Contoso teď můžete vytvořit zásady replikace.

1. V **připravit infrastrukturu** > **nastavení replikace** > **zásady replikace** >  **vytvořit a Přidružení**, vytvoří zásadu **ContosoMigrationPolicy**.
2. Používají výchozí nastavení:
    - **Prahová hodnota RPO**: výchozí hodnotu 60 minut. Tato hodnota určuje, jak často se tvoří body obnovení. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
    - **Uchování bodu obnovení**. Výchozí 24 hodin. Tato hodnota určuje, jak dlouho je okno uchování pro každý bod obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu.
    - **Frekvence snímkování konzistentní aplikace vzhledem**. Výchozí jednu hodinu. Tato hodnota určuje četnost, v němž jsou vytvořen snímky konzistentní s aplikacemi.

        ![Vytvoření zásady replikace](./media/contoso-migration-rehost-vm/replication-policy.png)

5. Tato zásada se automaticky přidruží ke konfiguračnímu serveru. 

    ![Přidružení zásady replikace](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>Povolení replikace pro WEBVM

S vše na místě můžete povolit Contoso replikace pro virtuální počítače. Začněte WebVM.

1. V **replikujte aplikaci** > **zdroj** > **+ replikovat** vyberou nastavení zdroje.
2. Indikuje, že chcete povolit virtuální počítače, vyberte vCenter server a konfigurační server.

    ![Povolení replikace](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. Výběrem nastavení target, včetně, skupinu prostředků a síť Azure a účet úložiště.

     ![Povolení replikace](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. Vybere contoso **WebVM** pro replikaci, ověří zásady replikace a umožňuje replikace.

    - V této fázi Contoso pouze vybere WEBVM, protože je třeba vybrat virtuální síť a podsíť a společnosti Contoso je umístění aplikace virtuální počítače v různých podsítích.
    - Site Recovery automaticky nainstaluje služba Mobility na virtuálním počítači, pokud je zapnutá replikace.

    ![Povolení replikace](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. Budou sledovat průběh replikace **úlohy**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.
6. V **Essentials** Contoso na portálu Azure můžete zobrazit strukturu pro virtuální počítače replikující se do Azure.


### <a name="enable-replication-for-sqlvm"></a>Povolení replikace pro SQLVM

Contoso můžete nyní spustit replikaci SQLVM počítače, pomocí stejný postup, jak je uvedeno výše.

1. Výběrem nastavení zdroje.

    ![Povolení replikace](./media/contoso-migration-rehost-vm/enable-replication1.png)

2. Poté určí nastavení cíle.

     ![Povolení replikace](./media/contoso-migration-rehost-vm/enable-replication2-sqlvm.png)

3. Vyberou SQLVM pro replikaci. 

    ![Povolení replikace](./media/contoso-migration-rehost-vm/enable-replication3-sqlvm.png)

4. Použít stejné zásady replikace, která byla použita pro WEBVM a povolit replikaci.

    ![Zobrazení infrastruktury](./media/contoso-migration-rehost-vm/essentials.png)

**Potřebujete další pomoc?**

- Můžete si přečíst úplné návod všechny tyto kroky v [nastavit zotavení po havárii pro místní virtuální počítače VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Podrobné pokyny jsou k dispozici vám pomohou [nastavení zdrojového prostředí](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [nasazení konfigurační server](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), a [nakonfigurujete nastavení replikace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Další informace o [povolení replikace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Krok 4: Migrovat virtuální počítače 

Contoso spustit Rychlý testovací převzetí služeb a potom úplné převzetí služeb při selhání k migraci virtuálních počítačů.

### <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Testovací převzetí služeb pomáhá zajistit, že vše funguje podle očekávání. 

1. Contoso spustí testovací převzetí služeb k nejnovějšímu dostupnému bodu v čase (**nejnovější zpracovat**).
2. Vyberou **vypnout počítač před zahájením převzetí služeb při selhání**tak, aby Site Recovery se pokusí vypnout zdrojový virtuální počítač před spuštěním převzetí služeb při selhání. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. 
3. Testovací převzetí služeb při selhání spouští: 

    - Kontrola předpokladů postupné a zkontrolujte, zda jsou všechny podmínky, požadována pro migraci na místě.
    - Převzetí služeb při selhání tato data zpracuje, aby se mohl vytvořit virtuální počítač Azure. Pokud jste vybrali nejnovější bod obnovení, vytvoří se z těchto dat nový.
    - Pomocí dat zpracovaných v předchozím kroku se vytvoří virtuální počítač Azure.
    
3. Po dokončení převzetí služeb při selhání se zobrazí na portálu Azure repliky virtuálního počítače Azure. Contoso kontroluje, zda virtuální počítač odpovídající velikost, připojení k síti správné a je spuštěná. 
4. Po ověření testu převzetí služeb, jejich vyčištění převzetí služeb při selhání a zaznamenejte a uložte jakékoli připomínky. 

### <a name="create-and-customize-a-recovery-plan"></a>Vytvářet a přizpůsobovat plán obnovení

 Po ověření, že testovací převzetí služeb fungovala podle očekávání, vytvoří Contoso plán obnovení pro migraci. 

- Plán obnovení určuje pořadí, ve které převzetí služeb při selhání proběhne a určuje, jak budou virtuální počítače Azure do online režimu v Azure.
- Vzhledem k tomu, že aplikace je dvouvrstvá, jejich přizpůsobit plán obnovení tak, aby data virtuálního počítače (SQLVM) spustí před front-endu (WEBVM).

1. V **plány obnovení (služba Site Recovery)** > **+ plán obnovení**, vytvoření plánu a přidejte do ní virtuální počítače.

    ![Plán obnovení](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. Po vytvoření plánu, přizpůsobení budou (**plány obnovení** > **SmartHotelMigrationPlan** > **přizpůsobit**).
2.  Odeberou WEBVM z **1. skupina: spuštění**.  Tím se zajistí, že první akci spuštění ovlivňuje pouze SQLVM.
3.  V **+ skupiny** > **přidat chráněné položky**, zvyšují WEBVM na 2. skupina: spuštění.  Virtuální počítače musí být v různých skupinách.


### <a name="migrate-the-vms"></a>Migrovat virtuální počítače


Contoso teď můžete spustit úplné převzetí služeb při selhání k dokončení migrace.

1. Plán obnovení vyberou > **převzetí služeb při selhání**.
2. Vyberou k převzetí služeb při selhání na nejnovější bod obnovení a obnovení lokality se pokuste vypnout virtuální počítač na místě před spuštěním převzetí služeb při selhání. Průběh převzetí služeb při selhání může kliknout **úlohy** stránky.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm/failover1.png)


3. Po převzetí služeb při selhání budou ověřte, že virtuální počítač Azure zobrazuje podle očekávání na portálu Azure.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm/failover2.png)  

3. Po ověření po dokončení migrace pro každý virtuální počítač. Zastavení replikace pro virtuální počítač a zastaví Site Recovery fakturace.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-vm/failover3.png)

**Potřebujete další pomoc?**

- [Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) spuštěná testovací převzetí služeb. 
- [Další informace](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) vytvoření plánu obnovení.
- [Další informace o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) přebírání služeb při selhání do Azure.

## <a name="clean-up-after-migration"></a>Vyčištění po migraci

Pomocí nástroje Migrace dokončení vrstvy SmartHotel aplikace běží na virtuálních počítačích Azure.

Nyní Contoso potřebuje k dokončení těchto kroků čištění:  

- Odeberte počítač WEBVM z inventáře vCenter.
- Odeberte počítač SQLVM z inventáře vCenter.
- Odeberte WEBVM a SQLVM z místní úlohy zálohování.
- Aktualizujte interní dokumentaci zobrazíte nové umístění a IP adresy pro virtuální počítače.
- Zkontrolujte všechny prostředky, které pracují s virtuálními počítači a aktualizovat všechny relevantní nastavení nebo v dokumentaci tak, aby odrážely novou konfiguraci.

## <a name="review-the-deployment"></a>Zkontrolujte nasazení

Pomocí aplikace nyní spuštěna Contoso nyní je plně zprovoznit a zabezpečení v Azure.

### <a name="security"></a>Zabezpečení

Tým zabezpečení Contoso projde virtuálních počítačích Azure, chcete-li zjistit jakékoli problémy se zabezpečením.

- K řízení přístupu, že Zkontrolujte skupiny zabezpečení sítě (Nsg) pro virtuální počítače. Skupiny Nsg se používají k zajištění, že pouze provoz povolený do aplikace můžete spojit.
- Také považují za zabezpečení dat na disku pomocí Azure Disk Encryption a KeyVault.

[Další informace](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o postupy zabezpečení pro virtuální počítače.

### <a name="backups"></a>Zálohování

Contoso se bude zálohovat data na virtuálních počítačích pomocí služby zálohování Azure. [Další informace](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licencování a cenově optimalizace

1. Contoso má existující licencování pro jejich virtuální počítače a bude využívat výhody hybridní Azure.  Převádějí stávající virtuální počítače Azure, využívat výhod této ceny.
2. Contoso povolíte Azure náklady na správu licencované Cloudyn, dceřiné společnosti Microsoft. Je řešení pro správu více cloudové náklady, které vám pomůžou využít a spravovat Azure a dalším prostředkům cloudu. [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o Azure náklady na správu. 

## <a name="conclusion"></a>Závěr

V tomto článku Contoso rehosted SmartHotel aplikaci v Azure pomocí migrace aplikace virtuálních počítačů k virtuálním počítačům Azure pomocí služby Site Recovery. 


## <a name="next-steps"></a>Další postup

V další článek v řadě jsme budete ukazují, jak Contoso opětovným hostováním SmartHotel aplikace front-endu virtuálního počítače v Azure pomocí služby Site Recovery a migrace databáze do skupiny dostupnosti Azure SQL AlwaysOn.

