---
title: Omezení rozsahu nasazení Update Management Azure Automation
description: V tomto článku se dozvíte, jak používat konfigurace oboru k omezení rozsahu nasazení Update Management.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: aafd284122ca61ba2b668186095b88003be2775c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87449971"
---
# <a name="limit-update-management-deployment-scope"></a>Omezení rozsahu nasazení Update Management

Tento článek popisuje, jak pracovat s konfiguracemi oboru při použití funkce [Update Management](update-mgmt-overview.md) k nasazení aktualizací a oprav do vašich virtuálních počítačů. Další informace najdete v tématu [cílení řešení monitorování v Azure monitor (Preview)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>O konfiguracích oboru

Konfigurace oboru je skupina jednoho nebo několika uložených hledání (dotazů) používaných k omezení rozsahu Update Management na konkrétní počítače. Konfigurace oboru se používá v pracovním prostoru Log Analytics pro cílení na počítače, které chcete povolit. Když přidáte počítač pro příjem aktualizací z Update Management, počítač se přidá i do uloženého hledání v pracovním prostoru.

## <a name="set-the-scope-limit"></a>Nastavení omezení rozsahu

Omezení rozsahu nasazení Update Management:

1. V účtu Automation vyberte v části **související prostředky**možnost **propojený pracovní prostor** .

2. Vyberte **Přejít k pracovnímu prostoru**.

3. V části **zdroje dat pracovního prostoru**vyberte **Konfigurace oboru (Preview)** .

4. Vyberte tři tečky napravo od  `MicrosoftDefaultScopeConfig-Updates` Konfigurace oboru a vyberte **Upravit**.

5. V podokně úpravy rozbalte položku **Vybrat skupiny počítačů**. V podokně skupiny počítačů se zobrazí uložená hledání, která slouží k vytvoření konfigurace oboru. Uložené hledání, které používá Update Management, je:

    |Name     |Kategorie  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | Aktualizace        | Updates__MicrosoftDefaultComputerGroup         |

6. Vyberte uložené hledání, které chcete zobrazit, a upravte dotaz, který jste použili k naplnění skupiny. Následující obrázek znázorňuje dotaz a jeho výsledky:

    [![Uložená hledání](./media/update-mgmt-scope-configuration/logsearch.png)](./media/update-mgmt-scope-configuration/logsearch-expanded.png#lightbox)

## <a name="next-steps"></a>Další kroky

Můžete zadat [dotaz na protokoly Azure monitor](update-mgmt-query-logs.md) , abyste mohli analyzovat posouzení aktualizací, nasazení a další související úlohy správy. Obsahuje předem definované dotazy, které vám pomůžou začít.
