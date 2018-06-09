---
title: Vytváření prostřednictvím kódu programu předplatných Azure Enterprise | Microsoft Docs
description: Naučte se vytvořit další předplatné Azure Enterprise nebo Enterprise vývoje/testování programově.
services: azure-resource-manager
author: jlian
manager: jlian
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: jlian
ms.openlocfilehash: df66ba1ec2c855a24731387210b0127892f5796f
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234780"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Vytváření předplatných Azure Enterprise (preview) prostřednictvím kódu programu

Jako Azure odběratele na [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), můžete vytvořit EA (MS-AZR - 0017 P) a odběry EA pro vývoj/testování (MS-AZR - 0148 P) prostřednictvím kódu programu. V tomto článku zjistěte, jak vytvořit odběry programově pomocí Azure Resource Manager.

Při vytváření předplatného služby Azure z toto rozhraní API, toto předplatné se řídí smlouvy, pod kterým jste získali služby Microsoft Azure od společnosti Microsoft nebo autorizovaný prodejce. Další informace najdete v tématu [Microsoft Azure právní informace](https://azure.microsoft.com/support/legal/).

## <a name="prerequisites"></a>Požadavky

* Váš účet musí být vlastníkem účtu v Azure EA zápisu. Pokud ne, požádejte správce zápisu, aby [můžete přidat jako vlastníka účtu pomocí portálu EA](https://ea.azure.com/helpdocs/addNewAccount) (přihlášení vyžaduje). Postupujte podle pokynů v e-mailová pozvánka přijatých ručně vytvořit počáteční předplatné. Potvrdili vlastnictví účet a předplatné počáteční EA před pokračováním k dalšímu kroku, vytvořte ručně. Jenom přidání účtu, který se registrace není dost.

* Pokud chcete použít pro vytvoření odběru EA hlavní název služby, musíte [udělení tohoto objektu služby schopnost vytvářet odběry](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Najít účty, které máte přístup k

Po přidání k zápisu Azure EA jako vlastníka účtu Azure používá účet registrace vztah k určení místa k vyúčtování poplatky za předplatné. Všechny odběry pod účtem, se účtují směrem EA registrace, který účet je v. Chcete-li vytvářet odběry, musíte zadat hodnoty o registraci účtu a objekty uživatele k vlastní odběr. 

Pokud chcete spustit následující příkazy, musíte být přihlášení k majiteli účtu *domovský adresář*, což je adresáře, který odběry jsou vytvořené v ve výchozím nastavení.

# <a name="resttabrest"></a>[REST](#tab/rest)

Požadavek na seznamu všech účtů registrace:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure odpoví seznamu všech účtů registrace, ke kterým máte přístup do:

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

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Použití [příkaz Get-AzureRmEnrollmentAccount](/powershell/module/azurerm.billing/get-azurermenrollmentaccount) k zobrazení seznamu všech účtů registrace máte přístup k.

```azurepowershell-interactive
Get-AzureRmEnrollmentAccount
```

Azure odpoví seznam adres ID objektů a e-mailové účty.

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Použití [az fakturace účtu registrace seznamu](https://aka.ms/EASubCreationPublicPreviewCLI) seznam všech účtů registrace máte přístup k příkazu.

```azurecli-interactive 
az billing enrollment-account list
```

Azure odpoví seznam adres ID objektů a e-mailové účty.

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
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

---

Použití `principalName` vlastnost k identifikaci účtu, který má odběry účtován. Použít `id` jako `enrollmentAccount` hodnotu, která používáte k vytvoření odběru v dalším kroku.

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Vytvořte odběr pod účtem konkrétní registrace 

Následující příklad vytvoří žádost o vytvoření odběru s názvem *Dev tým předplatné* a nabídka předplatného je *MS-AZR - 0017P* (regulární EA). Účet pro zápis je `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (hodnotu zástupného symbolu, tato hodnota je identifikátor GUID), což je účet registrace pro SignUpEngineering@contoso.com. Přidá volitelně dva uživatele jako vlastníci RBAC pro předplatné.

# <a name="resttabrest"></a>[REST](#tab/rest)

Použití `id` z `enrollmentAccount` v cestě požadavek na vytvoření odběru.

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

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
| `displayName` | Ne      | Řetězec | Zobrazovaný název odběru. Pokud není zadáno, je nastavena na název nabídky, například "Microsoft Azure Enterprise."                                 |
| `offerType`   | Ano      | Řetězec | Nabídka předplatné. Jsou dvě možnosti pro EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produkční použití) a [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (vývoj/testování, musí být [zapnutá pomocí portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Ne       | Řetězec | ID objektu každý uživatel, který chcete přidat jako vlastníka RBAC na předplatné, když je vytvořeno.  |

V odpovědi, můžete se vrátit `subscriptionOperation` objekt pro monitorování. Po dokončení vytváření odběru `subscriptionOperation` by vrátit objekt `subscriptionLink` objekt, který má ID předplatného.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete používat tento modul preview, nainstalovat spuštěním `Install-Module AzureRM.Subscription -AllowPrerelease` první. Abyste měli jistotu, `-AllowPrerelease` funguje, nainstalujte nejnovější verzi PowerShellGet z [modulu Get PowerShellGet](/powershell/gallery/installing-psget).

Použití [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview) spolu s `enrollmentAccount` ID jako objektu `EnrollmentAccountObjectId` parametr k vytvoření nové předplatné. 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| Název elementu  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Ne      | Řetězec | Zobrazovaný název odběru. Pokud není zadáno, je nastavena na název nabídky, například "Microsoft Azure Enterprise."                                 |
| `OfferType`   | Ano      | Řetězec | Nabídka předplatné. Jsou dvě možnosti pro EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produkční použití) a [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (vývoj/testování, musí být [zapnutá pomocí portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Ano       | Řetězec | ID objektu registrace účet, který předplatné se vytvořil v rámci a účtován. Tato hodnota je identifikátor GUID, který můžete získat z `Get-AzureRmEnrollmentAccount`. |
| `OwnerObjectId`      | Ne       | Řetězec | ID objektu každý uživatel, který chcete přidat jako vlastníka RBAC na předplatné, když je vytvořeno.  |
| `OwnerSignInName`    | Ne       | Řetězec | E-mailovou adresu každý uživatel, který chcete přidat jako vlastníka RBAC na předplatné, když je vytvořeno. Můžete použít tento parametr místo `OwnerObjectId`.|
| `OwnerApplicationId` | Ne       | Řetězec | ID aplikace instanční objekt, který chcete přidat jako vlastníka RBAC na předplatné, když je vytvořeno. Můžete použít tento parametr místo `OwnerObjectId`.| 

Pokud chcete zobrazit úplný seznam všech parametrů, najdete v části [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li použít tuto příponu preview, nainstalujte jej spuštěním `az extension add --name subscription` první.

Použití [vytvořit účet az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) spolu s `enrollmentAccount` ID jako objektu `enrollment-account-object-id` parametr k vytvoření nové předplatné.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Název elementu  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Ne      | Řetězec | Zobrazovaný název odběru. Pokud není zadáno, je nastavena na název nabídky, například "Microsoft Azure Enterprise."                                 |
| `offer-type`   | Ano      | Řetězec | Nabídka předplatné. Jsou dvě možnosti pro EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produkční použití) a [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (vývoj/testování, musí být [zapnutá pomocí portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Ano       | Řetězec | ID objektu registrace účet, který předplatné se vytvořil v rámci a účtován. Tato hodnota je identifikátor GUID, který můžete získat z `az billing enrollment-account list`. |
| `owner-object-id`      | Ne       | Řetězec | ID objektu každý uživatel, který chcete přidat jako vlastníka RBAC na předplatné, když je vytvořeno.  |
| `owner-upn`    | Ne       | Řetězec | E-mailovou adresu každý uživatel, který chcete přidat jako vlastníka RBAC na předplatné, když je vytvořeno. Můžete použít tento parametr místo `owner-object-id`.|
| `owner-spn` | Ne       | Řetězec | ID aplikace instanční objekt, který chcete přidat jako vlastníka RBAC na předplatné, když je vytvořeno. Můžete použít tento parametr místo `owner-object-id`.| 

Pokud chcete zobrazit úplný seznam všech parametrů, najdete v části [vytvořit účet az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Omezení vytváření předplatného Azure Enterprise rozhraní API

- Toto rozhraní API pomocí lze vytvořit pouze předplatných Azure Enterprise.
- Je omezeno na 50 předplatná každý účet. Potom můžete odběry vytvořit pouze pomocí centra pro účet.
- Je potřeba aspoň jeden EA nebo EA vývoje/testování odběry pod účtem, což znamená, že vlastníka účtu prošel přes ruční registraci alespoň jednou.
- Uživatelé, kteří nejsou vlastníky účet, ale byly přidány k účtu registrace prostřednictvím RBAC, nelze vytvářet odběry pomocí centra účtů.
- Klient pro odběr vytvořené v nelze vybrat. Předplatné je vytvořen vždy v domácí klientovi vlastníka účtu. Předplatné přesunout do jiné klienta, najdete v tématu [Změna předplatného klienta](..\active-directory\active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Další postup

* Příklad k vytvoření odběrů pomocí rozhraní .NET, naleznete v části [ukázkový kód na Githubu](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Teď, když jste vytvořili předplatné, můžete udělit tuto možnost pro ostatní uživatele a objekty služby. Další informace najdete v tématu [udělit přístup k vytvoření předplatného Azure Enterprise (preview)](grant-access-to-create-subscription.md).
* Další informace o správě velkého počtu odběry pomocí skupin pro správu najdete v tématu [uspořádání prostředků s skupin pro správu Azure](management-groups-overview.md)
