---
title: Povolení Azure Automation Change Tracking a inventáře z virtuálního počítače Azure
description: V tomto článku se dozvíte, jak povolit Change Tracking a inventář z virtuálního počítače Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4eccdef6bd3f2bfcd0eced8281f7b998536f22a9
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749183"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Povolení Change Tracking a inventáře z virtuálního počítače Azure

Tento článek popisuje, jak můžete pomocí virtuálního počítače Azure povolit funkci [Change Tracking a inventáře](change-tracking.md) na jiných počítačích. Pokud chcete povolit virtuální počítače Azure ve velkém měřítku, musíte povolit existující virtuální počítač pomocí Change Tracking a inventáře. 

> [!NOTE]
> Při povolování Change Tracking a inventáře jsou podporovány pouze určité oblasti pro propojení Log Analyticsho pracovního prostoru a účtu Automation. Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](how-to/region-mappings.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-offering-get-started.md) pro správu počítačů.
* [Virtuální počítač](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Povolení řešení Change Tracking a Inventory

1. V [Azure Portal](https://portal.azure.com)vyberte **virtuální počítače** nebo vyhledejte a vyberte **virtuální počítače** z domovské stránky.

2. Vyberte virtuální počítač, pro který chcete povolit Change Tracking a inventář. Virtuální počítače můžou existovat v libovolné oblasti bez ohledu na umístění vašeho účtu Automation.

3. Na stránce virtuální počítač vyberte v části **Správa konfigurace**možnost **inventarizace** nebo **sledování změn** .

4. `Microsoft.OperationalInsights/workspaces/read`Abyste mohli zjistit, jestli je virtuální počítač pro pracovní prostor povolený, musíte mít oprávnění. Další informace o dalších požadovaných oprávněních najdete v tématu [oprávnění k instalaci funkcí](automation-role-based-access-control.md#feature-setup-permissions). Další informace o tom, jak povolit více počítačů najednou, najdete v tématu [povolení Change Tracking a inventáře z účtu Automation](automation-enable-changes-from-auto-acct.md).

5. Vyberte pracovní prostor Log Analytics a účet Automation a kliknutím na **Povolit** povolte Change Tracking a inventář pro virtuální počítač. Dokončení instalace trvá až 15 minut. 

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Ověřit konfiguraci oboru

Change Tracking a inventář používá v rámci pracovního prostoru konfiguraci oboru pro cílení na počítače, které mají funkci povolit. Konfigurace oboru je skupina jednoho nebo více uložených hledání, která slouží k omezení rozsahu funkce na konkrétní počítače. Další informace najdete v tématu [práce s konfiguracemi oboru pro Change Tracking a inventář](automation-scope-configurations-change-tracking.md).

## <a name="next-steps"></a>Další kroky

* [Správa Change Tracking a inventáře](change-tracking-file-contents.md)
* [Práce s konfiguracemi oboru pro Change Tracking a inventář](automation-scope-configurations-change-tracking.md)
* [Identifikace softwaru na virtuálním počítači](automation-tutorial-installed-software.md)
* [Zrušení propojení pracovního prostoru s účtem služby Automation pro Change Tracking a inventář](automation-unlink-workspace-change-tracking.md)
* [Odebrání virtuálních počítačů z Change Tracking a inventáře](automation-remove-vms-from-change-tracking.md)
* [Řešení potíží se změnami na virtuálním počítači Azure](automation-tutorial-troubleshoot-changes.md)
* [Řešení problémů s Change Tracking a inventářem](troubleshoot/change-tracking.md)
