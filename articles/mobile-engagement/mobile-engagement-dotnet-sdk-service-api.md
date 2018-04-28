---
title: Pomocí sady .NET SDK pro přístup k rozhraní API služby Azure Mobile Engagement
description: Popisuje, jak používat Mobile Engagement .NET SDK pro přístup k rozhraní API služby Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: c07728aa-43f2-4238-8b4a-c9eddf9d838b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 4a303db638b8ea304db5cba4075b0b102eedd0f3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="using-net-sdk-to-access-azure-mobile-engagement-service-apis"></a>Pomocí sady .NET SDK pro přístup k rozhraní API služby Azure Mobile Engagement
> [!IMPORTANT]
> Azure Mobile Engagement se vyřadí z provozu 31. 3. 2018. Tato stránka bude odstraněna krátce potom.
> 

Azure Mobile Engagement poskytuje sadu rozhraní API pro správu zařízení, kampaní Reach/nabízených atd. Pro interakci s Tato rozhraní API, poskytujeme také můžete [soubor Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) , můžete pomocí nástrojů můžete vygenerovat pomocí sady SDK pro preferovaný jazyk. Doporučujeme používat [AutoRest](https://github.com/Azure/AutoRest) nástroj pro generování vaší sady SDK z našich souboru Swagger.

> [!NOTE]
> Službu Azure Mobile Engagement vyřadíme z provozu v březnu 2018. V současnosti je dostupná jenom pro stávající zákazníky. Další informace najdete v tématu [Mobile Engagement](https://azure.microsoft.com/services/mobile-engagement/).

Vytvořili jsme .NET SDK podobným způsobem, který umožňuje pracovat s Tato rozhraní API pomocí obálku C# a nemusíte dělat vyjednávání tokenu ověřování a aktualizujte sami.  

Tato ukázka prochází sadu kroků se používat sadu .NET SDK:

1. Nejdřív všech, je potřeba nastavit ověřování pro vaše rozhraní API pomocí Azure Active Directory, jak je popsáno [zde](mobile-engagement-api-authentication.md#authentication). Na konci těchto kroků, by měl mít platnou **SubscriptionId**, **TenantId**, **ApplicationId** a **tajný klíč**. 
2. Použijeme jednoduchou aplikaci konzoly Windows k předvedení pracovní pomocí .NET SDK se scénářem vytvoření kampaně oznámení. Proto otevřete Visual Studio a vytvořte **konzolové aplikace**.   
3. Potom budete muset stáhnout sady .NET SDK, která je k dispozici jako **knihovna Microsoft Azure Engagement správy** v galerii Nuget [zde](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/).
   Pokud instalujete Nuget ze sady Visual Studio, je potřeba zajistit, abyste měli kontrolu označena **zahrnout předběžné verze** možnost při vyhledávání pro balíček:
   
    ![][1]
4. V `Program.cs` soubor, přidejte následující obory názvů:
   
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;
5. Dále je třeba definovat následující konstanty, které budeme používat pro ověřování a interakci s aplikace Mobile Engagementu, ve kterém vytváříte kampaň oznámení:
   
        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";
   
        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";
   
        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";
6. Definování `EngagementManagementClient` proměnné, které použijete k volání metody Mobile Engagement SDK:
   
        static EngagementManagementClient engagementClient; 
7. Přidejte následující vaší `Main` metoda:
   
        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();
   
                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }
8. Definujte následující metodu, která má na starosti inicializaci `EngagementManagementClient` nejprve ověřením a přiřazení samotné aplikace Mobile Engagementu, ve kterém chcete vytvořit kampaň oznámení:
   
        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
   
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;
   
            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }
   
   > [!IMPORTANT]
   > Všimněte si, že budete muset použít **název prostředku aplikace** jak jsou definovány v portálu správy Azure pro parametr AppName. 
   > 
   > 
9. Nakonec zadejte CreateCampaign metodu, která se postará o pomocí dříve inicializovaného EngagementClient vytvořit jednoduchou **kdykoli** & **pouze oznámení** kampaně s název a zpráva: 
   
        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );
   
            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }
10. Spusťte konzolovou aplikaci a zobrazí se následující v úspěšném vytvoření kampaně:
    
    **Id kampaně "159" byl úspěšně vytvořen a je ve stavu "konceptu.**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png
