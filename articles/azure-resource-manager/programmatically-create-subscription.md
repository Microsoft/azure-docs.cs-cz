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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/18/2018
ms.author: jlian
ms.openlocfilehash: 8d495bf89697a5e14ff79953ab98f241ef8972e8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Vytváření předplatných Azure Enterprise (preview) prostřednictvím kódu programu

Jako Azure odběratele na [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), můžete vytvořit EA (MS-AZR - 0017 P) a odběry EA pro vývoj/testování (MS-AZR - 0148 P) prostřednictvím kódu programu. Pokud chcete poskytnout jiný uživatel nebo instančního objektu oprávnění k vytvoření odběrů účtují ke svému účtu, přiřaďte jim [řízení přístupu na základě Role (RBAC)](../active-directory/role-based-access-control-configure.md) přístup ke svému účtu registrace. 

> [!IMPORTANT]
> Předplatná Azure vytvořené prostřednictvím toto rozhraní API se řídí smlouvy, pod kterým jste získali služby Microsoft Azure od společnosti Microsoft nebo autorizovaný prodejce. Další informace najdete v tématu [Microsoft Azure právní informace](https://azure.microsoft.com/support/legal/).

V tomto článku budete:

> [!div class="checklist"]
> * Naučte se vytvářet odběry programově pomocí Azure Resource Manager
> * Pochopit, jak používat funkci RBAC sdílet schopnost vytvářet odběry účtován účtu EA

Další informace naleznete [.NET ukázkový kód na Githubu](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).

## <a name="ask-your-ea-enrollment-admin-to-add-you-as-account-owner"></a>Požádejte správce registrace EA můžete přidat jako vlastníka účtu

Pokud chcete začít, požádejte správce zápisu, aby [můžete přidat jako vlastníka účtu pomocí portálu EA](https://ea.azure.com/helpdocs/addNewAccount) (přihlášení vyžaduje). Postupujte podle pokynů v e-mailová pozvánka přijatých ručně vytvořit počáteční předplatné.

> [!IMPORTANT]
> Musíte potvrdili vlastnictví účet a předplatné počáteční EA před pokračováním k dalšímu kroku, vytvořte ručně. Právě přidáním účtu do registrace není dost.

## <a name="find-accounts-you-have-access-to"></a>Najít účty, které máte přístup k

Po přidání k zápisu Azure EA jako vlastníka účtu Azure používá účet registrace vztah k určení místa k vyúčtování poplatky za předplatné. K vytvoření odběrů, nejprve zjistěte, jaké účty registrace, které máte přístup. Pokud jste aktuálně vlastníka účtu EA a pokusíte použít toto rozhraní API, Azure kontroluje následující podmínky:

- Váš účet byl přidán do EA zápisu
- Máte jeden nebo více EA nebo EA vývoje/testování předplatných, což znamená, že jste alespoň jednou prošli ruční registrace
- Jste přihlášení k majiteli účtu *domovský adresář*, což je adresáře, který odběry jsou vytvořené v ve výchozím nastavení

Pokud jsou výše uvedené dvě podmínky splněny, `enrollmentAccount` prostředků se vrátí, a můžete začít vytvářet odběry pod tímto účtem. Všechny odběry pod účtem, se účtují směrem EA registrace, který účet je v.

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
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
      "name": "<enrollmentAccountId>",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "MobileOnboardingEng@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
      "name": "<enrollmentAccountId>",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "MobileBackendEng@contoso.com"
      }
    }
  ]
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Použití [příkaz Get-AzureRmEnrollmentAccount](/powershell/module/azurerm.billing/get-azurermenrollmentaccount) k zobrazení seznamu všech účtů registrace máte přístup k.

```azurepowershell-interactive
Get-AzureRmEnrollmentAccount
```

Azure odpoví seznam adres ID objektů a e-mailové účty.

```azurepowershell
ObjectId                               | PrincipalName
<enrollmentAccountId>   | MobileOnboardingEng@contoso.com
<enrollmentAccountId>   | MobileBackendEng@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Použití [az fakturace účtu registrace seznamu](https://aka.ms/EASubCreationPublicPreviewCLI) seznam všech účtů registrace máte přístup k příkazu.

```azurecli-interactive 
az billing enrollment-account list
```
Azure odpoví seznam adres ID objektů a e-mailové účty.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
      "name": "<enrollmentAccountId>",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "MobileOnboardingEng@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
      "name": "<enrollmentAccountId>",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "MobileBackendEng@contoso.com"
      }
    }
  ]
}
```

---

Použití `principalName` vlastnost k identifikaci účtu, který má odběry účtován. Použít `id` jako `enrollmentAccount` hodnotu, která používáte k vytvoření odběru v dalším kroku.

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Vytvořte odběr pod účtem konkrétní registrace 

Následující příklad vytvoří žádost o vytvoření odběru s názvem *Dev tým předplatné* a nabídka předplatného je *MS-AZR - 0017P* (regulární EA). Účet pro zápis je `<enrollmentAccountId>`, což je účet registrace pro MobileOnboardingEng@contoso.com. Přidá volitelně dva uživatele jako vlastníci RBAC pro předplatné.

# <a name="resttabrest"></a>[REST](#tab/rest)

Použití `id` z `enrollmentAccount` v cestě požadavek na vytvoření odběru.

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

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

Pokud chcete používat tento modul preview, nainstalovat spuštěním `Install-Module AzureRM.Subscription -AllowPrerelease` první. Abyste měli jistotu, `-AllowPrerelease` funguje, nainstalujte nejnovější verzi PowerShellGet z [modulu Get PowerShellGet](/powershell/gallery/psget/get_psget_module).

Použití [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview) spolu s `enrollmentAccount` název jako `EnrollmentAccountObjectId` parametr k vytvoření nové předplatné. 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountId> -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| Název elementu  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Ne      | Řetězec | Zobrazovaný název odběru. Pokud není zadáno, je nastavena na název nabídky, například "Microsoft Azure Enterprise."                                 |
| `OfferType`   | Ano      | Řetězec | Nabídka předplatné. Jsou dvě možnosti pro EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produkční použití) a [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (vývoj/testování, musí být [zapnutá pomocí portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `OwnerObjectId`      | Ne       | Řetězec | ID objektu každý uživatel, který chcete přidat jako vlastníka RBAC na předplatné, když je vytvořeno.  |
| `OwnerSignInName`    | Ne       | Řetězec | E-mailovou adresu každý uživatel, který chcete přidat jako vlastníka RBAC na předplatné, když je vytvořeno. Můžete použít tento parametr místo `OwnerObjectId`.|
| `OwnerApplicationId` | Ne       | Řetězec | ID aplikace instanční objekt, který chcete přidat jako vlastníka RBAC na předplatné, když je vytvořeno. Můžete použít tento parametr místo `OwnerObjectId`.| 

Pokud chcete zobrazit úplný seznam všech parametrů, najdete v části [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li použít tuto příponu preview, nainstalujte jej spuštěním `az extension add --name subscription` první.

Použití [az účet vytvořit](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) spolu s `enrollmentAccount` název jako `enrollment_account_name` parametr k vytvoření nové předplatné.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-name "<enrollmentAccountId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Název elementu  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Ne      | Řetězec | Zobrazovaný název odběru. Pokud není zadáno, je nastavena na název nabídky, například "Microsoft Azure Enterprise."                                 |
| `offer-type`   | Ano      | Řetězec | Nabídka předplatné. Jsou dvě možnosti pro EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (produkční použití) a [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (vývoj/testování, musí být [zapnutá pomocí portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owner-object-id`      | Ne       | Řetězec | ID objektu každý uživatel, který chcete přidat jako vlastníka RBAC na předplatné, když je vytvořeno.  |
| `owner-upn`    | Ne       | Řetězec | E-mailovou adresu každý uživatel, který chcete přidat jako vlastníka RBAC na předplatné, když je vytvořeno. Můžete použít tento parametr místo `owner-object-id`.|
| `owner-spn` | Ne       | Řetězec | ID aplikace instanční objekt, který chcete přidat jako vlastníka RBAC na předplatné, když je vytvořeno. Můžete použít tento parametr místo `owner-object-id`.| 

Pokud chcete zobrazit úplný seznam všech parametrů, najdete v části [vytvořit účet az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Delegovat přístup k účtu registrace pomocí RBAC

Umožnit jiný uživatel nebo instanční objekt schopnost vytvářet odběry proti určitého účtu, [jim poskytnout roli vlastníka RBAC v oboru účet zápisu](../active-directory/role-based-access-control-manage-access-rest.md). Následující příklad obsahuje uživatele v klientovi s `principalId` z `<userObjectId>` (pro MobileOnboardingEng@contoso.com) roli vlastníka na účet pro zápis. 

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>"
  }
}
```
Když jsou v rozsahu účtu registrace úspěšně přiřazeny roli vlastníka, odpoví Azure informace o přiřazení role:

```json
{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>",
    "scope": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
    "createdOn": "2018-03-05T08:36:26.4014813Z",
    "updatedOn": "2018-03-05T08:36:26.4014813Z",
    "createdBy": "<assignerObjectId>",
    "updatedBy": "<assignerObjectId>"
  },
  "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "<roleAssignmentGuid>"
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Použití [New-AzureRmRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) k přidělit jinému uživateli vlastníka přístup ke svému účtu registrace.

```azurepowershell-interactive
New-AzureRmRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Použití [vytvořit přiřazení role az](../active-directory/role-based-access-control-manage-access-azure-cli.md) k přidělit jinému uživateli vlastníka přístup ke svému účtu registrace.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>
```

----

Jakmile uživatel změní na vlastníka RBAC pro váš účet pro zápis, mohou prostřednictvím kódu programu vytvářet odběry v něm. Předplatné vytvořené delegovaný uživatel má stále původního vlastníka účtu jako správce služby, ale má také delegovaný uživatel jako vlastníka ve výchozím nastavení. 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Audit, který vytvořil odběry pomocí protokoly aktivity

Chcete-li sledovat odběry vytvořené prostřednictvím toto rozhraní API, použijte [rozhraní API pro klienty aktivity protokolu](/rest/api/monitor/tenantactivitylogs). Aktuálně není možné použít PowerShell, rozhraní příkazového řádku nebo portálu Azure ke sledování vytváření odběru.

1. Jako správce klienta klienta Azure AD [zvýšení přístupu](../active-directory/role-based-access-control-tenant-admin-access.md) pak auditování uživateli podle oboru přiřadit role Čtenář `/providers/microsoft.insights/eventtypes/management`.
1. Jako uživatel auditování, volání [rozhraní API pro klienty aktivity protokolu](/rest/api/monitor/tenantactivitylogs) zobrazíte předplatné Vytvoření aktivity. Příklad:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> Toto rozhraní API pohodlně volat z příkazového řádku, zkuste [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Omezení vytváření předplatného Azure Enterprise rozhraní API

- Toto rozhraní API pomocí lze vytvořit pouze předplatných Azure Enterprise.
- Je omezeno na 50 předplatná každý účet. Potom můžete odběry vytvořit pouze pomocí centra pro účet.
- Je potřeba aspoň jeden EA nebo EA vývoje/testování odběry pod účtem, což znamená, že vlastníka účtu prošel přes ruční registraci alespoň jednou.
- Uživatelé, kteří nejsou vlastníky účet, ale byly přidány k účtu registrace prostřednictvím RBAC, nelze vytvářet odběry pomocí centra účtů.
- Klient pro odběr vytvořené v nelze vybrat. Předplatné je vytvořen vždy v domácí klientovi vlastníka účtu. Předplatné přesunout do jiné klienta, najdete v tématu [Změna předplatného klienta](..\active-directory\active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Další postup

* Příklad k vytvoření odběrů pomocí rozhraní .NET, naleznete v části [ukázkový kód na Githubu](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Další informace o Azure Resource Manager a jejích rozhraní API najdete v tématu [přehled Azure Resource Manageru](resource-group-overview.md).
* Další informace o správě velkého počtu odběry pomocí skupin pro správu najdete v tématu [uspořádání prostředků s skupin pro správu Azure](management-groups-overview.md)
* Komplexní osvědčených postupů pokyny pro velké organizace na řízení předplatného najdete v tématu [Azure enterprise vygenerované uživatelské rozhraní – zásady správného řízení doporučený předplatného](resource-manager-subscription-governance.md)
