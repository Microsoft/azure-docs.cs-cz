---
title: Opětovným hostováním aplikaci aplikační Contoso Linux služby podpory k Azure a MySQL v Azure | Microsoft Docs
description: Zjistěte, jak Contoso opětovném hostování aplikace Linux místní migrací virtuálních počítačích Azure a MySQL v Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/10/2018
ms.author: raynew
ms.openlocfilehash: 4367bf7cb02bb6a1e343dc3fb171be731e15c32b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300707"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms-and-azure-mysql"></a>Migrace Contoso: opětovným hostováním místní aplikace Linux na virtuálních počítačích Azure a MySQL v Azure

Tento článek ukazuje, jak Contoso jsou opětovného hostování své místní dvouvrstvá Linux služby podpory aplikace (osTicket), a migrujte jej do Azure a MySQL v Azure.

Tento dokument je osmou v řadě články, které ukazují, jak fiktivní společnosti Contoso migruje jeho místních prostředků do cloudu Microsoft Azure. Řada obsahuje základní informace a scénáře, které ukazují, jak nastavit infrastrukturu migrace a spustit různé typy migrace. Scénáře růst v složitost a jsme přidali další články v čase.

**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Poskytuje přehled strategie migrace společnosti Contoso, řady článku a ukázkových aplikací, které používáme. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Popisuje, jak Contoso připraví jeho místní a infrastrukturu Azure pro migraci. Stejnou infrastrukturu se používá pro všechny scénáře migrace Contoso. | K dispozici.
[Článek 3: Vyhodnocení místních prostředků](contoso-migration-assessment.md)  | Ukazuje, jak Contoso spouští posouzení své místní aplikace SmartHotel dvouvrstvá běžící ve VMware. Jejich vyhodnocení aplikace virtuálních počítačů s [Azure migrovat](migrate-overview.md) služby a aplikace databáze systému SQL Server s [Azure databáze migrace pomocníka](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | K dispozici.
[Článek 4: Metody opětovného hostování virtuální počítače Azure a spravované Instance SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Ukazuje, jak Contoso migruje SmartHotel aplikace do Azure. Jejich migraci virtuálních počítačů webové aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)a databáze aplikace pomocí [migrace databáze Azure](https://docs.microsoft.com/azure/dms/dms-overview) služby migrovat do spravované Instance SQL. | K dispozici.
[Článek 5: Pro virtuální počítače Azure opětovným hostováním](contoso-migration-rehost-vm.md) | Ukazuje, jak migrovat Contoso jejich SmartHotel do virtuálních počítačů Azure IaaS, pomocí služby Site Recovery.
[Článek 6: Opětovným hostováním do Azure virtuálních počítačů a skupin dostupnosti SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Ukazuje, jak Contoso migruje SmartHotel aplikace. Site Recovery používají k migraci aplikace virtuální počítače a migraci databáze službu, kterou chcete migrovat databázi aplikace do skupiny dostupnosti SQL Server. | K dispozici.
[Článek 7: Opětovným hostováním Linux aplikace pro virtuální počítače Azure](contoso-migration-rehost-linux-vm.md) | Ukazuje, jak Contoso migruje své aplikace Linux osTicket virtuální počítače Azure IaaS pomocí Azure Site Recovery.
Článek 8: Opětovným hostováním Linux aplikace na virtuálních počítačích Azure a MySQL serveru Azure (v tomto článku) | Ukazuje, jak Contoso migruje aplikace osTicket Linux. Používají k migraci na instanci serveru MySQL Azure Site Recovery pro migraci virtuálních počítačů a MySQL Workbench. | K dispozici.

V tomto článku Contoso migruje dvouvrstvá Linux Apache MySQL PHP (svítilna) služby podpory aplikace (osTicket) do Azure. Pokud chcete používat tuto aplikaci open source, si můžete stáhnout z [Githubu](https://github.com/osTicket/osTicket).



## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzce spolupracuje s jejich obchodním partnerům umožní pochopit, co chtějí dosáhnout:

- **Adresa růst podniku**: Contoso roste a výsledkem je tlak na místních systémů a infrastruktury.
- **Omezit riziko**: podpory aplikační služby je důležité pro firmy Contoso. Chtějí ho přesunout do Azure s nulové riziko.
- **Rozšíření**: Contoso nechce aplikaci nyní změnit. Jednoduše chtějí zachovat stabilní.


## <a name="migration-goals"></a>Migrace cíle

Tým cloudu Contoso má připnutý dolů cíle pro migraci, aby bylo možné určit nejlepší metody migrace:

- Po migraci aplikaci v Azure měli stejné možnosti výkonu jako dnes ve svém prostředí VMWare místně.  Aplikace bude stále jako kritické v cloudu jako je na místě. 
- Contoso nechce investovat do této aplikace.  Je důležité pro jejich podnikání, ale v současné podobě jednoduše chtějí bezpečně ho přesunout do cloudu.
- Po dokončení několika migrací aplikace systému Windows, Contoso chce Naučte se používat infrastrukturu se systémem Linux v Azure.
- Contoso chce minimalizovat úlohy správy databáze po přesunutí aplikace do cloudu.

## <a name="proposed-architecture"></a>Navrhované architektura

V tomto scénáři:

- Aplikace je vrstvené mezi dva virtuální počítače (OSTICKETWEB a OSTICKETMYSQL).
- Virtuální počítače se nenachází v hostiteli VMware ESXi **contosohost1.contoso.com** (verze 6.5).
- Spravuje prostředí VMware vCenter Server verze 6.5 (**vcenter.contoso.com**), spuštěné na virtuálním počítači.
- Contoso má místní datacenter (contoso-datacenter), s řadič domény místní (**contosodc1**).
- Webovou aplikaci vrstvy na OSTICKETWEB se budou migrovat do virtuálního počítače Azure IaaS.
- Databáze aplikace se budou migrovat do databáze Azure pro službu MySQL PaaS.
- Vzhledem k tomu, že se jejich migraci produkční zatížení, prostředky se bude nacházet ve skupině prostředků produkční **ContosoRG**.
- Prostředky se replikují do primární oblasti (východní USA 2) a umístěny v produkční síť (VNET-PRODUKČNÍMU-EUS2):
    - Webové virtuálního počítače se bude nacházet v podsíti front-endu (PRODUKČNÍMU-FE – EUS2).
    - Instance databáze se bude nacházet v podsíti databáze (PRODUKČNÍMU-DB-EUS2).
- Databáze aplikace se budou migrovat do Azure MySQL pomocí nástrojů MySQL.
- Místní virtuální počítače v datovém centru společnosti Contoso bude vyřazena z provozu, po dokončení migrace.


![Architektura scénáře](./media/contoso-migration-rehost-linux-vm-mysql/architecture.png) 


## <a name="migration-process"></a>Proces migrace

Contoso dokončí proces migrace následujícím způsobem:

Pokud chcete migrovat na webu virtuálních počítačů:

1. Jako první krok Contoso nastaví Azure a místní infrastrukturu potřebnou pro nasazení Site Recovery.
2. Jakmile připravíte Azure a místní komponenty, jejich nastavit a zapnout replikaci pro virtuální počítač web.
3. Nahoru a spuštění po replikaci jejich migraci virtuálního počítače pomocí selhání do Azure.

Migrace databáze:

1. Contoso zřídí instance databáze MySQL v Azure.
2. Nastavit MySQL workbench a zálohujte databázi místně.
3. Pak se obnovit databázi z místního zálohování do Azure.

![Proces migrace](./media/contoso-migration-rehost-linux-vm-mysql/migration-process.png) 


### <a name="azure-services"></a>Služby Azure

**Služba** | **Popis** | **Náklady**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Služba orchestruje a spravuje migrace a zotavení po havárii pro virtuální počítače Azure a místní virtuální počítače a fyzické servery.  | Při replikaci do Azure se vám neúčtují poplatky za úložiště Azure.  Virtuální počítače Azure se vytvářejí a platit poplatky, když dojde k převzetí služeb při selhání. [Další informace](https://azure.microsoft.com/pricing/details/site-recovery/) o poplatky a ceny.
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) | Databáze je založena na modul MySQL serveru open source. Poskytuje plně spravovaná, připravené pro podniky komunity databáze MySQL, jako služba pro vývoj aplikací a nasazení. 

 
## <a name="prerequisites"></a>Požadavky

Pokud chcete spustit tento scénář vám (a Contoso), zde je co byste měli mít.

**Požadavky** | **Podrobnosti**
--- | ---
**Předplatné Azure** | Měli jste již vytvořili předplatné během časná články této série. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud používáte stávající předplatné a nejste správce, budete muset ve spolupráci s správcem můžete přiřadit oprávnění vlastníka nebo přispěvatele.<br/><br/> Pokud potřebujete podrobnější oprávnění, přečtěte si [v tomto článku](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infrastrukturu Azure** | Contoso nastavit jejich infrastrukturu Azure, jak je popsáno v [infrastrukturu Azure pro migraci](contoso-migration-infrastructure.md).<br/><br/> Další informace o konkrétní [sítě](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) a [úložiště](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) požadavky pro Site Recovery.
**Místní servery** | Vaše místní vCenter server musí používat verzi 5.5, 6.0 nebo 6.5<br/><br/> Hostiteli ESXi verzí 5.5, 6.0 nebo 6.5<br/><br/> Jeden nebo více virtuálních počítačů VMware na hostiteli ESXi spuštěna.
**Místní virtuální počítače** | [Kontrola požadavků na virtuální počítač s Linuxem](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) jsou podporovány pro migraci pomocí Site Recovery.<br/><br/> Ověřte podporované [Souborová služba a úložiště systémů Linux](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> Virtuální počítače, musí splňovat [požadavky pro Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Kroky scénáře

Zde je, jak Azure dokončí migrace:

> [!div class="checklist"]
> * **Krok 1: Příprava Azure Site Recovery**: vytvoření účtu úložiště Azure k ukládání replikovaných dat a vytvoření trezoru služeb zotavení.
> * **Krok 2: Příprava místní VMware Site Recovery**: Příprava účty pro virtuální počítač zjišťování a instalaci agenta a příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání.
 * **Krok 3: poskytnutí databáze]**: V Azure, zřízení instance databáze MySQL na Azure.
> * **Krok 4: Replikace virtuálních počítačů**: Konfigurace Site Recovery zdrojové a cílové prostředí, nastavte zásady replikace a zahájení replikace virtuálních počítačů do úložiště Azure.
> * **Krok 5: Migrace databáze**: nastavení migrace nástroje MySQL.
> * **Krok 6: Migrace virtuálních počítačů pomocí Site Recovery**: Spusťte převzetí služeb při selhání a ujistěte se, vše funguje a pak spusťte úplné převzetí služeb při selhání k migraci virtuálních počítačů do Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Krok 1: Příprava Azure pro službu Site Recovery

Contoso potřebuje několik komponent Azure Site Recovery:

- Převzetí služeb při selhání virtuální síť, ve kterém jsou umístěné prostředky (Contoso použije produkční síť VNet už nasazená).
- Nový účet úložiště Azure pro uložení replikovaná data. 
- Trezor služeb zotavení v Azure.

Contoso již vytvořili virtuální síť během [nasazení infrastruktury Azure](contoso-migration-infrastructure.md), takže jednoduše vytvořit účet úložiště a trezoru.


1. Contoso vytvoří účet úložiště Azure (**contosovmsacc20180528**) v oblasti Východ USA 2.

    - Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.
    - Společnost Contoso využívá účet obecné účely s standardní úložiště a LRS replikace.

    ![Úložiště obnovení lokality](./media/contoso-migration-rehost-linux-vm-mysql/asr-storage.png)

3. Pomocí účtu sítě a úložiště na místě Contoso vytvoří úložiště (ContosoMigrationVault) a umístí jej do **ContosoFailoverRG** skupinu prostředků, v primární oblasti Východ USA 2.

    ![Trezor služby Recovery Services](./media/contoso-migration-rehost-linux-vm-mysql/asr-vault.png)

**Potřebujete další pomoc?**

[Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) nastavení Azure Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Krok 2: Příprava místní VMware Site Recovery

Contoso připraví na místní infrastrukturu VMware následujícím způsobem:

- Vytvoří účet na serveru vCenter server, k automatizaci zjišťování virtuálních počítačů.
- Vytvoří účet, který umožňuje automatickou instalaci služby Mobility na virtuální počítače VMware, který chcete replikovat.
- Připraví místní virtuální počítače, tak, aby mohli připojit k virtuálním počítačům Azure, když jste vytvořili po migraci.


### <a name="prepare-an-account-for-automatic-discovery"></a>Příprava účtu pro automatické zjišťování

Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Automaticky zjistit virtuální počítače. Vyžaduje se alespoň účet jen pro čtení.
- Orchestrace replikace, převzetí služeb při selhání a navrácení služeb po obnovení. Je třeba účet, který můžete spustit operací, jako je vytváření a odebrání disků a zapnutí virtuálních počítačů.

Účet contoso nastavuje následujícím způsobem:

1. Contoso vytvoří roli na úrovni vCenter.
2. Tato role contoso poté přiřadí požadovaná oprávnění.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Služba Mobility musí být nainstalovaná na každý virtuální počítač, který Contoso chce migrovat.

- Site Recovery můžete provést automatické nabízené instalace této součásti, když povolíte replikaci pro virtuální počítače.
- Pro automatickou instalaci. Site Recovery potřebuje účet s oprávněními pro přístup k virtuálnímu počítači. 
- Podrobnosti o účtu se vstup během instalace replikace. 
- Účet může být domény nebo místní účet, dokud má oprávnění pro instalaci.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Po převzetí služeb při selhání do Azure Contoso chce být schopni se připojit k virtuálním počítačům Azure. K tomu, které potřebují udělat několik věcí: 

- Pro přístup přes internet, umožňují SSH na Linux místní virtuální počítač před migrací.  Pro Ubuntu to může být dokončený, pomocí následujícího příkazu: **Sudo výstižný get ssh, nainstalujte -y**.
- Po převzetí služeb při selhání, by se mělo zjistit **spouštění diagnostiky** zobrazíte snímek virtuálního počítače.
- Pokud to nepomůže, je potřeba ověřit, že virtuální počítač používá a zkontrolujte tyto [tipy pro odstraňování potíží](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

**Potřebujete další pomoc?**

- [Další informace o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) vytvoření a přiřazení role pro automatické zjišťování.
- [Další informace o](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) vytváření účtu pro nabízenou instalaci služby Mobility.


## <a name="step-3-provision-azure-database-for-mysql"></a>Krok 3: Zřízení Azure databáze pro databázi MySQL

Zřizuje contoso MySQL databáze instance v primární oblasti Východ USA 2.

1. Na portálu Azure jejich vytvoření databáze Azure pro prostředek MySQL. 

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-1.png)

2. Zvyšují název **contosoosticket** pro databázi Azure. Zvyšují databázi ke skupině prostředků produkční **ContosoRG**a zadejte přihlašovací údaje pro ni.
3. Místní databázi MySQL je verzi 5.7, takže jejich výběru této verze pro kompatibilitu. Používají výchozí velikostí, které se shodují jejich požadavky na databázi.

     ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-2.png)

4. Pro **možnosti zálohování redundance**, vybere Contoso používat **geograficky redundantní**. Tato možnost umožňuje, aby obnovit databázi v jejich sekundární oblasti střed USA, když dojde k výpadku. Tato možnost se můžete konfigurovat pouze při zřizování databáze.

     ![Redundance](./media/contoso-migration-rehost-linux-vm-mysql/db-redundancy.png)

4. V **virtuální sítě. PRODUKČNÍMU EUS2** sítě > **koncové body služby**, si přidat koncový bod služby (databáze podsítě) pro službu SQL.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-3.png)

5. Po přidání podsítě, uživatel vytvořit pravidlo virtuální sítě, které umožňuje přístup z podsítě databáze v produkční sítě.

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-4.png)


## <a name="step-4-replicate-the-on-premises-vms"></a>Krok 4: Replikovat místní virtuální počítače

Před jejich lze migrovat webu virtuálních počítačů do Azure, nastaví Contoso a umožňuje replikace.

### <a name="set-a-protection-goal"></a>Nastavit cíl ochrany

1. V úložišti, v části název trezoru (ContosoVMVault) nastavují cílem replikace (**Začínáme** > **Site Recovery** > **připravit infrastrukturu**.
2. Určí, že jejich počítačů se nacházejí na místních, že jsou virtuální počítače VMware, a že se chcete replikovat do Azure.

    ![Cíl replikace](./media/contoso-migration-rehost-linux-vm-mysql/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Potvrďte plánování nasazení

Chcete-li pokračovat, potvrzení, že jste dokončili plánování nasazení tak, že vyberete **Ano, mám to hotové**. Contoso jsou pouze při migraci jednoho virtuálního počítače v tomto scénáři a nepotřebujete plánování nasazení.

### <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Contoso je potřeba nakonfigurovat jejich zdrojové prostředí. To provedete pomocí šablony OVF jejich nasazení Site Recovery konfigurace serveru jako s vysokou dostupností, místní virtuální počítač VMware. Po konfiguraci serveru je spuštěný a funkční, se zaregistrovat v trezoru.

Konfigurační server běží počet součástí:

- Součást konfigurace serveru, který koordinuje komunikaci mezi místními a Azure a spravuje replikaci dat.
- Procesový server, který funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do úložiště Azure.
- Procesový server také na všechny virtuální počítače, které chcete replikovat, nainstaluje službu mobility a automaticky vyhledá místní virtuální počítače VMware.

Contoso následujícím způsobem proveďte tyto kroky:


1. Stáhnou šablony OVF **Příprava infrastruktury** > **zdroj** > **konfigurační Server**.
    
    ![Stáhnout OVF](./media/contoso-migration-rehost-linux-vm-mysql/add-cs.png)

2. Importujte šablonu do VMware vytvořte virtuální počítač a nasadit virtuální počítač.

    ![Šablona OVF](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-wizard.png)

3. Při zapnutí virtuální počítač poprvé, spustí se až do instalace produktu Windows Server 2016. Přijměte licenční smlouvu a zadejte heslo správce.
4. Po dokončení instalace se přihlaste k virtuálnímu počítači jako správce. Při prvním přihlášení ve výchozím nastavení spustí nástroj Azure obnovení lokality konfigurace.
5. V nástroji určí název pro registraci konfigurační server v trezoru.
6. Nástroj zkontroluje, jestli se virtuální počítač může připojit k Azure.
7. Po navázání připojení k přihlášení k předplatnému Azure. Přihlašovací údaje musí mít přístup k úložišti, ve kterém se budete zaregistrujte konfigurační server.

    ![Zaregistrujte konfigurační server](./media/contoso-migration-rehost-linux-vm-mysql/config-server-register2.png)

8. Nástroj provede několik úloh konfigurace a pak restartuje počítač.
9. Přihlášení k počítači znovu a automaticky spustí Průvodce konfigurací serveru správy.
10. V Průvodci vyberou síťový adaptér pro příjem přenosů replikace. Toto nastavení nelze změnit po dokončení své konfigurace.
11. Výběrem předplatné, skupinu prostředků a úložiště, ve kterém zaregistrujte konfigurační server.

    ![Trezoru](./media/contoso-migration-rehost-linux-vm-mysql/cswiz1.png) 

12. Nyní stáhněte a nainstalujte MySQL Server a VMWare PowerCLI. 
13. Po ověření že zadejte plně kvalifikovaný název domény nebo IP adresa vCenter server nebo vSphere hostitele. Ponechte výchozí port a zadejte popisný název pro vCenter server.
14. Zadejte jejich účet vytvořený pro automatické zjišťování a přihlašovací údaje, které budou používat Site Recovery můžete automaticky nainstalovat službu Mobility. 

    ![vCenter](./media/contoso-migration-rehost-linux-vm-mysql/cswiz2.png)

14. Po dokončení registrace na portálu Azure Contoso ověří, že konfigurační server a VMware server jsou uvedené na **zdroj** stránky v úložišti. Zjišťování může trvat 15 minut nebo déle. 
15. Vše na místě Site Recovery se připojuje k servery VMware a vyhledá virtuální počítače.

### <a name="set-up-the-target"></a>Nastavit cíl

Nyní Contoso vstupy cílit nastavení replikace.

1. V **připravit infrastrukturu** > **cíl**, vyberou nastavení cíle.
2. Site Recovery zkontroluje, že je účet úložiště Azure a sítě v zadané cílové.


### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

Zdroj a cíl nastavit je připravena vytvořit zásadu replikace Contoso.

1. V **připravit infrastrukturu** > **nastavení replikace** > **zásady replikace** >  **vytvořit a Přidružení**, vytvoří zásadu **ContosoMigrationPolicy**.
2. Používají výchozí nastavení:
    - **Prahová hodnota RPO**: výchozí hodnotu 60 minut. Tato hodnota určuje, jak často se tvoří body obnovení. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
    - **Uchování bodu obnovení**. Výchozí 24 hodin. Tato hodnota určuje, jak dlouho je okno uchování pro každý bod obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu.
    - **Frekvence snímkování konzistentní aplikace vzhledem**. Výchozí jednu hodinu. Tato hodnota určuje četnost, v němž jsou vytvořen snímky konzistentní s aplikacemi.
 
        ![Vytvoření zásady replikace](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy.png)

5. Tato zásada se automaticky přidruží ke konfiguračnímu serveru. 

    ![Přidružení zásady replikace](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy2.png)


**Potřebujete další pomoc?**

- Můžete si přečíst úplné návod všechny tyto kroky v [nastavit zotavení po havárii pro místní virtuální počítače VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Podrobné pokyny jsou k dispozici vám pomohou [nastavení zdrojového prostředí](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [nasazení konfigurační server](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), a [nakonfigurujete nastavení replikace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Další informace](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) o Azure hostovaného agenta pro Linux.

### <a name="enable-replication-for-the-web-vm"></a>Povolení replikace pro virtuální počítač Web

Teď můžete Contoso zahájení replikace **OSTICKETWEB** virtuálních počítačů.

1. V **replikujte aplikaci** > **zdroj** > **+ replikovat** vyberou nastavení zdroje.
2. Indikují, že chtějí povolit virtuální počítače a vyberte nastavení zdroje, včetně serveru vCenter a konfigurační server.

    ![Povolení replikace](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication-source.png)

3. Teď určit nastavení cíle. Jedná se o skupinu prostředků a sítě, ve kterém virtuální počítač Azure budou umístěné po převzetí služeb při selhání a účet úložiště, ve kterém se uloží replikovaná data. 

     ![Povolení replikace](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication2.png)

3. Vybere contoso **OSTICKETWEB** pro replikaci. 

    ![Povolení replikace](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication3.png)

4. Ve vlastnostech virtuálního počítače budou vyberte účet, který se má použít k automatické instalaci služby Mobility do virtuálního počítače.

     ![Služba Mobility](./media/contoso-migration-rehost-linux-vm-mysql/linux-mobility.png)

5. v **nastavení replikace** > **nakonfigurujete nastavení replikace**, že zkontrolujte, jestli zásady správné replikační použité a vyberte možnost **povolit replikaci**. Služba Mobility se automaticky nainstaluje.
6.  Budou sledovat průběh replikace **úlohy**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.


**Potřebujete další pomoc?**

Můžete si přečíst úplné návod všechny tyto kroky v [povolit replikaci](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-5-migrate-the-database"></a>Krok 5: Migrace databáze

Contoso bude migrovat databázi pomocí zálohování a obnovení, pomocí nástrojů MySQL. Jejich nainstalujte MySQL Workbench, zálohovat databázi z OSTICKETMYSQL a obnovte ji do databáze Azure pro Server databáze MySQL.

### <a name="install-mysql-workbench"></a>Instalace aplikace MySQL Workbench

1. Contoso kontroly [požadavky a soubory ke stažení MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. Instalaci MySQL Workbench pro Windows v souladu s [pokyny k instalaci](https://dev.mysql.com/doc/workbench/en/wb-installing.html).
3. V MySQL Workbench vytvoří připojení databáze MySQL na OSTICKETMYSQL. 

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench1.png)

4. Se exportovat databázi jako **osticket**, do místního souboru úplný a samostatný.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench2.png)

5. Po databázi byly zálohovány místně, vytvoří připojení k databázi Azure pro instanci databáze MySQL.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench3.png)

6. Contoso teď můžete importovat (obnovení) databáze v instanci Azure MySQL, z samostatný soubor. Nové schéma (osticket) se vytvoří pro instanci.

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench4.png)

## <a name="step-6-migrate-the-vms-with-site-recovery"></a>Krok 6: Migrace virtuálních počítačů pomocí Site Recovery

Spustit rychlou contoso testovací převzetí služeb při selhání a potom migrovat virtuální počítač.

### <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Spuštěná testovací převzetí služeb pomáhá ověřte, zda vše funguje podle očekávání, před migrací. 

1. Contoso spustí testovací převzetí služeb k nejnovějšímu dostupnému bodu v čase (**nejnovější zpracovat**).
2. Vyberou **vypnout počítač před zahájením převzetí služeb při selhání**tak, aby Site Recovery se pokusí vypnout zdrojový virtuální počítač před spuštěním převzetí služeb při selhání. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. 
3. Testovací převzetí služeb při selhání spouští: 

    - Kontrola předpokladů postupné a zkontrolujte, zda jsou všechny podmínky, požadována pro migraci na místě.
    - Převzetí služeb při selhání tato data zpracuje, aby se mohl vytvořit virtuální počítač Azure. Pokud jste vybrali nejnovější bod obnovení, vytvoří se z těchto dat nový.
    - Pomocí dat zpracovaných v předchozím kroku se vytvoří virtuální počítač Azure.

3. Po dokončení převzetí služeb při selhání se zobrazí na portálu Azure repliky virtuálního počítače Azure. Contoso kontroluje, zda je virtuální počítač odpovídající velikost, byl připojený k síti správné, a zda je spuštěna. 
4. Po ověření, jejich vyčištění převzetí služeb při selhání a zaznamenejte a uložte jakékoli připomínky.

### <a name="migrate-the-vm"></a>Migraci virtuálního počítače

Pokud chcete migrovat virtuální počítač, Contoso vytvoří plán obnovení, který obsahuje virtuální počítač a selhání plán přes Azure.

1. Contoso vytvoří plán a přidá **OSTICKETWEB** k němu.

    ![Plán obnovení](./media/contoso-migration-rehost-linux-vm-mysql/recovery-plan.png)

2. Spuštění převzetí služeb při selhání v plánu. Vyberte nejnovější bod obnovení a určit, že Site Recovery se pokuste vypnout virtuální počítač na místě před spuštěním převzetí služeb při selhání. Průběh převzetí služeb při selhání může kliknout **úlohy** stránky.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-linux-vm-mysql/failover1.png)

3. Během převzetí služeb při selhání vCenter Server vysílá příkazy Zastavit dva virtuální počítače běžící v hostiteli ESXi.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-failover.png)

4. Po převzetí služeb při selhání Contoso ověřuje, že virtuální počítač Azure se zobrazí podle očekávání na portálu Azure.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-linux-vm-mysql/failover2.png)  

5. Po zkontrolování virtuálního počítače, po dokončení migrace. Zastavení replikace pro virtuální počítač a zastaví fakturace Site Recovery pro virtuální počítač.

    ![Převzetí služeb při selhání](./media/contoso-migration-rehost-linux-vm-mysql/failover3.png)

**Potřebujete další pomoc?**

- [Další informace o](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) spuštěná testovací převzetí služeb. 
- [Další informace](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) vytvoření plánu obnovení.
- [Další informace o](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) přebírání služeb při selhání do Azure.


### <a name="connect-the-vm-to-the-database"></a>Připojit virtuální počítač do databáze

Jako poslední krok v procesu migrace Contoso aktualizovat připojovací řetězec aplikace tak, aby odkazoval na databázi Azure pro databázi MySQL. 

1. Provádění připojení SSH pro virtuální počítač OSTICKETWEB pomocí klienta Putty nebo jiného klienta SSH. Virtuální počítač je soukromé, aby se přihlásit pomocí privátní IP adresy.

    ![připojení k databázi](./media/contoso-migration-rehost-linux-vm-mysql/db-connect.png)  

    ![připojení k databázi](./media/contoso-migration-rehost-linux-vm-mysql/db-connect2.png)  

2. Se aktualizovat nastavení tak, aby **OSTICKETWEB** virtuálního počítače může komunikovat s **OSTICKETMYSQL** databáze. Konfigurace je aktuálně pevně zakódované s 172.16.0.43 místní IP adresu.

    **Před aktualizací**
    
    ![Aktualizujte IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip1.png)  

    **Po aktualizaci**
    
    ![Aktualizujte IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip2.png) 
    
    ![Aktualizujte IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip3.png) 

3. Se restartovat službu s **systemctl restartujte apache2**.

    ![Restartování](./media/contoso-migration-rehost-linux-vm-mysql/restart.png) 

4. Nakonec se aktualizovat záznamy DNS pro **OSTICKETWEB**, na jednom z řadičů domény Contoso.

    ![Aktualizovat DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 


##  <a name="clean-up-after-migration"></a>Vyčištění po migraci

Pomocí nástroje Migrace dokončení vrstvy osTicket aplikace běží na virtuálních počítačích Azure.

Teď Contoso je potřeba provést následující akce: 
- Odebrání virtuálních počítačů VMware vCenter inventáře
- Odeberte místní virtuální počítače z místní úlohy zálohování.
- Aktualizace dokumentace interní zobrazit nová umístění a IP adresy. 
- Zkontrolujte všechny prostředky, které interakci s místní virtuální počítače a aktualizovat všechny relevantní nastavení nebo v dokumentaci tak, aby odrážely novou konfiguraci.
- Contoso použije službu Azure migrovat pomocí mapování závislostí k vyhodnocení **OSTICKETWEB** virtuálního počítače pro migraci. Nyní odstraňte agenty (Microsoft Monitoring Agent agenta a závislosti) instalováni pro tento účel, z virtuálního počítače.

## <a name="review-the-deployment"></a>Zkontrolujte nasazení

Aplikace je nyní spuštěna Contoso nutné plně zprovoznit a zabezpečit jejich novou infrastrukturu.

### <a name="security"></a>Zabezpečení

Tým zabezpečení Contoso zkontrolujte virtuálního počítače a databáze, chcete-li zjistit jakékoli problémy se zabezpečením.

- Zkontrolujte že skupiny zabezpečení sítě (Nsg) pro virtuální počítač, abyste mohli řídit přístup. K zajištění, že pouze povolené k aplikaci přenos dat se používají skupiny Nsg.
- Považují za zabezpečení dat na disky virtuálních počítačů pomocí šifrování disku a Azure KeyVault.
- Komunikace mezi instance virtuálního počítače a databáze není nakonfigurován pro protokol SSL. Bude nutné provést k zajištění, že nelze hacker provozu databáze.

[Další informace](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) o postupy zabezpečení pro virtuální počítače.

### <a name="backups"></a>Zálohování

- Contoso bude zálohovat data na virtuálním počítači pomocí služby zálohování Azure. [Další informace](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- Nemusí se ke konfiguraci zálohování pro databázi. Databáze pro databázi MySQL Azure automaticky vytvoří záloh serveru a uloží. Vybral používat geografická redundance pro databázi, takže je odolné a produkční prostředí.

### <a name="licensing-and-cost-optimization"></a>Licencování a cenově optimalizace

- Po nasazení prostředků, přiřadí Contoso Azure značky, v souladu s rozhodnutí se během [infrastrukturu Azure](contoso-migration-infrastructure.md#set-up-tagging) nasazení.
- Nejsou žádné problémy s licencí pro servery, Contoso Ubuntu.
- Contoso povolíte Azure náklady na správu licencované Cloudyn, dceřiné společnosti Microsoft. Je řešení pro správu více cloudové náklady, který vám pomůže využívat a spravovat Azure a dalším prostředkům cloudu.  [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o Azure náklady na správu.


## <a name="next-steps"></a>Další postup

V tomto scénáři jsme vám ukázal, scénáři konečné opětovného hostování, vyzkoušeny Contoso. Migrovat virtuální počítač místní Linux osTicket aplikace front-endu na virtuální počítač Azure a migrovat aplikace databáze na instanci Azure MySQL.

V sadě další kurzy v této sérii migrace vytvoříme ukazují, jak Contoso provádí sadu migrace, složitější, zahrnující aplikace refaktoring místo jednoduché navýšení a shift migrace.
