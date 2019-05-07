---
title: Vytvoření aplikace function app v Linuxu na webu Azure Portal | Dokumentace Microsoftu
description: Naučíte se postup vytvoření první funkce Azure Function pro provádění pomocí webu Azure Portal bez serveru.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: glenga
ms.custom: ''
ms.openlocfilehash: f7fa1d58c21ea0ed457cc5987d7823fad68d554f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153820"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Vytvoření aplikace funkcí v plánu služby Azure App Service v Linuxu

Služba Azure Functions umožňuje hostovat funkce v Linuxu ve výchozím kontejneru Azure App Service. Tento článek vás provede způsob použití [webu Azure portal](https://portal.azure.com) k vytvoření aplikace funkcí hostované v systému Linux, na kterém běží v [plán služby App Service](functions-scale.md#app-service-plan). Můžete také [použít vlastní kontejner](functions-create-function-linux-custom-image.md).

![Vytvoření aplikace Function App na webu Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com> pomocí svého účtu Azure.

## <a name="create-a-function-app"></a>Vytvoření Function App

K hostování provádění funkcí v Linuxu musíte mít aplikaci funkcí. Aplikace funkcí poskytuje prostředí pro provádění kódu funkce. Umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. V tomto článku vytvoříte plán služby App Service při vytváření aplikace funkcí.

1. Vyberte **vytvořit prostředek** nalezeno tlačítko v levém horním rohu webu Azure portal, pak vyberte **Compute** > **aplikace Function App**.

    ![Vytvoření aplikace Function App na portálu Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Použijte nastavení aplikace Function App uvedená v tabulce pod obrázkem.

    ![Určení nastavení nové aplikace Function App](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název aplikace** | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky jsou `a-z`, `0-9` a `-`.  | 
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace Function App vytvořena. | 
    | **[Skupina prostředků](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. |
    | **OS** | Linux | Aplikace function app běží na systému Linux. |
    | **Publikování** | Kód | Výchozí kontejner Linuxu pro vaše **zásobník modulu Runtime** se používá. Vše, co je potřeba zadat je kód projektu funkce aplikace. Další možností je publikovat vlastní [image Dockeru](functions-create-function-linux-custom-image.md). |
    | **[Plán hostování](functions-scale.md)** | Plán služby App Service | Plán hostování, který určuje způsob přidělování prostředků aplikaci Function App. Při spuštění v plánu služby App Service, můžete řídit [škálování vaší aplikace function App](functions-scale.md).  |
    | **Plán služby App Service/umístění** | Vytvoření plánu | Zvolte **vytvořit nový** a zadat **plán služby App Service** název. Zvolte **umístění** v [oblasti](https://azure.microsoft.com/regions/) vaší blízkosti nebo v blízkosti jiných služeb vaše funkce využívají. Zvolte požadované  **[cenová úroveň](https://azure.microsoft.com/pricing/details/app-service/linux/)**. <br/>Aplikace function App Linux i Windows nelze spustit v rámci stejného plánu služby App Service. |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Zvolte **.NET** pro funkce C# a F#. [Podpora jazyka Python](functions-reference-python.md) je ve verzi preview v současnosti. |
    | **[Úložiště](../storage/common/storage-quickstart-create-account.md)** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete použít také existující účet, který splňuje [požadavky na účet úložiště](functions-scale.md#storage-account-requirements). |
    | **[Application Insights](functions-monitoring.md)** | Enabled | Application Insights je ve výchozím nastavení zakázána. Doporučujeme, abyste povoluje se integrace služby Application Insights teď a výběr umístění pro hostování poblíž vaší aktuální polohy plán služby App Service. Pokud chcete udělat to později, přečtěte si téma [monitorování Azure Functions](functions-monitoring.md).  |

3. Aplikaci Function App zřídíte a nasadíte kliknutím na **Vytvořit**.

4. Vyberte ikonu oznámení v pravém horním rohu portálu a sledujte, kdy se objeví zpráva, že **nasazení bylo úspěšné**.

    ![Určení nastavení nové aplikace Function App](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte.

Dál vytvoříte v nové aplikaci Function App funkci. I když vaše aplikace function app je k dispozici, může trvat několik minut, než plně inicializován.

## <a name="create-function"></a>Vytvoření funkce aktivované protokolem HTTP

Tato část ukazuje, jak vytvořit funkci ve vaší nové aplikaci function app na portálu.

> [!NOTE]
> Portálu vývojové prostředí může být užitečné pro vyzkoušení Azure Functions. Pro většinu scénářů doporučujeme vývoj funkcí místně a publikování projektu do aplikace function app, buď pomocí [Visual Studio Code](functions-create-first-function-vs-code.md#create-an-azure-functions-project) nebo [nástrojů Azure Functions Core](functions-run-local.md#create-a-local-functions-project).  

1. Ve vaší nové aplikaci function app, vyberte **přehled** kartu a po jeho načtení zcela zvolte **+ nová funkce**.

    ![Vytvoření nové funkce z karty přehledu](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. V **rychlý Start** kartě **na portálu**a vyberte **pokračovat**.

    ![Volba vývojářské platformy funkce.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

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

## <a name="next-steps"></a>Další postup

Vytvořili jste aplikaci Function App s jednoduchou funkcí aktivovanou protokolem HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Další informace najdete v tématu [Vazby protokolu HTTP služby Azure Functions](functions-bindings-http-webhook.md).
