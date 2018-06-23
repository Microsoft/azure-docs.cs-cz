---
title: Střední s seznamy vlastní termín v obsahu moderátora Azure | Microsoft Docs
description: Střední s vlastní termín seznamů pomocí sady Azure obsahu moderátora SDK pro .NET.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/11/2018
ms.author: sajagtap
ms.openlocfilehash: 6da72ad070d9c3a6be38e24626dff77b52fed852
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342519"
---
# <a name="moderate-with-custom-term-lists-in-net"></a>Střední s seznamy vlastní termín v rozhraní .NET

Pro většinu obsahu přerušování potřeb stačí výchozí globální seznam termínů v moderátora obsahu Azure. Nicméně budete muset obrazovky podmínek, které jsou specifické pro vaši organizaci. Například můžete chtít názvy konkurence značek hodnocení. 

Obsahu moderátora SDK pro rozhraní .NET můžete použít k vytvoření vlastních seznamů podmínky pro použití s rozhraní API přerušování Text.

> [!NOTE]
> Maximální limit je **5 termín uvádí** s každou seznamu **není delší než 10 000 podmínky**.
>

Tento článek obsahuje informace a ukázky kódu, které vám pomůžou začít používat sadu SDK obsahu moderátora pro technologii .NET:
- Vytvoří seznam.
- Přidáte do seznamu podmínek.
- Obrazovky podmínky s výrazy v seznamu.
- Podmínky odstraňte ze seznamu.
- Odstranění seznamu.
- Upravte informace o seznamu.
- Aktualizujte index, aby se změny provedené v seznamu jsou zahrnuté v nové prohledání.

Tento článek předpokládá, že jste již obeznámeni s Visual Studio a C#.

## <a name="sign-up-for-content-moderator-services"></a>Zaregistrujte si obsahu moderátora služby

Před použitím služby obsahu moderátora přes rozhraní REST API nebo sady SDK, je nutné klíč předplatného.

Na řídicím panelu obsahu moderátora můžete najít svůj klíč předplatného v **nastavení** > **pověření** > **rozhraní API**  >  **Zkušební Ocp-Apim-Subscription-Key**. Další informace najdete v tématu [přehled](overview.md).

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

1. Přidejte nový **konzolovou aplikaci (rozhraní .NET Framework)** projekt pro vaše řešení.

1. Název projektu **TermLists**. Vyberte tento projekt jako jeden počáteční projekt pro řešení.

1. Přidat odkaz na **ModeratorHelper** sestavení, které jste vytvořili v projektu [obsahu moderátora klienta pomocná rychlý Start](content-moderator-helper-quickstart-dotnet.md).

### <a name="install-required-packages"></a>Instalace požadovaných balíčků

Nainstalujte následující balíčky NuGet pro projekt TermLists:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizace programu je pomocí příkazů

Upravit program je pomocí příkazů.

    using System;
    using System.Threading;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;

### <a name="add-private-properties"></a>Přidáním vlastních vlastností

Přidejte následující soukromé vlastnosti do oboru názvů TermLists, třídy Program.

    /// <summary>
    /// The language of the terms in the term lists.
    /// </summary>
    private const string lang = "eng";

    /// <summary>
    /// The minimum amount of time, in milliseconds, to wait between calls
    /// to the Content Moderator APIs.
    /// </summary>
    private const int throttleRate = 3000;

    /// <summary>
    /// The number of minutes to delay after updating the search index before
    /// performing image match operations against a the list.
    /// </summary>
    private const double latencyDelay = 0.5;

## <a name="create-a-term-list"></a>Vytvoří seznam podmínek

Vytvoří seznam podmínek s **ContentModeratorClient.ListManagementTermLists.Create**. První parametr **vytvořit** je řetězec, který obsahuje typ MIME, které by se měly "application/json". Další informace najdete v tématu [referenční dokumentace rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). Druhý parametr je **textu** objekt, který obsahuje název a popis seznamu nové období.

Přidejte následující definici metody do oboru názvů TermLists, třídy Program.

> [!NOTE]
> Klíč obsahu moderátora služby má požadavky na druhý omezení četnosti (RPS) a pokud limit překročíte, vyvolá výjimku s kódem 429 chyby, sady SDK. 
>
> Úroveň free klíč může mít jeden RPS rychlost.

    /// <summary>
    /// Creates a new term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <returns>The term list ID.</returns>
    static string CreateTermList (ContentModeratorClient client)
    {
        Console.WriteLine("Creating term list.");

        Body body = new Body("Term list name", "Term list description");
        TermList list = client.ListManagementTermLists.Create("application/json", body);
        if (false == list.Id.HasValue)
        {
            throw new Exception("TermList.Id value missing.");
        }
        else
        {
            string list_id = list.Id.Value.ToString();
            Console.WriteLine("Term list created. ID: {0}.", list_id);
            Thread.Sleep(throttleRate);
            return list_id;
        }
    }

## <a name="update-term-list-name-and-description"></a>Aktualizace termín seznamu název a popis

Aktualizovat informace o seznamu období s **ContentModeratorClient.ListManagementTermLists.Update**. První parametr **aktualizace** je ID termín seznamu. Druhý parametr je typ MIME, které by se měly "application/json". Další informace najdete v tématu [referenční dokumentace rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f685). Třetí parametr není **textu** objekt, který obsahuje nový název a popis.

Přidejte následující definici metody do oboru názvů TermLists, třídy Program.

    /// <summary>
    /// Update the information for the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to update.</param>
    /// <param name="name">The new name for the term list.</param>
    /// <param name="description">The new description for the term list.</param>
    static void UpdateTermList (ContentModeratorClient client, string list_id, string name = null, string description = null)
    {
        Console.WriteLine("Updating information for term list with ID {0}.", list_id);
        Body body = new Body(name, description);
        client.ListManagementTermLists.Update(list_id, "application/json", body);
        Thread.Sleep(throttleRate);
    }

## <a name="add-a-term-to-a-term-list"></a>Přidat do seznamu termín termín

Přidejte následující definici metody do oboru názvů TermLists, třídy Program.

    /// <summary>
    /// Add a term to the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to update.</param>
    /// <param name="term">The term to add to the term list.</param>
    static void AddTerm (ContentModeratorClient client, string list_id, string term)
    {
        Console.WriteLine("Adding term \"{0}\" to term list with ID {1}.", term, list_id);
        client.ListManagementTerm.AddTerm(list_id, term, lang);
        Thread.Sleep(throttleRate);
    }

## <a name="get-all-terms-in-a-term-list"></a>Získat všechny podmínky v seznamu termín

Přidejte následující definici metody do oboru názvů TermLists, třídy Program.

    /// <summary>
    /// Get all terms in the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list from which to get all terms.</param>
    static void GetAllTerms(ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Getting terms in term list with ID {0}.", list_id);
        Terms terms = client.ListManagementTerm.GetAllTerms(list_id, lang);
        TermsData data = terms.Data;
        foreach (TermsInList term in data.Terms)
        {
            Console.WriteLine(term.Term);
        }
        Thread.Sleep(throttleRate);
    }

## <a name="add-code-to-refresh-the-search-index"></a>Přidání kódu k aktualizaci indexu vyhledávání

Když provedete změny v seznamu termín, můžete obnovit jeho index vyhledávání změny, které mají být zahrnuty při příštím použití seznamu termín na obrazovce text. Toto je podobná jak vyhledávacího webu na ploše (Pokud je povoleno) nebo vyhledávací web průběžně aktualizuje její index zahrnout nových souborů nebo stránky.

Aktualizovat index vyhledávání seznamu termín s **ContentModeratorClient.ListManagementTermLists.RefreshIndexMethod**.

Přidejte následující definici metody do oboru názvů TermLists, třídy Program.

    /// <summary>
    /// Refresh the search index for the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to refresh.</param>
    static void RefreshSearchIndex (ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Refreshing search index for term list with ID {0}.", list_id);
        client.ListManagementTermLists.RefreshIndexMethod(list_id, lang);
        Thread.Sleep((int)(latencyDelay * 60 * 1000));
    }

## <a name="screen-text-using-a-term-list"></a>Obrazovka textu s použitím seznam podmínek

Obrazovky textu s použitím termín seznam s **ContentModeratorClient.TextModeration.ScreenText**, které mají následující parametry.

- Jazyk podmínky v seznamu podmínek.
- Typ MIME, který může být "text/html", "text/xml", "text/markdownu" nebo "text/plain".
- Text na obrazovku.
- Logická hodnota. Nastavte pole na **true** k automatické opravy textu před jeho blokování.
- Logická hodnota. Nastavte pole na **true** ke zjištění osobních identifikovatelných informací (PII) v textu.
- ID termín seznamu.

Další informace najdete v tématu [referenční dokumentace rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f).

**ScreenText** vrátí **obrazovky** objekt, který má **podmínky** vlastnost, která uvádí všechny podmínky tohoto obsahu moderátora v prověřování zjištěn. Všimněte si, že pokud moderátora obsahu se nepodařilo rozpoznat podmínek během blokování, **podmínky** vlastnost má hodnotu **null**.

Přidejte následující definici metody do oboru názvů TermLists, třídy Program.

    /// <summary>
    /// Screen the indicated text for terms in the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to use to screen the text.</param>
    /// <param name="text">The text to screen.</param>
    static void ScreenText (ContentModeratorClient client, string list_id, string text)
    {
        Console.WriteLine("Screening text: \"{0}\" using term list with ID {1}.", text, list_id);
        Screen screen = client.TextModeration.ScreenText(lang, "text/plain", text, false, false, list_id);
        if (null == screen.Terms)
        {
            Console.WriteLine("No terms from the term list were detected in the text.");
        }
        else
        {
            foreach (DetectedTerms term in screen.Terms)
            {
                Console.WriteLine(String.Format("Found term: \"{0}\" from list ID {1} at index {2}.", term.Term, term.ListId, term.Index));
            }
        }
        read.Sleep(throttleRate);
    }

## <a name="delete-terms-and-lists"></a>Odstranit podmínky a seznamy

Odstraněním termín nebo seznamu je jednoduchá. Použití sady SDK k provádění následujících úloh:

- Odstraňte termín. (**ContentModeratorClient.ListManagementTerm.DeleteTerm**)
- Odstraňte všechny podmínky v seznamu bez odstranění seznamu. (**ContentModeratorClient.ListManagementTerm.DeleteAllTerms**)
- Odstraňte seznam a veškerý jeho obsah. (**ContentModeratorClient.ListManagementTermLists.Delete**)

### <a name="delete-a-term"></a>Odstranit termín

Přidejte následující definici metody do oboru názvů TermLists, třídy Program.

    /// <summary>
    /// Delete a term from the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list from which to delete the term.</param>
    /// <param name="term">The term to delete.</param>
    static void DeleteTerm (ContentModeratorClient client, string list_id, string term)
    {
        Console.WriteLine("Removed term \"{0}\" from term list with ID {1}.", term, list_id);
        client.ListManagementTerm.DeleteTerm(list_id, term, lang);
        Thread.Sleep(throttleRate);
    }

### <a name="delete-all-terms-in-a-term-list"></a>Odstranit všechny podmínky v seznamu termín

Přidejte následující definici metody do oboru názvů TermLists, třídy Program.

    /// <summary>
    /// Delete all terms from the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list from which to delete all terms.</param>
    static void DeleteAllTerms (ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Removing all terms from term list with ID {0}.", list_id);
        client.ListManagementTerm.DeleteAllTerms(list_id, lang);
        Thread.Sleep(throttleRate);
    }

### <a name="delete-a-term-list"></a>Odstranit seznam podmínek

Přidejte následující definici metody do oboru názvů TermLists, třídy Program.

    /// <summary>
    /// Delete the indicated term list.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="list_id">The ID of the term list to delete.</param>
    static void DeleteTermList (ContentModeratorClient client, string list_id)
    {
        Console.WriteLine("Deleting term list with ID {0}.", list_id);
        client.ListManagementTermLists.Delete(list_id);
        Thread.Sleep(throttleRate);
    }

## <a name="putting-it-all-together"></a>Třeba umisťovat všechny společně

Přidat **hlavní** definici metody do oboru názvů TermLists, třídy Program. Nakonec zavřete třídy Program a TermLists obor názvů.

    static void Main(string[] args)
    {
        using (var client = Clients.NewClient())
        {
            string list_id = CreateTermList(client);

            UpdateTermList(client, list_id, "name", "description");
            AddTerm(client, list_id, "term1");
            AddTerm(client, list_id, "term2");

            GetAllTerms(client, list_id);

            // Always remember to refresh the search index of your list
            RefreshSearchIndex(client, list_id);

            string text = "This text contains the terms \"term1\" and \"term2\".";
            ScreenText(client, list_id, text);

            DeleteTerm(client, list_id, "term1");

            // Always remember to refresh the search index of your list
            RefreshSearchIndex(client, list_id);

            text = "This text contains the terms \"term1\" and \"term2\".";
            ScreenText(client, list_id, text);

            DeleteAllTerms(client, list_id);
            DeleteTermList(client, list_id);

            Console.WriteLine("Press ENTER to close the application.");
            Console.ReadLine();
        }
    }

## <a name="run-the-application-to-see-the-output"></a>Spusťte aplikaci zobrazíte výstup

Výstup bude na následující řádky, ale data se může lišit.

    Creating term list.
    Term list created. ID: 252.
    Updating information for term list with ID 252.
    
    Adding term "term1" to term list with ID 252.
    Adding term "term2" to term list with ID 252.
    
    Getting terms in term list with ID 252.
    term1
    term2
    
    Refreshing search index for term list with ID 252.
    
    Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
    Found term: "term1" from list ID 252 at index 32.
    Found term: "term2" from list ID 252 at index 46.
    
    Removed term "term1" from term list with ID 252.
    
    Refreshing search index for term list with ID 252.
    
    Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
    Found term: "term2" from list ID 252 at index 46.
    
    Removing all terms from term list with ID 252.
    Deleting term list with ID 252.
    Press ENTER to close the application.
    
## <a name="next-steps"></a>Další postup

[Stáhněte si řešení sady Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) v tomto a dalších – elementy QuickStart obsahu moderátora pro platformu .NET a začít na svoji integraci.
