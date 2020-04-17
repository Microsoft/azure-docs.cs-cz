---
title: Použití parametrů k vytváření dynamických podrobných plánů
description: Přečtěte si o statických a dynamických parametrech a o tom, jak je používat k vytváření zabezpečených a dynamických podrobných plánů.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: e5953617d5fa27098380f3f0e95843c69800f823
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458484"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Vytváření dynamických podrobných plánů prostřednictvím parametrů

Plně definovaný podrobný plán s různými artefakty (například skupiny prostředků, šablony Správce prostředků, zásady nebo přiřazení rolí) nabízí rychlé vytváření a konzistentní vytváření objektů v rámci Azure. Chcete-li povolit flexibilní použití těchto opakovaně použitelných návrhových vzorů a kontejnerů, Azure Blueprints podporuje parametry. Parametr vytváří flexibilitu, a to jak během definice a přiřazení, změnit vlastnosti na artefakty nasazené podrobný plán.

Jednoduchým příkladem je artefakt skupiny prostředků. Při vytvoření skupiny prostředků má dvě požadované hodnoty, které musí být poskytnuty: název a umístění. Při přidávání skupiny prostředků do podrobného plánu, pokud parametry neexistovaly, byste definovat tento název a umístění pro každé použití podrobného plánu. Toto opakování by způsobit každé použití podrobného plánu k vytvoření artefakty ve stejné skupině prostředků. Prostředky uvnitř této skupiny prostředků by se staly duplicitními a způsobily by konflikt.

> [!NOTE]
> Není problém pro dva různé podrobné plány zahrnout skupinu prostředků se stejným názvem.
> Pokud skupina prostředků zahrnutá v podrobném plánu již existuje, podrobný plán pokračuje ve vytváření souvisejících artefaktů v této skupině prostředků. To může způsobit konflikt jako dva prostředky se stejným názvem a typu prostředku nemůže existovat v rámci předplatného.

Řešením tohoto problému jsou parametry. Azure Blueprints umožňuje definovat hodnotu pro každou vlastnost artefaktu během přiřazení k předplatnému. Parametr umožňuje znovu použít podrobný plán, který vytvoří skupinu prostředků a další prostředky v rámci jednoho předplatného bez konfliktu.

## <a name="blueprint-parameters"></a>Parametry podrobného plánu

Prostřednictvím rozhraní REST API parametry lze vytvořit na samotný podrobný plán. Tyto parametry se liší od parametrů na každém z podporovaných artefaktů. Při vytvoření parametru na podrobný plán, může být použit artefakty v tomto podrobném plánu. Příkladem může být předpona pro pojmenování skupiny prostředků. Artefakt můžete použít parametr podrobného plánu k vytvoření "většinou dynamický" parametr. Jako parametr lze také definovat během přiřazení, tento vzor umožňuje konzistenci, která může dodržovat pravidla pojmenování. Kroky naleznete v [tématu nastavení statických parametrů - parametr úrovně podrobného plánu](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Použití parametrů secureString a secureObject

Zatímco _artefakt_ šablony Správce prostředků podporuje parametry typů **secureString** a **secureObject,** Azure Blueprints vyžaduje, aby každý z nich byl propojen s trezorem klíčů Azure. Toto bezpečnostní opatření zabraňuje nebezpečné praxi ukládání tajemství spolu s blueprint a podporuje zaměstnávání bezpečných vzorů. Azure Blueprints podporuje toto bezpečnostní opatření a detekuje zahrnutí buď zabezpečeného parametru do _artefaktu_šablony Správce prostředků . Služba pak během přiřazení zobrazí výzvu pro následující vlastnosti trezoru klíčů na detekovaný zabezpečený parametr:

- ID prostředku trezoru klíčů
- Tajný název trezoru klíčů
- Tajná verze trezoru klíčů

Pokud přiřazení podrobného plánu používá **systémem přiřazenou spravovanou identitu**, _odkazovaný_ trezor klíčů musí existovat ve stejném předplatném, ke kterým je přiřazena definice podrobného plánu.

Pokud přiřazení podrobného plánu používá **uživatelem přiřazenou spravovanou identitu**, _může_ odkazovaný trezor klíčů existovat v centralizovaném předplatném. Spravovaná identita musí být udělena příslušná práva v trezoru klíčů před přiřazením podrobného plánu.

> [!IMPORTANT]
> V obou případech musí mít trezor klíčů **povolení přístupu ke Správci prostředků Azure pro nasazení šablon** nakonfigurované na stránce zásad **přístupu.** Pokyny k povolení této funkce naleznete v tématu [Trezor klíčů – povolení nasazení šablony](../../../azure-resource-manager/managed-applications/key-vault-access.md#enable-template-deployment).

Další informace o azure key vault, najdete v [tématu Key Vault Přehled](../../../key-vault/general/overview.md).

## <a name="parameter-types"></a>Typy parametrů

### <a name="static-parameters"></a>Statické parametry

Hodnota parametru definovaná v definici podrobného plánu se nazývá **statický parametr**, protože každé použití podrobného plánu nasadí artefakt pomocí této statické hodnoty. V příkladu skupiny prostředků, i když to nedává smysl pro název skupiny prostředků, může to mít smysl pro umístění. Potom každé přiřazení podrobného plánu by vytvořit skupinu prostředků, bez ohledu na to, co se nazývá během přiřazení, ve stejném umístění. Tato flexibilita umožňuje být selektivní v tom, co definujete jako požadované vs co lze změnit během přiřazení.

#### <a name="setting-static-parameters-in-the-portal"></a>Nastavení statických parametrů na portálu

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Na stránce vlevo vyberte **definice podrobného plánu.**

1. Klikněte na existující podrobný plán a potom klikněte na **Upravit podrobný plán** nebo klikněte na + Vytvořit podrobný **plán** a vyplňte informace na kartě **Základy.**

1. Klikněte na **Další: Artefakty** nebo klikněte na kartu **Artefakty.**

1. Artefakty přidané do podrobného plánu, které mají možnosti parametrů, zobrazují **parametry X Y navyplněné** ve sloupci **Parametry.** Klikněte na řádek artefaktu upravit parametry artefaktu.

   :::image type="content" source="../media/parameters/parameter-column.png" alt-text="Parametry podrobného plánu v definici podrobného plánu" border="false":::

1. Stránka **Upravit artefakt** zobrazuje možnosti hodnoty odpovídající artefaktu, na který jste klikli. Každý parametr na artefaktmá název, pole hodnoty a zaškrtávací políčko. Nastavte políčko na nezaškrtnuté, aby bylo **statický parametr**. V níže uvedeném příkladu je **statický parametr** pouze _Umístění,_ protože není zaškrtnuto a je zaškrtnuto _políčko Název skupiny prostředků._

   :::image type="content" source="../media/parameters/static-parameter.png" alt-text="Statické parametry podrobného plánu na artefaktu podrobného plánu" border="false":::

#### <a name="setting-static-parameters-from-rest-api"></a>Nastavení statických parametrů z rozhraní REST API

Každý identifikátor URI v REST API používá proměnné, které je potřeba nahradit vašimi vlastními hodnotami:

- Proměnnou `{YourMG}` nahraďte názvem skupiny pro správu.
- Proměnnou `{subscriptionId}` nahraďte ID předplatného.

##### <a name="blueprint-level-parameter"></a>Parametr úrovně podrobného plánu

Při vytváření podrobného plánu prostřednictvím rozhraní REST API je možné vytvořit [parametry podrobného plánu](#blueprint-parameters). Chcete-li tak učinit, použijte následující identifikátor URI rozhraní REST API a formát těla:

- Identifikátor URI v REST API

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- Text žádosti

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

Jakmile je vytvořen parametr úrovně podrobného plánu, lze jej použít na artefakty přidané do tohoto podrobného plánu.
Následující příklad rozhraní REST API vytvoří artefakt přiřazení role v podrobném plánu a použije parametr úrovně podrobného plánu.

- Identifikátor URI v REST API

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- Text žádosti

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

V tomto příkladu **principalIds** vlastnost používá objekt úrovně podrobného **plánu vlastníků** pomocí hodnoty . `[parameters('owners')]` Nastavení parametru na artefaktu pomocí parametru úrovně podrobného plánu je stále příkladem **statického parametru**. Parametr úrovně podrobného plánu nelze nastavit během přiřazení podrobného plánu a bude mít stejnou hodnotu pro každé přiřazení.

##### <a name="artifact-level-parameter"></a>Parametr úrovně artefaktu

Vytváření **statických parametrů** na artefakt je podobné, ale trvá `parameters()` rovnou hodnotu namísto použití funkce. Následující příklad vytvoří dva statické parametry, **tagName** a **tagValue**. Hodnota na každém je přímo k dispozici a nepoužívá volání funkce.

- Identifikátor URI v REST API

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- Text žádosti

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Dynamické parametry

Opakem statického parametru je **dynamický parametr**. Tento parametr není definován v podrobném plánu, ale místo toho je definován během každého přiřazení podrobného plánu. V příkladu skupiny prostředků použití **dynamického parametru** má smysl pro název skupiny prostředků. Poskytuje jiný název pro každé přiřazení podrobného plánu. Seznam funkcí podrobného plánu najdete v odkazu na [funkce podrobného plánu.](../reference/blueprint-functions.md)

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Nastavení dynamických parametrů na portálu

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Na stránce vlevo vyberte **definice podrobného plánu.**

1. Klikněte pravým tlačítkem myši na podrobný plán, který chcete přiřadit. Vyberte **Přiřadit podrobný plán** NEBO klikněte na podrobný plán, který chcete přiřadit, a klikněte na tlačítko Přiřadit podrobný **plán.**

1. Na stránce **Přiřadit podrobný plán** vyhledejte část **Parametry artefaktu.** Každý artefakt s alespoň jedním **dynamickým parametrem** zobrazí artefakt a možnosti konfigurace. Před přiřazením podrobného plánu zadejte požadované hodnoty parametrům. V příkladu níže _Name_ je **dynamický parametr,** který musí být definován k dokončení přiřazení podrobného plánu.

   :::image type="content" source="../media/parameters/dynamic-parameter.png" alt-text="Dynamický parametr podrobného plánu během přiřazení podrobného plánu" border="false":::

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Nastavení dynamických parametrů z rozhraní REST API

Nastavení **dynamických parametrů** během přiřazení se provádí zadáním hodnoty přímo. Namísto použití funkce, jako je [například parameters()](../reference/blueprint-functions.md#parameters), je zadaný hodnota vhodným řetězcem. Artefakty pro skupinu prostředků jsou definovány s "názvem šablony", **názvem**a vlastnostmi **umístění.** Všechny ostatní parametry pro zahrnuté artefakty jsou definovány pod **parametry** s ** \<dvojicí\> ** názvu a **klíče hodnoty.** Pokud je podrobný plán nakonfigurován pro dynamický parametr, který není k dispozici během přiřazení, přiřazení se nezdaří.

- Identifikátor URI v REST API

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- Text žádosti

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>Další kroky

- Podívejte se na seznam [funkcí podrobného plánu](../reference/blueprint-functions.md).
- Další informace o [životním cyklu podrobného plánu](lifecycle.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)