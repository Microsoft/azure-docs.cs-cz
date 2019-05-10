---
title: Příprava prostředků Azure na zotavení po havárii místních počítačů
description: Zjistěte, jak připravit Azure na zotavení po havárii místních virtuálních počítačů Hyper-V pomocí Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 29189a5919a01fcb897758fb64ca9e84b9381fb6
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410903"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Příprava prostředků Azure na zotavení po havárii místních počítačů

 [Azure Site Recovery](site-recovery-overview.md) pomáhá obchodní kontinuity podnikových procesů a zotavení po havárii (BCDR) tím, že udržování obchodních aplikací během plánovaných i neplánovaných výpadků. Site Recovery spravuje a orchestruje zotavení po havárii místních počítačů a virtuálních počítačů Azure, včetně replikace, převzetí služeb při selhání a zotavení.

Tento kurz je první z řady, který popisuje, jak nastavit zotavení po havárii pro místní virtuální počítače Hyper-V.

> [!NOTE]
> Navrhujeme kurzy, které ukazují nejjednodušší způsob nasazení pro scénář. Tyto kurzy použijte výchozí možnosti, pokud je to možné a nezobrazovat všechna možná nastavení a cesty. Další informace najdete v části "Jak na" pro každý odpovídající scénář.

V tomto kurzu se dozvíte, jak připravit komponenty Azure v případě, že chcete replikovat místní virtuální počítače (Hyper-V) do Azure. Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Ověření oprávnění účtu Azure k replikaci
> * Vytvořte účet úložiště Azure, které jsou uloženy bitové kopie replikovaných počítačů.
> * Vytvořte trezor služby Recovery Services, která ukládá metadata a konfigurační informace pro virtuální počítače a další komponenty replikace.
> * Nastavit síť Azure. Když jsou virtuální počítače Azure vytvořené po převzetí služeb při selhání, připojí se k této síti.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.

## <a name="sign-in"></a>Přihlášení

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Ověření oprávnění k účtu

Pokud jste právě vytvořili bezplatný účet Azure, jste správce daného předplatného. Pokud si nejste správce, požádejte správce o přiřazení potřebných oprávnění. Pokud chcete povolit replikaci pro nový virtuální počítač, musíte mít následující oprávnění:

- Vytvoření virtuálního počítače ve vybrané skupině prostředků
- Vytvoření virtuálního počítače ve vybrané virtuální síti
- Zápis do vybraného účtu úložiště

K dokončení těchto úloh by váš účet přiřazenou předdefinovanou roli Přispěvatel virtuálních počítačů. Ke správě operací Site Recovery v trezoru, by měl být váš účet přiřazenou předdefinovanou roli Přispěvatel Site Recovery.

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Bitové kopie replikovaných počítačů jsou uložené v úložišti Azure. Virtuální počítače Azure se vytvoří z úložiště, když převezmete služby při selhání z místní lokality do Azure. Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.

1. V [webu Azure portal](https://portal.azure.com) nabídce vyberte možnost **vytvořit prostředek** > **úložiště** > **účet úložiště – objekt blob, soubor, tabulka, fronta** .
2. V části **Vytvořit účet úložiště** zadejte název účtu.  Název zvoleném musí být jedinečný v rámci Azure, se ze 3 až 24 znaků dlouhý a pouze použití malá písmena a číslice. Pro účely tohoto kurzu použijte **contosovmsacct1910171607**.
3. V části **Model nasazení** vyberte **Resource Manager**.
4. V **druh účtu**vyberte **úložiště (pro obecné účely v1)**. Nevybírejte úložiště objektů blob.
5. V části **Replikace** vyberte výchozí redundanci úložiště **Geograficky redundantní úložiště jen pro čtení**. Ponechte zabezpečený přenos vyžaduje nastavení jako – zakázané.
6. V části **Výkon** vyberte **Standard**. Dále v **úroveň přístupu**, vyberte výchozí možnost **Hot**.
7. V **předplatné**, vyberte předplatné, ve kterém chcete vytvořit nový účet úložiště.
8. V části **Skupina prostředků** zadejte název nové skupiny prostředků. Skupina prostředků Azure je logický kontejner, ve které se nasazují a spravují prostředky. Pro účely tohoto kurzu použijte **ContosoRG**.
9. V **umístění**, zvolte zeměpisné umístění účtu úložiště. Pro účely tohoto kurzu použijte **západní Evropa**.
10. Vyberte **Vytvořit** a vytvořte účet úložiště.

   ![vytvořit účet úložiště](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru služby Recovery Services

1. Na webu Azure Portal, vyberte **+ vytvořit prostředek**a poté vyhledejte služby Recovery Services na webu Azure Marketplace.
2. Vyberte **Backup a Site Recovery (OMS)**. V položce **Backup a Site Recovery** stránce **vytvořit**.
1. V **trezor služby Recovery services > název**, zadejte popisný název pro identifikaci trezoru. Pro účely tohoto kurzu použijte **ContosoVMVault**.
2. V **skupiny prostředků**, vyberte existující skupinu prostředků nebo vytvořte novou. Pro účely tohoto kurzu použijte **contosoRG**.
3. V **umístění**, vyberte oblast, ve kterém by měl být umístěn v trezoru. Pro účely tohoto kurzu použijte **západní Evropa**.
4. Pokud chcete mít k trezoru rychlý přístup z řídicího panelu, vyberte **Připnout na řídicí panel** > **Vytvořit**.

![Vytvoření nového trezoru](./media/tutorial-prepare-azure/new-vault-settings.png)

Nový trezor se zobrazí v části **Řídicí panel** > **Všechny prostředky** a na hlavní stránce **Trezory služby Recovery Services**.

## <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

Když se po převzetí služeb při selhání vytvoří z úložiště virtuální počítače Azure, připojí se do této sítě.

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Sítě** > **Virtuální síť**. Ponechte vybraný jako model nasazení Resource Manageru.
2. V části **Název** zadejte název sítě. Název musí být v rámci skupiny prostředků Azure jedinečný. Pro účely tohoto kurzu použijte **ContosoASRnet**.
3. Zadejte skupinu prostředků, ve kterém chcete vytvořit síť. Pro účely tohoto kurzu použijte existující skupinu prostředků **contosoRG**.
4. V **rozsah adres**, zadejte **10.0.0.0/24** jako rozsah adres v síti. Neexistuje žádná podsíť pro tuto síť.
5. V části **Předplatné** vyberte předplatné, ve kterém chcete síť vytvořit.
6. V **umístění**, zvolte **západní Evropa**. Síť musí být ve stejné oblasti jako trezor Služeb zotavení.
7. Ponechte výchozí možnosti základní ochranu před útoky DDoS, se žádný koncový bod služby v síti.
8. Vyberte **Vytvořit**.

![Vytvoření virtuální sítě](media/tutorial-prepare-azure/create-network.png)

Vytvoření virtuální sítě trvá několik sekund. Po jeho vytvoření, uvidíte na řídicím panelu Azure portal.

## <a name="useful-links"></a>Užitečné odkazy

Další informace o:
- [Sítě Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Spravované disky](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)



## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Příprava na místní infrastrukturu Hyper-V pro zotavení po havárii do Azure](hyper-v-prepare-on-premises-tutorial.md)
