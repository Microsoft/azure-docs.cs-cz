---
title: Azure obsahu moderátora SDK pro .NET Pomocná metoda | Microsoft Docs
description: Jak vracet obsahu moderátora klienta pomocí sady Azure obsahu moderátora SDK pro .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/04/2018
ms.author: sajagtap
ms.openlocfilehash: 36f2124708731f78f34849d8210ed39ea8f59140
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342393"
---
# <a name="helper-code-to-return-a-content-moderator-client"></a>Kód pomocného objektu vrátit obsahu moderátora klienta

Tento článek obsahuje informace a ukázky kódu, které vám pomůžou začít používat obsahu moderátora SDK pro .NET k vytváření obsahu moderátora klienta pro vaše předplatné.

Knihovny používá jiné – elementy QuickStart v této části.

Tento článek předpokládá, že jste již obeznámeni s Visual Studio a C#.

> [!IMPORTANT]
> Tato knihovna tříd obsahuje kód určena pouze pro demonstrační účely.
> Pokud však upravit tento kód pro použití v produkčním prostředí, použijte zabezpečené metody ukládání a použití svůj klíč předplatného moderátora obsahu.

## <a name="sign-up-for-content-moderator-services"></a>Zaregistrujte si obsahu moderátora služby

Před použitím služby obsahu moderátora přes rozhraní REST API nebo sady SDK, je nutné klíč předplatného.
Odkazovat [rychlý Start](quick-start.md) se dozvíte, jak můžete získat klíč.

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

1. Vytvořte novou **knihovny tříd (rozhraní .NET Framework)** projektu.

   V ukázkovém kódu I s názvem projektu **ModeratorHelper**.

1. Přidat odkaz na **System.Configuration** sestavení rozhraní.

### <a name="install-required-packages"></a>Instalace požadovaných balíčků

Nainstalujte následující balíčky NuGet:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="create-the-content-moderator-client"></a>Vytvoření obsahu moderátora klienta

Nahraďte obsah souboru ModeratorHelper.cs následujícím kódem:

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
> Aktualizace **AzureRegion** a **CMSubscriptionKey** pole s hodnotami klíče oblast identifikátor a předplatné.

Nyní máte rychlý způsob, jak vytvořit klienta obsahu moderátora pro vaše předplatné.

## <a name="next-steps"></a>Další postup

[Stáhněte si řešení sady Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) v tomto a dalších – elementy QuickStart obsahu moderátora pro platformu .NET a začít na svoji integraci.
