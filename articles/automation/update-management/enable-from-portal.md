---
title: Povolit Azure Automation Update Management z Azure Portal
description: V tomto článku se dozvíte, jak povolit Update Management z Azure Portal.
services: automation
ms.subservice: update-management
ms.date: 01/07/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 089c5fea6ac4a6fc4fb25af2d631335ef51cf4cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99054902"
---
# <a name="enable-update-management-from-the-azure-portal"></a>Povolení Update Managementu z webu Azure Portal

Tento článek popisuje, jak můžete povolit funkci [Update Management](overview.md) pro virtuální počítače procházením Azure Portal. Pokud chcete povolit virtuální počítače Azure ve velkém měřítku, musíte povolit existující virtuální počítač Azure pomocí Update Management.

Počet skupin prostředků, které můžete použít pro správu virtuálních počítačů, je omezený [Správce prostředků limity nasazení](../../azure-resource-manager/templates/deploy-to-resource-group.md). Správce prostředků nasazení, nemusíte se zaměňovat s nasazeními aktualizací, jsou omezená na pět skupin prostředků na nasazení. Dvě z těchto skupin prostředků jsou rezervované pro konfiguraci Log Analyticsho pracovního prostoru, účtu Automation a souvisejících prostředků. Tím se dokončí tři skupiny prostředků pro výběr správy pomocí Update Management. Tento limit se vztahuje pouze na souběžné nastavení, nikoli na počet skupin prostředků, které lze spravovat pomocí funkce automatizace.

> [!NOTE]
> Při povolování Update Management jsou podporovány pouze určité oblasti pro propojení pracovního prostoru Log Analytics a účtu Automation. Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](../automation-security-overview.md) pro správu počítačů.
* [Virtuální počítač](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure na adrese https://portal.azure.com .

## <a name="enable-update-management"></a>Povolení řešení Update Management

1. V Azure Portal přejděte na **virtuální počítače**.

2. Na stránce **virtuální počítače** pomocí zaškrtávacích políček vyberte virtuální počítače, které chcete přidat do Update Management. Můžete přidávat počítače až do tří různých skupin prostředků najednou. Virtuální počítače Azure můžou existovat v jakékoli oblasti bez ohledu na umístění vašeho účtu Automation.

    ![Seznam virtuálních počítačů](media/enable-from-portal/vmlist.png)

    > [!TIP]
    > Pomocí ovládacích prvků filtru vyberte virtuální počítače z různých předplatných, umístění a skupin prostředků. Kliknutím na zaškrtávací políčko v horní části můžete vybrat všechny virtuální počítače v seznamu.

    [![Povolit Update Management](./media/enable-from-portal/onboard-feature.png)](./media/enable-from-portal/onboard-feature-expanded.png#lightbox)

3. Vyberte **služby** a pro funkci Update Management vyberte **Update Management** .

4. Seznam virtuálních počítačů je filtrovaný tak, aby zobrazoval pouze virtuální počítače, které jsou ve stejném předplatném a umístění. Pokud jsou vaše virtuální počítače ve více než třech skupinách prostředků, vybere se první tři skupiny prostředků.

5. Ve výchozím nastavení je vybraný existující Log Analytics pracovní prostor a účet Automation. Pokud chcete použít jiný Log Analytics pracovní prostor a účet Automation, vyberte možnost **vlastní** a vyberte si je na stránce vlastní konfigurace. Když zvolíte pracovní prostor Log Analytics, provedeme kontrolu, která určí, jestli je propojený s účtem Automation. Pokud se najde propojený účet Automation, zobrazí se následující obrazovka. Až budete hotovi, vyberte **OK**.

    [![Vyberte pracovní prostor a účet.](./media/enable-from-portal/select-workspace-and-account.png)](./media/enable-from-portal/select-workspace-and-account-expanded.png#lightbox)

6. Pokud vybraný pracovní prostor není propojený s účtem Automation, zobrazí se následující obrazovka. Vyberte účet Automation a po dokončení vyberte **OK** .

    ![Žádný pracovní prostor](media/enable-from-portal/no-workspace.png)

7. Zrušte výběr všech virtuálních počítačů, které nechcete povolit. Virtuální počítače, které se nedají povolit, už nejsou vybrané.

8. Tuto funkci povolíte výběrem **Povolit** . Po povolení Update Management může trvat přibližně 15 minut, než se vám zobrazí posouzení aktualizace.

## <a name="next-steps"></a>Další kroky

* Pokud chcete použít Update Management pro virtuální počítače, přečtěte si téma [Správa aktualizací a oprav pro vaše virtuální](manage-updates-for-vm.md)počítače.
* Pokud chcete řešit obecné chyby Update Management, přečtěte si téma [řešení potíží s Update Management](../troubleshoot/update-management.md).
* Informace o řešení problémů s agentem Windows Update najdete v tématu řešení potíží s [agentem pro Windows Update](../troubleshoot/update-agent-issues.md).
* Informace o řešení problémů s agentem aktualizací pro Linux najdete v tématu [řešení potíží s agentem aktualizace pro Linux](../troubleshoot/update-agent-issues-linux.md).