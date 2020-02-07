---
title: Vysvětlení uzamykání prostředků
description: Přečtěte si o možnostech uzamykání v plánech Azure k ochraně prostředků při přiřazování podrobného plánu.
ms.date: 04/24/2019
ms.topic: conceptual
ms.openlocfilehash: e042a4d117e28a2fd2228ce36f1be98a1da31e91
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057341"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Vysvětlení uzamykání prostředků v semodrotiskych Azure

Vytváření konzistentních prostředí se škálováním je skutečně cenné jenom v případě, že existuje mechanismus pro zachování této konzistence. Tento článek vysvětluje, jak funguje uzamykání prostředků v Azure modrotisky. Příklad uzamčení prostředků a použití _přiřazení odepřít_najdete v kurzu [ochrana nových prostředků](../tutorials/protect-new-resources.md) .

## <a name="locking-modes-and-states"></a>Režimy zamykání a stavy

Režim uzamykání se vztahuje na přiřazení podrobného plánu a má tři možnosti: **nezamknout**, jen **pro čtení**nebo **neodstraňovat**. Režim uzamykání se konfiguruje během nasazování artefaktů během přiřazení podrobného plánu. V případě, že aktualizujete přiřazení podrobného plánu, lze nastavit jiný režim uzamykání.
Blokovací režimy se ale nedají změnit mimo plány.

Prostředky vytvořené artefakty v přiřazení podrobného plánu mají čtyři stavy: **Neuzamčeno**, jen **pro čtení**, **nelze je upravit nebo odstranit**nebo **nelze odstranit**. Každý typ artefaktu může být ve stavu **Neuzamčeno** . K určení stavu prostředku lze použít následující tabulku:

|Režim|Typ prostředku artefaktu|State|Popis|
|-|-|-|-|
|Nezamknout|*|Neuzamčeno|Prostředky nejsou chráněny pomocí modrotisky. Tento stav se používá také pro prostředky přidané do **pouze pro čtení** nebo **neodstraňují** artefakt skupiny prostředků z vnějšího přiřazení podrobného plánu.|
|Jen pro čtení|Skupina prostředků|Nelze upravit/odstranit|Skupina prostředků je jen pro čtení a značky ve skupině prostředků nejde upravovat. Do této skupiny prostředků se dají přidat, přesunout, změnit nebo odstranit prostředky, **které nejsou zamčené** .|
|Jen pro čtení|Skupina bez prostředků|Jen pro čtení|Prostředek se nedá změnit jakýmkoli způsobem – bez změn a nedá se odstranit.|
|Neodstraňovat|*|Nejde odstranit|Prostředky je možné změnit, ale nelze je odstranit. Do této skupiny prostředků se dají přidat, přesunout, změnit nebo odstranit prostředky, **které nejsou zamčené** .|

## <a name="overriding-locking-states"></a>Přepsání stavů uzamčení

Pro někoho, kdo má v předplatném příslušné [role](../../../role-based-access-control/overview.md) (RBAC), je obvykle možné, jako je role vlastník, aby bylo možné upravovat nebo odstraňovat jakékoli prostředky. Tento přístup se netýká případu, kdy se při nasazení v rámci nasazeného přiřazení používá jako součást zámky. Pokud bylo přiřazení nastaveno s možností jen **pro čtení** nebo **neodstraňovat** , není ani vlastník předplatného, který může u chráněného prostředku provést akci zablokování.

Tato míra zabezpečení chrání konzistenci definovaného podrobného plánu a prostředí, které bylo navrženo pro vytvoření z náhodného nebo neprogramového odstranění nebo změny.

## <a name="removing-locking-states"></a>Odebírají se stavy zamykání.

Pokud bude nutné upravit nebo odstranit prostředek chráněný přiřazením, existují dva způsoby, jak to provést.

- Aktualizuje se přiřazení podrobného plánu na režim uzamčení **bez zámku** .
- Odstranit přiřazení podrobného plánu

Po odebrání přiřazení se odeberou zámky vytvořené pomocí modrotisky. Prostředek je však ponechán na pozadí a je třeba jej odstranit běžným způsobem.

## <a name="how-blueprint-locks-work"></a>Jak podrobný plán funguje zámky

Pokud přiřazení vybere možnost **jen pro čtení** nebo **neodstraní** , je u prostředků artefaktů při přiřazení podrobného plánu použita akce odepřít odmítnutí [přiřazení](../../../role-based-access-control/deny-assignments.md) . Akce odepřít je přidána spravovanou identitou přiřazení podrobného plánu a lze ji odebrat pouze z prostředků artefaktu pomocí stejné spravované identity. Tato míra zabezpečení vynutila blokovací mechanizmus a zabraňuje odebrání zámku podrobného plánu mimo plány.

![Přiřazení podrobného plánu k zamítnutí ve skupině prostředků](../media/resource-locking/blueprint-deny-assignment.png)

[Vlastnosti přiřazení odepřít](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) pro každý režim jsou následující:

|Režim |Oprávnění. akce |Oprávnění. NotActions |Principals[i].Type |ExcludePrincipals [i]. Účet | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Jen pro čtení |**\*** |**\*/Read** |SystemDefined (všichni) |přiřazení podrobného plánu a uživatelsky definované v **excludedPrincipals** |Skupina prostředků – _pravda_; Prostředek – _NEPRAVDA_ |
|Neodstraňovat |**\*/DELETE** | |SystemDefined (všichni) |přiřazení podrobného plánu a uživatelsky definované v **excludedPrincipals** |Skupina prostředků – _pravda_; Prostředek – _NEPRAVDA_ |

> [!IMPORTANT]
> Azure Resource Manager ukládá do mezipaměti Podrobnosti přiřazení role po dobu až 30 minut. V důsledku toho nemusí být přiřazení zamítnutí akcí Odepřít u prostředků podrobného plánu okamžitě platit. Během této doby může být možné odstranit prostředek určený k ochraně pomocí zámků podrobného plánu.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Vyloučení objektu zabezpečení z přiřazení zamítnutí

V některých scénářích návrhu nebo zabezpečení může být nutné vyřadit objekt zabezpečení z [přiřazení zamítnutí](../../../role-based-access-control/deny-assignments.md) , které přiřazení podrobného plánu vytvoří. To se provádí v REST API přidáním až pěti hodnot do pole **excludedPrincipals** ve vlastnosti **zámky** při [vytváření přiřazení](/rest/api/blueprints/assignments/createorupdate). Toto je příklad textu žádosti, který obsahuje **excludedPrincipals**:

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

Podobně jako při [vyloučení objektu zabezpečení](#exclude-a-principal-from-a-deny-assignment) u [přiřazení zamítnout](../../../role-based-access-control/deny-assignments.md) v přiřazení podrobného plánu můžete vyloučit konkrétní [operace RBAC](../../../role-based-access-control/resource-provider-operations.md). V bloku **Properties. zámků** , na stejném místě, kde **excludedPrincipals** je, lze přidat **excludedActions** :

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

Zatímco **excludedPrincipals** musí být explicitní, **excludedActions** položky můžou využít `*` pro zástupné znaky pro porovnání operací RBAC.

## <a name="next-steps"></a>Další kroky

- Postupujte podle kurzu [ochrany nových prostředků](../tutorials/protect-new-resources.md) .
- Další informace o [životním cyklu podrobného plánu](lifecycle.md)
- Principy použití [statických a dynamických parametrů](parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](sequencing-order.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)