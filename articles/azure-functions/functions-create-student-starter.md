---
title: Vytvoření funkce pomocí Azure for Students Starter
description: Přečtěte si, jak vytvořit funkci Azure z předplatného Azure for Student Starter
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
author: alexkarcher-msft
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: 189a0bc80ab2894b2888b496b3beb45249d859f6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75921137"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Vytvoření funkce pomocí Azure for Students Starter

V tomto kurzu vytvoříme funkci HTTP "hello world" v předplatném Azure for Students Starter. V tomto typu předplatného také probereme, co je dostupné v Azure Functions.

Microsoft *Azure for Students Starter* vám zachce začít s produkty Azure, které potřebujete vyvíjet v cloudu, a to bez nákladů. [Další informace o této nabídce naleznete zde.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Služba Azure Functions umožňuje spuštění kódu v prostředí [bez serveru](https://azure.microsoft.com/solutions/serverless/), aniž byste nejdřív museli vytvořit virtuální počítač nebo publikovat webovou aplikaci. [Další informace o funkcích naleznete zde.](./functions-overview.md)

## <a name="create-a-function"></a>Vytvoření funkce

 V tomto tématu se dozvíte, jak pomocí funkce funkce vytvořit funkci HTTP aktivovanou "hello world" na webu Azure Portal.

![Vytvoření aplikace Function App na webu Azure Portal](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com> pomocí svého účtu Azure.

## <a name="create-a-function-app"></a>Vytvoření Function App

K hostování provádění funkcí musíte mít aplikaci Function App. Aplikace funkce umožňuje seskupit funkce jako logická jednotka pro snadnější správu, nasazení, škálování a sdílení prostředků.

1. Vyberte tlačítko **Vytvořit prostředek,** které se nachází v levém horním rohu portálu Azure. Pak vyberte **aplikaci výpočetní** > **funkce**.

    ![Vytvoření aplikace Function App na portálu Azure Portal](./media/functions-create-student-starter/function-app-create-flow.png)

2. Použijte nastavení aplikace Function App uvedená v tabulce pod obrázkem.

    <img src="./media/functions-create-student-starter/Function-create-start.png" width="315">

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název aplikace** | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky jsou `a-z`, `0-9` a `-`.  | 
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace Function App vytvořena. | 
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. |
   | **[Plán/umístění služby App Service](./functions-scale.md)** | Nová | Plán hostování, který určuje, do jaké oblasti se aplikace funkce nasadí a hustota vašich prostředků. Více aplikací funkcí nasazených do stejného plánu bude všechny sdílet stejnou jednu bezplatnou instanci. Jedná se o omezení plánu Student Starter. Plné možnosti hostingu jsou [vysvětleny zde.](./functions-scale.md)|
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Zvolte **.NET** pro funkce C# a F#. |
    |**[Application Insights](./functions-monitoring.md)**| Povoleno | Application Insights se používá k ukládání a analýze protokolů aplikace funkce. Je ve výchozím nastavení povolena, pokud zvolíte umístění, které podporuje Application Insights. Application Insights lze povolit pro libovolnou funkci ručním výběrem blízké oblasti pro nasazení Application Insights. Bez Application Insights, budete moci zobrazit pouze živé streamování protokoly.

3. Chcete-li zvolit jiné umístění, vyberte výše uvedený **plán služby App Service/umístění.**

4. Vyberte **Vytvořit nový** a pak dát svému plánu jedinečný název.

5. Vyberte umístění, které je vám nejblíže. [Tady najdete celou mapu oblastí Azure.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. Aplikaci Function App zřídíte a nasadíte kliknutím na **Vytvořit**.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Vyberte ikonu oznámení v pravém horním rohu portálu a sledujte, kdy se objeví zpráva, že **nasazení bylo úspěšné**.

    ![Určení nastavení nové aplikace Function App](./media/functions-create-student-starter/function-app-create-notification.png)

8. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte.

Dál vytvoříte v nové aplikaci Function App funkci.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Vytvoření funkce aktivované protokolem HTTP

1. Rozbalte novou aplikaci funkcí, pak **+** vyberte tlačítko vedle **položky Funkce**, zvolte **In-portal**a vyberte **Pokračovat**.

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

V Azure for Students Starter máte přístup k většině funkcí runtime Funkce Azure, s několika klíčovými omezeními uvedenými níže:

* Aktivační událost PROTOKOLU HTTP je jediným podporovaným typem aktivační události.
    * Všechny vstupy a všechny výstupní vazby jsou podporovány! [Celý seznam naleznete zde.](functions-triggers-bindings.md)
* Podporované jazyky: 
    * C# (.NET Core 2)
    * JavaScript (Node.js 8 & 10)
    * F# (.NET Core 2)
    * [Viz jazyky podporované ve vyšších plánech zde](supported-languages.md)
* Systém Windows je jediným podporovaným operačním systémem.
* Škálování je omezeno na [jednu bezplatnou instanci úrovně,](https://azure.microsoft.com/pricing/details/app-service/windows/) která běží až 60 minut každý den. Budete serverlessly škálovat od 0 do 1 instance automaticky jako přenos HTTP je přijata, ale žádné další.
* Je podporována pouze [verze 2.x a novější](functions-versions.md) modul runtime Funkce.
* Všechny vývojářské nástroje jsou podporovány pro úpravy a publikování funkcí. To zahrnuje VS Code, Visual Studio, Azure CLI a portál Azure. Pokud chcete použít něco jiného než portál, budete muset nejprve vytvořit aplikaci na portálu a pak ji v preferovaném nástroji vybrat jako cíl nasazení.

## <a name="next-steps"></a>Další kroky

Vytvořili jste aplikaci funkcí s jednoduchou funkcí spouštěnou http! Nyní můžete prozkoumat místní nástroje, více jazyků, monitorování a integrace.

 * [Vytvoření první funkce pomocí sady Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Vytvoření první funkce pomocí Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Průvodce vývojářem JavaScriptu azure funkce](./functions-reference-node.md)
 * [Připojení k databázi Azure SQL pomocí funkcí Azure](./functions-scenario-database-table-cleanup.md)
 * [Další informace o vazbách HTTP Azure Functions](./functions-bindings-http-webhook.md).
 * [Sledování funkcí Azure](./functions-monitoring.md)
