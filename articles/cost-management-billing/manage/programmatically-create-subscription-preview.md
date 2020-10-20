---
title: Programové vytváření předplatných Azure pomocí rozhraní API ve verzi Preview
description: Naučte se programově vytvářet další předplatná Azure pomocí verzí Preview rozhraní REST API, Azure CLI a Azure PowerShellu.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/12/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1ddd471746224c2084fa11a74d8fcee3b5bd3d5b
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026377"
---
# <a name="programmatically-create-azure-subscriptions-with-preview-apis"></a>Programové vytváření předplatných Azure pomocí rozhraní API ve verzi Preview

Tento článek se věnuje programovému vytváření předplatných Azure pomocí našeho staršího rozhraní API ve verzi Preview. Vydali jsme [novější verzi rozhraní API](programmatically-create-subscription.md). Pokud nechcete využít nejnovější verzi, přečtěte si informace v tomto článku. V tomto článku se naučíte, jak programově vytvářet předplatná s využitím Azure Resource Manageru.

Zákazníci Azure s fakturačním účtem pro následující typy smluv můžou vytvářet předplatná programově:

- [Smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Smlouva se zákazníkem Microsoftu (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)
- [Smlouva s partnerem Microsoftu](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement)

Když vytvoříte předplatné Azure programově, vztahuje se na toto předplatné smlouva, na základě které jste získali služby Azure od Microsoftu nebo autorizovaného prodejce. Další informace najdete v článku [Právní informace o Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-subscriptions-for-an-ea-billing-account"></a>Vytváření předplatných pro fakturační účet EA

K vytváření předplatných EA použijte informace v následujících sekcích.

### <a name="prerequisites"></a>Předpoklady

K vytvoření předplatného je potřeba, abyste pro registrační účet měli roli vlastníka. Existují dva způsoby, jak tuto roli získat:

* Podnikový správce vaší registrace vás [může nastavit jako vlastníka účtu](https://ea.azure.com/helpdocs/addNewAccount) (vyžaduje se přihlášení), takže pak budete vlastníkem tohoto registračního účtu.
* [Přístup vám může udělit](grant-access-to-create-subscription.md) stávající vlastník registračního účtu. Podobně platí, že pokud chcete k vytvoření předplatného EA využít instanční objekt, musíte [tomuto instančnímu objektu udělit možnost vytvářet předplatná](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Vyhledání účtů, ke kterým máte přístup

Jakmile jste přidáni k registračnímu účtu přidruženému k vlastníkovi účtu, Azure pomocí tohoto vztahu účet-registrace určí, kam se mají účtovat poplatky za předplatné. Všechna předplatná vytvořená v rámci účtu se fakturují v rámci registrace EA, do níž tento účet patří. Pokud chcete vytvářet předplatná, musíte předat hodnoty týkající se registračního účtu a instančních objektů uživatele, kteří mají být vlastníky těchto předplatných.

Ke spuštění následujících příkazů je potřeba, abyste byli přihlášení k *domovskému adresáři* vlastníka účtu. V tomto adresáři se ve výchozím nastavení vytvářejí předplatná.

### <a name="rest"></a>[REST](#tab/rest)

Vyžádejte si výpis všech registračních účtů, ke kterým máte přístup:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

V odpovědi rozhraní API se zobrazí všechny registrační účty, ke kterým máte přístup.

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

K určení účtu, pod kterým se mají předplatná fakturovat, použijte vlastnost `principalName`. Zkopírujte `name` tohoto účtu. Pokud například chcete vytvářet předplatná v rámci registračního účtu SignUpEngineering@contoso.com, zkopírujte ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Tento identifikátor představuje ID objektu registračního účtu. Tuto hodnotu někam vložte, abyste ji mohli použít v dalším kroku jako `enrollmentAccountObjectId`.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Otevřete [Azure Cloud Shell](https://shell.azure.com/) a vyberte PowerShell.

K zobrazení seznamu všech registračních účtů, ke kterým máte přístup, použijte rutinu [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount).

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure odpoví výpisem registračních účtů, ke kterým máte přístup:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
K určení účtu, pod kterým se mají předplatná fakturovat, použijte vlastnost `principalName`. Zkopírujte `ObjectId` tohoto účtu. Pokud například chcete vytvářet předplatná v rámci registračního účtu SignUpEngineering@contoso.com, zkopírujte ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Toto ID objektu někam vložte, abyste ho mohli použít v dalším kroku jako `enrollmentAccountObjectId`.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí příkazu [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) zobrazte výpis všech registračních účtů, ke kterým máte přístup.

```azurecli-interactive
az billing enrollment-account list
```

Azure odpoví výpisem registračních účtů, ke kterým máte přístup:

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

K určení účtu, pod kterým se mají předplatná fakturovat, použijte vlastnost `principalName`. Zkopírujte `name` tohoto účtu. Pokud například chcete vytvářet předplatná v rámci registračního účtu SignUpEngineering@contoso.com, zkopírujte ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Tento identifikátor představuje ID objektu registračního účtu. Tuto hodnotu někam vložte, abyste ji mohli použít v dalším kroku jako `enrollmentAccountObjectId`.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Vytváření předplatných v rámci konkrétního registračního účtu

Následující příklad vytvoří předplatné s názvem *Dev Team Subscription* v registračním účtu, který jste vybrali v předchozím kroku. Nabídka předplatného je *MS-AZR-0017P* (běžná smlouva Microsoft Enterprise). Volitelně také pro toto předplatné přidá dva uživatele jako vlastníky Azure RBAC.

### <a name="rest"></a>[REST](#tab/rest)

Proveďte následující požadavek, ve kterém nahraďte `<enrollmentAccountObjectId>` hodnotou `name`, kterou jste zkopírovali v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Pokud chcete zadat vlastníky, podívejte se, jak [získat ID objektů uživatele](grant-access-to-create-subscription.md#userObjectId).

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
| `displayName` | No      | Řetězec | Zobrazovaný název předplatného. Pokud není zadaný, nastaví se na název nabídky, například Microsoft Azure Enterprise.                                 |
| `offerType`   | Ano      | Řetězec | Nabídka předplatného. Pro EA jsou k dispozici dvě možnosti: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produkční využití) a [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (vývoj/testování, musí se [aktivovat prostřednictvím portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | No       | Řetězec | ID objektu libovolného uživatele, kterého chcete přidat jako vlastníka Azure RBAC při vytváření předplatného.  |

V odpovědi se jako součást hlavičky `Location`vrátí adresa URL, na které se můžete dotazovat na stav operace vytváření předplatného. Jakmile se vytvoření předplatného dokončí, příkaz GET pro adresu URL `Location` vrátí objekt `subscriptionLink`, který obsahuje ID předplatného. Další podrobnosti najdete v [dokumentaci k rozhraní API pro předplatné](/rest/api/subscription/).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li nainstalovat nejnovější verzi modulu, který obsahuje rutinu `New-AzSubscription`, spusťte `Install-Module Az.Subscription`. Pokud chcete nainstalovat novější modulu PowerShellGet, projděte si téma [Získání modulu PowerShellGet](/powershell/scripting/gallery/installing-psget).

Spusťte následující příkaz [New-AzSubscription](/powershell/module/az.subscription) a `<enrollmentAccountObjectId>` nahraďte hodnotou `ObjectId` získanou v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Pokud chcete zadat vlastníky, podívejte se, jak [získat ID objektů uživatele](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Název prvku  | Požaduje se | Typ   | Popis |
|---------------|----------|--------|----|
| `Name` | No      | Řetězec | Zobrazovaný název předplatného. Pokud není zadaný, nastaví se na název nabídky, například *Microsoft Azure Enterprise*. |
| `OfferType`   | Ano      | Řetězec | Nabídka předplatného. Pro EA jsou k dispozici dvě možnosti: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produkční využití) a [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (vývoj/testování, musí se [aktivovat prostřednictvím portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Ano       | Řetězec | ID objektu registračního účtu, pod kterým je předplatné vytvořené a pod kterým se fakturuje. Tato hodnota je identifikátor GUID získaný z rutiny `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | No       | Řetězec | ID objektu libovolného uživatele, kterého chcete přidat jako vlastníka Azure RBAC při vytváření předplatného.  |
| `OwnerSignInName`    | No       | Řetězec | E-mailová adresa libovolného uživatele, kterého chcete přidat jako vlastníka Azure RBAC při vytváření předplatného. Tento parametr můžete použít místo parametru `OwnerObjectId`.|
| `OwnerApplicationId` | No       | Řetězec | ID aplikace libovolného instančního objektu, který chcete přidat jako vlastníka Azure RBAC při vytváření předplatného. Tento parametr můžete použít místo parametru `OwnerObjectId`. Při použití tohoto parametru musí mít instanční objekt [oprávnění ke čtení pro příslušný adresář](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true).|

Úplný seznam všech parametrů najdete v tématu [New-AzSubscription](/powershell/module/az.subscription/New-AzSubscription).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Nejdřív spuštěním příkazu `az extension add --name subscription` nainstalujte rozšíření ve verzi Preview.

Spusťte následující příkaz [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create&preserve-view=true) a nahraďte přitom `<enrollmentAccountObjectId>` hodnotou `name`, kterou jste zkopírovali v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Pokud chcete zadat vlastníky, podívejte se, jak [získat ID objektů uživatele](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Název prvku  | Požaduje se | Typ   | Popis |
|---------------|----------|--------|------------|
| `display-name` | No      | Řetězec | Zobrazovaný název předplatného. Pokud není zadaný, nastaví se na název nabídky, například *Microsoft Azure Enterprise*.|
| `offer-type`   | Ano      | Řetězec | Nabídka předplatného. Pro EA jsou k dispozici dvě možnosti: [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produkční využití) a [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (vývoj/testování, musí se [aktivovat prostřednictvím portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Ano       | Řetězec | ID objektu registračního účtu, pod kterým je předplatné vytvořené a pod kterým se fakturuje. Tato hodnota je identifikátor GUID získaný z rutiny `az billing enrollment-account list`. |
| `owner-object-id`      | No       | Řetězec | ID objektu libovolného uživatele, kterého chcete přidat jako vlastníka Azure RBAC při vytváření předplatného.  |
| `owner-upn`    | No       | Řetězec | E-mailová adresa libovolného uživatele, kterého chcete přidat jako vlastníka Azure RBAC při vytváření předplatného. Tento parametr můžete použít místo parametru `owner-object-id`.|
| `owner-spn` | No       | Řetězec | ID aplikace libovolného instančního objektu, který chcete přidat jako vlastníka Azure RBAC při vytváření předplatného. Tento parametr můžete použít místo parametru `owner-object-id`. Při použití tohoto parametru musí mít instanční objekt [oprávnění ke čtení pro příslušný adresář](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true).|

Úplný seznam všech parametrů najdete u příkazu [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create&preserve-view=true).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Omezení rozhraní API pro vytváření předplatných Azure Enterprise

- Pomocí tohoto rozhraní API se dají vytvářet jenom předplatná Azure Enterprise.
- Platí limit 2 000 předplatných na jeden registrační účet. Další předplatná pro tento účet se potom dají vytvářet jenom na webu Azure Portal. Pokud chcete prostřednictvím tohoto rozhraní API vytvořit více předplatných, vytvořte si další registrační účet.
- Uživatelé, kteří nejsou vlastníky účtu, ale byli do registračního účtu přidáni přes Azure RBAC, nemůžou vytvářet předplatná na webu Azure Portal.
- Tenanta, ve kterém se má předplatné vytvořit, nejde vybrat. Předplatné se vždycky vytvoří v domovském tenantovi vlastníka účtu. Pokud chcete předplatné přesunout do jiného tenanta, projděte si téma věnované [změně tenanta předplatného](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Vytváření předplatných pro fakturační účet MCA

V následujících částech najdete informace o vytváření předplatných pro účet MCA.

### <a name="prerequisites"></a>Předpoklady

Abyste mohli vytvářet předplatná, musíte mít roli vlastníka, přispěvatele nebo tvůrce předplatného Azure pro oddíl faktury nebo roli vlastníka nebo přispěvatele pro fakturační profil nebo fakturační účet. Další informace najdete v tématu [Role a úlohy fakturace předplatného](understand-mca-roles.md#subscription-billing-roles-and-tasks).

V následujících příkladech se používají rozhraní REST API. PowerShell ani Azure CLI se v současné době nepodporují.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Vyhledání fakturačních účtů, ke kterým máte přístup

Pomocí následujícího požadavku zobrazte seznam všech fakturačních účtů.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
V odpovědi rozhraní API se zobrazí všechny fakturační účty, ke kterým máte přístup.

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
Pomocí vlastnosti `displayName` identifikujte fakturační účet, pro který chcete vytvářet předplatná. Zkontrolujte, jestli má agreementType tohoto účtu hodnotu *MicrosoftCustomerAgreement*. Zkopírujte `name` tohoto účtu.  Pokud například chcete vytvořit předplatné pro fakturační účet `Contoso`, zkopírujte `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Tuto hodnotu někam vložte, abyste ji mohli použít v dalším kroku.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Vyhledání oddílů faktur pro vytváření předplatných

V oddílu faktury pro fakturační profil se zobrazí poplatky za vaše předplatné. Pomocí následujícího rozhraní API získáte seznam oddílů faktur a fakturačních profilů, u kterých máte oprávnění k vytváření předplatných Azure.

Proveďte následující požadavek, ve kterém nahraďte `<billingAccountName>` hodnotou `name`, kterou jste zkopírovali v prvním kroku (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```

V odpovědi rozhraní API se zobrazí všechny oddíly faktur a jejich fakturační profily, u kterých máte přístup pro vytváření předplatných:

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

Pomocí vlastnosti `invoiceSectionDisplayName` identifikujte oddíl faktury, pro který chcete vytvářet předplatná. Pro tento oddíl faktury zkopírujte `invoiceSectionId`, `billingProfileId` a jednu z hodnot `skuId`. Pokud například chcete vytvořit předplatné typu `Microsoft Azure plan` pro oddíl faktury `Development`, zkopírujte `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` a `0001`. Tyto hodnoty někam vložte, abyste je mohli použít v dalším kroku.

### <a name="create-a-subscription-for-an-invoice-section"></a>Vytvoření předplatného pro oddíl faktury

Následující příklad vytvoří předplatné s názvem *Dev Team Subscription* typu *plán Microsoft Azure* pro oddíl faktury *Development*. Toto předplatné se účtuje do fakturačního profilu *Contoso finance* a v příslušné faktuře bude uvedené v oddílu *Development*.

Proveďte následující požadavek, ve kterém nahradíte `<invoiceSectionId>` hodnotou `invoiceSectionId`, kterou jste zkopírovali v druhém kroku (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). Jako parametry požadavku pro rozhraní API zadejte hodnoty `billingProfileId` a `skuId` zkopírované v druhém kroku. Pokud chcete zadat vlastníky, podívejte se, jak [získat ID objektů uživatele](grant-access-to-create-subscription.md#userObjectId).

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
| `billingProfileId`   | Ano      | Řetězec | ID fakturačního profilu, na který se účtují poplatky za využití předplatného.  |
| `skuId` | Ano      | Řetězec | Identifikátor SKU, který určuje typ plánu Azure. |
| `owners`      | No       | Řetězec | ID objektu libovolného instančního objektu služby nebo uživatele, který chcete přidat jako vlastníka Azure RBAC při vytváření předplatného.  |
| `costCenter` | No      | Řetězec | Nákladové centrum přidružené k předplatnému. Zobrazuje se v souboru CSV s daty využití. |
| `managementGroupId` | No      | Řetězec | ID skupiny pro správu, do které bude toto předplatné přidáno. Pokud chcete získat seznam skupin pro správu, projděte si téma zaměřené na [skupiny pro správu – rozhraní API pro zobrazení seznamu](/rest/api/resources/managementgroups/list). Použijte ID skupiny pro správu z tohoto rozhraní API. |

V odpovědi se vrátí objekt `subscriptionCreationResult` pro monitorování. Jakmile se vytváření předplatného dokončí, objekt `subscriptionCreationResult` vrátí objekt `subscriptionLink`, který obsahuje ID předplatného.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Vytváření předplatných pro fakturační účet MPA

V následujících částech najdete informace o vytváření předplatných pro fakturační účet MPA.

### <a name="prerequisites"></a>Požadavky

K vytvoření předplatného pro váš fakturační účet musíte mít v účtu poskytovatele cloudového řešení vaší organizace roli globálního správce nebo agenta pro správu. Další informace najdete v tématu [Partnerské centrum – přiřazování uživatelských rolí a oprávnění](/partner-center/permissions-overview).

V následujících příkladech se používají rozhraní REST API. PowerShell ani Azure CLI se v současné době nepodporují.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Vyhledání fakturačních účtů, ke kterým máte přístup

Pomocí následujícího požadavku zobrazte seznam všech fakturačních účtů, ke kterým máte přístup.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

V odpovědi rozhraní API se zobrazí seznam fakturačních účtů.

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
Pomocí vlastnosti `displayName` identifikujte fakturační účet, pro který chcete vytvářet předplatná. Zkontrolujte, jestli má agreementType tohoto účtu hodnotu *MicrosoftPartnerAgreement*. Zkopírujte `name` tohoto účtu. Pokud například chcete vytvořit předplatné pro fakturační účet `Contoso`, zkopírujte `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Tuto hodnotu někam vložte, abyste ji mohli použít v dalším kroku.

### <a name="find-customers-that-have-azure-plans"></a>Vyhledání zákazníků s plány Azure

Proveďte následující požadavek, ve kterém nahradíte `<billingAccountName>` hodnotou `name`, kterou jste zkopírovali v prvním kroku (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```). Zobrazí se seznam všech zákazníků ve fakturačním účtu, pro které můžete vytvářet předplatná Azure.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
V odpovědi rozhraní API se zobrazí zákazníci ve fakturačním účtu s plány Azure. Pro tyto zákazníky můžete vytvářet předplatná.

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

Pomocí vlastnosti `displayName` identifikujte zákazníka, pro kterého chcete vytvářet předplatná. Zkopírujte `id` tohoto zákazníka. Pokud například chcete vytvořit předplatné pro zákazníka `Fabrikam toys`, zkopírujte `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Tuto hodnotu někam vložte, abyste ji mohli použít v dalších krocích.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Volitelné pro nepřímé poskytovatele: Zjištění prodejců pro zákazníka

Pokud jste nepřímý poskytovatel v dvouúrovňovém modelu CSP, můžete při vytváření předplatných pro zákazníky určit prodejce.

Proveďte následující požadavek, ve kterém nahradíte `<customerId>` hodnotou `id`, kterou jste zkopírovali v druhém kroku (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Zobrazí se seznam všech prodejců, kteří jsou pro příslušného zákazníka k dispozici.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
V odpovědi rozhraní API se zobrazí prodejci pro tohoto zákazníka:

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

Pomocí vlastnosti `description` identifikujte prodejce, kterého chcete přidružit k předplatnému. Zkopírujte `resellerId` tohoto prodejce. Pokud například chcete přidružit prodejce `Wingtip`, zkopírujte `3xxxxx`. Tuto hodnotu někam vložte, abyste ji mohli použít v dalším kroku.

### <a name="create-a-subscription-for-a-customer"></a>Vytvoření předplatného pro zákazníka

Následující příklad vytvoří předplatné s názvem *Dev Team Subscription* pro zákazníka *Fabrikam toys* a přidruží k tomuto předplatnému prodejce *Wingtip*. 

Proveďte následující požadavek, ve kterém nahradíte `<customerId>` hodnotou `id`, kterou jste zkopírovali v druhém kroku (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Jako parametry požadavku pro rozhraní API předejte volitelnou hodnotu *resellerId* zkopírovanou v druhém kroku.

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
| `skuId` | Ano      | Řetězec | Identifikátor SKU plánu Azure. Pro předplatná typu plán Microsoft Azure použijte *0001*. |
| `resellerId`      | No       | Řetězec | Identifikátor MPN prodejce, který bude přidružený k předplatnému.  |

V odpovědi se vrátí objekt `subscriptionCreationResult` pro monitorování. Jakmile se vytváření předplatného dokončí, objekt `subscriptionCreationResult` vrátí objekt `subscriptionLink`. Ten obsahuje ID předplatného.

## <a name="next-steps"></a>Další kroky

- Příklad vytvoření předplatného pro smlouvu Enterprise (EA) pomocí rozhraní .NET najdete v [ukázkovém kódu na GitHubu](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Teď když jste vytvořili předplatné, můžete tuto možnost poskytnout dalším uživatelům a instančním objektům. Další informace najdete v tématu [Udělení přístupu pro vytváření předplatných Azure Enterprise (Preview)](grant-access-to-create-subscription.md).
* Další informace o správě velkého počtu předplatných pomocí skupin pro správu najdete v tématu věnovaném [uspořádání prostředků pomocí skupin pro správu v Azure](../../governance/management-groups/overview.md).