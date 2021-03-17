---
title: Použití parametrů k vytváření dynamických modrotisky
description: Přečtěte si o statických a dynamických parametrech a jejich použití k vytváření zabezpečených a dynamických modrotisky.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: 5dbf7ec02e89eac791ec3e17202a5ab13a04b81d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918530"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Vytváření dynamických modrotisky prostřednictvím parametrů

Plně definovaný podrobný plán s různými artefakty, jako jsou skupiny prostředků, šablony Azure Resource Manager (šablony ARM), zásady nebo přiřazení rolí, nabízí rychlé vytváření a konzistentní vytváření objektů v rámci Azure. Aby bylo možné povolit flexibilní používání těchto opakovaně použitelných vzorů a kontejnerů, Azure modrotisky podporuje parametry. Parametr vytvoří flexibilitu v rámci definice i přiřazení pro změnu vlastností artefaktů nasazených podrobným plánem.

Jednoduchým příkladem je artefakt skupiny prostředků. Při vytvoření skupiny prostředků mají dvě požadované hodnoty, které musí být poskytnuty: název a umístění. Při přidávání skupiny prostředků do podrobného plánu, pokud parametry neexistují, byste tento název a umístění definovali pro každé použití podrobného plánu. Toto opakování způsobí, že každé použití podrobného plánu vytvoří artefakty ve stejné skupině prostředků. Prostředky v této skupině prostředků by se staly duplicitními a způsobily konflikt.

> [!NOTE]
> Nejedná se o problém, který by měl obsahovat skupinu prostředků se stejným názvem, protože se nejedná o dva různé plány.
> Pokud skupina prostředků zahrnutá v podrobném plánu již existuje, plán plánu pokračuje v vytváření souvisejících artefaktů v této skupině prostředků. To může způsobit konflikt, protože v rámci předplatného nemůže existovat dva prostředky se stejným názvem a typem prostředku.

Řešením tohoto problému jsou parametry. Plány Azure vám umožňují definovat hodnotu pro každou vlastnost artefaktu během přiřazení k předplatnému. Parametr umožňuje znovu použít podrobný plán, který vytvoří skupinu prostředků a další prostředky v rámci jednoho předplatného, aniž by došlo ke konfliktu.

## <a name="blueprint-parameters"></a>Parametry podrobného plánu

Prostřednictvím REST API lze parametry vytvořit přímo v podrobném plánu. Tyto parametry se liší od parametrů u každého podporovaného artefaktu. Když je v podrobném plánu vytvořen parametr, může být použit artefakty v tomto podrobném plánu. Příkladem může být předpona pro pojmenování skupiny prostředků. Artefakt může pomocí parametru podrobného plánu vytvořit "většinou dynamický" parametr. Vzhledem k tomu, že parametr lze také definovat během přiřazení, tento model umožňuje konzistenci, která může být dodržena pravidla pojmenování. Postup najdete v tématu [Nastavení statických parametrů – parametr úrovně podrobného](#blueprint-level-parameter)plánu.

### <a name="using-securestring-and-secureobject-parameters"></a>Použití parametrů secureString a secureObject

I když _artefakt_ šablony ARM podporuje parametry typů **secureString** a **secureObject** , musí být každý z nich spojen s Azure Key Vault. Tato míra zabezpečení zabraňuje nebezpečným postupům ukládání tajných kódů spolu s podrobným plánem a podporuje práci se zabezpečenými vzory. Azure modrotisky podporují toto opatření zabezpečení a zjišťují zahrnutí zabezpečeného parametru v _artefaktu_ šablony ARM. Služba pak vyzve během přiřazování pro následující Key Vault vlastnosti podle zjištěného zabezpečeného parametru:

- ID prostředku Key Vault
- Key Vault název tajného klíče
- Verze Key Vault tajného klíče

Pokud přiřazení podrobného plánu používá **spravovanou identitu přiřazenou systémem**, _musí_ existovat odkazované Key Vault ve stejném předplatném, ke kterému je přiřazena definice podrobného plánu.

Pokud přiřazení podrobného plánu používá **uživatelem přiřazenou spravovanou identitu**, odkazovaná Key Vault _mohou_ existovat v centralizovaném předplatném. Spravované identitě musí být před přiřazením podrobného plánu udělena příslušná práva k Key Vault.

> [!IMPORTANT]
> V obou případech musí Key Vault mít **povolený přístup k Azure Resource Manager nasazení šablony** nakonfigurované na stránce **zásady přístupu** . Pokyny k povolení této funkce naleznete v tématu [Key Vault-Enable Template Deployment](../../../azure-resource-manager/managed-applications/key-vault-access.md#enable-template-deployment).

Další informace o Azure Key Vault najdete v tématu [Key Vault Overview](../../../key-vault/general/overview.md).

## <a name="parameter-types"></a>Typy parametrů

### <a name="static-parameters"></a>Statické parametry

Hodnota parametru definovaná v definici podrobného plánu se nazývá **statický parametr**, protože při každém použití podrobného plánu dojde k nasazení artefaktu pomocí této statické hodnoty. V příkladu skupiny prostředků, který nemá smysl pro název skupiny prostředků, může být pro danou polohu smysl. Každé přiřazení podrobného plánu pak vytvoří skupinu prostředků bez ohledu na to, kde je volána během přiřazení, ve stejném umístění. Tato flexibilita vám umožní výběr v tom, co definujete podle potřeby a co je možné během přiřazení změnit.

#### <a name="setting-static-parameters-in-the-portal"></a>Nastavení statických parametrů na portálu

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Na levé straně stránky vyberte **definice** podrobného plánu.

1. Vyberte existující podrobný plán a pak vyberte **Upravit podrobný plán** nebo vyberte **+ vytvořit podrobný plán** a vyplňte informace na kartě **základy** .

1. Vyberte **Další: artefakty** nebo vyberte kartu **artefakty** .

1. Artefakty přidané do podrobného plánu, které mají možnosti parametrů, zobrazují **X z parametrů Y naplněné** ve sloupci **Parameters** . Vyberte řádek artefaktu pro úpravu parametrů artefaktu.

   :::image type="content" source="../media/parameters/parameter-column.png" alt-text="Snímek obrazovky s definicí podrobného plánu a zvýrazněnými hodnotami X z parametrů Y" border="false":::

1. Stránka **Upravit artefakt** zobrazuje možnosti hodnot odpovídající vybranému artefaktu. Každý parametr v artefaktu má název, pole hodnoty a zaškrtávací políčko. Nastavte políčko na nezaškrtnuté, aby se mu zajistil **statický parametr**. V následujícím příkladu je pouze _umístění_ **statický parametr** , protože není zaškrtnuto a je zaškrtnuto _pole název skupiny prostředků_ .

   :::image type="content" source="../media/parameters/static-parameter.png" alt-text="Snímek obrazovky se statickými parametry pro artefakt podrobného plánu" border="false":::

#### <a name="setting-static-parameters-from-rest-api"></a>Nastavení statických parametrů z REST API

Každý identifikátor URI v REST API používá proměnné, které je potřeba nahradit vašimi vlastními hodnotami:

- Proměnnou `{YourMG}` nahraďte názvem skupiny pro správu.
- Proměnnou `{subscriptionId}` nahraďte ID předplatného.

##### <a name="blueprint-level-parameter"></a>Parametr úrovně podrobného plánu

Při vytváření podrobného plánu prostřednictvím REST API je možné vytvořit [parametry](#blueprint-parameters)podrobného plánu. K tomu použijte následující REST API identifikátor URI a formát textu:

- Identifikátor URI v REST API

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- Text požadavku

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

Po vytvoření parametru úrovně podrobného plánu jej lze použít pro artefakty přidané do tohoto podrobného plánu.
Následující REST API příklad vytvoří artefakt přiřazení role v podrobném plánu a použije parametr úrovně podrobného plánu.

- Identifikátor URI v REST API

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- Text požadavku

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

V tomto příkladu vlastnost **principalIds** používá parametr na úrovni podrobného plánu **Owners** pomocí hodnoty `[parameters('owners')]` . Nastavení parametru pro artefakt pomocí parametru úrovně podrobného plánu je stále příkladem **statického parametru**. Parametr úrovně podrobného plánu nelze nastavit během přiřazení podrobného plánu a bude mít stejnou hodnotu u každého přiřazení.

##### <a name="artifact-level-parameter"></a>Parametr úrovně artefaktu

Vytváření **statických parametrů** na artefaktu je podobné, ale přebírá rovnou hodnotu namísto použití `parameters()` funkce. Následující příklad vytvoří dva statické parametry, **TagName** a **tagValue**. Hodnota u každého je poskytnuta přímo a nepoužívá volání funkce.

- Identifikátor URI v REST API

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- Text požadavku

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

Opakem statického parametru je **dynamický parametr**. Tento parametr není definován v podrobném plánu, ale je definován během každého přiřazení podrobného plánu. V příkladu skupiny prostředků použití **dynamického parametru** dává smysl pro název skupiny prostředků. Pro každé přiřazení podrobného plánu poskytuje jiný název. Seznam funkcí podrobných plánů najdete v referenčních informacích k [funkcím](../reference/blueprint-functions.md) podrobného plánu.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Nastavení dynamických parametrů na portálu

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Na levé straně stránky vyberte **definice** podrobného plánu.

1. Klikněte pravým tlačítkem na podrobný plán, který chcete přiřadit. Vyberte **přiřadit** podrobný plán nebo vyberte plán, který chcete přiřadit, a pak použijte tlačítko **přiřadit podrobný plán** .

1. Na stránce **přiřadit podrobný plán** Najděte oddíl **parametry artefaktů** . Každý artefakt s alespoň jedním **dynamickým parametrem** zobrazuje artefakt a možnosti konfigurace. Před přiřazením podrobného plánu poskytněte parametrům požadované hodnoty. V následujícím příkladu je _název_ **dynamickým parametrem** , který musí být definován k dokončení přiřazení podrobného plánu.

   :::image type="content" source="../media/parameters/dynamic-parameter.png" alt-text="Snímek obrazovky s nastavením dynamických parametrů během přiřazení podrobného plánu" border="false":::

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Nastavení dynamických parametrů z REST API

Nastavení **dynamických parametrů** během přiřazování je provedeno přímým zadáním hodnoty. Namísto použití funkce, jako jsou například [parametry ()](../reference/blueprint-functions.md#parameters), je poskytnutá hodnota vhodný řetězec. Artefakty pro skupinu prostředků jsou definované s vlastnostmi název šablony, **název** a **umístění** . Všechny ostatní parametry zahrnutého artefaktu jsou definovány v části **parametry** s **\<name\>** dvojicí klíčů a a **hodnot** . Pokud je podrobný plán konfigurován pro dynamický parametr, který není k dispozici během přiřazení, přiřazení se nezdaří.

- Identifikátor URI v REST API

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- Text požadavku

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

- Podívejte se na seznam [funkcí](../reference/blueprint-functions.md)podrobného plánu.
- Další informace o [životním cyklu podrobného plánu](./lifecycle.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](./sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](./resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)