---
title: Vývoj pro soubory Azure NetApp pomocí rozhraní REST API | Dokumentace Microsoftu
description: Popisuje, jak začít používat rozhraní REST API souborů NetApp Azure.
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
ms.topic: how-to-article
ms.date: 02/06/2019
ms.author: b-juche
ms.openlocfilehash: 169638fed9a513b8ed835076c049ee21979085fe
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967178"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Vývoj pro soubory Azure NetApp pomocí rozhraní REST API 

Rozhraní REST API pro službu Azure NetApp Files definuje operace HTTP s prostředky, jako je NetApp účtu, kapacity fondu, svazky a snímků. Tento článek vám pomůže začít pracovat s použitím REST API služby soubory Azure NetApp.

## <a name="access-the-azure-netapp-files-rest-api"></a>Přístup k rozhraní REST API služby soubory Azure NetApp  

1. [Instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Pokud jste tak již neučinili.
2. Vytvoření instančního objektu v Azure Active Directory (Azure AD):
    1. Ověřte, že máte [dostatečná oprávnění](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

    1. Zadejte následující příkaz v rozhraní příkazového řádku Azure:  

            az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE--password $YOURGENERATEDPASSWORDGOESHERE

    Podobně jako v následujícím příkladu je výstup tohoto příkazu:  

            { 
                "appId": "appIDgoeshere", 
                "displayName": "APPNAME", 
                "name": "http://APPNAME", 
                "password": "supersecretpassword", 
                "tenant": "tenantIDgoeshere" 
            } 

    Zachovejte výstup příkazu.  Budete potřebovat `appId`, `password`, a `tenant` hodnoty. 

3. Žádost o přístupový token OAuth:

    V příkladech v tomto článku používáme nástroj cURL.  Můžete také použít různé nástroje rozhraní API, jako [Postman](https://www.getpostman.com/), [režimu spánku](https://insomnia.rest/), a [Paw](https://paw.cloud/).  

    Nahraďte proměnné v následujícím příkladu výstupu příkazu v kroku 2 výše. 

        curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token

    Výstup obsahuje přístupový token podobně jako v následujícím příkladu:

        eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9

    Zobrazený token je platný pro 3 600 sekund. Potom budete muset požádat o nový token. 
    Token uložte do textového editoru.  Je třeba na další krok.

4. Odeslání volání testu a zahrnují token ověřit váš přístup k rozhraní REST API:

        curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2016-08-01

## <a name="examples-using-the-api"></a>Příklady použití rozhraní API  

Tento článek používá následující adresu URL pro požadovaný směrný plán požadavků. Tato adresa URL odkazuje na kořenový obor názvů souborů NetApp Azure. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15`

Byste měli vyměnit `subID` a `resourceGroups` hodnoty v následujících příkladech s vlastními hodnotami. 

### <a name="get-request-examples"></a>ZÍSKÁNÍ příkladů žádosti

Použít požadavek GET na objekty dotazu souborů NetApp Azure v předplatném, jako následující příklady ukazují: 

        #get NetApp accounts 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15

        #get capacity pools for NetApp account 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2017-08-15

        #get volumes in NetApp account & capacity pool 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2017-08-15

        #get snapshots for a volume 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2017-08-15

### <a name="put-request-examples"></a>Vložit žádost příklady

Požadavek PUT použijete k vytvoření nových objektů v souborech NetApp Azure, jako následující příklady ukazují. Text žádosti PUT může obsahovat data ve formátu JSON pro změny nebo ho můžete určit soubor se má načíst z. 

        #create a NetApp account  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2017-08-15

        #create a capacity pool  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2017-08-15

        #create a volume  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2017-08-15

        #create a volume snapshot  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2017-08-15

### <a name="json-examples"></a>Příklady pro JSON

Následující příklad ukazuje, jak vytvořit účet NetApp:

    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 

Následující příklad ukazuje, jak vytvořit kapacitu fondu: 

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

Následující příklad ukazuje způsob vytvoření snímku svazku: 

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
> Je třeba zadat `fileSystemId` pro vytvoření snímku.  Můžete získat `fileSystemId` hodnotu požadavek GET na svazek. 

## <a name="next-steps"></a>Další postup

[Přečtěte si referenční informace rozhraní REST API služby Azure NetApp soubory](https://docs.microsoft.com/rest/api/netapp/)
