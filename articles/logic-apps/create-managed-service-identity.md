---
title: Ověřování pomocí spravovaných identit – Azure Logic Apps
description: K ověření bez přihlášení můžete vytvořit spravovanou identitu (dřív označovanou jako Identita spravované služby nebo MSI), aby vaše aplikace logiky mohla přistupovat k prostředkům v jiných klientech Azure Active Directory (Azure AD) bez přihlašovacích údajů nebo tajných klíčů.
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 03/29/2019
ms.openlocfilehash: bb1443afa14f2a23b807af52ab8fef6ac41ea200
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934041"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Ověřování a přístup k prostředkům se spravovanými identitami v Azure Logic Apps

Pokud chcete získat přístup k prostředkům v jiných klientech Azure Active Directory (Azure AD) a ověřit vaši identitu bez přihlášení, vaše aplikace logiky může používat [spravovanou identitu](../active-directory/managed-identities-azure-resources/overview.md) (dřív označovanou jako identita spravované služby nebo MSI) místo přihlašovacích údajů nebo tajných kódů. Azure tuto identitu spravuje za vás a pomáhá zabezpečit vaše přihlašovací údaje, protože nemusíte zadávat ani otáčet tajné klíče. V tomto článku se dozvíte, jak můžete nastavit a používat spravovanou identitu přiřazenou systémem pro vaši aplikaci logiky. Další informace o spravovaných identitách najdete v tématu [co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!NOTE]
> Vaše aplikace logiky může používat spravované identity pouze s konektory, které podporují spravované identity. V současné době pouze konektor HTTP podporuje spravované identity.
>
> V každém předplatném Azure můžete v současnosti mít až 10 pracovních postupů aplikací logiky se spravovanými identitami přiřazenými systémem.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure, nebo pokud předplatné nemáte, zaregistrujte si [bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, ve které chcete použít spravovanou identitu přiřazenou systémem. Pokud nemáte aplikaci logiky, přečtěte si téma [Vytvoření prvního pracovního postupu aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="enable-identity"></a>

## <a name="enable-managed-identity"></a>Povolit spravovanou identitu

Pro spravované identity přiřazené systémem nemusíte tuto identitu vytvářet ručně. Pokud chcete pro vaši aplikaci logiky nastavit spravovanou identitu přiřazenou systémem, můžete použít tyto způsoby: 

* [Azure Portal](#azure-portal) 
* [Šablony Azure Resource Manager](#template) 
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 

<a name="azure-portal"></a>

### <a name="azure-portal"></a>portál Azure

Pokud chcete pro vaši aplikaci logiky povolit spravovanou identitu přiřazenou systémem prostřednictvím Azure Portal, zapněte v nastavení identity vaší aplikace logiky nastavení **přiřazené systémem** .

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V nabídce aplikace logiky v části **Nastavení**vyberte **Identita**.

1. V části**stav** **přiřazení** > systému vyberte **zapnuto**. Pak vyberte **Uložit** > **Ano**.

   ![Zapnout nastavení spravované identity](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

   Vaše aplikace logiky teď má v Azure Active Directory zaregistrovanou spravovanou identitu přiřazenou systémem:

   ![Identifikátory GUID pro ID objektu](./media/create-managed-service-identity/object-id.png)

   | Vlastnost | Value | Popis |
   |----------|-------|-------------|
   | **ID objektu** | <*identity-resource-ID*> | Globálně jedinečný identifikátor (GUID), který představuje spravovanou identitu přiřazenou systémem pro vaši aplikaci logiky v tenantovi Azure AD. |
   ||||

<a name="template"></a>

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

Pokud chcete automatizovat vytváření a nasazování prostředků Azure, jako jsou Logic Apps, můžete použít [šablony Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Pokud chcete pro vaši aplikaci logiky vytvořit spravovanou identitu přiřazenou systémem pomocí šablony, přidejte `"identity"` do definice pracovního postupu aplikace logiky v šabloně nasazení element a `"type"` vlastnost: 

```json
"identity": {
   "type": "SystemAssigned"
}
```

Příklad:

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

Když Azure vytvoří aplikaci logiky, tato definice pracovního postupu aplikace logiky zahrnuje tyto další vlastnosti:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Vlastnost | Value | Popis |
|----------|-------|-------------|
| **principalId** | <*ID objektu zabezpečení*> | Globálně jedinečný identifikátor (GUID), který představuje aplikaci logiky v tenantovi Azure AD a někdy se zobrazuje jako ID objektu nebo`objectID` |
| **tenantId** | <*Azure-AD-tenant-ID*> | Globálně jedinečný identifikátor (GUID), který představuje tenanta Azure AD, ve kterém je teď aplikace logiky členem. V tenantovi služby Azure AD má instanční objekt stejný název jako instance aplikace logiky. |
||||

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Přístup k prostředkům pomocí spravované identity

Po vytvoření spravované identity přiřazené systémem pro vaši aplikaci logiky můžete [této identitě udělit přístup k dalším prostředkům Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Tuto identitu pak můžete použít pro ověřování podobně jako u jakéhokoli jiného [instančního objektu](../active-directory/develop/app-objects-and-service-principals.md). 

> [!NOTE]
> Spravovaná identita přiřazená systémem i prostředek, ke kterému chcete přiřadit přístup, musí mít stejné předplatné Azure.

### <a name="assign-access-to-managed-identity"></a>Přiřazení přístupu ke spravované identitě

Pokud chcete udělit přístup k jinému prostředku Azure pro spravovanou identitu přiřazenou vaší aplikaci logiky, postupujte takto:

1. V Azure Portal přejděte do prostředku Azure, ke kterému chcete přiřadit přístup pro spravovanou identitu.

1. V nabídce prostředku vyberte **řízení přístupu (IAM)** . Na panelu nástrojů klikněte na **Přidat** > **přiřazení role přidat**.

   ![Přidat přiřazení role](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. V části **Přidat přiřazení role**vyberte **roli** , kterou chcete pro identitu.

1. V poli **přiřadit přístup k** vlastnosti vyberte možnost **uživatel, skupina nebo INSTANČNÍ objekt Azure AD**, pokud ještě není vybraný.

1. Do pole **Vybrat** počínaje prvním znakem v názvu vaší aplikace logiky zadejte název vaší aplikace logiky. Po zobrazení aplikace logiky vyberte aplikaci logiky.

   ![Výběr aplikace logiky se spravovanou identitou](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Jakmile budete hotoví, vyberte **Uložit**.

### <a name="authenticate-with-managed-identity-in-logic-app"></a>Ověřování pomocí spravované identity v aplikaci logiky

Po nastavení aplikace logiky pomocí spravované identity přiřazené systémem a přiřazeného přístupu k prostředku, který chcete použít pro tuto identitu, teď můžete tuto identitu použít k ověřování. Můžete například použít akci HTTP, takže aplikace logiky může odeslat požadavek HTTP nebo zavolat tomuto prostředku. 

1. V aplikaci logiky přidejte akci **http** .

1. Zadejte potřebné údaje pro tuto akci, například **metodu** žádosti a umístění identifikátoru **URI** pro prostředek, který chcete volat.

   Předpokládejme například, že používáte ověřování Azure Active Directory (Azure AD) s [jednou z těchto služeb Azure, které podporují službu Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). Do pole **identifikátor URI** zadejte adresu URL koncového bodu pro tuto službu Azure. Takže pokud používáte Azure Resource Manager, zadejte tuto hodnotu do vlastnosti **URI** :

   `https://management.azure.com/subscriptions/<Azure-subscription-ID>?api-version=2016-06-01`

1. V akci HTTP vyberte **Zobrazit upřesňující možnosti**.

1. V seznamu **ověřování** vyberte **spravovaná identita**. Po výběru tohoto ověřování se zobrazí vlastnost **cílová skupina** s výchozí hodnotou ID prostředku:

   ![Vyberte spravovanou identitu.](./media/create-managed-service-identity/select-managed-service-identity.png)

   > [!IMPORTANT]
   > 
   > Hodnota ID prostředku ve vlastnosti **cílové skupiny** se musí přesně shodovat s tím, co očekává služba Azure AD, včetně všech požadovaných koncových lomítek. 
   > Tyto hodnoty ID prostředků najdete v této [tabulce popisující služby Azure, které podporují Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). 
   > Pokud například používáte ID prostředku Azure prostředků Manageru, ujistěte se, že identifikátor URI má koncové lomítko.

1. Pokračujte v sestavování aplikace logiky způsobem, který požadujete.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Odebrat spravovanou identitu

Pokud chcete v aplikaci logiky zakázat spravovanou identitu přiřazenou systémem, můžete postupovat podle kroků, které jsou podobné způsobu nastavení identity prostřednictvím Azure Portal, Azure Resource Manager šablon nasazení nebo Azure PowerShell.

Při odstranění aplikace logiky Azure automaticky odebere identitu přiřazenou systémem vaší aplikace logiky ze služby Azure AD.

### <a name="azure-portal"></a>portál Azure

Pokud chcete pro vaši aplikaci logiky pomocí Azure Portal odebrat spravovanou identitu přiřazenou systémem, vypněte nastavení **přiřazené systému** v nastavení identity vaší aplikace logiky.

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V nabídce aplikace logiky v části **Nastavení**vyberte **Identita**.

1. V části**stav** **přiřazení** > systému vyberte **vypnuto**. Pak zvolte **Uložit** > **Ano**.

   ![Vypnout nastavení spravované identity](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Šablona nasazení

Pokud jste vytvořili spravovanou identitu přiřazenou systémem aplikace logiky pomocí šablony nasazení Azure Resource Manager, nastavte `"identity"` `"type"` vlastnost prvku na `"None"`hodnotu. Tato akce také odstraní ID objektu zabezpečení z Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Další postup

* [Zabezpečený přístup a data v Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)