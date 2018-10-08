---
title: 'Rychlý start: Metoda pomocné rutiny sady Content Moderator SDK pro .NET'
titlesuffix: Azure Cognitive Services
description: Jak vracet klienta Content Moderatoru pomocí sady Azure Content Moderator SDK pro .NET
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
ms.date: 01/04/2018
ms.author: sajagtap
ms.openlocfilehash: 0c167de3af8aea6820962e7cc8d0c21f5ba61807
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223263"
---
# <a name="quickstart-helper-code-to-return-a-content-moderator-client"></a>Rychlý start: Kód pomocné rutiny k vrácení klienta Content Moderatoru

Tento článek obsahuje informace a ukázky kódu, které vám pomůžou začít používat sadu Content Moderator SDK pro .NET, abyste mohli vytvořit klienta Content Moderatoru pro své předplatné.

Knihovnu používají další rychlé starty v této části.

Tento článek předpokládá, že už máte zkušenosti se sadou Visual Studio a jazykem C#.

> [!IMPORTANT]
> Tato knihovna tříd obsahuje kód určený pouze pro demonstrační účely.
> Pokud upravíte tento kód pro použití v produkčním prostředí, použijte bezpečný způsob ukládání a používání svého klíče předplatného Content Moderatoru.

## <a name="sign-up-for-content-moderator-services"></a>Registrace do služeb Content Moderatoru

Než začnete služby Content Moderatoru prostřednictvím rozhraní REST API nebo sady SDK používat, budete potřebovat klíč předplatného.
Podívejte se na tento [Rychlý start](quick-start.md), kde se dozvíte, jak klíč získat.

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu v sadě Visual Studio

1. Vytvořte nový projekt **Knihovna tříd (.NET Framework)**.

   V ukázkovém kódu jsem projekt pojmenoval **ModeratorHelper**.

1. Přidejte odkaz na sestavení Frameworku **System.Configuration**.

### <a name="install-required-packages"></a>Instalace požadovaných balíčků

Nainstalujte následující balíčky NuGet:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="create-the-content-moderator-client"></a>Vytvoření klienta Content Moderatoru

Obsah souboru ModeratorHelper.cs nahraďte následujícím kódem:

    using Microsoft.CognitiveServices.ContentModerator;

    namespace ModeratorHelper
    {
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
        private static readonly string AzureRegion = "myRegion";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "myKey";

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

            client.BaseUrl = AzureBaseURL;
            return client;
        }
    }
    }


> [!IMPORTANT]
> Aktualizujte pole **AzureRegion** a **CMSubscriptionKey** hodnotami identifikátoru oblasti a klíče předplatného.

Teď už umíte rychle vytvořit klienta Content Moderatoru pro své předplatné.

## <a name="next-steps"></a>Další kroky

[Stáhněte si řešení sady Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pro tento rychlý start a jiné rychlé starty Content Moderatoru pro .NET a začněte se svou integrací.
