---
title: Programové vytváření předplatných Azure pro Smlouvu s partnerem Microsoftu s využitím nejnovějších rozhraní API
description: Naučte se programově vytvářet předplatná Azure pro Smlouvu s partnerem Microsoftu s využitím nejnovějších verzí rozhraní REST API, Azure CLI a Azure PowerShellu.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: de1183c1364fcb7e5483559899c2939df15d26b6
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215762"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-partner-agreement-with-the-latest-apis"></a>Programové vytváření předplatných Azure pro Smlouvu s partnerem Microsoftu s využitím nejnovějších rozhraní API

Tento článek vám pomůže programově vytvářet předplatná Azure pro Smlouvu s partnerem Microsoftu s využitím nejnovějších verzí rozhraní API. Pokud ještě používáte starší verzi Preview, přejděte na téma [Programové vytváření předplatných Azure pomocí rozhraní API ve verzi Preview](programmatically-create-subscription-preview.md). 

V tomto článku se naučíte, jak programově vytvářet předplatná s využitím Azure Resource Manageru.

Když vytvoříte předplatné Azure programově, na toto předplatné se vztahuje smlouva, na základě které jste získali služby Azure od společnosti Microsoft nebo autorizovaného prodejce. Další informace najdete v článku [Právní informace o Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

Abyste mohli vytvořit předplatné pro váš fakturační účet, musíte mít v účtu poskytovatele cloudového řešení vaší organizace roli globálního správce nebo agenta pro správu. Další informace najdete v tématu [Partnerské centrum – přiřazování uživatelských rolí a oprávnění](/partner-center/permissions-overview).

Pokud nevíte, jestli máte přístup k účtu se Smlouvou s partnerem Microsoftu, projděte si část [Kontrola přístupu ke Smlouvě s partnerem Microsoftu](mpa-request-ownership.md#check-access-to-a-microsoft-partner-agreement).

V následujících příkladech se používají rozhraní REST API. PowerShell ani Azure CLI se v současné době nepodporují.

## <a name="find-the-billing-accounts-that-you-have-access-to"></a>Vyhledání fakturačních účtů, ke kterým máte přístup

Pomocí následujícího požadavku zobrazte seznam všech fakturačních účtů, ke kterým máte přístup.

### <a name="rest"></a>[REST](#tab/rest-getBillingAccount-MPA)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

V odpovědi rozhraní API se zobrazí seznam fakturačních účtů.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Partner",
        "agreementType": "MicrosoftPartnerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": true
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Pomocí vlastnosti `displayName` identifikujte fakturační účet, pro který chcete vytvářet předplatná. Zkontrolujte, jestli má agreementType tohoto účtu hodnotu *MicrosoftPartnerAgreement*. Zkopírujte `name` tohoto účtu. Pokud například chcete vytvořit předplatné pro fakturační účet `Contoso`, zkopírujte `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Tuto hodnotu někam vložte, abyste ji mohli použít v dalším kroku.

<!--
### [PowerShell](#tab/azure-powershell-getBillingAccounts-MPA)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-getBillingAccounts-MPA)

```azurecli
> az billing account list
```
Zobrazí se seznam všech fakturačních účtů, ke kterým máte přístup. 

```json
[
  {
    "accountStatus": "Active",
    "accountType": "Partner",
    "agreementType": "MicrosoftPartnerAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": null,
    "enrollmentDetails": null,
    "hasReadAccess": true,
    "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "soldTo": null,
    "type": "Microsoft.Billing/billingAccounts"
  }
]
```

Pomocí vlastnosti DisplayName Identifikujte fakturační účet, pro který chcete vytvořit odběry. Zkontrolujte, jestli má agreementType tohoto účtu hodnotu MicrosoftPartnerAgreement. Zkopírujte název účtu. Pokud třeba chcete vytvořit předplatné pro fakturační účet contoso, zkopírujte 99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx. Tuto hodnotu někam vložte, abyste ji mohli použít v dalším kroku.

---

## <a name="find-customers-that-have-azure-plans"></a>Vyhledání zákazníků s plány Azure

Proveďte následující požadavek, ve kterém zadáte údaj `name` zkopírovaný z prvního kroku (```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```). Zobrazí se seznam všech zákazníků ve fakturačním účtu, pro které můžete vytvářet předplatná Azure.

### <a name="rest"></a>[REST](#tab/rest-getCustomers)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers?api-version=2020-05-01
```

V odpovědi rozhraní API se zobrazí zákazníci ve fakturačním účtu s plány Azure. Pro tyto zákazníky můžete vytvářet předplatná.

```json
{
  "totalCount": 2,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Contoso toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }
  ]
}

```

Pomocí vlastnosti `displayName` identifikujte zákazníka, pro kterého chcete vytvářet předplatná. Zkopírujte `id` tohoto zákazníka. Pokud například chcete vytvořit předplatné pro zákazníka `Fabrikam toys`, zkopírujte `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Tuto hodnotu někam vložte, abyste ji mohli použít v dalších krocích.

<!--
### [PowerShell](#tab/azure-powershell-getCustomers)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->


### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-getCustomers)

```json
> az billing customer list --account-name 99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx
```

V odpovědi rozhraní API se zobrazí zákazníci ve fakturačním účtu s plány Azure. Pro tyto zákazníky můžete vytvářet předplatná.

```json
[
  {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "displayName": "Fabrikam toys",
    "id": "providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "resellers": null,
    "type": "Microsoft.Billing/billingAccounts/customers"
  },
  {
    "billingProfileDisplayName": "Contoso toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "displayName": "Contoso toys",
    "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "d49c364c-f866-4cc2-a284-d89f369b7951",
    "resellers": null,
    "type": "Microsoft.Billing/billingAccounts/customers"
  }
]

```

Pomocí vlastnosti `displayName` identifikujte zákazníka, pro kterého chcete vytvářet předplatná. Zkopírujte `id` tohoto zákazníka. Pokud například chcete vytvořit předplatné pro zákazníka `Fabrikam toys`, zkopírujte `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Tuto hodnotu někam vložte, abyste ji mohli použít v dalších krocích.

---

## <a name="get-the-resellers-for-a-customer"></a>Zjištění prodejců pro zákazníka

Tato část je volitelná a určená pouze pro nepřímé poskytovatele.

Pokud jste nepřímý poskytovatel v dvouúrovňovém modelu CSP, můžete při vytváření předplatných pro zákazníky určit prodejce.

### <a name="rest"></a>[REST](#tab/rest-getIndirectResellers)

Proveďte následující požadavek s údajem `id` zkopírovaným v druhém kroku (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Zobrazí se seznam všech prodejců, kteří jsou pro příslušného zákazníka k dispozici.

```json
GET "https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx?$expand=resellers&api-version=2020-05-01"
```
V odpovědi rozhraní API se zobrazí prodejci pro tohoto zákazníka:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
}]
}
```

Pomocí vlastnosti `description` určete prodejce, který bude přidružený k předplatnému. Zkopírujte `resellerId` tohoto prodejce. Pokud například chcete přidružit prodejce `Wingtip`, zkopírujte `3xxxxx`. Tuto hodnotu někam vložte, abyste ji mohli použít v dalším kroku.

<!--
### [PowerShell](#tab/azure-powershell-getIndirectResellers)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-getIndirectResellers)

Proveďte následující požadavek s `name` kopírováním z prvního kroku ( ```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx``` ) a zákazníka `name` zkopírovaného z předchozího kroku ( ```acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` ).

```azurecli-interactive
 > az billing customer show --expand "enabledAzurePlans,resellers" --account-name "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx" --name "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

V odpovědi rozhraní API se zobrazí prodejci pro tohoto zákazníka:

```json
{
  "billingProfileDisplayName": "Fabrikam toys Billing Profile",
  "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
  "displayName": "Fabrikam toys",
  "enabledAzurePlans": [
    {
      "skuDescription": "Microsoft Azure Plan",
      "skuId": "0001"
    }
  ],
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resellers": [
    {
      "description": "Wingtip",
      "resellerId": "3xxxxx"
    }
  ],
  "type": "Microsoft.Billing/billingAccounts/customers"
}

```

Pomocí vlastnosti `description` určete prodejce, který bude přidružený k předplatnému. Zkopírujte `resellerId` tohoto prodejce. Pokud například chcete přidružit prodejce `Wingtip`, zkopírujte `3xxxxx`. Tuto hodnotu někam vložte, abyste ji mohli použít v dalším kroku.

---

## <a name="create-a-subscription-for-a-customer"></a>Vytvoření předplatného pro zákazníka

Následující příklad vytvoří předplatné s názvem *Dev Team Subscription* pro *Fabrikam toys* a přidruží k tomuto předplatnému prodejce *Wingtip*. Použijete rozsah fakturace zkopírovaný v předchozím kroku: `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

### <a name="rest"></a>[REST](#tab/rest-MPA)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>Text požadavku

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "DisplayName": "Dev Team subscription",
        "Workload": "Production"
    }
}
```
### <a name="response"></a>Odpověď

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

Stav požadavku můžete získat metodou GET na stejné adrese URL.

### <a name="request"></a>Žádost

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>Odpověď

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

Probíhající stav se vrátí jako stav `Accepted` u položky `provisioningState`. 

V textu požadavku pro rozhraní API zadejte volitelnou hodnotu *resellerId* zkopírovanou v druhém kroku.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-MPA)

Chcete-li nainstalovat nejnovější verzi modulu, který obsahuje rutinu `New-AzSubscriptionAlias`, spusťte `Install-Module Az.Subscription`. Pokud chcete nainstalovat novější modulu PowerShellGet, projděte si téma [Získání modulu PowerShellGet](/powershell/scripting/gallery/installing-psget).

Spusťte následující příkaz [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) a použijte v něm rozsah fakturace `"/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -Workload 'Production"
```

V rámci odpovědi příkazu se vrátí údaj subscriptionId.

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

Ve volání `New-AzSubscriptionAlias` uveďte volitelnou hodnotu *resellerId* zkopírovanou v druhém kroku.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-MPA)

Nejdřív spuštěním příkazů `az extension add --name account` a `az extension add --name alias` nainstalujte rozšíření.

Spusťte následující příkaz [az account alias create](/cli/azure/ext/account/account/alias#ext_account_az_account_alias_create). 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --display-name "Dev Team Subscription" --workload "Production"
```

V rámci odpovědi příkazu se vrátí údaj subscriptionId.

```azurecli
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

Ve volání `az account alias create` zadejte volitelnou hodnotu *resellerId* zkopírovanou v druhém kroku.

---

## <a name="next-steps"></a>Další kroky

* Teď když jste vytvořili předplatné, můžete tuto možnost poskytnout dalším uživatelům a instančním objektům. Další informace najdete v tématu [Udělení přístupu pro vytváření předplatných Azure Enterprise (Preview)](grant-access-to-create-subscription.md).
* Další informace o správě velkého počtu předplatných pomocí skupin pro správu najdete v tématu věnovaném [uspořádání prostředků pomocí skupin pro správu v Azure](../../governance/management-groups/overview.md).
