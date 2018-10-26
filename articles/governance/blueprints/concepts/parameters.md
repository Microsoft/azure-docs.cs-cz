---
title: Vytváření dynamických plány prostřednictvím parametrů v Azure podrobné plány
description: Další informace o statické a dynamické parametry a jejich používání způsob, jak vytvořit dynamické podrobné plány.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: f6485b01c391ba336799ceb35ee67402b3603585
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093747"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Vytváření dynamických plány prostřednictvím parametrů

Rychlé vytváření konzistentní vytváření objektů v rámci Azure nabízí plně definovaná podrobného plánu pomocí různých artefaktů (jako jsou skupiny prostředků Resource Manageru šablony, zásady a přiřazení rolí). Povolit flexibilní používání těchto opakovaně použitelných návrhových postupů a kontejnery, plány Azure podporuje parametry. Parametr vytvoří flexibilitu, jak během definice a přiřazení, chcete-li změnit vlastnosti na artefakty, které nasadil podrobný plán.

Jednoduchý příklad je artefakt skupiny prostředků. Když se vytvoří skupina prostředků, má dvě požadované hodnoty, které musí být k dispozici: název a umístění. Pokud přidáváte skupinu prostředků pro váš plán parametry tenkrát neexistovaly, byste definovali tento název a umístění pro každý použít podrobný plán. Opakování by způsobit, že každé použití podrobného plánu artefakty vytvořit ve stejné skupině prostředků. Prostředky v příslušné skupině prostředků by být duplicitní a způsobit konflikt.

> [!NOTE]
> Není to problém pro dva různé plány zahrnout skupiny prostředků se stejným názvem.
> Pokud skupinu prostředků, který je součástí podrobný plán už existuje, podrobný plán dál v této skupině prostředků vytvořit související artefakty. To může způsobit konflikt jako dva prostředky se stejným názvem a typ prostředku nemůže existovat v rámci předplatného.

Řešení tohoto problému je parametry. Plány můžete zadat hodnotu pro každou vlastnost artefaktu během přiřazení k předplatnému. Tento parametr umožňuje znovu použít podrobný plán, který vytvoří skupinu prostředků a dalších prostředků v rámci jednoho předplatného bez nutnosti konflikt.

## <a name="blueprint-parameters"></a>Parametry podrobného plánu

Prostřednictvím rozhraní REST API se dají vytvořit na podrobný plán samotné parametry. Tyto parametry se liší od parametry na všech podporovaných artefakty. Když se na podrobný plán parametr, můžete použít pomocí artefaktů v této matrice. Příkladem mohou být předpona pro názvy skupiny prostředků. Artefakt slouží k vytvoření "hlavně dynamický" parametr parametr podrobného plánu. Jako parametr lze definovat také během přiřazení, tento model umožňuje konzistence, která může splňovat pravidla pojmenování. Pokyny najdete v tématu [nastavení statické parametry – podrobný plán úrovně parametr](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Použití parametrů secureString a secureObject

Zatímco šablonu Resource Manageru _artefaktů_ podporuje parametry **secureString** a **secureObject** typy, plány Azure vyžaduje, každá z nich mohla být spojena s Azure Key Vault.
Tato bezpečnostní opatření zabrání unsafe postup ukládání tajných kódů spolu s podrobný plán a může vést ke vzniku zaměstnání zabezpečené vzory. Plány Azure podporuje tato bezpečnostní opatření, zjišťování zahrnutí zabezpečený parametr. buď v šabloně Resource Manageru _artefaktů_. Služba vyzve během přiřazení pro následující vlastnosti služby Key Vault za zjištěné zabezpečený parametr:

- ID prostředku Key Vault
- Název tajného kódu Key Vault
- Verze tajného kódu Key Vault

Odkazované služby Key Vault musí existovat ve stejném předplatném jako podrobný plán je přiřazen.
Musí mít rovněž **povolit přístup k Azure Resource Manageru pro nasazení šablony** nakonfigurované ve službě Key Vault **zásady přístupu** stránky. Pokyny o tom, jak tuto funkci povolit, najdete v části [služby Key Vault – nasazení šablony povolit](../../../managed-applications/key-vault-access.md#enable-template-deployment). Další informace o službě Azure Key Vault najdete v tématu [Key Vault přehled](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Typy parametrů

### <a name="static-parameters"></a>Statické parametery

Hodnota parametru definované v definici plán, podle kterého se volá **statický parametr**, protože každý použít podrobný plán se nasadí pomocí tohoto statické hodnoty artefaktu. V příkladu skupiny prostředků zatímco ho nemá smysl pro název skupiny prostředků může mít smysl pro umístění. Pak každý přiřazení podrobný plán by vytvořte skupinu prostředků, cokoli, co je volána při přiřazení, ve stejném umístění. Díky této flexibilitě umožňuje selektivní v jaké je definovat jako požadované vs co lze změnit během přiřazení.

#### <a name="setting-static-parameters-in-the-portal"></a>Statické parametery nastavení na portálu

1. Klikněte na **všechny služby** a vyhledáte a vyberete **zásady** v levém podokně. Na stránce **Zásady** klikněte na **plány**.

1. Vyberte **definice podrobného plánu** ze stránky na levé straně.

1. Klikněte na existující plán a pak klikněte na tlačítko **upravit podrobný plán** nebo klikněte na tlačítko **+ vytvořit podrobný plán** a vyplňte informace o **Základy** kartu.

1. Klikněte na tlačítko **Další: artefakty** nebo klikněte na **artefakty** kartu.

1. Artefakty do podrobný plán, které mají parametr možnosti Zobrazit **naplněných parametrů X z Y** v **parametry** sloupce. Klikněte na řádek artefaktů můžete upravit parametry artefaktů.

   ![Parametry podrobného plánu](../media/parameters/parameter-column.png)

1. **Upravit artefaktů** stránce se zobrazí hodnota možnosti pro artefakt kliknuli. Každý parametr na artefakt má název, hodnota pole a zaškrtávací políčko. Nastavit pole není zaškrtnuto, aby byl **statický parametr**. V níže uvedeném příkladu, pouze _umístění_ je **statický parametr** je na něm není zaškrtnuto a _název skupiny prostředků_ je zaškrtnuté políčko.

   ![Statické parametry podrobného plánu](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>Nastavení statické parametry z rozhraní REST API

Každý identifikátor URI v REST API používá proměnné, které je potřeba nahradit vašimi vlastními hodnotami:

- Proměnnou `{YourMG}` nahraďte názvem skupiny pro správu.
- Proměnnou `{subscriptionId}` nahraďte ID předplatného.

##### <a name="blueprint-level-parameter"></a>Úroveň parametr podrobného plánu

Při vytváření podrobného plánu prostřednictvím rozhraní REST API, je možné vytvořit [podrobný plán parametry](#blueprint-parameters). Uděláte to tak, použijte následující formát identifikátoru URI REST API a text:

- Identifikátor URI v REST API

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2017-11-11-preview
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

Po vytvoření úrovně parametr podrobného plánu můžete použít na artefakty, které jsou přidány do této matrice.
Následující příklad rozhraní REST API vytvoří artefaktu přiřazení role na podrobný plán a používá úroveň parametr podrobného plánu.

- Identifikátor URI v REST API

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2017-11-11-preview
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

V tomto příkladu **principalIds** používá vlastnost **vlastníky** podrobný plán parametr úrovně s použitím hodnoty z `[parameters('owners')]`. Nastavení parametru na artefakt pomocí parametru level podrobného plánu je stále příkladem **statický parametr**. Parametr podrobného plánu úrovně nemůže být nastavena během přiřazení podrobného plánu a bude mít stejnou hodnotu na každé přiřazení.

##### <a name="artifact-level-parameter"></a>Parametr úrovně artefaktů

Vytváření **statické parametry** na artefakt je podobné, ale má hodnotu přímo namísto použití `parameters()` funkce. Následující příklad vytvoří dvě statické parametry **tagName** a **tagValue**. Hodnota v každém neposkytujeme přímo a nepoužívá volání funkce.

- Identifikátor URI v REST API

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2017-11-11-preview
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

Je opakem statický parametr **dynamického parametru**. Tento parametr není definován na podrobný plán, ale místo toho je definován při každé přiřazení podrobný plán. V příkladu skupiny prostředků, použití **dynamického parametru** dává smysl pro název skupiny prostředků. Poskytuje pro každé přiřazení podrobný plán jiný název.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Nastavení dynamické parametry na portálu

1. Klikněte na **všechny služby** a vyhledáte a vyberete **zásady** v levém podokně. Na stránce **Zásady** klikněte na **plány**.

1. Vyberte **definice podrobného plánu** ze stránky na levé straně.

1. Klikněte pravým tlačítkem na podrobný plán, který chcete přiřadit. Vyberte **přiřazení podrobného plánu** nebo na plán, podle kterého chcete přiřadit, klepněte **přiřazení podrobného plánu** tlačítko.

1. Na **přiřazení podrobného plánu** stránky, vyhledejte **artefaktů parametry** oddílu. Každý artefakt s alespoň jedním **dynamického parametru** zobrazí artefaktů a možnosti konfigurace. Zadejte požadované hodnoty pro parametry před přiřazením podrobný plán. V následujícím příkladu _název_ je **dynamického parametru** , který musí být definován k dokončení přiřazení podrobného plánu.

   ![Dynamický parametr podrobného plánu](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Nastavení dynamických parametrů z rozhraní REST API

Nastavení **dynamických parametrů** během přiřazení, které se provádí tak, že zadáte hodnotu přímo.
Namísto použití funkce, jako například `parameters()`, poskytnutá hodnota je odpovídající řetězec.
Artefakty pro skupinu prostředků, které jsou definovány pomocí "název šablony," **název**, a **umístění** vlastnosti. Všechny ostatní parametry pro artefakt součástí jsou definovány v části **parametry** s **\<název\>** a **hodnotu** pár klíčů. Pokud podrobný plán je nakonfigurovaný pro dynamický parametr, který není k dispozici během přiřazení, přiřazení selže.

- Identifikátor URI v REST API

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2017-11-11-preview
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

## <a name="next-steps"></a>Další postup

- Další informace o [životním cyklu podrobného plánu](lifecycle.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](sequencing-order.md)
- Zjistěte, jak používat [zamykání prostředků podrobného plánu](resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)