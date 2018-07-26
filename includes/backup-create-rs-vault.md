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
ms.openlocfilehash: 0ff9bf829e2dbe1bca078360ccded94bad63d9a6
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249457"
---
## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení
Trezor služby Recovery Services je entita, která ukládá zálohy a body obnovení vytvořené v čase. Trezor služby Recovery Services obsahuje také zásady zálohování, které jsou spojeny s chráněných virtuálních počítačů.

Chcete-li vytvořit trezor Služeb zotavení:

1. Přihlaste se k vašemu předplatnému [webu Azure portal](https://portal.azure.com/).

2. V nabídce vlevo vyberte **všechny služby**.

    ![Vyberte všechny služby](./media/backup-create-rs-vault/click-all-services.png)

3. V **všechny služby** dialogového okna zadejte **služby Recovery Services**. Vyfiltruje seznam prostředků podle vašeho zadání. V seznamu prostředků vyberte **trezory služby Recovery Services**.

    ![Zadejte a vyberte trezory služby Recovery Services](./media/backup-create-rs-vault/all-services.png)

    Zobrazí se seznam trezorů služby Recovery Services v rámci předplatného.
    
4. Na **trezory služby Recovery Services** řídicího panelu, vyberte **přidat**.

    ![Přidat trezor služby Recovery Services](./media/backup-create-rs-vault/add-button-create-vault.png)

    **Trezor služby Recovery Services** zobrazí se dialogové okno. Zadejte hodnoty pro **název**, **předplatné**, **skupiny prostředků**, a **umístění**.

    ![Konfigurace trezoru služby Recovery Services](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Název**: Zadejte popisný název pro identifikaci trezoru. Název musí být jedinečný pro příslušné předplatné Azure. Zadejte název, který má alespoň dva, ale ne více než 50 znaků. Název musí začínat písmenem a obsahovat jenom písmena, číslice a pomlčky.
   - **Předplatné**: Vyberte předplatné, které chcete použít. Pokud jste členem jenom jedno předplatné, zobrazí se vám tento název. Pokud si nejste jistí, jaké předplatné použít, použijte výchozí (doporučeno) předplatné. Existují možnosti více pouze tehdy, pokud váš pracovní nebo školní účet souvisí s více než jedno předplatné Azure.
   - **Skupina prostředků**: použijte existující skupinu prostředků nebo vytvořte novou. Pokud chcete zobrazit seznam dostupných skupin prostředků ve vašem předplatném, vyberte **použít existující**a pak vyberte prostředek z rozevíracího seznamu. Chcete-li vytvořit novou skupinu prostředků, vyberte **vytvořit nový** a zadejte název. Kompletní informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manageru](../articles/azure-resource-manager/resource-group-overview.md).
   - **Umístění**: vyberte zeměpisnou oblast trezoru. Vytvoření trezoru pro ochranu virtuálních počítačů, trezor **musí** být ve stejné oblasti jako virtuální počítače.

      > [!IMPORTANT]
      > Pokud si nejste jisti umístění virtuálního počítače, zavřete dialogové okno. Přejdete na seznam virtuálních počítačů na portálu. Pokud máte virtuální počítače v několika oblastech, vytvořte trezor služby Recovery Services v každé oblasti. Vytvořte trezor nejprve v první oblasti, než vytvoříte trezor pro jiné umístění. Není potřeba specifikovat účty úložiště pro ukládání dat záloh. Trezor služby Recovery Services a služby Azure Backup, která umožňují automaticky zpracovat.
      >
      >

5. Až budete připravení vytvořit trezor služby Recovery Services, vyberte **vytvořit**.

    ![Vytvoření trezoru služby Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    Může trvat nějakou vytvořte trezor služby Recovery Services. Sledujte oznámení o stavu v **oznámení** oblasti v pravém horním rohu portálu. Poté, co je trezor vytvořený, je zobrazen v seznamu trezorů služby Recovery Services. Pokud svůj trezor nevidíte, vyberte **aktualizovat**.

     ![Aktualizovat seznam trezorů záloh](./media/backup-create-rs-vault/refresh-button.png)
