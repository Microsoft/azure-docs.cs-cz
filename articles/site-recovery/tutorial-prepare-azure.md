---
title: Příprava Azure na zotavení po havárii místních počítačů pomocí Azure Site Recovery | Microsoft Docs
description: Zjistěte, jak připravit Azure na zotavení po havárii místních počítačů pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 8bdb711d39f514375362235388943ec42451b312
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315568"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Příprava prostředků Azure na zotavení po havárii místních počítačů

 [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii provozní kontinuity a zotavení po havárii (BCDR) tím, že zajišťuje provoz a dostupnost obchodních aplikací během plánovaných i neplánovaných výpadků. Site Recovery spravuje a orchestruje zotavení po havárii místních počítačů a virtuálních počítačů Azure, včetně replikace, převzetí služeb při selhání a zotavení.

Tento článek obsahuje první kurz řady, která ukazuje, jak nastavit zotavení po havárii pro místní virtuální počítače. Prostudujte si ho, pokud potřebujete chránit místní virtuální počítače VMware, virtuální počítače Hyper-V nebo fyzické servery.

> [!NOTE]
> Tyto kurzy demonstrují ten nejjednodušší způsob nasazení určitého scénáře. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny najdete v části **Postupy** pro odpovídající scénář.

V tomto článku se dozvíte, jak připravit komponenty Azure v případě, že chcete replikovat místní virtuální počítače (Hyper-V nebo VMware) nebo fyzické servery s Windows nebo Linuxem do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověření oprávnění účtu Azure k replikaci
> * Vytvořte trezor služby Recovery Services. Trezor obsahuje metadata a informace o konfiguraci virtuálních počítačů a další komponenty replikace.
> * Nastavit síť Azure. Když se po převzetí služeb při selhání vytvoří virtuální počítače Azure, připojí se do této sítě Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Ověření oprávnění k účtu

Pokud jste si právě vytvořili bezplatný účet Azure, jste správcem předplatného. Jestliže správcem předplatného nejste, požádejte správce o přiřazení potřebných oprávnění. Pokud chcete povolit replikaci pro nový virtuální počítač, musíte mít následující oprávnění:

- Vytvoření virtuálního počítače ve vybrané skupině prostředků
- Vytvoření virtuálního počítače ve vybrané virtuální síti
- Zápis do účtu úložiště.
- Zápis do spravovaného disku.

K provedení těchto úloh by váš účet měl mít přiřazenou předdefinovanou roli Přispěvatel virtuálních počítačů. Pokud chcete spravovat operace Site Recovery v trezoru, měl by váš účet mít navíc přiřazenou předdefinovanou roli Přispěvatel Site Recovery.


## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

1. Na webu Azure Portal, klikněte na tlačítko **+ vytvořit prostředek**a na Marketplace vyhledejte **služby Recovery services**.
2. Klikněte na tlačítko **Backup a Site Recovery (OMS)** a na stránce Backup a Site Recovery na **vytvořit**. 
1. V **trezor služby Recovery Services** > **název**, zadejte popisný název pro identifikaci trezoru. Pro tuto sadu kurzů používáme název **ContosoVMVault**.
2. V **skupiny prostředků**, vyberte existující skupinu prostředků nebo vytvořte novou. Pro účely tohoto kurzu používáme **contosoRG**.
3. V **umístění**, vyberte oblast, ve kterém by měl být umístěn v trezoru. používáme oblast **Západní Evropa**.
4. Pokud chcete mít k trezoru rychlý přístup z řídicího panelu, vyberte **Připnout na řídicí panel** > **Vytvořit**.

   ![Vytvoření nového trezoru](./media/tutorial-prepare-azure/new-vault-settings.png)

   Nový trezor se zobrazí v části **Řídicí panel** > **Všechny prostředky** a na hlavní stránce **Trezory služby Recovery Services**.

## <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

Když virtuální počítače Azure se vytvoří spravované disky po převzetí služeb při selhání, připojí se k této síti.

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Sítě** > **Virtuální síť**.
2. Jako model nasazení nechte vybraný **Resource Manager**.
3. V části **Název** zadejte název sítě. Název musí být v rámci skupiny prostředků Azure jedinečný. V tomto kurzu používáme **ContosoASRnet**.
4. Zadejte skupinu prostředků, ve které se vytvoří síť. V tomto kurzu používáme existující skupinu prostředků **contosoRG**.
5. V části **Rozsah adres** zadejte rozsah pro síť **10.0.0.0/24**. V této síti nepoužíváme podsíť.
6. V části **Předplatné** vyberte předplatné, ve kterém chcete síť vytvořit.
7. Jako **umístění** vyberte **Západní Evropa**. Síť musí být ve stejné oblasti jako trezor Služeb zotavení.
8. Ponecháme výchozí možnosti základní ochrany před útoky DDoS bez koncového bodu služby v síti.
9. Klikněte na možnost **Vytvořit**.

   ![Vytvoření virtuální sítě](media/tutorial-prepare-azure/create-network.png)

   Vytvoření virtuální sítě trvá několik sekund. Po vytvoření se zobrazí na řídicím panelu webu Azure Portal.

## <a name="useful-links"></a>Užitečné odkazy

- Informace o [sítích Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Další informace o](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) spravované disky.



## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Příprava místní infrastruktury VMware na zotavení po havárii do Azure](tutorial-prepare-on-premises-vmware.md)
