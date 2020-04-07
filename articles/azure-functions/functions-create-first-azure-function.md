---
title: Vytvoření první funkce na webu Azure Portal
description: Naučíte se postup vytvoření první funkce Azure Function pro provádění pomocí webu Azure Portal bez serveru.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: a96d2ede80b4c57e7b85048379a4bfb66cacfd52
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754844"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Vytvoření první funkce na webu Azure Portal

Funkce Azure umožňuje spouštět kód v prostředí bez serveru, aniž byste museli nejprve vytvořit virtuální počítač (VM) nebo publikovat webovou aplikaci. V tomto článku se dozvíte, jak pomocí funkce Azure vytvořit funkci HTTP "hello world" aktivovanou na webu Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Pokud jste vývojář c#, [zvažte vytvoření první funkce v Sadě Visual Studio 2019](functions-create-your-first-function-visual-studio.md) namísto na portálu. 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

## <a name="create-a-function-app"></a>Vytvoření Function App

K hostování provádění funkcí musíte mít aplikaci Function App. Aplikace funkce umožňuje seskupit funkce jako logická jednotka pro snadnější správu, nasazení, škálování a sdílení prostředků.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Dále vytvořte funkci v nové aplikaci funkce.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Vytvoření funkce aktivované protokolem HTTP

1. Rozbalte novou aplikaci **+** funkcí, vyberte tlačítko vedle **položky Funkce**, zvolte **In-portal**a pak vyberte **Pokračovat**.

    ![Funkce rychlý start pro výběr platformy.](./media/functions-create-first-azure-function/function-app-quickstart-choose-portal.png)

1. Zvolte **WebHook + API**a pak vyberte **Vytvořit**.

    ![Stručný úvod do služby Functions na webu Azure Portal.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

   Na základě šablony funkce aktivované protokolem HTTP pro určitý jazyk se vytvoří funkce.

Novou funkci můžete spustit odesláním požadavku HTTP.

## <a name="test-the-function"></a>Testování funkce

1. V nové funkci vyberte v pravém horním rohu **adresu URL funkce</> Získat.** 

1. V dialogovém okně **Získat adresu URL funkce** vyberte v rozevíracím seznamu výchozí **(funkční klávesu)** a pak vyberte **Kopírovat**. 

    ![Kopírování adresy URL funkce z webu Azure Portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Vložte adresu URL funkce do panelu Adresa vašeho prohlížeče. Přidejte hodnotu `&name=<your_name>` řetězce dotazu na konec této adresy URL a stisknutím klávesy Enter spusťte požadavek. 

    Následující příklad ukazuje odpověď v prohlížeči:

    ![Odezva funkce v prohlížeči.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    Adresa URL požadavku obsahuje klíč, který je ve výchozím nastavení nezbytný pro přístup k funkci přes protokol HTTP.

1. Při spuštění funkce se do protokolů zaznamenávají informace o trasování. Chcete-li zobrazit výstup trasování z předchozího spuštění, vraťte se do funkce na portálu a vyberte šipku v dolní části obrazovky rozbalte **protokoly**.

   ![Prohlížeč protokolu funkcí na webu Azure Portal.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

