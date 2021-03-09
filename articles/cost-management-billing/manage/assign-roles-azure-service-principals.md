---
title: Přiřazení rolí k hlavním názvům služeb Azure smlouva Enterprise
description: Tento článek vám pomůže přiřadit role k hlavním názvům služby pomocí PowerShellu a rozhraní REST API.
author: bandersmsft
ms.reviewer: ruturajd
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/07/2021
ms.author: banders
ms.openlocfilehash: 0f30c90bf81a837b1e78ca5f91450cf085cc91bc
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102495071"
---
# <a name="assign-roles-to-azure-enterprise-agreement-service-principal-names"></a>Přiřazení rolí k hlavním názvům služeb Azure smlouva Enterprise

Registraci smlouva Enterprise (EA) můžete spravovat na [portálu Azure Enterprise](https://ea.azure.com/). Můžete vytvářet různé role pro správu vaší organizace, zobrazovat náklady a vytvářet předplatná. Tento článek vám pomůže automatizovat některé z těchto úloh pomocí Azure PowerShell a rozhraní REST API s použitím hlavních názvů služby (SPN) Azure.

Než začnete, ujistěte se, že máte zkušenosti s následujícími články:

- [Role smlouvy Enterprise](understand-ea-roles.md)
- [Přihlášení pomocí Azure PowerShellu](/powershell/azure/authenticate-azureps?view=azps-5.5.0&preserve-view=true)
- [Volání rozhraní REST API pomocí metody post](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

## <a name="create-and-authenticate-your-service-principal"></a>Vytvoření a ověření objektu služby

Chcete-li automatizovat akce EA pomocí hlavního názvu služby (SPN), je nutné vytvořit aplikaci Azure Active Directory (Azure AD). Může se ověřit automatizovaným způsobem. Přečtěte si následující články a podle kroků v nich vytvořte a ověřte váš instanční objekt.

1. [Vytvoření instančního objektu](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [Získání hodnot ID tenanta a aplikace pro přihlášení](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

Tady je příklad snímku obrazovky, který ukazuje registraci aplikace.

:::image type="content" source="./media/assign-roles-azure-service-principals/register-an-application.png" alt-text="Snímek obrazovky, který ukazuje registraci aplikace" lightbox="./media/assign-roles-azure-service-principals/register-an-application.png" :::

### <a name="find-your-spn-and-tenant-id"></a>Vyhledat hlavní název služby (SPN) a ID tenanta

Budete také potřebovat ID objektu hlavního názvu služby (SPN) a ID klienta aplikace. Informace o operacích přiřazení oprávnění budete potřebovat v dalších oddílech.

ID tenanta aplikace Azure AD najdete na stránce Přehled pro danou aplikaci. Pokud ho chcete najít v Azure Portal, přejděte na Azure Active Directory a vyberte **podnikové aplikace**. Vyhledejte aplikaci.

:::image type="content" source="./media/assign-roles-azure-service-principals/enterprise-application.png" alt-text="Snímek obrazovky znázorňující ukázkovou podnikovou aplikaci." lightbox="./media/assign-roles-azure-service-principals/enterprise-application.png" :::

Vyberte aplikaci. Zde je příklad ukazující ID aplikace a ID objektu.

:::image type="content" source="./media/assign-roles-azure-service-principals/application-id-object-id.png" alt-text="Snímek obrazovky znázorňující ID aplikace a ID objektu pro podnikovou aplikaci." lightbox="./media/assign-roles-azure-service-principals/application-id-object-id.png" :::

ID klienta najdete na stránce Přehled Microsoft Azure AD.

:::image type="content" source="./media/assign-roles-azure-service-principals/tenant-id.png" alt-text="Snímek obrazovky s informacemi o tom, kde zobrazit ID tenanta" lightbox="./media/assign-roles-azure-service-principals/tenant-id.png" :::

Vaše hlavní ID tenanta se také označuje jako ID objektu zabezpečení, hlavní název služby (SPN) a ID objektu v různých umístěních. Hodnota ID tenanta Azure AD vypadá jako GUID s následujícím formátem: `11111111-1111-1111-1111-111111111111` .

## <a name="permissions-that-can-be-assigned-to-the-spn"></a>Oprávnění, která se dají přiřadit k hlavnímu názvu služby (SPN)

V dalších krocích udělíte aplikaci Azure AD oprávnění k provádění akcí pomocí role EA. SPN můžete přiřadit pouze následující role. ID definice role, přesně jak je uvedeno, se později používá v krocích přiřazení.

| Role | Povolené akce | ID definice role |
| --- | --- | --- |
| EnrollmentReader | Může zobrazit využití a poplatky napříč všemi účty a předplatnými. Může zobrazit zůstatek předplacené Azure (dříve označovaný jako peněžní závazek), který je přidružený k registraci. | 24f8edb6-1668-4659-b5e2-40bb5f3a7d7e |
| DepartmentReader | Stáhněte si podrobnosti o využití pro oddělení, které spravuje. Může zobrazit využití a poplatky spojené s jejich oddělením. | db609904-a47f-4794-9be8-9bd86fbffd8a |
| SubscriptionCreator | Vytvoří nová předplatná v daném oboru účtu. | a0bcee42-bf30-4d1b-926a-48d21664ef71 |

- Čtečku zápisu je možné přiřadit k hlavnímu názvu služby (SPN) pouze uživateli s rolí zapisovače zápisu.
- Čtečku oddělení je možné přiřadit k hlavnímu názvu služby jenom uživateli, který má roli zapisovače zápisu nebo roli zapisovače oddělení.
- Role tvůrce předplatného může být přiřazena k hlavnímu názvu služby (SPN) pouze uživateli, který je vlastníkem účtu registrace.

## <a name="assign-enrollment-account-role-permission-to-the-spn"></a>Přiřazení oprávnění role účtu pro zápis k hlavnímu názvu služby (SPN)

Přečtěte si článek [přiřazení rolí – Put](/rest/api/billing/2019-10-01-preview/roleassignments/put) REST API.

Při čtení článku vyberte **vyzkoušet** , abyste mohli začít používat hlavní název služby (SPN).

:::image type="content" source="./media/assign-roles-azure-service-principals/put-try-it.png" alt-text="Snímek obrazovky znázorňující možnost vyzkoušet si v článku Put" lightbox="./media/assign-roles-azure-service-principals/put-try-it.png" :::

Přihlaste se pomocí svého účtu do tenanta, který má přístup k registraci, ke které chcete přiřadit přístup.

Jako součást požadavku rozhraní API zadejte následující parametry.

**billingAccountName**

Parametr je ID fakturačního účtu. V Azure Portal najdete na stránce Přehled Cost Management + fakturace.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Snímek obrazovky zobrazující ID fakturačního účtu" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

Parametr je jedinečný identifikátor GUID, který je třeba zadat. Identifikátor GUID můžete vygenerovat pomocí příkazu [New-GUID](/powershell/module/microsoft.powershell.utility/new-guid?view=powershell-7.1&preserve-view=true) PowerShell.

Nebo můžete k vygenerování jedinečného identifikátoru GUID použít web [GUID a generátor identifikátoru UUID online](https://guidgenerator.com/) .

**verze API-Version**

Použijte verzi **2019-10-01-Preview** .

Tělo žádosti obsahuje kód JSON, který je třeba použít.

Použijte vzorový text žádosti na [přiřazení rolí – příklady Put](/rest/api/billing/2019-10-01-preview/roleassignments/put#examples).

Existují tři parametry, které je třeba použít jako součást formátu JSON.

| Parametr | Kde ji najít |
| --- | --- |
| Properties. principalId | Viz [najít hlavní název služby (SPN) a ID tenanta](#find-your-spn-and-tenant-id). |
| Properties. principalTenantId | Viz [najít hlavní název služby (SPN) a ID tenanta](#find-your-spn-and-tenant-id). |
| Properties. roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/24f8edb6-1668-4659-b5e2-40bb5f3a7d7e" |

Název fakturačního účtu je stejný parametr, který jste použili v parametrech rozhraní API. Je to ID registrace, které vidíte na portálu EA a Azure Portal.

Všimněte si, že `24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` je ID definice fakturační role pro EnrollmentReader.

Vyberte **Spustit** a spusťte příkaz.

:::image type="content" source="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" alt-text="Snímek obrazovky s ukázkovým přiřazením rolí – Vyzkoušejte si to s ukázkovými informacemi, které jsou připravené ke spuštění." lightbox="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" :::

`200 OK`Odpověď ukazuje, že hlavní název služby (SPN) byl úspěšně přidán.

Nyní můžete použít hlavní název služby (Aplikace Azure AD s ID objektu) k automatickému přístupu k rozhraním API EA. Hlavní název služby (SPN) má roli EnrollmentReader.

## <a name="assign-the-department-reader-role-to-the-spn"></a>Přiřazení role čtenáře oddělení k hlavnímu názvu služby

Než začnete, přečtěte si článek [přiřazení rolí oddělení registrace – Put](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put) REST API.

Při čtení článku vyberte možnost **vyzkoušet**.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Snímek obrazovky s možností vyzkoušet si v článku přiřazení role registračního oddělení" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Přihlaste se pomocí svého účtu do tenanta, který má přístup k registraci, ke které chcete přiřadit přístup.

Jako součást požadavku rozhraní API zadejte následující parametry.

**billingAccountName**

Toto je ID fakturačního účtu. V Azure Portal najdete na stránce Přehled Cost Management + fakturace.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Snímek obrazovky zobrazující ID fakturačního účtu" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

Parametr je jedinečný identifikátor GUID, který je třeba zadat. Identifikátor GUID můžete vygenerovat pomocí příkazu [New-GUID](/powershell/module/microsoft.powershell.utility/new-guid?view=powershell-7.1&preserve-view=true) PowerShell.

Nebo můžete k vygenerování jedinečného identifikátoru GUID použít web [GUID a generátor identifikátoru UUID online](https://guidgenerator.com/) .

**departmentName**

Toto je ID oddělení. ID oddělení můžete zobrazit v Azure Portal. Přejděte na Cost Management a fakturační > **oddělení**.

V tomto příkladu jsme použili oddělení ACE. ID pro příklad je `84819` .

:::image type="content" source="./media/assign-roles-azure-service-principals/department-id.png" alt-text="Snímek obrazovky zobrazující ukázkové ID oddělení" lightbox="./media/assign-roles-azure-service-principals/department-id.png" :::

**verze API-Version**

Použijte verzi **2019-10-01-Preview** .

Tělo žádosti obsahuje kód JSON, který je třeba použít.

Použijte ukázku [přiřazení role oddělení registrace – Put](/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put). Existují tři parametry, které je třeba použít jako součást formátu JSON.

| Parametr | Kde ji najít |
| --- | --- |
| Properties. principalId | Viz [najít hlavní název služby (SPN) a ID tenanta](#find-your-spn-and-tenant-id). |
| Properties. principalTenantId | Viz [najít hlavní název služby (SPN) a ID tenanta](#find-your-spn-and-tenant-id). |
| Properties. roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/db609904-a47f-4794-9be8-9bd86fbffd8a" |

Název fakturačního účtu je stejný parametr, který jste použili v parametrech rozhraní API. Je to ID registrace, které vidíte na portálu EA a Azure Portal.

ID definice fakturační role `db609904-a47f-4794-9be8-9bd86fbffd8a` je pro čtenáře oddělení.

Vyberte **Spustit** a spusťte příkaz.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" alt-text="Snímek obrazovky se vzorovými přiřazeními rolí registračního oddělení – umístěte REST a vyzkoušejte si příklady informací, které jsou připravené ke spuštění." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" :::

`200 OK`Odpověď ukazuje, že hlavní název služby (SPN) byl úspěšně přidán.

Nyní můžete použít hlavní název služby (Aplikace Azure AD s ID objektu) k automatickému přístupu k rozhraním API EA. Hlavní název služby (SPN) má roli DepartmentReader.

## <a name="assign-the-subscription-creator-role-to-the-spn"></a>Přiřazení role tvůrce předplatného k hlavnímu názvu služby

Přečtěte si článek [přiřazení rolí účtu pro zápis – Put](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) .

Při čtení vyberte **zkusit** pro přiřazení role tvůrce předplatného k hlavnímu názvu služby (SPN).

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Snímek obrazovky s možností vyzkoušet si v článku přiřazení rolí účtu pro zápis" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Přihlaste se pomocí svého účtu do tenanta, který má přístup k registraci, ke které chcete přiřadit přístup.

Jako součást požadavku rozhraní API zadejte následující parametry. Přečtěte si článek v tématu [přiřazení rolí účtu registrace – parametry Put-URI](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put#uri-parameters).

**billingAccountName**

Parametr je ID fakturačního účtu. V Azure Portal najdete na stránce Přehled Cost Management + fakturace.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Snímek obrazovky zobrazující ID fakturačního účtu" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

Parametr je jedinečný identifikátor GUID, který je třeba zadat. Identifikátor GUID můžete vygenerovat pomocí příkazu [New-GUID](/powershell/module/microsoft.powershell.utility/new-guid?view=powershell-7.1&preserve-view=true) PowerShell.

Nebo můžete k vygenerování jedinečného identifikátoru GUID použít web [GUID a generátor identifikátoru UUID online](https://guidgenerator.com/) .
**enrollmentAccountName**

Parametr je ID účtu. Vyhledejte ID účtu pro název účtu v Azure Portal v Cost Management + fakturace v oboru registrace a oddělení.

V tomto příkladu jsme použili testovací účet GTM. ID je `196987` .

:::image type="content" source="./media/assign-roles-azure-service-principals/account-id.png" alt-text="Snímek obrazovky zobrazující ID účtu" lightbox="./media/assign-roles-azure-service-principals/account-id.png" :::

**verze API-Version**

Použijte verzi **2019-10-01-Preview** .

Tělo žádosti obsahuje kód JSON, který je třeba použít.

Použijte ukázku [přiřazení role oddělení registrace – Příklady operací Put](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put#putenrollmentdepartmentadministratorroleassignment).

Existují tři parametry, které je třeba použít jako součást formátu JSON.

| Parametr | Kde ji najít |
| --- | --- |
| Properties. principalId | Viz [najít hlavní název služby (SPN) a ID tenanta](#find-your-spn-and-tenant-id). |
| Properties. principalTenantId | Viz [najít hlavní název služby (SPN) a ID tenanta](#find-your-spn-and-tenant-id). |
| Properties. roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountID}/enrollmentAccounts/196987/billingRoleDefinitions/a0bcee42-bf30-4d1b-926a-48d21664ef71" |

Název fakturačního účtu je stejný parametr, který jste použili v parametrech rozhraní API. Je to ID registrace, které vidíte na portálu EA a Azure Portal.

ID definice fakturační role `a0bcee42-bf30-4d1b-926a-48d21664ef71` je pro roli tvůrce předplatného.

Vyberte **Spustit** a spusťte příkaz.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" alt-text="Snímek obrazovky s možností vyzkoušet v přiřazení role účtu pro zápis – článek o tom, kam" lightbox="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" :::

`200 OK`Odpověď ukazuje, že hlavní název služby (SPN) byl úspěšně přidán.

Nyní můžete použít hlavní název služby (Aplikace Azure AD s ID objektu) k automatickému přístupu k rozhraním API EA. Hlavní název služby (SPN) má roli SubscriptionCreator.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [správě portálu Azure EA](ea-portal-administration.md).