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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38730494"
---
## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení
Trezor služby Recovery Services je entita, která ukládá zálohy a body obnovení, které byly vytvořeny v čase. Trezor služby Recovery Services obsahuje také zásady zálohování, které jsou spojeny s chráněných virtuálních počítačů.

Chcete-li vytvořit trezor Služeb zotavení:

1. Přihlaste se k vašemu předplatnému [webu Azure portal](https://portal.azure.com/).
2. V nabídce vlevo vyberte **všechny služby**.

    ![V hlavní nabídce zvolte možnost všechny služby](./media/backup-create-rs-vault/click-all-services.png) <br/>

3. V dialogovém okně všechny služby, zadejte *služby Recovery Services*. Když začnete psát, váš vstup filtruje seznam prostředků. Jakmile se zobrazí, vyberte **trezory služby Recovery Services**.

    ![Zadejte do dialogového okna všechny služby Recovery Services](./media/backup-create-rs-vault/all-services.png) <br/>

    Zobrazí se seznam trezorů služby Recovery Services v rámci předplatného.
4. Na **trezory služby Recovery Services** nabídce vyberte možnost **přidat**.

    ![Vytvoření trezoru Recovery Services – krok 2](./media/backup-create-rs-vault/add-button-create-vault.png)

    **Trezory služby Recovery Services** otevře se nabídka. To vás vyzve k zadání informací pro **název**, **předplatné**, **skupiny prostředků**, a **umístění**.

    ![Podokno "Trezory služby recovery Services"](./media/backup-create-rs-vault/create-new-vault-dialog.png)
5. Jako **Název** zadejte popisný název pro identifikaci trezoru. Název musí být jedinečný pro příslušné předplatné Azure. Zadejte název, který obsahuje alespoň dva, ale ne více než 50 znaků. Název musí začínat písmenem a může obsahovat jenom písmena, číslice a pomlčky.
6. Pro **předplatné**, vyberte předplatné, které chcete použít. Pokud jste členem jenom jedno předplatné, zobrazí se tento název. Pokud si nejste jistí, jaké předplatné použít, přejděte pomocí výchozí (nebo navrhované) předplatné. Existují možnosti více pouze tehdy, pokud váš pracovní nebo školní účet je přidružený k více předplatným Azure.
7. Pro **skupiny prostředků** můžete použít existující skupinu prostředků nebo vytvořte novou. Pokud chcete zobrazit seznam dostupných skupin prostředků ve vašem předplatném, vyberte **použít existující**a klikněte na rozevírací nabídku. Chcete-li vytvořit novou skupinu prostředků, vyberte **vytvořit nový** a zadejte název. Kompletní informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manageru](../articles/azure-resource-manager/resource-group-overview.md).
8. Pro **umístění** vyberte zeměpisnou oblast trezoru. Při vytváření trezoru pro ochranu virtuálních počítačů, trezor *musí* být ve stejné oblasti jako virtuální počítače.

   > [!IMPORTANT]
   > Pokud si nejste jisti umístění, ve kterém jsou vaše virtuální počítače, zavřete dialogové okno Vytvoření trezoru a přejít na seznam virtuálních počítačů na portálu. Pokud máte virtuální počítače v několika oblastech, vytvořte trezor služby Recovery Services v každé z nich. Vytvořte trezor nejprve v první oblasti, poté přejděte k další oblasti. Není potřeba specifikovat účty úložiště pro ukládání dat záloh. Trezor služby Recovery Services a služby Azure Backup, která umožňují automaticky zpracovat.
   >
   >

9. Až budete připravení vytvořit trezor služby Recovery Services, klikněte na tlačítko **vytvořit**.

    ![Seznam trezorů záloh](./media/backup-create-rs-vault/click-create-button.png)

    Vytvoření trezoru Služeb zotavení může chvíli trvat. Sledujte oznámení o stavu v části oznámení (horní pravé oblasti na portálu). Poté, co je trezor vytvořený, zobrazí se v seznamu trezorů služby Recovery Services. Pokud váš trezor stále nevidíte, klikněte na tlačítko **aktualizovat**.

     ![Seznam trezorů záloh](./media/backup-create-rs-vault/refresh-button.png)
