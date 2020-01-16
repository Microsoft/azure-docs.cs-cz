---
title: zahrnout soubor
description: zahrnout soubor
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: 74b5ebc89777f8043b44be4569a71fc8b33768d8
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979860"
---
## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Recovery Services trezor je entita, která ukládá zálohy a body obnovení vytvořené v průběhu času. Trezor Recovery Services obsahuje také zásady zálohování, které jsou přidruženy k chráněným virtuálním počítačům.

Chcete-li vytvořit trezor Služeb zotavení:

1. Přihlaste se k předplatnému v [Azure Portal](https://portal.azure.com/).

2. V nabídce vlevo vyberte **všechny služby**.

    ![vybrat všechny služby](./media/backup-create-rs-vault/click-all-services.png)

3. V dialogovém okně **všechny služby** zadejte **Recovery Services**. Seznam prostředků se filtruje podle vašeho zadání. V seznamu prostředků vyberte **Recovery Services trezory**.

    ![Zadejte a vyberte trezory Recovery Services.](./media/backup-create-rs-vault/all-services.png)

    Zobrazí se seznam trezorů Recovery Services v předplatném.

4. Na řídicím panelu **trezorů Recovery Services** vyberte **Přidat**.

    ![Přidání trezoru Recovery Services](./media/backup-create-rs-vault/add-button-create-vault.png)

    Otevře se dialogové okno **Recovery Services trezor** . Zadejte hodnoty pro **název**, **předplatné**, **skupinu prostředků**a **umístění**.

    ![Konfigurace trezoru Recovery Services](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Název**: zadejte popisný název pro identifikaci trezoru. Název musí být jedinečný pro předplatné Azure. Zadejte název, který obsahuje alespoň 2 znaky, ale ne více než 50 znaků. Název musí začínat písmenem a obsahovat jenom písmena, číslice a spojovníky.
   - **Předplatné**: vyberte předplatné, které chcete použít. Pokud jste členem jenom jednoho předplatného, uvidíte tento název. Pokud si nejste jistí, které předplatné se má použít, použijte výchozí (navrhované) předplatné. K dispozici je více možností pouze v případě, že je váš pracovní nebo školní účet spojen s více než jedním předplatným Azure.
   - **Skupina prostředků**: použijte existující skupinu prostředků nebo vytvořte novou. Pokud chcete zobrazit seznam dostupných skupin prostředků ve vašem předplatném, vyberte **použít existující**a pak v rozevíracím seznamu vyberte prostředek. Pokud chcete vytvořit novou skupinu prostředků, vyberte **vytvořit novou** a zadejte název. Úplné informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
   - **Umístění**: vyberte zeměpisnou oblast trezoru. K vytvoření trezoru pro ochranu virtuálních počítačů **musí** být trezor ve stejné oblasti jako virtuální počítače.

      > [!IMPORTANT]
      > Pokud si nejste jisti umístěním virtuálního počítače, zavřete dialogové okno. Přejít na seznam virtuálních počítačů na portálu. Pokud máte virtuální počítače v několika oblastech, vytvořte v každé oblasti Recovery Services trezor. Vytvořte trezor v prvním umístění, než vytvoříte trezor pro jiné umístění. Není nutné zadávat účty úložiště pro ukládání zálohovaných dat. Recovery Services trezor a popisovač služby Azure Backup, který se automaticky.
      >
      >

5. Až budete připraveni k vytvoření trezoru Recovery Services, vyberte **vytvořit**.

    ![Vytvoření trezoru Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    Vytvoření trezoru Recovery Services může chvíli trvat. Sledujte oznámení o stavu v oblasti **oznámení** v pravém horním rohu portálu. Když je trezor vytvořený, zobrazí se v seznamu trezorů Recovery Services. Pokud váš trezor nevidíte, vyberte **aktualizovat**.

     ![Aktualizuje seznam trezorů služby Backup.](./media/backup-create-rs-vault/refresh-button.png)
