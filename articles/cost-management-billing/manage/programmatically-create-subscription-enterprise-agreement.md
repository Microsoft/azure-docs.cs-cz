---
title: Programové vytváření předplatných Azure se smlouvou Enterprise s využitím nejnovějších rozhraní API
description: Naučte se programově vytvářet předplatná Azure se smlouvou Enterprise s využitím nejnovějších verzí rozhraní REST API, Azure CLI a Azure PowerShellu.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: bd155ea3c98231cf20fa7c62325e3c2ecfb89920
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185916"
---
# <a name="programmatically-create-azure-enterprise-agreement-subscriptions-with-the-latest-apis"></a>Programové vytváření předplatných Azure se smlouvou Enterprise s využitím nejnovějších rozhraní API

Tento článek vám pomůže programově vytvářet předplatná Azure se smlouvou Enterprise (EA) pro fakturační účet EA s využitím nejnovějších verzí rozhraní API. Pokud ještě používáte starší verzi Preview, přejděte na téma [Programové vytváření předplatných Azure pomocí rozhraní API ve verzi Preview](programmatically-create-subscription-preview.md). 

V tomto článku se naučíte, jak programově vytvářet předplatná s využitím Azure Resource Manageru.

Když vytvoříte předplatné Azure programově, na toto předplatné se vztahuje smlouva, na základě které jste získali služby Azure od společnosti Microsoft nebo autorizovaného prodejce. Další informace najdete v článku [Právní informace o Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

K vytvoření předplatného je potřeba, abyste pro registrační účet měli roli vlastníka. Existují dva způsoby, jak tuto roli získat:

* Podnikový správce pro vaši registraci vás [může nastavit jako vlastníka účtu](https://ea.azure.com/helpdocs/addNewAccount) (vyžaduje se přihlášení) a to z vás udělá vlastníka tohoto registračního účtu.
* [Přístup vám může udělit](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) stávající vlastník registračního účtu. Podobně platí, že pokud chcete k vytvoření předplatného EA využít instanční objekt, musíte [tomuto instančnímu objektu udělit možnost vytvářet předplatná](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). 
  > [!NOTE]
  > Ujistěte se, že používáte správnou verzi rozhraní API pro udělení oprávnění vlastníka registračního účtu. Pro tento článek a rozhraní API, která jsou v něm dokumentovaná, použijte rozhraní API verze [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Pokud migrujete na novější rozhraní API, musíte znovu udělit oprávnění vlastníka s využitím verze [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Předchozí konfigurace vytvořená s verzí [2015-07-01](grant-access-to-create-subscription.md) se automaticky nepřevede na použití s novějšími rozhraními API.

## <a name="find-accounts-you-have-access-to"></a>Vyhledání účtů, ke kterým máte přístup

Jakmile jste přidáni k registračnímu účtu přidruženému k vlastníkovi účtu, Azure pomocí tohoto vztahu účet-registrace určí, kam se mají účtovat poplatky za předplatné. Všechna předplatná vytvořená v rámci účtu se fakturují v rámci registrace EA, do níž tento účet patří. Pokud chcete vytvářet předplatná, musíte předat hodnoty týkající se registračního účtu a instančních objektů uživatele, kteří mají být vlastníky těchto předplatných.

Abyste mohli spustit následující příkazy, musíte být přihlášeni k *domovskému adresáři* vlastníka účtu. V tomto adresáři se ve výchozím nastavení vytvářejí předplatná.

### <a name="rest"></a>[REST](#tab/rest-getEnrollments)

Vyžádejte si výpis všech registračních účtů, ke kterým máte přístup:

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

V odpovědi rozhraní API se zobrazí všechny registrační účty, ke kterým máte přístup.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
      "name": "1234567",
      "properties": {
        "accountStatus": "Unknown",
        "accountType": "Enterprise",
        "agreementType": "EnterpriseAgreement",
        "soldTo": {
          "companyName": "Contoso",
          "country": "US "
        },
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "enrollmentAccounts": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
            "name": "7654321",
            "type": "Microsoft.Billing/enrollmentAccounts",
            "properties": {
              "accountName": "Contoso",
              "accountOwnerEmail": "kenny@contoso.onmicrosoft.com",
              "costCenter": "Test",
              "isDevTest": false
            }
          }
        ],
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```

Hodnota pro rozsah fakturace a `id` je stejná. Parametrem `id` pro váš registrační účet je rozsah fakturace, v jehož rámci byla žádost pro předplatné iniciována. Je důležité toto ID znát, protože se jedná o povinný parametr, který použijete později v tomto článku k vytvoření předplatného.

<!-- 
### [PowerShell](#tab/azure-powershell-getEnrollments)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.

-->


<!--
### [Azure CLI](#tab/azure-cli-getEnrollments)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Vytváření předplatných v rámci konkrétního registračního účtu

Následující příklad vytvoří předplatné s názvem *Dev Team Subscription* v registračním účtu, který jste vybrali v předchozím kroku. 

### <a name="rest"></a>[REST](#tab/rest-EA)

Volejte rozhraní API PUT za účelem vytvoření požadavku/aliasu pro vytvoření předplatného.

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01 
```

V textu požadavku zadejte jako `billingScope` údaj `id` některého z vašich účtů `enrollmentAccounts`.

```json 
{
  "properties": {
        "billingScope": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321",
        "DisplayName": "Dev Team Subscription", //Subscription Display Name
        "Workload": "Production"
  }
}
```

Povolené hodnoty pro `Workload` jsou `Production` a `DevTest`.

#### <a name="response"></a>Odpověď

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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-EA)

Chcete-li nainstalovat nejnovější verzi modulu, který obsahuje rutinu `New-AzSubscriptionAlias`, spusťte `Install-Module Az.Subscription`. Pokud chcete nainstalovat novější modulu PowerShellGet, projděte si téma [Získání modulu PowerShellGet](/powershell/scripting/gallery/installing-psget).

Spusťte následující příkaz [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) a použijte v něm rozsah fakturace `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload 'Production"
```

V rámci odpovědi příkazu se vrátí údaj subscriptionId.

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  }
}
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-EA)

Nejdřív spuštěním příkazů `az extension add --name account` a `az extension add --name alias` nainstalujte rozšíření.

Spusťte následující příkaz [az account alias create](/cli/azure/ext/account/account/alias?view=azure-cli-latest#ext_account_az_account_alias_create&preserve-view=true) a zadejte v něm údaje `billing-scope` a `id` některého z vašich účtů `enrollmentAccounts`. 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/654321" --display-name "Dev Team Subscription" --workload "Production"
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

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Omezení rozhraní API pro vytváření předplatných Azure Enterprise

- Pomocí rozhraní API se dají vytvářet jenom předplatná Azure Enterprise.
- Platí limit 2 000 předplatných na jeden registrační účet. Další předplatná pro tento účet se potom dají vytvářet jenom na webu Azure Portal. Pokud chcete prostřednictvím rozhraní API vytvořit více předplatných, vytvořte si další registrační účet. Zrušená, odstraněná a přenesená předplatná se započítávají do limitu 2000.
- Uživatelé, kteří nejsou vlastníky účtu, ale byli do registračního účtu přidáni přes Azure RBAC, nemůžou vytvářet předplatná na webu Azure Portal.
- Tenanta, ve kterém se má předplatné vytvořit, nejde vybrat. Předplatné se vždycky vytvoří v domovském tenantovi vlastníka účtu. Pokud chcete předplatné přesunout do jiného tenanta, projděte si téma věnované [změně tenanta předplatného](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="next-steps"></a>Další kroky

* Teď když jste vytvořili předplatné, můžete tuto možnost poskytnout dalším uživatelům a instančním objektům. Další informace najdete v tématu [Udělení přístupu pro vytváření předplatných Azure Enterprise (Preview)](grant-access-to-create-subscription.md).
* Další informace o správě velkého počtu předplatných pomocí skupin pro správu najdete v tématu věnovaném [uspořádání prostředků pomocí skupin pro správu v Azure](../../governance/management-groups/overview.md).
