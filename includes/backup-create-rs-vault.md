---
title: zahrnout soubor
description: zahrnout soubor
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 5/14/2018
ms.author: markgal
ms.custom: include file
ms.openlocfilehash: 5590da80a1c217e7902e8e010688e40f5624898c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34664910"
---
## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení
Trezor služeb zotavení je entita, která ukládá zálohy a body obnovení, které byly vytvořeny v čase. Trezor služeb zotavení obsahuje také zásady zálohování, které jsou přidružené chráněných virtuálních počítačů.

Chcete-li vytvořit trezor Služeb zotavení:

1. Přihlaste se k vašemu předplatnému v [portál Azure](https://portal.azure.com/).
2. V levé nabídce vyberte **všechny služby**.

    ![Vyberte možnost všechny služby v hlavní nabídce](./media/backup-create-rs-vault/click-all-services.png) <br/>

3. V dialogovém okně všechny služby, zadejte *služeb zotavení*. Během zadávání váš vstup filtrování seznamu prostředků. Jakmile se zobrazí, vyberte **trezory služeb zotavení**.

    ![Typ služeb zotavení v dialogovém okně všechny služby](./media/backup-create-rs-vault/all-services.png) <br/>

    Zobrazí se seznam trezorů služeb zotavení v rámci předplatného.
4. Na **trezory služeb zotavení** nabídce vyberte možnost **přidat**.

    ![Vytvoření trezoru Recovery Services – krok 2](./media/backup-create-rs-vault/add-button-create-vault.png)

    **Trezory služeb zotavení** otevře se nabídka. Budete vyzváni k zadání informací o pro **název**, **předplatné**, **skupiny prostředků**, a **umístění**.

    ![Podokno "Trezory služeb zotavení"](./media/backup-create-rs-vault/create-new-vault-dialog.png)
5. Jako **Název** zadejte popisný název pro identifikaci trezoru. Název musí být jedinečné pro předplatné Azure. Zadejte název, který obsahuje alespoň dva, ale ne víc než 50 znaků. Název musí začínat písmenem a může obsahovat pouze písmena, číslice a pomlčky.
6. Pro **předplatné**, vyberte předplatné, které chcete použít. Pokud jste členem jenom jedno předplatné, zobrazí se stejným názvem. Pokud si nejste jisti, jaké předplatné použít, přejděte výchozí (nebo navrhované) předplatné. Existuje několik možností, pouze pokud vaše pracovní nebo školní účet je přidružen více předplatných Azure.
7. Pro **skupiny prostředků** můžete použít existující skupinu prostředků nebo vytvořte novou. Pokud chcete zobrazit seznam dostupných skupin prostředků v rámci vašeho předplatného, vyberte **použít existující**a klikněte na rozevírací nabídku. Chcete-li vytvořit novou skupinu prostředků, vyberte **vytvořit nový** a zadejte název. Úplné informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manageru](../articles/azure-resource-manager/resource-group-overview.md).
8. Pro **umístění** vyberte zeměpisnou oblast trezoru. Při vytváření trezoru k ochraně virtuálních počítačů, trezoru *musí* být ve stejné oblasti jako virtuální počítače.

   > [!IMPORTANT]
   > Pokud si nejste jisti oblastí, ve kterém je váš virtuální počítač, zavřete dialogové okno Vytvoření trezoru a přejít na seznam virtuálních počítačů na portálu. Pokud máte virtuální počítače v několika oblastech, vytvořte trezor služby Recovery Services v každé z nich. Vytvořte trezor nejprve v první oblasti, poté přejděte k další oblasti. Není potřeba specifikovat účty úložiště pro ukládání dat záloh. Trezor služeb zotavení a služba Azure Backup zpracování, automaticky.
   >
   >

9. Když budete chtít vytvořit trezor služeb zotavení, klikněte na tlačítko **vytvořit**.

    ![Seznam trezorů záloh](./media/backup-create-rs-vault/click-create-button.png)

    Vytvoření trezoru Služeb zotavení může chvíli trvat. Sledujte oznámení o stavu v části oznámení (pravém oblast portálu). Po vytvoření svůj trezor se zobrazí v seznamu trezorů služeb zotavení. Pokud stále nevidíte trezoru, klikněte na tlačítko **aktualizovat**.

     ![Seznam trezorů záloh](./media/backup-create-rs-vault/refresh-button.png)
