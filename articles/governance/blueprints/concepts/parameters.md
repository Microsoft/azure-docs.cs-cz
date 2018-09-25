---
title: Vytváření dynamických plány prostřednictvím parametrů v Azure podrobné plány
description: Další informace o statické a dynamické parametry a jejich používání způsob, jak vytvořit dynamické podrobné plány.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e1a1d736eb9b22cd444a75b94d112abfe3fbe5af
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993574"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Vytváření dynamických plány prostřednictvím parametrů

Plně definovaná podrobného plánu pomocí různých artefaktů (jako jsou skupiny prostředků Resource Manageru šablony, zásady a přiřazení rolí) nabízí rychlé vytváření a konzistentní vytváření objektů v rámci Azure. Povolit flexibilní používání těchto opakovaně použitelných návrhových postupů a kontejnery, plány Azure podporuje parametry. Parametr vytvoří flexibilitu, jak během definice a přiřazení, chcete-li změnit vlastnosti na artefakty, které nasadil podrobný plán.

Jednoduchý příklad je artefakt skupiny prostředků. Když se vytvoří skupina prostředků, má dvě požadované hodnoty, které musí být k dispozici: název a umístění. Pokud přidáváte skupinu prostředků pro váš plán parametry tenkrát neexistovaly, byste definovali tento název a umístění pro každý použít podrobný plán. To by způsobilo každý použít podrobný plán vytváření artefaktů ve stejné skupině prostředků. Když není problém s vybranou skupinou prostředků samotné prostředky v příslušné skupině prostředků by být duplicitní a způsobit konflikt.

> [!NOTE]
> Není to problém pro dva různé plány zahrnout skupiny prostředků se stejným názvem.
> Pokud skupinu prostředků, který je součástí podrobný plán už existuje, podrobný plán dál v této skupině prostředků vytvořit související artefakty. To může způsobit konflikt jako dva prostředky se stejným názvem a typ prostředku nemůže existovat v rámci předplatného.

To je, kde přizpůsobit parametry. Hodnota pro těchto vlastností, v případě názvu skupiny prostředků a umístění, plány vám umožní nedefinuje jejich během definice podrobný plán, ale místo toho definujte jejich hodnoty během přiřazení k předplatnému. Díky tomu je možné znovu použít podrobný plán, který vytvoří skupinu prostředků a dalších prostředků v rámci jednoho předplatného bez nutnosti konflikt.

## <a name="blueprint-parameters"></a>Parametry podrobného plánu

Prostřednictvím rozhraní REST API se dají vytvořit na samotný podrobný plán kromě všech podporovaných artefakty parametry. Když se na podrobný plán parametr, můžete použít pomocí artefaktů v této matrice. Příkladem mohou být předpona pro názvy skupiny prostředků. Artefakt pak můžete použít parametr podrobného plánu vytvářet "hlavně dynamický" parametr, protože parametr může být definován stále během přiřazení, ale bude mít konzistence, která může splňovat pravidla pojmenování organizace. Pokyny najdete v tématu [nastavení statické parametry – podrobný plán úrovně parametr](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Použití parametrů secureString a secureObject

Zatímco šablonu Resource Manageru _artefaktů_ podporuje parametry **secureString** a **secureObject** typy, plány Azure vyžaduje, každá z nich mohla být spojena s Azure Key Vault.
Tím se zabrání unsafe postup ukládání tajných kódů spolu s podrobný plán a může vést ke vzniku zaměstnání zabezpečené vzory. Plány Azure usnadňuje to nalezením zahrnutí zabezpečený parametr. buď v šabloně Resource Manageru _artefaktů_ dotazování během přiřazení podrobného plánu pro následující služby Key Vault vlastností pro jednotlivé zjištěné zabezpečené a parametr:

- ID prostředku Key Vault
- Název tajného kódu Key Vault
- Verze tajného kódu Key Vault

Musí existovat ve stejném předplatném jako podrobný plán je přiřazen k odkazované službě Key Vault a musí mít taky **povolit přístup k Azure Resource Manageru pro nasazení šablony** nakonfigurované ve službě Key Vault **přístup zásady** stránky. Pokyny o tom, jak tuto funkci povolit, najdete v části [služby Key Vault – nasazení šablony povolit](../../../managed-applications/key-vault-access.md#enable-template-deployment).
Další informace o službě Azure Key Vault najdete v tématu [Key Vault přehled](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Typy parametrů

### <a name="static-parameters"></a>Statické parametery

Hodnota parametru definované v definici plán, podle kterého se volá **statický parametr**. Je to proto, že každý použít podrobný plán se nasadí pomocí tohoto statické hodnoty artefaktu. V příkladu skupiny prostředků zatímco to by nedávalo smysl pro název skupiny prostředků může mít smysl pro umístění. Pak každý přiřazení podrobný plán by vytvořte skupinu prostředků, cokoli, co je volána při přiřazení, ve stejném umístění. To umožňuje selektivní v jaké je definovat jako požadované vs co lze změnit během přiřazení.

#### <a name="setting-static-parameters-in-the-portal"></a>Statické parametery nastavení na portálu

1. Spusťte službu Azure plány na webu Azure Portal kliknutím na **všechny služby** a vyhledáte a vyberete **zásady** v levém podokně. Na **zásady** stránky, klikněte na **plány**.

1. Vyberte **definice podrobného plánu** ze stránky na levé straně.

1. Klikněte na existující plán a pak klikněte na tlačítko **upravit podrobný plán** nebo klikněte na tlačítko **+ vytvořit podrobný plán** a vyplňte informace o **Základy** kartu.

1. Klikněte na tlačítko **Další: artefakty** nebo klikněte na **artefakty** kartu.

1. Artefakty do podrobný plán, které mají parametr možnosti Zobrazit **naplněných parametrů X z Y** v **parametry** sloupce. Klikněte na řádek artefaktů můžete upravit parametry artefaktů.

   ![Parametry podrobného plánu](../media/parameters/parameter-column.png)

1. **Upravit artefaktů** stránce se zobrazí hodnota možnosti pro artefakt kliknuli. Každý parametr na artefakt má název, hodnota pole a zaškrtávací políčko. Nastavit pole není zaškrtnuto, aby byl **statický parametr**. V níže uvedeném příkladu, pouze _umístění_ je **statický parametr** je zaškrtnuté políčko a _název skupiny prostředků_ je zaškrtnuté políčko.

   ![Statické parametry podrobného plánu](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>Nastavení statické parametry z rozhraní REST API

V každé identifikátor URI rozhraní API REST jsou proměnné, které se používají, že budete muset nahraďte vlastními hodnotami:

- `{YourMG}` -Nahraďte název skupiny pro správu
- `{subscriptionId}` -Nahraďte ID vašeho předplatného

##### <a name="blueprint-level-parameter"></a>Úroveň parametr podrobného plánu

Při vytváření podrobného plánu prostřednictvím rozhraní REST API, je možné vytvořit [podrobný plán parametry](#blueprint-parameters). Chcete-li to provést, použijte následující formát identifikátoru URI REST API a text:

- IDENTIFIKÁTOR URI ROZHRANÍ API REST

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

- IDENTIFIKÁTOR URI ROZHRANÍ API REST

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

V tomto příkladu **principalIds** vlastnost provedené využívání **vlastníky** podrobný plán úrovně parametr tím, že poskytuje hodnotu `[parameters('owners')]`. Nastavení parametru na artefakt pomocí parametru level podrobného plánu je stále příkladem **statický parametr** nemůže být nastavena během přiřazení podrobného plánu a bude tato hodnota v každé přiřazení.

##### <a name="artifact-level-parameter"></a>Parametr úrovně artefaktů

Vytváření **statické parametry** na artefakt je podobné, ale má hodnotu přímo namísto použití `parameters()` funkce. Následující příklad vytvoří dvě statické parametry **tagName** a **tagValue**. Hodnota v každém neposkytujeme přímo a nepoužívá volání funkce.

- IDENTIFIKÁTOR URI ROZHRANÍ API REST

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

Je opakem statický parametr **dynamického parametru**. Toto je parametr, který není definován na podrobný plán, ale místo toho je definován při každé přiřazení podrobný plán. V příkladu skupiny prostředků to dává smysl pro název skupiny prostředků, poskytuje pro každé přiřazení podrobný plán jiný název.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Nastavení dynamické parametry na portálu

1. Spusťte službu Azure plány na webu Azure Portal kliknutím na **všechny služby** a vyhledáte a vyberete **zásady** v levém podokně. Na **zásady** stránky, klikněte na **plány**.

1. Vyberte **definice podrobného plánu** ze stránky na levé straně.

1. Klikněte pravým tlačítkem na podrobný plán, který chcete přiřadit a vyberte **přiřazení podrobného plánu** nebo na plán, podle kterého chcete přiřadit, klepněte **přiřazení podrobného plánu** tlačítko.

1. Na **přiřazení podrobného plánu** stránky, vyhledejte **artefaktů parametry** oddílu. Každý artefakt s alespoň jedním **dynamického parametru** zobrazí artefaktů a možnosti konfigurace. Zadejte požadované hodnoty pro parametry před přiřazením podrobný plán. V následujícím příkladu _název_ je **dynamického parametru** , který musí být definován k dokončení přiřazení podrobného plánu.

   ![Dynamický parametr podrobného plánu](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Nastavení dynamických parametrů z rozhraní REST API

Nastavení **dynamických parametrů** během přiřazení se uvede požadovanou hodnotu přímo. Namísto použití funkce, jako například `parameters()`, poskytnutá hodnota je odpovídající řetězec. Artefakty pro skupinu prostředků, které jsou definovány pomocí "název šablony," a **název** a **umístění** vlastnosti. Všechny ostatní parametry pro každý artefakt součástí je definován v rámci **parametry** s **\<název\>** a **hodnotu** pár klíčů. Pokud podrobný plán je nakonfigurovaný pro dynamický parametr, který není k dispozici během přiřazení, přiřazení selže.

- IDENTIFIKÁTOR URI ROZHRANÍ API REST

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

- Další informace o [podrobný plán životního cyklu](lifecycle.md)
- Zjistěte, jak přizpůsobit [podrobný plán pořadí řazení](sequencing-order.md)
- Zjistěte, jak se využívání [podrobný plán uzamčení prostředků](resource-locking.md)
- Zjistěte, jak [aktualizovat existující přiřazení](../how-to/update-existing-assignments.md)
- Řešení problémů při přiřazení podrobného plánu se [obecný postup řešení potíží](../troubleshoot/general.md)