---
title: Nastavení zotavení po havárii technologie Hyper-V (s VMM) pomocí azure site recovery
description: Zjistěte, jak nastavit zotavení po havárii místních virtuálních počítačích Hyper-V v cloudech System Center VMM do Azure pomocí site recovery.
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: a391d8eb3cf7bc43b52883cbf2e76170338c44c6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067580"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Nastavení zotavení po havárii místních virtuálních počítačů Hyper-V v cloudech VMM do Azure

Tento kurz popisuje, jak povolit replikaci pro místní virtuální počítače Hyper-V (VM) spravované správcem virtuálních počítačů System Center (VMM) do Azure pomocí [Azure Site Recovery](site-recovery-overview.md). Pokud nepoužíváte VMM, [postupujte podle tohoto kurzu](hyper-v-azure-tutorial.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vybrat zdroj a cíl replikace.
> * Nastavte prostředí zdrojové replikace, včetně místních součástí site recovery a cílového prostředí replikace.
> * Nastavte mapování sítě mezi sítěmi virtuálních vsíti VM v VM a virtuálními sítěmi Azure.
> * Vytvořit zásadu replikace.
> * Povolit replikaci virtuálního počítače.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu nasazení pro scénář. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny naleznete v článcích v části **Návody k průvodcům** v [dokumentaci k obnovení webu](/azure/site-recovery/).

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že jste již dokončili následující kurzy:

1. [Příprava Azure](tutorial-prepare-azure.md)
1. [Příprava místních serverů Hyper-V](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Výběr cíle replikace

1. Na webu Azure Portal přejděte do **trezorů služby Recovery Services** a vyberte trezor **ContosoVMVault,** který byl vytvořen v kurzu [Příprava Azure.](tutorial-prepare-azure.md#create-a-recovery-services-vault)
1. V **části Začínáme**vyberte **Možnost Obnovení** > webu**Připravit infrastrukturu** a nakonfigurujte následující nastavení:
    1. **Cíl** > ochrany Kde se vaše **On-premises****stroje nacházejí?**
    1. **Kde chcete replikovat vaše počítače?**, vyberte **Do Azure**.
    1. **Jsou vaše počítače virtualizovány?**, vyberte **Ano, pomocí technologie Hyper-V**.
    1. **Používáte ke správě hostitelů Technologie Hyper-V nástroj System Center Vslužbi?** **Yes**
1. Vyberte **OK**.

   ![Cíl replikace](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

1. Pokud **Deployment planning**plánujete velké nasazení , stáhněte plánovač nasazení pro technologie Hyper-V z odkazu na stránce. [Přečtěte si další informace](hyper-v-deployment-planner-overview.md) o plánování nasazení technologie Hyper-V.
1. Pro účely tohoto kurzu nepotřebujeme Plánovač nasazení. V **vyberte možnost Dokončení plánování nasazení?** **I will do it later** **OK**

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Při nastavování zdrojového prostředí nainstalujete zprostředkovatele obnovení webu Azure na server VMM a zaregistrujete server v trezoru. Agenta služby Azure Recovery Services nainstalujete na každého hostitele Hyper-V.

1. **Připravte infrastrukturu**. Vyberte **zdroj**.
1. **Připravte zdroj**. Výběrem **možnosti + VMM** přidáte server VMM. V části **Přidat server** zkontrolujte, že se v poli **Typ serveru** zobrazí **Server System Center VMM**.
1. Stáhněte si instalační program pro zprostředkovatele obnovení webu Microsoft Azure.
1. Stáhněte registrační klíč trezoru. Tento klíč potřebujete při spuštění nastavení zprostředkovatele. Klíč je platný pět dní od jeho vygenerování.
1. Stáhněte si instalační program pro agenta služby Microsoft Azure Recovery Services.

   ![Stáhnout zprostředkovatele, registrační klíč a agenta](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalace zprostředkovatele na server VMM

1. V průvodci nastavením zprostředkovatele obnovení webu Azure **Microsoft Update**. Přihlaste se ke kontrole aktualizací zprostředkovatele pomocí služby Microsoft Update.
1. **Instalace**. Přijměte výchozí umístění instalace zprostředkovatele a vyberte **možnost Instalovat**.
1. Po instalaci vyberte v Průvodci registrací obnovení webu Microsoft Azure **nastavení úložiště**, **Procházet**a v **souboru klíče**vyberte stažený soubor klíče trezoru.
1. Zadejte předplatné Azure Site Recovery a název trezoru (**ContosoVMVault**). Zadejte popisný název serveru VMM, který ho identifikuje v úschovně.
1. **Nastavení proxy serveru**. Vyberte **Připojit přímo k Azure Site Recovery bez proxy**.
1. Přijměte výchozí umístění certifikátu, který se používá k šifrování dat. Šifrovaná data budou při převzetí služeb při selhání dešifrována.
1. **Synchronizace metadat cloudu**. Vyberte **Synchronizovat meta data cloudu s portálem Site Recovery**. Tato akce se musí stát pouze jednou na každém serveru. Potom vyberte **Registrovat**.
1. Po zaregistrování serveru v úschovně vyberte **možnost Dokončit**.

Po dokončení registrace se metadata ze serveru načtou pomocí Azure Site Recovery a server VMM se zobrazí v **infrastruktuře site recovery**infrastructure .

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Instalace agenta Recovery Services na hostitele Hyper-V

Nainstalujte agenta na každém hostiteli Hyper-V obsahujícím virtuální počítač, který chcete replikovat.

V Průvodci instalací agenta služby Microsoft Azure Recovery Services nakonfigurujte tato nastavení:

1. **Požadavky Kontrola**. Vyberte **další**. Všechny chybějící požadavky budou nainstalovány automaticky.
1. **Nastavení instalace**. Přijměte umístění instalace a umístění mezipaměti. Jednotka mezipaměti potřebuje alespoň 5 GB úložiště. Doporučujeme jednotku s 600 GB nebo více volného místa. Pak vyberte **Nainstalovat**.
1. **Instalace**. Po dokončení instalace vyberte **Zavřít** a průvodce dokončete.

   ![Nainstalovat agenta](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

1. Vyberte možnost Připravit > **cíl** **infrastruktury**.
1. Vyberte předplatné a skupinu prostředků (**ContosoRG),** ve kterém se virtuální počítače Azure vytvoří po převzetí služeb při selhání.
1. Vyberte model nasazení **Správce prostředků.**

Site Recovery zkontroluje, že existuje jeden nebo více kompatibilních účtů a sítí úložiště Azure.

## <a name="configure-network-mapping"></a>Konfigurace mapování sítě

1. **Mapování** > sítě sítě**infrastruktury obnovy lokality** > **Network Mapping**. Vyberte ikonu **+Mapování sítě.**
1. **Přidat mapování sítě**. Vyberte server **VMM zdrojového systémového centra.** Pro **cíl**vyberte Azure.
1. Ověřte předplatné a model nasazení po převzetí služeb při selhání.
1. **Zdrojová síť**. Vyberte zdrojovou místní síť virtuálních počítačů.
1. **Cílová síť**. Vyberte síť Azure, ve které se budou replikovat virtuální počítače Azure, když se po převzetí služeb při selhání vytvoří. Potom vyberte **OK**.

   ![Mapování sítě](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

1. Vyberte Možnost Připravit > nastavení**replikace** **infrastruktury** > **+Vytvořit a přidružit**.
1. V **poli Vytvořit a přidružit zásady**zadejte název zásady. Používáme **Zásady contosoreplicationpolicy**.
1. Přijměte výchozí nastavení a vyberte **OK**:
   - **Frekvence kopírování** označuje, že po počáteční replikaci se rozdílová data replikují každých pět minut.
   - **Uchování bodů obnovení** označuje, že každý bod obnovení bude zachován po dobu dvou hodin.
   - **Frekvence snímků konzistentních vzhledem k aplikacím** označuje, že se body obnovení obsahující snímky konzistentní vzhledem k aplikacím budou vytvářet každou hodinu.
   - **Počáteční čas zahájení replikace** označuje, že počáteční replikace bude zahájena okamžitě.
   - **Šifrování dat uložených v Azure** je nastaveno na výchozí **(Vypnuto)** a znamená, že data v klidovém stavu v Azure nejsou šifrovaná.
1. Po vytvoření zásady vyberte **OK**. Když vytvoříte novou zásadu, bude automaticky přidružena ke cloudu VMM.

## <a name="enable-replication"></a>Povolení replikace

1. **Replikovat aplikaci**. Vyberte **zdroj**.
1. **Zdroj**. Vyberte cloud VMM. Potom vyberte **OK**.
1. **Cíl**. Ověřte cíl (Azure), předplatné trezoru a vyberte model **Správce prostředků.**
1. Vyberte účet úložiště **contosovmsacct1910171607** a síť **ContosoASRnet** Azure.
1. **Virtuální počítače** > **Vyberte**. Vyberte virtuální ms, který chcete replikovat. Potom vyberte **OK**.

   Průběh akce **Povolení ochrany** můžete sledovat v části **Úlohy** > **Úlohy Site Recovery**. Po dokončení dokončení úlohy **Finalize Protection** je počáteční replikace dokončena a virtuální ho svícení je připraveno k převzetí služeb při selhání.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md)
