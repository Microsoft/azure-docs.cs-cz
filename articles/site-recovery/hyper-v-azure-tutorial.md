---
title: Nastavení zotavení po havárii do Azure pro místní virtuální počítače Hyper-V (bez VMM) pomocí Azure Site Recovery | Microsoft Docs
description: Zjistěte, jak nastavit zotavení po havárii místních virtuálních počítačů Hyper-V (bez VMM) do Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/21/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9ee5478412b02615efec983dd0b99c12fc2d9213
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643579"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Nastavení zotavení po havárii místních virtuálních počítačů Hyper-V do Azure

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení pro místní počítače a virtuální počítače Azure.

V tomto kurzu se dozvíte, jak nastavit zotavení po havárii místních virtuálních počítačů Hyper-V do Azure. Tento kurz se týká virtuálních počítačů Hyper-V, které nespravuje System Center Virtual Machine Manager (VMM). V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vybrat zdroj a cíl replikace.
> * Nastavit zdrojové prostředí replikace, včetně místních komponent řešení Site Recovery, a cílové prostředí replikace.
> * Vytvořit zásadu replikace.
> * Povolit replikaci virtuálního počítače.

Toto je třetí kurz ze specializované série. Tento kurz předpokládá, že jste už dokončili úlohy z předchozích kurzů:

1. [Příprava Azure](tutorial-prepare-azure.md)
2. [Příprava místního Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

Než začnete, doporučujeme [zkontrolovat architekturu](concepts-hyper-v-to-azure-architecture.md) pro tento scénář zotavení po havárii.

## <a name="select-a-replication-goal"></a>Výběr cíle replikace


1. V části **Všechny služby** > **Trezory služby Recovery Services** vyberte trezor, který jste připravili v předchozím kurzu – **ContosoVMVault**.
2. V části **Začínáme** klikněte na **Site Recovery**. Pak klikněte na **Příprava infrastruktury**.
3. V části **Cíl ochrany** > **Kde jsou vaše počítače umístěné?** vyberte **Místní**.
4. V části **Kam chcete počítače replikovat?** vyberte **Do Azure**.
5. V části **Používáte System Center VMM ke správě svých hostitelů Hyper-V** vyberte **Ne**. Pak klikněte na **OK**.

    ![Cíl replikace](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

Při plánování velkého nasazení byste se měli ujistit, že jste dokončili [plánování nasazení pro replikaci Hyper-V](hyper-v-deployment-planner-overview.md). Pro účely tohoto kurzu v části **Dokončili jste plánování nasazení?** vyberte v rozevíracím seznamu možnost **Udělám to později**.

![Plánování nasazení](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Pokud chcete nastavit zdrojové prostředí, vytvořte lokalitu Hyper-V a přidejte do ní hostitele Hyper-V. Potom stáhněte a nainstalujte zprostředkovatele Azure Site Recovery a agenta Azure Recovery Services a zaregistrujte lokalitu Hyper-V v trezoru. 

1. V části **Příprava infrastruktury** klikněte na **Zdroj**.
2. Klikněte na **+ Lokalita Hyper-V** a zadejte název lokality, kterou jste vytvořili v předchozím kurzu – **ContosoHyperVSite**.

    ![Lokalita Hyper-V](./media/hyper-v-azure-tutorial/hyperv-site.png)

3. Jakmile je lokalita vytvořená, klikněte na **+ Server Hyper-V**.

    ![Server Hyper-V](./media/hyper-v-azure-tutorial/hyperv-server.png)

4. Stáhněte instalační soubor zprostředkovatele.
6. Stáhněte registrační klíč trezoru. Tento klíč budete potřebovat ke spuštění instalace zprostředkovatele. Klíč je platný pět dní od jeho vygenerování.

    ![Stažení zprostředkovatele](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Instalace zprostředkovatele

Spusťte instalační soubor zprostředkovatele (AzureSiteRecoveryProvider.exe) na každém hostiteli Hyper-V, kterého jste přidali do lokality **ContosoHyperVSite**. Na každém hostiteli Hyper-V se nainstaluje zprostředkovatel Azure Site Recovery a agent Recovery Services.

1. V průvodci instalací zprostředkovatele Azure Site Recovery v části **Microsoft Update** vyjádřete výslovný souhlas s použitím Microsoft Update ke kontrole aktualizací zprostředkovatele.
2. V části **Instalace** přijměte výchozí umístění instalace zprostředkovatele a agenta a klikněte na **Nainstalovat**.
3. Po instalaci v průvodci registrací Microsoft Azure Site Recovery v části **Nastavení trezoru** klikněte na **Procházet** a v části **Soubor klíče** vyberte stažený soubor klíče trezoru. 
4. Zadejte předplatné Azure Site Recovery, název trezoru (**ContosoVMVault**), a název lokality Hyper-V (**ContosoHyperVSite**), do které patří server Hyper-V.
5. V části **Nastavení proxy** vyberte **Připojit přímo k Azure Site Recovery bez proxy**.
6. Po registraci serveru v trezoru v části **Registrace** klikněte na **Dokončit**.

Azure Site Recovery načte metadata ze serveru Hyper-V a server se zobrazí v části **Infrastruktura Site Recovery** > **Hostitelé Hyper-V**. Tento proces může trvat až 30 minut.


## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky. 

1. Klikněte na **Příprava infrastruktury** > **Cíl**.
2. Vyberte předplatné a skupinu prostředků **ContosoRG**, ve kterých se vytvoří virtuální počítače Azure po převzetí služeb při selhání.
3. Vyberte model nasazení **Resource Manager**.

Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.


## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

> [!NOTE]
> U zásad replikace Hyper-V do Azure se možnost 15minutové frekvence kopírování přestává podporovat a místo ní je k dispozici možnost nastavení 5minutové a 30sekundové frekvence kopírování. Zásady replikace využívající 15minutovou frekvenci kopírování se automaticky aktualizují, aby využívaly nastavení 5minutové frekvence kopírování. Možnosti 5minutové a 30sekundové frekvence kopírování poskytují oproti 15minutové frekvenci kopírování lepší výkon replikace a lepší cíle bodů obnovení s minimálním dopadem na využití šířky pásma a objem přenášených dat.

1. Klikněte na **Příprava infrastruktury** > **Nastavení replikace** > **+ Vytvořit a přidružit**.
2. V části **Vytvořit a přidružit zásady** zadejte název zásady **ContosoReplicationPolicy**.
3. Ponechte výchozí nastavení a klikněte na **OK**.
    - **Frekvence kopírování** označuje, že se rozdílová data (po počáteční replikaci) budou replikovat každých pět minut.
    - **Uchovávání bodů obnovení** označuje, že interval uchovávání každého bodu obnovení bude dvě hodiny.
    - **Frekvence snímků konzistentních vzhledem k aplikacím** označuje, že se body obnovení obsahující snímky konzistentní vzhledem k aplikacím budou vytvářet každou hodinu.
    - **Čas spuštění počáteční replikace** označuje, že se počáteční replikace spustí okamžitě.
4. Po vytvoření zásady klikněte na **OK**. Když vytvoříte novou zásadu, automaticky se přidruží k zadané lokalitě Hyper-V (**ContosoHyperVSite**).

    ![Zásady replikace](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>Povolení replikace


1. V části **Replikovat aplikaci** klikněte na **Zdroj**. 
2. V části **Zdroj** vyberte lokalitu **ContosoHyperVSite**. Pak klikněte na **OK**.
3. V části **Cíl** potvrďte jako cíl Azure, předplatné trezoru a model nasazení **Resource Manager**.
4. Vyberte pro replikovaná data účet úložiště **contosovmsacct1910171607**, který jste vytvořili v předchozím kurzu, a síť **ContosoASRnet**, do které se umístí virtuální počítače Azure po převzetí služeb při selhání.
5. V části **Virtuální počítače** > **Vybrat** vyberte virtuální počítač, který chcete replikovat. Pak klikněte na **OK**.

 Průběh akce **Povolení ochrany** můžete sledovat v části **Úlohy** > **Úlohy Site Recovery**. Po dokončení úlohy **Dokončení ochrany** bude počáteční replikace dokončený a virtuální počítač bude připravený na převzetí služeb při selhání.

## <a name="next-steps"></a>Další kroky
[Spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md)
