---
title: Vytvoření první funkce na webu Azure Portal
description: Naučíte se postup vytvoření první funkce Azure Function pro provádění pomocí webu Azure Portal bez serveru.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: a96d2ede80b4c57e7b85048379a4bfb66cacfd52
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80754844"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Vytvoření první funkce na webu Azure Portal

Azure Functions umožňuje spuštění kódu v prostředí bez serveru, aniž by bylo nutné nejprve vytvořit virtuální počítač nebo publikovat webovou aplikaci. V tomto článku se naučíte, jak pomocí Azure Functions vytvořit funkci "Hello World" s protokolem HTTP spuštěnou v Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Pokud jste vývojář jazyka C#, zvažte [Vytvoření první funkce v aplikaci Visual Studio 2019](functions-create-your-first-function-visual-studio.md) místo na portálu. 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

## <a name="create-a-function-app"></a>Vytvoření Function App

K hostování provádění funkcí musíte mít aplikaci Function App. Aplikace Function App umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování, škálování a sdílení prostředků.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Dále vytvořte funkci v nové aplikaci Function App.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Vytvoření funkce aktivované protokolem HTTP

1. Rozbalte novou **+** aplikaci Function App, vyberte tlačítko vedle možnosti **funkce**, zvolte možnost **v portálu**a pak vyberte **pokračovat**.

    ![Rychlé spuštění funkcí pro výběr platformy.](./media/functions-create-first-azure-function/function-app-quickstart-choose-portal.png)

1. Zvolte **Webhook + rozhraní API**a pak vyberte **vytvořit**.

    ![Stručný úvod do služby Functions na webu Azure Portal.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

   Na základě šablony funkce aktivované protokolem HTTP pro určitý jazyk se vytvoří funkce.

Novou funkci můžete spustit odesláním požadavku HTTP.

## <a name="test-the-function"></a>Testování funkce

1. V nové funkci vyberte **</> získat adresu URL funkce** v pravém horním rohu. 

1. V dialogovém okně **získat adresu URL funkce** v rozevíracím seznamu vyberte **výchozí (klíč funkce)** a pak vyberte **Kopírovat**. 

    ![Kopírování adresy URL funkce z webu Azure Portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Vložte adresu URL funkce do panelu Adresa vašeho prohlížeče. Na konec této adresy URL `&name=<your_name>` přidejte hodnotu řetězce dotazu a stisknutím klávesy ENTER spusťte požadavek. 

    Následující příklad ukazuje odpověď v prohlížeči:

    ![Odezva funkce v prohlížeči.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    Adresa URL požadavku obsahuje klíč, který je ve výchozím nastavení nezbytný pro přístup k funkci přes protokol HTTP.

1. Při spuštění funkce se do protokolů zaznamenávají informace o trasování. Chcete-li zobrazit výstup trasování z předchozího spuštění, vraťte se na funkci na portálu a výběrem šipky v dolní části obrazovky rozbalte položku **protokoly**.

   ![Prohlížeč protokolu funkcí na webu Azure Portal.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

