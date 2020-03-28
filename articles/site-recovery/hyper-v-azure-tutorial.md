---
title: Nastavení zotavení po havárii technologie Hyper-V pomocí azure site recovery
description: Zjistěte, jak nastavit zotavení po havárii místních virtuálních počítačích Hyper-V (bez VMM) do Azure pomocí site recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 741d4718b5e6140f4ddd2bb22e1a2ec830763176
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239844"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Nastavení zotavení po havárii místních virtuálních počítačů Hyper-V do Azure

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá k vaší strategii zotavení po havárii tím, že spravuje a organizuje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení místních počítačů a virtuálních počítačů Azure.

Toto je třetí kurz ze specializované série. Ukazuje, jak nastavit zotavení po havárii místních virtuálních počítačích Hyper-V do Azure. Tento kurz se vztahuje na virtuální počítače Hyper-V, které nejsou spravované správcem virtuálních strojů Microsoft System Center (VMM).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vybrat zdroj a cíl replikace.
> * Nastavte prostředí zdrojové replikace, včetně místních součástí site recovery a cílového prostředí replikace.
> * Vytvořit zásadu replikace.
> * Povolit replikaci virtuálního počítače.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu nasazení pro scénář. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny naleznete v článcích v části **Návody k průvodcům** v [dokumentaci k obnovení webu](https://docs.microsoft.com/azure/site-recovery).



## <a name="before-you-begin"></a>Než začnete

Toto je třetí kurz ze specializované série. Předpokládá, že jste již dokončili úkoly v předchozích kurzech:

1. [Příprava Azure](tutorial-prepare-azure.md)
2. [Příprava místního Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Výběr cíle replikace

1. Na webu Azure Portal přejděte do **trezorů služby Recovery Services** a vyberte trezor. Trezor **ContosoVMVault** jsme připravili v předchozím kurzu.
2. V **možnosti Začínáme**vyberte **Obnovení webu**a pak vyberte Připravit **infrastrukturu**.
3. V **cíli** > Ochrany Kde se vaše **On-premises****počítače nacházejí?**
4. V **yberte Kde chcete replikovat počítače?** **To Azure**
5. V **Yes, with Hyper-V** **nejste na počítačích virtualizováni?**
6. V **v: Používáte system center VMM ke správě hostitelů Technologie Hyper-V?** **No**
7. Vyberte **OK**.

    ![Cíl replikace](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

1. Pokud **Deployment planning**plánujete velké nasazení , stáhněte plánovač nasazení pro technologie Hyper-V z odkazu na stránce. [Přečtěte si další informace](hyper-v-deployment-planner-overview.md) o plánování nasazení technologie Hyper-V.
2. Pro účely tohoto kurzu nepotřebujeme Plánovač nasazení. V **vyberte možnost Dokončení plánování nasazení?** **I will do it later** **OK**

    ![Plánování nasazení](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Chcete-li nastavit zdrojové prostředí, vytvořte web Technologie Hyper-V a přidejte na tuto lokalitu hostitele Technologie Hyper-V obsahující virtuální servery, které chcete replikovat. Potom stáhnete a nainstalujete zprostředkovatele obnovení webu Azure a agenta služby Azure Recovery Services na každého hostitele a zaregistrujete web Hyper-V v trezoru.

1. V části **Připravit infrastrukturu**vyberte **Zdroj**.
2. V **okně Připravit zdroj**vyberte **položku + web Technologie Hyper-V**.
3. V **části Vytvořit web Hyper-V**zadejte název webu. Používáme **ContosoHyperVSite**.

    ![Lokalita Hyper-V](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. Po vytvoření webu vyberte v **části Připravit zdroj** > **krok 1: Vyberte web Technologie Hyper-V**web, který jste vytvořili.
5. Vyberte **+ Server Hyper-V**.

    ![Server Hyper-V](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Stáhněte si instalační program pro zprostředkovatele obnovení webu Microsoft Azure.
7. Stáhněte registrační klíč trezoru. Tento klíč potřebujete k instalaci zprostředkovatele. Klíč je platný pět dní od jeho vygenerování.

    ![Stáhnout zprostředkovatele a registrační klíč](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Instalace zprostředkovatele

Nainstalujte stažený instalační soubor (AzureSiteRecoveryProvider.exe) na každého hostitele Hyper-V, který chcete přidat do webu Hyper-V. Instalační program nainstaluje zprostředkovatele obnovení webu Azure a agenta služby recovery services na každého hostitele Hyper-V.

1. Spusťte instalační soubor.
2. V průvodci instalací zprostředkovatele Azure Site Recovery v části **Microsoft Update** vyjádřete výslovný souhlas s použitím Microsoft Update ke kontrole aktualizací zprostředkovatele.
3. V **části Instalace**přijměte výchozí umístění instalace zprostředkovatele a agenta a vyberte **instalovat**.
4. Po instalaci vyberte v Průvodci registrací obnovení webu Microsoft Azure > **nastavení úložiště** **vyberte Procházet**a v **key file**vyberte stažený soubor klíče trezoru.
5. Zadejte předplatné Azure Site Recovery, název trezoru (**ContosoVMVault**), a název lokality Hyper-V (**ContosoHyperVSite**), do které patří server Hyper-V.
6. V části **Nastavení proxy** vyberte **Připojit přímo k Azure Site Recovery bez proxy**.
7. Po **registraci**serveru v úschovně vyberte možnost **Dokončit**.

Metadata ze serveru Hyper-V se načítají pomocí azure site recovery a server se zobrazí v **site recovery infrastructure** > **hyper-v hosts**. Tento proces může trvat až 30 minut.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Instalace zprostředkovatele na základní server Hyper-V

Pokud používáte základní server Hyper-V, stáhněte si instalační soubor a postupujte takto:

1. Extrahujte soubory z azuresiterecoveryprovider.exe do místního adresáře spuštěním tohoto příkazu:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Spusťte `.\setupdr.exe /i`. Výsledky jsou zaznamenány do souboru %Programdata%\ASRLogs\DRASetupWizard.log.

3. Zaregistrujte server spuštěním tohoto příkazu:

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

Výběr a ověření cílových zdrojů:

1. Vyberte možnost Připravit > **cíl** **infrastruktury**.
2. Vyberte předplatné a skupinu prostředků **ContosoRG,** ve kterém se virtuální počítače Azure vytvoří po převzetí služeb při selhání.
3. Vyberte model nasazení **Resource Manager**.

Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.

## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

1. Vyberte Možnost Připravit > nastavení**replikace** **infrastruktury** > **+Vytvořit a přidružit**.
2. V **poli Vytvořit a přidružit zásady**zadejte název zásady. Používáme **Zásady contosoreplicationpolicy**.
3. V tomto kurzu ponecháme výchozí nastavení:
    - **Frekvence kopírování** označuje, jak často se budou rozdílová data (po počáteční replikaci) replikovat. Výchozí frekvence je každých pět minut.
    - **Zachování bodu obnovení** označuje, že body obnovení budou zachovány po dobu dvou hodin.
    - **Frekvence snímků konzistentních vzhledem k aplikacím** označuje, že se body obnovení obsahující snímky konzistentní vzhledem k aplikacím budou vytvářet každou hodinu.
    - **Počáteční čas zahájení replikace** označuje, že počáteční replikace bude zahájena okamžitě.
4. Po vytvoření zásady vyberte **OK**. Když vytvoříte novou zásadu, bude automaticky přidružena k určenému webu Hyper-V. V našem tutoriálu, to je **ContosoHyperVSite**.

    ![Zásady replikace](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Povolení replikace

1. V **aplikaci Replikovat**vyberte **položku Zdroj**.
2. V části **Zdroj** vyberte lokalitu **ContosoHyperVSite**. Potom vyberte **OK**.
3. V **targetu**ověřte cíl (Azure), předplatné trezoru a model nasazení **Správce prostředků.**
4. Pokud používáte nastavení kurzu, vyberte účet úložiště **contosovmsacct1910171607** vytvořený v předchozím kurzu pro replikovaná data. Vyberte také síť **ContosoASRnet,** ve které budou virtuální počítače Azure umístěny po převzetí služeb při selhání.
5. Ve **virtuálních počítačích** > **Vyberte**vyberte virtuální počítač, který chcete replikovat. Potom vyberte **OK**.

   Průběh akce **Povolení ochrany** můžete sledovat v části **Úlohy** > **Úlohy Site Recovery**. Po dokončení dokončení úlohy **Finalize Protection** je počáteční replikace dokončena a virtuální ho svícení je připraveno k převzetí služeb při selhání.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md)
