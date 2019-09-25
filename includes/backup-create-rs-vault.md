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
ms.openlocfilehash: 4f6099975ad6968313e3083f2e7f5e3220db03cb
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71241084"
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

   - **Název**: Zadejte popisný název pro identifikaci trezoru. Název musí být jedinečný pro předplatné Azure. Zadejte název, který obsahuje alespoň 2 znaky, ale ne více než 50 znaků. Název musí začínat písmenem a obsahovat jenom písmena, číslice a spojovníky.
   - **Předplatné:** Vyberte předplatné, které chcete použít. Pokud jste členem jenom jednoho předplatného, uvidíte tento název. Pokud si nejste jistí, které předplatné se má použít, použijte výchozí (navrhované) předplatné. K dispozici je více možností pouze v případě, že je váš pracovní nebo školní účet spojen s více než jedním předplatným Azure.
   - **Skupina prostředků**: Použijte existující skupinu prostředků nebo vytvořte novou. Pokud chcete zobrazit seznam dostupných skupin prostředků ve vašem předplatném, vyberte **použít existující**a pak v rozevíracím seznamu vyberte prostředek. Pokud chcete vytvořit novou skupinu prostředků, vyberte **vytvořit novou** a zadejte název. Úplné informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md).
   - **Umístění**: Vyberte zeměpisnou oblast trezoru. K vytvoření trezoru pro ochranu virtuálních počítačů **musí** být trezor ve stejné oblasti jako virtuální počítače.

      > [!IMPORTANT]
      > Pokud si nejste jisti umístěním virtuálního počítače, zavřete dialogové okno. Přejít na seznam virtuálních počítačů na portálu. Pokud máte virtuální počítače v několika oblastech, vytvořte v každé oblasti Recovery Services trezor. Vytvořte trezor v prvním umístění, než vytvoříte trezor pro jiné umístění. Není nutné zadávat účty úložiště pro ukládání zálohovaných dat. Recovery Services trezor a popisovač služby Azure Backup, který se automaticky.
      >
      >

5. Až budete připraveni k vytvoření trezoru Recovery Services, vyberte **vytvořit**.

    ![Vytvoření trezoru Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    Vytvoření trezoru Recovery Services může chvíli trvat. Sledujte oznámení o stavu v oblasti **oznámení** v pravém horním rohu portálu. Když je trezor vytvořený, zobrazí se v seznamu trezorů Recovery Services. Pokud váš trezor nevidíte, vyberte **aktualizovat**.

     ![Aktualizuje seznam trezorů služby Backup.](./media/backup-create-rs-vault/refresh-button.png)
