---
title: Ověřování pomocí spravovaných identit
description: Přístup k prostředkům chráněným pomocí Azure Active Directory bez přihlašování pomocí přihlašovacích údajů nebo tajných kódů pomocí spravované identity
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: article
ms.date: 01/15/2021
ms.openlocfilehash: 9ac8a23569d9a85787768419a0377967026e9bd9
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251563"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Ověřování přístupu k prostředkům Azure pomocí spravovaných identit v Azure Logic Apps

Pro snadný přístup k dalším prostředkům, které jsou chráněné Azure Active Directory (Azure AD) a ověřují vaši identitu, může vaše aplikace logiky používat [spravovanou identitu](../active-directory/managed-identities-azure-resources/overview.md) (dřív identita spravované služby nebo MSI) místo přihlašovacích údajů, tajných kódů nebo tokenů Azure AD. Azure tuto identitu spravuje za vás a pomáhá zabezpečit vaše přihlašovací údaje, protože nemusíte spravovat tajné klíče ani přímo používat tokeny Azure AD.

Azure Logic Apps podporuje spravované identity přiřazené [*systémem*](../active-directory/managed-identities-azure-resources/overview.md) i [*uživatelem*](../active-directory/managed-identities-azure-resources/overview.md) . Vaše aplikace logiky nebo jednotlivá připojení můžou používat identitu přiřazenou systémem nebo *jedinou* identitu přiřazenou uživatelem, kterou můžete sdílet přes skupinu aplikací logiky, ale ne obojí.

## <a name="where-can-logic-apps-use-managed-identities"></a>Kde můžou Logic Apps používat spravované identity?

V současné době můžou k ověřování používat spravovanou identitu jenom [konkrétní integrované triggery a akce](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) a [konkrétní spravované konektory](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) , které podporují Azure AD OAuth. Tady je příklad výběru:

**Předdefinované triggery a akce**

* Azure API Management
* Azure App Services
* Azure Functions
* HTTP
* HTTP + Webhook

**Spravované konektory**

* Azure Automation
* Azure Event Grid
* Azure Key Vault
* Protokoly služby Azure Monitor
* Azure Resource Manager
* HTTP s Azure AD

Podpora pro spravované konektory je momentálně ve verzi Preview. Aktuální seznam naleznete v tématu [typy ověřování pro aktivační události a akce, které podporují ověřování](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

Tento článek popisuje, jak pro vaši aplikaci logiky nastavit oba druhy spravovaných identit. Další informace najdete v těchto tématech:

* [Aktivační události a akce, které podporují spravované identity](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)
* [Omezení spravovaných identit pro Logic Apps](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Služby Azure, které podporují ověřování Azure AD se spravovanými identitami](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Předpoklady

* Účet a předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). Spravovaná identita i cílový prostředek Azure, ke kterému potřebujete přístup, musí používat stejné předplatné Azure.

* Pokud chcete spravované identitě udělit přístup k prostředku Azure, musíte pro tuto identitu přidat roli k cílovému prostředku. K přidání rolí potřebujete [oprávnění správce Azure AD](../active-directory/roles/permissions-reference.md) , která můžou přiřadit role k identitám v příslušném TENANTOVI Azure AD.

* Cílový prostředek Azure, ke kterému chcete získat přístup. Na tomto prostředku přidáte roli pro spravovanou identitu, která pomůže aplikaci logiky ověřit přístup k cílovému prostředku.

* Aplikace logiky, ve které chcete použít [aktivační událost nebo akce, které podporují spravované identity](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

## <a name="enable-managed-identity"></a>Povolení spravované identity

Chcete-li nastavit spravovanou identitu, kterou chcete použít, použijte odkaz na tuto identitu:

* [Identita přiřazená systémem](#system-assigned)
* [Identita přiřazená uživatelem](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Povolit identitu přiřazenou systémem

Na rozdíl od uživatelsky přiřazených identit nemusíte ručně vytvářet identitu přiřazenou systémem. Chcete-li nastavit identitu přiřazenou systémem pro vaši aplikaci logiky, můžete použít následující možnosti:

* [Azure Portal](#azure-portal-system-logic-app)
* [Šablony Azure Resource Manageru](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Povolit identitu přiřazenou systémem v Azure Portal

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V nabídce aplikace logiky v části **Nastavení** vyberte **Identita**. Vyberte možnost **systém byl přiřazen**  >  **při**  >  **uložení**. Až Azure zobrazí výzvu k potvrzení, vyberte **Ano**.

   ![Povolit identitu přiřazenou systémem](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Pokud se zobrazí chyba, že můžete mít jenom jednu spravovanou identitu, vaše aplikace logiky je už přidružená k identitě přiřazené uživatelem. Než budete moct přidat identitu přiřazenou systémem, musíte nejdřív z vaší aplikace logiky *Odebrat* uživatelem přiřazenou identitu.

   Vaše aplikace logiky teď může používat identitu přiřazenou systémem, která je zaregistrovaná ve službě Azure AD, a je reprezentovaná ID objektu.

   ![ID objektu pro identitu přiřazenou systémem](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **ID objektu** | <*Identita-Resource-ID*> | Globálně jedinečný identifikátor (GUID), který představuje identitu přiřazenou systémem pro vaši aplikaci logiky v tenantovi Azure AD. |
   ||||

1. Nyní postupujte podle [kroků, které této identitě přiřadí přístup k prostředku](#access-other-resources) dále v tomto tématu.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Povolit identitu přiřazenou systémem v šabloně Azure Resource Manager

K automatizaci vytváření a nasazování prostředků Azure, jako jsou Logic Apps, můžete použít [šablony Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Pokud chcete pro vaši aplikaci logiky v šabloně povolit spravovanou identitu přiřazenou systémem, přidejte do `identity` `type` definice prostředků aplikace logiky v šabloně objekt a podřízenou vlastnost, například:

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Když Azure vytvoří definici prostředků aplikace logiky, `identity` objekt získá tyto další vlastnosti:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Property (JSON) | Hodnota | Popis |
|-----------------|-------|-------------|
| `principalId` | <*ID objektu zabezpečení*> | Globálně jedinečný identifikátor (GUID) instančního objektu služby pro spravovanou identitu, která představuje vaši aplikaci logiky v tenantovi Azure AD. Tento identifikátor GUID se někdy zobrazuje jako "ID objektu" nebo `objectID` . |
| `tenantId` | <*Azure-AD-tenant-ID*> | Globálně jedinečný identifikátor (GUID), který představuje tenanta Azure AD, ve kterém je teď aplikace logiky členem. V tenantovi služby Azure AD má instanční objekt stejný název jako instance aplikace logiky. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Povolit identitu přiřazenou uživatelem

Pokud chcete pro vaši aplikaci logiky nastavit spravovanou identitu přiřazenou uživatelem, musíte nejdřív vytvořit tuto identitu jako samostatný samostatný prostředek Azure. Tady jsou možnosti, které můžete použít:

* [Azure Portal](#azure-portal-user-identity)
* [Šablony Azure Resource Manageru](#template-user-identity)
* Azure PowerShell
  * [Vytvoření uživatelsky přiřazené identity](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Přidat přiřazení role](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [Vytvoření uživatelsky přiřazené identity](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Přidat přiřazení role](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Azure REST API
  * [Vytvoření uživatelsky přiřazené identity](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Přidat přiřazení role](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Vytvoření uživatelsky přiřazené identity v Azure Portal

1. V [Azure Portal](https://portal.azure.com)do vyhledávacího pole na libovolné stránce zadejte `managed identities` a vyberte **spravované identity**.

   ![Najde a vybere spravované identity.](./media/create-managed-service-identity/find-select-managed-identities.png)

1. V části **spravované identity** vyberte **Přidat**.

   ![Přidat novou spravovanou identitu](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Zadejte informace o spravované identitě a pak vyberte **zkontrolovat + vytvořit**, například:

   ![Vytvoření spravované identity přiřazené uživatelem](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Předplatné** | Ano | <*Azure – předplatné – název*> | Název předplatného Azure, které se má použít |
   | **Skupina prostředků** | Ano | <*Azure-Resource-Group-Name*> | Název skupiny prostředků, která se má použít. Vytvořte novou skupinu nebo vyberte existující skupinu. Tento příklad vytvoří novou skupinu s názvem `fabrikam-managed-identities-RG` . |
   | **Oblast** | Ano | <*Oblast Azure*> | Oblast Azure, ve které se mají ukládat informace o prostředku V tomto příkladu se používá "Západní USA". |
   | **Název** | Ano | <*uživatelsky přiřazené-identity-Name*> | Název, kterému chcete přiřadit identitu přiřazenou uživatelem. Tento příklad používá `Fabrikam-user-assigned-identity`. |
   |||||

   Po ověření těchto údajů Azure vytvoří spravovanou identitu. Nyní můžete do aplikace logiky přidat identitu přiřazenou uživatelem. Do aplikace logiky nemůžete přidat více než jednu identitu přiřazenou uživatelem.

1. V Azure Portal vyhledejte a otevřete aplikaci logiky v návrháři aplikace logiky.

1. V nabídce aplikace logiky v části **Nastavení** vyberte **Identita** a pak vyberte **přiřadit uživatele**  >  **Přidat**.

   ![Přidat spravovanou identitu přiřazenou uživatelem](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. V podokně **Přidat spravovanou identitu přiřazenou uživateli** vyberte v seznamu **předplatné** předplatné Azure, pokud ještě není vybrané. V seznamu, který zobrazuje *všechny* spravované identity v tomto předplatném, najděte a vyberte identitu přiřazenou uživateli, kterou chcete. Chcete-li filtrovat seznam, zadejte do vyhledávacího pole **přiřazené identity spravované uživatelem** název identity nebo skupiny prostředků. Až budete hotovi, vyberte **Přidat**.

   ![Vyberte identitu přiřazenou uživatelem, kterou chcete použít.](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Pokud se zobrazí chyba, že můžete mít jenom jednu spravovanou identitu, vaše aplikace logiky je už přidružená k identitě přiřazené systémem. Než budete moct přidat identitu přiřazenou uživatelem, musíte nejdřív v aplikaci logiky zakázat identitu přiřazenou systémem.

   Vaše aplikace logiky je teď přidružená k spravované identitě přiřazené uživatelem.

   ![Přidružení s uživatelem přiřazenou identitou](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Nyní postupujte podle [kroků, které této identitě přiřadí přístup k prostředku](#access-other-resources) dále v tomto tématu.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Vytvoření uživatelsky přiřazené identity v šabloně Azure Resource Manager

K automatizaci vytváření a nasazování prostředků Azure, jako jsou Logic Apps, můžete použít [Azure Resource Manager šablony](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), které podporují [identity přiřazené uživateli pro ověřování](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md). V `resources` oddílu šablony vyžaduje definice prostředků vaší aplikace logiky tyto položky:

* `identity`Objekt s `type` vlastností nastavenou na`UserAssigned`

* Podřízený `userAssignedIdentities` objekt, který určuje prostředek a název přiřazený uživateli

Tento příklad ukazuje definici prostředků aplikace logiky pro požadavek HTTP PUT a obsahuje neparametrizovaný `identity` objekt. Odpověď na požadavek PUT a následná operace GET má také tento `identity` objekt:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

Pokud šablona obsahuje také definici prostředků spravované identity, můžete `identity` objekt parametrizovat. Tento příklad ukazuje, jak podřízený `userAssignedIdentities` objekt odkazuje na `userAssignedIdentity` proměnnou, kterou definujete v oddílu šablony `variables` . Tato proměnná odkazuje na ID prostředku pro vaši identitu přiřazenou uživatelem.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {}
      }
  ]
}
```

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Udělení přístupu k prostředkům v identitě

Než budete moct použít spravovanou identitu vaší aplikace logiky k ověřování, nastavte přístup k této identitě v prostředku Azure, ve kterém chcete identitu používat. K dokončení této úlohy přiřaďte příslušné roli k této identitě v cílovém prostředku Azure. Tady jsou možnosti, které můžete použít:

* [Azure Portal](#azure-portal-assign-access)
* [Šablona Azure Resource Manageru](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)) – Další informace najdete v tématu [Přidání přiřazení role pomocí Azure RBAC a Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Azure CLI ([AZ role Assignment Create](/cli/azure/role/assignment?view=azure-cli-latest&preserve-view=true#az-role-assignment-create)) – Další informace najdete v tématu [Přidání přiřazení role pomocí Azure RBAC a Azure CLI](../role-based-access-control/role-assignments-cli.md).
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Přiřadit přístup v Azure Portal

V cílovém prostředku Azure, ke kterému má mít spravovaná identita přístup, poskytněte tomuto prostředku přístup na základě role identity k cílovému prostředku.

1. V [Azure Portal](https://portal.azure.com)přejděte do prostředku Azure, ke kterému chcete mít přístup ke spravované identitě.

1. V nabídce prostředku vyberte přiřazení rolí **řízení přístupu (IAM)**,  >   kde můžete zkontrolovat aktuální přiřazení rolí pro daný prostředek. Na panelu nástrojů vyberte **Přidat**  >  **Přidat přiřazení role**.

   ![Vyberte Přidat > přidat přiřazení role.](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Pokud je možnost **Přidat přiřazení role** zakázaná, pravděpodobně nemáte oprávnění. Další informace o oprávněních, která umožňují spravovat role pro prostředky, najdete v tématu [oprávnění role správce v Azure Active Directory](../active-directory/roles/permissions-reference.md).

1. V části **Přidat přiřazení role** vyberte **roli** , která poskytuje vaší identitě potřebný přístup k cílovému prostředku.

   V tomto tématu musí vaše identita mít [roli, která má přístup k objektu BLOB v kontejneru Azure Storage](../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights), takže vyberte roli **Přispěvatel dat objektů BLOB úložiště** pro spravovanou identitu.

   ![Vyberte roli Přispěvatel dat objektů BLOB úložiště.](./media/create-managed-service-identity/select-role-for-identity.png)

1. Pro spravovanou identitu použijte tento postup:

   * **Identita přiřazená systémem**

     1. V poli **přiřadit přístup k** vyberte **Aplikace logiky**. Když se zobrazí vlastnost **předplatné** , vyberte předplatné Azure, které je přidružené k vaší identitě.

        ![Vybrat přístup pro identitu přiřazenou systémem](./media/create-managed-service-identity/assign-access-system.png)

     1. V poli **Vybrat** vyberte ze seznamu aplikaci logiky. Pokud je seznam příliš dlouhý, použijte pole **Vybrat** k filtrování seznamu.

        ![Vyberte aplikaci logiky pro systémově přiřazenou identitu.](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Identita přiřazená uživatelem**

     1. V poli **přiřadit přístup k** vyberte **spravovaná identita přiřazená uživatelem**. Když se zobrazí vlastnost **předplatné** , vyberte předplatné Azure, které je přidružené k vaší identitě.

        ![Vybrat přístup pro uživatelem přiřazenou identitu](./media/create-managed-service-identity/assign-access-user.png)

     1. V **rozevíracím seznamu vyberte svoji** identitu ze seznamu. Pokud je seznam příliš dlouhý, použijte pole **Vybrat** k filtrování seznamu.

        ![Vyberte vaši uživatelem přiřazenou identitu.](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Jakmile budete mít hotovo, vyberte **Uložit**.

   V seznamu přiřazení role cílového prostředku se teď zobrazuje vybraná spravovaná identita a role. Tento příklad ukazuje, jak můžete použít identitu přiřazenou systémem pro jednu aplikaci logiky a uživatelskou identitu pro skupinu jiných aplikací logiky.

   ![Do cílového prostředku se přidaly spravované identity a role.](./media/create-managed-service-identity/added-roles-for-identities.png)

   Další informace získáte, [když pomocí Azure Portal přiřadíte přístup spravované identity k prostředku](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

1. Nyní postupujte podle [pokynů k ověření přístupu k identitě](#authenticate-access-with-identity) v aktivační události nebo akci, která podporuje spravované identity.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Ověření přístupu pomocí spravované identity

Po [Povolení spravované identity pro vaši aplikaci logiky](#azure-portal-system-logic-app) a [udělení této identity přístup k cílovému prostředku nebo entitě](#access-other-resources)můžete tuto identitu použít v [aktivačních událostech a akcích, které podporují spravované identity](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

> [!IMPORTANT]
> Pokud máte funkci Azure, kde chcete používat identitu přiřazenou systémem, nejdřív [Povolte ověřování pro službu Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions).

Tyto kroky ukazují, jak používat spravovanou identitu s triggerem nebo akcí prostřednictvím Azure Portal. Pokud chcete určit spravovanou identitu v aktivační události nebo v základní definici JSON akce, přečtěte si téma [ověřování spravované identity](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. Pokud jste to ještě neudělali, přidejte [Trigger nebo akci, která podporuje spravované identity](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

   > [!NOTE]
   > Ne všechny triggery a akce podporují přidání typu ověřování. Další informace najdete v tématu [typy ověřování pro aktivační události a akce, které podporují ověřování](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. V aktivační události nebo akci, kterou jste přidali, postupujte takto:

   * **Integrované triggery a akce, které podporují použití spravované identity**

     1. Pokud se vlastnost ještě nezobrazuje, přidejte vlastnost **ověřování** .

     1. V části **typ ověřování** vyberte **spravovaná identita**.

     Další informace najdete v tématu [Příklad: ověření integrované aktivační události nebo akce se spravovanou identitou](#authenticate-built-in-managed-identity).
 
   * **Aktivační události spravovaného konektoru a akce, které podporují použití spravované identity**

     1. Na stránce Výběr tenanta vyberte **připojit se se spravovanou identitou**.

     1. Na další stránce zadejte název připojení.

        Ve výchozím nastavení se v seznamu spravovaných identit zobrazuje jenom aktuálně povolená spravovaná identita, protože aplikace logiky podporuje povolování jenom jedné spravované identity, například:

        ![Snímek obrazovky zobrazující stránku s názvem připojení a vybranou spravovanou identitou](./media/create-managed-service-identity/system-assigned-managed-identity.png)

     Další informace najdete v tématu [Příklad: ověření triggeru nebo akce spravovaného konektoru pomocí spravované identity](#authenticate-managed-connector-managed-identity).

     Připojení, která vytvoříte pro použití spravované identity, jsou speciální typ připojení, který funguje jenom se spravovanou identitou. V době běhu připojení používá spravovanou identitu, která je povolená na aplikaci logiky. Tato konfigurace je uložená v objektu definice prostředků aplikace logiky `parameters` , který obsahuje `$connections` odkazy na ID prostředků připojení spolu s ID prostředku identity, pokud je povolená identita přiřazená uživatelem.

     Tento příklad ukazuje, jak konfigurace vypadá, když aplikace logiky povoluje spravovanou identitu přiřazenou systémem:

     ```json
     "parameters": {
        "$connections": {
           "value": {
              "<action-name>": {
                 "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/connections/{connection-name}",
                 "connectionName": "{connection-name}",
                 "connectionProperties": {
                    "authentication": {
                       "type": "ManagedServiceIdentity"
                    }
                 },
                 "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
              }
           }
        }
     }
     ```

     Tento příklad ukazuje, jak konfigurace vypadá, když aplikace logiky povoluje uživatelsky přiřazenou spravovanou identitu:

     ```json
     "parameters": {
        "$connections": {
           "value": {
              "<action-name>": {
                 "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/connections/{connection-name}",
                 "connectionName": "{connection-name}",
                 "connectionProperties": {
                    "authentication": {
                       "identity": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/microsoft.managedidentity/userassignedidentities/{managed-identity-name}",
                       "type": "ManagedServiceIdentity"
                    }
                 },
                 "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
              }
           }
        }
     }
     ```

     Během běhu služba Logic Apps kontroluje, jestli se nějaké triggery spravovaného konektoru a akce v aplikaci logiky nastavily tak, aby používaly spravovanou identitu a jestli jsou všechna požadovaná oprávnění nastavená tak, aby používala spravovanou identitu pro přístup k cílovým prostředkům, které jsou určené triggerem a akcemi. V případě úspěchu služba Logic Apps načte token Azure AD, který je přidružený ke spravované identitě, a pomocí této identity ověří přístup k cílovému prostředku a provede nakonfigurovanou operaci v aktivační události a akce.

<a name="authenticate-built-in-managed-identity"></a>

#### <a name="example-authenticate-built-in-trigger-or-action-with-a-managed-identity"></a>Příklad: ověření integrované aktivační události nebo akce se spravovanou identitou

Aktivační událost nebo akce HTTP může používat identitu přiřazenou systémem, kterou jste povolili pro vaši aplikaci logiky. V obecném případě Trigger HTTP nebo akce tyto vlastnosti používá k určení prostředku nebo entity, ke které chcete získat přístup:

| Vlastnost | Povinné | Popis |
|----------|----------|-------------|
| **Metoda** | Ano | Metoda HTTP, kterou používá operace, kterou chcete spustit |
| **Identifikátor URI** | Ano | Adresa URL koncového bodu pro přístup k cílovému prostředku Azure nebo entitě. Syntaxe identifikátoru URI obvykle zahrnuje [ID prostředku](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) pro prostředek nebo službu Azure. |
| **Hlavičky** | Ne | Všechny hodnoty hlaviček, které potřebujete nebo chcete zahrnout do odchozího požadavku, jako je typ obsahu |
| **Dotazy** | Ne | Všechny parametry dotazů, které potřebujete nebo chcete zahrnout do žádosti, jako je například parametr konkrétní operace nebo verze rozhraní API pro operaci, kterou chcete spustit |
| **Authentication** | Ano | Typ ověřování, který se má použít pro ověřování přístupu k cílovému prostředku nebo entitě |
||||

Jako konkrétní příklad Předpokládejme, že chcete spustit [operaci Snapshot BLOB](/rest/api/storageservices/snapshot-blob) u objektu BLOB v účtu Azure Storage, kde jste předtím nastavili přístup k vaší identitě. Ale [konektor Azure Blob Storage](/connectors/azureblob/) v současnosti tuto operaci nenabízí. Místo toho můžete tuto operaci spustit pomocí [akce http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) nebo jiné [operace REST API služby BLOB Service](/rest/api/storageservices/operations-on-blobs).

> [!IMPORTANT]
> Pokud chcete získat přístup k účtům Azure Storage za brány firewall pomocí požadavků HTTP a spravovaných identit, ujistěte se, že jste také nastavili účet úložiště s [výjimkou, která umožňuje přístup důvěryhodnými službami Microsoftu](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

Pokud chcete spustit [operaci objektu BLOB snímku](/rest/api/storageservices/snapshot-blob), akce http určuje tyto vlastnosti:

| Vlastnost | Požaduje se | Příklad hodnoty | Popis |
|----------|----------|---------------|-------------|
| **Metoda** | Ano | `PUT`| Metoda HTTP, kterou používá operace objektu BLOB snímku |
| **Identifikátor URI** | Ano | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | ID prostředku pro soubor Azure Blob Storage v globálním (veřejném) prostředí Azure, které používá tuto syntaxi |
| **Hlavičky** | Pro Azure Storage | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` <p>`x-ms-date` = `@{formatDateTime(utcNow(),'r'}` | `x-ms-blob-type` `x-ms-version` Hodnoty hlaviček, a `x-ms-date` jsou požadovány pro operace Azure Storage. <p><p>**Důležité**: v odchozích triggerech http a požadavcích akcí pro Azure Storage hlavička vyžaduje `x-ms-version` vlastnost a verzi rozhraní API pro operaci, kterou chcete spustit. `x-ms-date`Musí být aktuální datum. V opačném případě vaše aplikace logiky selže s `403 FORBIDDEN` chybou. Chcete-li získat aktuální datum v požadovaném formátu, můžete použít výraz v příkladu hodnoty. <p>Další informace najdete v těchto tématech: <p><p>- [Hlavičky žádosti – objekt BLOB snímku](/rest/api/storageservices/snapshot-blob#request) <br>- [Správa verzí pro služby Azure Storage Services](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
| **Dotazy** | Pouze pro operaci objektu BLOB snímku | `comp` = `snapshot` | Název a hodnota parametru dotazu pro operaci. |
|||||

Tady je příklad akce HTTP, která zobrazuje všechny tyto hodnoty vlastností:

![Přidání akce HTTP pro přístup k prostředku Azure](./media/create-managed-service-identity/http-action-example.png)

1. Po přidání akce HTTP přidejte do akce HTTP vlastnost **ověřování** . V seznamu **Přidat nový parametr** vyberte možnost **ověřování**.

   ![Přidat vlastnost ověřování do akce HTTP](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Ne všechny triggery a akce podporují přidání typu ověřování. Další informace najdete v tématu [typy ověřování pro aktivační události a akce, které podporují ověřování](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. V seznamu **typ ověřování** vyberte **spravovaná identita**.

   ![U možnosti ověřování vyberte spravovaná identita.](./media/create-managed-service-identity/select-managed-identity.png)

1. Ze seznamu spravovaných identit vyberte z dostupných možností v závislosti na vašem scénáři.

   * Pokud jste nastavili identitu přiřazenou systémem, vyberte možnost **spravovaná identita přiřazená systémem** , pokud ještě není vybraná.

     ![Vyberte spravovanou identitu přiřazenou systémem.](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Pokud jste nastavili identitu přiřazenou uživatelem, vyberte tuto identitu, pokud ještě není vybraná.

     ![Vyberte identitu přiřazenou uživatelem.](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Tento příklad pokračuje pomocí **spravované identity přiřazené systémem**.

1. U některých triggerů a akcí se také zobrazí vlastnost **cílová skupina** , ve které můžete nastavit ID cílového prostředku. Nastavte vlastnost **cílová skupina** na [ID prostředku pro cílový prostředek nebo službu](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). Jinak ve výchozím nastavení vlastnost **cílová skupina** používá `https://management.azure.com/` ID prostředku, což je id prostředku pro Azure Resource Manager.

   > [!IMPORTANT]
   > Ujistěte se, že ID cílového prostředku *přesně odpovídá* hodnotě, kterou Azure Active Directory (AD) očekává, včetně požadovaných koncových lomítek. ID prostředku pro všechny účty Azure Blob Storage například vyžaduje koncové lomítko. ID prostředku pro konkrétní účet úložiště ale nevyžaduje koncové lomítko. Ověřte [ID prostředků služeb Azure, které podporují Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   V tomto příkladu se nastaví vlastnost **cílové skupiny** `https://storage.azure.com/` tak, aby přístupové tokeny používané pro ověřování byly platné pro všechny účty úložiště. Pro určitý účet úložiště ale taky můžete zadat adresu URL kořenové služby `https://fabrikamstorageaccount.blob.core.windows.net` .

   ![Nastavit vlastnost "cílová" na ID cílového prostředku](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Další informace o autorizaci přístupu ke službě Azure AD pro Azure Storage najdete v těchto tématech:

   * [Autorizace přístupu k Azure Blob a frontám pomocí Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Autorizovat přístup k Azure Storage pomocí Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Pokračujte v sestavování aplikace logiky způsobem, který požadujete.

<a name="authenticate-managed-connector-managed-identity"></a>

#### <a name="example-authenticate-managed-connector-trigger-or-action-with-a-managed-identity"></a>Příklad: ověření triggeru nebo akce spravovaného konektoru pomocí spravované identity

Akce Azure Resource Manager, **Přečtěte si prostředek**, může používat spravovanou identitu, kterou jste povolili pro vaši aplikaci logiky. Tento příklad ukazuje, jak používat spravovanou identitu přiřazenou systémem.

1. Po přidání akce do pracovního postupu vyberte na stránce Výběr tenanta **připojit se správou identity**.

   ![Snímek obrazovky, který zobrazuje Azure Resource Manager akci a možnost připojit ke spravované identitě](./media/create-managed-service-identity/select-connect-managed-identity.png)

   Akce teď zobrazuje stránku název připojení se seznamem spravovaných identit, která zahrnuje spravovaný typ identity, který je v aplikaci logiky aktuálně povolený.

1. Na stránce název připojení zadejte název připojení. Ze seznamu spravovaných identit vyberte spravovanou identitu, která je v tomto příkladu **přiřazená pomocí spravované identity** , a vyberte **vytvořit**. Pokud jste povolili spravovanou identitu přiřazenou uživatelem, vyberte místo toho identitu.

   ![Snímek obrazovky, který zobrazuje Azure Resource Manager akci se zadaným názvem připojení a vybraným systémem přiřazenou spravovanou identitou.](./media/create-managed-service-identity/system-assigned-managed-identity.png)

   Pokud spravovaná identita není povolená, při pokusu o vytvoření připojení se zobrazí následující chyba:

   *Musíte povolit spravovanou identitu pro vaši aplikaci logiky a potom udělit požadovaný přístup k identitě v cílovém prostředku.*

   ![Snímek obrazovky, který zobrazuje Azure Resource Manager akci s chybou, pokud není povolená žádná spravovaná identita.](./media/create-managed-service-identity/system-assigned-managed-identity-disabled.png)

1. Po úspěšném vytvoření připojení může Návrhář načíst všechny dynamické hodnoty, obsah nebo schéma pomocí spravovaného ověřování identity.

1. Pokračujte v sestavování aplikace logiky způsobem, který požadujete.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Zakázat spravovanou identitu

Pokud chcete přestat používat spravovanou identitu pro vaši aplikaci logiky, máte tyto možnosti:

* [Azure Portal](#azure-portal-disable)
* [Šablony Azure Resource Manageru](#template-disable)
* Azure PowerShell
  * [Odebrat přiřazení role](../role-based-access-control/role-assignments-powershell.md)
  * [Odstranění identity přiřazené uživatelem](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [Odebrat přiřazení role](../role-based-access-control/role-assignments-cli.md)
  * [Odstranění identity přiřazené uživatelem](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure REST API
  * [Odebrat přiřazení role](../role-based-access-control/role-assignments-rest.md)
  * [Odstranění identity přiřazené uživatelem](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Pokud odstraníte aplikaci logiky, Azure automaticky odebere spravovanou identitu ze služby Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Zakázat spravovanou identitu v Azure Portal

V Azure Portal nejprve odeberte přístup identity k [cílovému prostředku](#disable-identity-target-resource). V dalším kroku vypněte identitu přiřazenou systémem nebo z [vaší aplikace logiky](#disable-identity-logic-app)odeberte identitu přiřazenou uživatelem.

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Odebrání přístupu k identitám z prostředků

1. V [Azure Portal](https://portal.azure.com)přejděte do cílového prostředku Azure, ve kterém chcete odebrat přístup pro spravovanou identitu.

1. V nabídce cílového prostředku vyberte **řízení přístupu (IAM)**. Na panelu nástrojů vyberte **přiřazení rolí**.

1. V seznamu role vyberte spravované identity, které chcete odebrat. Na panelu nástrojů vyberte **Odebrat**.

   > [!TIP]
   > Pokud je možnost **Odebrat** zakázaná, pravděpodobně nemáte oprávnění. Další informace o oprávněních, která umožňují spravovat role pro prostředky, najdete v tématu [oprávnění role správce v Azure Active Directory](../active-directory/roles/permissions-reference.md).

Spravovaná identita je nyní odebrána a již nemá přístup k cílovému prostředku.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Zakázat spravovanou identitu v aplikaci logiky

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V nabídce aplikace logiky v části **Nastavení** vyberte **Identita** a pak postupujte podle pokynů pro vaši identitu:

   * Vyberte možnost **systém byl přiřazen**  >  **při**  >  **uložení**. Až Azure zobrazí výzvu k potvrzení, vyberte **Ano**.

     ![Zakažte identitu přiřazenou systémem.](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Vyberte **přiřazený uživatel** a spravovaná identita a pak vyberte **Odebrat**. Až Azure zobrazí výzvu k potvrzení, vyberte **Ano**.

     ![Odebrání uživatelsky přiřazené identity](./media/create-managed-service-identity/remove-user-assigned-identity.png)

Spravovaná identita je teď ve vaší aplikaci logiky zakázaná.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Zakázat spravovanou identitu v šabloně Azure Resource Manager

Pokud jste vytvořili spravovanou identitu aplikace logiky pomocí šablony Azure Resource Manager, nastavte `identity` `type` podřízenou vlastnost objektu na `None` .

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Další kroky

* [Zabezpečený přístup a data v Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
