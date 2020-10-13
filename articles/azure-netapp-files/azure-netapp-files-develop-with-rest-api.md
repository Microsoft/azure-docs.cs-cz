---
title: Vývoj pro Azure NetApp Files s využitím REST API | Microsoft Docs
description: REST API služby Azure NetApp Files definuje operace HTTP pro prostředky, jako je účet NetApp, fond kapacit, svazky a snímky.
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
ms.topic: how-to
ms.date: 06/02/2020
ms.author: b-juche
ms.openlocfilehash: 4599f4e3ca291c312c99e938b237d1eb9cd9d407
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929276"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Vývoj pro službu Azure NetApp Files s využitím rozhraní REST API 

REST API služby Azure NetApp Files definuje operace HTTP s prostředky, jako je účet NetApp, fond kapacit, svazky a snímky. Tento článek vám pomůže začít s používáním Azure NetApp Files REST API.

## <a name="azure-netapp-files-rest-api-specification"></a>Specifikace Azure NetApp Files REST API

Specifikace REST API pro Azure NetApp Files je publikovaná prostřednictvím [GitHubu](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager):

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Přístup k Azure NetApp Files REST API  

1. Pokud jste to ještě neudělali, [nainstalujte rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) .
2. Vytvoření instančního objektu v Azure Active Directory (Azure AD):
   1. Ověřte, zda máte [dostatečná oprávnění](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

   2. V Azure CLI zadejte následující příkaz: 
    
        ```azurecli
        az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE
        ```

      Výstup příkazu je podobný následujícímu příkladu:  

        ```output
        { 
            "appId": "appIDgoeshere", 
            "displayName": "APPNAME", 
            "name": "http://APPNAME", 
            "password": "supersecretpassword", 
            "tenant": "tenantIDgoeshere" 
        } 
        ```

      Ponechte výstup příkazu.  Budete potřebovat `appId` `password` hodnoty, a `tenant` . 

3. Vyžádání přístupového tokenu OAuth:

    Příklady v tomto článku používají oblé. Můžete také použít různé nástroje API, jako je například [post](https://www.getpostman.com/), [režimu spánku](https://insomnia.rest/)a [privilegovaným přístupem](https://paw.cloud/).  

    Nahraďte proměnné v následujícím příkladu příkazem výstup z kroku 2 výše. 
    
    ```azurecli
    curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token
    ```

    Výstup poskytuje přístupový token podobný následujícímu příkladu:

    `eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9`

    Zobrazený token je platný po dobu 3600 sekund. Potom je potřeba požádat o nový token. 
    Uložte token do textového editoru.  Budete ho potřebovat pro další krok.

4. Odeslat testovací hovor a zahrnout token pro ověření přístupu k REST API:

    ```azurecli
    curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2019-11-01
    ```

## <a name="examples-using-the-api"></a>Příklady použití rozhraní API  

Tento článek používá následující adresu URL pro směrný plán požadavků. Tato adresa URL odkazuje na kořen oboru názvů Azure NetApp Files. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01`

`SUBIDGOESHERE` `RESOURCEGROUPGOESHERE` Hodnoty a v následujících příkladech byste měli nahradit vlastními hodnotami. 

### <a name="get-request-examples"></a>Příklady získání požadavků

Použijete požadavek GET k dotazování objektů Azure NetApp Files v předplatném, jak ukazují následující příklady: 

```azurecli
#get NetApp accounts 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01
```

```azurecli
#get capacity pools for NetApp account 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2019-11-01
```

```azurecli
#get volumes in NetApp account & capacity pool 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2019-11-01
```

```azurecli
#get snapshots for a volume 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2019-11-01
```

### <a name="put-request-examples"></a>Příklady požadavků PUT

K vytvoření nových objektů v Azure NetApp Files použijte požadavek PUT, jak ukazuje následující příklad. Tělo žádosti o vložení může obsahovat data ve formátu JSON pro změny. Musí být součástí příkazu složeného jako text nebo odkazy jako soubor. Chcete-li odkazovat na tělo jako soubor, uložte příklad JSON do souboru a přidejte `-d @<filename>` jej do příkazu složeného.

```azurecli
#create a NetApp account  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2019-11-01
```

```azurecli
#create a capacity pool  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2019-11-01
```

```azurecli
#create a volume  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2019-11-01
```

```azurecli
 #create a volume snapshot  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2019-11-01
```

### <a name="json-examples"></a>Příklady JSON

Následující příklad ukazuje, jak vytvořit účet NetApp:

```json
{ 
    "name": "MYNETAPPACCOUNT", 
    "type": "Microsoft.NetApp/netAppAccounts", 
    "location": "westus2", 
    "properties": { 
        "name": "MYNETAPPACCOUNT" 
    }
} 
```

Následující příklad ukazuje, jak vytvořit fond kapacit: 

```json
{
    "name": "MYNETAPPACCOUNT/POOLNAME",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
    "location": "westus2",
    "properties": {
        "name": "POOLNAME",
        "size": "4398046511104",
        "serviceLevel": "Premium"
    }
}
```

Následující příklad ukazuje, jak vytvořit nový svazek. (Výchozí protokol pro svazek je NFSV3.) 

```json
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
```

Následující příklad ukazuje, jak vytvořit snímek svazku: 

```json
{
    "name": "apitest2/apiPool01/apiVol01/snap02",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
    "location": "westus2",
    "properties": {
         "name": "snap02",
        "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
    }
}
```

> [!NOTE] 
> Je nutné zadat `fileSystemId` pro vytvoření snímku.  Můžete získat `fileSystemId` hodnotu s požadavkem get na svazek. 

## <a name="next-steps"></a>Další kroky

[Podívejte se na odkaz Azure NetApp Files REST API](/rest/api/netapp/)