---
title: Omezení rozsahu nasazení Update Management Azure Automation
description: V tomto článku se dozvíte, jak používat konfigurace oboru k omezení rozsahu nasazení Update Management.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 76063c479950d12985d5f3f52393f9bb0d5ecd8d
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222217"
---
# <a name="limit-update-management-deployment-scope"></a>Omezení rozsahu nasazení Update Management

Tento článek popisuje, jak pracovat s konfiguracemi oboru při použití funkce [Update Management](overview.md) k nasazení aktualizací a oprav do vašich virtuálních počítačů. Další informace najdete v tématu [cílení řešení monitorování v Azure monitor (Preview)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>O konfiguracích oboru

Konfigurace oboru je skupina jednoho nebo několika uložených hledání (dotazů) používaných k omezení rozsahu Update Management na konkrétní počítače. Konfigurace oboru se používá v pracovním prostoru Log Analytics pro cílení na počítače, které chcete povolit. Když přidáte počítač pro příjem aktualizací z Update Management, počítač se přidá i do uloženého hledání v pracovním prostoru.

## <a name="set-the-scope-limit"></a>Nastavení omezení rozsahu

Omezení rozsahu nasazení Update Management:

1. V účtu Automation vyberte v části **související prostředky**možnost **propojený pracovní prostor** .

2. Vyberte **Přejít k pracovnímu prostoru**.

3. V části **zdroje dat pracovního prostoru**vyberte **Konfigurace oboru (Preview)** .

4. Vyberte tři tečky napravo od  `MicrosoftDefaultScopeConfig-Updates` Konfigurace oboru a vyberte **Upravit**.

5. V podokně úpravy rozbalte položku **Vybrat skupiny počítačů**. V podokně skupiny počítačů se zobrazí uložená hledání, která slouží k vytvoření konfigurace oboru. Uložené hledání, které používá Update Management, je:

    |Název     |Kategorie  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | Aktualizace        | Updates__MicrosoftDefaultComputerGroup         |

6. Vyberte uložené hledání, které chcete zobrazit, a upravte dotaz, který jste použili k naplnění skupiny. Následující obrázek znázorňuje dotaz a jeho výsledky:

    [![Uložená hledání](./media/scope-configuration/logsearch.png)](./media/scope-configuration/logsearch-expanded.png#lightbox)

## <a name="next-steps"></a>Další kroky

Můžete zadat [dotaz na protokoly Azure monitor](query-logs.md) , abyste mohli analyzovat posouzení aktualizací, nasazení a další související úlohy správy. Obsahuje předem definované dotazy, které vám pomůžou začít.
