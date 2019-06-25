---
title: Nastavení zotavení po havárii pro místní virtuální počítače Hyper-(bez VMM) do Azure pomocí Site Recovery | Dokumentace Microsoftu
description: Zjistěte, jak nastavit zotavení po havárii místních virtuálních počítačů Hyper-V (bez VMM) do Azure pomocí Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: f4c9ddf608033f6b85268fe74b12861eb8c30082
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295160"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Nastavení zotavení po havárii místních virtuálních počítačů Hyper-V do Azure

[Azure Site Recovery](site-recovery-overview.md) služby přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení z místních počítačů a Azure virtual machines (VM).

Toto je třetí kurz ze specializované série. To se dozvíte, jak nastavit zotavení po havárii místních virtuálních počítačů Hyper-V do Azure. Tento kurz se vztahuje virtuálních počítačů Hyper-V, která nejsou spravovaná pomocí Microsoft System Center Virtual Machine Manager (VMM).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vybrat zdroj a cíl replikace.
> * Nastavit zdrojové prostředí replikace, včetně místních komponent Site Recovery a cílové prostředí replikace.
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
6. V **používáte System Center VMM ke správě hostitelů technologie Hyper-V?** vyberte **ne**.
7. Vyberte **OK**.

    ![Cíl replikace](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

1. V **plánování nasazení**, pokud máte v plánu velkého nasazení, stáhněte si Deployment Planneru pro Hyper-V z odkazu na stránce. [Další informace](hyper-v-deployment-planner-overview.md) o plánování nasazení technologie Hyper-V.
2. V tomto kurzu nepotřebujeme plánovače nasazení služby. V **dokončili jste plánování nasazení?** vyberte **udělám to později**a pak vyberte **OK**.

    ![Plánování nasazení](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Nastavit zdrojové prostředí, vytvořte do lokality Hyper-V a přidejte k této lokalitě hostitele Hyper-V obsahující virtuální počítače, které chcete replikovat. Pak můžete stáhnout a nainstalovat zprostředkovatele Azure Site Recovery a agenta služeb zotavení Azure na každém hostiteli a zaregistrujete lokalitu Hyper-V v trezoru.

1. V části **připravit infrastrukturu**vyberte **zdroj**.
2. V **připravit zdroj**vyberte **+ lokalita Hyper-V**.
3. V **z lokality Hyper-V vytvořit**, zadejte název lokality. Používáme **ContosoHyperVSite**.

    ![Lokalita Hyper-V](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. Po vytvoření webu, v **připravit zdroj** > **krok 1: Vyberte z lokality Hyper-V**, vyberte web, který jste vytvořili.
5. Vyberte **+ Server Hyper-V**.

    ![Server Hyper-V](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Stáhněte si instalační program pro zprostředkovatele Microsoft Azure Site Recovery.
7. Stáhnout registrační klíč trezoru Potřebujete tento klíč pro instalaci poskytovatele. Klíč je platný pět dní od jeho vygenerování.

    ![Stažení zprostředkovatele a registrační klíč](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Instalace zprostředkovatele

Nainstalujte na stažený instalační soubor (AzureSiteRecoveryProvider.exe) na každém hostiteli Hyper-V, který chcete přidat do lokality Hyper-V. Instalační program nainstaluje zprostředkovatele Azure Site Recovery a agenta služby Recovery Services na každém hostiteli Hyper-V.

1. Spusťte instalační soubor.
2. V průvodci instalací zprostředkovatele Azure Site Recovery v části **Microsoft Update** vyjádřete výslovný souhlas s použitím Microsoft Update ke kontrole aktualizací zprostředkovatele.
3. V **instalace**, přijměte výchozí umístění instalace zprostředkovatele a agenta a vyberte **nainstalovat**.
4. Po instalaci v Průvodci registrací Microsoft Azure Site Recovery > **nastavení trezoru**vyberte **Procházet**a v **soubor klíče**, vyberte soubor klíče trezoru, který jste si stáhli.
5. Zadejte předplatné Azure Site Recovery, název trezoru (**ContosoVMVault**), a název lokality Hyper-V (**ContosoHyperVSite**), do které patří server Hyper-V.
6. V části **Nastavení proxy** vyberte **Připojit přímo k Azure Site Recovery bez proxy**.
7. V **registrace**po server je zaregistrovaný v trezoru, vyberte **Dokončit**.

Azure Site Recovery načte metadata ze serveru Hyper-V a server se zobrazí v části **Infrastruktura Site Recovery** > **Hostitelé Hyper-V**. Tento proces může trvat až 30 minut.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Nainstalujte zprostředkovatele na jádro serveru technologie Hyper-V

Pokud používáte Hyper-V server core, stáhněte si instalační soubor a postupujte podle těchto kroků:

1. Extrahujte soubory z AzureSiteRecoveryProvider.exe do místního adresáře spuštěním tohoto příkazu:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Spusťte `.\setupdr.exe /i`. Výsledky jsou protokolovány % Programdata%\ASRLogs\DRASetupWizard.log.

3. Registrace serveru spuštěním následujícího příkazu:

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky:

1. Vyberte **Příprava infrastruktury** > **Cíl**.
2. Vyberte předplatné a skupinu prostředků **ContosoRG** ve virtuálních počítačích Azure vytvoří po převzetí služeb při selhání.
3. Vyberte model nasazení **Resource Manager**.

Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.

## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

1. Vyberte **připravit infrastrukturu** > **nastavení replikace** >  **+ vytvořit a přidružit**.
2. V části **Vytvořit a přidružit zásady** zadejte název zásady. Používáme **ContosoReplicationPolicy**.
3. Pro účely tohoto kurzu Ponecháme výchozí nastavení:
    - **Frekvence kopírování** udává, jak často budou replikovat rozdílová data (po počáteční replikaci). Je výchozí frekvence každých pět minut.
    - **Uchování bodu obnovení** označuje, že se uchovají body obnovení pro dvě hodiny.
    - **Frekvence snímků konzistentních vzhledem k aplikacím** označuje, že se body obnovení obsahující snímky konzistentní vzhledem k aplikacím budou vytvářet každou hodinu.
    - **Čas spuštění počáteční replikace** označuje, že počáteční replikace spustí okamžitě.
4. Po vytvoření zásady vyberte **OK**. Když vytvoříte novou zásadu, se automaticky přidruží zadané lokalitě Hyper-V. V našem kurzu to **ContosoHyperVSite**.

    ![Zásady replikace](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Povolení replikace

1. V **replikovat aplikaci**vyberte **zdroj**.
2. V části **Zdroj** vyberte lokalitu **ContosoHyperVSite**. Pak vyberte **OK**.
3. V **cílové**, ověření cíle (Azure), předplatné trezoru a **Resource Manageru** modelu nasazení.
4. Pokud používáte kurz nastavení, vyberte **contosovmsacct1910171607** vytvořili v předchozím kurzu pro replikovaná data účet úložiště. Také vyberte **ContosoASRnet** síť, ve které virtuální počítače Azure budou umístěné po převzetí služeb při selhání.
5. V **virtuálních počítačů** > **vyberte**, vyberte virtuální počítač, který chcete replikovat. Pak vyberte **OK**.

   Průběh akce **Povolení ochrany** můžete sledovat v části **Úlohy** > **Úlohy Site Recovery**. Po **dokončit ochranu** úloha dokončí, dokončení počáteční replikace a virtuální počítač připravený k převzetí služeb při selhání.

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md)
