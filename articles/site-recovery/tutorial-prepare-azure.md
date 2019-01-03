---
title: Příprava Azure na zotavení po havárii místních počítačů pomocí Azure Site Recovery | Microsoft Docs
description: Zjistěte, jak připravit Azure na zotavení po havárii místních počítačů pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 947e5d4004565fd10e81e703111daf26fce10368
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53973515"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Příprava prostředků Azure na zotavení po havárii místních počítačů

 [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii provozní kontinuity a zotavení po havárii (BCDR) tím, že zajišťuje provoz a dostupnost obchodních aplikací během plánovaných i neplánovaných výpadků. Site Recovery spravuje a orchestruje zotavení po havárii místních počítačů a virtuálních počítačů Azure, včetně replikace, převzetí služeb při selhání a zotavení.

Tento článek obsahuje první kurz řady, která ukazuje, jak nastavit zotavení po havárii pro místní virtuální počítače. Prostudujte si ho, pokud potřebujete chránit místní virtuální počítače VMware, virtuální počítače Hyper-V nebo fyzické servery.

> [!NOTE]
> Tyto kurzy demonstrují ten nejjednodušší způsob nasazení určitého scénáře. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny najdete v části **Postupy** pro odpovídající scénář.

V tomto článku se dozvíte, jak připravit komponenty Azure v případě, že chcete replikovat místní virtuální počítače (Hyper-V nebo VMware) nebo fyzické servery s Windows nebo Linuxem do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověření oprávnění účtu Azure k replikaci
> * Vytvoření účtu úložiště Azure Jsou v něm uložené image replikovaných počítačů.
> * Vytvořte trezor služby Recovery Services. Trezor obsahuje metadata a informace o konfiguraci virtuálních počítačů a další komponenty replikace.
> * Nastavit síť Azure. Když se po převzetí služeb při selhání vytvoří virtuální počítače Azure, připojí se do této sítě Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu [Azure Portal](http://portal.azure.com).

## <a name="verify-account-permissions"></a>Ověření oprávnění k účtu

Pokud jste si právě vytvořili bezplatný účet Azure, jste správcem předplatného. Jestliže správcem předplatného nejste, požádejte správce o přiřazení potřebných oprávnění. Pokud chcete povolit replikaci pro nový virtuální počítač, musíte mít následující oprávnění:

- Vytvoření virtuálního počítače ve vybrané skupině prostředků
- Vytvoření virtuálního počítače ve vybrané virtuální síti
- Zápis do vybraného účtu úložiště

K provedení těchto úloh by váš účet měl mít přiřazenou předdefinovanou roli Přispěvatel virtuálních počítačů. Pokud chcete spravovat operace Site Recovery v trezoru, měl by váš účet mít navíc přiřazenou předdefinovanou roli Přispěvatel Site Recovery.

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Bitové kopie replikovaných počítačů jsou uložené v úložišti Azure. Virtuální počítače Azure se vytvoří z úložiště, když převezmete služby při selhání z místní lokality do Azure. Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services. V tomto kurzu používáme oblast Západní Evropa.

1. V nabídce webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Úložiště** > **Účet úložiště – objekt blob, soubor, tabulka, fronta**.
2. V části **Vytvořit účet úložiště** zadejte název účtu. Pro účely těchto kurzů používáme název **contosovmsacct1910171607**. Vybraný název musí být v rámci Azure jedinečný, musí být dlouhý 3 až 24 znaků a obsahovat pouze číslice a malá písmena.
3. V části **Model nasazení** vyberte **Resource Manager**.
4. V části **Druh účtu** vyberte **Úložiště (obecné účely v1)**. Nevybírejte úložiště objektů blob.
5. V části **Replikace** vyberte výchozí redundanci úložiště **Geograficky redundantní úložiště jen pro čtení**. Možnost pro **vyžadování zabezpečeného přenosu** ponechte **zakázanou**.
6. V části **Výkon** vyberte **Standard** a v části **Úroveň přístupu** zvolte výchozí možnost **Horká**.
7. V části **Předplatné** vyberte předplatné, ve kterém chcete nový účet úložiště vytvořit.
8. V části **Skupina prostředků** zadejte název nové skupiny prostředků. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pro účely těchto kurzů používáme název **ContosoRG**.
9. V části **Umístění** vyberte zeměpisné umístění účtu úložiště. 

   ![vytvořit účet úložiště](media/tutorial-prepare-azure/create-storageacct.png)

9. Vyberte **Vytvořit** a vytvořte účet úložiště.

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

1. Na webu Azure Portal vyberte **Vytvořit prostředek** > **Úložiště** > **Backup a Site Recovery (OMS)**.
2. Jako **Název** zadejte popisný název pro identifikaci trezoru. Pro tuto sadu kurzů používáme název **ContosoVMVault**.
3. V části **Skupina prostředků** používáme **contosoRG**.
4. V části **Umístění** používáme oblast **Západní Evropa**.
5. Pokud chcete mít k trezoru rychlý přístup z řídicího panelu, vyberte **Připnout na řídicí panel** > **Vytvořit**.

   ![Vytvoření nového trezoru](./media/tutorial-prepare-azure/new-vault-settings.png)

   Nový trezor se zobrazí v části **Řídicí panel** > **Všechny prostředky** a na hlavní stránce **Trezory služby Recovery Services**.

## <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

Když se po převzetí služeb při selhání vytvoří z úložiště virtuální počítače Azure, připojí se do této sítě.

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
- Informace o [typech účtů úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)
- Další informace o [redundanci úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs#read-access-geo-redundant-storage) a [zabezpečeném přenosu](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) pro úložiště



## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Příprava místní infrastruktury VMware na zotavení po havárii do Azure](tutorial-prepare-on-premises-vmware.md)
