---
title: Udělení přístupu k jiné aplikaci azure prostředky
description: Tento článek vysvětluje, jak udělit aplikaci Service Fabric s podporou spravované identity přístup k dalším prostředkům Azure podporujícím ověřování na základě služby Azure Active Directory.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 3b1feab1e67e993df771564a1a7c1aba4236b2c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614789"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>Udělení přístupu spravované identity aplikace Service Fabric k prostředkům Azure (preview)

Předtím, než aplikace může používat svou spravovanou identitu pro přístup k jiným prostředkům, musí být udělena oprávnění této identitě v chráněném prostředku Azure, ke kterému se přistupuje. Udělení oprávnění je obvykle akce správy na "rovině ovládacího prvku" služby Azure, která vlastní chráněný prostředek směrovaný prostřednictvím Správce prostředků Azure, což vynucuje všechny příslušné kontroly přístupu na základě rolí.

Přesná posloupnost kroků pak bude záviset na typu prostředků Azure, ke kterým se přistupuje, a také na jazyku/klientovi, který se používá k udělení oprávnění. Zbývající část článku předpokládá uživatelem přiřazenou identitu přiřazenou aplikaci a obsahuje několik typických příkladů pro vaše pohodlí, ale v žádném případě není vyčerpávajícím odkazem pro toto téma; Aktuální pokyny k udělování oprávnění naleznete v dokumentaci příslušných služeb Azure.  

## <a name="granting-access-to-azure-storage"></a>Udělení přístupu k Úložišti Azure
Pomocí spravované identity aplikace Service Fabric (v tomto případě přiřazené uživatelem) můžete načíst data z objektu blob úložiště Azure. Udělte identitě požadovaná oprávnění na webu Azure Portal pomocí následujících kroků:

1. Přechod na účet úložiště
2. Na panelu vlevo klikněte na odkaz Řízení přístupu (IAM).
3. (nepovinné) Zkontrolujte existující přístup: v ovládacím prvku "Najít" vyberte spravovanou identitu přiřazenou systémem nebo uživatelem. vyberte příslušnou identitu z následujícího seznamu výsledků
4. Kliknutím na + Přidat přiřazení role v horní části stránky přidáte nové přiřazení role pro identitu aplikace.
V části Role v rozevíracím souboru vyberte Čtečka dat objektů blob úložiště.
5. V dalším rozevíracím souboru v `User assigned managed identity`části Přiřadit přístup zvolte .
6. Potom se ujistěte, že v rozevíracím seznamu Předplatné je správné předplatné, a nastavte Skupinu prostředků na Všechny skupiny prostředků.
7. V části Vybrat zvolte UAI odpovídající aplikaci Service Fabric a klikněte na Uložit.

Podpora systémově přiřazených identit spravovaných služby nezahrnuje integraci na webu Azure Portal; Pokud vaše aplikace používá systémem přiřazenou identitu, budete muset nejprve najít ID klienta identity aplikace a `Azure AD user, group, or service principal` potom opakujte výše uvedené kroky, ale výběr možnosti v ovládacím prvku Najít.

## <a name="granting-access-to-azure-key-vault"></a>Udělení přístupu k úložišti klíčů Azure
Podobně s přístupem k úložišti můžete využít spravovanou identitu aplikace Service Fabric pro přístup k trezoru klíčů Azure. Kroky pro udělení přístupu na webu Azure Portal jsou podobné těm, které jsou uvedeny výše, a tady se nebudou opakovat. Rozdíly naleznete na obrázku níže.

![Zásady přístupu k trezoru klíčů](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

Následující příklad ilustruje udělení přístupu k trezoru prostřednictvím nasazení šablony; přidejte úryvky níže jako další položku `resources` pod prvek šablony. Ukázka ukazuje udělení přístupu pro uživatelem přiřazené i systémem přiřazené typy identit , v uvedeném pořadí - zvolte příslušný.

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
A pro spravované identity přiřazené k systému:
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

Další podrobnosti naleznete v [tématu Trezory – Aktualizovat zásady přístupu](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Další kroky
* [Nasazení aplikace Azure Service Fabric se spravovanou identitou přiřazenou systémem](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Nasazení aplikace Azure Service Fabric s uživatelem přiřazenou spravovanou identitou](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)