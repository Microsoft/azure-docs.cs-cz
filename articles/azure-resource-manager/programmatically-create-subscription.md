---
title: Programové vytváření předplatných Azure Enterprise | Dokumentace Microsoftu
description: Zjistěte, jak programově vytvářet další předplatná Azure Enterprise nebo Enterprise pro vývoj/testování.
services: azure-resource-manager
author: adpick
manager: adpick
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: adpick
ms.openlocfilehash: 2bfa9944d85fde65ad8dbd73ddda11fa405df2f8
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358347"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Programové vytváření předplatných Azure Enterprise (preview)

Jako zákazník Azure na [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), EA (MS-AZR - 0017 P) a předplatných EA pro vývoj/testování (MS-AZR - 0148 P) můžete vytvořit prostřednictvím kódu programu. V tomto článku se dozvíte, jak vytvářet předplatná prostřednictvím kódu programu pomocí Azure Resource Manageru.

Při vytváření předplatného služby Azure z tohoto rozhraní API, toto předplatné se řídí Smlouvou, pod kterým jste získali služby Microsoft Azure od společnosti Microsoft nebo autorizovaného prodejce. Další informace najdete v tématu [právní informace týkající se Microsoft Azure](https://azure.microsoft.com/support/legal/).

## <a name="prerequisites"></a>Požadavky

Chcete vytvářet předplatná v rámci registrace účtu musí mít roli vlastníka nebo přispěvatele. Existují dva způsoby, jak získat tyto role:

* Správce registrace můžete [můžete nastavit jako vlastníka účtu](https://ea.azure.com/helpdocs/addNewAccount) (přihlášení vyžaduje) díky vlastníka účtu pro zápis. Postupujte podle pokynů v e-mailové pozvánce, že abyste dostávali ruční vytvoření počátečního předplatného. Potvrdit vlastnictví účtu a ruční vytvoření počátečního předplatného EA, než budete pokračovat k dalšímu kroku. Stačí přidat účet pro přihlášení není k dispozici dostatek.

* Můžete stávající vlastník účtu registrace [udělení přístupu k](grant-access-to-create-subscription.md). Podobně, pokud chcete použít k vytvoření předplatného EA instanční objekt služby, musíte [udělit takového objektu služby možnost vytvářet předplatná](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Najít účty, ke kterým máte přístup k

Po přidání na registraci smlouvy Azure EA jako vlastník účtu Azure používá k určení, kam účtovat poplatky za odběr vztah registrace účtu. Všechna předplatná vytvořená v rámci účtu se účtují směrem k registraci smlouvy Enterprise, který obsahuje příslušný účet. Vytvářet předplatná, musíte předat hodnoty o registraci účtu a objekty zabezpečení uživatelů k vlastní předplatné. 

Pokud chcete spustit následující příkazy, musíte být přihlášení k majiteli účtu *domovský adresář*, což je adresář, který odběry vytvářejí ve výchozím nastavení.

# <a name="resttabrest"></a>[REST](#tab/rest)

Požadavek na výpis všech registračních účtů:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure jako odpověď vrátí seznam všech registračních účtů, ke kterým máte přístup k:

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

Použití [příkazu Get-AzureRmEnrollmentAccount](/powershell/module/azurerm.billing/get-azurermenrollmentaccount) vypsat všechny účty registrace máte přístup k.

```azurepowershell-interactive
Get-AzureRmEnrollmentAccount
```

Azure jako odpověď vrátí seznam ID objektů a e-mailové adresy účtů.

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Použití [az fakturační účet registrace seznamu](https://aka.ms/EASubCreationPublicPreviewCLI) seznam všech registračních účtů, máte přístup k příkazu.

```azurecli-interactive 
az billing enrollment-account list
```

Azure jako odpověď vrátí seznam ID objektů a e-mailové adresy účtů.

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

Použití `principalName` vlastnost k identifikaci účtu, který má předplatné účtovat na. Použít `id` jako `enrollmentAccount` hodnotu, kterou použijete k vytvoření odběru v dalším kroku.

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Vytvářet předplatná v rámci konkrétní registraci účtu 

Následující příklad vytvoří požadavek na vytvoření odběr s názvem *Dev týmového odběru* a nabídky předplatného je *MS-AZR - 0017P* (pravidelných EA). Účet pro zápis je `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (hodnotu zástupného symbolu, tato hodnota je identifikátor GUID), což je registrace účtu pro SignUpEngineering@contoso.com. Také v případě potřeby přidá dva uživatele jako vlastníky RBAC pro předplatné.

# <a name="resttabrest"></a>[REST](#tab/rest)

Použití `id` z `enrollmentAccount` v cestě požadavek na vytvoření předplatného.

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
| `displayName` | Ne      | Řetězec | Zobrazovaný název předplatného. Pokud není zadán, je nastavena na název nabídky, jako je "Microsoft Azure Enterprise."                                 |
| `offerType`   | Ano      | Řetězec | Nabídka předplatného. Jsou dvě možnosti pro EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (použití v produkčním prostředí) a [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (pro vývoj/testování, musí být [zapnuté na portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Ne       | Řetězec | ID objektu každý uživatel, který chcete přidat jako vlastníka předplatného RBAC při jeho vytvoření.  |

V odpovědi, které získáte zpět `subscriptionOperation` objekt monitorování. Po dokončení vytvoření předplatného `subscriptionOperation` vrátí objekt `subscriptionLink` objektu, který má ID předplatného.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete použít tento modul ve verzi preview, nainstalujte ho spuštěním `Install-Module AzureRM.Subscription -AllowPrerelease` první. Aby se zajistilo `-AllowPrerelease` funguje, nainstalujte nejnovější verzi modulu PowerShellGet z [získat modul PowerShellGet](/powershell/gallery/installing-psget).

Použití [New-AzureRmSubscription](/powershell/module/azurerm.subscription) spolu s `enrollmentAccount` jako ID objektu `EnrollmentAccountObjectId` parametr k vytvoření nového předplatného. 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| Název elementu  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Ne      | Řetězec | Zobrazovaný název předplatného. Pokud není zadán, je nastavena na název nabídky, jako je "Microsoft Azure Enterprise."                                 |
| `OfferType`   | Ano      | Řetězec | Nabídka předplatného. Jsou dvě možnosti pro EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (použití v produkčním prostředí) a [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (pro vývoj/testování, musí být [zapnuté na portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Ano       | Řetězec | Účet pro zápis, že je předplatné vytvořené v rámci a účtuje na ID objektu. Tato hodnota je identifikátor GUID, který obdržíte od `Get-AzureRmEnrollmentAccount`. |
| `OwnerObjectId`      | Ne       | Řetězec | ID objektu každý uživatel, který chcete přidat jako vlastníka předplatného RBAC při jeho vytvoření.  |
| `OwnerSignInName`    | Ne       | Řetězec | E-mailová adresa každý uživatel, který chcete přidat jako vlastníka předplatného RBAC při jeho vytvoření. Můžete použít tento parametr místo `OwnerObjectId`.|
| `OwnerApplicationId` | Ne       | Řetězec | ID aplikace všechny instanční objekt, který chcete přidat jako vlastníka předplatného RBAC při jeho vytvoření. Můžete použít tento parametr místo `OwnerObjectId`.| 

Úplný seznam všech parametrů najdete v tématu [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Použití tohoto rozšíření ve verzi preview, nainstalujte ho spuštěním `az extension add --name subscription` první.

Použití [vytvořit účet az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) spolu s `enrollmentAccount` jako ID objektu `enrollment-account-object-id` parametr k vytvoření nového předplatného.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Název elementu  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Ne      | Řetězec | Zobrazovaný název předplatného. Pokud není zadán, je nastavena na název nabídky, jako je "Microsoft Azure Enterprise."                                 |
| `offer-type`   | Ano      | Řetězec | Nabídka předplatného. Jsou dvě možnosti pro EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (použití v produkčním prostředí) a [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (pro vývoj/testování, musí být [zapnuté na portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Ano       | Řetězec | Účet pro zápis, že je předplatné vytvořené v rámci a účtuje na ID objektu. Tato hodnota je identifikátor GUID, který obdržíte od `az billing enrollment-account list`. |
| `owner-object-id`      | Ne       | Řetězec | ID objektu každý uživatel, který chcete přidat jako vlastníka předplatného RBAC při jeho vytvoření.  |
| `owner-upn`    | Ne       | Řetězec | E-mailová adresa každý uživatel, který chcete přidat jako vlastníka předplatného RBAC při jeho vytvoření. Můžete použít tento parametr místo `owner-object-id`.|
| `owner-spn` | Ne       | Řetězec | ID aplikace všechny instanční objekt, který chcete přidat jako vlastníka předplatného RBAC při jeho vytvoření. Můžete použít tento parametr místo `owner-object-id`.| 

Úplný seznam všech parametrů najdete v tématu [vytvořit účet az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Omezení vytváření předplatného Azure Enterprise API

- Pouze Azure Enterprise odběry můžete vytvořit pomocí tohoto rozhraní API.
- Platí omezení na jeden účet 50 předplatných. Potom můžete odběry vytvořit pouze pomocí centra pro účty.
- Musí existovat alespoň jeden EA nebo EA pro vývoj/testování předplatných v rámci účtu, což znamená, že vlastník účtu má prošli ruční registraci alespoň jednou.
- Uživatelé, kteří nejsou vlastníky účtů, ale byly přidány do registrace účtu prostřednictvím RBAC, nelze vytvářet odběry pomocí centra pro účty.
- Nelze vybrat tenanta pro předplatné má být vytvořen v. Předplatné je vytvořen vždy v domovském tenantovi vlastníka účtu. Chcete-li přesunout předplatné na jiného tenanta, naleznete v tématu [změnit předplatného tenanta](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Další postup

* Příklad vytvoření předplatného pomocí rozhraní .NET, naleznete v tématu [ukázkový kód na Githubu](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Teď, když vytvoříte odběr, můžete udělit tuto možnost pro ostatní uživatele a instančních objektů. Další informace najdete v tématu [udělit přístup k vytvoření předplatného Azure Enterprise (preview)](grant-access-to-create-subscription.md).
* Další informace o správě velkého počtu předplatných pomocí skupin pro správu najdete v tématu [uspořádání prostředků se skupinami pro správu Azure](management-groups-overview.md)
