---
title: Nastavení zotavení po havárii místních virtuálních počítačů Hyper-V v cloudech VMM do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Zjistěte, jak nastavit zotavení po havárii místních virtuálních počítačů Hyper-V v cloudech System Center VMM do Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: f57dd806e345cad80b8c60424e12211fc74814d6
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50213215"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Nastavení zotavení po havárii místních virtuálních počítačů Hyper-V v cloudech VMM do Azure

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení pro místní počítače a virtuální počítače Azure.

V tomto kurzu se dozvíte, jak nastavit zotavení po havárii místních virtuálních počítačů Hyper-V do Azure. Tento kurz se týká virtuálních počítačů Hyper-V, které jsou spravovány nástrojem System Center Virtual Machine Manager (VMM). V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vybrat zdroj a cíl replikace.
> * Nastavit zdrojové prostředí replikace, včetně místních komponent řešení Site Recovery, a cílové prostředí replikace.
> * Nastavte mapování sítě, k mapování mezi sítěmi virtuálních počítačů ve VMM a virtuálním sítím Azure.
> * Vytvoření zásady replikace
> * Povolit replikaci virtuálního počítače

Toto je třetí kurz ze specializované série. Tento kurz předpokládá, že jste už dokončili úlohy z předchozích kurzů:

1. [Příprava Azure](tutorial-prepare-azure.md)
2. [Příprava místního Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

Než začnete, doporučujeme [zkontrolovat architekturu](concepts-hyper-v-to-azure-architecture.md) pro tento scénář zotavení po havárii.



## <a name="select-a-replication-goal"></a>Výběr cíle replikace

1. V **všechny služby** > **trezory služby Recovery Services**, klikněte na název trezoru v těchto kurzech používáme **ContosoVMVault**.
2. V části **Začínáme** klikněte na **Site Recovery**. Pak klikněte na **Příprava infrastruktury**.
3. V části **Cíl ochrany** > **Kde jsou vaše počítače umístěné?** vyberte **Místní**.
4. V části **Kam chcete počítače replikovat?** vyberte **Do Azure**.
5. V **jsou vaše počítače virtualizované**vyberte **Ano, s technologií Hyper-V**.
6. V **používáte System Center VMM**vyberte **Ano**. Pak klikněte na **OK**.

    ![Cíl replikace](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)



## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Při nastavování zdrojové prostředí, nainstalujte zprostředkovatele Azure Site Recovery a agenta služeb zotavení Azure a zaregistrujte na místních serverech v trezoru. 

1. V části **Příprava infrastruktury** klikněte na **Zdroj**.
2. V okně **Připravit zdroj** klikněte na **+ VMM** a přidejte server VMM. V **přidat Server**, zkontrolujte, že **serveru System Center VMM** se zobrazí v **typ serveru**.
3. Stáhněte si instalační program pro zprostředkovatele Microsoft Azure Site Recovery.
4. Stáhněte registrační klíč trezoru. Budete ho potřebovat po spuštění instalace zprostředkovatele. Klíč je platný pět dní od jeho vygenerování.
5. Stáhněte agenta služby Recovery Services.

    ![Ke stažení](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalace zprostředkovatele na server VMM

1. V průvodci instalací zprostředkovatele Azure Site Recovery v části **Microsoft Update** vyjádřete výslovný souhlas s použitím Microsoft Update ke kontrole aktualizací zprostředkovatele.
2. V **instalace**, přijměte výchozí umístění instalace zprostředkovatele a klikněte na tlačítko **nainstalovat**. 
3. Po instalaci v Průvodci registrací Microsoft Azure Site Recovery > **nastavení trezoru**, klikněte na tlačítko **Procházet**a v **soubor klíče**, vyberte soubor klíče trezoru, který jste Stáhnout.
4. Zadejte předplatné Azure Site Recovery a název trezoru (**ContosoVMVault**). Zadejte popisný název serveru VMM, který ji identifikuje v trezoru.
5. V části **Nastavení proxy** vyberte **Připojit přímo k Azure Site Recovery bez proxy**.
6. Přijměte výchozí umístění pro certifikát, který se používá k šifrování dat. Šifrovaná data bude možné dešifrovat, při převzetí služeb při selhání.
7. V **synchronizovat metadata cloudu**vyberte **synchronizace cloudu metadata k portálu Site Recovery**. Tuto akci stačí na každém serveru provést pouze jednou. Pak klikněte na tlačítko **zaregistrovat**.
8. Jakmile je server zaregistrovaný v trezoru, klikněte na tlačítko **Dokončit**.

Po dokončení registrace Azure Site Recovery načte metadata ze serveru a VMM server se zobrazí v **infrastruktura Site Recovery**.

### <a name="install-the-recovery-services-agent"></a>Instalace agenta služby Recovery Services

Nainstalujte agenta na každého hostitele Hyper-V obsahující virtuální počítače, které chcete replikovat.

1. V Microsoft Azure Recovery Services Průvodce instalací agenta nástroje > **Kontrola předpokladů**, klikněte na tlačítko **Další**. Veškeré chybějící požadované položky budou automaticky nainstalovány.
2. V **nastavení instalace**, přijměte umístění instalace a umístění mezipaměti. Jednotka mezipaměti vyžaduje minimálně 5 GB úložiště. Doporučujeme jednotku s 600 GB nebo více volného místa. Pak klikněte na **Nainstalovat**.
3. V **instalace**, po dokončení instalace, klikněte na tlačítko **Zavřít** dokončete průvodce.

    ![Instalace agenta](./media/hyper-v-vmm-azure-tutorial/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

1. Klikněte na **Příprava infrastruktury** > **Cíl**.
2. Vyberte předplatné a skupinu prostředků (**ContosoRG**) ve virtuálních počítačích Azure vytvoří po převzetí služeb při selhání.
3. Vyberte model nasazení **Resource Manager**.

Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.


## <a name="configure-network-mapping"></a>Konfigurace mapování sítě

1. V části **Infrastruktura Site Recovery** > **Mapování sítí** > **Mapování sítě** klikněte na ikonu **+Mapování sítě**.
2. V **přidání mapování sítě**, vyberte zdrojový server VMM. Vyberte **Azure** jako cíl.
3. Ověřte předplatné a model nasazení po převzetí služeb při selhání.
4. V **zdrojovou síť**, vyberte zdrojovou místní síť virtuálních počítačů.
5. V **Cílová síť**, vyberte síť Azure, ve které virtuální počítače Azure repliky budou umístěné při jejich vytvoření po převzetí služeb při selhání. Pak klikněte na **OK**.

    ![Mapování sítě](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

1. Klikněte na **Příprava infrastruktury** > **Nastavení replikace** > **+ Vytvořit a přidružit**.
2. V části **Vytvořit a přidružit zásady** zadejte název zásady **ContosoReplicationPolicy**.
3. Ponechte výchozí nastavení a klikněte na **OK**.
    - **Frekvence kopírování** označuje, že se rozdílová data (po počáteční replikaci) budou replikovat každých pět minut.
    - **Uchovávání bodů obnovení** označuje, že interval uchovávání každého bodu obnovení bude dvě hodiny.
    - **Frekvence snímků konzistentních vzhledem k aplikacím** označuje, že se body obnovení obsahující snímky konzistentní vzhledem k aplikacím budou vytvářet každou hodinu.
    - **Čas spuštění počáteční replikace** označuje, že se počáteční replikace spustí okamžitě.
    - **Šifrovat data ve službě Azure** – výchozí **vypnout** nastavení znamená, že neaktivní uložená data v Azure nejsou šifrována.
4. Po vytvoření zásady klikněte na **OK**. Když vytvoříte novou zásadu, automaticky se přidruží ke cloudu VMM.

## <a name="enable-replication"></a>Povolení replikace

1. V části **Replikovat aplikaci** klikněte na **Zdroj**. 
2. V **zdroj**, vyberte VMM cloud. Pak klikněte na **OK**.
3. V **cílové**ověřte Azure jako cíl, předplatné trezoru a vyberte **Resource Manageru** modelu.
4. Vyberte **contosovmsacct1910171607** účtu úložiště a **ContosoASRnet** sítě Azure.
5. V části **Virtuální počítače** > **Vybrat** vyberte virtuální počítač, který chcete replikovat. Pak klikněte na **OK**.

 Průběh akce **Povolení ochrany** můžete sledovat v části **Úlohy** > **Úlohy Site Recovery**. Po **dokončit ochranu** příslušná úloha skončí, dokončení počáteční replikace a virtuální počítač připravený k převzetí služeb při selhání.


## <a name="next-steps"></a>Další postup
[Spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md)
