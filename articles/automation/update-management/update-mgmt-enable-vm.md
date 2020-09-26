---
title: Povolení Azure Automation Update Management z virtuálního počítače Azure
description: V tomto článku se dozvíte, jak povolit Update Management z virtuálního počítače Azure.
services: automation
ms.date: 09/16/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: dead4649635bebd9f73f79937dea1d24565f273e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327784"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Povolení Update Managementu z virtuálního počítače Azure

Tento článek popisuje, jak můžete pomocí virtuálního počítače Azure povolit funkci [Update Management](update-mgmt-overview.md) v jiných počítačích. Pokud chcete povolit virtuální počítače Azure ve velkém měřítku, musíte povolit existující virtuální počítač pomocí Update Management.

> [!NOTE]
> Při povolování Update Management jsou podporovány pouze určité oblasti pro propojení pracovního prostoru Log Analytics a účtu Automation. Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](../index.yml) pro správu počítačů.
* [Virtuální počítač](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="enable-the-feature-for-deployment"></a>Povolit funkci pro nasazení

1. V [Azure Portal](https://portal.azure.com)vyberte **virtuální počítače** nebo vyhledejte a vyberte **virtuální počítače** z domovské stránky.

2. Vyberte virtuální počítač, pro který chcete povolit Update Management. Virtuální počítače můžou existovat v libovolné oblasti bez ohledu na umístění vašeho účtu Automation. Vy

3. Na stránce virtuální počítač v části **operace**vyberte **Host + aktualizace hostitelů**.

    ![V levém podokně vyberte aktualizace hosta + hostitel.](media/update-mgmt-enable-vm/select-guest-and-os-updates.png)

4. `Microsoft.OperationalInsights/workspaces/read`Abyste mohli zjistit, jestli je virtuální počítač pro pracovní prostor povolený, musíte mít oprávnění. Další informace o dalších požadovaných oprávněních najdete v tématu [oprávnění potřebná k povolení počítačů](../automation-role-based-access-control.md#feature-setup-permissions). Další informace o tom, jak povolit více počítačů najednou, najdete v tématu [povolení Update Management z účtu Automation](update-mgmt-enable-automation-account.md).

5. Na stránce povolit Update Management vyberte pracovní prostor Log Analytics a účet Automation a kliknutím na **Povolit** povolte Update Management. Po povolení Update Management může trvat přibližně 15 minut, než si můžete prohlédnout posouzení aktualizace z virtuálního počítače.

    ![Povolení řešení Update Management](media/update-mgmt-enable-vm/enable-update-management.png)

## <a name="next-steps"></a>Další kroky

* Pokud chcete použít Update Management pro virtuální počítače, přečtěte si téma [Správa aktualizací a oprav pro virtuální počítače Azure](update-mgmt-manage-updates-for-vm.md).

* Pokud chcete řešit obecné chyby Update Management, přečtěte si téma [řešení potíží s Update Management](../troubleshoot/update-management.md).
