---
title: Příprava Azure na zotavení po havárii místních počítačů pomocí Azure Site Recovery | Microsoft Docs
description: Zjistěte, jak připravit Azure na zotavení po havárii místních počítačů pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 3d2b22fc507b209a96870daa8bf12ea9ab60a466
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617409"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Příprava prostředků Azure na zotavení po havárii místních počítačů

Tento článek popisuje postup přípravy prostředků Azure a součásti tak, že můžete nastavit zotavení po havárii místních virtuálních počítačů VMware, virtuálních počítačů Hyper-V nebo Windows/Linux fyzických serverů do Azure, pomocí [Azure Site Recovery](site-recovery-overview.md) služby.

Tento článek obsahuje první kurz řady, která ukazuje, jak nastavit zotavení po havárii pro místní virtuální počítače. 


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověřte, zda má účet Azure replikace oprávnění.
> * Vytvořte trezor služby Recovery Services. Trezor obsahuje metadata a informace o konfiguraci virtuálních počítačů a další komponenty replikace.
> * Nastavení virtuální sítě Azure (VNet). Když jsou virtuální počítače Azure vytvořené po převzetí služeb při selhání, připojí se k této síti.

> [!NOTE]
> Kurzy vám ukážou, nejjednodušší způsob nasazení pro scénář. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny přečtěte si článek v části How To Site Recovery obsahu.

## <a name="before-you-start"></a>Než začnete

- Kontrola architektury pro [VMware](vmware-azure-architecture.md), [Hyper-V](hyper-v-azure-architecture.md), a [fyzický server](physical-azure-architecture.md) zotavení po havárii.
- Přečtěte si nejčastější dotazy ke [VMware](vmware-azure-common-questions.md) a Hyper-V(hyper-v-azure-common-questions.md)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete. Potom se přihlaste k [webu Azure portal](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Ověření oprávnění k účtu

Pokud jste právě vytvořili bezplatný účet Azure, jste správcem předplatného a že máte oprávnění, které potřebujete. Jestliže správcem předplatného nejste, požádejte správce o přiřazení potřebných oprávnění. Pokud chcete povolit replikaci pro nový virtuální počítač, musíte mít následující oprávnění:

- Vytvoření virtuálního počítače ve vybrané skupině prostředků
- Vytvoření virtuálního počítače ve vybrané virtuální síti
- Zápis do účtu služby Azure storage.
- Zapsat do služby Azure spravovaného disku.

K provedení těchto úloh by váš účet měl mít přiřazenou předdefinovanou roli Přispěvatel virtuálních počítačů. Pokud chcete spravovat operace Site Recovery v trezoru, měl by váš účet mít navíc přiřazenou předdefinovanou roli Přispěvatel Site Recovery.


## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

1. Na webu Azure Portal, klikněte na tlačítko **+ vytvořit prostředek**a na Marketplace vyhledejte **obnovení**.
2. Klikněte na tlačítko **Backup a Site Recovery (OMS)** a na stránce Backup a Site Recovery na **vytvořit**. 
1. V **trezor služby Recovery Services** > **název**, zadejte popisný název pro identifikaci trezoru. Pro tuto sadu kurzů používáme název **ContosoVMVault**.
2. V **skupiny prostředků**, vyberte existující skupinu prostředků nebo vytvořte novou. Pro účely tohoto kurzu používáme **contosoRG**.
3. V **umístění**, vyberte oblast, ve kterém by měl být umístěn v trezoru. používáme oblast **Západní Evropa**.
4. Pokud chcete mít k trezoru rychlý přístup z řídicího panelu, vyberte **Připnout na řídicí panel** > **Vytvořit**.

   ![Vytvoření nového trezoru](./media/tutorial-prepare-azure/new-vault-settings.png)

   Nový trezor se zobrazí v části **Řídicí panel** > **Všechny prostředky** a na hlavní stránce **Trezory služby Recovery Services**.

## <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

Počítače se replikují do Azure v místním spravované disky. Pokud dojde k převzetí služeb při selhání, virtuální počítače Azure vytvořené z těchto spravovaných disků a připojený k síti Azure, kterou zadáte v tomto postupu.

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Sítě** > **Virtuální síť**.
2. Zachovat **Resource Manageru** vybrali jako model nasazení.
3. V části **Název** zadejte název sítě. Název musí být v rámci skupiny prostředků Azure jedinečný. V tomto kurzu používáme **ContosoASRnet**.
4. Zadejte skupinu prostředků, ve které se vytvoří síť. V tomto kurzu používáme existující skupinu prostředků **contosoRG**.
5. V **rozsah adres**, zadejte pro rozsah adres v síti. Používáme **10.1.0.0/24**a ne pomocí podsítě.
6. V části **Předplatné** vyberte předplatné, ve kterém chcete síť vytvořit.
7. V **umístění**, vyberte stejnou oblast, ve kterém byla vytvořena trezoru služby Recovery Services. V našem kurzu má **západní Evropa**. Síť musí být ve stejné oblasti jako trezor.
8. Ponecháme výchozí možnosti základní ochrany před útoky DDoS bez koncového bodu služby v síti.
9. Klikněte na možnost **Vytvořit**.

   ![Vytvoření virtuální sítě](media/tutorial-prepare-azure/create-network.png)

Vytvoření virtuální sítě trvá několik sekund. Po vytvoření se zobrazí na řídicím panelu webu Azure Portal.




## <a name="next-steps"></a>Další postup

- Pro zotavení po havárii VMware [Příprava na místní infrastrukturu VMware](tutorial-prepare-on-premises-vmware.md).
- Pro zotavení po havárii Hyper-V [připravit místní servery Hyper-V](hyper-v-prepare-on-premises-tutorial.md).
- Pro zotavení po havárii fyzických serverů [nastavit konfigurační server a zdrojové prostředí](physical-azure-disaster-recovery.md)
- Informace o [sítích Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Další informace o](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) spravované disky.
