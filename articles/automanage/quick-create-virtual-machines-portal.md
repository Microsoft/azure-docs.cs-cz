---
title: Rychlý Start – povolení automatického spravování Azure pro virtuální počítače v Azure Portal
description: Zjistěte, jak rychle povolit automanage u virtuálních počítačů na novém nebo existujícím virtuálním počítači v Azure Portal.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: jushiman
ms.openlocfilehash: 6e0e582ed37230ba3f379f193a229cfec06f066c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648029"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Rychlý Start: povolení služby Azure automanage pro virtuální počítače v Azure Portal

Začínáme s Azure automanage pro virtuální počítače pomocí Azure Portal pro povolení automatizované správy na novém nebo existujícím virtuálním počítači.


## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) před tím, než začnete.

> [!NOTE]
> Bezplatné zkušební účty nemají přístup k virtuálním počítačům použitým v tomto kurzu. Upgradujte prosím na předplatné s průběžnými platbami.

> [!IMPORTANT]
> Abyste mohli povolit autosprávu pomocí stávajícího účtu pro správu, musíte mít ve skupině prostředků, která obsahuje vaše virtuální počítače, roli **Přispěvatel** . Pokud povolujete možnost automanage pomocí nového účtu pro autosprávu, budete potřebovat následující oprávnění: role **vlastníka** nebo **přispěvatele** spolu s rolemi **Správce přístupu uživatelů** v rámci vašeho předplatného.


## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://aka.ms/AutomanagePortal-Ignite21).

## <a name="enable-automanage-for-a-single-vm"></a>Povolení automanage pro jeden virtuální počítač

1. Přejděte k virtuálnímu počítači, který chcete povolit.

2. Klikněte na položku **automanage (Preview)** v obsahu v části **operace**.

3. Vyberte **Začínáme**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\VMManage-GetStarted.png" alt-text="Začínáme s jedním virtuálním počítačem.":::

4. Zvolte nastavení pro vaši správu (prostředí, předvolby, spravovat účet) a **Povolit**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\VMManage-Enable.png" alt-text="Povolte na jednom virtuálním počítači.":::

## <a name="enable-automanage-for-multiple-vms"></a>Povolení automanage pro více virtuálních počítačů

1. Na panelu hledání vyhledejte a vyberte možnost **automanage – osvědčené postupy pro Azure Machine**.

2. Vyberte **Povolit na stávajícím virtuálním počítači**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Povolit na stávajícím virtuálním počítači.":::

3. V okně **Vybrat počítače** :
    1. Vyfiltrujte seznam virtuálních počítačů podle vašeho **předplatného** a **skupiny prostředků**.
    1. Zaškrtněte políčko u každého virtuálního počítače, který chcete připojit.
    1. Klikněte na tlačítko **Vybrat** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Vyberte existující virtuální počítač ze seznamu dostupných virtuálních počítačů.":::

4. V části **konfigurační profil** klikněte na **Procházet a změňte profily a předvolby**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Procházet a změnit profily a předvolby.":::

5. V okně **vybrat konfigurační profil + Předvolby** :
    1. Vyberte profil vlevo: *vývoj/testování* pro testování, výrobní *zakázka* pro produkci.
    1. Klikněte na tlačítko **Vybrat** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Procházet konfigurační profil v produkčním prostředí":::

6. Klikněte na tlačítko **Povolit**.


## <a name="enable-automanage-for-a-new-vm"></a>Povolení automanage pro nový virtuální počítač

Pokud chcete vytvořit nový virtuální počítač a povolit možnost automanage, přihlaste [se k Azure Portal](https://aka.ms/AutomanagePortal-Ignite21) .

1. V levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.

2. Ve vyhledávacím poli nad seznamem prostředků Azure Marketplace vyhledejte a vyberte obrázek, který chcete použít, a pak zvolte **vytvořit**.

> [!NOTE]
> Podívejte se na podporované verze pro [Linux distribuce](automanage-linux.md#supported-linux-distributions-and-versions) a [Windows Server](automanage-windows-server.md#supported-windows-server-versions).

3. Vyplňte kartu **základy** s podrobnostmi o vašem virtuálním počítači.

> [!NOTE]
> Ověřte [oblasti podporované](automanage-virtual-machines#supported-regions)při autosprávě.

4. Přejděte na kartu **Správa** a vyberte své prostředí pro automatické **spravování**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\VMCreate-Management-Tab.png" alt-text="Povolit automanage na kartě Správa.":::

5. Zbytek ponechte ve výchozím nastavení a potom v dolní části stránky vyberte tlačítko **Zkontrolovat a vytvořit**.

6. Když se zobrazí zpráva, že ověření proběhlo úspěšně, vyberte **vytvořit**.

## <a name="disable-automanage-for-vms"></a>Zakázat pro virtuální počítače automanage

Rychlé ukončení používání služby Azure automanage pro virtuální počítače zakázáním automatizované správy.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Zakázání automanage na virtuálním počítači.":::

1. Navštivte stránku pro automatické spravování **– osvědčené postupy pro virtuální počítače Azure** se seznamem všech vašich automatických spravovaných virtuálních počítačů.
1. Zaškrtněte políčko vedle virtuálního počítače, který chcete zakázat.
1. Klikněte na tlačítko **Zakázat automanagent** .
1. Než se odsouhlaste s **zakázáním**, důkladně si přečtěte zprávy ve výsledném okně.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste vytvořili novou skupinu prostředků, abyste si vyzkoušeli službu Azure automanage pro virtuální počítače a už ji nepotřebujete, můžete odstranit skupinu prostředků. Odstraněním skupiny se odstraní taky virtuální počítač a všechny prostředky ve skupině prostředků.

Azure automanage vytvoří výchozí skupiny prostředků pro ukládání prostředků v. Ověřte skupiny prostředků, které mají konvence pojmenování "DefaultResourceGroupRegionName" a "AzureBackupRGRegionName" pro vyčištění všech prostředků.

1. Vyberte **skupinu prostředků**.
1. Na stránce skupiny prostředků vyberte **Odstranit**.
1. Po zobrazení výzvy potvrďte název skupiny prostředků a pak vyberte **Odstranit**.


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste povolili Azure automanage pro virtuální počítače.

Zjistěte, jak můžete vytvářet a používat vlastní předvolby při povolování automatické správy na virtuálním počítači.

> [!div class="nextstepaction"]
> [Azure automanage pro virtuální počítače – vlastní konfigurační profil](virtual-machines-custom-preferences.md)
