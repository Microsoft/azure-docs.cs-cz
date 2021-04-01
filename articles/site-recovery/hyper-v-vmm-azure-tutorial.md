---
title: Nastavení technologie Hyper-V (s nástrojem VMM) zotavení po havárii pomocí Azure Site Recovery
description: Naučte se, jak nastavit zotavení po havárii místních virtuálních počítačů Hyper-V v cloudech System Center VMM do Azure pomocí Site Recovery.
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: c806f968bc6530879f64ddbf6fd4c7d45aa7a8d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89442816"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Nastavení zotavení po havárii místních virtuálních počítačů Hyper-V v cloudech VMM do Azure

V tomto kurzu se dozvíte, jak povolit replikaci místních virtuálních počítačů Hyper-V spravovaných pomocí System Center Virtual Machine Manager (VMM) do Azure pomocí [Azure Site Recovery](site-recovery-overview.md). Pokud nepoužíváte VMM, [postupujte podle tohoto kurzu](hyper-v-azure-tutorial.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vybrat zdroj a cíl replikace.
> * Nastavte zdrojové prostředí replikace, včetně místních Site Recovery komponent a cílového prostředí replikace.
> * Nastavte mapování sítě mezi sítěmi virtuálních počítačů VMM a virtuálními sítěmi Azure.
> * Vytvořit zásadu replikace.
> * Povolit replikaci virtuálního počítače.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu k nasazení scénáře. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny najdete v článcích v [dokumentaci k Site Recovery](./index.yml)v části **návody** .

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že jste už dokončili následující kurzy:

1. [Příprava Azure](tutorial-prepare-azure.md)
1. [Příprava místních serverů Hyper-V](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Výběr cíle replikace

1. V Azure Portal přejdete do **Recovery Services trezorů** a vyberete trezor **ContosoVMVault** , který byl vytvořen v kurzu [Příprava Azure](tutorial-prepare-azure.md#create-a-recovery-services-vault) .
1. Z **Začínáme** vyberte **Site Recovery**  >  **připravit infrastrukturu** a nakonfigurujte následující nastavení:
    1. **Cíl ochrany**  >  **Kde jsou vaše počítače umístěné?** vyberte **místní**.
    1. **Kam chcete počítače replikovat?**, vyberte **do Azure**.
    1. **Jsou vaše počítače virtualizované?** vyberte **Ano, s technologií Hyper-V**.
    1. Používáte **System Center VMM ke správě hostitelů Hyper-V?** vyberte **Ano**.
1. Vyberte **OK**.

   ![Cíl replikace](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

1. Pokud plánujete rozsáhlé nasazení, v části **Plánování nasazení** si stáhněte Plánovač nasazení pro Hyper-V z odkazu na stránce. [Přečtěte si další informace](hyper-v-deployment-planner-overview.md) o plánování nasazení technologie Hyper-V.
1. Pro tento kurz nepotřebujeme Plánovač nasazení. **Dokončili jste plánování nasazení?** vyberte **I později** a pak vyberte **OK**.

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Při nastavování zdrojového prostředí nainstalujete poskytovatele Azure Site Recovery na server VMM a zaregistrujete Server do trezoru. Agenta Azure Recovery Services nainstalujete na každého hostitele Hyper-V.

1. **Připravte infrastrukturu**. Vyberte **zdroj**.
1. **Připravte zdroj**. Vyberte **+ VMM** a přidejte server VMM. V části **Přidat server** zkontrolujte, že se v poli **Typ serveru** zobrazí **Server System Center VMM**.
1. Stáhněte instalační program pro poskytovatele Microsoft Azure Site Recovery.
1. Stáhněte registrační klíč trezoru. Tento klíč budete potřebovat při spuštění instalace zprostředkovatele. Klíč je platný pět dní od jeho vygenerování.
1. Stáhněte instalační program pro agenta Microsoft Azure Recovery Services.

   ![Stažení poskytovatele, registračního klíče a agenta](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalace zprostředkovatele na server VMM

1. V Průvodci instalací poskytovatele Azure Site Recovery **Microsoft Update**. Pokud chcete vyhledat aktualizace poskytovatele, přihlaste se k používání Microsoft Update.
1. **Instalace**. Přijměte výchozí umístění instalace pro poskytovatele a vyberte **nainstalovat**.
1. Po instalaci vyberte v Průvodci registrací Site Recovery Microsoft Azure možnost **Nastavení trezoru**, **Procházet** a v **souboru klíče** vyberte soubor klíče trezoru, který jste stáhli.
1. Zadejte Azure Site Recovery předplatné a název trezoru (**ContosoVMVault**). Zadejte popisný název serveru VMM, který chcete v trezoru identifikovat.
1. **Nastavení proxy serveru**. Vyberte **připojit se přímo k Azure Site Recovery bez proxy serveru**.
1. Přijměte výchozí umístění certifikátu, který se používá k šifrování dat. Při převzetí služeb při selhání budou šifrovaná data dešifrována.
1. **Synchronizujte metadata cloudu**. Vyberte **synchronizovat metadata cloudu na Site Recovery Portal**. Tato akce musí na každém serveru probíhat pouze jednou. Pak vyberte **Registrovat**.
1. Po zaregistrování serveru v trezoru vyberte **Dokončit**.

Po dokončení registrace se metadata ze serveru načtou pomocí Azure Site Recovery a server VMM se zobrazí v **infrastruktuře Site Recovery**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Instalace agenta Recovery Services na hostitele Hyper-V

Nainstalujte agenta na všechny hostitele Hyper-V obsahující virtuální počítače, které chcete replikovat.

V Průvodci instalací agenta Microsoft Azure Recovery Services nakonfigurujte tato nastavení:

1. **Kontrolu předpokladů**. Vyberte **Další**. Všechny chybějící požadované součásti se nainstalují automaticky.
1. **Nastavení instalace**. Přijměte umístění instalace. Pak vyberte **Nainstalovat**.

    >[!NOTE]
    >Pro Azure Site Recovery není **umístění mezipaměti** vyžadováno.

1. **Instalace**. Po dokončení instalace vyberte **Zavřít** a dokončete průvodce.

   ![Nainstalovat agenta](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

1. Vyberte **připravit**  >  **cíl** infrastruktury.
1. Vyberte předplatné a skupinu prostředků (**ContosoRG**), ve kterých se po převzetí služeb při selhání vytvoří virtuální počítače Azure.
1. Vyberte model nasazení **Správce prostředků** .

Site Recovery kontroluje, jestli existuje minimálně jeden kompatibilní účet úložiště Azure a sítě.

## <a name="configure-network-mapping"></a>Konfigurace mapování sítě

1. **Site Recovery infrastruktura**  >  **Mapování sítě**  >  **Mapování sítě**. Vyberte ikonu **+ mapování sítě** .
1. **Přidejte mapování sítě**. Vyberte **zdrojový Server System Center VMM** . Jako **cíl** vyberte Azure.
1. Ověřte předplatné a model nasazení po převzetí služeb při selhání.
1. **Zdrojová síť**. Vyberte zdroj místní sítě virtuálních počítačů.
1. **Cílová síť**. Vyberte síť Azure, ve které se budou při vytváření převzetí služeb při selhání umístit virtuální počítače Azure repliky. Pak vyberte **OK**.

   ![Mapování sítě](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

1. Vyberte **připravit**  >  **nastavení replikace** infrastruktury  >  **+ vytvořit a přidružit**.
1. V nastavení **vytvořit a přidružit zásady** zadejte název zásady. Používáme **ContosoReplicationPolicy**.
1. Přijměte výchozí nastavení a vyberte **OK**:
   - **Frekvence kopírování** znamená, že po počáteční replikaci se rozdílová data replikují každých pět minut.
   - **Uchovávání bodu obnovení** indikuje, že každý bod obnovení bude uchován dvě hodiny.
   - **Frekvence snímků konzistentních vzhledem k aplikacím** označuje, že se body obnovení obsahující snímky konzistentní vzhledem k aplikacím budou vytvářet každou hodinu.
   - **Čas zahájení počáteční replikace** indikuje, že se počáteční replikace spustí hned.
   - **Šifrování dat uložených v Azure** je nastavené na výchozí (**vypnuto**) a označuje, že data uložená v Azure nejsou šifrovaná.
1. Po vytvoření zásady vyberte **OK**. Když vytvoříte novou zásadu, automaticky se přidruží k cloudu VMM.

## <a name="enable-replication"></a>Povolení replikace

1. **Replikuje aplikaci**. Vyberte **zdroj**.
1. **Zdroj**. Vyberte Cloud VMM. Pak vyberte **OK**.
1. **Cíl**. Ověřte cíl (Azure), předplatné trezoru a vyberte model **Správce prostředků** .
1. Vyberte účet úložiště **contosovmsacct1910171607** a síť Azure **ContosoASRnet** .
1. **Virtuální počítače**  >  **Vyberte**. Vyberte virtuální počítač, který chcete replikovat. Pak vyberte **OK**.

   Průběh akce **Povolení ochrany** můžete sledovat v části **Úlohy** > **Úlohy Site Recovery**. Po dokončení úlohy **dokončení ochrany** se počáteční replikace dokončí a virtuální počítač je připravený na převzetí služeb při selhání.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Provedení postupu zotavení po havárii](tutorial-dr-drill-azure.md)
