---
title: Spravovaná aplikace se spravovanou identitou
description: Nakonfigurujte spravovanou aplikaci se spravovanou identitou pro propojení s existujícími prostředky, správu prostředků Azure a poskytování provozní identity pro protokol aktivit.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: dbf75262440474c5cb50a6d733ac7cba212b5f3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651654"
---
# <a name="azure-managed-application-with-managed-identity"></a>Spravovaná aplikace Azure se spravovanou identitou

> [!NOTE]
> Podpora spravované identity pro spravované aplikace je aktuálně ve verzi Preview. K využití spravované identity použijte verzi rozhraní API 2018-09-01-preview.

Přečtěte si, jak nakonfigurovat spravovanou aplikaci tak, aby obsahovala spravovanou identitu. Spravovanou identitu lze použít k tomu, aby zákazník mohl udělit spravované aplikaci přístup k dalším existujícím prostředkům. Identita je spravována platformou Azure a nevyžaduje, abyste zrozovávat nebo otáčet žádné tajné klíče. Další informace o spravovaných identitách ve Službě Azure Active Directory (AAD) najdete v [tématu Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Vaší žádosti lze udělit dva typy identit:

- Systémem **přiřazená identita** je s vaší aplikací svázaná a pokud je vaše aplikace odstraněná, smaže se. Aplikace může mít pouze jednu identitu přiřazenou systému.
- **Identita přiřazená uživatelem** je samostatný prostředek Azure, který se dá přiřadit k vaší aplikaci. Aplikace může mít více identit přiřazených uživatelem.

## <a name="how-to-use-managed-identity"></a>Použití spravované identity

Spravovaná identita umožňuje mnoho scénářů pro spravované aplikace. Některé běžné scénáře, které lze vyřešit, jsou:

- Nasazení spravované aplikace propojené s existujícími prostředky Azure. Příkladem je nasazení virtuálního počítače (VM) Azure v rámci spravované aplikace, která je připojena k [existujícímu síťovému rozhraní](../../virtual-network/virtual-network-network-interface-vm.md).
- Udělení spravované aplikace a vydavatele přístupu k prostředkům Azure mimo **skupinu spravovaných prostředků**.
- Poskytování provozní identity spravovaných aplikací pro protokol aktivit a dalších služeb v rámci Azure.

## <a name="adding-managed-identity"></a>Přidání spravované identity

Vytvoření spravované aplikace se spravovanou identitou vyžaduje další vlastnost, která se má nastavit na prostředek Azure. Následující příklad ukazuje vlastnost ukázkové **identity:**

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Existují dva běžné způsoby, jak vytvořit spravovanou aplikaci s **identitou:** [CreateUIDefinition.json](./create-uidefinition-overview.md) a [Šablony Azure Resource Manager](../templates/template-syntax.md). Pro jednoduché scénáře jednoduché ho vytvoření CreateUIDefinition by měl být použit k povolení spravované identity, protože poskytuje bohatší prostředí. Při práci s pokročilými nebo složitými systémy, které vyžadují automatické nebo více nasazení spravovaných aplikací, však lze použít šablony.

### <a name="using-createuidefinition"></a>Pomocí CreateUIDefinition

Spravovanou aplikaci lze nakonfigurovat pomocí spravované identity prostřednictvím [souboru CreateUIDefinition.json](./create-uidefinition-overview.md). V [části výstupy](./create-uidefinition-overview.md#outputs)lze `managedIdentity` klíč použít k přepsání vlastnosti identity šablony spravované aplikace. Níže ukázkový obsah umožní **systémově přiřazenou** identitu ve spravované aplikaci. Složitější objekty identity lze vytvořit pomocí CreateUIDefinition prvky požádat spotřebitele o vstupy. Tyto vstupy lze použít ke vytvoření spravovaných aplikací s **identitou přiřazenou uživatelem**.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Kdy použít definici CreateUIDefinition pro spravovanou identitu

Níže jsou uvedeny některá doporučení, kdy použít CreateUIDefinition pro povolení spravované identity ve spravovaných aplikacích.

- Vytvoření spravované aplikace prochází portálem Azure nebo tržištěm.
- Spravovaná identita vyžaduje komplexní vstup příjemce.
- Spravovaná identita je potřebná při vytváření spravované aplikace.

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned CreateUIDefinition

Základní CreateUIDefinition, který umožňuje SystemAssigned identity pro spravovanou aplikaci.

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

Základní CreateUIDefinition, který bere **prostředek identity přiřazený uživateli** jako vstup a umožňuje identitu UserAssigned pro spravovanou aplikaci.

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

Výše uvedené soubor CreateUIDefinition.json generuje uživatelské prostředí, které má textové pole pro příjemce, aby mohl zadat ID prostředku Azure **přiřazené k identitě přiřazené uživateli.** Generovaný zážitek bude vypadat takto:

![Ukázka identity přiřazené uživateli CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Použití šablon Azure Resource Manageru

> [!NOTE]
> Šablony spravovaných aplikací Marketplace se automaticky generují pro zákazníky, kteří procházejí prostředím pro vytváření portálu Azure.
> Pro tyto scénáře `managedIdentity` výstupní klíč na CreateUIDefinition musí být použit k povolení identity.

Spravovanou identitu lze taky povolit prostřednictvím šablon Azure Resource Manager. Níže ukázkový obsah umožní **systémově přiřazenou** identitu ve spravované aplikaci. Složitější objekty identity lze vytvořit pomocí parametrů šablony Azure Resource Manager poskytnout vstupy. Tyto vstupy lze použít ke vytvoření spravovaných aplikací s **identitou přiřazenou uživatelem**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Kdy použít šablony Azure Resource Manageru pro spravovanou identitu

Níže jsou uvedeny některá doporučení, kdy použít šablony Azure Resource Manager pro povolení spravované identity ve spravovaných aplikacích.

- Spravované aplikace lze programově nasadit na základě šablony.
- Vlastní přiřazení rolí pro spravovanou identitu jsou potřeba k zřízení spravované aplikace.
- Spravovaná aplikace nepotřebuje tok vytvoření portálu Azure a marketplace.

#### <a name="systemassigned-template"></a>SystemAssigned šablona

Základní šablona Azure Resource Manageru, která nasazuje spravovanou aplikaci se **systémem přiřazenou** identitou.

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

### <a name="userassigned-template"></a>UserAssigned šablona

Základní šablona Azure Resource Manageru, která nasazuje spravovanou aplikaci s **identitou přiřazenou uživatelem**.

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

Jakmile spravované aplikace je udělena identita, může být udělen přístup k existující prostředky Azure. Tento proces lze provést prostřednictvím rozhraní řízení přístupu (IAM) na webu Azure Portal. Název spravované aplikace nebo **uživatelem přiřazené identity** lze prohledávat a přidat přiřazení role.

![Přidání přiřazení role pro spravovanou aplikaci](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Propojení stávajících prostředků Azure

> [!NOTE]
> Před nasazením spravované aplikace musí být [nakonfigurována](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) **identita přiřazená uživatelem.** Kromě toho je nasazení propojených prostředků spravovaných aplikací podporováno pouze pro druh **tržiště.**

Spravovanou identitu lze také použít k nasazení spravované aplikace, která vyžaduje přístup k existujícím prostředkům během jejího nasazení. Když je spravovaná aplikace zřízena zákazníkem, mohou být přidány **identity přiřazené uživateli,** které poskytují další autorizace pro nasazení **hlavní šablony.**

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Vytvoření definice CreateUIDefinition s propojeným zdrojem

Při propojení nasazení spravované aplikace s existujícími prostředky musí být poskytnuta existující prostředek Azure i **identita přiřazená uživatelem** s příslušným přiřazením role pro daný prostředek.

 Ukázka CreateUIDefinition, která vyžaduje dva vstupy: ID prostředku síťového rozhraní a id prostředku přiřazené uživateli.

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

Tento CreateUIDefinition.json generuje vytvořit uživatelské prostředí, které má dvě pole. První pole umožňuje uživateli zadat id prostředku Azure pro prostředek, který je propojen s nasazením spravované aplikace. Druhý je pro spotřebitele zadat **uživatelem přiřazenou identitu** ID prostředku Azure, který má přístup k propojenému prostředku Azure. Generovaný zážitek bude vypadat takto:

![Ukázka vytvoření definice u dvou vstupů: ID prostředku síťového rozhraní a ID prostředku přiřazené uživatelem](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Vytváření hlavní šablony s propojeným zdrojem

Kromě aktualizace CreateUIDefinition, hlavní šablona také musí být aktualizovány přijmout předány v propojené id prostředků. Hlavní šablonu lze aktualizovat tak, aby přijala nový výstup přidáním nového parametru. Vzhledem `managedIdentity` k tomu, že výstup přepíše hodnotu v generované šabloně spravované aplikace, není předán do hlavní šablony a neměl by být zahrnut do části parametry.

Ukázková hlavní šablona, která nastaví profil sítě na existující síťové rozhraní poskytované createuidefinition.

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

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Využití spravované aplikace s propojeným prostředkem

Po vytvoření balíčku spravované aplikace lze správu aplikace spotřebovávat prostřednictvím portálu Azure. Před tím, než může být spotřebována, existuje několik nezbytných kroků.

- Musí být vytvořena instance požadovaného propojeného prostředku Azure.
- Identita **přiřazená uživateli** musí být [vytvořena a přiřazena přiřazení rolí](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) propojenému prostředku.
- Existující ID propojeného prostředku a **ID identity přiřazené uživateli** jsou k dispozici createuidefinition.

## <a name="accessing-the-managed-identity-token"></a>Přístup k tokenu spravované identity

Token spravované aplikace lze nyní přistupovat `listTokens` prostřednictvím rozhraní API z klienta vydavatele. Ukázkový požadavek může vypadat takto:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

Parametry tělo požadavku:

Parametr | Požaduje se | Popis
---|---|---
autorizacePublikum | *ne* | Identifikátor URI ID aplikace cílového prostředku. Je to `aud` také (publikum) nárok vydaného tokenu. Výchozí hodnota jehttps://management.azure.com/" "
userAssignedIdentities | *ne* | Seznam spravovaných identit přiřazených uživatelem, pro které lze načíst token. Pokud není `listTokens` zadán, vrátí token pro systémem přiřazenou spravovanou identitu.


Ukázková odpověď může vypadat takto:

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

Odpověď bude obsahovat pole tokenů `value` pod vlastností:

Parametr | Popis
---|---
access_token | Požadovaný přístupový token.
expires_in | Počet sekund, po které bude přístupový token platný.
expires_on | Časový rozsah při vypršení platnosti přístupového tokenu. To je reprezentován jako počet sekund z epochy.
not_before | Časový rozsah, kdy se projeví přístupový token. To je reprezentován jako počet sekund z epochy.
autorizacePublikum | (Cílová `aud` skupina) přístupový token byl požadavek. To je stejné jako to, `listTokens` co bylo poskytnuto v žádosti.
resourceId | ID prostředku Azure pro vydaný token. Toto je buď ID spravované aplikace nebo ID identity přiřazené uživateli.
token_type | Typ tokenu

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Jak nakonfigurovat spravovanou aplikaci s vlastním zprostředkovatelem](../custom-providers/overview.md)
