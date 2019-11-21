---
title: Create a function app on Linux from the Azure portal
description: Naučíte se postup vytvoření první funkce Azure Function pro provádění pomocí webu Azure Portal bez serveru.
ms.topic: quickstart
ms.date: 02/28/2019
ms.openlocfilehash: fdc2d9c7b5945e48cc87f3edd918498c3d45f55e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233093"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Create a function app on Linux in an Azure App Service plan

Služba Azure Functions umožňuje hostovat funkce v Linuxu ve výchozím kontejneru Azure App Service. This article walks you through how to use the [Azure portal](https://portal.azure.com) to create a Linux-hosted function app that runs in an [App Service plan](functions-scale.md#app-service-plan). Můžete také [použít vlastní kontejner](functions-create-function-linux-custom-image.md).

![Vytvoření aplikace Function App na webu Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com> pomocí svého účtu Azure.

## <a name="create-a-function-app"></a>Vytvoření Function App

K hostování provádění funkcí v Linuxu musíte mít aplikaci funkcí. Aplikace funkcí poskytuje prostředí pro provádění kódu funkce. Umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. In this article, you create an App Service plan when you create your function app.

1. Select the **Create a resource** button found on the upper left-hand corner of the Azure portal, then select **Compute** > **Function App**.

    ![Vytvoření aplikace Function App na portálu Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Použijte nastavení aplikace Function App uvedená v tabulce pod obrázkem.

    ![Určení nastavení nové aplikace Function App](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název aplikace** | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky jsou `a-z`, `0-9` a `-`.  | 
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace Function App vytvořena. | 
    | **[Skupina prostředků](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. |
    | **OS** | Linux | The function app runs on Linux. |
    | **Publikování** | Kód | The default Linux container for your **Runtime Stack** is used. All you need to provide is your function app project code. Another option is to publish a custom [Docker image](functions-create-function-linux-custom-image.md). |
    | **[Plán hostování](functions-scale.md)** | Plán služby App Service | Plán hostování, který určuje způsob přidělování prostředků aplikaci Function App. When you run in an App Service plan, you can control the [scaling of your function app](functions-scale.md).  |
    | **App Service plan/Location** | Create plan | Choose **Create new** and supply an **App Service plan** name. Choose a **Location** in a [region](https://azure.microsoft.com/regions/) near you or near other services your functions access. Choose your desired **[Pricing tier](https://azure.microsoft.com/pricing/details/app-service/linux/)** . <br/>You can't run both Linux and Windows function apps in the same App Service plan. |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Zvolte **.NET** pro funkce C# a F#. |
    | **[Úložiště](../storage/common/storage-quickstart-create-account.md)** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete použít také existující účet, který splňuje [požadavky na účet úložiště](functions-scale.md#storage-account-requirements). |
    | **[Application Insights](functions-monitoring.md)** | Povoleno | Application Insights is disabled by default. We recommend enabling Application Insights integration now and choosing a hosting location near your App Service plan location. If you want to do this later, see [Monitor Azure Functions](functions-monitoring.md).  |

3. Aplikaci Function App zřídíte a nasadíte kliknutím na **Vytvořit**.

4. Vyberte ikonu oznámení v pravém horním rohu portálu a sledujte, kdy se objeví zpráva, že **nasazení bylo úspěšné**.

    ![Určení nastavení nové aplikace Function App](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte.

Dál vytvoříte v nové aplikaci Function App funkci. Even after your function app is available, it may take a few minutes to be fully initialized.

## <a name="create-function"></a>Vytvoření funkce aktivované protokolem HTTP

This section shows you how to create a function in your new function app in the portal.

> [!NOTE]
> The portal development experience can be useful for trying out Azure Functions. For most scenarios, consider developing your functions locally and publishing the project to your function app using either [Visual Studio Code](functions-create-first-function-vs-code.md#create-an-azure-functions-project) or the [Azure Functions Core Tools](functions-run-local.md#create-a-local-functions-project).  

1. In your new function app, choose the **Overview** tab, and after it loads completely choose **+ New function**.

    ![Create a new function from the Overview tab](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. In the **Quickstart** tab, choose **In-portal**, and select **Continue**.

    ![Choose your function development platform.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

1. Zvolte **Webhook + API** a potom vyberte **Vytvořit**.

    ![Stručný úvod do služby Functions na webu Azure Portal.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-node-webhook.png)

Na základě šablony funkce aktivované protokolem HTTP pro určitý jazyk se vytvoří funkce.

Novou funkci můžete spustit odesláním požadavku HTTP.

## <a name="test-the-function"></a>Testování funkce

1. V nové funkci klikněte vpravo nahoře na **</> Získat adresu URL funkce**, vyberte **výchozí (klíč funkce)** a potom klikněte na **Kopírovat**. 

    ![Kopírování adresy URL funkce z webu Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

2. Vložte adresu URL funkce do panelu Adresa vašeho prohlížeče. Na konec této adresy URL připojte hodnotu řetězce dotazu `&name=<yourname>` a stisknutím klávesy `Enter` na klávesnici požadavek proveďte. V prohlížeči by se měla zobrazit odpověď, kterou funkce vrátila.  

    Následující příklad ukazuje odpověď v prohlížeči:

    ![Odezva funkce v prohlížeči.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    Adresa URL požadavku obsahuje klíč, který je ve výchozím nastavení nezbytný pro přístup k funkci přes protokol HTTP.

3. Při spuštění funkce se do protokolů zaznamenávají informace o trasování. Pokud chcete zobrazit výstup trasování z předchozího zpracování, vraťte se k funkci na portálu a kliknutím na šipku ve spodní části obrazovky rozbalte položku **Protokoly**.

   ![Prohlížeč protokolu funkcí na webu Azure Portal.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Vytvořili jste aplikaci Function App s jednoduchou funkcí aktivovanou protokolem HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Další informace najdete v tématu [Vazby protokolu HTTP služby Azure Functions](functions-bindings-http-webhook.md).
