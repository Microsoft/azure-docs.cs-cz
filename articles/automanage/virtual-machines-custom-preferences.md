---
title: Vytvoření vlastní preference v Azure automanage pro virtuální počítače
description: Naučte se, jak upravit konfiguraci prostředí v Azure, a nastavit vlastní předvolby.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 584a3503bf736fcf727a169611e6c79e0c374c90
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101647925"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>Vytvoření vlastní preference v Azure automanage pro virtuální počítače

Osvědčené postupy pro virtuální počítače v Azure jsou ve výchozím prostředí, které je možné v případě potřeby upravit. V tomto článku se dozvíte, jak můžete nastavit vlastní předvolby při povolování funkce automanagement na novém nebo existujícím virtuálním počítači.

V současné době podporujeme přizpůsobení [Azure Backup](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) a [antimalwaru Microsoftu](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration).


> [!NOTE]
> Pokud je povolená možnost automanage, nemůžete změnit prostředí ani preference na VIRTUÁLNÍm počítači. Pro tento virtuální počítač budete muset zakázat možnost automatického spravování a pak znovu povolit správu pomocí požadovaných konfiguračních prostředí a předvoleb.


## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) před tím, než začnete.

> [!NOTE]
> Bezplatné zkušební účty nemají přístup k virtuálním počítačům použitým v tomto kurzu. Upgradujte prosím na předplatné s průběžnými platbami.

> [!IMPORTANT]
> K povolení funkce automanage můžete použít následující oprávnění Azure RBAC: roli **vlastníka** nebo **přispěvatele** spolu s rolemi **Správce přístupu uživatelů** .


## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Povolení automanage pro virtuální počítače na existujícím virtuálním počítači

1. Na panelu hledání vyhledejte a vyberte možnost **automanage – osvědčené postupy pro Azure Machine**.

2. Vyberte **Povolit na stávajícím virtuálním počítači**.

3. V okně **Vybrat počítače** :
    1. Vyfiltrujte seznam virtuálních počítačů podle vašeho **předplatného** a **skupiny prostředků**.
    1. Zaškrtněte políčko u každého virtuálního počítače, který chcete připojit.
    1. Klikněte na tlačítko **Vybrat** .

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="Vyberte existující virtuální počítač ze seznamu dostupných virtuálních počítačů.":::

    > [!NOTE]
    > Kliknutím na **Zobrazit neoprávněné počítače** zobrazíte seznam nepodporovaných počítačů a důvod. 

4. V části **Konfigurace** klikněte na **Porovnat prostředí**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="Porovnejte prostředí.":::

5. V okně **Podrobnosti prostředí** vyberte prostředí z rozevírací nabídky: *vývoj/testování* pro testování, *prod* pro produkci a klikněte na **OK** .

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="Projděte si produkční prostředí.":::

6. Po výběru prostředí můžete vybrat **Předvolby konfigurace**. Ve výchozím nastavení se použije předvolba Azure Best Practices. Tato předvolba obsahuje doporučená nastavení pro jednotlivé služby. Upravte tato nastavení tak, že vytvoříte vlastní preference: 
    1. Klikněte na **vytvořit nové předvolby**.
    1. V okně **vytvořit předvolby konfigurace** Vyplňte kartu základy:
        1. Předplatné
        1. Skupina prostředků
        1. Název předvolby
        1. Oblast

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="Vyplňte předvolby konfigurace.":::

7. Upravte požadované předvolby konfigurace.
        
    > [!NOTE]
    > Při změně konfigurace prostředí budou povoleny pouze úpravy, které se vejdou v rámci osvědčených postupů horní a dolní meze.

8. Zkontrolujte podrobnosti o konfiguraci.
9. Klikněte na tlačítko **Vytvořit**.

10. Klikněte na tlačítko **Povolit**.


## <a name="disable-automanage-for-vms"></a>Zakázat pro virtuální počítače automanage

Rychlé ukončení používání služby Azure automanage pro virtuální počítače zakázáním automatizované správy.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="Zakázání automanage na virtuálním počítači.":::

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

Získejte odpovědi na nejčastější dotazy v NEJČASTĚJŠÍch dotazech. 

> [!div class="nextstepaction"]
> [Nejčastější dotazy](faq.md)