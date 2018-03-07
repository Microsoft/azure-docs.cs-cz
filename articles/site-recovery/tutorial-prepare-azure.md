---
title: "Vytváření prostředků pro použití s Azure Site Recovery | Microsoft Docs"
description: "Zjistěte, jak připravit Azure na replikaci místních počítačů pomocí Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 2f6ff1d30eef1fe34e55457d9bdd4295804ec16a
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2018
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>Příprava prostředků Azure na replikaci místních počítačů

 [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii provozní kontinuity a zotavení po havárii (BCDR) tím, že zajišťuje provoz a dostupnost obchodních aplikací během plánovaných i neplánovaných výpadků. Site Recovery spravuje a orchestruje zotavení po havárii místních počítačů a virtuálních počítačů Azure, včetně replikace, převzetí služeb při selhání a zotavení.

V tomto kurzu se dozvíte, jak připravit komponenty Azure v případě, že chcete replikovat místní virtuální počítače (Hyper-V nebo VMware) nebo fyzické servery s Windows nebo Linuxem do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověření oprávnění účtu k replikaci
> * Vytvoření účtu úložiště Azure
> * Nastavení sítě Azure. Když se po převzetí služeb při selhání vytvoří virtuální počítače Azure, připojí se do této sítě Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portálu Azure](http://portal.azure.com).

## <a name="verify-account-permissions"></a>Ověření oprávnění k účtu

Pokud jste si právě vytvořili bezplatný účet Azure, jste správcem předplatného. Jestliže správcem předplatného nejste, požádejte správce o přiřazení potřebných oprávnění. Pokud chcete povolit replikaci pro nový virtuální počítač, musíte mít následující oprávnění:

- Vytvoření virtuálního počítače ve vybrané skupině prostředků
- Vytvoření virtuálního počítače ve vybrané virtuální síti
- Zápis do vybraného účtu úložiště

Tato oprávnění má předdefinovaná role Přispěvatel virtuálních počítačů. Potřebujete také oprávnění ke správě operací Site Recovery. Všechna oprávnění nutná ke správě operací Site Recovery v trezoru služby Recovery Services má role Přispěvatel Site Recovery.

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Bitové kopie replikovaných počítačů jsou uložené v úložišti Azure. Virtuální počítače Azure se vytvoří z úložiště, když převezmete služby při selhání z místní lokality do Azure.

1. V nabídce webu [Azure Portal](https://portal.azure.com) vyberte **Nový** > **Úložiště** > **Účet úložiště**.
2. V části **Vytvořit účet úložiště** zadejte název účtu. Pro účely těchto kurzů použijte název **contosovmsacct1910171607**. Název musí být v rámci Azure jedinečný, musí být dlouhý 3 až 24 znaků a obsahovat pouze číslice a malá písmena.
3. V části **Model nasazení** vyberte **Resource Manager**.
4. V části **Druh účtu** vyberte **Obecné účely**. V části **Výkon** vyberte **Standard**. Nevybírejte úložiště objektů blob.
5. V části **Replikace** vyberte výchozí redundanci úložiště **Geograficky redundantní úložiště jen pro čtení**.
6. V části **Předplatné** vyberte předplatné, ve kterém chcete nový účet úložiště vytvořit.
7. V části **Skupina prostředků** zadejte název nové skupiny prostředků. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pro účely těchto kurzů použijte název **ContosoRG**.
8. V části **Umístění** vyberte zeměpisné umístění účtu úložiště. Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services. Pro účely těchto kurzů použijte oblast **Západní Evropa**.

   ![vytvořit účet úložiště](media/tutorial-prepare-azure/create-storageacct.png)

9. Vyberte **Vytvořit** a vytvořte účet úložiště.

## <a name="create-a-vault"></a>Vytvoření trezoru

1. Na webu Azure Portal vyberte **Vytvořit prostředek** > **Monitorování a správa** > **Backup a Site Recovery**.
2. Jako **Název** zadejte popisný název pro identifikaci trezoru. Pro účely tohoto kurzu použijte **ContosoVMVault**.
3. V části **Skupina prostředků** vyberte existující skupinu prostředků **contosoRG**.
4. V části **Umístění** zadejte oblast Azure **Západní Evropa**, která se používá v této sérii kurzů.
5. Pokud chcete mít k trezoru rychlý přístup z řídicího panelu, vyberte **Připnout na řídicí panel** > **Vytvořit**.

   ![Vytvoření nového trezoru](./media/tutorial-prepare-azure/new-vault-settings.png)

   Nový trezor se zobrazí v části **Řídicí panel** > **Všechny prostředky** a na hlavní stránce **Trezory služby Recovery Services**.

## <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

Když se po převzetí služeb při selhání vytvoří z úložiště virtuální počítače Azure, připojí se do této sítě.

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Sítě** > **Virtuální síť**.
2. Jako model nasazení nechte vybraný **Resource Manager**. Resource Manager je upřednostňovaný model nasazení. Pak proveďte tyto kroky:

   a. V části **Název** zadejte název sítě. Název musí být v rámci skupiny prostředků Azure jedinečný. Použijte název **ContosoASRnet**.

   b. V části **Skupina prostředků** použijte existující skupinu prostředků **contosoRG**.

   c. V části **Rozsah adres** zadejte rozsah síťových adres **10.0.0.0/24**.

   d. Pro účely tohoto kurzu nepotřebujete podsíť.

   e. V části **Předplatné** vyberte předplatné, ve kterém chcete síť vytvořit.

   f. Jako **umístění** vyberte **Západní Evropa**. Síť musí být ve stejné oblasti jako trezor Služeb zotavení.

3. Vyberte **Vytvořit**.

   ![Vytvoření virtuální sítě](media/tutorial-prepare-azure/create-network.png)

   Vytvoření virtuální sítě trvá několik sekund. Po vytvoření se zobrazí na řídicím panelu webu Azure Portal.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Příprava místní infrastruktury VMware na zotavení po havárii do Azure](tutorial-prepare-on-premises-vmware.md)
