---
title: Vytvoření a šifrování virtuálního počítače s Windows s využitím webu Azure Portal
description: V tomto rychlém startu se dozvíte, jak pomocí portálu Azure portal vytvořit a zašifrovat virtuální počítač s Windows.
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 2518de3ad5d0f583cd4962796e4adda5751351df
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74151043"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-with-the-azure-portal"></a>Úvodní příručka: Vytvoření a šifrování virtuálního počítače s Windows pomocí portálu Azure

Virtuální počítače Azure je možné vytvářet na webu Azure Portal. Azure Portal je uživatelské rozhraní v prohlížeči, pomocí kterého můžete vytvářet virtuální počítače a související prostředky. V tomto rychlém startu použijete portál Azure k nasazení virtuálního počítače (VM) s Windows s Ubuntu 18.04 LTS, k vytvoření trezoru klíčů pro ukládání šifrovacích klíčů a k šifrování virtuálního počítače.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portálu Azure](https://portal.azure.com).


## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.
1. Na stránce Nový v části Oblíbené vyberte **Windows Server 2016 Datacenter**.
1. Na kartě Základy v části Podrobnosti o projektu zkontrolujte, zda je vybráno správné předplatné, a pak zvolte **Vytvořit novou skupinu prostředků**. Jako název zadejte *myResourceGroup.*
1. Do **pole Název virtuálního počítače**zadejte *myvm*.
1. V **části Oblast**vyberte stejnou oblast, kterou jste použili *East US*při vytváření trezoru klíčů výše (např.
1. Ujistěte se, že **velikost** je *standardní D2s v3*.
1. V části **Účet správce**vyberte **Heslo**. Zadejte uživatelské jméno a heslo.
    ![Obrazovka pro vytvoření skupiny prostředků](../media/disk-encryption/portal-qs-windows-vm-creation.png)
1. Vyberte kartu Správa a ověřte, zda máte účet úložiště diagnostiky. Pokud nemáte žádné účty úložiště, vyberte "Vytvořit nový", pojmenujte ![nový účet a vyberte "Ok" ResourceGroup vytvoření obrazovky](../media/disk-encryption/portal-qs-vm-creation-storage.png)
1. Klikněte na "Recenze + vytvořit".
1. Na stránce **Vytvoření virtuálního počítače** se zobrazí podrobnosti o virtuálním počítači, který se chystáte vytvořit. Až budete připraveni, vyberte **Vytvořit**.

Nasazení virtuálního počítače bude několik minut trvat. Po dokončení nasazení se přesuňte k další části.

## <a name="encrypt-the-virtual-machine"></a>Šifrování virtuálního počítače

1. Po dokončení nasazení virtuálního počítače vyberte **Přejít na prostředek**.
1. Na levém postranním panelu vyberte **Disky**.
1. Na obrazovce Disky vyberte **Možnost Šifrování**. 

    ![disky a výběr šifrování](../media/disk-encryption/portal-qs-disks-to-encryption.png)

1. Na šifrovací obrazovce v části **Disky k šifrování**zvolte **operační systém a datové disky**.
1. V části **Nastavení šifrování**zvolte Vybrat trezor klíčů a klíč **pro šifrování**.
1. Na obrazovce **Select key from Azure Key Vault** vyberte Create **New**.

    ![disky a výběr šifrování](../media/disk-encryption/portal-qs-keyvault-create.png)

1. Na obrazovce **Vytvořit trezor klíčů** se ujistěte, že skupina prostředků je stejná jako ta, kterou jste použili k vytvoření virtuálního počítače.
1. Dejte svému trezoru klíčů jméno.  Každý trezor klíčů v Azure musí mít jedinečný název.
1. Na kartě **Zásady přístupu** zaškrtněte **políčko Šifrování disku Azure pro šifrování svazku.**

    ![disky a výběr šifrování](../media/disk-encryption/portal-qs-keyvault-enable.png)

1. Vyberte **Zkontrolovat a vytvořit**.  
1. Po schválení trezoru klíčů vyberte **vytvořit**. Tím se vrátíte na kartu Select key z obrazovky **Azure Key Vault.**
1. Ponechte pole **Klíč** prázdné a zvolte **Vybrat**.
1. V horní části šifrovací obrazovky klikněte na **Uložit**. Vyskakovací okno vás upozorní, že se virtuální počítač restartuje. Klepněte na tlačítko **Ano**.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, virtuální počítač a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků pro příslušný virtuální počítač, vyberete Odstranit a pak potvrdíte název skupiny prostředků, kterou chcete odstranit.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili trezor klíčů, který byl povolen pro šifrovací klíče, vytvořil i virtuální počítač a povolil virtuální počítač pro šifrování.  

> [!div class="nextstepaction"]
> [Azure Disk Encryption – přehled](disk-encryption-overview.md)
