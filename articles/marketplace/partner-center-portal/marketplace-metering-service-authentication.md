---
title: Strategie ověřování služby měření na Marketplace | Azure Marketplace
description: Strategie ověřování služby měření podporované v Azure Marketplace.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/03/2020
ms.openlocfilehash: 31b9d4d57e38adcd079082a4f32770c4cbc8fbb3
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736198"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Strategie ověřování služby měření na Marketplace

Služba měření na Marketplace podporuje dvě strategie ověřování:

* [Token zabezpečení Azure AD](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
* [Spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 

Vysvětlíme, kdy a jak používat různé strategie ověřování k bezpečnému odesílání vlastních měřičů pomocí služby měření na webu Marketplace.

## <a name="using-the-azure-ad-security-token"></a>Použití tokenu zabezpečení Azure AD

Příslušné typy nabídek jsou SaaS a aplikace Azure s typem plánu spravované aplikace.  

Odeslání vlastních měřičů pomocí předdefinovaného pevného ID aplikace k ověření.

V případě nabídek SaaS je dostupná jenom možnost Azure AD.

Pro aplikace Azure s plánem spravované aplikace byste měli zvážit použití této strategie v následujících případech:

* Už máte mechanismus pro komunikaci s back-end službami a chcete tento mechanismus roztáhnout, aby se z centrální služby vygenerovaly vlastní měřiče.
* Máte složitou vlastní logiku měřičů.  Spusťte tuto logiku místo prostředků spravovaných aplikací v centrálním umístění.

Po zaregistrování aplikace můžete programově požádat o token zabezpečení Azure AD. Očekává se, že vydavatel použije tento token a vytvoří požadavek na jeho vyřešení.

Další informace o těchto tokenech naleznete v tématu [Azure Active Directory Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Získání tokenu založeného na aplikaci Azure AD

#### <a name="http-method"></a>HTTP – metoda

**POST**

#### <a name="request-url"></a>*Adresa URL požadavku*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*Parametr URI*

|  **Název parametru** |  **Požadováno**  |  **Popis**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   True         | ID tenanta registrované aplikace služby Azure AD   |
| | | |

#### <a name="request-header"></a>*Hlavička požadavku*

|  **Název hlavičky**    |  **Požadováno**  |  **Popis**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   True         | Typ obsahu přidružený k žádosti Výchozí hodnota je `application/x-www-form-urlencoded`.  |
| | | |

#### <a name="request-body"></a>*Text požadavku*

|  **Název vlastnosti**  |  **Požadováno**  |  **Popis**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   True         | Typ udělení Výchozí hodnota je `client_credentials`. |
|  `Client_id`        |   True         | Identifikátor klienta nebo aplikace přidružený k aplikaci Azure AD|
|  `client_secret`    |   True         | Heslo přidružené k aplikaci Azure AD  |
|  `Resource`         |   True         | Cílový prostředek, pro který je požadován token. Výchozí hodnota je `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`.  |
| | | |

#### <a name="response"></a>*Základě*

|  **Název**    |  **Typ**  |  **Popis**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | Požadavek byl úspěšný.  |
| | | |

#### <a name="tokenresponse"></a>*TokenResponse*

Vzorový token odpovědi:

```JSON
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

## <a name="using-the-azure-managed-identities-token"></a>Použití tokenu identity spravovaného Azure

Platný typ nabídky je aplikace Azure s typem plánu spravované aplikace.

Použití tohoto přístupu umožní, aby se identita nasazených prostředků ověřovala, aby odesílala události využití vlastních měřičů.  Kód, který generuje využití, můžete vložit v rámci hranic vašeho nasazení.

>[!Note]
>Vydavatel by měl zajistit, aby prostředky, které emitují použití, byly uzamčené, takže nebude manipulováno.

Vaše spravovaná aplikace může obsahovat jiný typ prostředků, od Virtual Machines po Azure Functions.  Další informace o tom, jak ověřit pomocí spravovaných identit pro různé služby, najdete v tématu [Jak používat spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-can-i-use-managed-identities-for-azure-resources).

Například použijte následující postup k ověření pomocí virtuálního počítače s Windows.

1. Ujistěte se, že je spravovaná identita nakonfigurovaná pomocí jedné z metod:
    * [Azure Portal UI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)
    * [Rozhraní příkazového řádku](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm)
    * [PowerShell](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm)
    * [Šablona Azure Resource Manageru](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm)
    * [REST](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-rest-vm#system-assigned-managed-identity)
    * [Sady Azure SDK](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm)

1. Získejte přístupový token pro ID aplikace služby na webu Marketplace (`20e940b3-4c77-4b0b-9a53-9e16a1b010a7`) pomocí identity systému, na virtuálním počítači RDP, otevřete konzolu PowerShellu a spusťte příkaz níže.

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. Získá ID spravované aplikace z aktuální vlastnosti ManagedBy skupin prostředků.

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 
    
    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. Služba měření na Marketplace vyžaduje `resourceID`, abyste nahlásili využití na `resourceUsageId` , a pokud spravovaná aplikace.

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. Využijte [rozhraní API služby měření na webu Marketplace](https://review.docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis?branch=pr-en-us-101847) k vygenerování využití.

## <a name="next-steps"></a>Další kroky

* [Vytvoření nabídky aplikací Azure](./create-new-azure-apps-offer.md)