---
title: Kontrola textu proti seznamu vlastních výrazů v jazyce C# – Content Moderator
titlesuffix: Azure Cognitive Services
description: Návod, jak moderovat text se seznamy vlastních výrazů pomocí sady Content Moderator SDK pro jazyk C#.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/10/2018
ms.author: sajagtap
ms.openlocfilehash: b3b2d7fe3268846546d1727b62018d9ef5b409ce
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219290"
---
# <a name="quickstart-check-text-against-a-custom-term-list-in-c"></a>Rychlý start: Zkontrolujte seznam vlastních termín se v textuC#

Výchozí globální seznam výrazů v Azure Content Moderatoru je dostačující pro většinu potřeb z hlediska moderování obsahu. Možná ale budete potřebovat vyhledat výrazy, které jsou specifické pro vaši organizaci. Například můžete chtít označit názvy konkurentů k další kontrole. 

Můžete pomocí sady [Content Moderator SDK pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) vytvořit vlastní seznamy výrazů pro použití s rozhraním API pro moderování textu.

Tento článek obsahuje informace a ukázky kódu, které vám pomůžou začít používat sadu Content Moderator SDK pro .NET k následujícím účelům:
- Vytvoření seznamu
- Přidání výrazů do seznamu
- Porovnání výrazů s výrazy v seznamu
- Odstranění výrazů ze seznamu
- Odstranění seznamu
- Úprava informací o seznamu
- Aktualizace indexu tak, aby změny provedené v seznamu byly součástí nového porovnávání

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="sign-up-for-content-moderator-services"></a>Registrace do služeb Content Moderatoru

Než začnete služby Content Moderatoru prostřednictvím rozhraní REST API nebo sady SDK používat, budete potřebovat klíč předplatného. Získáte ho přihlášením k odběru služby Content Moderator na webu [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu Visual Studio

1. Přidejte ke svému řešení nový projekt **Konzolová aplikace (.NET Framework)**.

1. Pojmenujte projekt **TermLists**. Projekt vyberte jako jediný spouštěný projekt řešení.

### <a name="install-required-packages"></a>Instalace požadovaných balíčků

Nainstalujte následující balíčky NuGet pro projekt TermLists:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizace příkazů using programu

Přidejte následující příkazy `using`.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Vytvoření klienta Content Moderatoru

Přidejte následující kód, abyste pro své předplatné vytvořili klienta Content Moderatoru.

> [!IMPORTANT]
> Aktualizujte pole **AzureRegion** a **CMSubscriptionKey** hodnotami identifikátoru oblasti a klíče předplatného.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this 
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The region/location for your Content Moderator account, 
    /// for example, westus.
    /// </summary>
    private static readonly string AzureRegion = "YOUR API REGION";

    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureBaseURL =
        $"https://{AzureRegion}.api.cognitive.microsoft.com";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureBaseURL;
        return client;
    }
}
```

### <a name="add-private-properties"></a>Přidání soukromých vlastností

Přidejte následující soukromé vlastnosti do oboru názvů TermLists, třída Program.

```csharp
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
/// performing image match operations against the list.
/// </summary>
private const double latencyDelay = 0.5;
```

## <a name="create-a-term-list"></a>Vytvoření seznamu výrazů

Vytvoříte seznam výrazů s **ContentModeratorClient.ListManagementTermLists.Create**. První parametr pro **Create** (Vytvořit) je řetězec, který obsahuje typ MIME, který by měl být "application/json". Další informace najdete v [referenčních informacích k rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). Druhý parametr je objekt **Body** (Tělo), který obsahuje název a popis nového seznamu výrazů.

> [!NOTE]
> Limit je maximálně **5 seznamů výrazů** a v každém seznamu může být **maximálně 10 000 výrazů**.

Přidejte následující definici metody do oboru názvů TermLists, třída Program.

> [!NOTE]
> Klíč služby Content Moderator má limit četnosti žádostí za sekundu (RPS), a pokud ho překročíte, sada SDK vyvolá výjimku s kódem chyby 429. Klíč úrovně Free má limit nastavený na 1 RPS.

```csharp
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
```

## <a name="update-term-list-name-and-description"></a>Aktualizace názvu a popisu seznamu výrazů

Informace o seznamu výrazů aktualizujete pomocí **ContentModeratorClient.ListManagementTermLists.Update**. První parametr pro **Update** (Aktualizace) je ID seznamu výrazů. Druhý parametr je typ MIME, který by měl být "application/json". Další informace najdete v [referenčních informacích k rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f685). Třetí parametr je objekt **Body**, který obsahuje nový název a popis.

Přidejte následující definici metody do oboru názvů TermLists, třída Program.

```csharp
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
```

## <a name="add-a-term-to-a-term-list"></a>Přidání výrazu do seznamu výrazů

Přidejte následující definici metody do oboru názvů TermLists, třída Program.

```csharp
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
```

## <a name="get-all-terms-in-a-term-list"></a>Načtení všech výrazů v seznamu výrazů

Přidejte následující definici metody do oboru názvů TermLists, třída Program.

```csharp
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
```

## <a name="add-code-to-refresh-the-search-index"></a>Přidání kódu k aktualizaci indexu vyhledávání

Po provedení změn v seznamu výrazů obnovte jeho index vyhledávání, aby tyto změny byly zahrnuty při příštím použití seznamu termínů k prohledání textu. Je to podobné, jako když vyhledávací modul na počítači (pokud je povolený) nebo vyhledávací web průběžně aktualizuje svůj index, aby obsahoval nové soubory nebo stránky.

Index vyhledávání seznamu termínů aktualizujete pomocí **ContentModeratorClient.ListManagementTermLists.RefreshIndexMethod**.

Přidejte následující definici metody do oboru názvů TermLists, třída Program.

```csharp
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
```

## <a name="screen-text-using-a-term-list"></a>Prohledávání textu pomocí seznamu výrazů

Text prohledáte s použitím seznamu výrazů pomocí **ContentModeratorClient.TextModeration.ScreenText**, který se dá použít s následujícími parametry.

- Jazyk výrazů v seznamu termínů.
- Typ MIME, což může být "text/html", "text/xml", "text/markdown" nebo "text/plain"
- Text, který se má prohledat
- Logická hodnota. Nastavte toto pole na **true**, pokud chcete, aby se v textu před jeho prohledáním provedly automatické opravy.
- Logická hodnota. Nastavte toto pole na **true**, pokud se mají v textu zjišťovat identifikovatelné osobní údaje (PII).
- ID seznamu výrazů

Další informace najdete v [referenčních informacích k rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f).

**ScreenText** vrátí objekt **Screen**, který má vlastnost **Terms**, která obsahuje všechny výrazy, které Content Moderator zjistil při prohledávání. Všimněte si, že pokud Content Moderator nezjistil žádné výrazy během prohledávání, má vlastnost **Terms** hodnotu **null**.

Přidejte následující definici metody do oboru názvů TermLists, třída Program.

```csharp
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
```

## <a name="delete-terms-and-lists"></a>Odstranění výrazů a seznamů

Odstranění výrazu nebo seznamu je jednoduché. Použijete sadu SDK k provádění následujících úloh:

- Odstranění výrazu. (**ContentModeratorClient.ListManagementTerm.DeleteTerm**)
- Odstranění všech výrazů v seznamu bez odstranění seznamu. (**ContentModeratorClient.ListManagementTerm.DeleteAllTerms**)
- Odstranění seznamu a veškerého jeho obsahu. (**ContentModeratorClient.ListManagementTermLists.Delete**)

### <a name="delete-a-term"></a>Odstranění výrazu

Přidejte následující definici metody do oboru názvů TermLists, třída Program.

```csharp
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
```

### <a name="delete-all-terms-in-a-term-list"></a>Odstranění všech výrazů v seznamu výrazů

Přidejte následující definici metody do oboru názvů TermLists, třída Program.

```csharp
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
```

### <a name="delete-a-term-list"></a>Odstranění seznamu výrazů

Přidejte následující definici metody do oboru názvů TermLists, třída Program.

```csharp
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
```

## <a name="putting-it-all-together"></a>Spojení všech součástí dohromady

Přidejte definici metody **Main** do oboru názvů **TermLists**, třída **Program**. Nakonec zavřete třídu **Program** a obor názvů **TermLists**.

```csharp
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
```

## <a name="run-the-application-to-see-the-output"></a>Zobrazení výstupu spuštěním aplikace

Výstup bude na následujících řádcích, ale data se můžou lišit.

```
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
```

## <a name="next-steps"></a>Další postup

Získejte pro tento rychlý start a jiné rychlé starty Content Moderatoru pro technologii .NET [sadu Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) a [řešení Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) a začněte se svou integrací.
