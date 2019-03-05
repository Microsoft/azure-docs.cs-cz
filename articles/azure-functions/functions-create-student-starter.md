---
title: Vytvoření funkce pomocí Azure for Students Starter | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit funkci Azure Functions z v rámci předplatné Azure for Student Starter ještě dnes
Customer intent: As a student, I want to be able to create a HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
services: functions
documentationcenter: na
author: alexkarcher-msft
manager: ggailey777
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: 5fd9f51185b8461269d498b254d9e9d9f4118270
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344592"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Vytvoření funkce pomocí Azure for Students Starter

V tomto kurzu vytvoříme HTTP funkci hello world v předplatné Azure for Students Starter. Také projdeme co je dostupné ve službě Azure Functions na tento typ předplatného.

Microsoft *Azure for Students Starter* vám pomůže začít s produkty Azure, které potřebujete pro vývoj v cloudu bezplatně pro vás. [Další informace o této nabídce tady.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Služba Azure Functions umožňuje spuštění kódu v prostředí [bez serveru](https://azure.microsoft.com/solutions/serverless/), aniž byste nejdřív museli vytvořit virtuální počítač nebo publikovat webovou aplikaci. [Další informace o funkcích najdete tady.](./functions-overview.md)

## <a name="create-a-function"></a>Vytvoření funkce

 V tomto tématu zjistěte, jak pomocí služby Functions vytvořit funkci aktivovanou protokolem HTTP "hello world" na webu Azure Portal.

![Vytvoření aplikace Function App na webu Azure Portal](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

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
   | **[Plán služby App Service/umístění](./functions-scale.md)** | Nová | Hostování plánování, které řídí, jaké oblasti nasazuje se do aplikace function app a s velkou hustotou vašich prostředků. Všechny více aplikací funkcí, které jsou nasazené na stejný plán, který bude sdílet stejnou instanci jeden bezplatný. Toto je omezení plánu Student Starter ještě dnes. Úplné možnosti hostování jsou [je popsáno zde.](./functions-scale.md)|
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Zvolte **.NET** pro funkce C# a F#. |
    |**[Application Insights](./functions-monitoring.md)**| Povoleno | Application Insights se používá k ukládání a analýza protokolů aplikace funkcí. Pokud se rozhodnete umístění, které podporuje Application Insights je povolen ve výchozím nastavení. Application Insights můžete povolit pro všechny funkce ručně výběrem nejbližší oblast k nasazení služby Application Insights. Bez služby Application Insights pouze budete moct zobrazit protokoly pro živé streamování.

3. Vyberte **plán služby App Service/umístění** výše a vyberte jiné umístění

4. Vyberte **vytvořit nový** a pak zadejte jedinečný název vašeho plánu.

5. Vyberte umístění co nejblíže k vám. [Zobrazit úplné mapa oblastí Azure tady.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. Aplikaci Function App zřídíte a nasadíte kliknutím na **Vytvořit**.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Vyberte ikonu oznámení v pravém horním rohu portálu a sledujte, kdy se objeví zpráva, že **nasazení bylo úspěšné**.

    ![Určení nastavení nové aplikace Function App](./media/functions-create-student-starter/function-app-create-notification.png)

8. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte.

> [!TIP]
> Pokud máte potíže najít na portálu své aplikace funkcí, zkuste [přidat aplikace funkcí mezi oblíbené na webu Azure Portal](./functions-how-to-use-azure-function-app-settings.md#favorite).
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

## <a name="supported-features-in-azure-for-students-starter"></a>Funkce podporované v Azure for Students Starter

V Azure for Student Starter ještě dnes máte přístup k většině funkcí modulu runtime Azure Functions s několika klíčových omezení uvedených níže:

* HTTP trigger je pouze typ aktivační události, které jsou podporovány.
    * Všechny vstupy a veškerý výstup, jsou podporována vazby! [Podívejte se na seznam tady.](functions-triggers-bindings.md)
* Podporované jazyky: 
    * C#(.NET core 2)
    * JavaScript (Node.js 8 a 10)
    * F#(.NET core 2)
    * [Jazyky podporované v vyšší plány zde naleznete v tématu](supported-languages.md)
* Windows je jediný podporovaný operační systém.
* Škálování je omezen na [jedné instance na úrovni free](https://azure.microsoft.com/pricing/details/app-service/windows/) běží až 60 minut, než se každý den. Bude serverlessly převedete od 0 do 1 instance automaticky jako přijaté přenosy HTTP, ale žádné další.
* Pouze [modul runtime verze 2.x](functions-versions.md) je podporována.
* Všechny nástroje pro vývojáře se podporuje pro úpravy a publikování funkce. To zahrnuje VS Code, Visual Studio, Azure CLI a webu Azure portal. Pokud chcete použít jinou než na portálu, musíte nejprve vytvořit aplikaci na portálu a pak zvolte tuto aplikaci jako cíl nasazení v váš preferovaný nástroj.

## <a name="next-steps"></a>Další postup

Vytvoříte aplikaci function app s jednoduchou funkcí aktivovanou protokolem HTTP. Teď si můžete projít, místní nástroje, další jazyky, sledování a integrace.

 * [Vytvoření první funkce pomocí sady Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Vytvoření první funkce pomocí nástroje Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Příručka pro vývojáře Azure Functions JavaScript](./functions-reference-node.md)
 * [Pomocí služby Azure Functions pro připojení ke službě Azure SQL Database](./functions-scenario-database-table-cleanup.md)
 * [Další informace o vazby protokolu HTTP služby Azure Functions](./functions-bindings-http-webhook.md).
 * [Monitorování Azure Functions](./functions-monitoring.md)
