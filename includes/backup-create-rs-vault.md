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
ms.openlocfilehash: 6167774171affda7e5469d5852a79657a6da700d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262665"
---
## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Recovery Services trezor je entita úložiště, která ukládá body obnovení vytvořené v průběhu času. Obsahuje také zásady zálohování, které jsou přidruženy k chráněným položkám.

Pokud chcete vytvořit trezor Recovery Services, postupujte podle těchto kroků.

1. Přihlaste se k předplatnému v [Azure Portal](https://portal.azure.com/).

1. V nabídce vlevo vyberte **všechny služby**.

    ![Vybrat všechny služby](./media/backup-create-rs-vault/click-all-services.png)

1. V dialogovém okně **všechny služby** zadejte *Recovery Services*. Seznam prostředků se filtruje podle vašeho zadání. V seznamu prostředků vyberte **Recovery Services trezory**.

    ![Zadejte a vyberte trezory Recovery Services.](./media/backup-create-rs-vault/all-services.png)

    Zobrazí se seznam trezorů Recovery Services v předplatném.

1. Na řídicím panelu **trezorů Recovery Services** vyberte **Přidat**.

    ![Přidání trezoru Recovery Services](./media/backup-create-rs-vault/add-button-create-vault.png)

    Otevře se dialogové okno **Recovery Services trezor** . Zadejte hodnoty pro **název**, **předplatné**, **skupinu prostředků**a **umístění**.

    ![Konfigurace trezoru Recovery Services](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Název**: zadejte popisný název pro identifikaci trezoru. Název musí být jedinečný pro předplatné Azure. Zadejte název, který má alespoň 2, ale ne více než 50 znaků. Název musí začínat písmenem a obsahovat jenom písmena, číslice a spojovníky.
   - **Předplatné**: vyberte předplatné, které chcete použít. Pokud jste členem jenom jednoho předplatného, uvidíte tento název. Pokud si nejste jistí, které předplatné se má použít, použijte výchozí (navrhované) předplatné. K dispozici je více možností pouze v případě, že je váš pracovní nebo školní účet spojen s více než jedním předplatným Azure.
   - **Skupina prostředků**: použijte existující skupinu prostředků nebo vytvořte novou. Pokud chcete zobrazit seznam dostupných skupin prostředků ve vašem předplatném, vyberte **použít existující**a pak v rozevíracím seznamu vyberte prostředek. Pokud chcete vytvořit novou skupinu prostředků, vyberte **vytvořit novou** a zadejte název. Další informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
   - **Umístění**: vyberte zeměpisnou oblast trezoru. K vytvoření trezoru pro ochranu virtuálních počítačů *musí* být trezor ve stejné oblasti jako virtuální počítače.

      > [!IMPORTANT]
      > Pokud si nejste jisti umístěním virtuálního počítače, zavřete dialogové okno. Přejít na seznam virtuálních počítačů na portálu. Pokud máte virtuální počítače v několika oblastech, vytvořte v každé oblasti Recovery Services trezor. Vytvořte trezor v prvním umístění, než vytvoříte trezor pro jiné umístění. Není nutné zadávat účty úložiště pro ukládání zálohovaných dat. Recovery Services trezor a Azure Backup popisovač automaticky.
      >
      >

1. Až budete připraveni k vytvoření trezoru Recovery Services, vyberte **vytvořit**.

    ![Vytvoření trezoru Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    Vytvoření trezoru Recovery Services může chvíli trvat. Sledujte oznámení o stavu v oblasti **oznámení** v pravém horním rohu portálu. Když je trezor vytvořený, zobrazí se v seznamu trezorů Recovery Services. Pokud váš trezor nevidíte, vyberte **aktualizovat**.

     ![Aktualizuje seznam trezorů služby Backup.](./media/backup-create-rs-vault/refresh-button.png)
