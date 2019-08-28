---
title: Vytvoření funkce pomocí Azure for Students Starter | Microsoft Docs
description: Naučte se vytvořit funkci Azure pomocí předplatného Azure for student Starter.
Customer intent: As a student, I want to be able to create a HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
services: functions
documentationcenter: na
author: alexkarcher-msft
manager: ggailey777
ms.assetid: ''
ms.service: azure-functions
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: b4a143f14dc4e443570e8eca9ce6ce9e81b1d783
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096639"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Vytvoření funkce pomocí Azure for Students Starter

V tomto kurzu vytvoříme ve službě Azure for Students Starter předplatné funkce Hello World HTTP. Také vás probereme, co je k dispozici v Azure Functions v tomto typu předplatného.

Microsoft *Azure for Students Starter* vám pomůže začít s produkty Azure, které potřebujete pro vývoj v cloudu, a to bez nákladů. [Další informace o této nabídce najdete tady.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Služba Azure Functions umožňuje spuštění kódu v prostředí [bez serveru](https://azure.microsoft.com/solutions/serverless/), aniž byste nejdřív museli vytvořit virtuální počítač nebo publikovat webovou aplikaci. [Další informace o funkcích najdete tady.](./functions-overview.md)

## <a name="create-a-function"></a>Vytvoření funkce

 V tomto tématu se dozvíte, jak pomocí Functions vytvořit funkci "Hello World", která se aktivuje protokolem HTTP v Azure Portal.

![Vytvoření aplikace Function App na webu Azure Portal](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com> pomocí svého účtu Azure.

## <a name="create-a-function-app"></a>Vytvoření Function App

K hostování provádění funkcí musíte mít aplikaci Function App. Aplikace Function App umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. 

1. Klikněte na tlačítko **Nový** v levém horním rohu portálu Azure Portal a pak vyberte **Compute** > **Function App**.

    ![Vytvoření aplikace Function App na portálu Azure Portal](./media/functions-create-student-starter/function-app-create-flow.png)

2. Použijte nastavení aplikace Function App uvedená v tabulce pod obrázkem.

    <img src="./media/functions-create-student-starter/Function-create-start.png" width="315">

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název aplikace** | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky jsou `a-z`, `0-9` a `-`.  | 
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace Function App vytvořena. | 
    | **[Skupina prostředků](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. |
   | **[App Service plán/umístění](./functions-scale.md)** | Nová | Plán hostování, který určuje, do jaké oblasti je aplikace Function App nasazená, a hustota prostředků. Všechny aplikace Function App nasazené do stejného plánu budou sdílet stejnou jedinou volnou instanci. Toto je omezení plánu počátečního startu studenta. Možnosti úplného hostování jsou [vysvětleny zde.](./functions-scale.md)|
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Zvolte **.NET** pro funkce C# a F#. |
    |**[Application Insights](./functions-monitoring.md)**| Enabled | Application Insights se používá k ukládání a analýze protokolů aplikace Function App. Pokud zvolíte umístění, které podporuje Application Insights, je povolené ve výchozím nastavení. Application Insights můžete pro libovolnou funkci povolit ručním výběrem okolní oblasti pro nasazení Application Insights. Bez Application Insights budete moct zobrazit jenom protokoly živého streamování.

3. Vyberte **App Service plán/umístění** výše a zvolte jiné umístění.

4. Vyberte **vytvořit nové** a zadejte jedinečný název pro svůj plán.

5. Vyberte umístění, které je pro vás nejblíže. [Tady najdete úplnou mapu oblastí Azure.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. Aplikaci Function App zřídíte a nasadíte kliknutím na **Vytvořit**.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Vyberte ikonu oznámení v pravém horním rohu portálu a sledujte, kdy se objeví zpráva, že **nasazení bylo úspěšné**.

    ![Určení nastavení nové aplikace Function App](./media/functions-create-student-starter/function-app-create-notification.png)

8. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte.

Dál vytvoříte v nové aplikaci Function App funkci.

## <a name="create-function"></a>Vytvoření funkce aktivované protokolem HTTP

1. Rozbalte svoji novou aplikaci funkcí, vyberte tlačítko **+** vedle položky **Funkce**, zvolte **Na portálu** a potom **Pokračovat**.

    ![Rychlý start služby Functions – výběr platformy](./media/functions-create-student-starter/function-app-quickstart-choose-portal.png)

1. Zvolte **Webhook + API** a potom vyberte **Vytvořit**.

    ![Stručný úvod do služby Functions na webu Azure Portal.](./media/functions-create-student-starter/function-app-quickstart-node-webhook.png)

Na základě šablony funkce aktivované protokolem HTTP pro určitý jazyk se vytvoří funkce.

Novou funkci můžete spustit odesláním požadavku HTTP.

## <a name="test-the-function"></a>Testování funkce

1. V nové funkci klikněte vpravo nahoře na **</> Získat adresu URL funkce**, vyberte **výchozí (klíč funkce)** a potom klikněte na **Kopírovat**. 

    ![Kopírování adresy URL funkce z webu Azure Portal](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

2. Vložte adresu URL funkce do panelu Adresa vašeho prohlížeče. Na konec této adresy URL připojte hodnotu řetězce dotazu `&name=<yourname>` a stisknutím klávesy `Enter` na klávesnici požadavek proveďte. V prohlížeči by se měla zobrazit odpověď, kterou funkce vrátila.  

    Následující příklad ukazuje odpověď v prohlížeči:

    ![Odezva funkce v prohlížeči.](./media/functions-create-student-starter/function-app-browser-testing.png)

    Adresa URL požadavku obsahuje klíč, který je ve výchozím nastavení nezbytný pro přístup k funkci přes protokol HTTP.

3. Při spuštění funkce se do protokolů zaznamenávají informace o trasování. Pokud chcete zobrazit výstup trasování z předchozího zpracování, vraťte se k funkci na portálu a kliknutím na šipku ve spodní části obrazovky rozbalte položku **Protokoly**.

   ![Prohlížeč protokolu funkcí na webu Azure Portal.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Podporované funkce v Azure for Students Starter

V Azure for student Starter máte přístup k většině funkcí prostředí Azure Functions runtime s několika omezeními, která jsou uvedená níže:

* Trigger HTTP je jediným podporovaným typem triggeru.
    * Podporují se všechny vstupní a výstupní vazby. [Úplný seznam najdete tady.](functions-triggers-bindings.md)
* Podporované jazyky: 
    * C#(.NET Core 2)
    * JavaScript (Node. js 8 & 10)
    * F#(.NET Core 2)
    * [Tady najdete jazyky podporované v vyšších plánech.](supported-languages.md)
* Jediným podporovaným operačním systémem je Windows.
* Rozsah je omezen na [jednu instanci bezplatné úrovně](https://azure.microsoft.com/pricing/details/app-service/windows/) , která je spuštěna po dobu až 60 minut každého dne. Serverlessly se bude automaticky škálovat z 0 na 1 instanci, protože se obdrží přenos HTTP, ale ještě ne.
* Podporuje se jenom [modul runtime 2. x](functions-versions.md) .
* Pro funkce úprav a publikování se podporují všechny vývojářské nástroje. To zahrnuje VS Code, Visual Studio, Azure CLI a Azure Portal. Pokud byste chtěli použít jinou možnost než portál, budete muset nejdřív vytvořit aplikaci na portálu a pak tuto aplikaci zvolit jako cíl nasazení v preferovaném nástroji.

## <a name="next-steps"></a>Další kroky

Vytvořili jste aplikaci funkcí s jednoduchou funkcí aktivovanou protokolem HTTP! Nyní můžete prozkoumat místní nástroje, další jazyky, monitorování a integrace.

 * [Vytvoření první funkce pomocí sady Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Vytvoření první funkce pomocí Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Azure Functions příručka pro vývojáře JavaScriptu](./functions-reference-node.md)
 * [Připojení k Azure SQL Database pomocí Azure Functions](./functions-scenario-database-table-cleanup.md)
 * [Přečtěte si další informace o Azure Functions vazeb HTTP](./functions-bindings-http-webhook.md).
 * [Monitorování Azure Functions](./functions-monitoring.md)
