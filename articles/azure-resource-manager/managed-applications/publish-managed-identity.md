---
title: Spravovaná aplikace se spravovanou identitou
description: Konfigurace spravované aplikace se spravovanou identitou pro propojení s existujícími prostředky, Správa prostředků Azure a poskytování provozní identity pro protokol aktivit
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 277faa2d47df9fddd1762d90d9aa2fb5bf00d4df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "82508121"
---
# <a name="azure-managed-application-with-managed-identity"></a>Spravovaná aplikace Azure se spravovanou identitou

> [!NOTE]
> Podpora spravované identity pro spravované aplikace je momentálně ve verzi Preview. K využití spravované identity použijte verzi rozhraní API 2018-09-01-Preview.

Naučte se, jak nakonfigurovat spravovanou aplikaci tak, aby obsahovala spravovanou identitu. Spravovaná identita se dá použít k tomu, aby zákazník udělil přístup spravované aplikace k dalším existujícím prostředkům. Identita je spravovaná platformou Azure a nevyžaduje zřízení ani otočení jakýchkoli tajných klíčů. Další informace o spravovaných identitách v Azure Active Directory (AAD) najdete v tématu [spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Aplikaci lze udělit dva typy identit:

- **Identita přiřazená systémem** je svázána s vaší aplikací a je odstraněna, pokud je vaše aplikace odstraněna. Aplikace může mít jenom jednu identitu přiřazenou systémem.
- **Uživatelsky přiřazená identita** je samostatný prostředek Azure, který je možné přiřadit k vaší aplikaci. Aplikace může mít více uživatelsky přiřazených identit.

## <a name="how-to-use-managed-identity"></a>Použití spravované identity

Spravovaná identita povoluje mnoho scénářů pro spravované aplikace. Některé běžné scénáře, které lze vyřešit:

- Nasazuje se spravovaná aplikace propojená s existujícími prostředky Azure. Příkladem je nasazení virtuálního počítače Azure v rámci spravované aplikace, která je připojená k [existujícímu síťovému rozhraní](../../virtual-network/virtual-network-network-interface-vm.md).
- Udělení přístupu spravované aplikaci a vydavateli k prostředkům Azure mimo **spravovanou skupinu prostředků**
- Poskytování provozní identity spravovaných aplikací pro protokol aktivit a další služby v rámci Azure.

## <a name="adding-managed-identity"></a>Přidání spravované identity

Vytvoření spravované aplikace se spravovanou identitou vyžaduje, abyste u prostředku Azure nastavili další vlastnost. Následující příklad zobrazuje ukázkovou vlastnost **identity** :

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Existují dva běžné způsoby, jak vytvořit spravovanou aplikaci s **identitou**: [CreateUIDefinition.jsv](./create-uidefinition-overview.md) [šablonách a Azure Resource Manager](../templates/template-syntax.md). V případě jednoduchých jednoduchých scénářů vytváření by se měla CreateUIDefinition použít k povolení spravované identity, protože poskytuje bohatší možnosti. Při práci s pokročilými nebo složitými systémy, které vyžadují automatizované nebo vícenásobné nasazení spravovaných aplikací, je ale možné použít šablony.

### <a name="using-createuidefinition"></a>Použití CreateUIDefinition

Spravovaná aplikace se dá nakonfigurovat se spravovanou identitou prostřednictvím [CreateUIDefinition.jsv](./create-uidefinition-overview.md). V [části výstupy](./create-uidefinition-overview.md#outputs) `managedIdentity` lze klíč použít k přepsání vlastnosti identity šablony spravované aplikace. Vzorový níže povolí pro spravovanou aplikaci identitu **přiřazenou systémem** . Složitější objekty identity mohou být vytvořeny pomocí CreateUIDefinition elementů, aby požádaly spotřebitele o vstupy. Tyto vstupy se dají použít k sestavování spravovaných aplikací s **uživatelsky přiřazenou identitou**.

```json
"outputs": {
    "managedIdentity": { "Type": "SystemAssigned" }
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Kdy použít CreateUIDefinition pro spravovanou identitu

Níže jsou uvedená doporučení týkající se použití CreateUIDefinition k povolení spravované identity na spravovaných aplikacích.

- Vytváření spravovaných aplikací projde Azure Portal nebo Marketplace.
- Spravovaná identita vyžaduje komplexní uživatelský vstup.
- Spravovaná identita je nutná při vytváření spravované aplikace.

#### <a name="managed-identity-createuidefinition-control"></a>Spravovaný CreateUIDefinition identity – ovládací prvek

CreateUIDefinition podporuje integrovaný [spravovaný ovládací prvek identity](./microsoft-managedidentity-identityselector.md).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.0.1-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "appName",
            "type": "Microsoft.Common.TextBox",
            "label": "Managed application Name",
            "toolTip": "Managed application instance name",
            "visible": true
          },
          {
            "name": "appIdentity",
            "type": "Microsoft.ManagedIdentity.IdentitySelector",
            "label": "Managed Identity Configuration",
            "toolTip": {
              "systemAssignedIdentity": "Enable system assigned identity to grant the managed application access to additional existing resources.",
              "userAssignedIdentity": "Add user assigned identities to grant the managed application access to additional existing resources."
            },
            "defaultValue": {
              "systemAssignedIdentity": "Off"
            },
            "options": {
              "hideSystemAssignedIdentity": false,
              "hideUserAssignedIdentity": false,
              "readOnlySystemAssignedIdentity": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "applicationResourceName": "[steps('applicationSettings').appName]",
      "location": "[location()]",
      "managedIdentity": "[steps('applicationSettings').appIdentity]"
    }
  }
}
```

![Managed identity CreateUIDefinition](./media/publish-managed-identity/msi-cuid.png)

### <a name="using-azure-resource-manager-templates"></a>Použití šablon Azure Resource Manageru

> [!NOTE]
> Šablony spravované aplikace Marketplace se automaticky generují pro zákazníky, kteří procházejí prostředím Azure Portal vytvořit.
> V těchto scénářích `managedIdentity` musí být výstupní klíč na CreateUIDefinition použit k povolení identity.

Spravovaná identita se dá povolit taky prostřednictvím šablon Azure Resource Manager. Vzorový níže povolí pro spravovanou aplikaci identitu **přiřazenou systémem** . Složitější objekty identity mohou být vytvořeny pomocí parametrů šablony Azure Resource Manager k poskytnutí vstupů. Tyto vstupy se dají použít k sestavování spravovaných aplikací s **uživatelsky přiřazenou identitou**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Kdy použít šablony Azure Resource Manager pro spravovanou identitu

Níže jsou uvedená doporučení týkající se použití Azure Resource Manager šablon pro povolení spravované identity na spravovaných aplikacích.

- Spravované aplikace lze programově nasadit na základě šablony.
- Ke zřízení spravované aplikace je potřeba vlastní přiřazení rolí pro spravovanou identitu.
- Spravovaná aplikace nepotřebuje tok vytváření Azure Portal a Marketplace.

#### <a name="systemassigned-template"></a>Šablona SystemAssigned

Základní Azure Resource Manager šablona, která nasadí spravovanou aplikaci s identitou **přiřazenou systémem** .

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

### <a name="userassigned-template"></a>Šablona UserAssigned

Základní Azure Resource Manager šablona, která nasadí spravovanou aplikaci s **uživatelem přiřazenou identitou**.

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

Po udělení identity spravované aplikaci může být udělen přístup k existujícím prostředkům Azure. Tento postup lze provést prostřednictvím rozhraní IAM (Access Control) v Azure Portal. Název spravované aplikace nebo **uživatelsky přiřazené identity** můžete vyhledat a přidat přiřazení role.

![Přidat přiřazení role pro spravovanou aplikaci](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Propojování existujících prostředků Azure

> [!NOTE]
> Před nasazením spravované aplikace musí být [nakonfigurovaná](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) **uživatelská identita** . Kromě toho se nasazení propojených prostředků u spravovaných aplikací podporuje jenom pro tento druh **Marketplace** .

Spravovaná identita se dá použít taky k nasazení spravované aplikace, která během jejího nasazení vyžaduje přístup k existujícím prostředkům. Když je spravovaná aplikace zřízená zákazníkem, je možné přidat **uživatelsky přiřazené identity** k zajištění dalších autorizací nasazení **mainTemplate** .

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Vytváření CreateUIDefinition s propojeným prostředkem

Při propojování nasazení spravované aplikace s existujícími prostředky je nutné zadat jak existující prostředek Azure, tak **uživatelem přiřazenou identitu** s příslušným přiřazením role daného prostředku.

 Vzorový CreateUIDefinition, který vyžaduje dva vstupy: ID prostředku síťového rozhraní a ID prostředku identity přiřazené uživatelem.

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

Tato CreateUIDefinition.jsv systému generuje prostředí pro vytváření uživatelů, které má dvě pole. První pole umožňuje uživateli zadat ID prostředku Azure pro prostředek propojený s nasazením spravované aplikace. Druhým je, aby příjemce zadal ID prostředku Azure **přiřazené identitě uživatele** , který má přístup k propojenému prostředku Azure. Vygenerované prostředí by vypadalo takto:

![Ukázka CreateUIDefinition se dvěma vstupy: ID prostředku síťového rozhraní a ID prostředku identity přiřazené uživatelem](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Vytváření mainTemplate s propojeným prostředkem

Kromě aktualizace CreateUIDefinition je potřeba aktualizovat také hlavní šablonu, aby přijímala předané ID odkazovaného prostředku. Hlavní šablonu lze aktualizovat tak, aby přijímala nový výstup přidáním nového parametru. Vzhledem k `managedIdentity` tomu, že výstup Přepisuje hodnotu vygenerované šablony spravované aplikace, není předána do hlavní šablony a neměl by být zahrnut do oddílu Parameters.

Ukázková hlavní šablona, která nastavuje profil sítě na stávající síťové rozhraní, které poskytuje CreateUIDefinition.

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

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Využívání spravované aplikace s propojeným prostředkem

Po vytvoření balíčku spravované aplikace je možné spravovanou aplikaci spotřebovat prostřednictvím Azure Portal. Předtím, než bude možné ji spotřebovat, je nutné provést několik požadovaných kroků.

- Musí být vytvořená instance požadovaného propojeného prostředku Azure.
- Je nutné vytvořit **identitu přiřazenou uživatelem** [a přiřadit přiřazení rolí](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) k propojenému prostředku.
- Existující ID propojeného prostředku a ID **identity přiřazené uživatelem** jsou k dispozici pro CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Přístup ke spravovanému tokenu identity

Token spravované aplikace je teď k dispozici prostřednictvím `listTokens` rozhraní API z tenanta vydavatele. Příklad požadavku může vypadat takto:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

Parametry textu žádosti:

Parametr | Povinné | Popis
---|---|---
authorizationAudience | *Ne* | Identifikátor URI ID aplikace cílového prostředku Je to také `aud` deklarace identity (cílová skupina) vydaného tokenu. Výchozí hodnota je " https://management.azure.com/ "
userAssignedIdentities | *Ne* | Seznam uživatelsky přiřazených spravovaných identit, pro které se má načíst token Pokud není zadaný, `listTokens` vrátí token pro spravovanou identitu přiřazenou systémem.


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

Odpověď bude obsahovat pole tokenů pod `value` vlastností:

Parametr | Popis
---|---
access_token | Požadovaný přístupový token
expires_in | Počet sekund, po který bude přístupový token platný
expires_on | Časový interval pro přístup k vypršení platnosti přístupového tokenu Tato hodnota je vyjádřena jako počet sekund od epocha.
not_before | Časové rozpětí, kdy se přístupový token projeví. Tato hodnota je vyjádřena jako počet sekund od epocha.
authorizationAudience | `aud`(Cílová skupina), pro který přístupový token požádal. To se shoduje s tím, co bylo v `listTokens` žádosti zadáno.
resourceId | ID prostředku Azure pro vydaný token. Toto je buď ID spravované aplikace, nebo ID identity přiřazené uživatelem.
token_type | Typ tokenu

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Jak nakonfigurovat spravovanou aplikaci pomocí vlastního zprostředkovatele](../custom-providers/overview.md)
