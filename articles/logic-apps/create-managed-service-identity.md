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
ms.date: 10/05/2018
ms.openlocfilehash: 84529e1097678ba7a039ffaeec57a9293c93dafd
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50229635"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Ověřování a přístup k prostředkům pomocí spravované identity v Azure Logic Apps

Přístup k prostředkům v jiných tenantů Azure Active Directory (Azure AD) a ověřit svou identitu i bez přihlášení, můžete použít svou aplikaci logiky [se identita spravované](../active-directory/managed-identities-azure-resources/overview.md) (dříve označované jako identita spravované služby nebo MSI), spíše než přihlašovací údaje nebo tajné kódy. Tato identita pro vás spravuje Platforma Azure a pomáhá zabezpečit svoje přihlašovací údaje, protože není nutné poskytovat nebo otočit tajných kódů. Tento článek popisuje, jak můžete vytvořit a použít systém přiřadil spravovanou identitu pro vaši aplikaci logiky. Další informace o spravovaných identit najdete v tématu [co je spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!NOTE]
> Můžete je aktuálně mít až 10 pracovních postupů aplikace logiky s systém přiřadil spravovat identity v rámci předplatného Azure.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure, nebo pokud nemáte předplatné, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrovat si bezplatný účet Azure</a>.

* Identita spravované aplikace logiky, ve které chcete použít systém přiřadil. Pokud nemáte aplikace logiky, přečtěte si téma [vytvoření prvního pracovního postupu aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-identity"></a>

## <a name="create-managed-identity"></a>Vytvoření spravované identity

Můžete vytvořit nebo povolit systém přiřadil spravovanou identitu pro vaši aplikaci logiky prostřednictvím webu Azure portal, šablon Azure Resource Manageru nebo Azure Powershellu. 

### <a name="azure-portal"></a>portál Azure

Chcete-li povolit systém přiřadil spravovanou identitu pro vaši aplikaci logiky na webu Azure portal, zapněte **zaregistrovat u služby Azure Active Directory** nastavení v nastavení pracovního postupu aplikace logiky.

1. V [webu Azure portal](https://portal.azure.com), otevřete v návrháři aplikace logiky aplikace logiky.

1. Postupujte následovně: 

   1. V nabídce aplikace logiky podle **nastavení**vyberte **nastavení pracovního postupu**. 

   1. V části **se identita spravované služby** > 
    **zaregistrovat u služby Azure Active Directory**, zvolte **na**.

   1. Jakmile budete hotovi, zvolte **Uložit** na panelu nástrojů.

      ![Zapnutí nastavení spravovaných identit](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

      Aplikace logiky teď má systém přiřadil spravovanou identitu ve službě Azure Active Directory zaregistrované tyto vlastnosti a hodnoty:

      ![Identifikátory GUID pro ID instančního objektu a ID tenanta](./media/create-managed-service-identity/principal-tenant-id.png)

      | Vlastnost | Hodnota | Popis | 
      |----------|-------|-------------| 
      | **ID objektu zabezpečení** | <*ID objektu zabezpečení*> | Globálně jedinečný identifikátor (GUID), který představuje aplikace logiky v tenantovi Azure AD | 
      | **ID tenanta** | <*Azure-AD-tenant-ID*> | Globálně jedinečný identifikátor (GUID), který představuje tenanta Azure AD, kde vaše aplikace logiky je teď členem. Instanční objekt v tenantovi Azure AD má stejný název jako instanci aplikace logiky. | 
      ||| 

### <a name="deployment-template"></a>Nasazení šablony

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
| **principalId** | <*ID objektu zabezpečení*> | Globálně jedinečný identifikátor (GUID), který představuje aplikace logiky v tenantovi Azure AD | 
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

1. V nabídce prostředků vyberte **řízení přístupu (IAM)** a zvolte **přidat**. 

   ![Přidání oprávnění](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. V části **přidat oprávnění**, vyberte **Role** chcete použít pro identitu. 

1. V **přiřadit přístup k** vlastnosti, vyberte **uživatele Azure AD, skupinu nebo aplikaci**, pokud ještě není vybraná.

1. V **vyberte** pole, počínaje první znak v názvu vaší aplikace logiky, zadejte název vaší aplikace logiky. Jakmile se zobrazí vaše aplikace logiky, vyberte aplikaci logiky.

   ![Vyberte aplikaci logiky s využitím spravované identity](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Jakmile budete hotoví, vyberte **Uložit**.

### <a name="authenticate-with-managed-identity-in-logic-app"></a>Ověřování pomocí spravované identity v aplikaci logiky

Jakmile nastavíte aplikaci logiky s systém přiřadil se identita spravované a přidělený přístup k prostředku, který chcete použít pro tuto identitu, teď můžete použít tuto identitu pro ověřování. Můžete například použít akce HTTP tak, aby vaše aplikace logiky může odeslat požadavek HTTP nebo volání do tohoto prostředku. 

1. V aplikaci logiky, přidejte **HTTP** akce. 

1. Zadejte potřebné podrobnosti pro tuto akci, jako je například požadavek **metoda** a **URI** umístění prostředků, kterou chcete volat.

1. V akci HTTP zvolte **zobrazit pokročilé možnosti**. 

1. Z **ověřování** seznamu vyberte **identita spravované služby**, který pak ukazuje **cílovou skupinu** vlastnost lze nastavit:

   ![Vyberte "Se identita spravované služby"](./media/create-managed-service-identity/select-managed-service-identity.png)

1. Pokračujte v sestavování aplikace logiky požadovaným způsobem.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Odebrání spravované identity

Systém přiřadil spravovanou identitu na svou aplikaci logiky zakázat, provedením kroků podobný vytvoření identity prostřednictvím webu Azure portal, šablon nasazení Azure Resource Manageru nebo Azure Powershellu. 

Při odstranění aplikace logiky Azure automaticky odstraní vaší aplikace logiky systém přiřadil identitu ze služby Azure AD.

### <a name="azure-portal"></a>portál Azure

1. V návrháři aplikace logiky otevřete aplikaci logiky.

1. Postupujte následovně: 

   1. V nabídce aplikace logiky podle **nastavení**vyberte **nastavení pracovního postupu**. 
   
   1. V části **se identita spravované služby**, zvolte **vypnout** pro **zaregistrovat u služby Azure Active Directory** vlastnost.

   1. Jakmile budete hotovi, zvolte **Uložit** na panelu nástrojů.

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

