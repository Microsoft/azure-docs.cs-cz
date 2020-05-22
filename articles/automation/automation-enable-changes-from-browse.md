---
title: Povolit Azure Automation Change Tracking a inventář z Azure Portal
description: V tomto článku se dozvíte, jak povolit funkci Change Tracking a inventáře z Azure Portal.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: b53c63fc1a14c456e0bca0725ca29b311301ce66
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749078"
---
# <a name="enable-change-tracking-and-inventory-from-the-azure-portal"></a>Povolit Change Tracking a inventář z Azure Portal

Tento článek popisuje, jak můžete povolit funkci [Change Tracking a inventáře](change-tracking.md) pro virtuální počítače procházením Azure Portal. Pokud chcete povolit virtuální počítače Azure ve velkém měřítku, musíte povolit existující virtuální počítač pomocí Change Tracking a inventáře. 

Počet skupin prostředků, které můžete použít pro správu virtuálních počítačů, je omezený [Správce prostředků limity nasazení](../azure-resource-manager/templates/cross-resource-group-deployment.md). Správce prostředků nasazení, nemusíte se zaměňovat s nasazeními aktualizací, jsou omezená na pět skupin prostředků na nasazení. Dvě z těchto skupin prostředků jsou rezervované pro konfiguraci Log Analyticsho pracovního prostoru, účtu Automation a souvisejících prostředků. Tím se dokončí tři skupiny prostředků pro výběr správy pomocí Change Tracking a inventáře. Tento limit se vztahuje pouze na souběžné nastavení, nikoli na počet skupin prostředků, které lze spravovat pomocí funkce automatizace.

> [!NOTE]
> Při povolování Change Tracking a inventáře jsou podporovány pouze určité oblasti pro propojení Log Analyticsho pracovního prostoru a účtu Automation. Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](how-to/region-mappings.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-offering-get-started.md) pro správu počítačů.
* [Virtuální počítač](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure na adrese https://portal.azure.com .

## <a name="enable-change-tracking-and-inventory"></a>Povolení řešení Change Tracking a Inventory

1. V Azure Portal přejděte na **virtuální počítače**.

2. Pomocí zaškrtávacích políček vyberte virtuální počítače, které chcete přidat do Change Tracking a inventáře. Můžete přidávat počítače až do tří různých skupin prostředků najednou. Virtuální počítače Azure můžou existovat v jakékoli oblasti bez ohledu na umístění vašeho účtu Automation.

    ![Seznam virtuálních počítačů](media/automation-enable-changes-from-browse/vmlist.png)

    > [!TIP]
    > Pomocí ovládacích prvků filtru vyberte virtuální počítače z různých předplatných, umístění a skupin prostředků. Kliknutím na zaškrtávací políčko v horní části můžete vybrat všechny virtuální počítače v seznamu.

3. V části **Správa konfigurace**vyberte možnost **sledování změn** nebo **inventarizace** .

4. Seznam virtuálních počítačů je filtrovaný tak, aby zobrazoval pouze virtuální počítače, které jsou ve stejném předplatném a umístění. Pokud jsou vaše virtuální počítače ve více než třech skupinách prostředků, vybere se první tři skupiny prostředků.

5. Ve výchozím nastavení je vybraný existující Log Analytics pracovní prostor a účet Automation. Pokud chcete použít jiný Log Analytics pracovní prostor a účet Automation, klikněte na tlačítko **vlastní** a vyberte je na stránce vlastní konfigurace. Když zvolíte pracovní prostor Log Analytics, provedeme kontrolu, která určí, jestli je propojený s účtem Automation. Pokud se najde propojený účet Automation, zobrazí se následující obrazovka. Až budete hotovi, klikněte na **OK**.

    ![Vyberte pracovní prostor a účet.](media/automation-enable-changes-from-browse/selectworkspaceandaccount.png)

6. Pokud vybraný pracovní prostor není propojený s účtem Automation, zobrazí se následující obrazovka. Vyberte účet Automation a po dokončení klikněte na **OK** .

    ![Žádný pracovní prostor](media/automation-enable-changes-from-browse/no-workspace.png)

7. Zrušte zaškrtnutí políčka vedle libovolného virtuálního počítače, který nechcete povolit. Virtuální počítače, které se nedají povolit, už nejsou vybrané.

8. Kliknutím na **Povolit** povolíte funkci, kterou jste vybrali. Dokončení instalace trvá až 15 minut.

## <a name="next-steps"></a>Další kroky

* [Správa Change Tracking a inventáře](change-tracking-file-contents.md)
* [Práce s konfiguracemi oboru pro Change Tracking a inventář](automation-scope-configurations-change-tracking.md)
* [Identifikace softwaru na virtuálním počítači](automation-tutorial-installed-software.md)
* [Zrušení propojení pracovního prostoru s účtem služby Automation pro Change Tracking a inventář](automation-unlink-workspace-change-tracking.md)
* [Odebrání virtuálních počítačů z Change Tracking a inventáře](automation-remove-vms-from-change-tracking.md)
* [Řešení potíží se změnami na virtuálním počítači Azure](automation-tutorial-troubleshoot-changes.md)
* [Řešení problémů s Change Tracking a inventářem](troubleshoot/change-tracking.md)