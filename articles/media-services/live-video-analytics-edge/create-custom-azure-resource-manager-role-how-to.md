---
title: Vytvoření vlastní role Azure Resource Manager a přiřazení k instančnímu objektu – Azure
description: Tento článek poskytuje pokyny k vytvoření vlastní role Azure Resource Manager a přiřazení k instančnímu objektu pro živé video analýzy v IoT Edge pomocí Azure CLI.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 80974c111dd451314635d06334766322bc68e437
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210440"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal"></a>Vytvoření vlastní role Azure Resource Manager a přiřazení k instančnímu objektu

Live video Analytics v instanci modulu IoT Edge potřebuje pro správné fungování aktivní účet Azure Media Services. Vztah mezi živým analýzou videa v modulu IoT Edge a účtem Azure Media Service je vytvořen pomocí sady vlastností funkčního modulu. Jednou z těchto dvojitých vlastností je [instanční objekt](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) , který umožňuje instanci modulu komunikovat s a triggerem potřebných operací na účtu Media Services. K minimalizaci potenciálního zneužití nebo náhodnému úniku dat z hraničního zařízení by měl tento instanční objekt mít nejnižší úroveň oprávnění.

V tomto článku se dozvíte, jak vytvořit vlastní roli Azure Resource Manager s Azure Cloud Shell, která pak slouží k vytvoření instančního objektu.

## <a name="prerequisites"></a>Předpoklady  

Požadavky pro tento článek jsou následující:

* Předplatné Azure s předplatným vlastníka.
* Azure Active Directory s oprávněním k vytvoření aplikace a přiřazení instančního objektu k roli.

Nejjednodušším způsobem, jak zkontrolovat, jestli má váš účet dostatečná oprávnění, je použít k tomu portál. Informace najdete v článku [Kontrola požadovaných oprávnění](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="overview"></a>Přehled  

Provedeme kroky k vytvoření vlastní role a její propojení s instančním objektem v následujícím pořadí:

1. Vytvořte účet Media Service, pokud ho ještě nemáte.
1. Vytvoření instančního objektu.
1. Vytvořte vlastní roli Azure Resource Manager s omezenými oprávněními.
1. "Omezit" oprávnění instančního objektu pomocí vytvořené vlastní role.
1. Spusťte jednoduchý test, abyste zjistili, jestli je možné úspěšně omezit instanční objekt.
1. Zachyťte parametry, které budou použity v manifestech nasazení IoT Edge.

### <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services  

Pokud nemáte účet Media Service, vytvořte ho pomocí následujících kroků.

1. Přejděte na [Cloud Shell](https://shell.azure.com/).
1. V rozevíracím seznamu na levé straně okna prostředí vyberte "bash" jako své prostředí.

    ![Capturs obrazovky zobrazuje bash vybrané z okna prostředí.](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. Jako výchozí účet nastavte své předplatné Azure pomocí následující šablony příkazů:
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. Vytvořte [skupinu prostředků](/cli/azure/group#az-group-create) a [účet úložiště](/cli/azure/storage/account#az-storage-account-create).
1. Nyní vytvořte účet služby Azure Media Service pomocí následující šablony příkazů v Cloud Shell:

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>Vytvoření instančního objektu  

Nyní vytvoříme nový instanční objekt a připojíme ho k vašemu účtu Media Service.

Bez parametrů ověřování se pro instanční objekt používá ověřování založené na hesle s náhodným heslem. V Cloud Shell použijte následující šablonu příkazu:

```
az ams account sp create --account-name < yourAMSAccountName > --resource-group < yourResouceGroup >
```

Tento příkaz vytvoří odpověď podobnou této:

```
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "<yourServicePrincipalPassword>",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": " <yourAMSAccountName >",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "Central US",
  "ResourceGroup": " <yourResouceGroup >",
  "SubscriptionId": "<yourSubscriptionId>"
}

```
1. Výstup pro instanční objekt s ověřováním hesla zahrnuje klíč hesla, který v tomto případě je parametrem "AadSecret". 

    Nezapomeňte tuto hodnotu zkopírovat – nedá se načíst. Pokud zapomenete heslo, [resetujte přihlašovací údaje instančního objektu](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials).
1. AppId a klíč tenanta se zobrazí ve výstupu jako "AadClientId" a "AadTenantId" v uvedeném pořadí. Používají se při ověřování instančního objektu. Poznamenejte si jejich hodnoty, ale můžete je kdykoli načíst pomocí příkaz [AZ AD SP list](/cli/azure/ad/sp#az-ad-sp-list).

### <a name="create-a-custom-role-definition"></a>Vytvoření vlastní definice role  

Pokud chcete vytvořit vlastní roli, postupujte podle následujících kroků:

1. Vytvořte soubor JSON definice role v místním systému a v souboru uložte následující text. 
    1. Nahrazení < yourSubscriptionId> pomocí ID předplatného Azure
    1. Pro tuto roli jsou povolené jenom tyto akce:
        * listContainerSas – pomáhá modulům seznam adres URL kontejnerů úložiště se sdíleným přístupovým podpisem (SAS) pro nahrávání a stahování obsahu prostředku.
        * Zápis assetů – pomáhá modulu vytvořit nebo aktualizovat jakýkoli Asset.
        * listEdgePolicies – zobrazí seznam zásad, které se aplikují na hraniční zařízení.  
        
        ```
        {
          "Name": "LVAEdge User",
          "IsCustom": true,
          "Description": "Can create assets, view list of containers and view Edge policies",
          "Actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action"
          ],
          "NotActions": [],
          "DataActions": [],
          "NotDataActions": [],
          "AssignableScopes": [
            "/subscriptions/<yourSubscriptionId>"
          ]
        }
        ```  
          
1. Po vytvoření spusťte následující šablonu příkazu pro vytvoření nové definice role v předplatném:
    
    ```
    az role definition create --role-definition "<location of the Role Definition JSON file >"
    ```

    Po úspěšném provedení příkazu se zobrazí následující výstup:
    
    ```
    {
      "assignableScopes": [
      "/subscriptions/<yourSubscriptionId>"
      ],
      "description": "Can create assets, view list of containers and view Edge policies",
      "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<unique name>",
      "name": "<unique name>",
      "permissions": [
        {
          "actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action",
          ],
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": " LVAEdge User ",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

### <a name="create-role-assignment"></a>Vytvořit přiřazení role  

Chcete-li přidat přiřazení role, budete potřebovat objectId objektu služby, který chcete přiřadit vlastní roli, kterou jste právě vytvořili.

K získání ID objectId použijte následující příkaz v Cloud Shell.

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> `<appId>` lze načíst z výstupu kroku [vytvořit instanční objekt služby](#create-service-principal) .

Výše uvedený příkaz vypíše objectId objektu služby. 

```
“objectId” : “<yourObjectId>”,
```

Pomocí [příkazu AZ role Assignment vytvořit šablonu příkazu](/cli/azure/role/assignment#az-role-assignment-create) propojte vlastní roli s objektem služby:

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

Parametry:

|Parametry|Description| 
|---|---|
|--role |Název nebo ID vlastní role V našem případě: "LVAEdge uživatel".|
|--nabyvatel-objektu-ID|ID objektu instančního objektu, který budete používat.|

Výsledek bude vypadat takto:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleAssignments/<yourCustomRoleId>",
  "name": "<yourCustomRoleId>",
  "principalId": "<yourServicePrincipalId>",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<yourRoleDefinitionId>",
  "scope": "/subscriptions/<yourSubscriptionId>",
  "type": "Microsoft.Authorization/roleAssignments"
} 
```

### <a name="confirm-that-role-assignment-happened"></a>Potvrďte, že k přiřazení role došlo

Pokud chcete potvrdit, že je objekt služby teď propojený s vlastní rolí, kterou jsme právě vytvořili, spusťte následující příkaz:

```
az role assignment list  --assignee < objectId>
```

Výsledek by měl vypadat takto:

```
[
  {
    "canDelegate": null,
    "id": "/subscriptions/xxx/providers/Microsoft.Authorization/roleAssignments/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "principalId": "<yourServicePrincipalID>",
    "principalName": "<yourServicePrincipalName>",
    "principalType": "ServicePrincipal",
    "roleDefinitionId": "/subscriptions/xxx/providers/Microsoft.Authorization/roleDefinitions/zzz",
    "roleDefinitionName": "LVAEdge User",
    "scope": "/subscriptions/<yourSubscription ID>",
    "type": "Microsoft.Authorization/roleAssignments
  }
]  
```
 
Vyhledejte "roleDefinitionName" a podívejte se, že jeho hodnota je nastavená na "LVAEdge User". 

Tím se potvrdí, že jsme propojili vlastní roli uživatele s instančním objektem, který se používá pro naši aplikaci.

### <a name="test-the-service-principal-access-control"></a>Testování řízení přístupu instančního objektu

1. Přihlaste se pomocí instančního objektu. V tomto případě budeme potřebovat 3 části informací, které Azure Active Directory poskytnout pro nás správný přístupový token, který můžeme získat z výstupu kroku [Vytvoření instančního objektu](#create-service-principal) :
    1. AadClientID 
    1. AadSecret
    1. AadTenantId
1. Nyní se můžete pokusit přihlásit pomocí šablony příkazu níže:
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  Teď se podívejme na to, jestli je přihlášení omezené na instanční objekt s rolí "LVAEdge User", a to tak, že se pokusíte vytvořit skupinu prostředků, abyste se ujistili, že se nezdařila. Spusťte následující příkaz v Cloud Shell:

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    Tento příkaz by měl selhat a bude vypadat takto:
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>Další kroky  

Všimněte si následujících hodnot z tohoto článku. Tyto hodnoty se budou vyžadovat pro konfiguraci dvojitých vlastností živé analýzy videí v modulu IoT Edge. Podívejte se na téma nefunkční [schéma JSON pro modul](module-twin-configuration-schema.md).

| Proměnná z tohoto článku|Název zdvojené vlastnosti pro analýzy živých videí v IoT Edge|
|---|---|
|AadSecret |    aadServicePrincipalPassword|
|AadTenantId |  aadTenantId|
|AadClientId    |aadServicePrincipalAppId|
