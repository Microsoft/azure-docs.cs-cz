---
title: Azure zásobníku rychlý Start – vytvoření virtuálního počítače s Windows
description: Azure zásobníku rychlý Start - vytvořit virtuální počítač s Windows pomocí portálu
services: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/23/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 5776fc472483018eb2c9e4f8962d0b1e8bce8081
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Rychlý úvod: vytvoření virtuálního počítače s Windows serverem na portálu Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Virtuální počítač systému Windows Server 2016 můžete vytvořit pomocí portálu Azure zásobníku. Postupujte podle kroků v tomto článku vytváření a používání virtuálního počítače.

## <a name="sign-in-to-the-azure-stack-portal"></a>Přihlaste se k portálu Azure zásobníku

Přihlaste se k portálu Azure zásobníku. Na adresu na portálu Azure zásobníku závisí, na který produkt Azure zásobníku se připojujete k:

* Pro Azure zásobníku Development Kit (ASDK) přejděte na: https://portal.local.azurestack.external.
* Pro systém Azure zásobníku integrované přejděte na adresu URL, kterou vaše operátor zásobník Azure poskytuje.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. Klikněte na tlačítko **nové** > **výpočetní** > **zkušební verze systému Windows Server 2016 Datacenter** > **vytvořit**. Pokud nevidíte **Windows Server 2016 Datacenter Eval** položku, obraťte se na vaše operátor zásobník Azure. Požádejte si ho jak je popsáno v přidat na Marketplace s cílem [přidat bitovou kopii virtuálního počítače Windows serveru 2016 do zásobníku Azure marketplace](../azure-stack-add-default-image.md) článku.

    ![Postup vytvoření virtuálního počítače s Windows v portálu](media/azure-stack-quick-windows-portal/image01.png)
2. V části **Základy**, zadejte **název**, **uživatelské jméno**, a **heslo**. Vyberte **předplatné**. Vytvoření **skupiny prostředků**, nebo vyberte existující jeden, vyberte možnost **umístění**a potom klikněte na **OK**.

    ![Konfigurace základního nastavení](media/azure-stack-quick-windows-portal/image02.png)
3. V části **zvolte velikost**, klikněte na tlačítko **D1 standardní** > **vyberte**.
    ![Zvolte velikost virtuálního počítače](media/azure-stack-quick-windows-portal/image03.png)
4. V části **nastavení**, přijměte výchozí hodnoty a klikněte na **OK**.
    ![Konfigurace nastavení virtuálního počítače](media/azure-stack-quick-windows-portal/image04.png)
5. V části **Souhrn**, klikněte na tlačítko **OK** k vytvoření virtuálního počítače.
    ![Zobrazit souhrn a vytvoření virtuálního počítače](media/azure-stack-quick-windows-portal/image05.png)
6. Chcete-li zobrazit nový virtuální počítač, klikněte na tlačítko **všechny prostředky**, vyhledejte název virtuálního počítače a potom klikněte na jeho název ve výsledcích hledání.
    ![Virtuální počítač v tématu](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi, používání virtuálních počítačů, odstraňte virtuální počítač a jeho prostředky. To pokud chcete udělat, vyberte skupinu prostředků na stránce virtuální počítač a klikněte na tlačítko **odstranit**.

## <a name="next-steps"></a>Další postup

V této úvodní jste nasadili základní virtuální počítač Windows serveru. Další informace o virtuálních počítačích Azure zásobníku, nadále [důležité informace pro virtuální počítače v Azure zásobníku](azure-stack-vm-considerations.md).
