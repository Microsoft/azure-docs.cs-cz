---
title: Vytvoření funkce, která se integruje s Azure Logic Apps | Microsoft Docs
description: Vytvořte funkci, která se integruje se službami Azure Logic Apps a Azure Cognitive Services za účelem kategorizace mínění ve tweetech a odesílání oznámení, pokud je mínění špatné.
services: functions, logic-apps, cognitive-services
keywords: pracovní postup, cloudové aplikace, cloudové služby, firemní procesy, systémová integrace, integrace podnikových aplikací, enterprise application integration, EAI
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 16a46b4c49687186e25c399dcc2c5c168e7c5004
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
ms.locfileid: "30284097"
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>Vytvoření funkce, která se integruje s Azure Logic Apps

Azure Functions se integruje s Azure Logic Apps v Návrháři pro Logic Apps. Tato integrace umožňuje využít výpočetní výkon služby Functions v orchestracích s dalšími službami Azure a třetích stran. 

V tomto kurzu se dozvíte, jak pomocí služeb Functions, Logic Apps a Microsoft Cognitive Services v Azure analyzovat mínění v příspěvcích na Twitteru. Funkce aktivovaná protokolem HTTP na základě skóre mínění kategorizuje tweety na zelené, žluté nebo červené. V případě rozpoznání špatného mínění se odešle e-mail. 

![obrázek s prvními dvěma korky aplikace v Návrháři pro Logic Apps](media/functions-twitter-email/designer1.png)

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
+ Toto téma používá jako výchozí bod prostředky, které jste vytvořili v kroku [Vytvoření první funkce na portálu Azure Portal](functions-create-first-azure-function.md).  
Pokud jste tento krok zatím neprovedli, vraťte se k němu a vytvořte aplikaci funkcí.

## <a name="create-a-cognitive-services-resource"></a>Vytvoření prostředku služeb Cognitive Services

Rozhraní API služeb Cognitive Services jsou v Azure k dispozici jako samostatné prostředky. K rozpoznávání mínění v monitorovaných tweetech použijte rozhraní API pro analýzu textu.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

3. Klikněte na **Application Insights a analýzy** > **Rozhraní API pro analýzu textu**. Pak použijte nastavení uvedená v tabulce, přijměte podmínky a zaškrtněte možnost **Připnout na řídicí panel**.

    ![Vytvoření stránky prostředku Cognitive](media/functions-twitter-email/cog_svcs_resource.png)

    | Nastavení      |  Navrhovaná hodnota   | Popis                                        |
    | --- | --- | --- |
    | **Název** | MyCognitiveServicesAccnt | Zvolte jedinečný název účtu. |
    | **Umístění** | Západní USA | Použijte umístění, které je k vám nejblíže. |
    | **Cenová úroveň** | F0 | Začněte s nejnižší úrovní. Pokud vyčerpáte všechna volání, proveďte škálování na vyšší úroveň.|
    | **Skupina prostředků** | myResourceGroup | Stejnou skupinu prostředků použijte pro všechny služby v tomto kurzu.|

4. Kliknutím na **Vytvořit** vytvořte prostředek. Po vytvoření vyberte nový prostředek služeb Cognitive Services připnutý na řídicí panel. 

5. V levém navigačním sloupci klikněte na **Klíče** a zkopírujte a uložte hodnotu **Klíč 1**. Tento klíč použijete pro připojení aplikace logiky k rozhraní API služeb Cognitive Services. 
 
    ![Klíče](media/functions-twitter-email/keys.png)

## <a name="create-the-function-app"></a>Vytvoření aplikace funkcí

Funkce poskytují skvělý způsob snižování zátěže úloh zpracování v pracovním postupu aplikací logiky. Tento kurz ke zpracování skóre mínění v tweetech ze služeb Cognitive Services a k vracení hodnoty kategorie používá funkci aktivovanou protokolem HTTP.  

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-an-http-triggered-function"></a>Vytvoření funkce aktivované protokolem HTTP  

1. Rozbalte aplikaci Function App a klikněte na tlačítko **+** vedle položky **Funkce**. Pokud jde o první funkci ve vaší aplikaci Function App, vyberte možnost **Vlastní funkce**. Zobrazí se kompletní sada šablon funkcí.

    ![Stručný úvod do služby Functions na webu Azure Portal](media/functions-twitter-email/add-first-function.png)

2. Do vyhledávacího pole zadejte `http` a zvolte pro šablonu triggeru HTTP **jazyk C#**. 

    ![Volba triggeru HTTP](./media/functions-twitter-email/select-http-trigger-portal.png)

3. Zadejte **Název** funkce, jako **[Úroveň ověřování](functions-bindings-http-webhook.md#http-auth)** zvolte `Function` a pak vyberte **Vytvořit**. 

    ![Vytvoření funkce aktivované protokolem HTTP](./media/functions-twitter-email/select-http-trigger-portal-2.png)

    Tím se vytvoří funkce skriptu jazyka C# pomocí šablony triggeru HTTP. Váš kód se zobrazí v novém okně jako `run.csx`.

4. Nahraďte obsah souboru `run.csx` následujícím kódem a klikněte na **Uložit**:

    ```csharp
    using System.Net;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // The sentiment category defaults to 'GREEN'. 
        string category = "GREEN";
    
        // Get the sentiment score from the request body.
        double score = await req.Content.ReadAsAsync<double>();
        log.Info(string.Format("The sentiment score received is '{0}'.",
                    score.ToString()));
    
        // Set the category based on the sentiment score.
        if (score < .3)
        {
            category = "RED";
        }
        else if (score < .6)
        {
            category = "YELLOW";
        }
        return req.CreateResponse(HttpStatusCode.OK, category);
    }
    ```
    Tento kód funkce vrátí barevnou kategorii na základě skóre mínění přijatého v požadavku. 

4. Pokud chcete funkci otestovat, kliknutím na **Test** úplně vpravo rozbalte kartu Test. Jako **Text požadavku** zadejte hodnotu `0.2` a klikněte na **Spustit**. V textu odpovědi se vrátí hodnota **RED** (Červená). 

    ![Test funkce na webu Azure Portal](./media/functions-twitter-email/test.png)

Teď máte funkci, která kategorizuje skóre mínění. Dále vytvoříte aplikaci logiky, která vaši funkci integruje s vaším účtem na Twitteru a rozhraním API služeb Cognitive Services. 

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky   

1. Klikněte na tlačítko **Nový** v levém horním rohu webu Azure Portal.

2. Klikněte na **Podniková integrace** > **Aplikace logiky**. Pak použijte nastavení uvedená v tabulce, zaškrtněte možnost **Připnout na řídicí panel** a klikněte na **Vytvořit**.
 
4. Pak zadejte **Název**, například `TweetSentiment`, použijte nastavení uvedená v tabulce, přijměte podmínky a zaškrtněte možnost **Připnout na řídicí panel**.

    ![Vytvoření aplikace logiky na webu Azure Portal](./media/functions-twitter-email/new_logic_app.png)

    | Nastavení      |  Navrhovaná hodnota   | Popis                                        |
    | ----------------- | ------------ | ------------- |
    | **Název** | TweetSentiment | Zvolte vhodný název vaší aplikace. |
    | **Skupina prostředků** | myResourceGroup | Zvolte stejnou existující skupinu prostředků jako předtím. |
    | **Umístění** | Východ USA | Zvolte umístění, které je blízko vás. |    

4. Zvolte **Připnout na řídicí panel** a pak kliknutím na **Vytvořit** vytvořte aplikaci logiky. 

5. Po vytvoření aplikace klikněte na novou aplikaci logiky připnutou na řídicí panel. Pak se v Návrháři pro Logic Apps posuňte dolů a klikněte na šablonu **Prázdná aplikace logiky**. 

    ![Šablona Prázdná aplikace logiky](media/functions-twitter-email/blank.png)

Teď můžete pomocí Návrháře pro Logic Apps do své aplikace přidat služby a triggery.

## <a name="connect-to-twitter"></a>Připojení k Twitteru

Nejprve vytvořte připojení ke svému účtu na Twitteru. Aplikace logiky se dotazuje na tweety a tím se aktivuje spuštění aplikace.

1. V návrháři klikněte na službu **Twitter** a pak na trigger **Když se publikuje nový tweet**. Přihlaste se ke svému účtu na Twitteru a povolte službě Logic Apps používat váš účet.

2. Použijte nastavení triggeru Twitteru uvedená v tabulce. 

    ![Nastavení konektoru Twitteru](media/functions-twitter-email/azure_tweet.png)

    | Nastavení      |  Navrhovaná hodnota   | Popis                                        |
    | ----------------- | ------------ | ------------- |
    | **Hledaný text** | #Azure | Použijte hashtag, který je dostatečně oblíbený, aby ve zvoleném intervalu generoval nové tweety. Pokud použijete úroveň Free a zvolený hashtag je příliš oblíbený, můžete ve svém rozhraní API služeb Cognitive Services rychle vyčerpat kvótu transakcí. |
    | **Frekvence** | Minuta | Jednotka frekvence použitá pro dotazování Twitteru.  |
    | **Interval** | 15 | Uplynulý čas mezi požadavky na Twitter v jednotkách frekvence. |

3.  Kliknutím na **Uložit** se připojte ke svému účtu na Twitteru. 

Vaše aplikace je teď připojená k Twitteru. Dále se připojíte k rozhraní API pro analýzu textu, které rozpozná mínění ve shromážděných tweetech.

## <a name="add-sentiment-detection"></a>Přidání rozpoznávání mínění

1. Klikněte na **Nový krok** a pak na **Přidat akci**.

    ![Nový krok a pak Přidat akci](media/functions-twitter-email/new_step.png)

2. V části **Zvolit akci** klikněte na **Analýza textu** a pak klikněte na **Rozpoznávání mínění**.

    ![Rozpoznávání mínění](media/functions-twitter-email/detect_sent.png)

3. Zadejte název připojení, například `MyCognitiveServicesConnection`, vložte uložený klíč vašeho rozhraní API služeb Cognitive Services a klikněte na **Vytvořit**.  

4. Klikněte na **Text, který se má analyzovat** > **Text tweetu** a pak klikněte na **Uložit**.  

    ![Rozpoznávání mínění](media/functions-twitter-email/ds_tta.png)

Když je teď nakonfigurované rozpoznávání mínění, můžete do své funkce přidat připojení využívající výstup skóre mínění.

## <a name="connect-sentiment-output-to-your-function"></a>Připojení výstupu mínění k funkci

1. V Návrháři pro Logic Apps klikněte na **Nový krok** > **Přidat akci** a pak klikněte na **Azure Functions**. 

2. Klikněte na **Zvolit funkci Azure** a vyberte funkci **CategorizeSentiment**, kterou jste vytvořili dříve.  

    ![Pole funkce Azure s textem Zvolit funkci Azure](media/functions-twitter-email/choose_fun.png)

3. V části **Text požadavku** klikněte na **Skóre** a pak na **Uložit**.

    ![Skóre](media/functions-twitter-email/trigger_score.png)

Vaše funkce se teď aktivuje při odeslání skóre mínění z aplikace logiky. Funkce do aplikace logiky vrátí barevně rozlišenou kategorii. Dále přidáte e-mailové oznámení, které se odešle, když funkce vrátí hodnotu mínění **RED** (Červená). 

## <a name="add-email-notifications"></a>Přidání e-mailového oznámení

Poslední částí pracovního postupu je aktivace e-mailu, když má skóre mínění hodnotu _RED_ (Červená). V tomto tématu se používá konektor Outlook.com. Podobný postup však můžete použít i pro konektor Gmail nebo Office 365 Outlook.   

1. V Návrháři pro Logic Apps klikněte na **Nový krok** > **Přidat podmínku**. 

2. Klikněte na **Zvolit hodnotu** a pak na **Text**. Vyberte **se rovná**, klikněte na **Zvolit hodnotu**, zadejte `RED` a klikněte na **Uložit**. 

    ![Přidání podmínky do aplikace logiky](media/functions-twitter-email/condition.png)

3. V části **POKUD JE TRUE** klikněte na **Přidat akci**, vyhledejte `outlook.com`, klikněte na **Odeslat e-mail** a přihlaste se ke svému účtu Outlook.com.
    
    ![Výběr akce pro podmínku](media/functions-twitter-email/outlook.png)

    > [!NOTE]
    > Pokud nemáte účet Outlook.com, můžete zvolit jiný konektor, například Gmail nebo Office 365 Outlook.

4. V akci **Odeslat e-mail** použijte nastavení e-mailu uvedená v tabulce. 

    ![Konfigurace e-mailu pro akci Odeslat e-mail](media/functions-twitter-email/send_email.png)

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
 
    ![Stav spuštění aplikace logiky](media/functions-twitter-email/over1.png)

3. (Volitelné) Kliknutím na některé ze spuštění zobrazte podrobnosti o spuštění.

4. Přejděte ke své funkci, zobrazte protokoly a ověřte příjem a zpracování hodnot mínění.
 
    ![Zobrazení protokolů funkce](media/functions-twitter-email/sent.png)

5. Když se zjistí potenciálně negativní mínění, obdržíte e-mail. Pokud jste žádný e-mail neobdrželi, můžete změnit kód funkce tak, aby pokaždé vracel text RED (Červená):

        return req.CreateResponse(HttpStatusCode.OK, "RED");

    Po ověření e-mailových oznámení změňte kód zpět na původní:

        return req.CreateResponse(HttpStatusCode.OK, category);

    > [!IMPORTANT]
    > Po dokončení tohoto kurzu byste měli aplikaci logiky zakázat. Zakázáním aplikace se vyhnete poplatkům za spouštění a vyčerpání transakcí ve vašem rozhraní API služeb Cognitive Services.

Právě jste viděli, jak snadné je integrovat funkce do pracovního postupu aplikací logiky.

## <a name="disable-the-logic-app"></a>Zákaz aplikace logiky

Pokud chcete aplikaci logiky zakázat, klikněte na **Přehled** a pak v horní části obrazovky klikněte na **Zakázat**. Tím se zastaví spuštění aplikace logiky a účtování poplatků, aniž by se aplikace odstranila. 

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

