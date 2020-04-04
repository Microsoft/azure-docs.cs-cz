---
title: Vytvoření první funkce na webu Azure Portal
description: Naučíte se postup vytvoření první funkce Azure Function pro provádění pomocí webu Azure Portal bez serveru.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: quickstart
ms.date: 03/26/2020
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 417ad96dc3dea25e322dbdb4d81c034a9b9c1e80
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656894"
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

1. V levé nabídce okna **Funkce** vyberte **Funkce**a pak v horní nabídce vyberte **Přidat.** 
 
1. V okně **Nová funkce** vyberte http **aktivační událost**.

    ![Volba aktivační funkce HTTP](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. V okně **Nová funkce** přijměte výchozí název funkce **nebo**zadejte nový název. 

1. Z rozevíracího seznamu **Úroveň autorizace** zvolte **Anonymní** a pak vyberte **Vytvořit funkci**.

    Azure vytvoří funkci aktivační události HTTP. Novou funkci můžete spustit odesláním požadavku HTTP.

## <a name="test-the-function"></a>Testování funkce

1. V nové funkci aktivační události HTTP vyberte v levé nabídce **kód + test** a v horní nabídce vyberte Získat adresu URL **funkce.**

    ![Vybrat adresu URL funkce Get](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. V dialogovém okně **Získat adresu URL funkce** vyberte **výchozí** z rozevíracího seznamu a pak vyberte ikonu Kopírovat **do schránky.** 

    ![Kopírování adresy URL funkce z webu Azure Portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Vložte adresu URL funkce do panelu Adresa vašeho prohlížeče. Přidejte hodnotu `?name=<your_name>` řetězce dotazu na konec této adresy URL a stisknutím klávesy Enter spusťte požadavek. 

    Následující příklad ukazuje odpověď v prohlížeči:

    ![Odezva funkce v prohlížeči.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    Adresa URL požadavku obsahuje klíč, který je ve výchozím nastavení nezbytný pro přístup k funkci přes protokol HTTP.

1. Při spuštění funkce se do protokolů zaznamenávají informace o trasování. Chcete-li zobrazit výstup trasování, vraťte se na stránku **Kód + Test** na portálu a rozbalte šipku **protokoly** v dolní části stránky.

   ![Prohlížeč protokolu funkcí na webu Azure Portal.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

