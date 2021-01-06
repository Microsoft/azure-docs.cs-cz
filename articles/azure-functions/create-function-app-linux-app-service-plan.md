---
title: Vytvoření aplikace Function App na platformě Linux z Azure Portal
description: Naučte se, jak vytvořit první funkci Azure Functions na platformě Linux pomocí Azure Portal.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 34a93795b5e041ccef8e9576f97092e16c429444
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937174"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Vytvoření aplikace Function App v systému Linux v plánu Azure App Service

Služba Azure Functions umožňuje hostovat funkce v Linuxu ve výchozím kontejneru Azure App Service. Tento článek vás provede postupem použití [Azure Portal](https://portal.azure.com) k vytvoření aplikace funkce hostované v systému Linux, která běží v [plánu App Service](dedicated-plan.md). Můžete také [použít vlastní kontejner](functions-create-function-linux-custom-image.md).

![Vytvoření aplikace Function App na webu Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com> pomocí svého účtu Azure.

## <a name="create-a-function-app"></a>Vytvoření aplikace funkcí

K hostování provádění funkcí v Linuxu musíte mít aplikaci funkcí. Aplikace funkcí poskytuje prostředí pro provádění kódu funkce. Umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování, škálování a sdílení prostředků. V tomto článku vytvoříte App Service plán při vytváření aplikace Function App.

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

1. Na stránce **Nový** vyberte **COMPUTE**  >  **Function App**.

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-flow.png" alt-text="Vytvoření aplikace funkcí na webu Azure Portal":::

1. Na stránce **základy** použijte nastavení aplikace Function App, jak je uvedeno v následující tabulce.

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace funkcí vytvořena. |
    | **[Skupina prostředků](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. |
    | **Název aplikace funkcí** | Globálně jedinečný název | Název identifikující novou aplikaci funkcí. Platné znaky jsou `a-z` (bez rozlišování malých a velkých písmen), `0-9` a `-`.  |
    |**Publikovat**| **Kód** (výchozí) | Možnost publikování souborů kódu nebo kontejneru Docker |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Vyberte **.NET Core** pro funkce jazyka C# a F #. |
    |**Verze**| Číslo verze | Vyberte verzi nainstalovaného modulu runtime.  |
    |**Oblast**| Upřednostňovaná oblast | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž nebo poblíž dalších služeb, ke kterým máte přístup. |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-basics-linux.png" alt-text="Stránka základy":::

1. Vyberte **Další: Hostování**. Na stránce **hostování** zadejte následující nastavení.

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Účet úložiště](../storage/common/storage-account-create.md)** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete použít i existující účet, který musí splňovat [požadavky na účet úložiště](../azure-functions/storage-considerations.md#storage-account-requirements). |
    |**Operační systém**| **Linux** | Operační systém je předem vybraný pro vás na základě výběru zásobníku modulu runtime, ale v případě potřeby můžete změnit nastavení. |
    | **[Plánování](../azure-functions/functions-scale.md)** | **Využití (bez serverů)** | Plán hostování, který určuje způsob přidělování prostředků aplikaci Function App. Ve výchozím plánu **Využití** se prostředky přidávají dynamicky podle požadavků příslušných funkcí. V tomto hostiteli bez [serveru](https://azure.microsoft.com/overview/serverless-computing/) platíte jenom za čas, kdy se vaše funkce spouštějí. Pokud používáte plán služby App Service, musíte zajistit správu [škálování vaší aplikace funkcí](../azure-functions/functions-scale.md).  |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-hosting-linux.png" alt-text="Stránka hostování":::

1. Vyberte **Další: monitorování**. Na stránce **monitorování** zadejte následující nastavení.

    | Nastavení      | Navrhovaná hodnota  | Popis |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | **Ano** (výchozí) | Vytvoří Application Insights prostředek stejného *názvu aplikace* v nejbližší podporované oblasti. Rozbalením tohoto nastavení nebo výběrem možnosti **vytvořit nový** můžete změnit název Application Insights nebo zvolit jinou oblast v [geografickém umístění Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , kam chcete ukládat data. |

   :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-monitoring-linux.png" alt-text="Stránka monitorování":::

1. Výběrem možnosti **zkontrolovat + vytvořit** zkontrolujte výběry konfigurace aplikace.

1. Na stránce **Revize + vytvořit** zkontrolujte nastavení a pak vyberte **vytvořit** a nasaďte a nasaďte aplikaci Function App.

1. Vyberte ikonu **oznámení** v pravém horním rohu portálu a sledujte zprávu o **úspěšném nasazení** .

1. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte. Můžete také vybrat **Připnout na řídicí panel**. Připnutí usnadňuje návrat k tomuto prostředku Function App z vašeho řídicího panelu.

    ![Oznámení o nasazení](./media/create-function-app-linux-app-service-plan/function-app-create-notification2.png)

    I po tom, co je aplikace Function App dostupná, může trvat několik minut, než se plně inicializuje.

Dál vytvoříte v nové aplikaci Function App funkci.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Vytvoření funkce triggeru HTTP

V této části se dozvíte, jak vytvořit funkci ve vaší nové aplikaci Function App na portálu.

> [!NOTE]
> Prostředí pro vývoj na portálu může být užitečné při vyzkoušení Azure Functions. V případě většiny scénářů zvažte místní vývoj funkcí a publikování projektu do aplikace Function App pomocí [Visual Studio Code](./create-first-function-vs-code-csharp.md#create-an-azure-functions-project) nebo [Azure Functions Core Tools](functions-run-local.md#create-a-local-functions-project).  

1. V levé nabídce okna **Functions (funkce** ) vyberte **funkce** a pak v horní nabídce vyberte **Přidat** . 
 
1. V **novém okně funkce** vyberte **Trigger http**.

    ![Zvolit funkci triggeru HTTP](./media/create-function-app-linux-app-service-plan/function-app-select-http-trigger.png)

1. V okně **Nová funkce** přijměte výchozí název **nové funkce** nebo zadejte nový název. 

1. V rozevíracím seznamu **úroveň autorizace** zvolte **anonymní** a pak vyberte **vytvořit funkci**.

    Azure vytvoří funkci triggeru HTTP. Novou funkci můžete spustit odesláním požadavku HTTP.

## <a name="test-the-function"></a>Testování funkce

1. V nové funkci triggeru HTTP v nabídce vlevo vyberte **Code + test** a potom v horní nabídce vyberte **získat adresu URL funkce** .

    ![Vyberte získat adresu URL funkce](./media/create-function-app-linux-app-service-plan/function-app-select-get-function-url.png)

1. V dialogovém okně **získat adresu URL funkce** vyberte v rozevíracím seznamu možnost **výchozí** a potom vyberte ikonu **Kopírovat do schránky** . 

    ![Kopírování adresy URL funkce z webu Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

1. Vložte adresu URL funkce do panelu Adresa vašeho prohlížeče. Na `?name=<your_name>` konec této adresy URL přidejte hodnotu řetězce dotazu a stisknutím klávesy ENTER spusťte požadavek. 

    Následující příklad ukazuje odpověď v prohlížeči:

    ![Odezva funkce v prohlížeči.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    Adresa URL požadavku obsahuje klíč, který je ve výchozím nastavení nezbytný pro přístup k funkci přes protokol HTTP.

1. Při spuštění funkce se do protokolů zaznamenávají informace o trasování. Chcete-li zobrazit výstup trasování, vraťte se na stránku **Code + test** na portálu a rozbalte šipku **protokoly** v dolní části stránky.

   ![Prohlížeč protokolu funkcí na webu Azure Portal.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Vytvořili jste aplikaci funkcí s jednoduchou funkcí triggeru HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Další informace najdete v tématu [Vazby protokolu HTTP služby Azure Functions](functions-bindings-http-webhook.md).