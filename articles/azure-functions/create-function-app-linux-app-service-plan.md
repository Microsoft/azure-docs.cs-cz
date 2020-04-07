---
title: Vytvoření aplikace funkcí na Linuxu z webu Azure Portal
description: Naučíte se postup vytvoření první funkce Azure Function pro provádění pomocí webu Azure Portal bez serveru.
ms.topic: how-to
ms.date: 02/28/2019
ms.openlocfilehash: b2de36faf07ad661ff8817adc48b726f54990ceb
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754113"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Vytvoření aplikace funkcí na Linuxu v plánu služby Azure App Service

Služba Azure Functions umožňuje hostovat funkce v Linuxu ve výchozím kontejneru Azure App Service. Tento článek vás provede, jak pomocí [portálu Azure](https://portal.azure.com) vytvořit aplikaci pro funkce hostované linuxem, která běží v [plánu služby App Service](functions-scale.md#app-service-plan). Můžete také [použít vlastní kontejner](functions-create-function-linux-custom-image.md).

![Vytvoření aplikace Function App na webu Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com> pomocí svého účtu Azure.

## <a name="create-a-function-app"></a>Vytvoření Function App

K hostování provádění funkcí v Linuxu musíte mít aplikaci funkcí. Aplikace funkcí poskytuje prostředí pro provádění kódu funkce. Umožňuje seskupit funkce jako logická jednotka pro snadnější správu, nasazení, škálování a sdílení prostředků. V tomto článku vytvoříte plán služby App Service při vytváření aplikace funkce.

1. V levém horním rohu webu Azure Portal vyberte tlačítko **Vytvořit prostředek** a pak vyberte **Compute** > **Function App**.

    ![Vytvoření aplikace Function App na portálu Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Použijte nastavení aplikace Function App uvedená v tabulce pod obrázkem.

    ![Určení nastavení nové aplikace Function App](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název aplikace** | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky jsou `a-z`, `0-9` a `-`.  | 
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace Function App vytvořena. | 
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. |
    | **OS** | Linux | Aplikace funkce běží na Linuxu. |
    | **Publikování** | kód | Používá se výchozí linuxový kontejner pro **zásobník runtime.** Vše, co potřebujete poskytnout, je kód projektu aplikace funkce. Další možností je publikovat vlastní [image Dockeru](functions-create-function-linux-custom-image.md). |
    | **[Hostingový plán](functions-scale.md)** | Plán služby App Service | Plán hostování, který určuje způsob přidělování prostředků aplikaci Function App. Když spustíte v plánu služby App Service, můžete řídit [škálování vaší aplikace funkce](functions-scale.md).  |
    | **Plán služby App Service / umístění** | Vytvořit plán | Zvolte **Vytvořit nový** a zadej název **plánu služby App Service.** Zvolte **umístění** v [oblasti](https://azure.microsoft.com/regions/) ve vašem okolí nebo v blízkosti jiných služeb, ke které vaše funkce přistupují. Zvolte požadovanou **[cenovou úroveň](https://azure.microsoft.com/pricing/details/app-service/linux/)**. <br/>Ve stejném plánu služby App Service nelze spouštět aplikace linuxových i windowsových funkcí. |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Zvolte **.NET** pro funkce C# a F#. |
    | **[Storage](../storage/common/storage-account-create.md)** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete použít také existující účet, který splňuje [požadavky na účet úložiště](storage-considerations.md#storage-account-requirements). |
    | **[Application Insights](functions-monitoring.md)** | Povoleno | Application Insights je ve výchozím nastavení zakázán. Doporučujeme povolit integraci Application Insights nyní a výběr umístění pro hostování v blízkosti umístění plánu služby App Service. Pokud to chcete udělat později, najdete [v tématu Monitorování funkcí Azure](functions-monitoring.md).  |

3. Aplikaci Function App zřídíte a nasadíte kliknutím na **Vytvořit**.

4. Vyberte ikonu oznámení v pravém horním rohu portálu a sledujte, kdy se objeví zpráva, že **nasazení bylo úspěšné**.

    ![Určení nastavení nové aplikace Function App](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte.

Dál vytvoříte v nové aplikaci Function App funkci. I poté, co je aplikace funkce k dispozici, může trvat několik minut, než bude plně inicializována.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Vytvoření funkce aktivované protokolem HTTP

V této části se zobrazí, jak vytvořit funkci v nové aplikaci funkcí na portálu.

> [!NOTE]
> Prostředí pro vývoj portálu může být užitečné při vyzkoušení funkcí Azure. Pro většinu scénářů zvažte vývoj funkcí místně a publikování projektu do aplikace funkce pomocí [visual studio kód](functions-create-first-function-vs-code.md#create-an-azure-functions-project) nebo nástroje Azure functions core [tools](functions-run-local.md#create-a-local-functions-project).  

1. V nové aplikaci funkcí zvolte kartu **Přehled** a po načtení zcela zvolte **+ Nová funkce**.

    ![Vytvoření nové funkce na kartě Přehled](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. Na kartě **Rychlý start** zvolte **In-portal**a vyberte **Pokračovat**.

    ![Vyberte si platformu pro vývoj funkcí.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

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
