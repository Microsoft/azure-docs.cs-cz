---
title: Povolení Azure Automation Update Management z virtuálního počítače Azure
description: V tomto článku se dozvíte, jak povolit Update Management z virtuálního počítače Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: cab114e50852f293a3d1caf5bdc9a341f75f2557
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743966"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Povolení Update Management z virtuálního počítače Azure

Tento článek popisuje, jak můžete pomocí virtuálního počítače Azure povolit funkci [Update Management](automation-update-management.md) v jiných počítačích. Pokud chcete povolit virtuální počítače Azure ve velkém měřítku, musíte povolit existující virtuální počítač pomocí Update Management. 

> [!NOTE]
> Při povolování Update Management jsou podporovány pouze určité oblasti pro propojení pracovního prostoru Log Analytics a účtu Automation. Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](how-to/region-mappings.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-offering-get-started.md) pro správu počítačů.
* [Virtuální počítač](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="enable-update-management"></a>Povolení řešení Update Management

1. V [Azure Portal](https://portal.azure.com)vyberte **virtuální počítače** nebo vyhledejte a vyberte **virtuální počítače** z domovské stránky.

2. Vyberte virtuální počítač, pro který chcete povolit Update Management. Virtuální počítače můžou existovat v libovolné oblasti bez ohledu na umístění vašeho účtu Automation. Vy

3. Na stránce virtuální počítač v části **operace**vyberte **Update Management**.

4. `Microsoft.OperationalInsights/workspaces/read`Abyste mohli zjistit, jestli je virtuální počítač pro pracovní prostor povolený, musíte mít oprávnění. Další informace o dalších požadovaných oprávněních najdete v tématu [oprávnění potřebná k povolení počítačů](automation-role-based-access-control.md#feature-setup-permissions). Další informace o tom, jak povolit více počítačů najednou, najdete v tématu [povolení Update Management z účtu Automation](automation-onboard-solutions-from-automation-account.md).

5. Vyberte pracovní prostor Log Analytics a účet Automation a kliknutím na **Povolit** povolte Update Management. Dokončení instalace trvá až 15 minut. 

    ![Povolení řešení Update Management](media/automation-tutorial-update-management/manageupdates-update-enable.png)

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Ověřit konfiguraci oboru

Update Management používá v rámci pracovního prostoru konfiguraci oboru pro cílení na počítače, které mají být pro funkci povoleny. Konfigurace oboru je skupina jednoho nebo více uložených hledání, která slouží k omezení rozsahu funkce na konkrétní počítače. Další informace najdete v tématu [práce s konfiguracemi oboru pro Update Management](automation-scope-configurations-update-management.md).

## <a name="next-steps"></a>Další kroky

* Pokud chcete použít Update Management pro virtuální počítače, přečtěte si téma [Správa aktualizací a oprav pro virtuální počítače Azure](automation-tutorial-update-management.md).
* Informace o konfiguracích oboru najdete v tématu [práce s konfiguracemi oboru pro Update Management](automation-scope-configurations-update-management.md).
* Pokud už nepotřebujete pracovní prostor Log Analytics, přečtěte si pokyny v tématu [zrušení propojení pracovního prostoru s účtem Automation pro Update Management](automation-unlink-workspace-update-management.md).
* Postup odstranění virtuálních počítačů z Update Management najdete v tématu [Odebrání virtuálních počítačů z Update Management](automation-remove-vms-from-update-management.md).
* Pokud chcete řešit obecné chyby Update Management, přečtěte si téma [řešení potíží s Update Management](troubleshoot/update-management.md).
* Informace o řešení problémů s agentem Windows Update najdete v tématu řešení potíží s [agentem pro Windows Update](troubleshoot/update-agent-issues.md).
* Informace o řešení problémů s agentem aktualizací pro Linux najdete v tématu[řešení potíží s agentem aktualizace pro Linux](troubleshoot/update-agent-issues-linux.md).
