---
title: Programově vytváření předplatných Azure
description: Přečtěte si, jak programově vytvořit další předplatná Azure.
author: amberbhargava
ms.topic: conceptual
ms.date: 03/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.openlocfilehash: 33f1d154f438b917b79cd299e81c9078e2f2e81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460394"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Programově vytvářet předplatná Azure (preview)

Zákazníci Azure s [podnikovou smlouvou (EA),](https://azure.microsoft.com/pricing/enterprise-agreement/) [fakturačním účtem Microsoft Customer Agreement (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) nebo [Microsoft Partner Agreement (MPA)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) můžou vytvářet předplatná programově. V tomto článku se dozvíte, jak vytvořit předplatná programově pomocí Správce prostředků Azure.

Když vytvoříte předplatné Azure programově, toto předplatné se řídí smlouvou, na základě které jste získali služby Azure od Microsoftu nebo autorizovaného prodejce. Další informace najdete v [tématu Právní informace Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Vytvoření předplatných pro fakturační účet EA

Informace v následujících částech použijte k vytvoření předplatných EA.

### <a name="prerequisites"></a>Požadavky

Chcete-li vytvořit předplatné, musíte mít roli vlastníka v účtu registrace. Existují dva způsoby, jak získat roli:

* Správce podniku vaší registrace z vás může [udělat vlastníka účtu](https://ea.azure.com/helpdocs/addNewAccount) (vyžaduje přihlášení), což z vás dělá vlastníka registračního účtu.

* Stávající vlastník účtu registrace vám může [udělit přístup](grant-access-to-create-subscription.md). Podobně pokud chcete použít instanční objekt k vytvoření předplatného EA, musíte [udělit tento instanční objekt možnost vytvářet odběry](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Vyhledání účtů, ke které máte přístup

Po přidání do registračního účtu přidruženého k vlastníkovi účtu azure použije vztah účet k registraci k určení, kde se mají účtovat poplatky za předplatné. Všechna předplatná vytvořená pod účtem se účtují do registrace EA, ve které se účet nachází. Chcete-li vytvořit odběry, musíte předat hodnoty o účtu registrace a objekty uživatelů vlastnit předplatné.

Chcete-li spustit následující příkazy, musíte být přihlášeni k *domovskému adresáři*vlastníka účtu , což je adresář, ve kterém jsou ve výchozím nastavení vytvořena předplatná.

### <a name="rest"></a>[Odpočinku](#tab/rest)

Žádost o seznam všech účtů registrace, ke které máte přístup:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Odpověď rozhraní API obsahuje seznam všech účtů registrace, ke kterým máte přístup:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

Pomocí `principalName` této vlastnosti můžete identifikovat účet, kterému se mají účtovat předplatná. Zkopírujte `name` tento účet. Pokud byste například chtěli vytvořit předplatná v rámci SignUpEngineering@contoso.com účtu ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```registrace, zkopírovali byste . Tento identifikátor je ID objektu účtu registrace. Tuto hodnotu vložte někam, abyste ji `enrollmentAccountObjectId`mohli použít v dalším kroku jako .

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Otevřete [Azure Cloud Shell](https://shell.azure.com/) a vyberte PowerShell.

Pomocí rutiny [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) můžete vypsat všechny účty registrace, ke které máte přístup.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure reaguje se seznamem účtů registrace, ke kterým máte přístup:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Pomocí `principalName` této vlastnosti můžete identifikovat účet, kterému se mají účtovat předplatná. Zkopírujte `ObjectId` tento účet. Pokud byste například chtěli vytvořit předplatná v rámci SignUpEngineering@contoso.com účtu ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```registrace, zkopírovali byste . Toto ID objektu vložte někam, abyste ho `enrollmentAccountObjectId`mohli použít v dalším kroku jako .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí příkazu [az billing-enrollment-account](https://aka.ms/EASubCreationPublicPreviewCLI) příkaz upsat všechny účty registrace, ke které máte přístup.

```azurecli-interactive
az billing enrollment-account list
```

Azure reaguje se seznamem účtů registrace, ke kterým máte přístup:

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]
```

Pomocí `principalName` této vlastnosti můžete identifikovat účet, kterému se mají účtovat předplatná. Zkopírujte `name` tento účet. Pokud byste například chtěli vytvořit předplatná v rámci SignUpEngineering@contoso.com účtu ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```registrace, zkopírovali byste . Tento identifikátor je ID objektu účtu registrace. Tuto hodnotu vložte někam, abyste ji `enrollmentAccountObjectId`mohli použít v dalším kroku jako .

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Vytvoření předplatných v rámci konkrétního účtu registrace

Následující příklad vytvoří předplatné s názvem *Dev Team Subscription* v účtu registrace vybraném v předchozím kroku. Nabídka předplatného je *MS-AZR-0017P* (běžná smlouva Microsoft Enterprise Agreement). Volitelně také přidá dva uživatele jako vlastníky RBAC pro předplatné.

### <a name="rest"></a>[Odpočinku](#tab/rest)

Proveďte následující požadavek, ve kterém nahraďte `<enrollmentAccountObjectId>` hodnotou `name`, kterou jste zkopírovali v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Pokud chcete zadat [vlastníky,](grant-access-to-create-subscription.md#userObjectId)přečtěte si, jak získat ID objektů uživatelů .

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Název prvku  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Ne      | Řetězec | Zobrazovaný název předplatného. Pokud není zadán, je nastavena na název nabídky, například "Microsoft Azure Enterprise."                                 |
| `offerType`   | Ano      | Řetězec | Nabídka předplatného. Dvě možnosti pro EA jsou [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (použití výroby) a [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (vývoj/test, je třeba [zapnout pomocí portálu EA).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `owners`      | Ne       | Řetězec | ID objektu libovolného uživatele, který chcete přidat jako vlastník RBAC na předplatné, když je vytvořen.  |

V odpovědi získáte zpět `subscriptionOperation` objekt pro monitorování. Po dokončení vytvoření předplatného `subscriptionOperation` objekt vrátí `subscriptionLink` objekt, který má ID předplatného.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nejprve nainstalujte tento `Install-Module Az.Subscription -AllowPrerelease`modul náhledu spuštěním . Chcete-li `-AllowPrerelease` se ujistit, že funguje, nainstalujte nejnovější verzi PowerShellGet z [get PowerShellGet Module](/powershell/scripting/gallery/installing-psget).

Spusťte příkaz [New-AzSubscription](/powershell/module/az.subscription) `<enrollmentAccountObjectId>` níže `ObjectId` a nahrazte```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```je shromážděnými v prvním kroku ( ). Pokud chcete zadat [vlastníky,](grant-access-to-create-subscription.md#userObjectId)přečtěte si, jak získat ID objektů uživatelů .

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Název prvku  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Ne      | Řetězec | Zobrazovaný název předplatného. Pokud není zadán, je nastavena na název nabídky, například "Microsoft Azure Enterprise."                                 |
| `OfferType`   | Ano      | Řetězec | Nabídka předplatného. Dvě možnosti pro EA jsou [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (použití výroby) a [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (vývoj/test, je třeba [zapnout pomocí portálu EA).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `EnrollmentAccountObjectId`      | Ano       | Řetězec | ID objektu účtu registrace, pod kterým se předplatné vytvoří a účtuje se. Tato hodnota je identifikátor GUID, který získáte od aplikace `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Ne       | Řetězec | ID objektu libovolného uživatele, který chcete přidat jako vlastník RBAC na předplatné, když je vytvořen.  |
| `OwnerSignInName`    | Ne       | Řetězec | E-mailová adresa libovolného uživatele, který chcete přidat jako vlastník RBAC na předplatné, když je vytvořen. Tento parametr můžete použít `OwnerObjectId`místo .|
| `OwnerApplicationId` | Ne       | Řetězec | ID aplikace libovolného instančního objektu, který chcete přidat jako vlastník RBAC na předplatné, když je vytvořen. Tento parametr můžete použít `OwnerObjectId`místo . Při použití tohoto parametru musí mít instanční objekt [přístup pro čtení do adresáře](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Úplný seznam všech parametrů naleznete v tématu [New-AzSubscription](/powershell/module/az.subscription).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Nejprve nainstalujte toto `az extension add --name subscription`rozšíření náhledu spuštěním .

Spusťte [az účet vytvořit](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) `<enrollmentAccountObjectId>` příkaz `name` níže, nahradí jste zkopírované v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Pokud chcete zadat [vlastníky,](grant-access-to-create-subscription.md#userObjectId)přečtěte si, jak získat ID objektů uživatelů .

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Název prvku  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Ne      | Řetězec | Zobrazovaný název předplatného. Pokud není zadán, je nastavena na název nabídky, například "Microsoft Azure Enterprise."                                 |
| `offer-type`   | Ano      | Řetězec | Nabídka předplatného. Dvě možnosti pro EA jsou [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (použití výroby) a [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (vývoj/test, je třeba [zapnout pomocí portálu EA).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `enrollment-account-object-id`      | Ano       | Řetězec | ID objektu účtu registrace, pod kterým se předplatné vytvoří a účtuje se. Tato hodnota je identifikátor GUID, který získáte od aplikace `az billing enrollment-account list`. |
| `owner-object-id`      | Ne       | Řetězec | ID objektu libovolného uživatele, který chcete přidat jako vlastník RBAC na předplatné, když je vytvořen.  |
| `owner-upn`    | Ne       | Řetězec | E-mailová adresa libovolného uživatele, který chcete přidat jako vlastník RBAC na předplatné, když je vytvořen. Tento parametr můžete použít `owner-object-id`místo .|
| `owner-spn` | Ne       | Řetězec | ID aplikace libovolného instančního objektu, který chcete přidat jako vlastník RBAC na předplatné, když je vytvořen. Tento parametr můžete použít `owner-object-id`místo . Při použití tohoto parametru musí mít instanční objekt [přístup pro čtení do adresáře](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Úplný seznam všech parametrů naleznete v tématu [vytvoření účtu az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Omezení rozhraní API pro vytváření předplatného Azure Enterprise

- Pomocí tohoto rozhraní API lze vytvořit jenom předplatná Azure Enterprise.
- Na jeden účet registrace je limit 500 předplatných. Za to, že další předplatná pro účet lze vytvořit pouze na webu Azure Portal. Pokud chcete vytvořit další odběry prostřednictvím rozhraní API, vytvořte jiný účet registrace.
- Uživatelé, kteří nejsou vlastníky účtů, ale byli přidáni do účtu registrace prostřednictvím RBAC, nemohou na webu Azure Portal vytvářet předplatná.
- Nelze vybrat klienta pro předplatné, které má být vytvořeno v. Předplatné se vždy vytvoří v domovním tenantovi vlastníka účtu. Pokud chcete přesunout předplatné do jiného tenanta, [přečtěte si tématu změna klienta předplatného](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Vytvoření předplatných pro účet MCA

### <a name="prerequisites"></a>Požadavky

Chcete-li vytvořit předplatná, musíte mít roli vlastníka, přispěvatele nebo autora předplatného Azure v oddílu faktury nebo roli vlastníka nebo přispěvatele na fakturačním profilu nebo fakturačním účtu. Další informace najdete v tématu [Role a úlohy fakturace předplatného](../../cost-management-billing/manage/understand-mca-roles.md#subscription-billing-roles-and-tasks).

Příklad uvedený níže použít REST API. PowerShell ani Azure CLI se v současné době nepodporují.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Vyhledání fakturačních účtů, ke kterým máte přístup

Proveďte níže uvedenou žádost a uveďte všechny fakturační účty.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
Odpověď rozhraní API uvádí fakturační účty, ke kterým máte přístup.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Pomocí `displayName` služby můžete identifikovat fakturační účet, pro který chcete vytvořit předplatná. Ujistěte se, že agreeementType účtu je *MicrosoftCustomerAgreement*. Zkopírujte `name` účet.  Pokud například chcete vytvořit předplatné fakturačního `Contoso` účtu, zkopírujete `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Tuto hodnotu někam vložte, abyste ji mohli použít v dalším kroku.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Vyhledání částí faktury pro vytvoření předplatných

Poplatky za vaše předplatné se zobrazí na faktuře fakturačního profilu. Pomocí následujícího rozhraní API získáte seznam částí faktur a fakturačních profilů, na kterých máte oprávnění k vytváření předplatných Azure.

Proveďte následující požadavek, ve kterém nahraďte `<billingAccountName>` hodnotou `name`, kterou jste zkopírovali v prvním kroku (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
Odpověď rozhraní API obsahuje seznam všech částí faktury a jejich fakturačních profilů, ke kterým máte přístup k vytváření předplatných:

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}

```

Pomocí `invoiceSectionDisplayName` této vlastnosti můžete identifikovat část faktury, pro kterou chcete vytvořit odběry. Zkopírujte `invoiceSectionId` `billingProfileId` a jeden `skuId` z oddílu faktury. Chcete-li například vytvořit předplatné typu `Microsoft Azure plan` `Development` pro oddíl faktury, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX` `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` zkopírujte `0001`a . Tyto hodnoty vložte někam, abyste je mohli použít v dalším kroku.

### <a name="create-a-subscription-for-an-invoice-section"></a>Vytvoření předplatného pro oddíl faktury

Následující příklad vytvoří předplatné s názvem *Dev Team předplatné* typu *Microsoft Azure Plán* pro *vývoj* faktury části. Předplatné se bude účtovat do fakturačního profilu *contoso finance* a zobrazí se v části *Vývoj* na faktuře.

Proveďte následující požadavek `<invoiceSectionId>` a `invoiceSectionId` nahrazte jej```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```zkopírovaným z druhého kroku ( ). Budete muset předat `billingProfileId` a `skuId` zkopírovat z druhého kroku v parametrech požadavku rozhraní API. Pokud chcete zadat [vlastníky,](grant-access-to-create-subscription.md#userObjectId)přečtěte si, jak získat ID objektů uživatelů .

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| Název prvku  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Ano      | Řetězec | Zobrazovaný název předplatného.|
| `billingProfileId`   | Ano      | Řetězec | ID fakturačního profilu, který se bude účtovat poplatky za předplatné.  |
| `skuId` | Ano      | Řetězec | ID sku, které určuje typ plánu Azure. |
| `owners`      | Ne       | Řetězec | ID objektu libovolného uživatele nebo instančního objektu, který chcete přidat jako vlastník RBAC na předplatné, když je vytvořen.  |
| `costCenter` | Ne      | Řetězec | Nákladové středisko přidružené k předplatnému. To se objeví v použití csv souboru. |
| `managementGroupId` | Ne      | Řetězec | ID skupiny pro správu, do které bude předplatné přidáno. Seznam skupin pro správu najdete v [tématu Skupiny pro správu – rozhraní API seznamu](/rest/api/resources/managementgroups/list). Použijte ID skupiny pro správu z rozhraní API. |

V odpovědi získáte zpět `subscriptionCreationResult` objekt pro monitorování. Po dokončení vytvoření předplatného `subscriptionCreationResult` objekt vrátí `subscriptionLink` objekt, který má ID předplatného.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Vytvoření předplatných pro fakturační účet mpa

### <a name="prerequisites"></a>Požadavky

Chcete-li vytvořit předplatné pro váš fakturační účet, musíte mít roli globálního správce nebo agenta správce v účtu poskytovatele cloudových řešení vaší organizace. Další informace naleznete v [tématu Partnerské centrum – Přiřazení rolí a oprávnění uživatelům](https://docs.microsoft.com/partner-center/permissions-overview).

Příklad uvedený níže použít REST API. PowerShell ani Azure CLI se v současné době nepodporují.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Vyhledání fakturačních účtů, ke kterým máte přístup

Níže požitek vypsat všechny fakturační účty, ke kterým máte přístup.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
Odpověď rozhraní API uvádí fakturační účty.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Pomocí `displayName` služby můžete identifikovat fakturační účet, pro který chcete vytvořit předplatná. Ujistěte se, že agreeementType účtu je *MicrosoftPartnerAgreement*. Zkopírujte `name` pro účet. Pokud například chcete vytvořit předplatné fakturačního `Contoso` účtu, zkopírujete `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Tuto hodnotu někam vložte, abyste ji mohli použít v dalším kroku.

### <a name="find-customers-that-have-azure-plans"></a>Najděte zákazníky, kteří mají plány Azure

Proveďte následující požadavek `<billingAccountName>` a `name` nahrazte zkopírovaným z prvního kroku (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) a uveďte všechny zákazníky ve fakturačním účtu, pro které můžete vytvořit předplatná Azure.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
Odpověď rozhraní API uvádí zákazníky ve fakturačním účtu s plány Azure. Můžete vytvořit odběry pro tyto zákazníky.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}

```

Pomocí `displayName` vlastnosti můžete identifikovat zákazníka, pro kterého chcete vytvořit odběry. Zkopírujte `id` pro odběratele. Chcete-li například vytvořit předplatné `Fabrikam toys`, zkopírujte `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Vložte tuto hodnotu někam použít v následujících krocích.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Volitelné pro nepřímé poskytovatele: Získejte prodejce pro zákazníka

Pokud jste nepřímý zprostředkovatel v modelu CSP dvouvrstvé, můžete zadat prodejce při vytváření předplatných pro zákazníky.

Proveďte následující požadavek `<customerId>` a `id` nahrazte jej```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```zkopírovaným z druhého kroku ( ) a uveďte seznam všech prodejců, kteří jsou k dispozici pro zákazníka.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
Odpověď rozhraní API uvádí prodejce pro zákazníka:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```
Pomocí `description` služby můžete identifikovat prodejce, který bude přidružen k předplatnému. Zkopírujte `resellerId` pro prodejce. Chcete-li například přidružit `Wingtip`, `3xxxxx`zkopírujte . Tuto hodnotu někam vložte, abyste ji mohli použít v dalším kroku.

### <a name="create-a-subscription-for-a-customer"></a>Vytvoření předplatného pro odběratele

Následující příklad vytvoří předplatné s názvem *Dev Team předplatné* pro *hračky Fabrikam* a přidružit *prodejce Wingtip* k předplatnému. T

Proveďte následující požadavek `<customerId>` a `id` nahrazte jej```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```zkopírovaným z druhého kroku ( ). Předaj te volitelné *muzikované* prodejce z druhého kroku v parametrech požadavku rozhraní API.

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Název prvku  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Ano      | Řetězec | Zobrazovaný název předplatného.|
| `skuId` | Ano      | Řetězec | ID sku plánu Azure. Použití *0001* pro předplatná typu Microsoft Azure Plan |
| `resellerId`      | Ne       | Řetězec | ID mpn prodejce, který bude přidružen k předplatnému.  |

V odpovědi získáte zpět `subscriptionCreationResult` objekt pro monitorování. Po dokončení vytvoření předplatného `subscriptionCreationResult` objekt vrátí `subscriptionLink` objekt, který má ID předplatného.

## <a name="next-steps"></a>Další kroky

* Příklad při vytváření předplatného smlouvy Enterprise (EA) pomocí rozhraní .NET naleznete [v ukázkovém kódu na GitHubu](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Teď, když jste vytvořili předplatné, můžete tuto možnost udělit ostatním uživatelům a instančním objektům. Další informace najdete v tématu [Udělení přístupu k vytvoření předplatných Azure Enterprise (preview).](grant-access-to-create-subscription.md)
* Další informace o správě velkého počtu předplatných pomocí skupin pro správu najdete v [tématu Uspořádání prostředků pomocí skupin pro správu Azure.](../../governance/management-groups/overview.md)
