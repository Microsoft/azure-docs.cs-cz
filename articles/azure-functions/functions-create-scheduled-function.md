---
title: Create a function that runs on a schedule in Azure
description: Zjistěte, jak v Azure vytvořit funkci, která se spouští na základě vámi definovaného plánu.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: quickstart
ms.date: 03/28/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 8e89c3923daab15793707ff99dbbed6deeb6a0b0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227177"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Vytvoření funkce v Azure aktivované časovačem

Learn how to use Azure Functions to create a [serverless](https://azure.microsoft.com/solutions/serverless/) function that runs based on a schedule that you define.

![Vytvoření aplikace Function App na webu Azure Portal](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Předpoklady

K provedení kroků v tomto kurzu je potřeba:

+ Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-an-azure-function-app"></a>Vytvoření aplikace Azure Function App

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Aplikace Function App byla úspěšně vytvořena.](./media/functions-create-first-azure-function/function-app-create-success.png)

Dál vytvoříte v nové aplikaci Function App funkci.

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Vytvoření funkce aktivované časovačem

1. Rozbalte aplikaci Function App a klikněte na tlačítko **+** vedle položky **Funkce**. Pokud jde o první funkci ve vaší aplikaci Function App, vyberte **Na portálu** a potom **Pokračovat**. Otherwise, go to step 3.

   ![Stručný úvod do služby Functions na webu Azure Portal](./media/functions-create-scheduled-function/function-app-quickstart-choose-portal.png)

2. Zvolte **Další šablony** a potom **Dokončit a zobrazit šablony**.

    ![Rychlý start služby Functions – výběr dalších šablon](./media/functions-create-scheduled-function/add-first-function.png)

3. In the search field, type `timer` and configure the new trigger with the settings as specified in the table below the image.

    ![Vytvořte na portálu Azure Portal funkci aktivovanou časovačem.](./media/functions-create-scheduled-function/functions-create-timer-trigger-2.png)

    | Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|
    | **Název** | Výchozí | Určuje název funkce aktivované časovačem. |
    | **Plán** | 0 \*/1 \* \* \* \* | Pole [Výraz CRON](functions-bindings-timer.md#ncrontab-expressions) v šestkové soustavě, ve kterém naplánujete spouštění funkce každou minutu. |

4. Klikněte na **Vytvořit**. Ve zvoleném jazyce se vytvoří funkce, která se bude spouštět každou minutu.

5. Podívejte se na informace o trasování zaznamenané v protokolech a ověřte provedení.

    ![Prohlížeč protokolu funkcí na webu Azure Portal.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

Teď můžete změnit plán funkce tak, aby se spouštěla jednou za hodinu (a ne jednou za minutu).

## <a name="update-the-timer-schedule"></a>Aktualizace plánu časovače

1. Rozbalte funkci a klikněte na **Integrace**. Tady se určují vstupní a výstupní vazby funkce a nastavuje plán. 

2. V poli **Plán** zadejte novou hodnotu `0 0 */1 * * *` a potom klikněte na **Uložit**.  

![Aktualizace plánu časovače funkcí na webu Azure Portal.](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

Teď máte funkci, která se spouští jednou za hodinu. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Vytvořili jste funkci, která se spouští na základě plánu. For more information about timer triggers, see [Schedule code execution with Azure Functions](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
