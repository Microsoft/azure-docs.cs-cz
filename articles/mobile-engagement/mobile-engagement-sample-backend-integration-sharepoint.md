---
title: Azure Mobile Engagement – integrace back-end
description: Připojení Azure Mobile Engagement s back-end služby SharePoint, k vytvoření kampaně ze služby SharePoint
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 06297b43-579f-46e6-8a58-961a68f9aa09
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: cf530ca6dce32050487f367aa80b10bf7f20b7e7
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement---api-integration"></a>Azure Mobile Engagement – integrace rozhraní API
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

V automatizovaný systém marketing vytvoření a aktivace marketingových kampaní taky dojít automaticky. Pro tento účel - Azure Mobile Engagement umožňuje vytváření takové automatizované marketingových kampaní, které jsou také pomocí rozhraní API. 

Obvykle zákazníkům použít rozhraní front-endu Mobile Engagement k vytvoření oznámení nebo hlasování podobně jako součást svých marketingových kampaní. Ale protože stále vyspělá marketingových kampaní, je zapotřebí využívat data zamknutý v back-end systémy (například systému CRM nebo systém CMS jako SharePoint), aby bylo možné vytvořit plně automatizovaného kanálu vytváří kampaně v Mobile Engagementu dynamicky na základě dat odesílaných v z back-end systémy. 

![][5]

V tomto kurzu projde scénář, kde uživatel obchodní SharePoint naplní seznam serveru SharePoint marketing daty a automatizovaného procesu převezme položek v seznamu a připojí se sadou Mobile Engagement použití dostupných rozhraní API REST k vytvoření marketingovou kampaň z dat služby SharePoint. 

> [!IMPORTANT]
> Obecně platí můžete tuto ukázku jako výchozí bod porozumět tomu, jak voláním jakéhokoli rozhraní API služby Mobile Engagement REST jako obsahuje podrobné informace o dva klíčové aspekty volání rozhraní API - ověřování a předejte parametry. 
> 
> 

## <a name="sharepoint-integration"></a>Integrace služby SharePoint
1. Zde je ukázka seznamu služby SharePoint, která bude vypadat takto. **Název**, **kategorie**, **NotificationTitle**, **zpráva** a **URL** jsou používány k vytvoření oznámení. Existuje sloupec s názvem **IsProcessed** který je používán procesem automatizace ukázka ve formě program konzoly. Můžete buď spustit tato konzola programu jako webová úloha služby Azure tak, aby ji můžete naplánovat nebo můžete přímo pracovního postupu služby SharePoint k programu, vytvoření a aktivace oznámení, když vložíte položky do seznamu služby SharePoint. V této ukázce používáme program konzoly, která přejde prostřednictvím položky v SharePoint seznam a vytvořit oznámení v Azure Mobile Engagementu pro každý z nich a nakonec označí **IsProcessed** příznak pravdivá na vytvoření úspěšné oznámení.
   
    ![][1]
2. Používáme kód z ukázky *vzdáleného ověřování ve službě SharePoint Online pomocí objektového modelu klienta* [sem](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c) k ověření pomocí seznamu služby SharePoint.
3. Po ověření, jsme projít položky seznamu a zjistěte, všechny nově vytvořené položky (které budou mít **IsProcessed** = false). 
   
         static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);
   
                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();
   
                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;
   
                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;
   
                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";
   
                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>Integrace Mobile Engagement
1. Jakmile se nám najít položku, která vyžaduje zpracování - jsme extrahovat informace požadované pro vytvoření oznámení z položky seznamu a volání `CreateAzMECampaign` ji vytvořit a následně `ActivateAzMECampaign` aktivovat. Toto jsou v podstatě volání rozhraní REST API na back-end Mobile Engagementu volání. 
2. Vyžadovat Mobile Engagement REST API **základní ověřování schématu autorizační HTTP hlavičky** který se skládá z `ApplicationId` a `ApiKey` který můžete získat z portálu Azure. Ujistěte se, že používáte klíč z **klíče rozhraní api** části a *není* z **sdk klíče** části. 
   
   ![][2]
   
       static string CreateAuthZHeader()
       {
           string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
           return BasicAuthzHeaderString;
       }
   
       static public string EncodeTo64(string toEncode)
       {
           byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
           string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
           return returnValue;
       }  
3. Pro vytvoření oznámení zadejte kampaň – odkazovat [dokumentaci](https://msdn.microsoft.com/library/azure/mt683750.aspx). Musíte zajistit, že určíte kampaň `kind` jako *oznámení* a [datové části](https://msdn.microsoft.com/library/azure/mt683751.aspx) a předejte ji jako FormUrlEncodedContent. 
   
        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";
   
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }
4. Jakmile máte oznámení vytvořen, zobrazí se něco jako následující na portál Mobile Engagement (Všimněte si, že stav = Koncept a aktivované = není k dispozici)
   
    ![][3]
5. `CreateAzMECampaign` Vytvoří oznámení kampaně a vrátí jeho Id na volajícího. `ActivateAzMECampaign` Toto Id vyžaduje jako parametr k aktivaci kampaně. 
   
        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }
6. Až budete mít aktivovaný oznámení, zobrazí se něco jako následující na portál Mobile Engagement:
   
    ![][4]
7. Po aktivaci kampaně získá jakékoli zařízení, které splňují kritérium na kampaň začne zobrazuje oznámení. 
8. Také si všimnete, že položka seznamu označené IsProcessed = false je nastavená na hodnotu True, po vytvoření kampaně oznámení.  

Tato ukázka vytvořit jednoduché oznámení kampaň zadání nejčastěji požadované vlastnosti. Tuto možnost můžete upravit tolik, kolik z portálu můžete pomocí informací o [zde](https://msdn.microsoft.com/library/azure/mt683751.aspx). 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png



