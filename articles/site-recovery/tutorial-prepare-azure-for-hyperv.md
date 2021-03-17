---
title: Příprava Azure na zotavení po havárii technologie Hyper-V pomocí Azure Site Recovery
description: Přečtěte si, jak připravit Azure na zotavení po havárii místních virtuálních počítačů Hyper-V pomocí Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5ca03336fc60abbb458615e922a2556dd21a79a5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739674"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Příprava prostředků Azure pro zotavení po havárii technologie Hyper-V

 [Azure Site Recovery](site-recovery-overview.md) pomáhá provozní kontinuitu a zotavení po havárii (BCDR) tím, že udržuje podnikové aplikace spuštěné během plánovaných a neplánovaných výpadků. Site Recovery spravuje a orchestruje zotavení po havárii místních počítačů a virtuálních počítačů Azure, včetně replikace, převzetí služeb při selhání a zotavení.

Tento kurz je první v řadě, který popisuje, jak nastavit zotavení po havárii pro místní virtuální počítače Hyper-V.

> [!NOTE]
> Navrhneme kurzy pro zobrazení nejjednodušší cesty nasazení pro scénář. Tyto kurzy používají výchozí možnosti, pokud je to možné, a nezobrazují všechna možná nastavení a cesty. Další informace najdete v části How to (jak to) pro každý odpovídající scénář.

V tomto kurzu se dozvíte, jak připravit komponenty Azure v případě, že chcete replikovat místní virtuální počítače (Hyper-V) do Azure. Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Ověření oprávnění účtu Azure k replikaci
> * Vytvořte účet úložiště Azure, který ukládá image replikovaných počítačů.
> * Vytvořte Recovery Services trezor, ve kterém se uchovávají informace o metadatech a konfiguraci pro virtuální počítače a další komponenty replikace.
> * Nastavit síť Azure. Když se po převzetí služeb při selhání vytvoří virtuální počítače Azure, připojí se k této síti.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="sign-in"></a>Přihlásit se

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Ověření oprávnění k účtu

Pokud jste právě vytvořili bezplatný účet Azure, jste správcem tohoto předplatného. Pokud nejste správce, ve spolupráci se správcem přiřaďte potřebná oprávnění. Pokud chcete povolit replikaci pro nový virtuální počítač, musíte mít následující oprávnění:

- Vytvoření virtuálního počítače ve vybrané skupině prostředků
- Vytvoření virtuálního počítače ve vybrané virtuální síti
- Zápis do vybraného účtu úložiště

K dokončení těchto úloh by měl být vašemu účtu přiřazená předdefinovaná role Přispěvatel virtuálních počítačů. Pokud chcete spravovat operace Site Recovery v trezoru, měl by mít váš účet přiřazenou předdefinovanou roli Site Recovery Přispěvatel.

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Bitové kopie replikovaných počítačů jsou uložené v úložišti Azure. Virtuální počítače Azure se vytvoří z úložiště, když převezmete služby při selhání z místní lokality do Azure. Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.

1. V nabídce [Azure Portal](https://portal.azure.com) vyberte **vytvořit prostředek** úložiště  >    >  **účet úložiště – objekt blob, soubor, tabulka, fronta**.
2. V části **Vytvořit účet úložiště** zadejte název účtu.  Název, který zvolíte, musí být v rámci Azure jedinečný, musí být dlouhý 3 až 24 znaků a obsahovat pouze malá písmena a číslice. Pro tento kurz použijte **contosovmsacct1910171607**.
3. V části **Model nasazení** vyberte **Resource Manager**.
4. V rozevíracím rámečku **druh účtu** vyberte **úložiště (pro obecné účely V1)**. Nevybírejte úložiště objektů blob.
5. V části **Replikace** vyberte výchozí redundanci úložiště **Geograficky redundantní úložiště jen pro čtení**.
6. V části **Výkon** vyberte **Standard**. V poli **úroveň přístupu** vyberte výchozí možnost **Hot (aktivní**).
7. V části **předplatné** vyberte předplatné, ve kterém chcete vytvořit nový účet úložiště.
8. V části **Skupina prostředků** zadejte název nové skupiny prostředků. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pro tento kurz použijte **ContosoRG**.
9. V části **umístění** vyberte geografické umístění účtu úložiště. Pro tento kurz použijte **západní Evropa**.
10. Vyberte **Vytvořit** a vytvořte účet úložiště.

   ![Snímek obrazovky s možnostmi vytvoření účtu úložiště](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru služby Recovery Services

1. V Azure Portal vyberte **+ vytvořit prostředek** a pak vyhledejte Recovery Services Azure Marketplace.
2. Vyberte **Backup a Site Recovery (OMS)**. Potom na stránce **zálohování a Site Recovery** vyberte **vytvořit**.
1. V **trezoru služby Recovery services > název** zadejte popisný název pro identifikaci trezoru. Pro účely tohoto kurzu použijte **ContosoVMVault**.
2. V **skupiny prostředků** vyberte existující skupinu prostředků nebo vytvořte novou. Pro tento kurz použijte **contosoRG**.
3. V části **umístění** vyberte oblast, ve které se má Trezor nacházet. Pro tento kurz použijte **západní Evropa**.
4. Chcete-li rychle získat přístup k trezoru z řídicího panelu, vyberte možnost **Připnout na řídicí panel**  >  **vytvořit**.

![Snímek obrazovky s stránkou vytvořit Recovery Services trezor](./media/tutorial-prepare-azure/new-vault-settings.png)

Nový trezor se zobrazí v části **řídicí panel**  >  **všechny prostředky** a na hlavní stránce **Recovery Services trezory** .

## <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

Když se po převzetí služeb při selhání vytvoří z úložiště virtuální počítače Azure, připojí se do této sítě.

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Sítě** > **Virtuální síť**. Jako model nasazení nechte vybraný Resource Manager.
2. V části **Název** zadejte název sítě. Název musí být v rámci skupiny prostředků Azure jedinečný. Pro tento kurz použijte **ContosoASRnet**.
3. Zadejte skupinu prostředků, ve které se má síť vytvořit. Pro tento kurz použijte existující skupinu prostředků **contosoRG**.
4. Do pole **Rozsah adres** zadejte **10.0.0.0/24** jako rozsah sítě. Pro tuto síť neexistuje žádná podsíť.
5. V části **Předplatné** vyberte předplatné, ve kterém chcete síť vytvořit.
6. V **umístění** vyberte možnost **západní Evropa**. Síť musí být ve stejné oblasti jako trezor Služeb zotavení.
7. Ponechte výchozí možnosti základní DDoS Protection bez koncového bodu služby v síti.
8. Vyberte **Vytvořit**.

![Snímek obrazovky s možnostmi vytvoření virtuální sítě](media/tutorial-prepare-azure/create-network.png)

Vytvoření virtuální sítě trvá několik sekund. Po vytvoření se zobrazí na řídicím panelu Azure Portal.

## <a name="useful-links"></a>Užitečné odkazy

Projdeme si tyto možnosti:
- [Sítě Azure](../virtual-network/virtual-networks-overview.md)
- [Spravované disky](../virtual-machines/managed-disks-overview.md)



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Příprava místní infrastruktury technologie Hyper-V pro zotavení po havárii do Azure](hyper-v-prepare-on-premises-tutorial.md)
