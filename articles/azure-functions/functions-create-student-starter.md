---
title: Vytvoření funkce pomocí Azure for Students Starter
description: Naučte se vytvořit funkci Azure pomocí předplatného Azure for student Starter.
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: f40405c9325743da5d1963e3baea781606d2d8d9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182525"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Vytvoření funkce pomocí Azure for Students Starter

V tomto kurzu vytvoříme funkci "Hello World" HTTP v předplatném Azure for Students Starter. Také vás probereme, co je k dispozici v Azure Functions v tomto typu předplatného.

Microsoft *Azure for Students Starter* vám pomůže začít s produkty Azure, které potřebujete pro vývoj v cloudu, a to bez nákladů. [Další informace o této nabídce najdete tady.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Služba Azure Functions umožňuje spuštění kódu v prostředí [bez serveru](https://azure.microsoft.com/solutions/serverless/), aniž byste nejdřív museli vytvořit virtuální počítač nebo publikovat webovou aplikaci. [Další informace o funkcích najdete tady.](./functions-overview.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function"></a>Vytvoření funkce

 V tomto článku se dozvíte, jak pomocí Azure Functions vytvořit v Azure Portal funkci triggeru HTTP "Hello World".

![Vytvoření aplikace Function App na webu Azure Portal](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

## <a name="create-a-function-app"></a>Vytvoření aplikace funkcí

K hostování provádění funkcí musíte mít aplikaci Function App. Aplikace Function App umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování, škálování a sdílení prostředků.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Dál vytvoříte v nové aplikaci Function App funkci.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Vytvoření funkce triggeru HTTP

1. V levé nabídce okna **Functions (funkce** ) vyberte **funkce** a pak v horní nabídce vyberte **Přidat** . 
 
1. V **novém okně funkce** vyberte **Trigger http**.

    ![Zvolit funkci triggeru HTTP](./media/functions-create-student-starter/function-app-select-http-trigger.png)

1. V okně **Nová funkce** přijměte výchozí název **nové funkce** nebo zadejte nový název. 

1. V rozevíracím seznamu **úroveň autorizace** zvolte **anonymní** a pak vyberte **vytvořit funkci**.

    Azure vytvoří funkci triggeru HTTP. Novou funkci můžete spustit odesláním požadavku HTTP.

## <a name="test-the-function"></a>Testování funkce

1. V nové funkci triggeru HTTP v nabídce vlevo vyberte **Code + test** a potom v horní nabídce vyberte **získat adresu URL funkce** .

    ![Vyberte získat adresu URL funkce](./media/functions-create-student-starter/function-app-select-get-function-url.png)

1. V dialogovém okně **získat adresu URL funkce** vyberte v rozevíracím seznamu možnost **výchozí** a potom vyberte ikonu **Kopírovat do schránky** . 

    ![Kopírování adresy URL funkce z webu Azure Portal](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

1. Vložte adresu URL funkce do panelu Adresa vašeho prohlížeče. Na `?name=<your_name>` konec této adresy URL přidejte hodnotu řetězce dotazu a stisknutím klávesy ENTER spusťte požadavek. 

    Následující příklad ukazuje odpověď v prohlížeči:

    ![Odezva funkce v prohlížeči.](./media/functions-create-student-starter/function-app-browser-testing.png)

    Adresa URL požadavku obsahuje klíč, který je ve výchozím nastavení nezbytný pro přístup k funkci přes protokol HTTP.

1. Při spuštění funkce se do protokolů zaznamenávají informace o trasování. Chcete-li zobrazit výstup trasování, vraťte se na stránku **Code + test** na portálu a rozbalte šipku **protokoly** v dolní části stránky.

   ![Prohlížeč protokolu funkcí na webu Azure Portal.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Podporované funkce v Azure for Students Starter

V Azure for Students Starter máte přístup k většině funkcí prostředí Azure Functions runtime s několika omezeními, která jsou uvedená níže:

* Trigger HTTP je jediným podporovaným typem triggeru.
    * Podporují se všechny vstupní a výstupní vazby. [Úplný seznam najdete tady.](functions-triggers-bindings.md)
* Podporované jazyky: 
    * C# (.NET Core 2)
    * JavaScript (Node.js 8 & 10)
    * F # (.NET Core 2)
    * [Tady najdete jazyky podporované v vyšších plánech.](supported-languages.md)
* Jediným podporovaným operačním systémem je Windows.
* Rozsah je omezen na [jednu instanci bezplatné úrovně](https://azure.microsoft.com/pricing/details/app-service/windows/) , která je spuštěna po dobu až 60 minut každého dne. Serverlessly se bude automaticky škálovat od 0 do 1 instance, protože se obdrží přenos HTTP, ale ještě ne.
* Podporují se jenom [verze 2. x a novější](functions-versions.md) z modulu runtime Functions.
* Pro funkce úprav a publikování se podporují všechny vývojářské nástroje. To zahrnuje VS Code, Visual Studio, Azure CLI a Azure Portal. Pokud byste chtěli použít jinou možnost než portál, musíte nejdřív vytvořit aplikaci na portálu a pak tuto aplikaci zvolit jako cíl nasazení v upřednostňovaném nástroji.

## <a name="next-steps"></a>Další kroky

Právě jste dokončili vytváření aplikace Function App s jednoduchou funkcí triggeru HTTP. Dále můžete prozkoumat místní nástroje, další jazyky, monitorování a integrace.

 * [Vytvoření první funkce pomocí sady Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Vytvoření první funkce pomocí Visual Studio Code](./create-first-function-vs-code-csharp.md)
 * [Azure Functions příručka pro vývojáře JavaScriptu](./functions-reference-node.md)
 * [Připojení k Azure SQL Database pomocí Azure Functions](./functions-scenario-database-table-cleanup.md)
 * [Přečtěte si další informace o Azure Functions vazeb HTTP](./functions-bindings-http-webhook.md).
 * [Monitorování Azure Functions](./functions-monitoring.md)