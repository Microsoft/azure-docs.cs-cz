---
title: Omezení Azure Automation Change Tracking a rozsahu nasazení inventáře
description: Tento článek obsahuje informace o tom, jak pracovat s konfiguracemi oboru pro omezení rozsahu Change Tracking a nasazení inventáře.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 353f29d9b4c6599226619d40e4378e21618bcad4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185904"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Omezení rozsahu nasazení Change Tracking a inventáře

Tento článek popisuje, jak pracovat s konfiguracemi oboru při použití funkce [Change Tracking a inventáře](change-tracking.md) k nasazení změn do virtuálních počítačů. Další informace najdete v tématu [cílení řešení monitorování v Azure monitor (Preview)](../azure-monitor/insights/solution-targeting.md). 

## <a name="about-scope-configurations"></a>O konfiguracích oboru

Konfigurace oboru je skupina jednoho nebo několika uložených hledání (dotazů) používaných k omezení rozsahu Change Tracking a inventáře na konkrétní počítače. Konfigurace oboru se používá v pracovním prostoru Log Analytics pro cílení na počítače, které chcete povolit. Když přidáte počítač ke změnám z funkce, počítač je také přidán do uloženého hledání v pracovním prostoru.

## <a name="set-the-scope-limit"></a>Nastavení omezení rozsahu

Omezení rozsahu pro nasazení Change Tracking a inventáře:

1. V účtu Automation vyberte v části **související prostředky**možnost **propojený pracovní prostor** .

2. Klikněte na **Přejít k pracovnímu prostoru**.

3. V části **zdroje dat pracovního prostoru**vyberte **Konfigurace oboru (Preview)** .

4. Vyberte tři tečky napravo od  `MicrosoftDefaultScopeConfig-ChangeTracking` Konfigurace oboru a klikněte na **Upravit**. 

5. V podokně úpravy vyberte **Vybrat skupiny počítačů**. V podokně skupiny počítačů se zobrazí uložená hledání, která slouží k vytvoření konfigurace oboru. Uložené výsledky hledání, které používá Change Tracking a inventář:

    |Name     |Kategorie  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  Sledování změn ve       | ChangeTracking__MicrosoftDefaultComputerGroup        |

6. Vyberte uložené hledání, které chcete zobrazit, a upravte dotaz, který jste použili k naplnění skupiny. Následující obrázek znázorňuje dotaz a jeho výsledky:

    ![Uložená hledání](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>Další kroky

* Pokud chcete pracovat s Change Tracking a inventářem, přečtěte si téma [správa Change Tracking a inventáře](change-tracking-file-contents.md).
* Řešení obecných potíží s funkcemi najdete v tématu [řešení potíží s Change Tracking a inventářem](troubleshoot/change-tracking.md).
