---
title: Vývoj souborů Azure NetApp s rozhraním REST API | Dokumenty společnosti Microsoft
description: Popisuje, jak začít s používáním rozhraní REST API souborů Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: b-juche
ms.openlocfilehash: 996fbcc7c3c9af0da9160216785ecd54840660e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65957042"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Vývoj pro soubory Azure NetApp s rozhraním REST API 

Rozhraní REST API pro službu Azure NetApp Files definuje operace HTTP proti prostředkům, jako je účet NetApp, fond kapacity, svazky a snímky. Tento článek vám pomůže začít s používáním rozhraní REST API souborů Azure NetApp.

## <a name="azure-netapp-files-rest-api-specification"></a>Specifikace rozhraní REST soubory Azure NetApp

Specifikace rozhraní REST API pro soubory Azure NetApp se publikuje prostřednictvím [GitHubu](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager):

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Přístup k rozhraní REST souborů Azure NetApp  

1. Pokud jste tak ještě neučinili, [nainstalujte příkazový příkaz k příkazu Konzumu Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
2. Vytvořte instanční objekt ve službě Azure Active Directory (Azure AD):
   1. Ověřte, zda máte [dostatečná oprávnění](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

   1. Do příkazového příkazu Azure zadejte následující příkaz:  

           az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE--password $YOURGENERATEDPASSWORDGOESHERE

      Výstup příkazu je podobný následujícímu příkladu:  

           { 
               "appId": "appIDgoeshere", 
               "displayName": "APPNAME", 
               "name": "http://APPNAME", 
               "password": "supersecretpassword", 
               "tenant": "tenantIDgoeshere" 
           } 

      Zachovat výstup příkazu.  Budete potřebovat `appId`, `password`, `tenant` a hodnoty. 

3. Vyžádejte si přístupový token OAuth:

    Příklady v tomto článku používají cURL.  Můžete také použít různé nástroje API, jako je [Pošťák](https://www.getpostman.com/), [Insomnie](https://insomnia.rest/)a [Paw](https://paw.cloud/).  

    Nahraďte proměnné v následujícím příkladu výstupem příkazu z výše uvedeného kroku 2. 

        curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token

    Výstup poskytuje přístupový token podobný následujícímu příkladu:

        eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9

    Zobrazený token je platný po dobu 3600 sekund. Poté musíte požádat o nový token. 
    Uložte token do textového editoru.  Budete ji potřebovat pro další krok.

4. Odešlete testovací hovor a zahrňte token pro ověření vašeho přístupu k rozhraní REST API:

        curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2016-08-01

## <a name="examples-using-the-api"></a>Příklady použití rozhraní API  

Tento článek používá následující adresu URL pro směrný plán požadavků. Tato adresa URL odkazuje na kořenový adresář oboru názvů Soubory Azure NetApp. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15`

Hodnoty `subID` a `resourceGroups` v následujících příkladech byste měli nahradit vlastními hodnotami. 

### <a name="get-request-examples"></a>Příklady požadavků GET

Požadavek GET se používá k dotazování objektů souborů Azure NetApp v předplatném, jak ukazují následující příklady: 

        #get NetApp accounts 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15

        #get capacity pools for NetApp account 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2017-08-15

        #get volumes in NetApp account & capacity pool 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2017-08-15

        #get snapshots for a volume 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2017-08-15

### <a name="put-request-examples"></a>Příklady požadavků PUT

Požadavek PUT se používá k vytvoření nových objektů v azure netapp soubory, jak ukazují následující příklady. Tělo požadavku PUT může obsahovat data ve formátu JSON pro změny nebo může určit soubor, ze který chcete číst. 

        #create a NetApp account  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2017-08-15

        #create a capacity pool  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2017-08-15

        #create a volume  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2017-08-15

        #create a volume snapshot  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2017-08-15

### <a name="json-examples"></a>Příklady JSON

Následující příklad ukazuje, jak vytvořit účet NetApp:

    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 

Následující příklad ukazuje, jak vytvořit fond kapacity: 

    {
        "name": "MYNETAPPACCOUNT/POOLNAME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
        "location": "westus2",
        "properties": {
            "name": "POOLNAME"
            "size": "4398046511104",
            "serviceLevel": "Premium"
        }
    }

Následující příklad ukazuje, jak vytvořit nový svazek: 

    {
        "name": "MYNEWVOLUME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
        "location": "westus2",
        "properties": {
            "serviceLevel": "Premium",
            "usageThreshold": "322122547200",
            "creationToken": "MY-FILEPATH",
            "snapshotId": "",
            "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
            }
    }

Následující příklad ukazuje, jak vytvořit snímek svazku: 

    {
        "name": "apitest2/apiPool01/apiVol01/snap02",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
        "location": "westus2",
        "properties": {
            "name": "snap02",
            "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
        }
    }

> [!NOTE] 
> Je třeba `fileSystemId` zadat pro vytvoření snímku.  Hodnotu `fileSystemId` můžete získat pomocí požadavku GET na svazek. 

## <a name="next-steps"></a>Další kroky

[Podívejte se na odkaz rozhraní REST soubory Azure NetApp](https://docs.microsoft.com/rest/api/netapp/)
