---
title: Přístup a ověřování bez přihlášení – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvořte spravovanou identitu, můžete aplikaci logiky ověřování a přístup k prostředkům v jiných tenantů Azure Active Directory (Azure AD) bez svoje přihlašovací údaje
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: fb1c31e6e7c075e20191a4e51d7b1a9323f3b979
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973962"
---
# <a name="access-resources-and-authenticate-as-managed-identities-in-azure-logic-apps"></a>Přístup k prostředkům a ověřit jako spravovaných identit v Azure Logic Apps

Přístup k prostředkům v jiných tenantů Azure Active Directory (Azure AD) a ověřit svou identitu i bez přihlášení, můžete vytvořit [se identita spravované](../active-directory/managed-identities-azure-resources/overview.md) , která vaše aplikace logiky používá namísto svoje přihlašovací údaje. Tato identita pro vás spravuje Platforma Azure a pomáhá zabezpečit svoje přihlašovací údaje, protože není nutné poskytovat nebo otočit tajných kódů. Tento článek ukazuje, jak vytvořit a používat spravovanou identitu pro vaši aplikaci logiky. Další informace najdete v tématu [Správa identit pro prostředky Azure](../app-service/app-service-managed-service-identity.md).

> [!NOTE]
> Spravované identity pro prostředky Azure je náhradní název služby, dřív označované jako Identity spravované služby (MSI).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure, nebo pokud nemáte předplatné, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrovat si bezplatný účet Azure</a>.

* Aplikace logiky, ve které chcete použít spravovanou identitu. Pokud nemáte aplikace logiky, přečtěte si téma [vytvoření prvního pracovního postupu aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-identity"></a>

## <a name="create-managed-identity"></a>Vytvoření spravované identity

Můžete vytvořit nebo povolit spravovanou identitu pro vaši aplikaci logiky prostřednictvím webu Azure portal, šablon Azure Resource Manageru nebo Azure Powershellu. 

### <a name="azure-portal"></a>portál Azure

Chcete-li vytvořit spravovanou identitu pro vaši aplikaci logiky na webu Azure portal, zapněte **zaregistrovat u služby Azure Active Directory** nastavení v nastavení pracovního postupu aplikace logiky.

1. V [webu Azure portal](https://portal.azure.com), otevřete v návrháři aplikace logiky aplikace logiky.

1. Postupujte následovně: 

   1. V nabídce aplikace logiky podle **nastavení**vyberte **nastavení pracovního postupu**. 

   1. V části **se identita spravované služby** > 
    **zaregistrovat u služby Azure Active Directory**, zvolte **na**.

   1. Jakmile budete hotovi, zvolte **Uložit** na panelu nástrojů.

      ![Zapnutí nastavení spravovaných identit](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

      Azure teď zobrazuje tyto vlastnosti a hodnoty pro spravovanou identitu vaší aplikace logiky:

      ![Identifikátory GUID pro ID instančního objektu a ID tenanta](./media/create-managed-service-identity/principal-tenant-id.png)

      | Vlastnost | Hodnota | Popis | 
      |----------|-------|-------------| 
      | **ID objektu zabezpečení** | <*instanční objekt. Identifikátor GUID*> | Globálně jedinečný identifikátor (GUID), který představuje aplikace logiky v tenantovi Azure AD | 
      | **ID tenanta** | <*-Tenanta služby Azure AD – Identifikátor GUID*> | Globálně jedinečný identifikátor (GUID), který představuje tenanta Azure AD, kde vaše aplikace logiky je teď členem. Instanční objekt v tenantovi Azure AD má stejný název jako instanci aplikace logiky. | 
      ||| 

### <a name="deployment-template"></a>Nasazení šablony

Pokud chcete automatizovat vytváření a nasazování prostředků Azure jako jsou například aplikace logiky, můžete nastavit šablon Azure Resource Manageru. Další informace najdete v tématu [vytvoření a nasazení aplikací logiky s využitím šablon Azure Resource Manageru](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md). 

Chcete-li vytvořit spravovanou identitu pro vaši aplikaci logiky pomocí šablony, přidejte **identity** elementu a **typ** vlastnost na definici pracovního postupu aplikace logiky do šablony nasazení. Tato nastavení značí, že Azure vytváří a spravuje tuto identitu pro vaši aplikaci logiky:

```json
"identity": {
    "type": "SystemAssigned"
}
```

Aplikace logiky může například vypadat tuto verzi:

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
    "principalId": "<principal-ID-GUID>",
    "tenantId": "<Azure-AD-tenant-ID>-GUID"
}
```

| Vlastnost | Hodnota | Popis | 
|----------|-------|-------------|
| **principalId** | <*instanční objekt. Identifikátor GUID*> | Globálně jedinečný identifikátor (GUID), který představuje aplikace logiky v tenantovi Azure AD | 
| **ID Tenanta** | <*-Tenanta služby Azure AD – Identifikátor GUID*> | Globálně jedinečný identifikátor (GUID), který představuje tenanta Azure AD, kde aplikace logiky je teď členem. Instanční objekt v tenantovi Azure AD má stejný název jako instanci aplikace logiky. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Přístup k prostředkům pomocí spravované identity

Po vytvoření spravovanou identitu pro vaši aplikaci logiky můžete [této identity přístup k dalším prostředkům](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Potom můžete tuto spravovanou identitu pro ověřování, stejně jako jakýkoli jiný [instanční objekt služby](../active-directory/develop/app-objects-and-service-principals.md). 

Předpokládejme například, že jste již nastavili aplikace logiky pomocí spravované identity, který má přístup do jiného prostředku. Nyní můžete přidat akce HTTP tak, aby vaše aplikace logiky může odeslat požadavek HTTP nebo volání do tohoto prostředku. 

1. V aplikaci logiky, přidejte **HTTP** akce. 

1. Zadejte potřebné podrobnosti pro tuto akci, jako je například požadavek **metoda** a **URI** umístění prostředků, kterou chcete volat.

1. V akci HTTP zvolte **zobrazit pokročilé možnosti**. 

1. Z **ověřování** seznamu vyberte **identita spravované služby**, který pak ukazuje **cílovou skupinu** vlastnost lze nastavit:

   ![Vyberte "Se identita spravované služby"](./media/create-managed-service-identity/select-managed-service-identity.png)

1. Pokračujte v sestavování aplikace logiky požadovaným způsobem.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Odebrání spravované identity

Zakázat spravovanou identitu na svou aplikaci logiky, provedením kroků podobný vytvoření identity prostřednictvím webu Azure portal, šablon nasazení Azure Resource Manageru nebo Azure Powershellu. 

Při odstranění aplikace logiky Azure automaticky odstraní vaší aplikace logiky systém přiřadil identitu ze služby Azure AD.

### <a name="azure-portal"></a>portál Azure

1. V návrháři aplikace logiky otevřete aplikaci logiky.

1. Postupujte následovně: 

   1. V nabídce aplikace logiky podle **nastavení**vyberte **nastavení pracovního postupu**. 
   
   1. V části **se identita spravované služby**, zvolte **vypnout** pro **zaregistrovat u služby Azure Active Directory** vlastnost.

   1. Jakmile budete hotovi, zvolte **Uložit** na panelu nástrojů.

      ![Vypněte nastavení spravovaných identit](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Nasazení šablony

Pokud jste vytvořili spravovanou identitu vaší aplikace logiky se šablonou nasazení Azure Resource Manageru, nastavte `"identity"` elementu `"type"` vlastnost `"None"`. Tato akce také odstraní ID objektu zabezpečení z Azure AD. 

```json
"identity": {
    "type": "None"
}
```

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).
