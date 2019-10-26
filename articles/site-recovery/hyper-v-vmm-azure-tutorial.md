---
title: Nastavení zotavení po havárii pro místní virtuální počítače Hyper-V v cloudech VMM do Azure pomocí Site Recovery
description: Naučte se, jak nastavit zotavení po havárii místních virtuálních počítačů Hyper-V v cloudech System Center VMM do Azure pomocí Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: f686a02e363025daa5d0c3b3d4e53e07da636544
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933833"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Nastavení zotavení po havárii místních virtuálních počítačů Hyper-V v cloudech VMM do Azure

Tento článek popisuje, jak povolit replikaci místních virtuálních počítačů Hyper-V spravovaných nástrojem System Center Virtual Machine Manager (VMM) pro zotavení po havárii do Azure pomocí služby [Azure Site Recovery](site-recovery-overview.md) . Pokud nepoužíváte VMM, [použijte místo toho tento kurz](hyper-v-azure-tutorial.md) .

Toto je třetí kurz v řadě, ve kterém se dozvíte, jak nastavit zotavení po havárii do Azure pro místní virtuální počítače VMware. V předchozím kurzu jsme [připravili místní prostředí Hyper-V](hyper-v-prepare-on-premises-tutorial.md) pro zotavení po havárii do Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vybrat zdroj a cíl replikace.
> * Nastavte zdrojové prostředí replikace, včetně místních Site Recovery komponent a cílového prostředí replikace.
> * Nastavte mapování sítě pro mapování mezi sítěmi virtuálních počítačů VMM a virtuálními sítěmi Azure.
> * Vytvořit zásadu replikace.
> * Povolit replikaci virtuálního počítače.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu k nasazení scénáře. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny najdete v článcích v [dokumentaci k Site Recovery](https://docs.microsoft.com/azure/site-recovery)v části **návody** .

> [!WARNING]
> Upozorňujeme, že podpora ASR pro použití konfigurace SCVMM v účtu bude brzy zastaralá a proto doporučujeme, abyste si před pokračováním přečetli podrobnosti o [zastaralosti](scvmm-site-recovery-deprecation.md) .


## <a name="before-you-begin"></a>Než začnete

Toto je třetí kurz ze specializované série. Předpokládá se, že jste již dokončili úlohy v předchozích kurzech:

1. [Příprava Azure](tutorial-prepare-azure.md)
2. [Příprava místního Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Výběr cíle replikace

1. V Azure Portal otevřete **Recovery Services trezory** a vyberte trezor. Připravili jsme trezor **ContosoVMVault** v předchozím kurzu.
2. V **Začínáme**vyberte **Site Recovery**a pak vyberte **připravit infrastrukturu**.
3. V **cíli ochrany** > **kde jsou vaše počítače umístěné?** vyberte **místní**.
4. V **místě, kam chcete počítače replikovat?** vyberte možnost **do Azure**.
5. V nástroji **jsou virtualizované počítače?** vyberte **Ano, s technologií Hyper-V**.
6. V **nástroji používáte nástroj System Center VMM ke správě hostitelů Hyper-V?** vyberte **Ano**.
7.  Vyberte **OK**.

    ![Cíl replikace](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

1. Pokud plánujete rozsáhlé nasazení, v části **Plánování nasazení**si stáhněte Plánovač nasazení pro Hyper-V z odkazu na stránce. [Přečtěte si další informace](hyper-v-deployment-planner-overview.md) o plánování nasazení technologie Hyper-V.
2. Pro tento kurz nepotřebujeme Plánovač nasazení. **Dokončili jste plánování nasazení?** vyberte **I později**a pak vyberte **OK**.

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Při nastavování zdrojového prostředí nainstalujete poskytovatele Azure Site Recovery na server VMM a zaregistrujete Server do trezoru. Agenta Azure Recovery Services nainstalujete na každého hostitele Hyper-V.

1. V oblasti **Příprava infrastruktury**vyberte **zdroj**.
2. V rámci **Příprava zdroje**vyberte **+ VMM** a přidejte server VMM. V části **Přidat server** zkontrolujte, že se v poli **Typ serveru** zobrazí **Server System Center VMM**.
3. Stáhněte instalační program pro poskytovatele Microsoft Azure Site Recovery.
4. Stáhněte registrační klíč trezoru. Tento klíč budete potřebovat při spuštění instalace zprostředkovatele. Klíč je platný pět dní od jeho vygenerování.
5. Stáhněte instalační program pro agenta Microsoft Azure Recovery Services.

    ![Stažení poskytovatele, registračního klíče a agenta](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalace zprostředkovatele na server VMM

1. V průvodci instalací zprostředkovatele Azure Site Recovery v části **Microsoft Update** vyjádřete výslovný souhlas s použitím Microsoft Update ke kontrole aktualizací zprostředkovatele.
2. V části **instalace**přijměte výchozí umístění instalace pro poskytovatele a vyberte **nainstalovat**.
3. Po instalaci v Průvodci registrací Site Recovery Microsoft Azure > **Nastavení trezoru**vyberte **Procházet**a v **souboru klíče**vyberte soubor klíče trezoru, který jste stáhli.
4. Zadejte Azure Site Recovery předplatné a název trezoru (**ContosoVMVault**). Zadejte popisný název serveru VMM, který chcete v trezoru identifikovat.
5. V části **Nastavení proxy** vyberte **Připojit přímo k Azure Site Recovery bez proxy**.
6. Přijměte výchozí umístění certifikátu, který se používá k šifrování dat. Při převzetí služeb při selhání budou šifrovaná data dešifrována.
7. V **synchronizovat metadata cloudu**vyberte **synchronizovat metadata cloudu na Site Recovery portál**. Tato akce musí na každém serveru probíhat pouze jednou. Pak vyberte **Registrovat**.
8. Po zaregistrování serveru v trezoru vyberte **Dokončit**.

Po dokončení registrace se načte metadata ze serveru Azure Site Recovery a v **infrastruktuře Site Recovery**se zobrazí server VMM.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Instalace agenta Recovery Services na hostitele Hyper-V

Nainstalujte agenta na všechny hostitele Hyper-V obsahující virtuální počítače, které chcete replikovat.

1. V Průvodci instalací agenta Microsoft Azure Recovery Services > **kontroly požadovaných součástí**vyberte **Další**. Všechny chybějící požadované součásti se nainstalují automaticky.
2. V **nastavení instalace**přijměte umístění instalace a umístění mezipaměti. Jednotka mezipaměti potřebuje alespoň 5 GB úložného prostoru. Doporučujeme jednotku s 600 GB nebo více volného místa. Pak vyberte **Nainstalovat**.
3. Po dokončení instalace **klikněte na tlačítko** **Zavřít** a dokončete průvodce.

    ![Nainstalovat agenta](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

1. Vyberte **Příprava infrastruktury** > **Cíl**.
2. Vyberte předplatné a skupinu prostředků (**ContosoRG**), ve kterých se po převzetí služeb při selhání vytvoří virtuální počítače Azure.
3. Vyberte model nasazení **Správce prostředků** .

Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.

## <a name="configure-network-mapping"></a>Konfigurace mapování sítě

1. V **Site Recovery infrastruktura** > mapování **sítě** > **mapování sítě**vyberte ikonu **+ mapování sítě** .
2. V části **Přidat mapování sítě**vyberte zdrojový server VMM. Jako cíl vyberte **Azure** .
3. Ověřte předplatné a model nasazení po převzetí služeb při selhání.
4. V části **zdrojová síť**vyberte zdroj místní sítě virtuálních počítačů.
5. V části **Cílová síť**vyberte síť Azure, ve které se budou při vytváření převzetí služeb při selhání umístit virtuální počítače Azure repliky. Pak vyberte **OK**.

    ![Mapování sítě](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

1. Vyberte **připravit infrastrukturu** > **nastavení replikace** >  **+ vytvořit a přidružit**.
2. V části **Vytvořit a přidružit zásady** zadejte název zásady. Používáme **ContosoReplicationPolicy**.
3. Ponechte výchozí nastavení a vyberte **OK**.
    - **Frekvence kopírování** znamená, že po počáteční replikaci se rozdílová data replikují každých pět minut.
    - **Uchovávání bodu obnovení** indikuje, že každý bod obnovení bude uchován dvě hodiny.
    - **Frekvence snímků konzistentních vzhledem k aplikacím** označuje, že se body obnovení obsahující snímky konzistentní vzhledem k aplikacím budou vytvářet každou hodinu.
    - **Čas zahájení počáteční replikace** indikuje, že se počáteční replikace spustí hned.
    - **Šifrování dat uložených v Azure** je nastavené na výchozí (**vypnuto**) a označuje, že data uložená v Azure nejsou šifrovaná.
4. Po vytvoření zásady vyberte **OK**. Když vytvoříte novou zásadu, automaticky se přidruží k cloudu VMM.

## <a name="enable-replication"></a>Povolení replikace

1. V **aplikaci replikovat aplikaci**vyberte **zdroj**.
2. V části **zdroj**vyberte Cloud VMM. Pak vyberte **OK**.
3. V části **cíl**ověřte cíl (Azure), předplatné trezoru a vyberte model **Správce prostředků** .
4. Vyberte účet úložiště **contosovmsacct1910171607** a síť Azure **ContosoASRnet** .
5. V části **virtuální počítače** > **Vybrat**vyberte virtuální počítač, který chcete replikovat. Pak vyberte **OK**.

   Průběh akce **Povolení ochrany** můžete sledovat v části **Úlohy** > **Úlohy Site Recovery**. Po dokončení úlohy **dokončení ochrany** se počáteční replikace dokončí a virtuální počítač je připravený na převzetí služeb při selhání.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md)
