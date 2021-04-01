---
title: Udělení přístupu aplikace k jiným prostředkům Azure
description: V tomto článku se dozvíte, jak udělit přístup k aplikaci Service Fabric s povolenými identitami do dalších prostředků Azure, které podporují ověřování založené na Azure Active Directory.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: c7560294fbf6d122396b6a5a8ffd3ee93bc89048
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97507451"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources"></a>Udělení přístupu spravované identitě Service Fabric aplikace do prostředků Azure

Aby mohla aplikace používat spravovanou identitu pro přístup k jiným prostředkům, musí se jim udělit oprávnění k přístupu k chráněným prostředkům Azure. Udělení oprávnění je obvykle akce správy na řídicí rovině služby Azure vlastnící chráněný prostředek, který je směrován prostřednictvím Azure Resource Manager, což vynutilo jakoukoli platnou kontrolu přístupu založenou na rolích.

Přesné pořadí kroků bude záviset na typu prostředku Azure, ke kterému se přistupoval, a na jazyk nebo klienta, který se používá pro udělení oprávnění. Zbývající část článku předpokládá, že uživatel přiřadil identitu přiřazenou aplikaci a obsahuje několik typických příkladů pro vaše pohodlí, ale není nijak vyčerpávajícím odkazem na toto téma; Projděte si dokumentaci příslušných služeb Azure, kde najdete aktuální pokyny k udělení oprávnění.  

## <a name="granting-access-to-azure-storage"></a>Udělení přístupu k Azure Storage
Pomocí spravované identity aplikace Service Fabric (v tomto případě uživatelem přiřazeného v tomto případě) můžete načíst data z objektu BLOB služby Azure Storage. Udělte identitě požadovaná oprávnění v Azure Portal pomocí následujících kroků:

1. Přejít na účet úložiště
2. Na panelu vlevo klikněte na odkaz Řízení přístupu (IAM).
3. volitelné Kontrola existujícího přístupu: v ovládacím prvku najít vyberte spravovanou identitu přiřazenou systémem nebo uživatelem. Vyberte příslušnou identitu ze seznamu výsledných výsledků.
4. Kliknutím na + přidat přiřazení role v horní části stránky přidejte nové přiřazení role pro identitu aplikace.
V části role v rozevíracím seznamu vyberte čtečka dat objektů BLOB úložiště.
5. V dalším rozevíracím seznamu v části přiřadit přístup k vyberte `User assigned managed identity` .
6. Potom se ujistěte, že v rozevíracím seznamu Předplatné je správné předplatné, a nastavte Skupinu prostředků na Všechny skupiny prostředků.
7. V části Vybrat zvolte UAI odpovídající aplikaci Service Fabric a pak klikněte na Uložit.

Podpora pro spravované identity přiřazené systémem Service Fabric nezahrnuje integraci do Azure Portal; Pokud vaše aplikace používá identitu přiřazenou systémem, budete muset nejprve najít ID klienta identity aplikace a pak opakovat výše uvedené kroky, ale pak vybrat `Azure AD user, group, or service principal` možnost v ovládacím prvku najít.

## <a name="granting-access-to-azure-key-vault"></a>Udělení přístupu k Azure Key Vault
Podobně jako v případě přístupu k úložišti můžete pro přístup k trezoru klíčů Azure využít spravovanou identitu aplikace Service Fabric. Postup pro udělení přístupu v Azure Portal je podobný těm, které jsou uvedené výše, a tady se neopakuje. Rozdíly najdete na obrázku níže.

![Zásada přístupu Key Vault](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

Následující příklad ukazuje udělení přístupu k trezoru prostřednictvím nasazení šablony; Níže uvedené fragmenty kódu přidejte jako jiný záznam v rámci `resources` elementu šablony. Ukázka znázorňuje udělení přístupu pro typy identity přiřazené uživateli i systémem, v uvedeném pořadí – vyberte tu platnou.

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
A pro spravované identity přiřazené systémem:
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

Další podrobnosti najdete v tématu [trezory – zásady přístupu pro aktualizaci](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Další kroky
* [Nasazení aplikace Azure Service Fabric se spravovanou identitou přiřazenou systémem](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Nasazení aplikace Azure Service Fabric s uživatelem přiřazenou spravovanou identitou](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
