---
title: Principy zamykání prostředků
description: Další informace o možnostech uzamčení v Azure Blueprints k ochraně prostředků při přiřazování podrobného plánu.
ms.date: 02/27/2020
ms.topic: conceptual
ms.openlocfilehash: b810e8d4ddd263f9e651704d1bf9b785ce0202db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199695"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Principy zamykání prostředků v azure blueprints

Vytvoření konzistentní prostředí ve velkém měřítku je skutečně cenné pouze v případě, že existuje mechanismus pro zachování této konzistence. Tento článek vysvětluje, jak funguje uzamčení prostředků v Azure Blueprints. Příklad uzamčení prostředků a použití _přiřazení odepření_naleznete v kurzu [ochrany nových prostředků.](../tutorials/protect-new-resources.md)

## <a name="locking-modes-and-states"></a>Režimy a stavy zamykání

Režim uzamčení se vztahuje na přiřazení podrobného plánu a má tři možnosti: **Nezamykat**, **Jen pro čtení**nebo **Neodstraňovat**. Režim uzamčení je konfigurován během nasazení artefaktu během přiřazení podrobného plánu. Jiný režim uzamčení lze nastavit aktualizací přiřazení podrobného plánu.
Režimy uzamčení však nelze změnit mimo podrobné plány.

Prostředky vytvořené artefakty v přiřazení podrobného plánu mají čtyři stavy: **Není uzamčeno**, **jen pro čtení**, nelze upravovat / **odstraňovat**nebo **nelze odstranit**. Každý typ artefaktu může být ve stavu **Není uzamčen.** Následující tabulku lze použít k určení stavu prostředku:

|Mode|Typ prostředku artefaktu|Stav|Popis|
|-|-|-|-|
|Nezamykat|*|Není uzamčeno|Prostředky nejsou chráněny plány. Tento stav se také používá pro prostředky přidané do **artefaktu skupiny jen pro čtení** nebo **Neodstraňovat** artefakt skupiny prostředků z mimo přiřazení podrobného plánu.|
|Jen pro čtení|Skupina prostředků|Nelze upravit / odstranit|Skupina prostředků je jen pro čtení a značky ve skupině prostředků nelze změnit. **Neuzamčené** prostředky lze přidat, přesunout, změnit nebo odstranit z této skupiny prostředků.|
|Jen pro čtení|Skupina bez prostředků|Jen pro čtení|Prostředek nelze žádným způsobem změnit – žádné změny a nelze jej odstranit.|
|Neodstraňovat|*|Nelze odstranit.|Prostředky lze změnit, ale nelze je odstranit. **Neuzamčené** prostředky lze přidat, přesunout, změnit nebo odstranit z této skupiny prostředků.|

## <a name="overriding-locking-states"></a>Přepsání stavů uzamčení

Obvykle je možné, že někdo s příslušným [řízením přístupu na základě rolí](../../../role-based-access-control/overview.md) (RBAC) v předplatném, jako je například role Vlastník, může změnit nebo odstranit libovolný prostředek. Tento přístup není případ, kdy podrobné plány platí uzamčení jako součást nasazené přiřazení. Pokud bylo přiřazení nastaveno pomocí **možnosti Jen pro čtení** nebo **Neodstraňovat,** ani vlastník předplatného nemůže provést blokovanou akci s chráněným prostředkem.

Toto bezpečnostní opatření chrání konzistenci definovaného podrobného plánu a prostředí, které bylo navrženo k vytvoření z náhodného nebo programového odstranění nebo změny.

### <a name="assign-at-management-group"></a>Přiřadit ve skupině pro správu

Další možností, jak zabránit vlastníkům předplatného v odebrání přiřazení podrobného plánu, je přiřadit podrobný plán skupině pro správu. V tomto scénáři pouze **vlastníci** skupiny pro správu mají oprávnění potřebná k odebrání přiřazení podrobného plánu.

Chcete-li přiřadit podrobný plán skupině pro správu namísto předplatného, změní volání rozhraní REST API takto:

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

Skupina pro správu definovaná podle `{assignmentMG}` musí být buď v hierarchii skupiny pro správu, nebo být stejná skupina pro správu, ve které je uložena definice podrobného plánu.

Tělo požadavku přiřazení podrobného plánu vypadá takto:

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

Hlavní rozdíl v tomto textu požadavku a jeden `properties.scope` přiřazen k odběru je vlastnost. Tato požadovaná vlastnost musí být nastavena na předplatné, které se vztahuje na přiřazení podrobného plánu. Předplatné musí být přímým podřízeným objektem hierarchie skupiny pro správu, kde je uloženo přiřazení podrobného plánu.

> [!NOTE]
> Podrobný plán přiřazený k oboru skupiny pro správu stále funguje jako přiřazení podrobného plánu na úrovni předplatného. Jediným rozdílem je, kde je uloženo přiřazení podrobného plánu zabránit vlastníkům předplatného v odebrání přiřazení a přidružené zámky.

## <a name="removing-locking-states"></a>Odebrání stavů uzamčení

Pokud bude nutné upravit nebo odstranit prostředek chráněný přiřazením, existují dva způsoby, jak to provést.

- Aktualizace přiřazení podrobného plánu na zamykací režim **Nezamykatelné**
- Odstranění přiřazení podrobného plánu

Při odebrání přiřazení jsou odebrány zámky vytvořené podrobné plány. Zdroj je však po sobě zanechal a bude nutné odstranit běžnými prostředky.

## <a name="how-blueprint-locks-work"></a>Jak fungují zámky podrobných plánů

RBAC [odepřít přiřazení](../../../role-based-access-control/deny-assignments.md) odepřít akce se použije na artefakt prostředky během přiřazení podrobného plánu, pokud přiřazení vybrané **jen pro čtení** nebo **neodstraňovat** možnost. Akce odepřít je přidán a spravované identity přiřazení podrobného plánu a lze odebrat z prostředků artefaktu pouze stejné spravované identity. Toto bezpečnostní opatření vynucuje mechanismus uzamčení a zabraňuje odebrání zámek podrobného plánu mimo podrobné plány.

![Podrobné zamítne přiřazení ve skupině prostředků](../media/resource-locking/blueprint-deny-assignment.png)

[Vlastnosti přiřazení odepření](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) každého režimu jsou následující:

|Mode |Oprávnění.Akce |Permissions.Notactions |Ředitelé[i]. Typ |ExcludePrincipals[i]. Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Jen pro čtení |**\*** |**\*/číst** |SystemDefined (všichni) |přiřazení podrobného plánu a uživatelem definované v **vyloučených principals** |Skupina prostředků - _true_; Zdroj - _false_ |
|Neodstraňovat |**\*/odstranit** | |SystemDefined (všichni) |přiřazení podrobného plánu a uživatelem definované v **vyloučených principals** |Skupina prostředků - _true_; Zdroj - _false_ |

> [!IMPORTANT]
> Azure Resource Manager ukládá podrobnosti o přiřazení role až na 30 minut. V důsledku toho odepřít přiřazení odepřít akce na podrobný plán prostředky nemusí být okamžitě v plném účinku. Během tohoto časového období může být možné odstranit prostředek určený k ochraně pomocí protokolů podrobný plán.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Vyloučit objekt zabezpečení z přiřazení zamítnutí

V některých scénářích návrhu nebo zabezpečení může být nutné vyloučit objekt zabezpečení z [odepřít přiřazení](../../../role-based-access-control/deny-assignments.md) vytvoří přiřazení podrobného plánu. Tento krok se provádí v rozhraní REST API přidáním až pět hodnot do pole **excludedPrincipals** v **vlastnosti locks** při [vytváření přiřazení](/rest/api/blueprints/assignments/createorupdate). Následující definice přiřazení je příkladem těla požadavku, který obsahuje **vyloučené Principals**:

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

## <a name="exclude-an-action-from-a-deny-assignment"></a>Vyloučení akce ze odepřít přiřazení

Podobně jako [vyloučení jistiny](#exclude-a-principal-from-a-deny-assignment) při [přiřazení odepřít](../../../role-based-access-control/deny-assignments.md) v přiřazení podrobného plánu, můžete vyloučit konkrétní [operace RBAC](../../../role-based-access-control/resource-provider-operations.md). V rámci **bloku properties.locks** na stejném místě, které je **excludedPrincipals,** lze přidat **vyloučené akce:**

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

Zatímco **excludedPrincipals** musí být explicitní, **položky excludedActions** můžete použít `*` pro porovnávání zástupných symbolů operací RBAC.

## <a name="next-steps"></a>Další kroky

- Postupujte podle kurzu [ochrany nových zdrojů.](../tutorials/protect-new-resources.md)
- Přečtěte si o [životním cyklu podrobného plánu](lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](sequencing-order.md).
- Přečtěte si, jak [aktualizovat existující přiřazení](../how-to/update-existing-assignments.md).
- Vyřešte problémy během přiřazení podrobného plánu s [obecným řešením potíží](../troubleshoot/general.md).