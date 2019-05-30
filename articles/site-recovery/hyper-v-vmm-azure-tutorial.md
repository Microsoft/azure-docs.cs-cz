---
title: Nastavení zotavení po havárii pro místní virtuální počítače Hyper-V v cloudech VMM do Azure pomocí Site Recovery | Dokumentace Microsoftu
description: Zjistěte, jak nastavit zotavení po havárii místních virtuálních počítačů Hyper-V v cloudech System Center VMM do Azure pomocí Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 653db1497fcce5981bba7416f073b0330ca2861f
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66398153"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Nastavení zotavení po havárii místních virtuálních počítačů Hyper-V v cloudech VMM do Azure

Tento článek popisuje, jak povolit replikaci místních virtuálních počítačů Hyper-V spravované nástrojem System Center Virtual Machine Manager (VMM) pro zotavení po havárii do Azure s použitím [Azure Site Recovery](site-recovery-overview.md) služby. Pokud nepoužíváte nástroj VMM, [v tomto kurzu](hyper-v-azure-tutorial.md) místo.

Toto je třetí kurz série, která ukazuje, jak nastavit zotavení po havárii do Azure pro místní virtuální počítače VMware. V předchozím kurzu jsme [připravené prostředí Hyper-V v místním](hyper-v-prepare-on-premises-tutorial.md) zotavení po havárii do Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vybrat zdroj a cíl replikace.
> * Nastavit zdrojové prostředí replikace, včetně místních komponent Site Recovery a cílové prostředí replikace.
> * Nastavte mapování sítě pro mapování mezi sítěmi virtuálních počítačů ve VMM a virtuálním sítím Azure.
> * Vytvořit zásadu replikace.
> * Povolit replikaci virtuálního počítače.

> [!NOTE]
> Kurzy vám ukážou, nejjednodušší způsob nasazení pro scénář. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny najdete v tématu v článcích **návody** část [dokumentace ke službě Site Recovery](https://docs.microsoft.com/azure/site-recovery).

## <a name="before-you-begin"></a>Než začnete

Toto je třetí kurz ze specializované série. Předpokládá, že jste už dokončili úlohy z předchozích kurzů:

1. [Příprava Azure](tutorial-prepare-azure.md)
2. [Příprava místního Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Výběr cíle replikace

1. Na webu Azure Portal, přejděte na **trezory služby Recovery Services** a vyberte trezor. Jsme připravili trezor **ContosoVMVault** v předchozím kurzu.
2. V **Začínáme**vyberte **Site Recovery**a pak vyberte **připravit infrastrukturu**.
3. V **cíl ochrany** > **kde jsou vaše počítače umístěné?** vyberte **On-premises**.
4. V **kam chcete počítače replikovat?** vyberte **do Azure**.
5. V **jsou vaše počítače virtualizované?** vyberte **Ano, s technologií Hyper-V**.
6. V **používáte System Center VMM ke správě hostitelů technologie Hyper-V?** vyberte **Ano**.
7.  Vyberte **OK**.

    ![Cíl replikace](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

1. V **plánování nasazení**, pokud máte v plánu velkého nasazení, stáhněte si Deployment Planneru pro Hyper-V z odkazu na stránce. [Další informace](hyper-v-deployment-planner-overview.md) o plánování nasazení technologie Hyper-V.
2. V tomto kurzu nepotřebujeme plánovače nasazení služby. V **dokončili jste plánování nasazení?** vyberte **udělám to později**a pak vyberte **OK**.

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Nastavit zdrojové prostředí, nainstalujte zprostředkovatele Azure Site Recovery na VMM server a zaregistrujte server v trezoru. Instalace agenta služeb zotavení Azure na každém hostiteli Hyper-V.

1. V **připravit infrastrukturu**vyberte **zdroj**.
2. V **připravit zdroj**vyberte **+ VMM** přidejte VMM server. V části **Přidat server** zkontrolujte, že se v poli **Typ serveru** zobrazí **Server System Center VMM**.
3. Stáhněte si instalační program pro zprostředkovatele Microsoft Azure Site Recovery.
4. Stáhnout registrační klíč trezoru Tento klíč budete potřebovat, když spustíte instalační program zprostředkovatele. Klíč je platný pět dní od jeho vygenerování.
5. Stáhněte si instalační program pro agenta Microsoft Azure Recovery Services.

    ![Stáhnout agenta, zprostředkovatele a registrační klíč](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalace zprostředkovatele na server VMM

1. V průvodci instalací zprostředkovatele Azure Site Recovery v části **Microsoft Update** vyjádřete výslovný souhlas s použitím Microsoft Update ke kontrole aktualizací zprostředkovatele.
2. V **instalace**, přijměte výchozí umístění instalace zprostředkovatele a vyberte **nainstalovat**.
3. Po instalaci v Průvodci registrací Microsoft Azure Site Recovery > **nastavení trezoru**vyberte **Procházet**a v **soubor klíče**, vyberte soubor klíče trezoru, který jste si stáhli.
4. Zadejte předplatné Azure Site Recovery a název trezoru (**ContosoVMVault**). Zadejte popisný název serveru VMM, který ji identifikuje v trezoru.
5. V části **Nastavení proxy** vyberte **Připojit přímo k Azure Site Recovery bez proxy**.
6. Přijměte výchozí umístění pro certifikát, který se používá k šifrování dat. Šifrovaná data bude možné dešifrovat, při převzetí služeb při selhání.
7. V **synchronizovat metadata cloudu**vyberte **synchronizace cloudu metadata k portálu Site Recovery**. Tato akce musí provést jenom jednou na každém serveru. Vyberte **zaregistrovat**.
8. Poté, co server je zaregistrovaný v trezoru, vybrat **Dokončit**.

Po dokončení registrace Azure Site Recovery načte metadata ze serveru a VMM server se zobrazí v **infrastruktura Site Recovery**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Instalace agenta služby Recovery Services na hostitelích Hyper-V

Nainstalujte agenta na každého hostitele Hyper-V obsahující virtuální počítače, které chcete replikovat.

1. V Microsoft Azure Recovery Services Průvodce instalací agenta nástroje > **Kontrola předpokladů**vyberte **Další**. Veškeré chybějící požadované položky se budou instalovat automaticky.
2. V **nastavení instalace**, přijměte umístění instalace a umístění mezipaměti. Jednotka mezipaměti vyžaduje minimálně 5 GB úložiště. Doporučujeme jednotku s 600 GB nebo více volného místa. Pak vyberte **Nainstalovat**.
3. V **instalace**, po dokončení instalace, vyberte **Zavřít** dokončete průvodce.

    ![Instalace agenta](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

1. Vyberte **Příprava infrastruktury** > **Cíl**.
2. Vyberte předplatné a skupinu prostředků (**ContosoRG**) ve virtuálních počítačích Azure vytvoří po převzetí služeb při selhání.
3. Vyberte **Resource Manageru** modelu nasazení.

Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.

## <a name="configure-network-mapping"></a>Konfigurace mapování sítě

1. V **infrastruktura Site Recovery** > **mapování sítí** > **mapování sítě**, vyberte **+ mapování sítě** ikonu.
2. V **přidání mapování sítě**, vyberte zdrojový server VMM. Vyberte **Azure** jako cíl.
3. Ověřte předplatné a model nasazení po převzetí služeb při selhání.
4. V **zdrojovou síť**, vyberte zdrojovou místní síť virtuálních počítačů.
5. V **Cílová síť**, vyberte síť Azure, ve které virtuální počítače Azure repliky budou umístěné při jejich vytvoření po převzetí služeb při selhání. Pak vyberte **OK**.

    ![Mapování sítě](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

1. Vyberte **připravit infrastrukturu** > **nastavení replikace** >  **+ vytvořit a přidružit**.
2. V části **Vytvořit a přidružit zásady** zadejte název zásady. Používáme **ContosoReplicationPolicy**.
3. Ponechejte výchozí nastavení a vyberte **OK**.
    - **Frekvence kopírování** označuje, že po počáteční replikaci se rozdílová data budou replikovat každých pět minut.
    - **Uchování bodu obnovení** označuje, že každý bod obnovení se uchovávají dvě hodiny.
    - **Frekvence snímků konzistentních vzhledem k aplikacím** označuje, že se body obnovení obsahující snímky konzistentní vzhledem k aplikacím budou vytvářet každou hodinu.
    - **Čas spuštění počáteční replikace** označuje, že počáteční replikace spustí okamžitě.
    - **Šifrovat data ve službě Azure** je nastavena na výchozí hodnotu (**vypnout**) a oznamuje, že data v klidovém stavu v Azure nejsou šifrována.
4. Po vytvoření zásady vyberte **OK**. Když vytvoříte novou zásadu, automaticky se přidruží ke cloudu VMM.

## <a name="enable-replication"></a>Povolení replikace

1. V **replikovat aplikaci**vyberte **zdroj**.
2. V **zdroj**, vyberte VMM cloud. Pak vyberte **OK**.
3. V **cílové**, ověření cíle (Azure), předplatné trezoru a vyberte **Resource Manageru** modelu.
4. Vyberte **contosovmsacct1910171607** účtu úložiště a **ContosoASRnet** sítě Azure.
5. V **virtuálních počítačů** > **vyberte**, vyberte virtuální počítač, který chcete replikovat. Pak vyberte **OK**.

   Průběh akce **Povolení ochrany** můžete sledovat v části **Úlohy** > **Úlohy Site Recovery**. Po **dokončit ochranu** úloha dokončí, dokončení počáteční replikace a virtuální počítač připravený k převzetí služeb při selhání.

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md)
