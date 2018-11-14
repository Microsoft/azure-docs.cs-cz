---
title: Vytvoření funkce, která se integruje s Azure Logic Apps
description: Vytvořte funkci, která se integruje se službami Azure Logic Apps a Azure Cognitive Services za účelem kategorizace mínění ve tweetech a odesílání oznámení, pokud je mínění špatné.
services: functions, logic-apps, cognitive-services
keywords: pracovní postup, cloudové aplikace, cloudové služby, firemní procesy, systémová integrace, integrace podnikových aplikací, enterprise application integration, EAI
author: craigshoemaker
manager: jeconnoc
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: azure-functions
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: cshoe
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 4c9f92f80275d04cd1bab408213fd02abf5c9139
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51279394"
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>Vytvoření funkce, která se integruje s Azure Logic Apps

Azure Functions se integruje s Azure Logic Apps v Návrháři pro Logic Apps. Tato integrace umožňuje využít výpočetní výkon služby Functions v orchestracích s dalšími službami Azure a třetích stran. 

V tomto kurzu se dozvíte, jak pomocí služeb Functions, Logic Apps a Cognitive Services v Azure provést analýzu mínění v příspěvcích na Twitteru. Funkce aktivovaná protokolem HTTP na základě skóre mínění kategorizuje tweety na zelené, žluté nebo červené. V případě rozpoznání špatného mínění se odešle e-mail. 

![obrázek s prvními dvěma korky aplikace v Návrháři pro Logic Apps](media/functions-twitter-email/00-logic-app-overview.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření prostředku rozhraní API služeb Cognitive Services
> * Vytvoření funkce, která kategorizuje mínění ve tweetech
> * Vytvoření aplikace logiky, která se připojí k Twitteru
> * Přidání rozpoznávání mínění do aplikace logiky 
> * Propojení aplikace logiky s funkcí
> * Odeslání e-mailu na základě odpovědi z funkce

## <a name="prerequisites"></a>Požadavky

+ Aktivní účet na [Twitteru](https://twitter.com/). 
+ Účet [Outlook.com](https://outlook.com/) (pro odesílání oznámení).
+ Tento článek využívá jako výchozí bod prostředky, které jste vytvořili v tématu [Vytvoření první funkce na webu Azure Portal](functions-create-first-azure-function.md).  
Pokud jste tento krok zatím neprovedli, vraťte se k němu a vytvořte aplikaci funkcí.

## <a name="create-a-cognitive-services-resource"></a>Vytvoření prostředku služeb Cognitive Services

Rozhraní API služeb Cognitive Services jsou v Azure k dispozici jako samostatné prostředky. K rozpoznávání mínění v monitorovaných tweetech použijte rozhraní API pro analýzu textu.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

3. Klikněte na **AI a Machine Learning** > **Analýza textu**. Potom vytvořte prostředek s použitím nastavení uvedených v tabulce.

    ![Vytvoření stránky prostředku Cognitive](media/functions-twitter-email/01-create-text-analytics.png)

    | Nastavení      |  Navrhovaná hodnota   | Popis                                        |
    | --- | --- | --- |
    | **Název** | MyCognitiveServicesAccnt | Zvolte jedinečný název účtu. |
    | **Umístění** | USA – západ | Použijte umístění, které je k vám nejblíže. |
    | **Cenová úroveň** | F0 | Začněte s nejnižší úrovní. Pokud vyčerpáte všechna volání, proveďte škálování na vyšší úroveň.|
    | **Skupina prostředků** | myResourceGroup | Stejnou skupinu prostředků použijte pro všechny služby v tomto kurzu.|

4. Kliknutím na **Vytvořit** vytvořte prostředek. 

5. Klikněte na **Přehled** a zkopírujte hodnotu **Koncový bod** do textového editoru. Tato hodnota se používá při vytváření připojení k rozhraní API služeb Cognitive Services.

    ![Nastavení služeb Cognitive Services](media/functions-twitter-email/02-cognitive-services.png)

6. V levém navigačním sloupci klikněte na **Klíče**, zkopírujte hodnotu **Klíč 1** a uložte ji do textového editoru. Tento klíč použijete pro připojení aplikace logiky k rozhraní API služeb Cognitive Services. 
 
    ![Klíče služeb Cognitive Services](media/functions-twitter-email/03-cognitive-serviecs-keys.png)

## <a name="create-the-function-app"></a>Vytvoření aplikace funkcí

Funkce poskytují skvělý způsob snižování zátěže úloh zpracování v pracovním postupu aplikací logiky. Tento kurz ke zpracování skóre mínění v tweetech ze služeb Cognitive Services a k vracení hodnoty kategorie používá funkci aktivovanou protokolem HTTP.  

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-an-http-triggered-function"></a>Vytvoření funkce aktivované protokolem HTTP  

1. Rozbalte aplikaci Function App a klikněte na tlačítko **+** vedle položky **Funkce**. Pokud jde o první funkci ve vaší aplikaci funkcí, vyberte **Na portálu**.

    ![Stručný úvod do služby Functions na webu Azure Portal](media/functions-twitter-email/05-function-app-create-portal.png)

2. Dále vyberte **Webhook + API** a klikněte na **Vytvořit**. 

    ![Volba triggeru HTTP](./media/functions-twitter-email/06-function-webhook.png)

3. Nahraďte obsah souboru `run.csx` následujícím kódem a klikněte na **Uložit**:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        string category = "GREEN";
    
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        log.LogInformation(string.Format("The sentiment score received is '{0}'.", requestBody));
    
        double score = Convert.ToDouble(requestBody);
    
        if(score < .3)
        {
            category = "RED";
        }
        else if (score < .6) 
        {
            category = "YELLOW";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(category)
            : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    }
    ```
    Tento kód funkce vrátí barevnou kategorii na základě skóre mínění přijatého v požadavku. 

4. Pokud chcete funkci otestovat, kliknutím na **Test** úplně vpravo rozbalte kartu Test. Jako **Text požadavku** zadejte hodnotu `0.2` a klikněte na **Spustit**. V textu odpovědi se vrátí hodnota **RED** (Červená). 

    ![Test funkce na webu Azure Portal](./media/functions-twitter-email/07-function-test.png)

Teď máte funkci, která kategorizuje skóre mínění. Dále vytvoříte aplikaci logiky, která vaši funkci integruje s vaším účtem na Twitteru a rozhraním API služeb Cognitive Services. 

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky   

1. Klikněte na tlačítko **Nový** v levém horním rohu webu Azure Portal.

2. Klikněte na **Web** > **Aplikace logiky**.
 
3. Potom zadejte hodnotu **Název**, například `TweetSentiment`, a použijte nastavení uvedená v tabulce.

    ![Vytvoření aplikace logiky na webu Azure Portal](./media/functions-twitter-email/08-logic-app-create.png)

    | Nastavení      |  Navrhovaná hodnota   | Popis                                        |
    | ----------------- | ------------ | ------------- |
    | **Název** | TweetSentiment | Zvolte vhodný název vaší aplikace. |
    | **Skupina prostředků** | myResourceGroup | Zvolte stejnou existující skupinu prostředků jako předtím. |
    | **Umístění** | USA – východ | Zvolte umístění, které je blízko vás. |    

4. Po zadání odpovídajících hodnot nastavení klikněte na **Vytvořit** a vytvořte aplikaci logiky. 

5. Po vytvoření aplikace klikněte na novou aplikaci logiky připnutou na řídicí panel. Pak se v Návrháři pro Logic Apps posuňte dolů a klikněte na šablonu **Prázdná aplikace logiky**. 

    ![Šablona Prázdná aplikace logiky](media/functions-twitter-email/09-logic-app-create-blank.png)

Teď můžete pomocí Návrháře pro Logic Apps do své aplikace přidat služby a triggery.

## <a name="connect-to-twitter"></a>Připojení k Twitteru

Nejprve vytvořte připojení ke svému účtu na Twitteru. Aplikace logiky se dotazuje na tweety a tím se aktivuje spuštění aplikace.

1. V návrháři klikněte na službu **Twitter** a pak na trigger **Když se publikuje nový tweet**. Přihlaste se ke svému účtu na Twitteru a povolte službě Logic Apps používat váš účet.

2. Použijte nastavení triggeru Twitteru uvedená v tabulce. 

    ![Nastavení konektoru Twitteru](media/functions-twitter-email/10-tweet-settings.png)

    | Nastavení      |  Navrhovaná hodnota   | Popis                                        |
    | ----------------- | ------------ | ------------- |
    | **Hledaný text** | #Azure | Použijte hashtag, který je dostatečně oblíbený, aby ve zvoleném intervalu generoval nové tweety. Pokud použijete úroveň Free a zvolený hashtag je příliš oblíbený, můžete ve svém rozhraní API služeb Cognitive Services rychle vyčerpat kvótu transakcí. |
    | **Interval** | 15 | Uplynulý čas mezi požadavky na Twitter v jednotkách frekvence. |
    | **Frekvence** | Minuta | Jednotka frekvence použitá pro dotazování Twitteru.  |

3.  Kliknutím na **Uložit** se připojte ke svému účtu na Twitteru. 

Vaše aplikace je teď připojená k Twitteru. Dále se připojíte k rozhraní API pro analýzu textu, které rozpozná mínění ve shromážděných tweetech.

## <a name="add-sentiment-detection"></a>Přidání rozpoznávání mínění

1. Klikněte na **Nový krok** a pak na **Přidat akci**.

2. V části **Vybrat akci** zadejte **Analýza textu** a pak klikněte na akci **Rozpoznávání mínění**.
    
    ![Nový krok a pak Přidat akci](media/functions-twitter-email/11-detect-sentiment.png)

3. Zadejte název připojení, například `MyCognitiveServicesConnection`, vložte klíč rozhraní API služeb Cognitive Services a koncový bod služeb Cognitive Services, které jste si uložili do textového editoru, a klikněte na **Vytvořit**.

    ![Nový krok a pak Přidat akci](media/functions-twitter-email/12-connection-settings.png)

4. Dále do textového pole zadejte **Text tweetu** a klikněte na **Nový krok**.

    ![Definice textu, který se má analyzovat](media/functions-twitter-email/13-analyze-tweet-text.png)

Když je teď nakonfigurované rozpoznávání mínění, můžete do své funkce přidat připojení využívající výstup skóre mínění.

## <a name="connect-sentiment-output-to-your-function"></a>Připojení výstupu mínění k funkci

1. V Návrháři pro Logic Apps klikněte na **Nový krok** > **Přidat akci**, vyfiltrujte **Azure Functions** a klikněte na **Zvolit funkci Azure**.

    ![Rozpoznávání mínění](media/functions-twitter-email/14-azure-functions.png)
  
4. Vyberte aplikaci funkcí, kterou jste vytvořili dříve.

    ![Výběr funkce](media/functions-twitter-email/15-select-function.png)

5. Vyberte funkci, kterou jste vytvořili pro tento kurz.

    ![Výběr funkce](media/functions-twitter-email/16-select-function.png)

4. V části **Text požadavku** klikněte na **Skóre** a pak na **Uložit**.

    ![Skóre](media/functions-twitter-email/17-function-input-score.png)

Vaše funkce se teď aktivuje při odeslání skóre mínění z aplikace logiky. Funkce do aplikace logiky vrátí barevně rozlišenou kategorii. Dále přidáte e-mailové oznámení, které se odešle, když funkce vrátí hodnotu mínění **RED** (Červená). 

## <a name="add-email-notifications"></a>Přidání e-mailového oznámení

Poslední částí pracovního postupu je aktivace e-mailu, když má skóre mínění hodnotu _RED_ (Červená). V tomto tématu se používá konektor Outlook.com. Podobný postup však můžete použít i pro konektor Gmail nebo Office 365 Outlook.   

1. V Návrháři pro Logic Apps klikněte na **Nový krok** > **Přidat podmínku**. 

    ![Přidání podmínky do aplikace logiky](media/functions-twitter-email/18-add-condition.png)

2. Klikněte na **Zvolit hodnotu** a pak na **Text**. Vyberte **se rovná**, klikněte na **Zvolit hodnotu**, zadejte `RED` a klikněte na **Uložit**. 

    ![Výběr akce pro podmínku](media/functions-twitter-email/19-condition-settings.png)    

3. V části **POKUD JE TRUE** klikněte na **Přidat akci**, vyhledejte `outlook.com`, klikněte na **Odeslat e-mail** a přihlaste se ke svému účtu Outlook.com.

    ![Konfigurace e-mailu pro akci Odeslat e-mail](media/functions-twitter-email/20-add-outlook.png)

    > [!NOTE]
    > Pokud nemáte účet Outlook.com, můžete zvolit jiný konektor, například Gmail nebo Office 365 Outlook.

4. V akci **Odeslat e-mail** použijte nastavení e-mailu uvedená v tabulce. 

    ![Konfigurace e-mailu pro akci Odeslat e-mail](media/functions-twitter-email/21-configure-email.png)
    
| Nastavení      |  Navrhovaná hodnota   | Popis  |
| ----------------- | ------------ | ------------- |
| **Komu** | Zadejte svou e-mailovou adresu. | E-mailová adresa, která přijímá oznámení. |
| **Předmět** | Rozpoznáno špatné mínění v tweetu  | Řádek předmětu e-mailového oznámení.  |
| **Text** | Text tweetu, Umístění | Klikněte na parametry **Text tweetu** a **Umístění**. |

5.  Klikněte na **Uložit**.

Když je teď pracovní postup dokončený, můžete aplikaci logiky povolit a podívat se na funkci v akci.

## <a name="test-the-workflow"></a>Test pracovního postupu

1. V Návrháři pro Logic Apps klikněte na **Spustit** a spusťte aplikaci.

2. Kliknutím na **Přehled** v levém sloupci zobrazte stav aplikace logiky. 
 
    ![Stav spuštění aplikace logiky](media/functions-twitter-email/22-execution-history.png)

3. (Volitelné) Kliknutím na některé ze spuštění zobrazte podrobnosti o spuštění.

4. Přejděte ke své funkci, zobrazte protokoly a ověřte příjem a zpracování hodnot mínění.
 
    ![Zobrazení protokolů funkce](media/functions-twitter-email/sent.png)

5. Když se zjistí potenciálně negativní mínění, obdržíte e-mail. Pokud jste žádný e-mail neobdrželi, můžete změnit kód funkce tak, aby pokaždé vracel text RED (Červená):

    ```csharp
    return (ActionResult)new OkObjectResult("RED");
    ```

    Po ověření e-mailových oznámení změňte kód zpět na původní:

    ```csharp
    return requestBody != null
        ? (ActionResult)new OkObjectResult(category)
        : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    ```

    > [!IMPORTANT]
    > Po dokončení tohoto kurzu byste měli aplikaci logiky zakázat. Zakázáním aplikace se vyhnete poplatkům za spouštění a vyčerpání transakcí ve vašem rozhraní API služeb Cognitive Services.

Právě jste viděli, jak snadné je integrovat funkce do pracovního postupu aplikací logiky.

## <a name="disable-the-logic-app"></a>Zákaz aplikace logiky

Pokud chcete aplikaci logiky zakázat, klikněte na **Přehled** a pak v horní části obrazovky klikněte na **Zakázat**. Zakázáním aplikace se zastaví spuštění aplikace a účtování poplatků, aniž by se aplikace odstranila.

![Protokoly funkce](media/functions-twitter-email/disable-logic-app.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření prostředku rozhraní API služeb Cognitive Services
> * Vytvoření funkce, která kategorizuje mínění ve tweetech
> * Vytvoření aplikace logiky, která se připojí k Twitteru
> * Přidání rozpoznávání mínění do aplikace logiky 
> * Propojení aplikace logiky s funkcí
> * Odeslání e-mailu na základě odpovědi z funkce

V dalším kurzu se dozvíte, jak pro svou funkci vytvořit rozhraní API bez serveru.

> [!div class="nextstepaction"] 
> [Vytvoření rozhraní API bez serveru pomocí služby Azure Functions](functions-create-serverless-api.md)

Další informace o službě Logic Apps najdete v tématu [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

