---
title: Přesunutí virtuálního počítače do jiné oblasti (Azure Site Recovery)
description: Zjistěte, jak můžete migrovat virtuální počítač SQL Serveru z jedné oblasti do druhé v rámci Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3b84119cdcc1bb7e8603de64e3d23c69dac70cc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022292"
---
# <a name="move-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery-services"></a>Přesunutí virtuálního počítače SQL Server do jiné oblasti v rámci Azure pomocí služeb Azure Site Recovery

Tento článek vás naučí, jak pomocí Azure Site Recovery migrovat virtuální počítač SQL Server (VM) z jedné oblasti do druhé v rámci Azure. 

Přesunutí virtuálního serveru SQL Server do jiné oblasti vyžaduje následující kroky:
1. [**Příprava**](#prepare-to-move): Zkontrolujte, zda jsou zdrojový virtuální počítač SQL Server a cílová oblast dostatečně připraveny pro přesun. 
1. [**Konfigurace**](#configure-azure-site-recovery-vault): Přesunutí virtuálního počítače SQL Server vyžaduje, aby se jedná o replikovaný objekt v trezoru obnovení webu Azure. Musíte přidat virtuální počítač SQL Server do trezoru obnovení webu Azure. 
1. [**Testování**](#test-move-process): Migrace virtuálního virtuálního soudu SQL Server vyžaduje jeho selhání ze zdrojové oblasti do replikované cílové oblasti. Chcete-li zajistit, že proces přesunutí bude úspěšný, je třeba nejprve otestovat, že váš virtuální počítač SQL Server může úspěšně převzetí služeb při selhání do cílové oblasti. To pomůže odhalit všechny problémy a vyhnout se jim při provádění skutečné přesunutí. 
1. [**Přesunutí**](#move-the-sql-server-vm): Jakmile vaše zkušební převzetí služeb při selhání prošel a víte, že jste bezpečné migrovat virtuální počítač SQL Server, můžete provést přesunutí virtuálního počítače do cílové oblasti. 
1. [**Čištění**](#clean-up-source-resources): Chcete-li se vyhnout účtování poplatků, odeberte virtuální ho hlavního virtuálního serveru SQL Server z trezoru a všechny zbytečné prostředky, které zbudou ve skupině prostředků. 

## <a name="verify-prerequisites"></a>Ověření požadavků 

- Zkontrolujte, zda [je podporován](../../../site-recovery/azure-to-azure-support-matrix.md#region-support)přesun ze zdrojové oblasti do cílové oblasti .  
- Zkontrolujte [architekturu scénáře a součásti,](../../../site-recovery/azure-to-azure-architecture.md) jakož i [omezení a požadavky podpory](../../../site-recovery/azure-to-azure-support-matrix.md). 
- Ověřte oprávnění účtu. Pokud jste si vytvořili bezplatný účet Azure, jste správcem předplatného. Pokud nejste správce předplatného, spolupracujte se správcem a přiřaďte oprávnění, která potřebujete. Pokud chcete povolit replikaci virtuálního počítače a kopírovat data pomocí Azure Site Recovery, musíte mít: 
    - Oprávnění k vytvoření virtuálního virtuálního mísy. Integrovaná role *přispěvatele virtuálního počítače* má tato oprávnění, mezi něž patří: 
        - Oprávnění k vytvoření virtuálního virtuálního pracovního kanálu ve vybrané skupině prostředků. 
        - Oprávnění k vytvoření virtuálního počítače ve vybrané virtuální síti. 
        - Oprávnění k zápisu do vybraného účtu úložiště. 
      - Oprávnění ke správě operací obnovení webu Azure. Role *Přispěvatel obnovení webu* má všechna oprávnění potřebná ke správě operací obnovení webu v trezoru služby Recovery Services.  

## <a name="prepare-to-move"></a>Připravte se na přesun
Připravte zdrojový virtuální počítač SQL Server i cílovou oblast pro přesun. 

### <a name="prepare-the-source-sql-server-vm"></a>Příprava zdrojového virtuálního serveru SQL Server

- Ujistěte se, že všechny nejnovější kořenové certifikáty jsou na virtuálním počítači SQL Server, který chcete přesunout. Pokud nejnovější kořenové certifikáty nejsou k dispozici, omezení zabezpečení zabrání kopírování dat do cílové oblasti. 
- Pro virtuální počítače se systémem Windows nainstalujte všechny nejnovější aktualizace systému Windows na virtuální počítač, aby všechny důvěryhodné kořenové certifikáty byly v počítači. V odpojeném prostředí postupujte podle standardního procesu aktualizace Systému Windows UPdate a certifikátu pro vaši organizaci. 
- Pro virtuální počítače s Linuxem postupujte podle pokynů vašeho distributora Linuxu a získejte nejnovější důvěryhodné kořenové certifikáty a seznam odvolaných certifikátů na virtuálním počítači. 
- Ujistěte se, že nepoužíváte ověřovací proxy server k řízení připojení k síti pro virtuální servery, které chcete přesunout. 
- Pokud virtuální počítače, který se pokoušíte přesunout nemá přístup k internetu, nebo je to pomocí proxy firewall řídit odchozí přístup, zkontrolujte požadavky. 
- Identifikujte rozložení zdrojové sítě a všechny prostředky, které právě používáte. To zahrnuje mimo jiné vykladače zatížení, skupiny zabezpečení sítě (NSG) a veřejné IP adresy. 

### <a name="prepare-the-target-region"></a>Příprava cílové oblasti

- Ověřte, že vaše předplatné Azure umožňuje vytvářet virtuální počítače v cílové oblasti, která se používá pro zotavení po havárii. O povolení požadované kvóty požádejte podporu. 
- Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu virtuálních počítačů s velikostí, která odpovídá vašim zdrojovým virtuálním počítačům. Pokud ke kopírování dat do cíle používáte Site Recovery, site recovery zvolí stejnou velikost nebo nejbližší možnou velikost pro cílový virtuální virtuální počítače. 
- Ujistěte se, že vytvoříte cílový prostředek pro každou součást, která je identifikována v rozložení zdrojové sítě. Tento krok je důležité zajistit, aby vaše virtuální počítače mají všechny funkce a funkce v cílové oblasti, které jste měli ve zdrojové oblasti. 
    - Azure Site Recovery automaticky zjišťuje a vytvoří virtuální síť, když povolíte replikaci pro zdrojový virtuální počítač. Můžete také předem vytvořit síť a přiřadit ji k virtuálnímu virtuálnímu virtuálnímu serveru v toku uživatele pro povolení replikace. Je třeba ručně vytvořit všechny ostatní prostředky v cílové oblasti.
- Pokud chcete vytvořit nejčastěji používané síťové prostředky, které jsou pro vás relevantní na základě konfigurace zdrojového virtuálního počítače, přečtěte si následující dokumentaci: 
    - [Skupiny zabezpečení sítě](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Vyrovnávání zatížení](../../../load-balancer/tutorial-load-balancer-basic-internal-portal.md)
    - [Veřejná IP adresa](../../../virtual-network/virtual-network-public-ip-address.md)
    - Další síťové součásti naleznete v [dokumentaci](../../../virtual-network/virtual-networks-overview.md)k síti .
- Chcete-li před provedením konečného přesunu do cílové oblasti otestovat konfiguraci, vytvořte ručně neprodukční síť v cílové oblasti. Tento krok doporučujeme, protože zajišťuje minimální rušení výrobní sítě. 

## <a name="configure-azure-site-recovery-vault"></a>Konfigurace trezoru obnovení webu Azure

Následující kroky ukazují, jak pomocí Azure Site Recovery zkopírovat data do cílové oblasti. Vytvořte trezor služby Recovery Services v libovolné oblasti než ve zdrojové oblasti. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 
1. Zvolte **vytvoření prostředku** z levého horního rohu navigačního podokna. 
1. Vyberte **nástroje pro správu & IT** a pak vyberte Zálohování a Obnovení **lokality**. 
1. Na kartě **Základy** v části **Podrobnosti o projektu**vytvořte novou skupinu zdrojů v cílové oblasti nebo vyberte existující skupinu zdrojů v cílové oblasti. 
1. V **části Podrobnosti instance**zadejte název trezoru a v rozevíracím souboru vyberte cílovou **oblast.** 
1. Chcete-li vytvořit trezor služby Recovery Services, vyberte **možnost Revize + Vytvořit.** 
1. V levém horním rohu navigačního podokna a v typu `recovery services`vyhledávacího pole vyberte Všechny **služby** . 
1. (Volitelně) Vyberte hvězdičku vedle **trezorů Služby obnovení** a přidejte ji na rychlou navigační lištu. 
1. Vyberte **trezory služeb obnovení** a pak vyberte trezor Služby pro obnovení, který jste vytvořili. 
1. V podokně **Přehled** vyberte **Replikovat**. 

   ![Konfigurace replikace](media/virtual-machines-windows-sql-move-to-new-region/configure-replication.png)

1. Vyberte **Zdroj** a pak jako zdroj vyberte **Azure.** Vyberte příslušné hodnoty pro ostatní rozevírací pole, jako je například umístění pro zdrojové virtuální počítače. V poli **Skupina zdrojů** budou viditelné pouze skupiny prostředků umístěné v oblasti **umístění zdroje.** 
1. Vyberte **Virtuální počítače** a pak zvolte virtuální počítače, které chcete migrovat. Výběrem **možnosti OK** uložíte výběr virtuálního počítače. 
1. V rozevíracím souboru vyberte **Nastavení**a pak zvolte **cílové umístění.** Měla by to být skupina prostředků, kterou jste připravili dříve. 
1. Po přizpůsobení replikace vyberte **Vytvořit cílové prostředky** a vytvořte prostředky v novém umístění. 
1. Po dokončení vytváření prostředků vyberte **Povolit replikaci** a zahájit replikaci virtuálního počítače SQL Server ze zdroje do cílové oblasti.
1. Stav replikace můžete zkontrolovat tak, že přejdete do úložiště pro obnovení, vyberete **replikované položky** a zkontrolujete **stav** virtuálního počítače SQL Server. Stav **Protected** označuje, že replikace byla dokončena. 

   ![Ověření stavu replikace](media/virtual-machines-windows-sql-move-to-new-region/check-replication-status.png)

## <a name="test-move-process"></a>Proces testovacího přesunu
Následující kroky ukazují, jak pomocí Azure Site Recovery otestovat proces přesunutí. 

1. Přejděte do **trezoru služby Recovery Services** na [webu Azure Portal](https://portal.azure.com) a vyberte **Replikované položky**. 
1. Vyberte virtuální modul SQL Server, který chcete přesunout, ověřte, že **se stav replikace** zobrazuje jako **V pořádku,** a pak vyberte **možnost Testovat převzetí služeb při selhání**. 

   ![Testování převzetí služeb při selhání pro váš virtuální počítač](media/virtual-machines-windows-sql-move-to-new-region/test-failover-of-replicated-vm.png)

1. Na stránce **Test převzetí služeb při selhání** vyberte nejnovější bod obnovení konzistentní s **aplikací,** který se má použít pro převzetí služeb při selhání, protože to je jediný typ snímku, který může zaručit konzistenci dat serveru SQL Server. 
1. Vyberte virtuální síť v části **Virtuální síť Azure** a pak vyberte **OK,** chcete-li otestovat převzetí služeb při selhání. 
   
   >[!IMPORTANT]
   > Doporučujeme použít samostatnou síť virtuálních počítačů Azure pro test převzetí služeb při selhání. Nepoužívejte produkční síť, která byla nastavena, když jste povolili replikaci a do které chcete virtuální počítače nakonec přesunout. 

1. Chcete-li sledovat průběh, přejděte do trezoru, v části **Sledování**vyberte **úlohy obnovení webu** a pak vyberte probíhající testovací úlohu převzetí služeb **při selhání.**

   ![Sledování průběhu testu převzetí služeb při selhání](media/virtual-machines-windows-sql-move-to-new-region/monitor-failover-test-job.png)

1. Po dokončení testu přejděte na **virtuální počítače** na portálu a zkontrolujte nově vytvořený virtuální počítač. Ujistěte se, že je spuštěný virtuální modul SQL Server, je správně dimenzován a je připojen k příslušné síti. 
1. Odstraňte virtuální hovirtuální ho disponitova, který byl vytvořen jako součást testu, protože možnost **převzetí služeb při selhání** bude šedě, dokud nebudou vyčištěny prostředky testu převzetí služeb při selhání. Přejděte zpět do úložiště, vyberte **Replikované položky**, vyberte virtuální ms sql serveru a pak vyberte **možnost Převzetí služeb při selhání testu vyčištění**. Zaznamenejte a uložte všechna pozorování přidružená k testu v části **Poznámky** a zaškrtněte políčko vedle **položky Testování je dokončeno. Odstranit testovací virtuální počítače s podporou převzetí služeb při selhání**. Chcete-li po testu vyčistit prostředky, vyberte **možnost OK.** 

   ![vyčistit položky po testu převzetí služeb při selhání](media/virtual-machines-windows-sql-move-to-new-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>Přesunutí virtuálního serveru SQL Server 
Následující kroky ukazují, jak přesunout virtuální počítač SQL Server ze zdrojové oblasti do cílové oblasti. 

1. Přejděte do trezoru **Služby pro obnovení,** vyberte **Replikované položky**, vyberte virtuální ms a pak vyberte **Převzetí služeb při selhání**. 

   ![Zahájit převzetí služeb při selhání](media/virtual-machines-windows-sql-move-to-new-region/initiate-failover.png)

1. V části **Bod obnovení**vyberte nejnovější bod obnovení **konzistentní s aplikací** . 
1. Zaškrtněte políčko vedle **položky Vypnout počítač před zahájením převzetí služeb při selhání**. Obnovení sítě se pokusí vypnout zdrojový virtuální hod před aktivací převzetí služeb při selhání. Převzetí služeb při selhání bude pokračovat i v případě, že vypnutí se nezdaří. 
1. Chcete-li spustit převzetí služeb při selhání, vyberte **možnost OK.**
1. Proces převzetí služeb při selhání můžete sledovat ze stejné stránky **úloh obnovení webu,** kterou jste si zobrazili při sledování testu převzetí služeb při selhání v předchozí části. 
1. Po dokončení úlohy zkontrolujte, zda se virtuální modul SQL Server zobrazí v cílové oblasti podle očekávání. 
1. Přejděte zpět do úložiště, vyberte **Replikované položky**, vyberte virtuální ms serveru SQL Server a vyberte **Potvrdit,** chcete-li dokončit proces přesunu do cílové oblasti. Počkejte, až bude úloha potvrzení dokončena. 
1. Zaregistrujte svůj virtuální počítač SQL Server u poskytovatele prostředků virtuálního počítače SQL, abyste povolili správu **virtuálního počítače SQL** na webu Azure Portal a funkce přidružené k poskytovateli prostředků. Další informace naleznete [v tématu Registrace virtuálního serveru SQL Server s poskytovatelem prostředků virtuálního virtuálního připojení SQL](virtual-machines-windows-sql-register-with-rp.md). 

  > [!WARNING]
  > Konzistence dat serveru SQL Server je zaručena pouze u snímků konzistentních s aplikací. **Nejnovější zpracovaný** snímek nelze použít pro převzetí služeb při selhání serveru SQL Server jako snímek zotavení po chybě nemůže zaručit konzistenci dat serveru SQL Server. 

## <a name="clean-up-source-resources"></a>Vyčištění zdrojových prostředků
Chcete-li se vyhnout poplatkům za fakturaci, odeberte virtuální ho sněmu serveru SQL Server z úložiště a odstraňte všechny nepotřebné přidružené prostředky. 

1. Přejděte zpět do **trezoru obnovení webu,** vyberte **replikované položky**a vyberte virtuální ms serveru SQL Server. 
1. Vyberte **zakázat replikaci**. Vyberte důvod zakázání ochrany a pak vyberte **OK,** chcete-li zakázat replikaci. 

   >[!IMPORTANT]
   > Je důležité provést tento krok, aby se zabránilo účtování poplatku za replikaci azure site recovery. 

1. Pokud nemáte žádné plány znovu použít některý z prostředků ve zdrojové oblasti, odstraňte všechny příslušné síťové prostředky a odpovídající účty úložiště. 


## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto článcích: 

* [Přehled sql serveru na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Nejčastější dotazy k SQL Serveru na virtuálním počítači windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server na základě pokynů k cenovým službách virtuálních počítačů s Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server na poznámkách k verzi virtuálního počítači windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


