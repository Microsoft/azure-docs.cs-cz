---
title: Povolení Azure Automation Change Tracking a inventáře z virtuálního počítače Azure
description: V tomto článku se dozvíte, jak povolit Change Tracking a inventář z virtuálního počítače Azure.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 9b14dcb9ce2f2426d8d1496541022602a114cb6e
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209671"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Povolení řešení Change Tracking a Inventory z virtuálního počítače Azure

Tento článek popisuje, jak můžete pomocí virtuálního počítače Azure povolit [Change Tracking a inventář](overview.md) na jiných počítačích. Pokud chcete povolit virtuální počítače Azure ve velkém měřítku, musíte povolit existující virtuální počítač pomocí Change Tracking a inventáře.

> [!NOTE]
> Při povolování Change Tracking a inventáře jsou podporovány pouze určité oblasti pro propojení Log Analyticsho pracovního prostoru a účtu Automation. Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](../index.yml) pro správu počítačů.
* [Virtuální počítač](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Povolení řešení Change Tracking a Inventory

1. V [Azure Portal](https://portal.azure.com)vyberte **virtuální počítače** nebo vyhledejte a vyberte **virtuální počítače** z domovské stránky.

2. Vyberte virtuální počítač, pro který chcete povolit Change Tracking a inventář. Virtuální počítače můžou existovat v libovolné oblasti bez ohledu na umístění vašeho účtu Automation.

3. Na stránce virtuální počítač vyberte v části **Správa konfigurace**možnost **inventarizace** nebo **sledování změn** .

4. `Microsoft.OperationalInsights/workspaces/read`Abyste mohli zjistit, jestli je virtuální počítač pro pracovní prostor povolený, musíte mít oprávnění. Další informace o dalších požadovaných oprávněních najdete v tématu [oprávnění k instalaci funkcí](../automation-role-based-access-control.md#feature-setup-permissions). Další informace o tom, jak povolit více počítačů najednou, najdete v tématu [povolení Change Tracking a inventáře z účtu Automation](enable-from-automation-account.md).

5. Vyberte pracovní prostor Log Analytics a účet Automation a kliknutím na **Povolit** povolte Change Tracking a inventář pro virtuální počítač. Dokončení instalace trvá až 15 minut.

## <a name="next-steps"></a>Další kroky

* Podrobnosti o práci s funkcí najdete v tématu [správa Change Tracking](manage-change-tracking.md) a [Správa inventáře](manage-inventory-vms.md).

* Řešení obecných problémů s funkcí najdete v tématu [řešení potíží s Change Tracking a inventářem](../troubleshoot/change-tracking.md).