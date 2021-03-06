---
title: Vytvoření první funkce na webu Azure Portal
description: Naučíte se postup vytvoření první funkce Azure Function pro provádění pomocí webu Azure Portal bez serveru.
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-csharp, mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 336e531f4ec64141770fc26d7e6eea9ebfedf922
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517130"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Vytvoření první funkce na webu Azure Portal

Azure Functions umožňuje spuštění kódu v prostředí bez serveru, aniž by bylo nutné nejprve vytvořit virtuální počítač nebo publikovat webovou aplikaci. V tomto článku se naučíte, jak pomocí Azure Functions vytvořit funkci triggeru HTTP "Hello World" v Azure Portal.

[!INCLUDE [functions-in-portal-editing-note](../../includes/functions-in-portal-editing-note.md)] 

Místo toho doporučujeme [vyvíjet funkce místně](functions-develop-local.md) a publikovat je do aplikace Function App v Azure.  
Pomocí jednoho z následujících odkazů můžete začít se zvoleným místním vývojovým prostředím a jazykem:

| Visual Studio Code | Terminál/příkazový řádek | Visual Studio |
| --- | --- | --- |
|  &bull;&nbsp;[Začínáme s C #](./create-first-function-vs-code-csharp.md)<br/>&bull;&nbsp;[Začínáme s jazykem Java](./create-first-function-vs-code-java.md)<br/>&bull;&nbsp;[Začínáme s JavaScriptem](./create-first-function-vs-code-node.md)<br/>&bull;&nbsp;[Začínáme s PowerShellem](./create-first-function-vs-code-powershell.md)<br/>&bull;&nbsp;[Začínáme s Pythonem](./create-first-function-vs-code-python.md) |&bull;&nbsp;[Začínáme s C #](./create-first-function-cli-csharp.md)<br/>&bull;&nbsp;[Začínáme s jazykem Java](./create-first-function-cli-java.md)<br/>&bull;&nbsp;[Začínáme s JavaScriptem](./create-first-function-cli-node.md)<br/>&bull;&nbsp;[Začínáme s PowerShellem](./create-first-function-cli-powershell.md)<br/>&bull;&nbsp;[Začínáme s Pythonem](./create-first-function-cli-python.md) | [Začínáme s C #](functions-create-your-first-function-visual-studio.md) |

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

## <a name="create-a-function-app"></a>Vytvoření aplikace funkcí

K hostování provádění funkcí musíte mít aplikaci Function App. Aplikace Function App umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování, škálování a sdílení prostředků.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Dále vytvořte funkci v nové aplikaci Function App.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Vytvoření funkce triggeru HTTP

1. V levé nabídce okna **Functions (funkce** ) vyberte **funkce** a pak v horní nabídce vyberte **Přidat** . 
 
1. V okně **Přidat funkci** vyberte šablonu **triggeru http** .

    ![Zvolit funkci triggeru HTTP](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. V části **Podrobnosti šablony** použít `HttpExample` pro **novou funkci** vyberte v rozevíracím seznamu **[úroveň autorizace](functions-bindings-http-webhook-trigger.md#authorization-keys)** možnost **anonymní** a pak vyberte **Přidat**.

    Azure vytvoří funkci triggeru HTTP. Novou funkci můžete spustit odesláním požadavku HTTP.

## <a name="test-the-function"></a>Testování funkce

1. V nové funkci triggeru HTTP v nabídce vlevo vyberte **Code + test** a potom v horní nabídce vyberte **získat adresu URL funkce** .

    ![Vyberte získat adresu URL funkce](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. V dialogovém okně **získat adresu URL funkce** vyberte v rozevíracím seznamu možnost **výchozí** a potom vyberte ikonu **Kopírovat do schránky** . 

    ![Kopírování adresy URL funkce z webu Azure Portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Vložte adresu URL funkce do panelu Adresa vašeho prohlížeče. Na `?name=<your_name>` konec této adresy URL přidejte hodnotu řetězce dotazu a stisknutím klávesy ENTER spusťte požadavek. V prohlížeči by se měla zobrazit zpráva odpovědi, která vrací zpět hodnotu řetězce dotazu. 

    Pokud adresa URL žádosti obsahovala [přístupový klíč](functions-bindings-http-webhook-trigger.md#authorization-keys) ( `?code=...` ), znamená to, že při vytváření funkce namísto úrovně **anonymního** přístupu zvolíte **funkci** . V takovém případě byste měli místo toho připojit `&name=<your_name>` .

1. Při spuštění funkce se do protokolů zaznamenávají informace o trasování. Chcete-li zobrazit výstup trasování, vraťte se na stránku **Code + test** na portálu a rozbalte šipku **protokoly** v dolní části stránky. Zavolejte funkci znovu, abyste viděli výstup trasování zapsaný do protokolů. 

    :::image type="content" source="media/functions-create-first-azure-function/function-view-logs.png" alt-text="Prohlížeč protokolu funkcí v Azure Portal":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
