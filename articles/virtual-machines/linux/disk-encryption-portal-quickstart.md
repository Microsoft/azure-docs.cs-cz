---
title: Vytvoření a šifrování virtuálního počítače s Linuxem s využitím webu Azure Portal
description: V tomto rychlém startu se dozvíte, jak pomocí Azure Portal vytvořit a zašifrovat virtuální počítač se systémem Linux.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 188ba72f4da4e5a24554f895473f1c74e48d50fa
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558351"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Rychlý Start: vytvoření a šifrování virtuálního počítače s Azure Portal

Virtuální počítače Azure je možné vytvářet na webu Azure Portal. Azure Portal je uživatelské rozhraní v prohlížeči, pomocí kterého můžete vytvářet virtuální počítače a související prostředky. V tomto rychlém startu použijete Azure Portal k nasazení virtuálního počítače se systémem Linux se systémem Ubuntu 18,04 LTS, vytvoříte Trezor klíčů pro ukládání šifrovacích klíčů a zašifrujete virtuální počítač.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.
1. Na nové stránce v části Oblíbené vyberte **Ubuntu Server 18,04 LTS**.
1. Na kartě základy v části Podrobnosti o projektu ověřte, zda je vybráno správné předplatné.
1. V části Skupina prostředků vyberte **vytvořit novou**. Jako název zadejte *myResourceGroup* a vyberte **OK**.
1. Jako **název virtuálního počítače** zadejte *MyVM*.
1. V **oblasti oblast** vyberte *(US) východní USA*.
1. Ujistěte se, že je **Velikost** *standardní D2s V3*.
1. V části **účet správce** vyberte jako **typ ověřování** možnost *heslo* . Zadejte uživatelské jméno a heslo.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-linux-vm-creation.png" alt-text="Obrazovka pro vytvoření virtuálního počítače se systémem Linux":::

    > [!WARNING]
    > Karta "disky" obsahuje pole "typ šifrování" v části **Možnosti disku**. Toto pole slouží k zadání možností šifrování pro [Managed disks](../managed-disks-overview.md) + CMK, **ne** pro Azure Disk Encryption.
    >
    > Aby nedocházelo k nejasnostem, doporučujeme při dokončení tohoto kurzu úplně přeskočit kartu *disky* .

1. Vyberte kartu Správa a ověřte, že máte účet úložiště diagnostiky. Pokud nemáte žádné účty úložiště, vyberte *vytvořit novou*, pojmenujte svůj účet úložiště *MyStorageAccount* a vyberte OK.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-vm-creation-storage.png" alt-text="Obrazovka pro vytvoření zdroje dat":::

1. Klikněte na zkontrolovat + vytvořit.
1. Na stránce **Vytvoření virtuálního počítače** se zobrazí podrobnosti o virtuálním počítači, který se chystáte vytvořit. Až budete připraveni, vyberte **Vytvořit**.

Nasazení virtuálního počítače bude několik minut trvat. Po dokončení nasazení se přesuňte k další části.

## <a name="encrypt-the-virtual-machine"></a>Zašifrovat virtuální počítač

1. Po dokončení nasazení virtuálního počítače vyberte **Přejít k prostředku**.
1. Na levém bočním panelu vyberte **disky**.
1. Na horním panelu vyberte **Další nastavení** .
1. V části **nastavení šifrování**  >  **disky pro šifrování** vyberte **operační systém a datové disky**.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-disks-to-encryption.png" alt-text="Snímek obrazovky, který zvýrazňuje operační systém a datové disky.":::

1. V části **nastavení šifrování** zvolte **Vybrat Trezor klíčů a klíč pro šifrování**.
1. Na obrazovce **Vybrat klíč z Azure Key Vault** vyberte **vytvořit novou**.

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-create.png" alt-text="Snímek obrazovky, který zvýrazňuje vytvoření nového.":::

1. Vlevo od **trezoru klíčů a klíče** vyberte **kliknutím na tlačítko vyberte klíč**.
1. Na kartě **vybrat z Azure Key Vault** v poli **Key Vault** vyberte **vytvořit novou**.
1. Na obrazovce **Vytvoření trezoru klíčů** se ujistěte, že je skupina prostředků *myResourceGroup*, a zadejte název trezoru klíčů.  Každý Trezor klíčů v Azure musí mít jedinečný název.
1. Na kartě **zásady přístupu** zaškrtněte políčko **Azure Disk Encryption pro šifrování svazku** .

    :::image type="content" source="../media/disk-encryption/portal-quickstart-keyvault-enable.png" alt-text="Výběr disků a šifrování":::

1. Vyberte **Zkontrolovat a vytvořit**.  
1. Po úspěšném ověření trezoru klíčů vyberte **vytvořit**. Tím se vrátíte na Azure Key Vault obrazovce na **klíč pro výběr** .
1. Pole **klíče** nechejte prázdné a zvolte **Vybrat**.
1. V horní části obrazovky šifrování klikněte na **Uložit**. Automaticky otevírané okno vás upozorní, že se virtuální počítač restartuje. Klikněte na **Ano**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, virtuální počítač a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků pro příslušný virtuální počítač, vyberete Odstranit a pak potvrdíte název skupiny prostředků, kterou chcete odstranit.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Key Vault, která byla povolená pro šifrovací klíče, vytvořili jste virtuální počítač a povolili jste virtuální počítač pro šifrování.  

> [!div class="nextstepaction"]
> [Přehled Azure Disk Encryption](disk-encryption-overview.md)