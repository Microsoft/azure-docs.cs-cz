---
title: Vytváření předplatných Azure prostřednictvím kódu programu | Microsoft Docs
description: Naučte se vytvářet další předplatná Azure prostřednictvím kódu programu.
services: azure-resource-manager
author: amberb
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: banders
ms.openlocfilehash: 5d977fd6ce74f9cabedd0553c5815fd64d4d09a7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376010"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Programové vytváření předplatných Azure (Preview)

Zákazníci Azure s fakturačním účtem služby [smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Microsoft Customer Agreement (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) nebo [Microsoft Partner Agreement (MPa)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) můžou vytvářet předplatná programově. V tomto článku se naučíte, jak vytvářet odběry prostřednictvím Azure Resource Manager.

Když vytvoříte předplatné Azure programově, na toto předplatné se vztahuje smlouva, na základě které jste získali služby Azure od společnosti Microsoft nebo autorizovaného prodejce. Další informace najdete v tématu [Microsoft Azure právní informace](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Vytvoření předplatných pro fakturační účet EA

### <a name="prerequisites"></a>Předpoklady

Abyste mohli vytvořit předplatné, musíte mít v účtu pro zápis roli vlastníka. Existují dva způsoby, jak získat roli:

* Podnikový správce vaší registrace vám může [vytvořit vlastníka účtu](https://ea.azure.com/helpdocs/addNewAccount) (vyžaduje se přihlášení), který vás provede vlastníkem registračního účtu.

* [Přístup může udělit](grant-access-to-create-subscription.md)stávající vlastník účtu pro zápis. Podobně pokud chcete k vytvoření předplatného EA použít instanční objekt, musíte [tomuto instančnímu objektu udělit možnost vytvářet odběry](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Vyhledání účtů, ke kterým máte přístup

Po přidání k účtu pro zápis, který je přidružený k vlastníkovi účtu, Azure pomocí vztahu account-to-Enrollment určí, kde se mají účtovat poplatky za předplatné. Všechna předplatná vytvořená v rámci účtu se účtují na základě registrace EA, na které je účet. Chcete-li vytvořit odběry, musíte předat hodnoty týkající se účtu registrace a objektů zabezpečení uživatele k vlastnímu předplatnému. 

Chcete-li spustit následující příkazy, musíte být přihlášeni k *domovskému adresáři*vlastníka účtu, což je adresář, ve kterém jsou předplatná vytvořena ve výchozím nastavení.

### <a name="resttabrest"></a>[REST](#tab/rest)

Požadavek na výpis všech účtů pro zápis, ke kterým máte přístup:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

V odpovědi rozhraní API najdete seznam všech účtů zápisu, ke kterým máte přístup:

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

Pomocí vlastnosti `principalName` Identifikujte účet, na který chcete vyplatit odběry. Zkopírujte `name` tohoto účtu. Pokud byste například chtěli vytvořit předplatná pod účtem pro zápis SignUpEngineering@contoso.com, nakopírujete ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Tento identifikátor je ID objektu registračního účtu. Vložte tuto hodnotu někam, abyste ji mohli použít v dalším kroku jako `enrollmentAccountObjectId`.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Otevřete [Azure Cloud Shell](https://shell.azure.com/) a vyberte PowerShell.

Pomocí rutiny [Get-AzEnrollmentAccount Zobrazte](/powershell/module/az.billing/get-azenrollmentaccount) seznam všech registračních účtů, ke kterým máte přístup.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure odpoví seznamem účtů pro zápis, ke kterým máte přístup:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Pomocí vlastnosti `principalName` Identifikujte účet, na který chcete vyplatit odběry. Zkopírujte `ObjectId` tohoto účtu. Pokud byste například chtěli vytvořit předplatná pod účtem pro zápis SignUpEngineering@contoso.com, nakopírujete ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Vložte toto ID objektu někam, abyste ho mohli použít v dalším kroku jako `enrollmentAccountObjectId`.

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

K vypsání všech registračních účtů, ke kterým máte přístup, použijte příkaz [AZ disenrollment-Account list](https://aka.ms/EASubCreationPublicPreviewCLI) .

```azurecli-interactive 
az billing enrollment-account list
```

Azure odpoví seznamem účtů pro zápis, ke kterým máte přístup:

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

Pomocí vlastnosti `principalName` Identifikujte účet, na který chcete vyplatit odběry. Zkopírujte `name` tohoto účtu. Pokud byste například chtěli vytvořit předplatná pod účtem pro zápis SignUpEngineering@contoso.com, nakopírujete ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Tento identifikátor je ID objektu registračního účtu. Vložte tuto hodnotu někam, abyste ji mohli použít v dalším kroku jako `enrollmentAccountObjectId`.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Vytváření předplatných v rámci konkrétního účtu registrace

Následující příklad vytvoří předplatné s názvem *vývojové týmu Subscription* v účtu pro zápis, který jste vybrali v předchozím kroku. Nabídka předplatného je *MS-AZR-0017P* (regular Microsoft smlouva Enterprise). Volitelně taky přidá dva uživatele jako vlastníky RBAC pro předplatné.

### <a name="resttabrest"></a>[REST](#tab/rest)

Proveďte následující požadavek a nahraďte `<enrollmentAccountObjectId>` pomocí `name` zkopírovaného z prvního kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Pokud chcete zadat vlastníky, přečtěte si, [Jak získat ID objektů uživatele](grant-access-to-create-subscription.md#userObjectId).

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

| Název elementu  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Ne      | Řetězec | Zobrazovaný název předplatného. Pokud není zadaný, nastaví se na název nabídky, například Microsoft Azure Enterprise.                                 |
| `offerType`   | Ano      | Řetězec | Nabídka předplatného. Dvě možnosti pro EA jsou [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produkční použití) a [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (vývoj/testování), které je potřeba [zapnout pomocí portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer).                |
| `owners`      | Ne       | Řetězec | ID objektu libovolného uživatele, který chcete přidat jako vlastníka RBAC v předplatném, když je vytvořen.  |

V odpovědi se vrátí objekt `subscriptionOperation` pro monitorování. Po dokončení vytvoření odběru vrátí objekt `subscriptionOperation` objekt `subscriptionLink`, který má ID předplatného.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Nejdřív nainstalujte tento modul verze Preview spuštěním `Install-Module Az.Subscription -AllowPrerelease`. Abyste se ujistili, že `-AllowPrerelease` funguje, nainstalujte nejnovější verzi PowerShellGet z [modulu získat PowerShellGet](/powershell/gallery/installing-psget).

Spusťte následující příkaz [New-AzSubscription](/powershell/module/az.subscription) , který nahradí `<enrollmentAccountObjectId>` s `ObjectId` shromážděnými v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Pokud chcete zadat vlastníky, přečtěte si, [Jak získat ID objektů uživatele](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Název elementu  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Ne      | Řetězec | Zobrazovaný název předplatného. Pokud není zadaný, nastaví se na název nabídky, například Microsoft Azure Enterprise.                                 |
| `OfferType`   | Ano      | Řetězec | Nabídka předplatného. Dvě možnosti pro EA jsou [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produkční použití) a [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (vývoj/testování), které je potřeba [zapnout pomocí portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer).                |
| `EnrollmentAccountObjectId`      | Ano       | Řetězec | ID objektu účtu pro zápis, pod kterým se předplatné vytvoří a účtuje se. Tato hodnota je identifikátor GUID, který získáte z `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Ne       | Řetězec | ID objektu libovolného uživatele, který chcete přidat jako vlastníka RBAC v předplatném, když je vytvořen.  |
| `OwnerSignInName`    | Ne       | Řetězec | E-mailová adresa libovolného uživatele, který chcete přidat jako vlastníka RBAC v předplatném, když se vytvoří. Tento parametr můžete použít místo `OwnerObjectId`.|
| `OwnerApplicationId` | Ne       | Řetězec | ID aplikace libovolného instančního objektu, který chcete přidat jako vlastníka RBAC v předplatném, když se vytvoří. Tento parametr můžete použít místo `OwnerObjectId`. Při použití tohoto parametru musí instanční objekt mít k [adresáři přístup pro čtení](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Úplný seznam všech parametrů naleznete v části [New-AzSubscription](/powershell/module/az.subscription).

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Nejdřív nainstalujte toto rozšíření Preview spuštěním `az extension add --name subscription`.

Spusťte příkaz [AZ Account Create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) níže a nahraďte `<enrollmentAccountObjectId>` s `name`, který jste zkopírovali v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Pokud chcete zadat vlastníky, přečtěte si, [Jak získat ID objektů uživatele](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Název elementu  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Ne      | Řetězec | Zobrazovaný název předplatného. Pokud není zadaný, nastaví se na název nabídky, například Microsoft Azure Enterprise.                                 |
| `offer-type`   | Ano      | Řetězec | Nabídka předplatného. Dvě možnosti pro EA jsou [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produkční použití) a [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (vývoj/testování), které je potřeba [zapnout pomocí portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer).                |
| `enrollment-account-object-id`      | Ano       | Řetězec | ID objektu účtu pro zápis, pod kterým se předplatné vytvoří a účtuje se. Tato hodnota je identifikátor GUID, který získáte z `az billing enrollment-account list`. |
| `owner-object-id`      | Ne       | Řetězec | ID objektu libovolného uživatele, který chcete přidat jako vlastníka RBAC v předplatném, když je vytvořen.  |
| `owner-upn`    | Ne       | Řetězec | E-mailová adresa libovolného uživatele, který chcete přidat jako vlastníka RBAC v předplatném, když se vytvoří. Tento parametr můžete použít místo `owner-object-id`.|
| `owner-spn` | Ne       | Řetězec | ID aplikace libovolného instančního objektu, který chcete přidat jako vlastníka RBAC v předplatném, když se vytvoří. Tento parametr můžete použít místo `owner-object-id`. Při použití tohoto parametru musí instanční objekt mít k [adresáři přístup pro čtení](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Úplný seznam všech parametrů najdete v tématu [AZ Account Create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Omezení rozhraní API pro vytváření předplatných Azure Enterprise

- Pomocí tohoto rozhraní API se dají vytvořit jenom předplatná Azure Enterprise.
- Pro každý registrační účet je povolený limit 200 předplatných. Další předplatná pro tento účet se pak dají vytvořit jenom v Azure Portal. Pokud chcete vytvořit další odběry prostřednictvím rozhraní API, vytvořte další účet pro zápis.
- Uživatelé, kteří nejsou vlastníkem účtu, ale byli přidáni do účtu pro zápis pomocí RBAC, nemůžou v Azure Portal vytvářet odběry.
- Nemůžete vybrat tenanta, ve kterém se má předplatné vytvořit. Předplatné se vždy vytvoří v domovském tenantovi vlastníka účtu. Pokud chcete přesunout předplatné do jiného tenanta, přečtěte si téma [Změna tenanta předplatného](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Vytvoření předplatných pro účet MCA

### <a name="prerequisites"></a>Předpoklady

Abyste mohli vytvářet předplatná, musíte mít roli vlastníka, přispěvatele nebo tvůrce předplatného Azure pro oddíl faktury nebo roli vlastníka nebo přispěvatele na fakturačním profilu nebo fakturačním účtu. Další informace najdete v tématu [Role a úlohy fakturace předplatného](../billing/billing-understand-mca-roles.md#subscription-billing-roles-and-tasks).

Níže uvedený příklad používá rozhraní REST API. V současné době není prostředí PowerShell a rozhraní příkazového řádku Azure podporováno.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Hledání účtů fakturace, ke kterým máte přístup 

Pokud chcete zobrazit seznam všech fakturačních účtů, proveďte níže uvedený požadavek.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
Odpověď rozhraní API obsahuje seznam fakturačních účtů, ke kterým máte přístup.

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
Pomocí vlastnosti `displayName` Identifikujte fakturační účet, pro který chcete vytvořit odběry. Ujistěte se, že agreeementType účtu je *MicrosoftCustomerAgreement*. Zkopírujte `name` účtu.  Pokud třeba chcete vytvořit předplatné pro fakturační účet `Contoso`, nakopírujete `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Vložte tuto hodnotu někam, abyste ji mohli použít v dalším kroku.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Najít oddíly faktury pro vytvoření předplatných

Poplatky za vaše předplatné se zobrazí v části faktury fakturačního profilu. Pomocí následujícího rozhraní API získáte seznam sekcí faktury a fakturačních profilů, na kterých máte oprávnění k vytváření předplatných Azure.

Proveďte následující požadavek a nahraďte `<billingAccountName>` pomocí `name` zkopírovaného z prvního kroku (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
V odpovědi rozhraní API najdete seznam všech oddílů faktury a jejich fakturačních profilů, na kterých máte přístup k vytváření předplatných:

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

Pomocí vlastnosti `invoiceSectionDisplayName` Identifikujte oddíl faktury, pro který chcete vytvořit odběry. Zkopírujte `invoiceSectionId` `billingProfileId` a jednu z `skuId` pro část faktura. Například pokud chcete vytvořit odběr typu `Microsoft Azure plan` pro `Development` fakturaci, měli byste zkopírovat `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` a `0001`. Vložte tyto hodnoty někam, abyste je mohli použít v dalším kroku.

### <a name="create-a-subscription-for-an-invoice-section"></a>Vytvoření odběru pro oddíl faktury

Následující příklad vytvoří předplatné s názvem vývojářský *tým předplatné* typu *Microsoft Azure plán* pro část *vývojová* faktura. Předplatné se bude fakturovat do fakturačního profilu *finance společnosti Contoso* a zobrazí se v části pro *vývoj* na jeho faktuře. 

Proveďte následující požadavek a nahraďte `<invoiceSectionId>` pomocí `invoiceSectionId` zkopírovaného z druhého kroku (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). Musíte předat `billingProfileId` a `skuId` zkopírované z druhého kroku v parametrech požadavku rozhraní API. Pokud chcete zadat vlastníky, přečtěte si, [Jak získat ID objektů uživatele](grant-access-to-create-subscription.md#userObjectId).

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

| Název elementu  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Ano      | Řetězec | Zobrazovaný název předplatného.|
| `billingProfileId`   | Ano      | Řetězec | ID fakturačního profilu, který se bude účtovat za poplatky za předplatné  |
| `skuId` | Ano      | Řetězec | ID SKU, které určuje typ plánu Azure. |
| `owners`      | Ne       | Řetězec | ID objektu libovolného uživatele nebo instančního objektu, který chcete přidat jako vlastníka RBAC v předplatném, když se vytvoří.  |
| `costCenter` | Ne      | Řetězec | Nákladové středisko přidružené k předplatnému. Zobrazuje se v souboru CSV o využití. |
| `managementGroupId` | Ne      | Řetězec | ID skupiny pro správu, do které bude předplatné přidáno. Seznam skupin pro správu získáte v tématu [rozhraní API pro skupiny pro správu seznam](https://docs.microsoft.com/rest/api/resources/managementgroups/list). Použijte ID skupiny pro správu z rozhraní API. |

V odpovědi se vrátí objekt `subscriptionCreationResult` pro monitorování. Po dokončení vytvoření odběru vrátí objekt `subscriptionCreationResult` objekt `subscriptionLink`, který má ID předplatného.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Vytvoření předplatných pro fakturační účet technologie MPA

### <a name="prerequisites"></a>Předpoklady

Abyste mohli vytvořit předplatné pro fakturační účet, musíte mít roli globálního správce nebo agenta správce v účtu poskytovatele Cloud Solution Provider vaší organizace. Další informace najdete v tématu [partner Center – přiřazení rolí a oprávnění uživatele](https://docs.microsoft.com/partner-center/permissions-overview).

Níže uvedený příklad používá rozhraní REST API. V současné době není prostředí PowerShell a rozhraní příkazového řádku Azure podporováno.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Najděte fakturační účty, ke kterým máte přístup. 

Pokud chcete zobrazit seznam všech fakturačních účtů, ke kterým máte přístup, vytvořte žádost níže.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
Seznam odpovědí rozhraní API pro fakturační účty.

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
Pomocí vlastnosti `displayName` Identifikujte fakturační účet, pro který chcete vytvořit odběry. Ujistěte se, že agreeementType účtu je *MicrosoftPartnerAgreement*. Zkopírujte `name` pro tento účet. Pokud třeba chcete vytvořit předplatné pro fakturační účet `Contoso`, nakopírujete `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Vložte tuto hodnotu někam, abyste ji mohli použít v dalším kroku.

### <a name="find-customers-that-have-azure-plans"></a>Vyhledání zákazníků s plány Azure

Proveďte následující požadavek a nahraďte `<billingAccountName>` pomocí `name` zkopírovaného z prvního kroku (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) a seznamte se se všemi zákazníky ve fakturačním účtu, pro které můžete vytvářet předplatná Azure. 

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
Odpověď rozhraní API uvádí zákazníky v fakturačním účtu s plány Azure. Pro tyto zákazníky můžete vytvořit odběry.

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

Pomocí vlastnosti `displayName` Identifikujte zákazníka, pro který chcete vytvořit odběry. Zkopírujte `id` pro zákazníka. Například pokud chcete vytvořit odběr pro `Fabrikam toys`, měli byste zkopírovat `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Sem vložte tuto hodnotu, abyste ji mohli použít v následujících krocích.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Volitelné pro nepřímé zprostředkovatele: získat prodejce pro zákazníka

Pokud jste nepřímým poskytovatelem v modelu dvou vrstev CSP, můžete zadat prodejce při vytváření předplatných pro zákazníky. 

Proveďte následující požadavek a nahraďte `<customerId>` pomocí `id` zkopírovaného z druhého kroku (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) pro výpis všech prodejců, kteří jsou pro zákazníka k dispozici.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
Odpověď rozhraní API seznam prodejců pro zákazníka:

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
K identifikaci prodejce, který bude přidružen k předplatnému, použijte vlastnost `description`. Zkopírujte `resellerId` pro prodejce. Například pokud chcete přidružit `Wingtip`, měli byste zkopírovat `3xxxxx`. Vložte tuto hodnotu někam, abyste ji mohli použít v dalším kroku.

### <a name="create-a-subscription-for-a-customer"></a>Vytvoření předplatného pro zákazníka

Následující příklad vytvoří předplatné s názvem *Dev Team Subscription* for *Fabrikam Toys* a přiřadí k předplatnému *společnost Wingtip* prodejce. bil.

Proveďte následující požadavek a nahraďte `<customerId>` pomocí `id` zkopírovaného z prvního kroku (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Předejte volitelné *resellerId* zkopírované z druhého kroku v parametrech požadavku rozhraní API. 

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Název elementu  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Ano      | Řetězec | Zobrazovaný název předplatného.|
| `skuId` | Ano      | Řetězec | ID SKU plánu Azure. Pro předplatná typu Microsoft Azureho plánu použijte *0001* . |
| `resellerId`      | Ne       | Řetězec | ID MPN prodejce, který bude přidružen k předplatnému.  |

V odpovědi se vrátí objekt `subscriptionCreationResult` pro monitorování. Po dokončení vytvoření odběru vrátí objekt `subscriptionCreationResult` objekt `subscriptionLink`, který má ID předplatného.

## <a name="next-steps"></a>Další kroky

* Příklad vytváření předplatného smlouva Enterprise (EA) pomocí rozhraní .NET najdete v tématu [vzorový kód na GitHubu](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Teď, když jste vytvořili předplatné, můžete tuto schopnost udělit ostatním uživatelům a instančním objektům. Další informace najdete v tématu [udělení přístupu k vytváření předplatných Azure Enterprise (Preview)](grant-access-to-create-subscription.md).
* Další informace o správě velkého počtu předplatných pomocí skupin pro správu najdete v tématu [uspořádání prostředků pomocí skupin pro správu Azure](management-groups-overview.md) .
