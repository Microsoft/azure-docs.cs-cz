---
title: Přesunutí virtuálního počítače do jiné oblasti (Azure Site Recovery)
description: Přečtěte si, jak můžete migrovat virtuální počítač s SQL Server z jedné oblasti do jiné v rámci Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 131deabfbd29e4d55a3f34252e3ba68261872ca0
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92785489"
---
# <a name="move-a-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery"></a>Přesunutí virtuálního počítače s SQL Server do jiné oblasti v rámci Azure pomocí Azure Site Recovery
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

V tomto článku se naučíte, jak pomocí Azure Site Recovery migrovat virtuální počítač s SQL Server z jedné oblasti do druhé v rámci Azure. 

Přesunutí SQL Server virtuálního počítače do jiné oblasti vyžaduje následující kroky:
1. [Příprava](#prepare-to-move): potvrďte, že je váš zdrojový SQL Server virtuální počítač i cílový region dostatečně připravené pro přesun. 
1. [Konfigurace](#configure-azure-site-recovery-vault): přesunutí virtuálního počítače s SQL Server vyžaduje, aby se jedná o replikovaný objekt v rámci trezoru Azure Site Recovery. Do trezoru Azure Site Recovery musíte přidat svůj SQL Server virtuální počítač. 
1. [Testování](#test-move-process): migrace SQL Server virtuálního počítače vyžaduje, aby převzala služby při selhání ze zdrojové oblasti do replikované cílové oblasti. Aby se zajistilo, že proces přesunu bude úspěšný, musíte nejdřív otestovat, že váš virtuální počítač SQL Server může úspěšně převzít služby při selhání do cílové oblasti. To vám pomůže odhalit případné problémy a vyhnout se jim při provádění skutečného přesunu. 
1. [Přesunutí](#move-the-sql-server-vm): po úspěšném dokončení testovacího převzetí služeb při selhání a víte, že jste bezpečně MIGROVALI SQL Server virtuální počítač, můžete provést přesun virtuálního počítače do cílové oblasti. 
1. [Čištění](#clean-up-source-resources): Pokud se chcete vyhnout poplatkům za účtování, odeberte z trezoru SQL Server virtuální počítač a všechny nepotřebné prostředky, které zůstaly ve skupině prostředků. 

## <a name="verify-prerequisites"></a>Ověření požadavků 

- Ověřte, že [se podporuje](../../../site-recovery/azure-to-azure-support-matrix.md#region-support)přesun ze zdrojové oblasti do cílové oblasti.  
- Zkontrolujte [architekturu a součásti scénáře](../../../site-recovery/azure-to-azure-architecture.md) a také [omezení podpory a požadavky](../../../site-recovery/azure-to-azure-support-matrix.md). 
- Ověřte oprávnění účtu. Pokud jste si vytvořili bezplatný účet Azure, jste správcem předplatného. Pokud nejste správcem předplatného, spolupracujte se správcem a přiřaďte potřebná oprávnění. Pokud chcete pro virtuální počítač povolit replikaci a kopírovat data pomocí Azure Site Recovery, musíte mít: 
    - Oprávnění k vytvoření virtuálního počítače. Předdefinovaná role *Přispěvatel virtuálních počítačů* má tato oprávnění, která zahrnují: 
        - Oprávnění k vytvoření virtuálního počítače ve vybrané skupině prostředků. 
        - Oprávnění k vytvoření virtuálního počítače ve vybrané virtuální síti. 
        - Oprávnění pro zápis do vybraného účtu úložiště. 
      - Oprávnění ke správě Azure Site Recoverych operací. Role *přispěvatel Site Recovery* má všechna oprávnění, která jsou nutná ke správě operací Site Recovery v trezoru služby Recovery Services.  

## <a name="prepare-to-move"></a>Příprava na přesunutí
Připravte zdrojový virtuální počítač SQL Server i cílovou oblast pro přesunutí. 

### <a name="prepare-the-source-sql-server-vm"></a>Příprava zdrojového SQL Server virtuálního počítače

- Zajistěte, aby všechny nejnovější kořenové certifikáty byly na SQL Serverm virtuálním počítači, který chcete přesunout. Pokud nejsou k dispozici nejnovější kořenové certifikáty, omezení zabezpečení zabrání v kopírování dat do cílové oblasti. 
- U virtuálních počítačů s Windows nainstalujte na virtuální počítač všechny nejnovější aktualizace Windows, aby se na tomto počítači používaly všechny důvěryhodné kořenové certifikáty. V odpojeném prostředí postupujte podle standardních web Windows Update a procesu aktualizace certifikátu pro vaši organizaci. 
- Pro virtuální počítače se systémem Linux postupujte podle pokynů, které poskytuje distributor pro Linux, a získejte nejnovější důvěryhodné kořenové certifikáty a seznam odvolaných certifikátů na virtuálním počítači. 
- Ujistěte se, že nepoužíváte ověřovací proxy server k řízení připojení k síti pro virtuální počítače, které chcete přesunout. 
- Pokud virtuální počítač, který se pokoušíte přesunout, nemá přístup k Internetu, nebo k řízení odchozího přístupu používá proxy server brány firewall, Projděte si požadavky. 
- Identifikujte rozložení zdrojové sítě a všechny prostředky, které aktuálně používáte. To zahrnuje, ale není omezené na nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě (skupin zabezpečení sítě) a veřejné IP adresy. 

### <a name="prepare-the-target-region"></a>Příprava cílové oblasti

- Ověřte, že vaše předplatné Azure umožňuje vytvářet virtuální počítače v cílové oblasti, která se používá pro zotavení po havárii. O povolení požadované kvóty požádejte podporu. 
- Ujistěte se, že vaše předplatné má dostatek prostředků na podporu virtuálních počítačů se stejnou velikostí, která odpovídá vašim zdrojovým virtuálním počítačům. Pokud používáte Site Recovery ke kopírování dat do cíle, Site Recovery zvolit stejnou velikost nebo nejbližší možnou velikost cílového virtuálního počítače. 
- Ujistěte se, že jste vytvořili cílový prostředek pro každou součást identifikovanou v rozložení zdrojové sítě. Tento krok je důležitý, aby se zajistilo, že vaše virtuální počítače mají všechny funkce a funkce v cílové oblasti, které jste měli ve zdrojové oblasti. 
    - Azure Site Recovery automaticky zjistí a vytvoří virtuální síť, když povolíte replikaci pro zdrojový virtuální počítač. Můžete také předem vytvořit síť a přiřadit ji k virtuálnímu počítači v toku uživatele pro povolení replikace. V cílové oblasti musíte ručně vytvořit všechny další prostředky.
- Pokud chcete vytvořit nejčastěji používané síťové prostředky, které jsou relevantní pro vás na základě konfigurace zdrojového virtuálního počítače, přečtěte si následující dokumentaci: 
    - [Skupiny zabezpečení sítě](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Nástroj pro vyrovnávání zatížení](../../../load-balancer/quickstart-load-balancer-standard-internal-portal.md)
    - [Veřejná IP adresa](../../../virtual-network/virtual-network-public-ip-address.md)
    - Další síťové součásti najdete v [dokumentaci k síti](../../../virtual-network/virtual-networks-overview.md).
- Ruční vytvoření neprodukční sítě v cílové oblasti, pokud chcete otestovat konfiguraci před provedením finálního přesunu do cílové oblasti. Tento krok doporučujeme, protože zajišťuje minimální interferenci s produkční sítí. 

## <a name="configure-azure-site-recovery-vault"></a>Konfigurace trezoru Azure Site Recovery

Následující kroky ukazují, jak použít Azure Site Recovery ke kopírování dat do cílové oblasti. Vytvořte Trezor Recovery Services v jiné oblasti, než je zdrojová oblast. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
1. V levém horním rohu navigačního podokna vyberte **vytvořit prostředek** . 
1. Vyberte **& nástroje pro správu** a pak vyberte **Backup and Site Recovery** . 
1. Na kartě **základy** v části **Project Details (podrobnosti projektu** ) vytvořte novou skupinu prostředků v cílové oblasti nebo vyberte existující skupinu prostředků v cílové oblasti. 
1. V části **Podrobnosti o instanci** zadejte název vašeho trezoru a potom z rozevíracího seznamu vyberte svou cílovou **oblast** . 
1. Vyberte **zkontrolovat + vytvořit** a vytvořte svůj Recovery Services trezor. 
1. V levém horním rohu navigačního podokna a do vyhledávacího pole vyberte **všechny služby** `recovery services` . 
1. Volitelně Vyberte hvězdičku vedle **Recovery Services trezory** a přidejte je do rychlého navigačního panelu. 
1. Vyberte **trezory služby Recovery Services** a pak vyberte Recovery Services trezor, který jste vytvořili. 
1. V podokně **Přehled** vyberte **replikovat** . 

   ![Konfigurace replikace](./media/move-sql-vm-different-region/configure-replication.png)

1. Vyberte **zdroj** a pak jako zdroj vyberte **Azure** . Vyberte odpovídající hodnoty pro ostatní rozevírací pole, jako je například umístění vašich zdrojových virtuálních počítačů. V poli **zdrojová skupina prostředků** se budou zobrazovat jenom skupiny prostředků nacházející se v oblasti **zdrojové umístění** . 
1. Vyberte **virtuální počítače** a pak vyberte virtuální počítače, které chcete migrovat. Výběrem **OK** uložte výběr virtuálního počítače. 
1. Vyberte **Nastavení** a potom z rozevíracího seznamu zvolte **cílové umístění** . Mělo by se jednat o skupinu prostředků, kterou jste připravili dříve. 
1. Po přizpůsobení replikace vyberte **vytvořit cílové prostředky** a vytvořte prostředky v novém umístění. 
1. Po vytvoření prostředku vyberte **Povolit replikaci** , aby se spustila replikace SQL Server virtuálního počítače ze zdroje do cílové oblasti.
1. Stav replikace můžete zjistit tak, že přejdete do trezoru pro obnovení, vyberete **replikované položky** a zobrazíte **stav** svého virtuálního počítače s SQL Server. Stav **chráněno** znamená, že se replikace dokončila. 

   ![Ověření stavu replikace](./media/move-sql-vm-different-region/check-replication-status.png)

## <a name="test-move-process"></a>Testovací proces přesunutí
Následující kroky ukazují, jak použít Azure Site Recovery k otestování procesu přesunutí. 

1. V [Azure Portal](https://portal.azure.com) přejděte do svého **trezoru Recovery Services** a vyberte **replikované položky** . 
1. Vyberte SQL Server virtuální počítač, který chcete přesunout, ověřte, že **stav replikace** je **v pořádku** , a pak vyberte **testovací převzetí služeb při selhání** . 

   ![Testování převzetí služeb při selhání pro virtuální počítač](./media/move-sql-vm-different-region/test-failover-of-replicated-vm.png)

1. Na stránce **testovací převzetí služeb při selhání** vyberte nejnovější bod obnovení **konzistentní vzhledem k aplikacím** , který se má použít pro převzetí služeb při selhání, protože to je jediný typ snímku, který může zaručit SQL Server konzistenci dat. 
1. Vyberte virtuální síť v rámci **Azure Virtual Network** a pak kliknutím na **OK** Otestujte převzetí služeb při selhání. 
   
   >[!IMPORTANT]
   > Pro tento test převzetí služeb při selhání doporučujeme použít samostatnou síť virtuálních počítačů Azure. Nepoužívejte produkční síť, která byla nastavena, když jste povolili replikaci a chcete přesunout virtuální počítače do složky nakonec. 

1. Pokud chcete sledovat průběh, přejděte do svého trezoru, v části **monitorování** vyberte **Site Recovery úlohy** a pak vyberte probíhající úlohu **testovací převzetí služeb při selhání** .

   ![Sledovat průběh testu převzetí služeb při selhání](./media/move-sql-vm-different-region/monitor-failover-test-job.png)

1. Po dokončení testu přejděte k **virtuálním počítačům** na portálu a Prohlédněte si nově vytvořený virtuální počítač. Ujistěte se, že je virtuální počítač SQL Server spuštěný, má odpovídající velikost a je připojený k příslušné síti. 
1. Odstraňte virtuální počítač, který byl vytvořen jako součást testu, protože možnost **převzetí služeb při** selhání bude šedá až do vyčištění prostředků testu převzetí služeb při selhání. Přejděte zpátky do trezoru, vyberte **replikované položky** , vyberte SQL Server virtuální počítač a pak vyberte **vyčistit testovací převzetí služeb při selhání** . Zaznamenejte a uložte všechny poznámky spojené s testem v části s **poznámkami** a zaškrtněte políčko vedle položky **testování bylo dokončeno. Odstraňte virtuální počítače testovacího převzetí služeb při selhání** . Výběrem **OK** vyčistěte prostředky po testu. 

   ![vyčistit položky po testu převzetí služeb při selhání](./media/move-sql-vm-different-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>Přesunutí virtuálního počítače s SQL Server 
Následující kroky ukazují, jak přesunout SQL Server virtuální počítač ze zdrojové oblasti do cílové oblasti. 

1. Přejděte do trezoru **Recovery Services** , vyberte **replikované položky** , vyberte virtuální počítač a pak vyberte **převzetí služeb při selhání** . 

   ![Iniciovat převzetí služeb při selhání](./media/move-sql-vm-different-region/initiate-failover.png)

1. V **bodu obnovení** vyberte nejnovější bod obnovení **konzistentní vzhledem k aplikacím** . 
1. Zaškrtněte políčko vedle **vypnout počítač před tím, než začne převzetí služeb při selhání** . Site Recovery se před aktivací převzetí služeb při selhání pokusí vypnout zdrojový virtuální počítač. Převzetí služeb při selhání bude pokračovat i v případě selhání vypnutí. 
1. Vyberte **OK** a spusťte převzetí služeb při selhání.
1. Proces převzetí služeb při selhání můžete monitorovat ze stejné stránky **Site Recovery úlohy** , kterou jste zobrazili při monitorování testu převzetí služeb při selhání v předchozí části. 
1. Po dokončení úlohy ověřte, že se virtuální počítač SQL Server v cílové oblasti zobrazuje podle očekávání. 
1. Přejděte zpátky do trezoru, vyberte **replikované položky** , vyberte SQL Server virtuální počítač a vyberte **Potvrdit** pro dokončení procesu přesunutí do cílové oblasti. Počkejte, než se dokončí úloha potvrzení změn. 
1. Zaregistrujte svůj SQL Server virtuální počítač pomocí poskytovatele prostředků virtuálního počítače SQL, aby bylo možné povolit správu **virtuálních počítačů SQL** v Azure Portal a funkcích přidružených k poskytovateli prostředků. Další informace najdete v tématu [registrace SQL Server virtuálního počítače pomocí poskytovatele prostředků virtuálního počítače SQL](sql-vm-resource-provider-register.md). 

  > [!WARNING]
  > Konzistence SQL Server dat je zaručena pouze u snímků konzistentních vzhledem k aplikacím. **Nejnovější zpracovaný** snímek nejde použít pro SQL Server převzetí služeb při selhání, protože snímek pro zotavení po havárii nemůže zaručit SQL Server konzistenci dat. 

## <a name="clean-up-source-resources"></a>Vyčištění zdrojových prostředků
Abyste se vyhnuli poplatkům za účtování, odeberte SQL Server virtuální počítač z trezoru a odstraňte všechny nepotřebné přidružené prostředky. 

1. Přejděte zpátky do trezoru **Site Recovery** , vyberte **replikované položky** a vyberte SQL Server virtuální počítač. 
1. Vyberte **Zakázat replikaci** . Vyberte důvod pro zakázání ochrany a pak výběrem **OK** zakažte replikaci. 

   >[!IMPORTANT]
   > K tomu, abyste se vyhnuli Azure Site Recovery replikace, je důležité provést tento krok. 

1. Pokud nemáte žádné plány k opakovanému použití některého z prostředků ve zdrojové oblasti, odstraňte všechny relevantní síťové prostředky a odpovídající účty úložiště. 


## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích: 

* [Přehled SQL Server na virtuálním počítači s Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Nejčastější dotazy k SQL Server na VIRTUÁLNÍm počítači s Windows](frequently-asked-questions-faq.md)
* [SQL Server doprovodné materiály k cenám pro virtuální počítače s Windows](pricing-guidance.md)
* [Zpráva k vydání verze Windows VM SQL Server](doc-changes-updates-release-notes.md)