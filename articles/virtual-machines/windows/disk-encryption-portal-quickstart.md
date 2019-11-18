---
title: Vytvoření a šifrování virtuálního počítače s Windows pomocí Azure Portal
description: V tomto rychlém startu se dozvíte, jak pomocí Azure Portal vytvořit a zašifrovat virtuální počítač s Windows.
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 2518de3ad5d0f583cd4962796e4adda5751351df
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151043"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-with-the-azure-portal"></a>Rychlý Start: vytvoření a šifrování virtuálního počítače s Windows pomocí Azure Portal

Virtuální počítače Azure je možné vytvářet na webu Azure Portal. Azure Portal je uživatelské rozhraní v prohlížeči, pomocí kterého můžete vytvářet virtuální počítače a související prostředky. V tomto rychlém startu použijete Azure Portal k nasazení virtuálního počítače s Windows, na kterém běží Ubuntu 18,04 LTS, vytvoříte Trezor klíčů pro ukládání šifrovacích klíčů a zašifrujete virtuální počítač.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlásit se na [Azure Portal](https://portal.azure.com).


## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.
1. Na nové stránce v části Oblíbené vyberte **Windows Server 2016 Datacenter**.
1. Na kartě základy v části Project Details (podrobnosti projektu) Zkontrolujte, zda je vybráno správné předplatné a pak zvolte možnost **vytvořit novou skupinu prostředků**. Jako název zadejte *myResourceGroup* .
1. Jako **název virtuálního počítače**zadejte *MyVM*.
1. V poli **oblast**vyberte stejnou oblast, kterou jste použili při vytváření trezoru klíčů (například *východní USA*).
1. Ujistěte se, že je **Velikost** *standardní D2s V3*.
1. V části **účet správce**vyberte **heslo**. Zadejte uživatelské jméno a heslo.
    obrazovka pro vytvoření zdroje ![](../media/disk-encryption/portal-qs-windows-vm-creation.png)
1. Vyberte kartu Správa a ověřte, že máte účet úložiště diagnostiky. Pokud nemáte žádné účty úložiště, vyberte vytvořit nový, zadejte název nového účtu a vyberte OK ![obrazovce pro vytváření zdrojů](../media/disk-encryption/portal-qs-vm-creation-storage.png)
1. Klikněte na zkontrolovat + vytvořit.
1. Na stránce **Vytvoření virtuálního počítače** se zobrazí podrobnosti o virtuálním počítači, který se chystáte vytvořit. Až budete připraveni, vyberte **Vytvořit**.

Nasazení virtuálního počítače bude několik minut trvat. Po dokončení nasazení se přesuňte k další části.

## <a name="encrypt-the-virtual-machine"></a>Zašifrovat virtuální počítač

1. Po dokončení nasazení virtuálního počítače vyberte **Přejít k prostředku**.
1. Na levém bočním panelu vyberte **disky**.
1. Na obrazovce disky vyberte **šifrování**. 

    ![Výběr disků a šifrování](../media/disk-encryption/portal-qs-disks-to-encryption.png)

1. Na obrazovce šifrování v části **disky k šifrování**vyberte **operační systém a datové disky**.
1. V části **nastavení šifrování**zvolte **Vybrat Trezor klíčů a klíč pro šifrování**.
1. Na obrazovce **Vybrat klíč z Azure Key Vault** vyberte **vytvořit novou**.

    ![Výběr disků a šifrování](../media/disk-encryption/portal-qs-keyvault-create.png)

1. Na obrazovce **Vytvoření trezoru klíčů** se ujistěte, že skupina prostředků je stejná jako ta, kterou jste použili k vytvoření virtuálního počítače.
1. Zadejte název trezoru klíčů.  Každý Trezor klíčů v Azure musí mít jedinečný název.
1. Na kartě **zásady přístupu** zaškrtněte políčko **Azure Disk Encryption pro šifrování svazku** .

    ![Výběr disků a šifrování](../media/disk-encryption/portal-qs-keyvault-enable.png)

1. Vyberte **Zkontrolovat a vytvořit**.  
1. Po úspěšném ověření trezoru klíčů vyberte **vytvořit**. Tím se vrátíte na Azure Key Vault obrazovce na **klíč pro výběr** .
1. Pole **klíče** nechejte prázdné a zvolte **Vybrat**.
1. V horní části obrazovky šifrování klikněte na **Uložit**. Automaticky otevírané okno vás upozorní, že se virtuální počítač restartuje. Klikněte na tlačítko **Ano**.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, virtuální počítač a všechny související prostředky. Pokud to chcete udělat, vyberte skupinu prostředků pro virtuální počítač, vyberte Odstranit a pak potvrďte název skupiny prostředků, která se má odstranit.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Key Vault, která byla povolená pro šifrovací klíče, vytvořili jste virtuální počítač a povolili jste virtuální počítač pro šifrování.  

> [!div class="nextstepaction"]
> [Přehled Azure Disk Encryption](disk-encryption-overview.md)
