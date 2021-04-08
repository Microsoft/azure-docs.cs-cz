---
title: Příprava prostředků Azure Site Recovery pro zotavení po havárii virtuálních počítačů řešení Azure VMware
description: Naučte se připravit prostředky Azure na zotavení po havárii počítačů řešení Azure VMware pomocí Azure Site Recovery.
services: site-recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 2bd305e3760a8c3d743037e7d90b71f5e9579eda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395474"
---
# <a name="prepare-azure-site-recovery-resources-for-disaster-recovery-of-azure-vmware-solution-vms"></a>Příprava prostředků Azure Site Recovery pro zotavení po havárii virtuálních počítačů řešení Azure VMware

Tento článek popisuje, jak připravit prostředky a součásti Azure, abyste mohli nastavit zotavení po havárii virtuálních počítačů řešení Azure VMware pomocí služby [Azure Site Recovery](site-recovery-overview.md) . [Řešení Azure VMware](../azure-vmware/introduction.md) poskytuje privátní cloudy v Azure. Tyto privátní cloudy obsahují vSphere clustery sestavené z vyhrazené holé infrastruktury Azure.

Tento článek je prvním kurzem série, kde se dozvíte, jak nastavit zotavení po havárii pro virtuální počítače řešení Azure VMware. 


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověřte, že účet Azure má oprávnění k replikaci.
> * Vytvořte trezor služby Recovery Services. Trezor obsahuje metadata a informace o konfiguraci virtuálních počítačů a další komponenty replikace.
> * Nastavte Azure Virtual Network (VNet). Když se po převzetí služeb při selhání vytvoří virtuální počítače Azure, připojí se k této síti.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu k nasazení scénáře. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny najdete v článku v části jak Site Recovery obsahu.

> [!NOTE]
> Některé z konceptů použití Azure Site Recovery pro řešení Azure VMware se překrývají s zotavením po havárii virtuálních počítačů VMware na Prem, a proto se odpovídajícím způsobem odkazuje na dokumentaci.

## <a name="before-you-start"></a>Než začnete

- [Nasazení](../azure-vmware/tutorial-create-private-cloud.md) privátního cloudu řešení Azure VMware v Azure
- Kontrola architektury pro zotavení po havárii [VMware](vmware-azure-architecture.md)
- Čtení běžných otázek pro [VMware](vmware-azure-common-questions.md)

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/). Pak se přihlaste k [Azure Portal](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Ověření oprávnění k účtu

Pokud jste si právě vytvořili bezplatný účet Azure, jste správcem předplatného a máte potřebná oprávnění. Jestliže správcem předplatného nejste, požádejte správce o přiřazení potřebných oprávnění. Pokud chcete povolit replikaci pro nový virtuální počítač, musíte mít následující oprávnění:

- Vytvoření virtuálního počítače ve vybrané skupině prostředků
- Vytvoření virtuálního počítače ve vybrané virtuální síti
- Zápis do účtu služby Azure Storage.
- Zapište na spravovaný disk Azure.

K provedení těchto úloh by váš účet měl mít přiřazenou předdefinovanou roli Přispěvatel virtuálních počítačů. Aby bylo možné spravovat Site Recovery operace v trezoru, měl by mít váš účet přiřazenou předdefinovanou roli Site Recovery Přispěvatel.


## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

1. V nabídce Azure Portal vyberte **vytvořit prostředek** a vyhledejte **obnovení** na Marketplace.
2. Ve výsledcích hledání vyberte **Backup a Site Recovery** a na stránce zálohování a Site Recovery klikněte na **vytvořit**. 
3. Na stránce **vytvořit trezor Recovery Services** vyberte **předplatné**. Používáme **předplatné contoso**.
4. V **skupiny prostředků** vyberte existující skupinu prostředků nebo vytvořte novou. Pro tento kurz používáme **contosoRG**.
5. Do pole **název trezoru** zadejte popisný název pro identifikaci trezoru. Pro tuto sadu kurzů používáme název **ContosoVMVault**.
6. V části **oblast** vyberte oblast, ve které se má Trezor umístit. používáme oblast **Západní Evropa**.
7. Vyberte **Zkontrolovat a vytvořit**.

   ![Snímek obrazovky s stránkou vytvořit Recovery Services trezor](./media/tutorial-prepare-azure/new-vault-settings.png)

   Nový trezor se teď zobrazí v části **řídicí panel**  >  **všechny prostředky** a na hlavní stránce **Recovery Services trezory** .

## <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

 Virtuální počítače řešení Azure VMware se replikují do Azure Managed disks. Když dojde k převzetí služeb při selhání, vytvoří se virtuální počítače Azure z těchto spravovaných disků a připojí se k síti Azure, kterou zadáte v tomto postupu.

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Sítě** > **Virtuální síť**.
2. Jako model nasazení nechte **Správce prostředků** vybrané.
3. V části **Název** zadejte název sítě. Název musí být v rámci skupiny prostředků Azure jedinečný. V tomto kurzu používáme **ContosoASRnet**.
4. Do pole **adresní prostor** zadejte rozsah adres virtuální sítě v notaci CdR. Používáme **10.1.0.0/24**.
5. V části **Předplatné** vyberte předplatné, ve kterém chcete síť vytvořit.
6. Zadejte **skupinu prostředků** , ve které bude síť vytvořena. V tomto kurzu používáme existující skupinu prostředků **contosoRG**.
7. V části **umístění** vyberte stejnou oblast, ve které byl vytvořen Recovery Services trezor. V našem kurzu je to **západní Evropa**. Síť musí být ve stejné oblasti jako trezor.
8. Do pole **Rozsah adres** zadejte rozsah sítě. Používáme **10.1.0.0/24** a nepoužívám podsíť.
9. Ponecháváme výchozí možnosti základní ochrany DDoS Protection bez koncového bodu služby nebo brány firewall v síti.
9. Vyberte **Vytvořit**.

   ![Snímek obrazovky s možnostmi vytvoření virtuální sítě](media/tutorial-prepare-azure/create-network.png)

Vytvoření virtuální sítě trvá několik sekund. Po vytvoření se zobrazí na řídicím panelu Azure Portal.




## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Příprava infrastruktury](avs-tutorial-prepare-avs.md)
- Informace o [sítích Azure](../virtual-network/virtual-networks-overview.md)
- [Přečtěte si o](../virtual-machines/managed-disks-overview.md) spravovaných discích.
