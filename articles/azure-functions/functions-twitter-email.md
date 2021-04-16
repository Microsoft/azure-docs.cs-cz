---
title: Vytvoření funkce, která se integruje s Azure Logic Apps
description: Vytvoření funkce, která se integruje s Azure Logic Apps a Azure Cognitive Services Výsledný pracovní postup zařadí do kategorií ve zabarvení zprávy e-mailová oznámení.
author: craigshoemaker
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.topic: tutorial
ms.date: 04/10/2021
ms.author: cshoe
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47
ms.openlocfilehash: 3517835859de82117de07ad67cdf8027960ab777
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388643"
---
# <a name="tutorial-create-a-function-to-integrate-with-azure-logic-apps"></a>Kurz: vytvoření funkce pro integraci s Azure Logic Apps

Azure Functions se integruje s Azure Logic Apps v Návrháři pro Logic Apps. Tato integrace umožňuje využít výpočetní výkon funkcí v orchestraci s ostatními službami Azure a službami třetích stran.

V tomto kurzu se dozvíte, jak vytvořit pracovní postup pro analýzu aktivity Twitteru. Po vyhodnocení tweety pracovní postup odešle oznámení, když budou zjištěny pozitivní zabarvení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření prostředku rozhraní API služeb Cognitive Services
> * Vytvoření funkce, která kategorizuje mínění ve tweetech
> * Vytvoření aplikace logiky, která se připojí k Twitteru
> * Přidání rozpoznávání mínění do aplikace logiky
> * Propojení aplikace logiky s funkcí
> * Odeslání e-mailu na základě odpovědi z funkce

## <a name="prerequisites"></a>Požadavky

* Aktivní účet na [Twitteru](https://twitter.com/).
* Účet [Outlook.com](https://outlook.com/) (pro odesílání oznámení).

> [!NOTE]
> Pokud chcete použít konektor Gmail, můžou tento konektor používat jenom obchodní účty G-Suite bez omezení v Logic Apps. Máte-li účet příjemce Gmail, můžete použít konektor Gmail s pouze konkrétními aplikacemi a službami, které jsou schváleny pro Google, nebo můžete [vytvořit klientskou aplikaci Google, která bude použita pro ověřování v konektoru Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). <br><br>Další informace najdete v tématu [zásady zabezpečení a ochrany osobních údajů pro konektory Google v Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

## <a name="create-text-analytics-resource"></a>Vytvořit prostředek Analýza textu

Rozhraní API služeb Cognitive Services jsou v Azure k dispozici jako samostatné prostředky. Použijte rozhraní API pro analýzu textu k detekci mínění publikovaných tweety.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.

1. V části _kategorie_ vyberte **AI + Machine Learning**

1. V části _Analýza textu_ vyberte **vytvořit**.

1. Na obrazovce _vytvořit analýza textu_ zadejte následující hodnoty.

    | Nastavení | Hodnota | Poznámky |
    | ------- | ----- | ------- |
    | Předplatné | Název vašeho předplatného Azure | |
    | Skupina prostředků | Vytvoření nové skupiny prostředků s názvem **promínění-prokurzů** | Později odstraníte tuto skupinu prostředků, abyste odebrali všechny prostředky vytvořené v rámci tohoto kurzu. |
    | Oblast | Vyberte oblast, která je k vám nejblíže | |
    | Name | **TweetSentimentApp** | |
    | Cenová úroveň | Vybrat **bezplatné F0** | |

1. Vyberte **Zkontrolovat a vytvořit**.

1. Vyberte **Vytvořit**.

1. Po dokončení nasazení vyberte **Přejít k prostředku**.

## <a name="get-text-analytics-settings"></a>Získat nastavení Analýza textu

Když se vytvoří prostředek Analýza textu, zkopírujete několik nastavení a nastavíte je pro pozdější použití.

1. Vyberte **klíče a koncový bod**.

1. Kliknutím na ikonu na konci vstupního pole zkopírujte **klíč 1** .

1. Vložte hodnotu do textového editoru.

1. Kliknutím na ikonu na konci vstupního pole zkopírujte **koncový bod** .

1. Vložte hodnotu do textového editoru.

## <a name="create-the-function-app"></a>Vytvoření aplikace funkcí

1. V horním vyhledávacím poli vyhledejte a vyberte **aplikace Function App**.

1. Vyberte **Vytvořit**.

1. Zadejte následující hodnoty.

    | Nastavení | Navrhovaná hodnota | Poznámky |
    | ------- | ----- | ------- |
    | Předplatné | Název vašeho předplatného Azure | |
    | Skupina prostředků | **mínění – kurz** | V rámci tohoto kurzu použijte stejný název skupiny prostředků. |
    | Název aplikace funkcí | **TweetSentimentAPI** + jedinečná Přípona | Názvy aplikací funkcí jsou globálně jedinečné. Platné znaky jsou `a-z` (bez rozlišování malých a velkých písmen), `0-9` a `-`. |
    | Publikování | **Kód** | |
    | Zásobník modulu runtime | **.NET** | Kód funkce, který je k dispozici, je v jazyce C#. |
    | Verze | Vyberte nejnovější číslo verze. | |
    | Oblast | Vyberte oblast, která je k vám nejblíže | |

1. Vyberte **Zkontrolovat a vytvořit**.

1. Vyberte **Vytvořit**.

1. Po dokončení nasazení vyberte **Přejít k prostředku**.

## <a name="create-an-http-triggered-function"></a>Vytvoření funkce aktivované protokolem HTTP  

1. V levé nabídce okna _Functions (funkce_ ) vyberte **funkce**.

1. V horní nabídce vyberte **Přidat** a zadejte následující hodnoty.

    | Nastavení | Hodnota | Poznámky |
    | ------- | ----- | ------- |
    | Vývojové prostředí | **Vývoj na portálu** | |
    | Template (Šablona) | **Aktivační událost HTTP** | |
    | Nová funkce | **TweetSentimentFunction** | Toto je název vaší funkce. |
    | Úroveň autorizace | **Funkce** | |

1. Vyberte tlačítko **Přidat** .

1. Vyberte tlačítko **kód + test** .

1. Vložte následující kód do okna Editor kódu.

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
    
        string requestBody = String.Empty;
        using (StreamReader streamReader =  new  StreamReader(req.Body))
        {
            requestBody = await streamReader.ReadToEndAsync();
        }
    
        dynamic score = JsonConvert.DeserializeObject(requestBody);
        string value = "Positive";
    
        if(score < .3)
        {
            value = "Negative";
        }
        else if (score < .6) 
        {
            value = "Neutral";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(value)
           : new BadRequestObjectResult("Pass a sentiment score in the request body.");
    }
    ```

    Do funkce se předává mínění skóre, které vrátí název kategorie pro hodnotu.

1. Kliknutím na tlačítko **Uložit** na panelu nástrojů uložte změny.

    > [!NOTE]
    > Pokud chcete funkci otestovat, v horní nabídce vyberte **test/Run** . Na kartě _vstup_ zadejte hodnotu `0.9` do pole vstup _těla_ a pak vyberte **Spustit**. Ověřte, že se v poli _obsah odpovědi HTTP_ v části _výstup_ vrátí hodnota _kladné_ .

V dalším kroku vytvoříte aplikaci logiky, která se integruje s Azure Functions, Twitterem a rozhraním Cognitive Services API.

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

1. V horním vyhledávacím poli vyhledejte a vyberte **Logic Apps**.

1. Vyberte **Přidat**.

1. Vyberte možnost **spotřebovat** a zadejte následující hodnoty.

    | Nastavení | Navrhovaná hodnota |
    | ------- | --------------- |
    | Předplatné | Název vašeho předplatného Azure |
    | Skupina prostředků | **mínění – kurz** |
    | Název aplikace logiky | **TweetSentimentApp** |
    | Oblast | Vyberte oblast, která je nejblíže, a to nejlépe v rámci stejné oblasti, jakou jste vybrali v předchozích krocích. |

    Přijměte výchozí hodnoty pro všechna ostatní nastavení.

1. Vyberte **Zkontrolovat a vytvořit**.

1. Vyberte **Vytvořit**.

1. Po dokončení nasazení vyberte **Přejít k prostředku**.

1. Vyberte tlačítko **prázdná aplikace logiky** .

    :::image type="content" source="media/functions-twitter-email/blank-logic-app-button.png" alt-text="Tlačítko prázdná aplikace logiky":::

1. Kliknutím na tlačítko **Uložit** na panelu nástrojů uložte průběh.

Nyní můžete pomocí návrháře Logic Apps přidat do aplikace služby a triggery.

## <a name="connect-to-twitter"></a>Připojení k Twitteru

Vytvořte připojení ke službě Twitter, aby se vaše aplikace mohla dotazovat na nové tweety.

1. Hledání **Twitteru** v horním vyhledávacím poli.

1. Vyberte ikonu **Twitteru** .

1. Vyberte trigger **Když se publikuje nový tweet**.

1. Zadejte následující hodnoty pro nastavení připojení.

    | Nastavení |  Hodnota |
    | ------- | ---------------- |
    | Název připojení | **MyTwitterConnection** |
    | Typ ověřování | **Použít výchozí sdílenou aplikaci** |

1. Vyberte **Sign in** (Přihlásit se).

1. Pomocí výzev v místním okně dokončete přihlášení k Twitteru.

1. V dalším kroku zadejte do pole _když je nová hodnota pro odeslání nové_ částice následující hodnoty.

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Hledaný text | **#my – Twitter – kurz** |
    | Jak trouba chcete kontrolovat položky? | **15** v textovém poli a <br> **Minuta** v rozevíracím seznamu |

1. Kliknutím na tlačítko **Uložit** na panelu nástrojů uložte průběh.

Potom se připojte k analýze textu a detekuje mínění shromážděných tweety.

## <a name="add-text-analytics-sentiment-detection"></a>Přidat Analýza textu detekci mínění

1. Vyberte **Nový krok**.

1. Do vyhledávacího pole vyhledejte **Analýza textu** .

1. Vyberte ikonu **Analýza textu** .

1. Vyberte možnost **detekovat mínění** a zadejte následující hodnoty.

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název připojení | **TextAnalyticsConnection** |
    | Klíč účtu | Vložte do klíče účtu Analýza textu, který jste nastavili dříve. |
    | Adresa URL webu | Vložte do koncového bodu Analýza textu, který jste nastavili dříve. |

1. Vyberte **Vytvořit**.

1. Klikněte do pole _Přidat nový parametr_ a zaškrtněte políčko u **dokumentu** , který se zobrazí v místním okně.

1. Klikněte do textového pole _dokumenty s ID – 1_ a otevřete automaticky otevírané okno dynamického obsahu.

1. V poli hledání _dynamického obsahu_ vyhledejte **ID** a klikněte na **ID** sady.

1. Kliknutím dovnitř _textu dokumentů – 1_ textové pole otevřete automaticky otevírané okno dynamického obsahu.

1. Do vyhledávacího pole _dynamického obsahu_ vyhledejte **text** a klikněte na **text** v poli.

1. V části **Vybrat akci** zadejte **Analýza textu** a pak klikněte na akci **Rozpoznávání mínění**.

1. Kliknutím na tlačítko **Uložit** na panelu nástrojů uložte průběh.

Pole _detekovat mínění_ by mělo vypadat jako na následujícím snímku obrazovky.

:::image type="content" source="media/functions-twitter-email/detect-sentiment.png" alt-text="Zjistit nastavení mínění":::

## <a name="connect-sentiment-output-to-function-endpoint"></a>Připojit výstup mínění k koncovému bodu funkce

1. Vyberte **Nový krok**.

1. Do vyhledávacího pole vyhledejte **Azure Functions** .

1. Vyberte ikonu **Azure Functions** .

1. Do vyhledávacího pole vyhledejte název vaší funkce. Pokud jste postupovali podle pokynů uvedených výše, název funkce začíná na **TweetSentimentAPI**.

1. Vyberte ikonu funkce.

1. Vyberte položku **TweetSentimentFunction** .

1. Klikněte do pole _Text žádosti_ a v automaticky otevíraném okně vyberte položku _detekovat mínění_ **skóre** .

1. Kliknutím na tlačítko **Uložit** na panelu nástrojů uložte průběh.

## <a name="add-conditional-step"></a>Přidat podmíněný krok

1. Vyberte tlačítko **přidat akci** .

1. Klikněte do _ovládacího prvku_ a vyhledejte a vyberte **ovládací prvek** v místním okně.

1. Vyberte **podmínku**.

1. Klikněte do pole _zvolit hodnotu_ a v automaticky otevíraném okně vyberte položku _TweetSentimentFunction_ **tělo** .

1. Do pole _zvolit hodnotu_ zadejte **kladné** .

1. Kliknutím na tlačítko **Uložit** na panelu nástrojů uložte průběh.

## <a name="add-email-notifications"></a>Přidání e-mailového oznámení

1. V poli _true_ vyberte tlačítko **přidat akci** .

1. Vyhledejte a v textovém poli vyberte **Office 365 Outlook** .

1. Vyhledejte **Odeslat** a v textovém poli vyberte **Odeslat e-mail** .

1. Vyberte tlačítko **Přihlásit** se.

1. Dokončete přihlášení do aplikace Office 365 Outlook podle pokynů v místním okně.

1. Do pole _do_ zadejte svou e-mailovou adresu.

1. Klikněte do pole _Předmět_ a v části _TweetSentimentFunction_ klikněte na položku **tělo** . Pokud se položka _text_ v seznamu nezobrazí, kliknutím na odkaz **Zobrazit další** rozbalte seznam možnosti.

1. Po položce _textu_ v _předmětu_ zadejte text, ze kterého se má **:**.

1. Po _:_ text, klikněte znovu na pole a vyberte **uživatelské jméno** v seznamu možnosti, _když se zobrazí nový_ seznam možností.

1. Klikněte do pole _text_ a v seznamu možnosti, _když se zobrazí nový_ seznam možností, vyberte možnost **text** v poli. Pokud _Textová_ položka v seznamu není zobrazená, klikněte na odkaz **Zobrazit další** a rozbalte seznam možnosti.

1. Kliknutím na tlačítko **Uložit** na panelu nástrojů uložte průběh.

Pole e-mail by teď mělo vypadat jako na tomto snímku obrazovky.

:::image type="content" source="media/functions-twitter-email/email-notification.png" alt-text="E-mailové oznámení":::

## <a name="run-the-workflow"></a>Spustit pracovní postup

1. Z vašeho účtu Twitteru si provedete následující text: **můžu se #my – Twitter-kurz**.

1. Vraťte se do návrháře Logic Apps a vyberte tlačítko **Spustit** .

1. Podívejte se na e-mail s oznámením z pracovního postupu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit všechny služby a účty Azure vytvořené během tohoto kurzu, odstraňte skupinu prostředků.

1. V horním vyhledávacím poli vyhledejte **skupiny prostředků** .

1. Vyberte **kurz v-mínění-kurzu**.

1. Vyberte **Odstranit skupinu prostředků** .

1. Do textového pole zadejte **mínění** .

1. Vyberte tlačítko **Odstranit**.

Volitelně můžete chtít přejít na svůj účet na Twitteru a odstranit všechny testovací tweety z vašeho informačního kanálu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření rozhraní API bez serveru pomocí služby Azure Functions](functions-create-serverless-api.md)
