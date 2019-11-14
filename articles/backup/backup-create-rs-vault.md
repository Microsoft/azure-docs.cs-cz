---
title: 'Azure Backup: vytvoření trezorů Recovery Services'
description: V tomto článku se dozvíte, jak vytvořit Recovery Services trezory, které ukládají zálohy a body obnovení.
ms.reviewer: sogup
author: dcurwin
manager: carmonm
keywords: Recovery Services trezor; Zálohování virtuálních počítačů Azure; Obnovení virtuálního počítače Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: dacurwin
ms.openlocfilehash: 30e2f8812b14f27b57ef22e30aea89f19fa56e58
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074878"
---
# <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Recovery Services trezor je entita, která ukládá zálohy a body obnovení vytvořené v průběhu času. Trezor Recovery Services obsahuje také zásady zálohování, které jsou přidruženy k chráněným virtuálním počítačům.

Chcete-li vytvořit trezor Služeb zotavení:

1. Přihlaste se k předplatnému v [Azure Portal](https://portal.azure.com/).

2. V nabídce vlevo vyberte **všechny služby**.

    ![Vybrat všechny služby](./media/backup-create-rs-vault/click-all-services.png)

3. V dialogovém okně **všechny služby** zadejte **Recovery Services**. Seznam prostředků se filtruje podle vašeho zadání. V seznamu prostředků vyberte **Recovery Services trezory**.

    ![Zadejte a vyberte trezory Recovery Services.](./media/backup-create-rs-vault/all-services.png)

    Zobrazí se seznam trezorů Recovery Services v předplatném.

4. Na řídicím panelu **trezorů Recovery Services** vyberte **Přidat**.

    ![Přidání trezoru Recovery Services](./media/backup-create-rs-vault/add-button-create-vault.png)

    Otevře se dialogové okno **Recovery Services trezor** . Zadejte hodnoty pro **název**, **předplatné**, **skupinu prostředků**a **umístění**.

    ![Konfigurace trezoru Recovery Services](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Název**: zadejte popisný název pro identifikaci trezoru. Název musí být jedinečný pro předplatné Azure. Zadejte název, který obsahuje alespoň 2 znaky, ale ne více než 50 znaků. Název musí začínat písmenem a obsahovat jenom písmena, číslice a spojovníky.
   - **Předplatné**: vyberte předplatné, které chcete použít. Pokud jste členem jenom jednoho předplatného, uvidíte tento název. Pokud si nejste jistí, které předplatné se má použít, použijte výchozí (navrhované) předplatné. K dispozici je více možností pouze v případě, že je váš pracovní nebo školní účet spojen s více než jedním předplatným Azure.
   - **Skupina prostředků**: použijte existující skupinu prostředků nebo vytvořte novou. Pokud chcete zobrazit seznam dostupných skupin prostředků ve vašem předplatném, vyberte **použít existující**a pak v rozevíracím seznamu vyberte prostředek. Pokud chcete vytvořit novou skupinu prostředků, vyberte **vytvořit novou** a zadejte název. Úplné informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Umístění**: vyberte zeměpisnou oblast trezoru. K vytvoření trezoru pro ochranu virtuálních počítačů **musí** být trezor ve stejné oblasti jako virtuální počítače.

      > [!IMPORTANT]
      > Pokud si nejste jisti umístěním virtuálního počítače, zavřete dialogové okno. Přejít na seznam virtuálních počítačů na portálu. Pokud máte virtuální počítače v několika oblastech, vytvořte v každé oblasti Recovery Services trezor. Vytvořte trezor v prvním umístění, než vytvoříte trezor pro jiné umístění. Není nutné zadávat účty úložiště pro ukládání zálohovaných dat. Recovery Services trezor a popisovač služby Azure Backup, který se automaticky.
      >
      >

5. Až budete připraveni k vytvoření trezoru Recovery Services, vyberte **vytvořit**.

    ![Vytvoření trezoru Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    Vytvoření trezoru Recovery Services může chvíli trvat. Sledujte oznámení o stavu v oblasti **oznámení** v pravém horním rohu portálu. Když je trezor vytvořený, zobrazí se v seznamu trezorů Recovery Services. Pokud váš trezor nevidíte, vyberte **aktualizovat**.

     ![Aktualizuje seznam trezorů služby Backup.](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Nastavit redundanci úložiště

Azure Backup automaticky zpracovává úložiště pro trezor. Musíte určit způsob replikace tohoto úložiště.

1. V okně **Trezory služby Recovery Services** klikněte na nový trezor. V části **Nastavení** klikněte na **vlastnosti**.
2. V části **vlastnosti**v části **Konfigurace zálohování**klikněte na **aktualizovat**.

3. Vyberte typ replikace úložiště a klikněte na **Uložit**.

     ![Nastavení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Pokud používáte Azure jako primární koncový bod úložiště záloh, doporučujeme, abyste používali výchozí **geograficky redundantní** nastavení.
   - Pokud Azure nepoužíváte jako primární koncový bod úložiště záloh, vyberte **Místně redundantní** – snížíte tím náklady na úložiště Azure.
   - Přečtěte si další informace o [geografické](../storage/common/storage-redundancy-grs.md) a [místní](../storage/common/storage-redundancy-lrs.md) redundanci.

> [!NOTE]
> Změna **typu replikace úložiště** (místně redundantní/geograficky redundantní) pro trezor služby Recovery Services je potřeba provést před konfigurací záloh v trezoru. Když nakonfigurujete zálohování, možnost změnit je zakázaná a **typ replikace úložiště**nemůžete změnit.

## <a name="next-steps"></a>Další kroky

[Další informace](backup-azure-recovery-services-vault-overview.md) Recovery Services trezory.
[Další informace](backup-azure-delete-vault.md) Odstraňte trezory Recovery Services.
