---
title: Nastavit zotavení po havárii místní virtuální počítače Hyper-V v cloudech VMM do Azure s Azure Site Recovery | Microsoft Docs
description: Zjistěte, jak nastavit zotavení po havárii místní virtuální počítače Hyper-V v cloudech System Center VMM do Azure, se službou Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: a8bbbe0a5aca20222ff7385be9d0ecf0a4224d5c
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Nastavit zotavení po havárii místní virtuální počítače Hyper-V v cloudech VMM do Azure

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení pro místní počítače a virtuální počítače Azure.

V tomto kurzu se dozvíte, jak nastavit zotavení po havárii místních virtuálních počítačů Hyper-V do Azure. Tento kurz je relevantní pro virtuální počítače Hyper-V, která jsou spravována nástrojem System Center Virtual Machine Manager (VMM). V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vybrat zdroj a cíl replikace.
> * Nastavit zdrojové prostředí replikace, včetně místních komponent řešení Site Recovery, a cílové prostředí replikace.
> * Nastavte mapování sítě pro mapování mezi sítěmi virtuálních počítačů nástroje VMM a virtuálních sítí Azure.
> * Vytvoření zásady replikace
> * Povolit replikaci virtuálního počítače

Toto je třetí kurz ze specializované série. Tento kurz předpokládá, že jste už dokončili úlohy z předchozích kurzů:

1. [Příprava Azure](tutorial-prepare-azure.md)
2. [Příprava místního Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

Než začnete, doporučujeme [zkontrolovat architekturu](concepts-hyper-v-to-azure-architecture.md) pro tento scénář zotavení po havárii.



## <a name="select-a-replication-goal"></a>Výběr cíle replikace

1. V **všechny služby** > **trezory služeb zotavení**, klikněte na název trezoru používáme v těchto kurzech **ContosoVMVault**.
2. V části **Začínáme** klikněte na **Site Recovery**. Pak klikněte na **Příprava infrastruktury**.
3. V části **Cíl ochrany** > **Kde jsou vaše počítače umístěné?** vyberte **Místní**.
4. V části **Kam chcete počítače replikovat?** vyberte **Do Azure**.
5. V **jsou vaše počítače virtualizovaných**, vyberte **Ano, s technologií Hyper-V**.
6. V **používáte System Center VMM**, vyberte **Ano**. Pak klikněte na **OK**.

    ![Cíl replikace](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)



## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

Při nastavování zdrojové prostředí, nainstalujte zprostředkovatele Azure Site Recovery a agenta služeb zotavení Azure a zaregistrujte na místní servery v trezoru. 

1. V části **Příprava infrastruktury** klikněte na **Zdroj**.
2. V okně **Připravit zdroj** klikněte na **+ VMM** a přidejte server VMM. V **přidat Server**, zkontrolujte, zda **serveru System Center VMM** se zobrazí v **typ serveru**.
3. Stáhněte si instalační program zprostředkovatele služby Microsoft Azure Site Recovery.
4. Stáhněte registrační klíč trezoru. Budete ho potřebovat po spuštění instalace zprostředkovatele. Klíč je platný pět dní od jeho vygenerování.
5. Stáhněte si agenta služeb zotavení.

    ![Ke stažení](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalace zprostředkovatele na server VMM

1. V průvodci instalací zprostředkovatele Azure Site Recovery v části **Microsoft Update** vyjádřete výslovný souhlas s použitím Microsoft Update ke kontrole aktualizací zprostředkovatele.
2. V **instalace**, přijměte výchozí umístění instalace pro zprostředkovatele a klikněte na **nainstalovat**. 
3. Po instalaci, v Průvodce registrací Microsoft Azure Site Recovery > **nastavení trezoru**, klikněte na tlačítko **Procházet**a v **soubor klíče**, vyberte klíč trezoru soubor, který Stáhnout.
4. Zadejte předplatné Azure Site Recovery a název trezoru (**ContosoVMVault**). Zadejte popisný název serveru VMM, který ji identifikuje v trezoru.
5. V části **Nastavení proxy** vyberte **Připojit přímo k Azure Site Recovery bez proxy**.
6. Přijměte výchozí umístění pro certifikát, který se používá k šifrování dat. Šifrovaná data se dešifrovat, pokud jste převzetí služeb při selhání.
7. V **synchronizovat metadata cloudu**, vyberte **synchronizovat metadata cloudu údajů na portál Site Recovery**. Tuto akci stačí na každém serveru provést pouze jednou. Pak klikněte na tlačítko **zaregistrovat**.
8. Po server je zaregistrovaný v trezoru, klikněte na tlačítko **Dokončit**.

Po dokončení registrace pomocí Azure Site Recovery je načíst metadata ze serveru a serveru VMM se zobrazí v **infrastruktura Site Recovery**.

### <a name="install-the-recovery-services-agent"></a>Nainstalujte agenta služeb zotavení

Nainstalujte agenta na každém hostiteli technologie Hyper-V obsahující virtuální počítače, které chcete replikovat.

1. V Průvodci instalací služby agenta Microsoft Azure obnovení > **Kontrola předpokladů**, klikněte na tlačítko **Další**. Veškeré chybějící požadované položky se automaticky nainstalují.
2. V **nastavení instalace**, přijměte umístění instalace a umístění mezipaměti. Jednotka mezipaměti vyžaduje minimálně 5 GB úložiště. Doporučujeme, abyste na jednotku s 600 GB nebo více volného místa. Pak klikněte na **Nainstalovat**.
3. V **instalace**, po dokončení instalace, klikněte na **Zavřít** ukončíte průvodce.

    ![Instalace agenta](./media/hyper-v-vmm-azure-tutorial/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>Nastavení cílového prostředí

1. Klikněte na **Příprava infrastruktury** > **Cíl**.
2. Vyberte předplatné a skupina prostředků (**ContosoRG**) v který virtuálních počítačích Azure bude vytvořen po převzetí služeb při selhání.
3. Vyberte model nasazení **Resource Manager**.

Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.


## <a name="configure-network-mapping"></a>Konfigurace mapování sítě

1. V části **Infrastruktura Site Recovery** > **Mapování sítí** > **Mapování sítě** klikněte na ikonu **+Mapování sítě**.
2. V **přidání mapování sítě**, vyberte zdrojový server VMM. Vyberte **Azure** jako cíl.
3. Ověřte předplatné a model nasazení po převzetí služeb při selhání.
4. V **zdrojové síti**, vyberte zdrojovou síť virtuálních počítačů na místě.
5. V **Cílová síť**, vyberte síť Azure, ve které virtuální počítače Azure repliky budou umístěné, když jste vytvořili po převzetí služeb při selhání. Pak klikněte na **OK**.

    ![Mapování sítě](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Nastavení zásady replikace

1. Klikněte na **Příprava infrastruktury** > **Nastavení replikace** > **+ Vytvořit a přidružit**.
2. V části **Vytvořit a přidružit zásady** zadejte název zásady **ContosoReplicationPolicy**.
3. Ponechte výchozí nastavení a klikněte na **OK**.
    - **Frekvence kopírování** označuje, že se rozdílová data (po počáteční replikaci) budou replikovat každých pět minut.
    - **Uchovávání bodů obnovení** označuje, že interval uchovávání každého bodu obnovení bude dvě hodiny.
    - **Frekvence snímků konzistentních vzhledem k aplikacím** označuje, že se body obnovení obsahující snímky konzistentní vzhledem k aplikacím budou vytvářet každou hodinu.
    - **Čas spuštění počáteční replikace** označuje, že se počáteční replikace spustí okamžitě.
    - **Šifrovat data uložená v Azure** -výchozí **vypnout** nastavení znamená, že v klidovém stavu uložených dat v Azure není zašifrovaná.
4. Po vytvoření zásady klikněte na **OK**. Když vytvoříte novou zásadu, automaticky se přidruží ke cloudu VMM.

## <a name="enable-replication"></a>Povolení replikace

1. V části **Replikovat aplikaci** klikněte na **Zdroj**. 
2. V **zdroj**, vyberte VMM cloud. Pak klikněte na **OK**.
3. V **cíl**zkontrolujte Azure jako cíl, trezor předplatného a vyberte **Resource Manager** modelu.
4. Vyberte **contosovmsacct1910171607** účet úložiště a **ContosoASRnet** síť Azure.
5. V části **Virtuální počítače** > **Vybrat** vyberte virtuální počítač, který chcete replikovat. Pak klikněte na **OK**.

 Průběh akce **Povolení ochrany** můžete sledovat v části **Úlohy** > **Úlohy Site Recovery**. Po **dokončení ochrany** dokončení úlohy, je dokončena počáteční replikace a je připravený pro převzetí služeb při selhání virtuálního počítače.


## <a name="next-steps"></a>Další postup
[Spuštění postupu zotavení po havárii](tutorial-dr-drill-azure.md)
