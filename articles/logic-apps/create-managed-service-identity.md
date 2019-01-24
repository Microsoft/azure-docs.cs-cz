---
title: Ověřování pomocí spravované identity – Azure Logic Apps | Dokumentace Microsoftu
description: K ověření bez přihlášení, můžete vytvořit spravovanou identitu (dříve se označovaly jako identita spravované služby nebo MSI), aplikace logiky mít přístup k prostředkům v jiné služby Azure Active Directory (Azure AD) klienti bez přihlašovací údaje a tajné kódy
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: a22512a960426cc21f4f012e06b9df4fa86e637e
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54807265"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Ověřování a přístup k prostředkům pomocí spravované identity v Azure Logic Apps

Přístup k prostředkům v jiných tenantů Azure Active Directory (Azure AD) a ověřit svou identitu i bez přihlášení, můžete použít svou aplikaci logiky [se identita spravované](../active-directory/managed-identities-azure-resources/overview.md) (dříve označované jako identita spravované služby nebo MSI), spíše než přihlašovací údaje nebo tajné kódy. Tato identita pro vás spravuje Platforma Azure a pomáhá zabezpečit svoje přihlašovací údaje, protože není nutné poskytovat nebo otočit tajných kódů. Tento článek popisuje, jak můžete nastavit a používat systém přiřadil spravovanou identitu pro vaši aplikaci logiky. Další informace o spravovaných identit najdete v tématu [co je spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!NOTE]
> Můžete je aktuálně mít až 10 pracovních postupů aplikace logiky s systém přiřadil spravovat identity v rámci předplatného Azure.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure, nebo pokud nemáte předplatné, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrovat si bezplatný účet Azure</a>.

* Identita spravované aplikace logiky, ve které chcete použít systém přiřadil. Pokud nemáte aplikace logiky, přečtěte si téma [vytvoření prvního pracovního postupu aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="enable-identity"></a>

## <a name="enable-managed-identity"></a>Povolit spravované identity

Pro systém přiřadil spravovaných identit není nutné ručně vytvořit tuto identitu. Nastavit systém přiřadil spravovanou identitu pro vaši aplikaci logiky, můžete těmito způsoby: 

* [Azure Portal](#azure-portal) 
* [Šablony Azure Resource Manageru](#template) 
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 

<a name="azure-portal"></a>

### <a name="azure-portal"></a>portál Azure

Chcete-li povolit systém přiřadil spravovanou identitu pro vaši aplikaci logiky na webu Azure portal, zapněte **přiřazenou systémem** nastavení v nastavení identit aplikace logiky.

1. V [webu Azure portal](https://portal.azure.com), otevřete v návrháři aplikace logiky aplikace logiky.

1. V nabídce aplikace logiky podle **nastavení**vyberte **Identity**. 

1. V části **přiřazenou systémem** > **stav**, zvolte **na**. Potom kliknutím na možnost **Uložit** > **Ano**.

   ![Zapnutí nastavení spravovaných identit](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

   Aplikace logiky teď má systém přiřadil spravované identity v Azure Active Directory:

   ![Identifikátory GUID pro ID objektu](./media/create-managed-service-identity/object-id.png)

   | Vlastnost | Hodnota | Popis | 
   |----------|-------|-------------| 
   | **ID objektu** | <*identity-resource-ID*> | Globálně jedinečný identifikátor (GUID), který představuje systém přiřadil spravované identity pro vaše aplikace logiky v tenantovi Azure AD | 
   ||| 

<a name="template"></a>

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

Pokud chcete automatizovat vytváření a nasazování prostředků Azure jako jsou například aplikace logiky, můžete použít [šablon Azure Resource Manageru](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md). Chcete-li vytvořit systém přiřadil spravovanou identitu pro vaši aplikaci logiky pomocí šablony, přidejte `"identity"` elementu a `"type"` vlastnost na definici pracovního postupu aplikace logiky do šablony nasazení: 

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

Když Azure vytvářet aplikace logiky, definice pracovního postupu vaší aplikace logiky zahrnuje tyto další vlastnosti:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Vlastnost | Hodnota | Popis | 
|----------|-------|-------------|
| **principalId** | <*principal-ID*> | Globálně jedinečný identifikátor (GUID), který představuje aplikace logiky v tenantovi Azure AD a někdy se zobrazí jako "ID objektu" nebo `objectID` | 
| **ID Tenanta** | <*Azure-AD-tenant-ID*> | Globálně jedinečný identifikátor (GUID), který představuje tenanta Azure AD, kde aplikace logiky je teď členem. Instanční objekt v tenantovi Azure AD má stejný název jako instanci aplikace logiky. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Přístup k prostředkům pomocí spravované identity

Po vytvoření systém přiřadil spravovanou identitu pro vaši aplikaci logiky můžete [poskytnout tento přístup identit k další prostředky Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Potom můžete tuto identitu pro ověřování, stejně jako jakýkoli jiný [instanční objekt služby](../active-directory/develop/app-objects-and-service-principals.md). 

> [!NOTE]
> Systém přiřadil spravovanou identitu a prostředků, ve které chcete přiřadit přístup, musí mít stejné předplatné Azure.

### <a name="assign-access-to-managed-identity"></a>Přiřazení přístupu k spravovaná identita

Udělit přístup do jiného prostředku Azure pro svou aplikaci logiky, systém přiřadil spravovanou identitu, postupujte podle těchto kroků:

1. Na webu Azure Portal přejděte k prostředku Azure ve které chcete přiřadit přístup pro spravovanou identitu. 

1. V nabídce prostředků vyberte **řízení přístupu (IAM)** a zvolte **přidat přiřazení role**. 

   ![Přidat přiřazení role](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. V části **přidat přiřazení role**, vyberte **Role** chcete použít pro identitu. 

1. V **přiřadit přístup k** vlastnosti, vyberte **uživatele, skupinu nebo instanční objekt služby Azure AD**, pokud ještě není vybraná.

1. V **vyberte** pole, počínaje první znak v názvu vaší aplikace logiky, zadejte název vaší aplikace logiky. Jakmile se zobrazí vaše aplikace logiky, vyberte aplikaci logiky.

   ![Vyberte aplikaci logiky s využitím spravované identity](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Jakmile budete hotoví, vyberte **Uložit**.

### <a name="authenticate-with-managed-identity-in-logic-app"></a>Ověřování pomocí spravované identity v aplikaci logiky

Jakmile nastavíte aplikaci logiky s systém přiřadil se identita spravované a přidělený přístup k prostředku, který chcete použít pro tuto identitu, teď můžete použít tuto identitu pro ověřování. Můžete například použít akce HTTP tak, aby vaše aplikace logiky může odeslat požadavek HTTP nebo volání do tohoto prostředku. 

1. V aplikaci logiky, přidejte **HTTP** akce. 

1. Zadejte potřebné podrobnosti pro tuto akci, jako je například požadavek **metoda** a **URI** umístění prostředků, kterou chcete volat.

   Předpokládejme například, že používáte ověřování Azure Active Directory (Azure AD) s [jednu z těchto služeb Azure, které podporují služby Azure AD](../active-directory/managed-identities-azure-resources/services-support-msi.md#azure-services-that-support-azure-ad-authentication). 
   V **URI** zadejte adresu URL koncového bodu pro tuto službu Azure. 
   Ano, pokud používáte Azure Resource Manageru, zadejte tuto hodnotu v **URI** vlastnost:

   `https://management.azure.com/subscriptions/<Azure-subscription-ID>?api-version-2016-06-01`

1. V akci HTTP zvolte **zobrazit pokročilé možnosti**. 

1. Z **ověřování** seznamu vyberte **Identity spravované**. Po výběru tohoto ověřování **cílovou skupinu** vlastnost se zobrazí s výchozí hodnotou ID prostředku:

   ![Vyberte "Spravované Identity"](./media/create-managed-service-identity/select-managed-service-identity.png)

   > [!IMPORTANT]
   > 
   > V **cílovou skupinu** vlastnost, hodnota ID prostředku musí přesně odpovídat hodnotám očekává Azure AD, včetně všech požadovaných koncových lomítek. 
   > Tyto hodnoty ID prostředků najdete v tomto [tabulku popisující Azure services – Azure AD, které podporují](../active-directory/managed-identities-azure-resources/services-support-msi.md#azure-services-that-support-azure-ad-authentication). 
   > Například pokud používáte ID prostředku správce prostředků Azure, ujistěte se, že identifikátor URI má koncové lomítko.

1. Pokračujte v sestavování aplikace logiky požadovaným způsobem.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Odebrání spravované identity

Systém přiřadil spravovanou identitu na svou aplikaci logiky zakázat, provedením kroků podobný nastavení identit prostřednictvím webu Azure portal, šablon nasazení Azure Resource Manageru nebo Azure Powershellu. 

Při odstranění aplikace logiky Azure automaticky odstraní vaší aplikace logiky systém přiřadil identitu ze služby Azure AD.

### <a name="azure-portal"></a>portál Azure

Chcete-li odebrat systém přiřadil spravovanou identitu pro vaši aplikaci logiky na webu Azure portal, vypněte **přiřazenou systémem** nastavení v nastavení identit aplikace logiky.

1. V [webu Azure portal](https://portal.azure.com), otevřete v návrháři aplikace logiky aplikace logiky.

1. V nabídce aplikace logiky podle **nastavení**vyberte **Identity**. 

1. V části **přiřazenou systémem** > **stav**, zvolte **vypnout**. Potom kliknutím na možnost **Uložit** > **Ano**.

   ![Vypněte nastavení spravovaných identit](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Nasazení šablony

Pokud jste vytvořili aplikaci logiky, systém přiřadil spravovanou identitu se šablonou nasazení Azure Resource Manageru, nastavte `"identity"` elementu `"type"` vlastnost `"None"`. Tato akce také odstraní ID objektu zabezpečení z Azure AD. 

```json
"identity": {
   "type": "None"
}
```

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).