---
title: Vysvětlení uzamykání prostředků
description: Přečtěte si o možnostech uzamykání v plánech Azure k ochraně prostředků při přiřazování podrobného plánu.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: b2004ad294ae0eec1b4f2fc6f49308efd32d652e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920186"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Vysvětlení uzamykání prostředků v semodrotiskych Azure

Vytváření konzistentních prostředí se škálováním je skutečně cenné jenom v případě, že existuje mechanismus pro zachování této konzistence. Tento článek vysvětluje, jak funguje uzamykání prostředků v Azure modrotisky. Příklad uzamčení prostředků a použití _přiřazení odepřít_ najdete v kurzu [ochrana nových prostředků](../tutorials/protect-new-resources.md) .

> [!NOTE]
> Zámky prostředků nasazené v rámci Azure modrotisky se aplikují jenom na prostředky nasazené přiřazením podrobného plánu. Existující prostředky, například ty ve skupinách prostředků, které už existují, nemají přidané zámky.

## <a name="locking-modes-and-states"></a>Režimy zamykání a stavy

Režim uzamykání se vztahuje na přiřazení podrobného plánu a má tři možnosti: **nezamknout**, jen **pro čtení** nebo **neodstraňovat**. Režim uzamykání se konfiguruje během nasazování artefaktů během přiřazení podrobného plánu. V případě, že aktualizujete přiřazení podrobného plánu, lze nastavit jiný režim uzamykání.
Blokovací režimy se ale nedají změnit mimo plány Azure.

Prostředky vytvořené artefakty v přiřazení podrobného plánu mají čtyři stavy: **Neuzamčeno**, jen **pro čtení**, **nelze je upravit nebo odstranit** nebo **nelze odstranit**. Každý typ artefaktu může být ve stavu **Neuzamčeno** . K určení stavu prostředku lze použít následující tabulku:

|Režim|Typ prostředku artefaktu|State|Popis|
|-|-|-|-|
|Nezamknout|*|Neuzamčeno|Prostředky nejsou chráněny pomocí Azure modrotisky. Tento stav se používá také pro prostředky přidané do **pouze pro čtení** nebo **neodstraňují** artefakt skupiny prostředků z vnějšího přiřazení podrobného plánu.|
|Jen pro čtení|Skupina prostředků|Nelze upravit/odstranit|Skupina prostředků je jen pro čtení a značky ve skupině prostředků nejde upravovat. Do této skupiny prostředků se dají přidat, přesunout, změnit nebo odstranit prostředky, **které nejsou zamčené** .|
|Jen pro čtení|Skupina bez prostředků|Jen pro čtení|Prostředek nelze žádným způsobem změnit. Žádné změny a nelze ji odstranit.|
|Neodstraňovat|*|Nejde odstranit|Prostředky je možné změnit, ale nelze je odstranit. Do této skupiny prostředků se dají přidat, přesunout, změnit nebo odstranit prostředky, **které nejsou zamčené** .|

## <a name="overriding-locking-states"></a>Přepsání stavů uzamčení

Pro uživatele s příslušným [řízením přístupu na základě role Azure (RBAC)](../../../role-based-access-control/overview.md) v předplatném, jako je třeba role Owner, je obvykle možné upravovat nebo odstraňovat jakékoli prostředky. Tento přístup neplatí v případě, že Azure modrotisky v rámci nasazeného přiřazení aplikuje uzamykání. Pokud bylo přiřazení nastaveno s možností jen **pro čtení** nebo **neodstraňovat** , není ani vlastník předplatného, který může u chráněného prostředku provést akci zablokování.

Tato míra zabezpečení chrání konzistenci definovaného podrobného plánu a prostředí, které bylo navrženo pro vytvoření z náhodného nebo neprogramového odstranění nebo změny.

### <a name="assign-at-management-group"></a>Přiřadit na skupinu pro správu

Jedinou možností, jak zabránit vlastníkům předplatného odebrání přiřazení podrobného plánu, je přiřazení podrobného plánu ke skupině pro správu. V tomto scénáři mají oprávnění potřebná k odebrání přiřazení podrobného plánu pouze **vlastníci** skupiny pro správu.

Chcete-li přiřadit podrobný plán ke skupině pro správu namísto předplatného, REST API volání bude vypadat takto:

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

Skupina pro správu definovaná nástrojem `{assignmentMG}` musí být buď v rámci hierarchie skupiny pro správu, nebo musí být stejná jako skupina pro správu, kde je uložena definice podrobného plánu.

Text žádosti přiřazení podrobného plánu vypadá takto:

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
        "parameters": {
            "storageAccountType": {
                "value": "Standard_LRS"
            },
            "costCenter": {
                "value": "Contoso/Online/Shopping/Production"
            },
            "owners": {
                "value": [
                    "johnDoe@contoso.com",
                    "johnsteam@contoso.com"
                ]
            }
        },
        "resourceGroups": {
            "storageRG": {
                "name": "defaultRG",
                "location": "eastus"
            }
        }
    }
}
```

Hlavním rozdílem v tomto textu žádosti a jedním přiřazeným k předplatnému je `properties.scope` vlastnost. Tato požadovaná vlastnost musí být nastavena na předplatné, pro které platí přiřazení podrobného plánu. Předplatné musí být přímým podřízeným prvkem hierarchie skupiny pro správu, ve kterém je uloženo přiřazení podrobného plánu.

> [!NOTE]
> Podrobný plán přiřazený k oboru skupiny pro správu stále funguje jako přiřazení podrobného plánu na úrovni předplatného. Jediným rozdílem je, že je uloženo přiřazení podrobného plánu, které brání vlastníkům předplatného odebrání přiřazení a přidružených zámků.

## <a name="removing-locking-states"></a>Odebírají se stavy zamykání.

Pokud bude nutné upravit nebo odstranit prostředek chráněný přiřazením, existují dva způsoby, jak to provést.

- Aktualizuje se přiřazení podrobného plánu na režim uzamčení **bez zámku** .
- Odstranit přiřazení podrobného plánu

Po odebrání přiřazení se odeberou zámky vytvořené v rámci Azure modrotisky. Prostředek je však ponechán na pozadí a je třeba jej odstranit běžným způsobem.

## <a name="how-blueprint-locks-work"></a>Jak podrobný plán funguje zámky

V případě, že přiřazení vybralo možnost **jen pro čtení** nebo **neodstraňovat** , je u prostředků artefaktů použita akce odepřít [přiřazení](../../../role-based-access-control/deny-assignments.md) , která je v systému Azure RBAC. Akce odepřít je přidána spravovanou identitou přiřazení podrobného plánu a lze ji odebrat pouze z prostředků artefaktu pomocí stejné spravované identity. Tato míra zabezpečení vynutila blokovací mechanizmus a zabraňuje odebrání zámku podrobného plánu mimo plány Azure.

:::image type="content" source="../media/resource-locking/blueprint-deny-assignment.png" alt-text="Snímek obrazovky se stránkou řízení přístupu (I a M) a záložkou zamítnutí přiřazení pro skupinu prostředků." border="false":::

[Vlastnosti přiřazení odepřít](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) pro každý režim jsou následující:

|Režim |Oprávnění. akce |Oprávnění. NotActions |Objekty zabezpečení [i]. Textový |ExcludePrincipals [i]. Účet | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Jen pro čtení |**\** _ |_ *\* /Read **<br />** Microsoft. Authorization/uzamyká/odstranit **<br />** Microsoft. Network/virtualNetwork/subnets/JOIN/Action** |SystemDefined (všichni) |přiřazení podrobného plánu a uživatelsky definované v **excludedPrincipals** |Skupina prostředků – _pravda_; Prostředek – _NEPRAVDA_ |
|Neodstraňovat |**\*/Delete** | **Microsoft. Authorization/zámky/DELETE**<br />**Microsoft. Network/virtualNetwork/subnets/JOIN/Action** |SystemDefined (všichni) |přiřazení podrobného plánu a uživatelsky definované v **excludedPrincipals** |Skupina prostředků – _pravda_; Prostředek – _NEPRAVDA_ |

> [!IMPORTANT]
> Azure Resource Manager ukládá do mezipaměti Podrobnosti přiřazení role po dobu až 30 minut. V důsledku toho nemusí být přiřazení zamítnutí akcí Odepřít u prostředků podrobného plánu okamžitě platit. Během této doby může být možné odstranit prostředek určený k ochraně pomocí zámků podrobného plánu.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Vyloučení objektu zabezpečení z přiřazení zamítnutí

V některých scénářích návrhu nebo zabezpečení může být nutné vyřadit objekt zabezpečení z [přiřazení zamítnutí](../../../role-based-access-control/deny-assignments.md) , které přiřazení podrobného plánu vytvoří. Tento krok se provádí v REST API přidáním až pěti hodnot do pole **excludedPrincipals** ve vlastnosti **zámky** při [vytváření přiřazení](/rest/api/blueprints/assignments/createorupdate). Následující definice přiřazení je příkladem těla žádosti, které zahrnuje **excludedPrincipals**:

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="exclude-an-action-from-a-deny-assignment"></a>Vyloučení akce z přiřazení zamítnutí

Podobně jako při [vyloučení objektu zabezpečení](#exclude-a-principal-from-a-deny-assignment) u [přiřazení zamítnout](../../../role-based-access-control/deny-assignments.md) v přiřazení podrobného plánu můžete vyloučit konkrétní [operace poskytovatele prostředků Azure](../../../role-based-access-control/resource-provider-operations.md). V bloku **Properties. zámků** , na stejném místě, kde **excludedPrincipals** je, lze přidat **excludedActions** :

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

Zatímco **excludedPrincipals** musí být explicitní, položky **excludedActions** mohou využívat `*` zástupné znaky pro porovnání s operacemi poskytovatele prostředků.

## <a name="next-steps"></a>Další kroky

- Postupujte podle kurzu [ochrany nových prostředků](../tutorials/protect-new-resources.md) .
- Další informace o [životním cyklu podrobného plánu](./lifecycle.md)
- Principy použití [statických a dynamických parametrů](./parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](./sequencing-order.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)
