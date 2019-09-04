---
title: Vytváření předplatných Azure Enterprise prostřednictvím kódu programu | Microsoft Docs
description: Naučte se vytvářet další předplatná Azure Enterprise nebo Enterprise pro vývoj/testování programově.
services: azure-resource-manager
author: jureid
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: jureid
ms.openlocfilehash: 755eabe97508b403205ff04a8d2d35feee314eb9
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258932"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Vytváření předplatných Azure Enterprise (Preview) prostřednictvím kódu programu

Jako zákazník Azure on [smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)můžete programově vytvořit odběry EA (MS-AZR-0017P) a EA dev/test (MS-AZR-0148P). V tomto článku se naučíte, jak vytvářet odběry prostřednictvím Azure Resource Manager.

Při vytváření předplatného Azure z tohoto rozhraní API se toto předplatné řídí smlouvou, na základě které jste získali Microsoft Azure služby od společnosti Microsoft nebo autorizovaného prodejce. Další informace najdete v tématu [Microsoft Azure právní informace](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Na účtu pro registraci, na kterém chcete vytvořit předplatná, musíte mít roli vlastníka. Existují dva způsoby, jak získat tyto role:

* Správce registrace vám může [vytvořit vlastníka účtu](https://ea.azure.com/helpdocs/addNewAccount) (vyžaduje se přihlášení), který vás provede vlastníkem registračního účtu. Postupujte podle pokynů v e-mailu s pozvánkou, kterou obdržíte, abyste ručně vytvořili počáteční předplatné. Než budete pokračovat k dalšímu kroku, potvrďte vlastnictví účtu a ručně vytvořte počáteční předplatné EA. Pouze přidávání účtu k registraci není dostatečné.

* [Přístup může udělit](grant-access-to-create-subscription.md)stávající vlastník účtu pro zápis. Podobně pokud chcete k vytvoření předplatného EA použít instanční objekt, musíte [tomuto instančnímu objektu udělit možnost vytvářet odběry](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Vyhledání účtů, ke kterým máte přístup

Po přidání k registraci EA Azure jako vlastníka účtu Azure pomocí vztahu account-to-Enrollment určí, kde se mají účtovat poplatky za předplatné. Všechna předplatná vytvořená v rámci účtu se účtují k registraci EA, na které je účet. Chcete-li vytvořit odběry, musíte předat hodnoty týkající se účtu registrace a objektů zabezpečení uživatele k vlastnímu předplatnému. 

Chcete-li spustit následující příkazy, musíte být přihlášeni k *domovskému adresáři*vlastníka účtu, což je adresář, ve kterém jsou předplatná vytvořena ve výchozím nastavení.

## <a name="resttabrest"></a>[REST](#tab/rest)

Požadavek na výpis všech účtů pro zápis, ke kterým máte přístup:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure odpoví seznamem všech účtů zápisu, ke kterým máte přístup:

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

`principalName` Pomocí vlastnosti Identifikujte účet, na který se mají odběry fakturovat. `name` Zkopírujte účet tohoto účtu. Pokud byste například chtěli vytvořit předplatná v rámci SignUpEngineering@contoso.com účtu pro registraci, budete zkopírováni. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Tento identifikátor je ID objektu registračního účtu. Vložte tuto hodnotu někam, abyste ji mohli použít v dalším kroku `enrollmentAccountObjectId`.

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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
`principalName` Pomocí vlastnosti Identifikujte účet, na který se mají odběry fakturovat. `ObjectId` Zkopírujte účet tohoto účtu. Pokud byste například chtěli vytvořit předplatná v rámci SignUpEngineering@contoso.com účtu pro registraci, budete zkopírováni. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Vložte toto ID objektu někam, abyste ho mohli použít v dalším kroku jako `enrollmentAccountObjectId`.

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

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

`principalName` Pomocí vlastnosti Identifikujte účet, na který se mají odběry fakturovat. `name` Zkopírujte účet tohoto účtu. Pokud byste například chtěli vytvořit předplatná v rámci SignUpEngineering@contoso.com účtu pro registraci, budete zkopírováni. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Tento identifikátor je ID objektu registračního účtu. Vložte tuto hodnotu někam, abyste ji mohli použít v dalším kroku `enrollmentAccountObjectId`.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Vytváření předplatných v rámci konkrétního účtu registrace

Následující příklad vytvoří předplatné s názvem *vývojové týmu Subscription* v účtu pro zápis, který jste vybrali v předchozím kroku. Nabídka předplatného je *MS-AZR-0017P* (regular Microsoft smlouva Enterprise). Volitelně taky přidá dva uživatele jako vlastníky RBAC pro předplatné.

# <a name="resttabrest"></a>[REST](#tab/rest)

Proveďte následující požadavek a nahraďte `<enrollmentAccountObjectId>` `name` ho zkopírovaným z prvního kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Pokud chcete zadat vlastníky, přečtěte si, [Jak získat ID objektů uživatele](grant-access-to-create-subscription.md#userObjectId).

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

| Název elementu  | Požadováno | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Ne      | Řetězec | Zobrazovaný název předplatného. Pokud není zadaný, nastaví se na název nabídky, například Microsoft Azure Enterprise.                                 |
| `offerType`   | Ano      | Řetězec | Nabídka předplatného. Dvě možnosti pro EA jsou [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produkční použití) a [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (vývoj/testování), které je potřeba [zapnout pomocí portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer).                |
| `owners`      | Ne       | Řetězec | ID objektu libovolného uživatele, který chcete přidat jako vlastníka RBAC v předplatném, když je vytvořen.  |

V odpovědi se vrátí `subscriptionOperation` objekt pro monitorování. Po dokončení `subscriptionOperation` vytváření odběru objekt `subscriptionLink` vrátí objekt, který má ID předplatného.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Nejdřív nainstalujte tento modul verze Preview spuštěním `Install-Module Az.Subscription -AllowPrerelease`. Abyste se ujistili, že `-AllowPrerelease` funguje, nainstalujte si nejnovější verzi PowerShellGet z [modulu získat PowerShellGet](/powershell/gallery/installing-psget).

Spusťte následující příkaz [New-AzSubscription](/powershell/module/az.subscription) a nahraďte `<enrollmentAccountObjectId>` `ObjectId` ho shromážděným v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Pokud chcete zadat vlastníky, přečtěte si, [Jak získat ID objektů uživatele](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Název elementu  | Požadováno | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Ne      | Řetězec | Zobrazovaný název předplatného. Pokud není zadaný, nastaví se na název nabídky, například Microsoft Azure Enterprise.                                 |
| `OfferType`   | Ano      | Řetězec | Nabídka předplatného. Dvě možnosti pro EA jsou [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produkční použití) a [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (vývoj/testování), které je potřeba [zapnout pomocí portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer).                |
| `EnrollmentAccountObjectId`      | Ano       | Řetězec | ID objektu účtu pro zápis, pod kterým se předplatné vytvoří a účtuje se. Tato hodnota je identifikátor GUID, ze `Get-AzEnrollmentAccount`kterého se dostanete. |
| `OwnerObjectId`      | Ne       | Řetězec | ID objektu libovolného uživatele, který chcete přidat jako vlastníka RBAC v předplatném, když je vytvořen.  |
| `OwnerSignInName`    | Ne       | Řetězec | E-mailová adresa libovolného uživatele, který chcete přidat jako vlastníka RBAC v předplatném, když se vytvoří. Tento parametr můžete použít místo `OwnerObjectId`.|
| `OwnerApplicationId` | Ne       | Řetězec | ID aplikace libovolného instančního objektu, který chcete přidat jako vlastníka RBAC v předplatném, když se vytvoří. Tento parametr můžete použít místo `OwnerObjectId`. Při použití tohoto parametru musí instanční objekt mít k [adresáři přístup pro čtení](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Úplný seznam všech parametrů naleznete v části [New-AzSubscription](/powershell/module/az.subscription).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Nejdřív nainstalujte toto rozšíření ve verzi Preview spuštěním `az extension add --name subscription`.

Spusťte příkaz [AZ Account Create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) níže a nahraďte `<enrollmentAccountObjectId>` `name` ho, který jste zkopírovali v prvním kroku```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```(). Pokud chcete zadat vlastníky, přečtěte si, [Jak získat ID objektů uživatele](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Název elementu  | Požadováno | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Ne      | Řetězec | Zobrazovaný název předplatného. Pokud není zadaný, nastaví se na název nabídky, například Microsoft Azure Enterprise.                                 |
| `offer-type`   | Ano      | Řetězec | Nabídka předplatného. Dvě možnosti pro EA jsou [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produkční použití) a [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (vývoj/testování), které je potřeba [zapnout pomocí portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer).                |
| `enrollment-account-object-id`      | Ano       | Řetězec | ID objektu účtu pro zápis, pod kterým se předplatné vytvoří a účtuje se. Tato hodnota je identifikátor GUID, ze `az billing enrollment-account list`kterého se dostanete. |
| `owner-object-id`      | Ne       | Řetězec | ID objektu libovolného uživatele, který chcete přidat jako vlastníka RBAC v předplatném, když je vytvořen.  |
| `owner-upn`    | Ne       | Řetězec | E-mailová adresa libovolného uživatele, který chcete přidat jako vlastníka RBAC v předplatném, když se vytvoří. Tento parametr můžete použít místo `owner-object-id`.|
| `owner-spn` | Ne       | Řetězec | ID aplikace libovolného instančního objektu, který chcete přidat jako vlastníka RBAC v předplatném, když se vytvoří. Tento parametr můžete použít místo `owner-object-id`. Při použití tohoto parametru musí instanční objekt mít k [adresáři přístup pro čtení](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Úplný seznam všech parametrů najdete v tématu [AZ Account Create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Omezení rozhraní API pro vytváření předplatných Azure Enterprise

- Pomocí tohoto rozhraní API se dají vytvořit jenom předplatná Azure Enterprise.
- Pro každý registrační účet je povolený limit 200 předplatných. Další předplatná pro tento účet se pak dají vytvořit jenom prostřednictvím centra účtů. Pokud chcete vytvořit další odběry prostřednictvím rozhraní API, vytvořte další účet pro zápis.
- Uživatelé, kteří nejsou vlastníkem účtu, ale Přidali se k účtu pro zápis přes RBAC, nemůžou vytvářet předplatná pomocí centra účtů.
- Nemůžete vybrat tenanta, ve kterém se má předplatné vytvořit. Předplatné se vždy vytvoří v domovském tenantovi vlastníka účtu. Pokud chcete přesunout předplatné do jiného tenanta, přečtěte si téma [Změna tenanta předplatného](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Další postup

* Příklad vytváření předplatných pomocí .NET najdete v tématu [vzorový kód na GitHubu](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Teď, když jste vytvořili předplatné, můžete tuto schopnost udělit ostatním uživatelům a instančním objektům. Další informace najdete v tématu [udělení přístupu k vytváření předplatných Azure Enterprise (Preview)](grant-access-to-create-subscription.md).
* Další informace o správě velkého počtu předplatných pomocí skupin pro správu najdete v tématu [uspořádání prostředků pomocí skupin pro správu Azure](management-groups-overview.md) .
