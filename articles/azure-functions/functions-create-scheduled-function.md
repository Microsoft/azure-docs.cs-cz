---
title: Vytvoření funkce v Azure, která běží podle plánu
description: Naučte se, jak pomocí Azure Portal vytvořit funkci, která se spouští na základě vámi definovaného plánu.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: how-to
ms.date: 04/16/2020
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 14d918cc41f49b954f5cabf48572db5df829fd10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98035185"
---
# <a name="create-a-function-in-the-azure-portal-that-runs-on-a-schedule"></a>Vytvoření funkce v Azure Portal, která se spouští podle plánu

Naučte se, jak pomocí Azure Portal vytvořit funkci, která na Azure běží bez [serveru](https://azure.microsoft.com/solutions/serverless/) na základě vámi definovaného plánu.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

+ Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-function-app"></a>Vytvoření aplikace funkcí

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Vaše nová aplikace Function App je připravena k použití. V dalším kroku vytvoříte funkci v nové aplikaci Function App.

:::image type="content" source="./media/functions-create-scheduled-function/function-app-create-success.png" alt-text="Aplikace Function App byla úspěšně vytvořena." border="true":::

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Vytvoření funkce aktivované časovačem

1. Ve vaší aplikaci Function App vyberte **funkce** a pak vyberte **+ Přidat** . 

   :::image type="content" source="./media/functions-create-scheduled-function/function-add-function.png" alt-text="Přidejte funkci do Azure Portal." border="true":::

1. Vyberte šablonu **triggeru časovače** . 

    :::image type="content" source="./media/functions-create-scheduled-function/function-select-timer-trigger.png" alt-text="Vyberte aktivační událost časovače v Azure Portal." border="true":::

1. Nakonfigurujte novou aktivační událost s nastavením uvedenými v tabulce pod obrázkem a pak vyberte **vytvořit funkci**.

    :::image type="content" source="./media/functions-create-scheduled-function/function-configure-timer-trigger.png" alt-text="Snímek obrazovky s vybranou šablonou triggeru časovače zobrazuje novou stránku funkce." border="true":::
    
    | Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|
    | **Název** | Výchozí | Určuje název funkce aktivované časovačem. |
    | **Plán** | 0 \* /1 \* \* \*\* | Pole [Výraz CRON](functions-bindings-timer.md#ncrontab-expressions) v šestkové soustavě, ve kterém naplánujete spouštění funkce každou minutu. |

## <a name="test-the-function"></a>Testování funkce

1. Ve své funkci vyberte **Code + test** a rozbalte protokoly.

    :::image type="content" source="./media/functions-create-scheduled-function/function-test-timer-trigger.png" alt-text="Otestujte Trigger časovače v Azure Portal." border="true":::

1. Ověřte provádění zobrazením informací zapsaných do protokolů.

    :::image type="content" source="./media/functions-create-scheduled-function/function-view-timer-logs.png" alt-text="Zobrazit Trigger časovače v Azure Portal." border="true":::

Teď můžete změnit plán funkce tak, aby se spouštěla jednou za hodinu (a ne jednou za minutu).

## <a name="update-the-timer-schedule"></a>Aktualizace plánu časovače

1. Ve své funkci vyberte **integrace**. Tady definujete vstupní a výstupní vazby pro svou funkci a také nakonfigurujete plán. 

1. Vyberte **časovač (myTimer)**.

    :::image type="content" source="./media/functions-create-scheduled-function/function-update-timer-schedule.png" alt-text="Aktualizujte plán časovače v Azure Portal." border="true":::

1. Aktualizujte hodnotu **plánu** na `0 0 */1 * * *` a pak vyberte **Uložit**.  

    :::image type="content" source="./media/functions-create-scheduled-function/function-edit-timer-schedule.png" alt-text="Aktualizace plánu časovače funkcí v Azure Portal." border="true":::

Teď máte funkci, která se spouští jednou za hodinu, a to za hodinu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Vytvořili jste funkci, která se spouští na základě plánu. Další informace o aktivačních událostech časovače najdete v tématu [plánování provádění kódu pomocí Azure Functions](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
