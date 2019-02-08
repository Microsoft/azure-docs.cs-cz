---
title: 'Rychlý start: Vytvoření klienta moderování pro .NET – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Jak vracet klienta Content Moderatoru pomocí sady Azure Content Moderator SDK pro .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 94a16d03e47a9bec29e5e1c4326beab376dd33dd
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864396"
---
# <a name="quickstart-helper-code-to-return-a-content-moderator-client"></a>Rychlý start: Kód pomocného objektu vrátit Content Moderator klienta

Tento článek obsahuje informace a ukázky kódu, které vám pomůžou začít používat sadu Content Moderator SDK pro .NET, abyste mohli vytvořit klienta Content Moderatoru pro své předplatné.

Knihovnu používají další rychlé starty v této části.

Tento článek předpokládá, že už máte zkušenosti se sadou Visual Studio a jazykem C#.

> [!IMPORTANT]
> Tato knihovna tříd obsahuje kód určený pouze pro demonstrační účely.
> Pokud upravíte tento kód pro použití v produkčním prostředí, použijte bezpečný způsob ukládání a používání svého klíče předplatného Content Moderatoru.

## <a name="sign-up-for-content-moderator-services"></a>Registrace do služeb Content Moderatoru

Než začnete služby Content Moderatoru prostřednictvím rozhraní REST API nebo sady SDK používat, budete potřebovat klíč předplatného.
Odkazovat [zkuste Content Moderator na webu](quick-start.md) rychlém startu se dozvíte, jak můžete získat klíč.

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu Visual Studio

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

## <a name="next-steps"></a>Další postup

[Stáhněte si řešení sady Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pro tento rychlý start a jiné rychlé starty Content Moderatoru pro .NET a začněte se svou integrací.
