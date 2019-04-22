---
title: Nastavení zotavení po havárii do Azure pro místní virtuální počítače Hyper-V (bez VMM) pomocí Azure Site Recovery | Microsoft Docs
description: Zjistěte, jak nastavit zotavení po havárii místních virtuálních počítačů Hyper-V (bez VMM) do Azure pomocí služby Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: da643a4d7a1dc74385b3854c1952af5ba93bd241
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59358093"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Nastavení zotavení po havárii místních virtuálních počítačů Hyper-V do Azure

c

> [!div class="checklist"]
> * Vybrat zdroj a cíl replikace.
> * Nastavit zdrojové prostředí replikace, včetně místních komponent řešení Site Recovery, a cílové prostředí replikace.
> * Vytvořit zásadu replikace.
> * Povolit replikaci virtuálního počítače.

> [!NOTE]
> Kurzy vám ukážou, nejjednodušší způsob nasazení pro scénář. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny přečtěte si článek v části How To Site Recovery obsahu.

## <a name="before-you-begin"></a>Než začnete
Toto je třetí kurz ze specializované série. Tento kurz předpokládá, že jste už dokončili úlohy z předchozích kurzů:

1. [Příprava Azure](tutorial-prepare-azure.md)
2. [Příprava místního Hyper-V](tutorial-prepare-on-premises-hyper-v.md)



## <a name="select-a-replication-goal"></a>Výběr cíle replikace

1. V okně **Trezory Recovery Services** vyberte trezor. Jsme připravili trezor **ContosoVMVault** v předchozím kurzu.
2. V části **Začínáme** klikněte na **Site Recovery**. Pak klikněte na **Příprava infrastruktury**.
3. V **cíl ochrany** > **kde jsou vaše počítače umístěné?** vyberte **On-premises**.
4. V **kam chcete počítače replikovat?** vyberte **do Azure**.
5. V **jsou vaše počítače virtualizované?** vyberte **Ano, s technologií Hyper-V**.
6. V části **Používáte System Center VMM ke správě svých hostitelů Hyper-V** vyberte **Ne**. Pak klikněte na **OK**.

    ![Cíl replikace](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

1. V **plánování nasazení**, pokud máte v plánu velkého nasazení, stáhněte si Deployment Planneru pro Hyper-V z odkazu na stránce. [Další informace](hyper-v-deployment-planner-overview.md) o plánování nasazení technologie Hyper-V.
2. Pro účely tohoto kurzu nepotřebujeme plánovače nasazení služby. V **dokončili jste plánování nasazení?** vyberte **udělám to později**. Pak klikněte na **OK**.

    ![Plánování nasazení](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Nastavit zdrojové prostředí, vytvoříte do lokality Hyper-V a přidáte hostitele Hyper-V obsahující virtuální počítače, které chcete replikovat do lokality. Potom stáhněte a nainstalujte zprostředkovatele Azure Site Recovery a agenta Azure Recovery Services a zaregistrujte lokalitu Hyper-V v trezoru. 

1. V části **připravit infrastrukturu**, klikněte na tlačítko **zdroj**.
2. V **připravit zdroj**, klikněte na tlačítko **+ lokalita Hyper-V**.
3. V **z lokality Hyper-V vytvořit**a zadejte název lokality. Používáme **ContosoHyperVSite**.

    ![Lokalita Hyper-V](./media/hyper-v-azure-tutorial/hyperv-site.png)

3. Po vytvoření webu, v **připravit zdroj** > **krok 1: Vyberte z lokality Hyper-V**, vyberte web, který jste vytvořili.
4. Klikněte na **+ Server Hyper-V**.

    ![Server Hyper-V](./media/hyper-v-azure-tutorial/hyperv-server.png)

4. Stáhněte si instalační program pro zprostředkovatele Microsoft Azure Site Recovery.
6. Stáhněte registrační klíč trezoru. Potřebujete tento klíč pro instalaci poskytovatele. Klíč je platný pět dní od jeho vygenerování.

    ![Stažení zprostředkovatele](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Instalace zprostředkovatele

Nainstalujte na stažený instalační soubor (AzureSiteRecoveryProvider.exe) na každém hostiteli Hyper-V, které chcete přidat do lokality Hyper-V. Na každém hostiteli Hyper-V se nainstaluje zprostředkovatel Azure Site Recovery a agent Recovery Services.

1. Spusťte instalační soubor.
2. V průvodci instalací zprostředkovatele Azure Site Recovery v části **Microsoft Update** vyjádřete výslovný souhlas s použitím Microsoft Update ke kontrole aktualizací zprostředkovatele.
3. V části **Instalace** přijměte výchozí umístění instalace zprostředkovatele a agenta a klikněte na **Nainstalovat**.
4. Po instalaci v průvodci registrací Microsoft Azure Site Recovery v části **Nastavení trezoru** klikněte na **Procházet** a v části **Soubor klíče** vyberte stažený soubor klíče trezoru. 
5. Zadejte předplatné Azure Site Recovery, název trezoru (**ContosoVMVault**), a název lokality Hyper-V (**ContosoHyperVSite**), do které patří server Hyper-V.
6. V části **Nastavení proxy** vyberte **Připojit přímo k Azure Site Recovery bez proxy**.
7. Po registraci serveru v trezoru v části **Registrace** klikněte na **Dokončit**.

Azure Site Recovery načte metadata ze serveru Hyper-V a server se zobrazí v části **Infrastruktura Site Recovery** > **Hostitelé Hyper-V**. Tento proces může trvat až 30 minut.        

#### <a name="install-on-hyper-v-core-server"></a>Instalace jádra serveru technologie Hyper-V

Pokud používáte Hyper-V server core, stáhněte si instalační soubor a postupujte takto:

1. Extrahujte soubory z AzureSiteRecoveryProvider.exe do místního adresáře spuštěním

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2.  Spuštění ".\setupdr.exe /i. Výsledky jsou protokolovány % Programdata%\ASRLogs\DRASetupWizard.log.

3.  Registrace serveru pomocí tohoto příkazu:

    ```
    cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```
 

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky. 

1. Klikněte na **Příprava infrastruktury** > **Cíl**.
2. Vyberte předplatné a skupinu prostředků **ContosoRG**, ve kterých se vytvoří virtuální počítače Azure po převzetí služeb při selhání.
3. Vyberte model nasazení **Resource Manager**.

Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.


## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

1. Klikněte na **Příprava infrastruktury** > **Nastavení replikace** > **+ Vytvořit a přidružit**.
2. V části **Vytvořit a přidružit zásady** zadejte název zásady. Používáme **ContosoReplicationPolicy**.
3. Pro účely tohoto kurzu Ponecháme výchozí nastavení.
    - **Frekvence kopírování** udává, jak často budou replikovat rozdílová data (po počáteční replikaci), každých pět minut, ve výchozím nastavení.
    - **Uchování bodu obnovení** označuje, že se uchovají body obnovení pro dvě hodiny.
    - **Frekvence snímků konzistentních vzhledem k aplikacím** označuje, že se body obnovení obsahující snímky konzistentní vzhledem k aplikacím budou vytvářet každou hodinu.
    - **Čas spuštění počáteční replikace** označuje, že se počáteční replikace spustí okamžitě.
4. Po vytvoření zásady klikněte na **OK**. Když vytvoříte novou zásadu, se automaticky přidruží zadané lokalitě Hyper-V (v našem kurzu to **ContosoHyperVSite**)

    ![Zásady replikace](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>Povolení replikace


1. V části **Replikovat aplikaci** klikněte na **Zdroj**. 
2. V části **Zdroj** vyberte lokalitu **ContosoHyperVSite**. Pak klikněte na **OK**.
3. V části **Cíl** potvrďte jako cíl Azure, předplatné trezoru a model nasazení **Resource Manager**.
4. Pokud používáte kurz nastavení, vyberte **contosovmsacct1910171607** vytvořili v předchozím kurzu pro replikovaná data účet úložiště a **ContosoASRnet** sítě, ve které budou virtuální počítače Azure nachází se po převzetí služeb při selhání.
5. V části **Virtuální počítače** > **Vybrat** vyberte virtuální počítač, který chcete replikovat. Pak klikněte na **OK**.

   Průběh akce **Povolení ochrany** můžete sledovat v části **Úlohy** > **Úlohy Site Recovery**. Po dokončení úlohy **Dokončení ochrany** bude počáteční replikace dokončený a virtuální počítač bude připravený na převzetí služeb při selhání.

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md)
