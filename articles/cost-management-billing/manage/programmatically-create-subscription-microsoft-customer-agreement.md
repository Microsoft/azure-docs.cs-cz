---
title: Programové vytváření předplatných Azure pro Smlouvu se zákazníkem Microsoftu s využitím nejnovějších rozhraní API
description: Naučte se programově vytvářet předplatná Azure pro Smlouvu se zákazníkem Microsoftu s využitím nejnovějších verzí rozhraní REST API, Azure CLI a Azure PowerShellu.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 2b0eaef7940b84dbd1e1325b5ae3bfb7b65dcef6
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200559"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-customer-agreement-with-the-latest-apis"></a>Programové vytváření předplatných Azure pro Smlouvu se zákazníkem Microsoftu s využitím nejnovějších rozhraní API

Tento článek vám pomůže programově vytvářet předplatná Azure pro Smlouvu se zákazníkem Microsoftu s využitím nejnovějších verzí rozhraní API. Pokud ještě používáte starší verzi Preview, přejděte na téma [Programové vytváření předplatných Azure pomocí rozhraní API ve verzi Preview](programmatically-create-subscription-preview.md). 

V tomto článku se naučíte, jak programově vytvářet předplatná s využitím Azure Resource Manageru.

Když vytvoříte předplatné Azure programově, na toto předplatné se vztahuje smlouva, na základě které jste získali služby Azure od společnosti Microsoft nebo autorizovaného prodejce. Další informace najdete v článku [Právní informace o Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

Abyste mohli vytvářet předplatná, musíte mít roli vlastníka, přispěvatele nebo tvůrce předplatného Azure pro oddíl faktury nebo roli vlastníka nebo přispěvatele pro fakturační profil nebo fakturační účet. Další informace najdete v tématu [Role a úlohy fakturace předplatného](understand-mca-roles.md#subscription-billing-roles-and-tasks).

Pokud nevíte, jestli máte přístup k účtu se Smlouvou se zákazníkem Microsoftu, projděte si část [Kontrola přístupu ke Smlouvě se zákazníkem Microsoftu](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement).

V následujících příkladech se používají rozhraní REST API. PowerShell ani Azure CLI se v současné době nepodporují.

## <a name="find-billing-accounts-that-you-have-access-to"></a>Vyhledání fakturačních účtů, ke kterým máte přístup

Pomocí následujícího požadavku zobrazte seznam všech fakturačních účtů.

### <a name="rest"></a>[REST](#tab/rest-getBillingAccounts)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01

```
V odpovědi rozhraní API se zobrazí všechny fakturační účty, ke kterým máte přístup.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Pomocí vlastnosti `displayName` identifikujte fakturační účet, pro který chcete vytvářet předplatná. Zkontrolujte, jestli má agreementType tohoto účtu hodnotu *MicrosoftCustomerAgreement*. Zkopírujte `name` tohoto účtu.  Pokud například chcete vytvořit předplatné pro fakturační účet `Contoso`, zkopírujte `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Tuto hodnotu někam vložte, abyste ji mohli použít v dalším kroku.

<!--
### [PowerShell](#tab/azure-powershell-getBillingAccounts)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingAccounts)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="find-billing-profiles--invoice-sections-to-create-subscriptions"></a>Vyhledání fakturačních profilů a oddílů faktur pro vytváření předplatných

V oddílu faktury pro fakturační profil se zobrazí poplatky za vaše předplatné. Pomocí následujícího rozhraní API získáte seznam fakturačních profilů a oddílů faktur, u kterých máte oprávnění k vytváření předplatných Azure.

Nejdřív získáte seznam fakturačních profilů v rámci fakturačního účtu, ke kterému máte přístup.

### <a name="rest"></a>[REST](#tab/rest-getBillingProfiles)
```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingprofiles/?api-version=2020-05-01
```
V odpovědi rozhraní API se zobrazí všechny fakturační profily, u kterých máte přístup k vytváření předplatných:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx",
      "name": "AW4F-xxxx-xxx-xxx",
      "properties": {
        "billingRelationshipType": "Direct",
        "billTo": {
          "addressLine1": "One Microsoft Way",
          "city": "Redmond",
          "companyName": "Contoso",
          "country": "US",
          "email": "kenny@contoso.com",
          "phoneNumber": "425xxxxxxx",
          "postalCode": "98052",
          "region": "WA"
        },
        "currency": "USD",
        "displayName": "Contoso Billing Profile",
        "enabledAzurePlans": [
          {
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
          },
          {
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
          }
        ],
        "hasReadAccess": true,
        "invoiceDay": 5,
        "invoiceEmailOptIn": false,
        "invoiceSections": {
          "hasMoreResults": false
        },
        "poNumber": "001",
        "spendingLimit": "Off",
        "status": "Active",
        "systemId": "AW4F-xxxx-xxx-xxx",
        "targetClouds": []
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles"
    }
  ]
}
```

 Zkopírujte `id`, abyste mohli v dalším kroku určit oddíly faktur v rámci daného fakturačního profilu. Například zkopírujte údaj `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx` a zavolejte následující rozhraní API.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoicesections?api-version=2020-05-01
```
### <a name="response"></a>Odpověď

```json
{
  "totalCount": 1,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
      "name": "SH3V-xxxx-xxx-xxx",
      "properties": {
        "displayName": "Development",
        "state": "Active",
        "systemId": "SH3V-xxxx-xxx-xxx"
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections"
    }
  ]
}
```

Pomocí vlastnosti `id` identifikujte oddíl faktury, pro který chcete vytvářet předplatná. Zkopírujte celý řetězec. Například, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

<!--
### [PowerShell](#tab/azure-powershell-getBillingProfiles)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingProfiles)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-a-subscription-for-an-invoice-section"></a>Vytvoření předplatného pro oddíl faktury

Následující příklad vytvoří předplatné s názvem *Dev Team Subscription* pro oddíl faktury *Development*. Toto předplatné se účtuje do fakturačního profilu *Contoso Billing Profile* a v příslušné faktuře bude uvedené v oddílu *Development*. Použijete rozsah fakturace zkopírovaný v předchozím kroku: `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

### <a name="rest"></a>[REST](#tab/rest-MCA)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>Text požadavku

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-MCA)

Chcete-li nainstalovat nejnovější verzi modulu, který obsahuje rutinu `New-AzSubscriptionAlias`, spusťte `Install-Module Az.Subscription`. Pokud chcete nainstalovat novější modulu PowerShellGet, projděte si téma [Získání modulu PowerShellGet](/powershell/scripting/gallery/installing-psget).

Spusťte následující příkaz [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) a použijte v něm rozsah fakturace `"/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" -Workload 'Production"
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

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-MCA)

Nejdřív spuštěním příkazů `az extension add --name account` a `az extension add --name alias` nainstalujte rozšíření.

Spusťte následující příkaz [az account alias create](/cli/azure/ext/account/account/alias#ext_account_az_account_alias_create).

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" --display-name "Dev Team Subscription" --workload "Production"
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

---

## <a name="next-steps"></a>Další kroky

* Teď když jste vytvořili předplatné, můžete tuto možnost poskytnout dalším uživatelům a instančním objektům. Další informace najdete v tématu [Udělení přístupu pro vytváření předplatných Azure Enterprise (Preview)](grant-access-to-create-subscription.md).
* Další informace o správě velkého počtu předplatných pomocí skupin pro správu najdete v tématu věnovaném [uspořádání prostředků pomocí skupin pro správu v Azure](../../governance/management-groups/overview.md).
