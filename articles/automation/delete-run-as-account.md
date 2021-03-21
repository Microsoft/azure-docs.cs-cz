---
title: Odstranit Azure Automation účet Spustit jako
description: Tento článek popisuje, jak odstranit účet Spustit jako pomocí PowerShellu nebo z Azure Portal.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: 6924a9a9394d237b08db878ef910de6767ca9b39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055749"
---
# <a name="delete-an-azure-automation-run-as-account"></a>Odstranit Azure Automation účet Spustit jako

Účty Spustit jako v Azure Automation poskytují ověřování pro správu prostředků v modelu nasazení Azure Resource Manager nebo Azure Classic pomocí runbooků Automation a dalších funkcí automatizace. Tento článek popisuje, jak odstranit účet Spustit jako nebo účet Spustit jako pro Classic. Při provedení této akce se účet Automation uchová. Po odstranění účtu Spustit jako ho můžete znovu vytvořit v Azure Portal nebo pomocí zadaného skriptu PowerShellu.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Odstranění účet Spustit jako nebo Spustit jako pro Classic

1. Na webu Azure Portal otevřete účet Automation.

2. V levém podokně vyberte **účty Spustit jako** v části nastavení účtu.

3. Na stránce vlastností Účty Spustit jako vyberte účet Spustit jako nebo účet Spustit jako pro Classic, který chcete odstranit.

4. V podokně vlastnosti vybraného účtu klikněte na **Odstranit**.

   ![Odstranění účtu Spustit jako](media/delete-run-as-account/automation-account-delete-run-as.png)

5. Zatímco se účet odstraňuje, můžete průběh sledovat v nabídce v části **Oznámení**.

## <a name="next-steps"></a>Další kroky

Pokud chcete znovu vytvořit účet Spustit jako nebo účet Spustit jako pro Azure Classic, přečtěte si téma [Vytvoření účtů spustit jako](create-run-as-account.md).