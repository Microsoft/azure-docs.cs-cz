---
title: Azure Media Services důvěryhodné úložiště
description: V tomto kurzu se naučíte, jak povolit důvěryhodné úložiště pro Azure Media Services, používat spravovaných identity pro důvěryhodné úložiště a poskytovat službám Azure přístup k účtu úložiště při použití brány firewall nebo VPN.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 2/8/2021
ms.openlocfilehash: 18cb4e3ada94822c2f4cb1ca7675310a37e44e84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100590645"
---
# <a name="tutorial-media-services-trusted-storage"></a>Kurz: Media Services důvěryhodné úložiště

V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Jak povolit důvěryhodné úložiště pro Azure Media Services
> - Použití spravovaných identit pro důvěryhodné úložiště
> - Jak dát službám Azure přístup k účtu úložiště při použití řízení přístupu k síti, jako je brána firewall nebo VPN

S rozhraním API 2020-05-01 můžete povolit důvěryhodné úložiště tím, že přidružíte spravovanou identitu k Media Servicesmu účtu.

>[!NOTE]
>Důvěryhodné úložiště je k dispozici pouze v rozhraní API a aktuálně není povoleno v Azure Portal.

Media Services může automaticky přistupovat k účtu úložiště pomocí ověřování systému. Media Services ověří, že účet Media Services a účet úložiště jsou ve stejném předplatném. Ověří také, že uživatel přidávající přidružení má přístup k účtu úložiště s Azure Resource Manager RBAC.

Pokud ale chcete použít řízení přístupu k síti k zabezpečení svého účtu úložiště a Povolit důvěryhodné úložiště, vyžaduje se ověřování [spravovaných identit](concept-managed-identities.md) . Umožňuje Media Services získat přístup k účtu úložiště, který byl nakonfigurován s bránou firewall nebo omezením virtuální sítě prostřednictvím důvěryhodného přístupu k úložišti.

## <a name="overview"></a>Přehled

> [!IMPORTANT]
> Pro všechny požadavky na Media Services použijte rozhraní API 2020-05-01.

Toto jsou obecné kroky pro vytvoření důvěryhodného úložiště pro Media Services:

1. Vytvořte skupinu prostředků.
1. Vytvoření účtu úložiště
1. Dotazujte se na účet úložiště, dokud není připravený. Až bude účet úložiště připravený, požadavek vrátí ID objektu služby.
1. Najděte ID role *Přispěvatel dat objektu BLOB úložiště* .
1. Zavolejte poskytovateli autorizace a přidejte přiřazení role.
1. Aktualizujte účet Media Services tak, aby se ověřil na účet úložiště pomocí spravované identity.
1. Odstraňte prostředky, pokud je nechcete dále používat a účtovat za ně.

<!-- Link to storage role contributor role access differences information -->

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete předplatné Azure.  Pokud nemáte předplatné Azure, [Vytvořte si bezplatný zkušební účet](https://azure.microsoft.com/free/).

### <a name="get-your-tenant-id-and-subscription-id"></a>Získat ID tenanta a ID předplatného

Pokud si nejste jisti, jak získat ID tenanta a ID předplatného, přečtěte si téma [Jak najít ID](how-to-set-azure-tenant.md) tenanta a [najít ID tenanta](how-to-set-azure-tenant.md).

### <a name="create-a-service-principal-and-secret"></a>Vytvoření instančního objektu a tajného klíče

Pokud si nejste jisti, jak vytvořit instanční objekt a tajný klíč, přečtěte si téma [získání přihlašovacích údajů pro přístup k Media Services API](access-api-howto.md).

## <a name="use-a-rest-client"></a>Použití klienta REST

Tento skript je určený pro použití s klientem REST, jako je to, co je k dispozici v rozšířeních Visual Studio Code Extensions.  Přizpůsobte si ho pro vývojové prostředí.

## <a name="set-initial-variables"></a>Nastavení počátečních proměnných

Tato část skriptu je určena pro použití v klientovi REST. V rámci vašeho vývojového prostředí můžete proměnné používat odlišně.

```rest
### AAD details
@tenantId = your tenant ID
@servicePrincipalId = the service principal ID
@servicePrincipalSecret = the service principal secret

### AAD resources
@armResource = https%3A%2F%2Fmanagement.core.windows.net%2F
@graphResource = https%3A%2F%2Fgraph.windows.net%2F
@storageResource = https%3A%2F%2Fstorage.azure.com%2F

### Service endpoints
@armEndpoint = management.azure.com
@graphEndpoint = graph.windows.net
@aadEndpoint = login.microsoftonline.com

### ARM details
@subscription = your subscription id
@resourceGroup = the resource group you'll be creating
@storageName = the name of the storage you'll be creating
@accountName = the name of the account you'll be creating
@resourceLocation = East US (or the location that works best for your region)
```

## <a name="get-a-token-for-azure-resource-manager"></a>Získání tokenu pro Azure Resource Manager

```rest
// @name getArmToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{armResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-a-token-for-the-graph-api"></a>Získání tokenu pro Graph API

Tato část skriptu je určena pro použití v klientovi REST. V rámci vašeho vývojového prostředí můžete proměnné používat odlišně.

```rest
// @name getGraphToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{graphResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-the-service-principal-details"></a>Získat podrobnosti objektu služby

```rest
// @name getServicePrincipals
GET https://{{graphEndpoint}}/{{tenantId}}/servicePrincipals?$filter=appId%20eq%20'{{servicePrincipalId}}'&api-version=1.6
x-ms-client-request-id: cae3e4f7-17a0-476a-a05a-0dab934ba959
Authorization:  Bearer {{getGraphToken.response.body.access_token}}
```

## <a name="store-the-service-principal-id"></a>Uložení ID objektu služby

```rest
@servicePrincipalObjectId = {{getServicePrincipals.response.body.value[0].objectId}}
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

```rest
// @name createResourceGroup
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}
    ?api-version=2016-09-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "location": "{{resourceLocation}}"
}
```

## <a name="create-storage-account"></a>Vytvoření účtu úložiště

```rest
// @name createStorageAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
    }
}
```

## <a name="get-the-storage-account-status"></a>Získat stav účtu úložiště

Účet úložiště bude chvíli trvat, takže se tento požadavek bude dotazovat na jeho stav.  Tento požadavek opakujte, dokud nebude účet úložiště připravený.

```rest
// @name getStorageAccountStatus
GET {{createStorageAccount.response.headers.Location}}
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-the-storage-account-details"></a>Získat podrobnosti účtu úložiště

Až bude účet úložiště připravený, Získejte vlastnosti účtu úložiště.

```rest
// @name getStorageAccount
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-a-token-for-arm"></a>Získání tokenu pro ARM

```rest
// @name getStorageToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{storageResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="create-a-media-services-account-with-a-system-assigned-managed-identity"></a>Vytvoření účtu Media Services se spravovanou identitou přiřazenou systémem

```rest
// @name createMediaServicesAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="get-the-storage-storage-blob-data-role-definition"></a>Získání definice role dat objektu BLOB úložiště úložiště

```rest
// @name getStorageBlobDataContributorRoleDefinition
GET https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions?$filter=roleName%20eq%20%27Storage%20Blob%20Data%20Contributor%27&api-version=2015-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

### <a name="set-the-storage-role-assignment"></a>Nastavení přiřazení role úložiště

Přiřazení role říká, že instanční objekt pro účet Media Services má *přispěvatele dat objektů BLOB úložiště* role úložiště.  Může to chvíli trvat a je důležité počkat nebo se účet Media Services nenastaví správně.

```rest
PUT https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleAssignments/{{$guid}}?api-version=2020-04-01-preview
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "roleDefinitionId": "/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions/{{getStorageBlobDataContributorRoleDefinition.response.body.value[0].name}}",
    "principalId": "{{createMediaServicesAccount.response.body.identity.principalId}}"
  }
}
```

## <a name="give-managed-identity-bypass-access-to-the-storage-account"></a>Udělení přístupu spravované identity k účtu úložiště

Tato akce změní přístup ze systémové identity spravované na spravovanou identitu. Tímto způsobem může účet úložiště získat přístup k účtu úložiště přes bránu firewall, protože služby Azure mají přístup k účtu úložiště bez ohledu na pravidla přístupu k IP adresám (ACL).

Znovu počkejte, než se role v účtu úložiště přiřadí, nebo se účet Media Services nesprávně nastaví.

```rest
// @name setStorageAccountFirewall
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
      "minimumTlsVersion": "TLS1_2",
      "networkAcls": {
        "bypass": "AzureServices",
        "virtualNetworkRules": [],
        "ipRules": [],
        "defaultAction": "Deny"
      }
    }
}
```

## <a name="update-the-media-services-account-to-use-the-managed-identity"></a>Aktualizace účtu Media Services pro použití spravované identity

Tento požadavek může být potřeba opakovat několikrát, protože rozšíření přiřazení role úložiště může trvat několik minut.

```rest
// @name updateMediaServicesAccountWithManagedStorageAuth
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "storageAuthentication": "ManagedIdentity",
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="test-access"></a>Testovat přístup

Otestujte přístup vytvořením assetu v účtu úložiště.

```rest
// @name createAsset
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}/assets/testasset{{index}}withoutmi?api-version=2018-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
}
```

## <a name="delete-resources"></a>Odstranění prostředků

Pokud nechcete, aby byly prostředky, které jste vytvořili, a nadále se jim budou účtovat poplatky, odstraňte je.

```rest
### Clean up the Storage account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="next-steps"></a>Další kroky

[Postup vytvoření Assetu](how-to-create-asset.md)
