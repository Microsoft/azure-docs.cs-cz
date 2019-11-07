---
title: Vytvoření aplikace Function App na platformě Linux z Azure Portal | Microsoft Docs
description: Naučíte se postup vytvoření první funkce Azure Function pro provádění pomocí webu Azure Portal bez serveru.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: glenga
ms.openlocfilehash: 89755e8b70a490f1c5746b23e0d36c5d90b624c1
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571657"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Vytvoření aplikace Function App v systému Linux v plánu Azure App Service

Služba Azure Functions umožňuje hostovat funkce v Linuxu ve výchozím kontejneru Azure App Service. Tento článek vás provede postupem použití [Azure Portal](https://portal.azure.com) k vytvoření aplikace funkce hostované v systému Linux, která běží v [plánu App Service](functions-scale.md#app-service-plan). Můžete také [použít vlastní kontejner](functions-create-function-linux-custom-image.md).

![Vytvoření aplikace Function App na webu Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com> pomocí svého účtu Azure.

## <a name="create-a-function-app"></a>Vytvoření Function App

K hostování provádění funkcí v Linuxu musíte mít aplikaci funkcí. Aplikace funkcí poskytuje prostředí pro provádění kódu funkce. Umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. V tomto článku vytvoříte App Service plán při vytváření aplikace Function App.

1. Vyberte tlačítko **vytvořit prostředek** v levém horním rohu Azure Portal a pak vyberte **COMPUTE** > **Function App**.

    ![Vytvoření aplikace Function App na portálu Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Použijte nastavení aplikace Function App uvedená v tabulce pod obrázkem.

    ![Určení nastavení nové aplikace Function App](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název aplikace** | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky jsou `a-z`, `0-9` a `-`.  | 
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace Function App vytvořena. | 
    | **[Skupina prostředků](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. |
    | **OS** | Linux | Aplikace Function App běží na Linux. |
    | **Publikování** | Kód | Použije se výchozí kontejner Linux pro **zásobník modulu runtime** . Vše, co potřebujete zadat, je váš kód projektu Function App. Další možností je publikovat vlastní [Image Docker](functions-create-function-linux-custom-image.md). |
    | **[Plán hostování](functions-scale.md)** | Plán služby App Service | Plán hostování, který určuje způsob přidělování prostředků aplikaci Function App. Když spustíte v plánu App Service, můžete řídit [škálování aplikace Function App](functions-scale.md).  |
    | **App Service plán/umístění** | Vytvořit plán | Vyberte **vytvořit nový** a zadejte název **App Serviceho plánu** . Vyberte **umístění** v [oblasti](https://azure.microsoft.com/regions/) poblíž nebo v blízkosti jiných služeb, ke kterým máte přístup. Vyberte požadovanou **[cenovou úroveň](https://azure.microsoft.com/pricing/details/app-service/linux/)** . <br/>Ve stejném plánu App Service nemůžete spouštět aplikace Function App pro Linux i Windows. |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Zvolte **.NET** pro funkce C# a F#. |
    | **[Úložiště](../storage/common/storage-quickstart-create-account.md)** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete použít také existující účet, který splňuje [požadavky na účet úložiště](functions-scale.md#storage-account-requirements). |
    | **[Application Insights](functions-monitoring.md)** | Povoleno | Application Insights je ve výchozím nastavení zakázaná. Doporučujeme nyní povolit Application Insights Integration a zvolit umístění hostování poblíž svého umístění plánu App Service. Pokud to chcete provést později, přečtěte si téma [monitorování Azure Functions](functions-monitoring.md).  |

3. Aplikaci Function App zřídíte a nasadíte kliknutím na **Vytvořit**.

4. Vyberte ikonu oznámení v pravém horním rohu portálu a sledujte, kdy se objeví zpráva, že **nasazení bylo úspěšné**.

    ![Určení nastavení nové aplikace Function App](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte.

Dál vytvoříte v nové aplikaci Function App funkci. I po tom, co je aplikace Function App dostupná, může trvat několik minut, než se plně inicializuje.

## <a name="create-function"></a>Vytvoření funkce aktivované protokolem HTTP

V této části se dozvíte, jak vytvořit funkci ve vaší nové aplikaci Function App na portálu.

> [!NOTE]
> Prostředí pro vývoj na portálu může být užitečné při vyzkoušení Azure Functions. V případě většiny scénářů zvažte místní vývoj funkcí a publikování projektu do aplikace Function App pomocí [Visual Studio Code](functions-create-first-function-vs-code.md#create-an-azure-functions-project) nebo [Azure Functions Core Tools](functions-run-local.md#create-a-local-functions-project).  

1. V nové aplikaci Function App klikněte na kartu **Přehled** a po načtení úplně vyberte **+ Nová funkce**.

    ![Vytvoření nové funkce na kartě Přehled](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. Na kartě **rychlý Start** zvolte možnost **v portálu**a vyberte **pokračovat**.

    ![Vyberte platformu pro vývoj funkcí.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

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
