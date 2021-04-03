---
title: Omezení Azure Automation Change Tracking a rozsahu nasazení inventáře
description: Tento článek obsahuje informace o tom, jak pracovat s konfiguracemi oboru pro omezení rozsahu Change Tracking a nasazení inventáře.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 6eb9a20920f0a340491459f0875fc85b90dfa193
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92209678"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Omezení rozsahu nasazení Change Tracking a inventáře

Tento článek popisuje, jak pracovat s konfiguracemi oboru při použití funkce [Change Tracking a inventáře](overview.md) k nasazení změn do virtuálních počítačů. Další informace najdete v tématu [cílení řešení monitorování v Azure monitor (Preview)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>O konfiguracích oboru

Konfigurace oboru je skupina jednoho nebo několika uložených hledání (dotazů) používaných k omezení rozsahu Change Tracking a inventáře na konkrétní počítače. Konfigurace oboru se používá v pracovním prostoru Log Analytics pro cílení na počítače, které chcete povolit. Když přidáte počítač ke změnám z funkce, počítač je také přidán do uloženého hledání v pracovním prostoru.

## <a name="set-the-scope-limit"></a>Nastavení omezení rozsahu

Omezení rozsahu pro nasazení Change Tracking a inventáře:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. V Azure Portal vyberte **všechny služby**. V seznamu prostředků zadejte **Automation**. Po zahájení psaní seznam vyfiltruje návrhy na základě vašeho vstupu. Vyberte **Účty Automation**.

3. V seznamu účtů Automation vyberte účet, který jste zvolili při povolování Change Tracking a inventáře.

4. V účtu Automation vyberte v části **související prostředky** možnost **propojený pracovní prostor** .

5. Klikněte na **Přejít k pracovnímu prostoru**.

6. V části **zdroje dat pracovního prostoru** vyberte **Konfigurace oboru (Preview)** .

7. Vyberte tři tečky napravo od  `MicrosoftDefaultScopeConfig-ChangeTracking` Konfigurace oboru a klikněte na **Upravit**.

8. V podokně úpravy vyberte **Vybrat skupiny počítačů**. V podokně skupiny počítačů se zobrazí uložená hledání, která slouží k vytvoření konfigurace oboru. Uložené výsledky hledání, které používá Change Tracking a inventář:

    |Name     |Kategorie  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  Sledování změn ve       | ChangeTracking__MicrosoftDefaultComputerGroup        |

9. Vyberte uložené hledání, které chcete zobrazit, a upravte dotaz, který jste použili k naplnění skupiny. Následující obrázek znázorňuje dotaz a jeho výsledky:

    ![Uložená hledání](media/manage-scope-configurations/logsearch.png)

## <a name="next-steps"></a>Další kroky

* Pokud chcete pracovat s Change Tracking a inventářem, přečtěte si téma [správa Change Tracking a inventáře](manage-change-tracking.md).
* Řešení obecných potíží s funkcemi najdete v tématu [řešení potíží s Change Tracking a inventářem](../troubleshoot/change-tracking.md).
