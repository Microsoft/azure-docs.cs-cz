---
title: Příprava Azure na místní zotavení po havárii pomocí Azure Site Recovery
description: Zjistěte, jak připravit Azure na zotavení po havárii místních počítačů pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ba5ecc29edbcd69324500e87add846e4395ce0a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067569"
---
# <a name="prepare-azure-for-on-premises-disaster-recovery-to-azure"></a>Příprava Azure na místní zotavení po havárii do Azure

Tento článek popisuje, jak připravit prostředky a součásti Azure tak, aby můžete nastavit zotavení po havárii místních virtuálních počítačích VMware, virtuálních počítačích Hyper-V nebo fyzických serverů Windows/Linux do Azure pomocí služby [Azure Site Recovery.](site-recovery-overview.md)

Tento článek obsahuje první kurz řady, která ukazuje, jak nastavit zotavení po havárii pro místní virtuální počítače. 


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověřte, zda má účet Azure oprávnění k replikaci.
> * Vytvořte trezor služby Recovery Services. Trezor obsahuje metadata a informace o konfiguraci virtuálních počítačů a další komponenty replikace.
> * Nastavte virtuální síť Azure (VNet). Když se virtuální počítače Azure vytvoří po převzetí služeb při selhání, připojí se k této síti.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu nasazení pro scénář. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny naleznete v článku v části Jak na webu recovery obsah.

## <a name="before-you-start"></a>Než začnete

- Zkontrolujte architekturu pro [vmware](vmware-azure-architecture.md), [Hyper-V](hyper-v-azure-architecture.md)a [fyzické server](physical-azure-architecture.md) zotavení po havárii.
- Přečtěte si běžné otázky pro [vmware](vmware-azure-common-questions.md) a [hyper-v](hyper-v-azure-common-questions.md)

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/pricing/free-trial/) než začnete. Pak se přihlaste k [portálu Azure](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Ověření oprávnění k účtu

Pokud jste si právě vytvořili bezplatný účet Azure, jste správcem předplatného a máte oprávnění, která potřebujete. Jestliže správcem předplatného nejste, požádejte správce o přiřazení potřebných oprávnění. Pokud chcete povolit replikaci pro nový virtuální počítač, musíte mít následující oprávnění:

- Vytvoření virtuálního počítače ve vybrané skupině prostředků
- Vytvoření virtuálního počítače ve vybrané virtuální síti
- Zapište do účtu úložiště Azure.
- Zapisovat na spravovaný disk Azure.

K provedení těchto úloh by váš účet měl mít přiřazenou předdefinovanou roli Přispěvatel virtuálních počítačů. Kromě toho ke správě operací obnovení webu v trezoru by měl být vašemu účtu přiřazena předdefinovaná role Přispěvatel obnovení webu.


## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

1. V nabídce Portál Azure vyberte **Vytvořit prostředek**a vyhledejte na marketplace pro **obnovení**.
2. Ve výsledcích hledání vyberte **Zálohovat a Obnovení webu** a na stránce Zálohování a Obnovení webu klepněte na **tlačítko Vytvořit**. 
3. Na stránce **Vytvořit úložiště služby Obnovení** vyberte možnost **Předplatný .** Používáme **předplatné Contoso**.
4. Ve **skupině Prostředků**vyberte existující skupinu prostředků nebo vytvořte novou. Pro tento kurz používáme **contosoRG**.
5. Do **pole Název úložiště**zadejte popisný název pro identifikaci trezoru. Pro tuto sadu kurzů používáme název **ContosoVMVault**.
6. V **oblasti**vyberte oblast, ve které má být úschovna umístěna. používáme oblast **Západní Evropa**.
7. Vyberte **Zkontrolovat a vytvořit**.

   ![Vytvoření nového trezoru](./media/tutorial-prepare-azure/new-vault-settings.png)

   Nový trezor bude nyní uveden na **řídicím panelu** > **Všechny prostředky**a na hlavní stránce **trezorů služby Recovery Services.**

## <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

Místní počítače se replikují na spravované disky Azure. Dojde-li k převzetí služeb při selhání, virtuální počítače Azure se vytvoří z těchto spravovaných disků a připojí se k síti Azure, kterou zadáte v tomto postupu.

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Sítě** > **Virtuální síť**.
2. Ponechte **správce prostředků** vybraný jako model nasazení.
3. V části **Název** zadejte název sítě. Název musí být v rámci skupiny prostředků Azure jedinečný. V tomto kurzu používáme **ContosoASRnet**.
4. V **adresním prostoru**zadejte rozsah adres virtuální sítě do zápisu CDR. Používáme **10.1.0.0/24**.
5. V části **Předplatné** vyberte předplatné, ve kterém chcete síť vytvořit.
6. Zadejte **skupinu prostředků,** ve které bude síť vytvořena. V tomto kurzu používáme existující skupinu prostředků **contosoRG**.
7. V **umístění**vyberte stejnou oblast, ve které byl vytvořen trezor služby Recovery Services. V našem tutoriálu je to **západní Evropa**. Síť musí být ve stejné oblasti jako trezor.
8. V **rozsahu Adresa**zadejte rozsah sítě. Používáme **10.1.0.0/24**a nepoužíváme podsíť.
9. Ponecháváme výchozí možnosti základní ochrany DDoS bez koncového bodu služby nebo brány firewall v síti.
9. Vyberte **Vytvořit**.

   ![Vytvoření virtuální sítě](media/tutorial-prepare-azure/create-network.png)

Vytvoření virtuální sítě trvá několik sekund. Po vytvoření se zobrazí na řídicím panelu portálu Azure.




## <a name="next-steps"></a>Další kroky

- Pro zotavení po havárii společnosti VMware [připravte místní infrastrukturu VMware](tutorial-prepare-on-premises-vmware.md).
- Pro zotavení po havárii technologie Hyper-V [připravte místní servery Hyper-V](hyper-v-prepare-on-premises-tutorial.md).
- Pro fyzické zotavení po havárii serveru [nastavte konfigurační server a zdrojové prostředí.](physical-azure-disaster-recovery.md)
- Informace o [sítích Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Informace o](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) spravovaných discích.
