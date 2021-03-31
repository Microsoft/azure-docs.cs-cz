---
title: Nastavení zotavení po havárii technologie Hyper-V pomocí Azure Site Recovery
description: Přečtěte si, jak nastavit zotavení po havárii místních virtuálních počítačů Hyper-V (bez VMM) do Azure pomocí Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5ce98c785700301bba92926d7d5a243b614eca7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87504224"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Nastavení zotavení po havárii místních virtuálních počítačů Hyper-V do Azure

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá k vaší strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení místních počítačů a virtuálních počítačů Azure (VM).

Toto je třetí kurz ze specializované série. Ukazuje, jak nastavit zotavení po havárii místních virtuálních počítačů Hyper-V do Azure. Tento kurz se týká virtuálních počítačů Hyper-V, které nespravuje Microsoft System Center Virtual Machine Manager (VMM).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vybrat zdroj a cíl replikace.
> * Nastavte zdrojové prostředí replikace, včetně místních Site Recovery komponent a cílového prostředí replikace.
> * Vytvořit zásadu replikace.
> * Povolit replikaci virtuálního počítače.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu k nasazení scénáře. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny najdete v článcích v [dokumentaci k Site Recovery](./index.yml)v části **návody** .



## <a name="before-you-begin"></a>Než začnete

Toto je třetí kurz ze specializované série. Předpokládá se, že jste již dokončili úlohy v předchozích kurzech:

1. [Příprava Azure](tutorial-prepare-azure.md)
2. [Příprava místního Hyper-V](./hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Výběr cíle replikace

1. V Azure Portal otevřete **Recovery Services trezory** a vyberte trezor. Připravili jsme trezor **ContosoVMVault** v předchozím kurzu.
2. V **Začínáme** vyberte **Site Recovery** a pak vyberte **připravit infrastrukturu**.
3. V nabídce **cíl ochrany**  >  ,**kde jsou vaše počítače umístěné?** vyberte **místní**.
4. V **místě, kam chcete počítače replikovat?** vyberte možnost **do Azure**.
5. V nástroji **jsou virtualizované počítače?** vyberte **Ano, s technologií Hyper-V**.
6. V **nástroji používáte nástroj System Center VMM ke správě hostitelů technologie Hyper-V?** vyberte možnost **ne**.
7. Vyberte **OK**.

    ![Snímek obrazovky s možnostmi cíle ochrany v části Příprava infrastruktury](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

1. Pokud plánujete rozsáhlé nasazení, v části **Plánování nasazení** si stáhněte Plánovač nasazení pro Hyper-V z odkazu na stránce. [Přečtěte si další informace](hyper-v-deployment-planner-overview.md) o plánování nasazení technologie Hyper-V.
2. Pro tento kurz nepotřebujeme Plánovač nasazení. **Dokončili jste plánování nasazení?** vyberte **I později** a pak vyberte **OK**.

    ![Snímek možností plánování nasazení v části Příprava infrastruktury](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Zdrojové prostředí nastavíte tak, že vytvoříte lokalitu Hyper-V a do této lokality přidáte hostitele Hyper-V obsahující virtuální počítače, které chcete replikovat. Pak stáhnete a nainstalujete poskytovatele Azure Site Recovery a agenta Azure Recovery Services na každého hostitele a zaregistrujete lokalitu Hyper-V v trezoru.

1. V části **připravit infrastrukturu** vyberte **zdroj**.
2. V rozevíracím sestavování **zdroj** vyberte **+ lokalita Hyper-V**.
3. V části **vytvořit lokalitu technologie Hyper-V** zadejte název lokality. Používáme **ContosoHyperVSite**.

    ![Snímek obrazovky s výběrem webu Hyper-V v přípravě infrastruktury.](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. Po vytvoření lokality v části **připravit zdroj**  >  **Krok 1: Vyberte lokalitu Hyper-V**, vyberte lokalitu, kterou jste vytvořili.
5. Vyberte **+ Server Hyper-V**.

    ![Snímek obrazovky s výběrem serveru Hyper-V v přípravě infrastruktury](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Stáhněte instalační program pro poskytovatele Microsoft Azure Site Recovery.
7. Stáhněte registrační klíč trezoru. Tento klíč budete potřebovat k instalaci poskytovatele. Klíč je platný pět dní od jeho vygenerování.

    ![Snímek obrazovky s možnostmi stažení poskytovatele a registračního klíče](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Instalace zprostředkovatele

Nainstalujte stažený instalační soubor (AzureSiteRecoveryProvider.exe) na každého hostitele Hyper-V, kterého chcete přidat do lokality Hyper-V. Instalační program nainstaluje poskytovatele Azure Site Recovery a agenta Recovery Services na každého hostitele Hyper-V.

1. Spusťte instalační soubor.
2. V průvodci instalací zprostředkovatele Azure Site Recovery v části **Microsoft Update** vyjádřete výslovný souhlas s použitím Microsoft Update ke kontrole aktualizací zprostředkovatele.
3. V části **instalace** přijměte výchozí umístění instalace pro poskytovatele a agenta a vyberte **nainstalovat**.
4. Po instalaci v Průvodci registrací Site Recovery Microsoft Azure > **Nastavení trezoru** vyberte **Procházet** a v **souboru klíče** vyberte soubor klíče trezoru, který jste stáhli.
5. Zadejte předplatné Azure Site Recovery, název trezoru (**ContosoVMVault**), a název lokality Hyper-V (**ContosoHyperVSite**), do které patří server Hyper-V.
6. V části **Nastavení proxy** vyberte **Připojit přímo k Azure Site Recovery bez proxy**.
7. Po **registraci** serveru v trezoru klikněte na **Dokončit**.

Metadata ze serveru Hyper-v se načítají pomocí Azure Site Recovery a server se zobrazí v části **Site Recovery**  >  **hostitelé Hyper-v** infrastruktury. Tento proces může trvat až 30 minut.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Instalace zprostředkovatele na server s jádrem Hyper-V

Pokud používáte server základní technologie Hyper-V, Stáhněte instalační soubor a proveďte následující kroky:

1. Extrahujte soubory z AzureSiteRecoveryProvider.exe do místního adresáře spuštěním tohoto příkazu:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Spusťte `.\setupdr.exe /i`. Výsledky jsou protokolovány do%Programdata%\ASRLogs\DRASetupWizard.log.

3. Zaregistrujte server spuštěním tohoto příkazu:

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Vyberte a ověřte cílové prostředky:

1. Vyberte **připravit**  >  **cíl** infrastruktury.
2. Vyberte předplatné a skupinu prostředků **ContosoRG** , ve které se po převzetí služeb při selhání vytvoří virtuální počítače Azure.
3. Vyberte model nasazení **Resource Manager**.

Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.

## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

1. Vyberte **připravit**  >  **nastavení replikace** infrastruktury  >  **+ vytvořit a přidružit**.
2. V nastavení **vytvořit a přidružit zásady** zadejte název zásady. Používáme **ContosoReplicationPolicy**.
3. Pro tento kurz ponecháme výchozí nastavení:
    - **Frekvence kopírování** indikuje, jak často se rozdílová data (po počáteční replikaci) replikují. Výchozí četnost je každých pět minut.
    - **Uchovávání bodů obnovení** označuje, že body obnovení budou uchovány po dobu dvou hodin. Maximální povolená hodnota pro uchování při ochraně virtuálních počítačů hostovaných na hostitelích Hyper-V je 24 hodin.
    - **Frekvence snímků konzistentních vzhledem k aplikacím** označuje, že se body obnovení obsahující snímky konzistentní vzhledem k aplikacím budou vytvářet každou hodinu.
    - **Čas zahájení počáteční replikace** indikuje, že se počáteční replikace spustí hned.
4. Po vytvoření zásady vyberte **OK**. Když vytvoříte novou zásadu, automaticky se přidruží k zadané lokalitě Hyper-V. V našem kurzu to je **ContosoHyperVSite**.

    ![Zásady replikace](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Povolení replikace

1. V **aplikaci replikovat aplikaci** vyberte **zdroj**.
2. V části **Zdroj** vyberte lokalitu **ContosoHyperVSite**. Pak vyberte **OK**.
3. V části **cíl** ověřte cíl (Azure), předplatné trezoru a model nasazení **Správce prostředků** .
4. Pokud používáte nastavení kurzu, vyberte pro replikovaná data účet úložiště **contosovmsacct1910171607** , který jste vytvořili v předchozím kurzu. Vyberte taky síť **ContosoASRnet** , ve které se po převzetí služeb při selhání budou virtuální počítače Azure nacházet.
5. V části **virtuální počítače**  >  **Vyberte** virtuální počítač, který chcete replikovat. Pak vyberte **OK**.

   Průběh akce **Povolení ochrany** můžete sledovat v části **Úlohy** > **Úlohy Site Recovery**. Po dokončení úlohy **dokončení ochrany** se počáteční replikace dokončí a virtuální počítač je připravený na převzetí služeb při selhání.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Provedení postupu zotavení po havárii](tutorial-dr-drill-azure.md)
