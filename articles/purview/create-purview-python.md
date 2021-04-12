---
title: 'Rychlý Start: vytvoření účtu dosah pomocí Pythonu'
description: Vytvořte účet Azure dosah pomocí Pythonu.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: python
ms.topic: quickstart
ms.date: 04/02/2021
ms.openlocfilehash: f8ac611d25507913d6d5f2e2dd289ea52ce4ae9f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387461"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>Rychlý Start: vytvoření účtu dosah pomocí Pythonu

V tomto rychlém startu vytvoříte účet dosah pomocí Pythonu. 

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Vlastní [klient Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Váš účet musí mít oprávnění k vytváření prostředků v předplatném.

* Pokud jste **Azure Policy** blokující všechny aplikace při vytváření **účtu úložiště** a **oboru názvů EventHub**, je potřeba vytvořit výjimku zásad pomocí značky, kterou je možné zadat během procesu vytváření účtu dosah. Hlavním důvodem je to, že pro každý vytvořený účet dosah je potřeba vytvořit spravovanou skupinu prostředků a v rámci této skupiny prostředků, účtu úložiště a oboru názvů EventHub. Další informace najdete v tématu [vytvoření portálu katalogu](create-catalog-portal.md) .


## <a name="install-the-python-package"></a>Instalace balíčku Pythonu

1. Otevřete terminál nebo příkazový řádek s oprávněními správce. 
2. Nejdřív nainstalujte balíček Pythonu pro prostředky správy Azure:

    ```python
    pip install azure-mgmt-resource
    ```
3. Pro instalaci balíčku Python pro dosah spusťte následující příkaz:

    ```python
    pip install azure-mgmt-purview
    ```

    [Python SDK for dosah](https://github.com/Azure/azure-sdk-for-python) podporuje Python 2,7, 3,3, 3,4, 3,5, 3,6 a 3,7.

4. Pokud chcete nainstalovat balíček Python pro ověřování identity Azure, spusťte následující příkaz:

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > V některých běžných závislostech může být balíček Azure-identity v konfliktu s "Azure-CLI". Pokud splňujete jakýkoliv problém s ověřováním, odeberte Azure-CLI a jeho závislosti nebo použijte čistý počítač bez instalace balíčku Azure-CLI, aby fungoval.
    
## <a name="create-a-purview-client"></a>Vytvoření klienta dosah

1. Vytvořte soubor s názvem **PurView.py**. Přidejte následující příkazy pro přidání odkazů na obory názvů.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    ```

2. Do metody **Main** přidejte následující kód, který vytvoří instanci třídy PurviewManagementClient. Tento objekt použijete k vytvoření účtu dosah, odstranění účtu dosah, kontrole dostupnosti názvů a dalších operací poskytovatele prostředků.
 
 ```python
    def main():
    
    # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Location name, where Purview account must be created.
    location = '<location name>'    

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    ```

## <a name="create-a-purview-account"></a>Vytvoření účtu dosah

Do metody **Main** přidejte následující kód, který vytvoří **účet dosah**. Pokud skupina prostředků už existuje, zakomentujte první příkaz `create_or_update`.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location =location )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", pa.name, " Id: " , pa.id ," tags: " , pa.tags)  
    except:
        print("Error")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)      
        
```

Potom přidejte následující příkaz, který vyvolá metodu **main** při spuštění programu:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Celý skript

Tady je kompletní kód v Pythonu:

```python
    
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    
     # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location ="southcentralus" )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", purview_name, " Id: " , pa.id ," tags: " , pa.tags) 
    except:
        print("Error in submitting job to create account")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)    

# Start the main method
main()
```

## <a name="run-the-code"></a>Spuštění kódu

Sestavte a spusťte aplikaci a potom ověřte spuštění kanálu.

Konzola vytiskne průběh vytváření datové továrny, propojených služeb, datových sad, kanálu a spuštění kanálu. Počkejte, dokud aktivita kopírování nezobrazí údaje o velikosti načtených/zapsaných dat. Pak pomocí nástrojů, jako je například [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) , zkontrolujte, zda jsou objekty blob zkopírovány do "outputBlobPath" z "inputBlobPath", jak jste určili v proměnných.

Zde je ukázkový výstup:

```console
location: southcentralus  Purview Account Name:  purviewpython7  Id:  /subscriptions/8c2c7b23-848d-40fe-b817-690d79ad9dfd/resourceGroups/Demo_Catalog/providers/Microsoft.Purview/accounts/purviewpython7  tags:  None
Creating
Creating
Succeeded
```

## <a name="verify-the-output"></a>Ověření výstupu

Přejít na stránku **účty dosah** v Azure Portal a ověřte účet vytvořený pomocí výše uvedeného kódu. 

## <a name="delete-purview-account"></a>Odstranit účet dosah

K odstranění účtu dosah přidejte do programu následující kód:

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>Další kroky

Kód v tomto kurzu vytvoří účet dosah a odstraní účet dosah. Teď si můžete stáhnout sadu Python SDK a získat informace o dalších akcích poskytovatele prostředků, které můžete provést pro účet dosah.

V dalším článku se dozvíte, jak uživatelům dovolit přístup k vašemu účtu Azure dosah. 

> [!div class="nextstepaction"]
> [Přidání uživatelů k účtu Azure dosah](catalog-permissions.md)
