---
title: Ověřování pomocí spravovaných identit
description: Přístup k prostředkům v jiných klientech Služby Azure Active Directory bez přihlášení pomocí přihlašovacích údajů nebo tajných klíčů pomocí spravované identity
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 82710a66cdf7874c745070e49b2c7aff7bc8816d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117285"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Ověřování přístupu k prostředkům Azure pomocí spravovaných identit v Azure Logic Apps

Chcete-li získat přístup k prostředkům v jiných klientech Služby Azure Active Directory (Azure AD) a ověřit vaši identitu bez přihlášení, vaše aplikace logiky můžete použít [spravovanou identitu](../active-directory/managed-identities-azure-resources/overview.md) (dříve identitu spravované služby nebo MSI), nikoli přihlašovací údaje nebo tajné klíče. Azure spravuje tuto identitu za vás a pomáhá zabezpečit vaše přihlašovací údaje, protože nemusíte poskytovat nebo otáčet tajné kódy.

Azure Logic Apps podporuje spravované identity [*přiřazené*](../active-directory/managed-identities-azure-resources/overview.md) i [*uživatelem.*](../active-directory/managed-identities-azure-resources/overview.md) Vaše aplikace logiky můžete použít buď systémem přiřazenou identitu nebo *jeden* uživatel přiřazenou identitu, kterou můžete sdílet ve skupině aplikací logiky, ale ne obojí. V současné době podporují spravované identity spravované identity, nikoli spravované konektory nebo připojení, pouze [konkrétní předdefinované aktivační události a akce:](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

* HTTP
* Azure Functions
* Azure API Management
* Azure App Services

Tento článek ukazuje, jak nastavit oba druhy spravovaných identit pro aplikaci logiky. Další informace najdete v těchto tématech:

* [Aktivační události a akce, které podporují spravované identity](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Podporované typy ověřování u odchozích volání](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Limity spravovaných identit pro aplikace logiky](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Služby Azure, které podporují ověřování Azure AD se spravovanými identitami](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). Spravovaná identita i cílový prostředek Azure, kde potřebujete přístup, musí používat stejné předplatné Azure.

* Chcete-li udělit přístup spravované identity k prostředku Azure, musíte přidat roli do cílového prostředku pro tuto identitu. Chcete-li přidat role, budete potřebovat [oprávnění správce Azure AD,](../active-directory/users-groups-roles/directory-assign-admin-roles.md) který můžete přiřadit role k identitám v odpovídajícím tenantovi Azure AD.

* Cílový prostředek Azure, ke kterému chcete získat přístup. Na tomto prostředku přidáte roli pro spravovanou identitu, která pomáhá aplikaci logiky ověřit přístup k cílovému prostředku.

* Aplikace logiky, kde chcete použít [aktivační událost nebo akce, které podporují spravované identity](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

## <a name="enable-managed-identity"></a>Povolení spravované identity

Chcete-li nastavit spravovanou identitu, kterou chcete použít, postupujte podle odkazu pro tuto identitu:

* [Systémem přiřazená identita](#system-assigned)
* [Identita přiřazená uživatelem](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Povolit identitu přiřazenou systémem

Na rozdíl od identit přiřazených uživatelem není nutné ručně vytvářet systémově přiřazenou identitu. Chcete-li nastavit systémem přiřazenou identitu pro aplikaci logiky, tady jsou možnosti, které můžete použít:

* [Portál Azure](#azure-portal-system-logic-app)
* [Šablony Azure Resource Manageru](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Povolení systémově přiřazené identity na webu Azure Portal

1. Na [webu Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v Návrháři aplikací logiky.

1. V nabídce aplikace logiky vyberte v části **Nastavení** **položku Identita**. Vyberte **možnost Systém přiřazený** > **při** > **uložení**. Až vás Azure vyzve k potvrzení, vyberte **Ano**.

   ![Povolení systémově přiřazené identity](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Pokud se zobrazí chyba, že můžete mít pouze jednu spravovanou identitu, vaše aplikace logiky je již přidružena k identitě přiřazené uživateli. Před přidáním systémem přiřazené identity, musíte *nejprve odebrat* uživatelem přiřazenou identitu z aplikace logiky.

   Vaše aplikace logiky teď můžete použít systémem přiřazenou identitu, která je registrovaná ve službě Azure Active Directory a je reprezentována ID objektu.

   ![ID objektu pro systémově přiřazenou identitu](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **ID objektu** | <*identity-resource-ID*> | Globálně jedinečný identifikátor (GUID), který představuje systémem přiřazenou identitu pro vaši aplikaci logiky v tenantovi Azure AD |
   ||||

1. Nyní postupujte podle [kroků, které poskytují tuto identitu přístup k prostředku](#access-other-resources) dále v tomto tématu.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Povolení systémově přiřazené identity v šabloně Azure Resource Manager

K automatizaci vytváření a nasazování prostředků Azure, jako jsou aplikace [logiky,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)můžete použít šablony Azure Resource Manageru . Chcete-li povolit systémem přiřazenou spravovanou identitu `identity` pro aplikaci logiky v šabloně, přidejte objekt a podřízenou `type` vlastnost do definice prostředku aplikace logiky v šabloně, například:

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

Když Azure vytvoří definici prostředku `identity` aplikace logiky, objekt získá tyto další vlastnosti:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Vlastnost (JSON) | Hodnota | Popis |
|-----------------|-------|-------------|
| `principalId` | <*hlavní ID*> | Globálně jedinečný identifikátor (GUID) objektu instančního objektu pro spravovanou identitu, který představuje vaši aplikaci logiky v tenantovi Azure AD. Tento identifikátor GUID se někdy zobrazuje `objectID`jako "ID objektu" nebo . |
| `tenantId` | <*ID klienta Azure-AD*> | Globálně jedinečný identifikátor (GUID), který představuje klienta Azure AD, kde je aplikace logiky nyní členem. Uvnitř klienta Azure AD instanční objekt má stejný název jako instance aplikace logiky. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Povolit identitu přiřazenou uživateli

Chcete-li nastavit uživatelem přiřazenou spravovanou identitu pro vaši aplikaci logiky, musíte tuto identitu nejprve vytvořit jako samostatný prostředek Azure. Zde jsou možnosti, které můžete použít:

* [Portál Azure](#azure-portal-user-identity)
* [Šablony Azure Resource Manageru](#template-user-identity)
* Azure PowerShell
  * [Vytvořit identitu přiřazenou uživateli](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Přidat přiřazení role](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [Vytvořit identitu přiřazenou uživateli](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Přidat přiřazení role](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Azure REST API
  * [Vytvořit identitu přiřazenou uživateli](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Přidat přiřazení role](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Vytvoření identity přiřazené uživateli na webu Azure Portal

1. Na [webu Azure Portal](https://portal.azure.com)do vyhledávacího pole `managed identities`na libovolné stránce zadejte a vyberte **Spravované identity**.

   ![Vyhledání a výběr "Spravované identity"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. V části **Spravované identity**vyberte **Přidat**.

   ![Přidání nové spravované identity](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Zadejte informace o spravované identitě a pak vyberte **Vytvořit**, například:

   ![Vytvořit spravovanou identitu přiřazenou uživateli](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název prostředku** | Ano | <*název přiřazené uživateli*> | Název, který má být přiřazen k uživateli. Tento příklad používá "Fabrikam-user-assigned-identity". |
   | **Předplatné** | Ano | <*Název předplatného Azure*> | Název předplatného Azure, které má být používáno |
   | **Skupina prostředků** | Ano | <*Název skupiny Azure-resource-group*> | Název skupiny prostředků, který má být používán. Vytvořte novou skupinu nebo vyberte existující skupinu. Tento příklad vytvoří novou skupinu s názvem "fabrikam spravované identity-RG". |
   | **Umístění** | Ano | <*Oblast Azure*> | Oblast Azure, kde chcete ukládat informace o vašem prostředku. Tento příklad používá "Západní USA". |
   |||||

   Teď můžete přidat uživatelem přiřazenou identitu do aplikace logiky. Do aplikace logiky nelze přidat více než jednu identitu přiřazenou uživateli.

1. Na webu Azure Portal najděte a otevřete aplikaci logiky v Návrháři aplikací logiky.

1. V nabídce aplikace logiky vyberte v části **Nastavení** **položku Identita**a pak vyberte **Možnost Přidat přiřazenou** > možnost Přidat uživatele **.**

   ![Přidání spravované identity přiřazené uživateli](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. V podokně **Přidat uživatele přiřazené spravované identitě** vyberte ze seznamu **Předplatná** předplatné Azure, pokud už není vybrané. Ze seznamu, který zobrazuje *všechny* spravované identity v tomto předplatném, vyhledejte a vyberte požadovanou identitu přiřazenou uživatelem. Chcete-li seznam filtrovat, zadejte do vyhledávacího pole **Uživatele přiřazené spravované identity** název identity nebo skupiny prostředků. Až budete hotovi, vyberte **Přidat**.

   ![Vyberte identitu přiřazenou uživateli, kterou chcete použít.](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Pokud se zobrazí chyba, že můžete mít pouze jednu spravovanou identitu, vaše aplikace logiky je již přidružena k identitě přiřazené systému. Před přidáním uživatelem přiřazené identity, musíte nejprve zakázat systémem přiřazenou identitu v aplikaci logiky.

   Vaše aplikace logiky je teď přidružená k spravované identitě přiřazené uživateli.

   ![Přidružení k identitě přiřazené uživateli](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Nyní postupujte podle [kroků, které poskytují tuto identitu přístup k prostředku](#access-other-resources) dále v tomto tématu.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Vytvoření identity přiřazené uživateli v šabloně Správce prostředků Azure

K automatizaci vytváření a nasazování prostředků Azure, jako jsou aplikace [logiky,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)můžete použít šablony Azure Resource Manageru , které podporují [uživatelem přiřazené identity pro ověřování](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md). V `resources` části šablony vyžaduje definice prostředků aplikace logiky tyto položky:

* Objekt `identity` s `type` vlastností nastavenou na`UserAssigned`

* Podřízený `userAssignedIdentities` objekt, který určuje ID prostředku identity, což je `principalId` jiný `clientId` podřízený objekt, který má vlastnosti a

Tento příklad ukazuje definici prostředku aplikace logiky pro požadavek `identity` HTTP PUT a obsahuje neparametrizovaný objekt. Odpověď na požadavek PUT a následnou `identity` operaci GET mají také tento objekt:

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
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {
                  "principalId": "<principal-ID>",
                  "clientId": "<client-ID>"
               }
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

| Vlastnost (JSON) | Hodnota | Popis |
|-----------------|-------|-------------|
| `principalId` | <*hlavní ID*> | Globálně jedinečný identifikátor (GUID) pro uživatelem přiřazenou spravovanou identitu v tenantovi Azure AD |
| `clientId` | <*ID klienta*> | Globálně jedinečný identifikátor (GUID) pro novou identitu aplikace logiky, který se používá pro volání za běhu |
||||

Pokud vaše šablona obsahuje také definici prostředku spravované identity, můžete `identity` parametrizovat objekt. Tento příklad ukazuje, `userAssignedIdentities` jak podřízený objekt odkazuje na proměnnou, `userAssignedIdentity` kterou definujete v `variables` části šablony. Tato proměnná odkazuje na ID prostředku pro identitu přiřazenou uživateli.

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
         "properties": {
            "tenantId": "<tenant-ID>",
            "principalId": "<principal-ID>",
            "clientId": "<client-ID>"
         }
      }
  ]
}
```

| Vlastnost (JSON) | Hodnota | Popis |
|-----------------|-------|-------------|
| `tenantId` | <*ID klienta Azure-AD*> | Globálně jedinečný identifikátor (GUID), který představuje klienta Azure AD, kde je nyní členem identita přiřazená uživatelem. Uvnitř klienta Azure AD má instanční objekt stejný název jako název identity přiřazený uživateli. |
| `principalId` | <*hlavní ID*> | Globálně jedinečný identifikátor (GUID) pro uživatelem přiřazenou spravovanou identitu v tenantovi Azure AD |
| `clientId` | <*ID klienta*> | Globálně jedinečný identifikátor (GUID) pro novou identitu aplikace logiky, který se používá pro volání za běhu |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Poskytnutí přístupu k identitě k prostředkům

Než budete moci použít spravovanou identitu aplikace logiky pro ověřování, nastavte přístup pro tuto identitu na prostředek Azure, kde plánujete použít identitu. Chcete-li dokončit tento úkol, přiřaďte příslušnou roli této identity na cílovém prostředku Azure. Zde jsou možnosti, které můžete použít:

* [Portál Azure](#azure-portal-assign-access)
* [Šablona Azure Resource Manageru](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)) – další informace najdete v tématu [Přidání přiřazení role pomocí Azure RBAC a Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Azure CLI ([az role přiřazení vytvořit](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)) – další informace naleznete v [tématu přidání přiřazení role pomocí Azure RBAC a Azure CLI](../role-based-access-control/role-assignments-cli.md).
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Přiřazení přístupu na webu Azure Portal

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na prostředek Azure, kde chcete mít přístup ke spravované identitě.

1. V nabídce zdroje vyberte**přiřazení rolí** **řízení přístupu (IAM),** > kde můžete zkontrolovat aktuální přiřazení rolí pro tento zdroj. Na panelu nástrojů vyberte **Přidat** > **přiřazení role**.

   ![Vyberte "Přidat" > "Přidat přiřazení role"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Pokud je možnost **Přidat přiřazení role** zakázána, pravděpodobně nemáte oprávnění. Další informace o oprávněních, která umožňují spravovat role prostředků, najdete [v tématu Oprávnění rolí správce ve službě Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. V části **Přidat přiřazení role**vyberte **roli,** která vaší identitě poskytuje potřebný přístup k cílovému prostředku.

   V příkladu tohoto tématu vaše identita potřebuje [roli, která může přistupovat k objektu blob v kontejneru úložiště Azure](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

   ![Vyberte roli "Přispěvatel dat objektů blob úložiště"](./media/create-managed-service-identity/select-role-for-identity.png)

1. Postupujte podle následujících kroků pro spravovanou identitu:

   * **Systémem přiřazená identita**

     1. V poli **Přiřadit přístup** vyberte **Aplikace logiky**. Když se zobrazí vlastnost **Subscription,** vyberte předplatné Azure, které je přidruženo k vaší identitě.

        ![Vybrat přístup pro systémově přiřazenou identitu](./media/create-managed-service-identity/assign-access-system.png)

     1. V části **Vybrat** vyberte aplikaci logiky ze seznamu. Pokud je seznam příliš dlouhý, **vyfiltrujte** seznam pomocí pole Vybrat.

        ![Vybrat aplikaci logiky pro systémově přiřazenou identitu](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Identita přiřazená uživatelem**

     1. V poli **Přiřadit přístup vyberte** **Uživatel přiřazenou spravovanou identitu**. Když se zobrazí vlastnost **Subscription,** vyberte předplatné Azure, které je přidruženo k vaší identitě.

        ![Výběr přístupu pro identitu přiřazenou uživateli](./media/create-managed-service-identity/assign-access-user.png)

     1. V poli **Vybrat** vyberte ze seznamu svou identitu. Pokud je seznam příliš dlouhý, **vyfiltrujte** seznam pomocí pole Vybrat.

        ![Vyberte identitu přiřazenou uživateli](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Jakmile budete mít hotovo, vyberte **Uložit**.

   Seznam přiřazení rolí cílového zdroje nyní zobrazuje vybranou spravovanou identitu a roli. Tento příklad ukazuje, jak můžete použít systémem přiřazenou identitu pro jednu aplikaci logiky a uživatelem přiřazenou identitu pro skupinu jiných aplikací logiky.

   ![Přidány spravované identity a role pro cílovou aplikaci prostředku](./media/create-managed-service-identity/added-roles-for-identities.png)

   Další informace získáte [tak, že přístup ke spravované identitě k prostředku přiřadíte pomocí portálu Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

1. Nyní postupujte podle [pokynů k ověření přístupu s identitou](#authenticate-access-with-identity) v aktivační události nebo akci, která podporuje spravované identity.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Ověření přístupu pomocí spravované identity

Po [povolení spravované identity pro aplikaci logiky](#azure-portal-system-logic-app) a [udělit této identity přístup k cílovému prostředku nebo entity](#access-other-resources), můžete použít tuto identitu v [aktivačních událostech a akcí, které podporují spravované identity](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Pokud máte funkci Azure, kde chcete použít systémově přiřazenou identitu, [nejprve povolte ověřování pro funkce Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

Tyto kroky ukazují, jak používat spravovanou identitu s aktivační událostí nebo akcí prostřednictvím portálu Azure. Pokud chcete určit spravovanou identitu v základní definici JSON aktivační události nebo akce, přečtěte si informace [o ověřování spravované identity](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. Na [webu Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v Návrháři aplikací logiky.

1. Pokud jste tak dosud neučinili, přidejte [aktivační událost nebo akci, která podporuje spravované identity](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   Aktivační událost nebo akce PROTOKOLU HTTP může například použít systémově přiřazenou identitu, kterou jste povolili pro aplikaci logiky. Aktivační událost nebo akce PROTOKOLU HTTP obecně používá tyto vlastnosti k určení prostředku nebo entity, ke které chcete získat přístup:

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Metoda** | Ano | Metoda HTTP používaná operací, kterou chcete spustit |
   | **Identifikátor URI** | Ano | Adresa URL koncového bodu pro přístup k cílovému prostředku nebo entitě Azure. Syntaxe identifikátoru URI obvykle obsahuje [ID prostředku](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) pro prostředek nebo službu Azure. |
   | **Hlavičky** | Ne | Všechny hodnoty záhlaví, které potřebujete nebo chcete zahrnout do odchozí žádosti, například typ obsahu |
   | **Dotazy** | Ne | Všechny parametry dotazu, které potřebujete nebo chcete zahrnout do požadavku, například parametr pro konkrétní operaci nebo verzi rozhraní API pro operaci, kterou chcete spustit |
   | **Ověřování** | Ano | Typ ověřování, který se má použít pro ověřování přístupu k cílovému prostředku nebo entitě |
   ||||

   Jako konkrétní příklad předpokládejme, že chcete spustit [operaci objektu blob snímek](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) na objekt blob v účtu Azure Storage, kde jste dříve nastavili přístup pro vaši identitu. [Konektor úložiště objektů blob Azure](https://docs.microsoft.com/connectors/azureblob/) však aktuálně nenabízí tuto operaci. Místo toho můžete spustit tuto operaci pomocí [akce HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) nebo jiné operace rozhraní REST ROZHRANÍ [TYPU Blob Service .](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs)

   > [!IMPORTANT]
   > Chcete-li získat přístup k účtům úložiště Azure za bránami firewall pomocí požadavků HTTP a spravovaných identit, ujistěte se, že jste také nastavili účet úložiště s [výjimkou, která umožňuje přístup důvěryhodných služeb Microsoftu](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

   Chcete-li spustit [operaci objektu blob snímku](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob), akce HTTP určuje tyto vlastnosti:

   | Vlastnost | Požaduje se | Příklad hodnoty | Popis |
   |----------|----------|---------------|-------------|
   | **Metoda** | Ano | `PUT`| Metoda HTTP, která používá operace objektu blob snímek používá |
   | **Identifikátor URI** | Ano | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | ID prostředku pro soubor úložiště objektů blob Azure v globálním (veřejném) prostředí Azure, které používá tuto syntaxi |
   | **Hlavičky** | Ano, pro Azure Storage | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Hodnoty `x-ms-blob-type` `x-ms-version` a záhlaví, které jsou vyžadovány pro operace azure storage. <p><p>**Důležité:** V odchozíhttp aktivační události a požadavky na akce `x-ms-version` pro Azure Storage záhlaví vyžaduje vlastnost a verzi rozhraní API pro operaci, kterou chcete spustit. <p>Další informace najdete v těchto tématech: <p><p>- [Záhlaví požadavku – objekt blob snímku](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Správa verzí pro služby Azure Storage](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Dotazy** | Ano, pro tuto operaci | `comp` = `snapshot` | Název a hodnota parametru dotazu pro operaci objektu blob snímek. |
   |||||

   Zde je příklad akce HTTP, která zobrazuje všechny tyto hodnoty vlastností:

   ![Přidání akce HTTP pro přístup k prostředku Azure](./media/create-managed-service-identity/http-action-example.png)

1. Nyní přidejte vlastnost **Authentication** do akce HTTP. V seznamu **Přidat nový parametr** vyberte Možnost **Ověřování**.

   ![Přidání vlastnosti Ověřování do akce HTTP](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Ne všechny aktivační události a akce podporují umožňuje přidat typ ověřování. Další informace naleznete v tématu [Přidání ověřování k odchozím voláním](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. V seznamu **Typ ověřování** vyberte **položku Spravovaná identita**.

   ![Pro "Ověřování" vyberte "Spravovaná identita"](./media/create-managed-service-identity/select-managed-identity.png)

1. Ze seznamu spravovaných identit vyberte z dostupných možností na základě vašeho scénáře.

   * Pokud nastavíte systémově přiřazenou identitu, vyberte **Položku Spravovaná identita přiřazená systému,** pokud již není vybraná.

     ![Vyberte možnost "Systémově přiřazená spravovaná identita"](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Pokud nastavíte identitu přiřazenou uživateli, vyberte tuto identitu, pokud již není vybraná.

     ![Vyberte identitu přiřazenou uživateli.](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Tento příklad pokračuje pomocí **spravované identity přiřazené k systému**.

1. U některých aktivačních událostí a akcí se zobrazí také vlastnost **Okruh uživatelů,** kde můžete nastavit ID cílového prostředku. Nastavte vlastnost **Cílovou skupinu** na [ID prostředku cílového prostředku nebo služby](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). V opačném případě **Audience** ve výchozím `https://management.azure.com/` nastavení používá vlastnost Audience ID prostředku, což je ID prostředku pro Správce prostředků Azure.

   > [!IMPORTANT]
   > Ujistěte se, že id cílového prostředku *přesně odpovídá* hodnotě, kterou služba Azure Active Directory (AD) očekává, včetně všech požadovaných koncových lomítka. Například ID prostředku pro všechny účty úložiště objektů blob Azure vyžaduje koncové lomítko. ID prostředku pro konkrétní účet úložiště však nevyžaduje koncové lomítko. Zkontrolujte [ID prostředků pro služby Azure, které podporují Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   Tento příklad **Audience** nastaví `https://storage.azure.com/` vlastnost Audience tak, aby přístupové tokeny používané pro ověřování byly platné pro všechny účty úložiště. Můžete však také zadat adresu `https://fabrikamstorageaccount.blob.core.windows.net`URL kořenové služby , pro konkrétní účet úložiště.

   ![Nastavení vlastnosti Cílová skupina na cílové ID prostředku](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Další informace o autorizaci přístupu pomocí Azure AD pro Azure Storage najdete v těchto tématech:

   * [Autorizace přístupu k objektům BLOB a frontám Azure pomocí služby Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Autorizace přístupu k Úložišti Azure pomocí Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Pokračujte v vytváření aplikace logiky požadovaným způsobem.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Zakázat spravovanou identitu

Chcete-li přestat používat spravovanou identitu pro aplikaci logiky, máte tyto možnosti:

* [Portál Azure](#azure-portal-disable)
* [Šablony Azure Resource Manageru](#template-disable)
* Azure PowerShell
  * [Odebrání přiřazení role](../role-based-access-control/role-assignments-powershell.md)
  * [Odstranit identitu přiřazenou uživateli](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [Odebrání přiřazení role](../role-based-access-control/role-assignments-cli.md)
  * [Odstranit identitu přiřazenou uživateli](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure REST API
  * [Odebrání přiřazení role](../role-based-access-control/role-assignments-rest.md)
  * [Odstranit identitu přiřazenou uživateli](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Pokud odstraníte aplikaci logiky, Azure automaticky odebere spravovanou identitu z Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Zakázání spravované identity na webu Azure Portal

Na webu Azure Portal nejprve odeberte přístup identity k [cílovému prostředku](#disable-identity-target-resource). Dále vypněte systémem přiřazenou identitu nebo odeberte identitu přiřazenou uživateli z [aplikace logiky](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Odebrání přístupu k identitě z prostředků

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na cílový prostředek Azure, kde chcete odebrat přístup pro spravovanou identitu.

1. V nabídce cílového prostředku vyberte **řízení přístupu (IAM)**. Na panelu nástrojů vyberte **Přiřazení rolí**.

1. V seznamu rolí vyberte spravované identity, které chcete odebrat. Na panelu nástrojů vyberte **Odebrat**.

   > [!TIP]
   > Pokud je možnost **Odebrat** zakázána, pravděpodobně nemáte oprávnění. Další informace o oprávněních, která umožňují spravovat role prostředků, najdete [v tématu Oprávnění rolí správce ve službě Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Spravovaná identita je nyní odebrána a již nemá přístup k cílovému prostředku.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Zakázání spravované identity v aplikaci logiky

1. Na [webu Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v Návrháři aplikací logiky.

1. V nabídce aplikace logiky vyberte v části **Nastavení** **položku Identita**a postupujte podle pokynů pro vaši identitu:

   * Vyberte **možnost Systém přiřazený** > **při** > **uložení**. Až vás Azure vyzve k potvrzení, vyberte **Ano**.

     ![Zakázání systémově přiřazené identity](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Vyberte **Přiřazený uživatel** a spravovanou identitu a pak vyberte **Odebrat**. Až vás Azure vyzve k potvrzení, vyberte **Ano**.

     ![Odebrání identity přiřazené uživateli](./media/create-managed-service-identity/remove-user-assigned-identity.png)

Spravovaná identita je teď ve vaší aplikaci logiky zakázána.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Zakázání spravované identity v šabloně Azure Resource Manager

Pokud jste vytvořili spravovanou identitu aplikace logiky pomocí `identity` šablony Azure `type` Resource `None`Manager, nastavte podřízenou vlastnost objektu na . Pro identitu spravovanou systémem tato akce také odstraní id hlavního ve službě Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Další kroky

* [Zabezpečený přístup a data v Aplikacích Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)