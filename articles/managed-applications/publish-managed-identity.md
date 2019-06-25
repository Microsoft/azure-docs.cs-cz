---
title: Spravované aplikace Azure s využitím spravované Identity
description: Zjistěte, jak nakonfigurovat spravované Identity spravované aplikace. Spravované Identity lze použít k nasazení spravované aplikace propojené s existující prostředky, udělte spravované aplikace spravovat prostředky Azure mimo spravovanou skupinu prostředků a poskytovat provozní identitu spravované aplikace pro protokol aktivit a Další služby v rámci Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 5ef653e825a5f1eb0f5df52f9c2544a5224b34cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66003443"
---
# <a name="azure-managed-application-with-managed-identity"></a>Spravované aplikace Azure s využitím spravované Identity

> [!NOTE]
> Podpora Identity spravovaných pro spravované aplikace je aktuálně ve verzi preview. Použijte prosím verzi rozhraní api 2018-09-01-preview využívat spravované Identity.

Zjistěte, jak nakonfigurovat spravované aplikace tak, aby obsahovala spravované Identity. Spravované Identity umožňuje povolit zákazníka k udělení přístupu spravované aplikace na další existující prostředky. Identita je spravovaná Platforma Azure a není nutné zřizovat nebo otočit jakýchkoli tajných kódů. Další informace o spravovaných identit v Azure Active Directory (AAD) najdete v tématu [spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

Aplikaci lze udělit dva typy identit:

- A **systém přiřadil identity** se váže na vaši aplikaci a je odstranit, pokud vaše aplikace se odstraní. Aplikace může mít pouze jednu identitu systém přiřadil.
- A **uživatelsky přiřazené identity** je samostatný prostředek Azure, který lze přiřadit k aplikaci. Aplikace může obsahovat více uživatelsky přiřazené identity.

## <a name="how-to-use-managed-identity"></a>Použití spravované Identity

Spravované Identity umožňuje mnoho scénářů pro spravované aplikace. Jsou uvedeny některé obvyklé scénáře, které je možné řešit:

- Nasazení spravované aplikace propojené s existující prostředky Azure. Příklad nasazuje virtuální počítač Azure (VM) v rámci spravované aplikace, která je přiřazena [existující síťové rozhraní](../virtual-network/virtual-network-network-interface-vm.md).
- Udělení přístupu spravovaných aplikací a vydavatele na Azure prostředky mimo **spravované skupiny prostředků**.
- Zajištění provozní identitu spravované aplikace pro protokol aktivit a dalších služeb v rámci Azure.

## <a name="adding-managed-identity"></a>Přidání spravovaných identit

Vytvoření spravované aplikace pomocí spravované Identity vyžaduje další vlastnosti pro nastavení u prostředku Azure. Následující příklad ukazuje ukázku **identity** vlastnost:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Existují dva běžné způsoby vytvoření spravované aplikace s **identity**: [CreateUIDefinition.json](./create-uidefinition-overview.md) a [šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md). Pro zadání jedné jednoduché vytvářet scénáře, CreateUIDefinition by se povolit identitu spravované použít, protože poskytuje pohodlnější a pestřejší prostředí. Při zpracování komplexnějších pokročilé nebo komplexních systémů, které vyžadují automatické nebo více nasazení spravované aplikace, šablony lze použít.

### <a name="using-createuidefinition"></a>Pomocí CreateUIDefinition

Spravované aplikace může mít nakonfigurovanou identitu spravované prostřednictvím [CreateUIDefinition.json](./create-uidefinition-overview.md). V [výstupy části](./create-uidefinition-overview.md#outputs), klíč `managedIdentity` můžete použít k přepsání vlastnosti identita třídy šablony spravované aplikace. Ukázka zmeškáte umožní **systém přiřadil** identitu na spravované aplikace. Složitější objekty identity může být tvořen pomocí elementů CreateUIDefinition požádat spotřebitele o vstupy. Tyto vstupy lze použít k vytvoření spravované aplikace s **uživatelsky přiřazené identity**.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Kdy použít pro identitu spravované CreateUIDefinition

Níže uvádíme několik doporučení, na kdy použít CreateUIDefinition pro povolení Identity spravované na spravované aplikace.

- Vytvoření spravované aplikace přejde na webu Azure portal nebo v marketplace.
- Identita spravované vyžaduje zadání složitých příjemce.
- Identita spravované je potřeba při vytvoření spravované aplikace.

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned CreateUIDefinition

Základní CreateUIDefinition umožňující SystemAssigned identity pro spravované aplikace.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>UserAssigned CreateUIDefinition

Základní CreateUIDefinition, který přijímá **uživatelsky přiřazené identity** prostředků, stejně jako vstup a aktivovat UserAssigned identity pro spravované aplikace.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

Výše uvedené CreateUIDefinition.json generuje uživatelské prostředí vytvořit, obsahující textové pole pro spotřebitele zadat **uživatelsky přiřazené identity** ID prostředku Azure. Vygenerovaný prostředí vypadat nějak takto:

![Ukázka uživatelsky přiřazené identity CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Pomocí šablon Azure Resource Manageru

> [!NOTE]
> Šablony Marketplace spravované aplikace jsou automaticky generovány pro zákazníky, že přejdete na webu Azure portal vytvořit prostředí.
> Pro tyto scénáře `managedIdentity` použije klíč výstup na CreateUIDefinition povolit identitu.

Identita spravované je možné povolit také pomocí šablon Azure Resource Manageru. Ukázka zmeškáte umožní **systém přiřadil** identitu na spravované aplikace. Složitější objekty identity může být tvořen použití parametrů šablony Azure Resource Manageru pro zajištění vstupy. Tyto vstupy lze použít k vytvoření spravované aplikace s **uživatelsky přiřazené identity**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Použití šablon Azure Resource Manageru pro spravované Identity

Níže je několik doporučení pro použití šablon Azure Resource Manageru pro povolení Identity spravované na spravované aplikace.

- Spravované aplikace je možné nasadit prostřednictvím kódu programu, na základě šablony.
- Přiřazení vlastních rolí pro identitu spravované jsou potřeba ke zřízení spravovaných aplikací.
- Spravované aplikace Azure portal a marketplace průběh vytváření nemusí.

#### <a name="systemassigned-template"></a>SystemAssigned šablony

Základní šablony Azure Resource Manageru, který se nasazuje spravované aplikace s **systém přiřadil** identity.

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>UserAssigned šablony

Základní šablony Azure Resource Manageru, které spravované aplikace se nasadí **uživatelsky přiřazené identity**.

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Udělení přístupu k prostředkům Azure

Jakmile spravované aplikace jsou udělena identitu, lze udělit přístup ke stávajícím prostředkům azure. Tento postup lze provést prostřednictvím rozhraní (IAM) řízení přístupu na webu Azure Portal. Název spravované aplikace nebo **uživatelsky přiřazené identity** může být prohledávány při přidání přiřazení role.

![Přidání přiřazení role pro spravované aplikace](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Propojení stávajících prostředků Azure

> [!NOTE]
> A **uživatelsky přiřazené identity** musí být [nakonfigurované](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) před nasazením spravované aplikace. Kromě toho propojeného prostředku nasazení spravované aplikace se podporuje jenom pro **marketplace** druhu.

Spravované Identity lze použít také k nasazení spravované aplikace, která vyžaduje přístup ke stávajícím prostředkům během nasazení. Při zřízení spravovaných aplikací zákazníka, **uživatelsky přiřazené identity** lze přidat k poskytování dalších povolení **mainTemplate** nasazení.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Vytváření CreateUIDefinition s propojeného prostředku

Při propojování nasazení spravovaných aplikací ke stávajícím prostředkům, i stávající prostředky Azure a **uživatelsky přiřazené identity** k příslušné roli musí být zadaná přiřazení na tento prostředek.

 Ukázka CreateUIDefinition, která vyžaduje dva vstupy: ID prostředku rozhraní sítě a id prostředku identity přiřazené uživateli.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

Tato CreateUIDefinition.json generuje vytvořit uživatelské prostředí, která má dvě pole. První pole umožňuje uživateli zadat ID prostředku Azure pro prostředek propojena k nasazení spravované aplikace. Druhá je příjemce k zadání **uživatelsky přiřazené identity** ID prostředku Azure, která má přístup k propojenému prostředku Azure. Vygenerovaný prostředí vypadat nějak takto:

![Ukázkový CreateUIDefinition se dva vstupy: síťové rozhraní ID prostředku a ID prostředku identity přiřazené uživateli](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Vytváření mainTemplate s propojeného prostředku

Kromě aktualizací CreateUIDefinition hlavní šablony musí také aktualizovat tak, aby přijímal předané ID propojeného prostředku. Hlavní šablony je možné aktualizovat tak, aby přijímal nový výstup tak, že přidáte nový parametr. Vzhledem k tomu, `managedIdentity` výstup přepíše hodnotu na vygenerované šablony spravované aplikace, který není předaný do hlavní šablony a by neměla být obsažená v sekci parametrů.

Ukázka hlavní šablony, které nastaví na existující síťové rozhraní, poskytuje CreateUIDefinition profilu sítě.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Použití spravovaných aplikací s propojeného prostředku

Po vytvoření balíčku spravované aplikace mohou být spotřebovány spravovaných aplikací na webu Azure portal. Předtím, než může být upotřebena, existuje několik předpokladů kroků.

- Musí být vytvořena instance vyžaduje propojeného prostředku Azure.
- **Uživatelsky přiřazené identity** musí být [vytvořen a zadané přiřazení rolí](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) k propojenému prostředku.
- Stávající propojená ID prostředku a **uživatelsky přiřazené identity** ID jsou k dispozici na CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Přístup k tokenu spravované Identity

Token spravované aplikace je teď přístupný prostřednictvím `listTokens` rozhraní api z klienta vydavatele. Příklad žádosti může vypadat:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}?api-version=2018-09-01-preview HTTP/1.1
```

Ukázkové odpovědi může vypadat:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Ke konfiguraci spravovaných aplikací s využitím vlastního zprostředkovatele](./custom-providers-overview.md)