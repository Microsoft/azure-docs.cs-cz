---
title: Vytvoření a šifrování virtuálního počítače s Windows pomocí Azure Portal
description: V tomto rychlém startu se dozvíte, jak pomocí Azure Portal vytvořit a zašifrovat virtuální počítač s Windows.
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 240c0e157d7fcd8b76ee7f42f4c780361df6281d
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72246120"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-with-the-azure-portal"></a>Rychlý Start: vytvoření a šifrování virtuálního počítače s Windows pomocí Azure Portal

Virtuální počítače Azure je možné vytvořit prostřednictvím Azure Portal. Azure Portal je uživatelské rozhraní založené na prohlížeči pro vytváření virtuálních počítačů a jejich přidružených prostředků. V tomto rychlém startu použijete Azure Portal k nasazení virtuálního počítače s Windows, na kterém běží Ubuntu 18,04 LTS, vytvoříte Trezor klíčů pro ukládání šifrovacích klíčů a zašifrujete virtuální počítač.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlaste se k Azure

Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

## <a name="create-a-key-vault"></a>Vytvoření trezoru klíčů

1. V levém horním rohu Azure Portal vyberte možnost **vytvořit prostředek** .
1. Do vyhledávacího pole zadejte **Key Vault**.
1. V seznamu výsledků vyberte možnost **Key Vault**.
1. V části Key Vault vyberte **vytvořit**.
1. Na obrazovce **Vytvoření trezoru klíčů** vyberte jedinečný název pro nový trezor klíčů.

    > [!Important]
    > Každý Key Vault musí mít jedinečný název. Následující příklad vytvoří Key Vault s názvem *myADEKV*, ale je nutné, abyste si pojmenovali něco jiného.

1. Vyberte **předplatné**.
1.  V části **Skupina prostředků**vyberte **vytvořit novou**. V automaticky otevíraném okně zadejte *myResourceGroup* pro název skupiny prostředků a pak zvolte **OK**. 

    ![Obrazovka pro vytvoření skupiny prostředků](../media/disk-encryption/portal-qs-keyvaultcreation.png)

1. V rozevírací nabídce **umístění** vyberte možnost **východní USA**.
1. U ostatních možností ponechte výchozí nastavení.
1. Vyberte možnost přístup k zásadám, která vás převezme na novou obrazovku.
1. Zaškrtněte políčko u možnosti Povolit přístup k Azure Disk Encryption šifrování svazku.

    ![Obrazovka pro vytvoření zdroje dat](../media/disk-encryption/portal-qs-keyvault-enable-encryption.png)

1. V dolní části obrazovky zásady přístupu klikněte na zkontrolovat + vytvořit.
1. Po kontrole klikněte na vytvořit.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levém horním rohu Azure Portal vyberte **vytvořit prostředek** .

1. Na nové stránce v části Oblíbené vyberte **Windows Server 2016 Datacenter**.
1. Na kartě **základy** v části **Project Details (podrobnosti projektu**) Zkontrolujte, že je vybrané správné předplatné.
1. V poli **Skupina prostředků**vyberte skupinu prostředků, kterou jste vytvořili při výše uvedeném trezoru klíčů (např. **myResourceGroup**).
1. Jako **název virtuálního počítače** zadejte *MyVM*a vyberte 
1. V poli **oblast**vyberte stejnou oblast, kterou jste použili při vytváření trezoru klíčů (například **východní USA**).
1. Ujistěte se, že je **Velikost** *standardní D2s V3*.
1. V části **účet správce**vyberte **heslo**. Zadejte uživatelské jméno a heslo.
    @no__t – obrazovka pro vytváření 0ResourceGroup @ no__t-1
1. Vyberte kartu Správa a ověřte, že máte účet úložiště diagnostiky. Pokud nemáte žádné účty úložiště, vyberte vytvořit nový, zadejte název nového účtu a pak vyberte OK. @no__t – obrazovka pro vytváření 0ResourceGroup @ no__t-1
1. Klikněte na zkontrolovat + vytvořit.
1. Na stránce **vytvořit virtuální počítač** můžete zobrazit podrobnosti o virtuálním počítači, který se chystáte vytvořit. Až budete připraveni, vyberte **vytvořit**.

Nasazení virtuálního počítače bude trvat několik minut. Až se nasazení dokončí, přejděte k další části.

## <a name="encrypt-the-virtual-machine"></a>Zašifrovat virtuální počítač

1. Po dokončení nasazení virtuálního počítače vyberte **Přejít k prostředku**.
1. Na levém bočním panelu vyberte **disky**.
1. Na obrazovce disky vyberte **šifrování**. 

    ![Výběr disků a šifrování](../media/disk-encryption/portal-qs-disks-to-encryption.png)

1. Na obrazovce šifrování v části **disky k šifrování**vyberte **operační systém a datové disky**.
1. V části **nastavení šifrování**klikněte na vybrat Trezor klíčů a klíč pro šifrování.
1. V pravém bočním panelu vyberte název trezoru klíčů, který jste vytvořili dříve, jako hodnotu * Trezor klíčů * *, a klikněte na **Vybrat**.

    ![Výběr disků a šifrování](../media/disk-encryption/portal-qs-encrypt-vm-screen.png)
1. V horní části obrazovky šifrování klikněte na Uložit. Automaticky otevírané okno vás upozorní, že se virtuální počítač restartuje. Klikněte na **Ano**.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, virtuální počítač a všechny související prostředky. Pokud to chcete udělat, vyberte skupinu prostředků pro virtuální počítač, vyberte Odstranit a pak potvrďte název skupiny prostředků, která se má odstranit.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Key Vault, která byla povolená pro šifrovací klíče, vytvořili jste virtuální počítač a povolili jste virtuální počítač pro šifrování.  

> [!div class="nextstepaction"]
> [Přehled Azure Disk Encryption](disk-encryption-overview.md)