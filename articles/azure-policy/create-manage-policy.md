---
title: Vytváření a správa zásad pro vynucování dodržování předpisů v organizaci pomocí služby Azure Policy | Microsoft Docs
description: Pomocí služby Azure Policy můžete vynucovat standardy, plnit legislativní požadavky na audit a dodržování předpisů, řídit náklady, udržovat konzistentní zabezpečení a výkon a uplatňovat principy návrhu v rámci celého podniku.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/19/2018
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 0b6e77d080ce4e4483709b9a5e47dca21c22e1d9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>Vytváření a správa zásad pro vynucování dodržování předpisů

Pochopení způsobu, jakým se v Azure vytvářejí a spravují zásady, je důležité pro zajištění souladu s firemními standardy a smlouvami o úrovni služeb. V tomto kurzu se dozvíte, jak pomocí služby Azure Policy provádět některé běžné úlohy související s vytvářením, přiřazováním a správou zásad v rámci celé organizace, jako například:

> [!div class="checklist"]
> * Přiřazení zásady vynucující podmínku u prostředků, které vytvoříte v budoucnu
> * Vytvoření a přiřazení definice iniciativy pro sledování dodržování předpisů u několika prostředků
> * Řešení problému s prostředkem nedodržujícím předpisy nebo zamítnutým prostředkem
> * Implementace nové zásady v rámci celé organizace

Pokud chcete přiřadit zásadu pro identifikaci aktuálního stavu dodržování předpisů u vašich existujících prostředků, postup najdete v článcích Rychlý start. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="assign-a-policy"></a>Přiřazení zásady

Prvním krokem při vynucování dodržování předpisů pomocí služby Azure Policy je přiřazení definice zásady. Definice zásady definuje, za jakých podmínek se zásada vynucuje a jaká akce se má provést. V tomto příkladu přiřadíte předdefinovanou definici zásady *Vyžadovat SQL Server verze 12.0*, která bude vynucovat podmínku, že všechny databáze SQL Serveru musí být verze 12.0, aby dodržovaly předpisy.

1. Spusťte službu Azure Policy na webu Azure Portal tak, že v levém podokně vyhledáte a vyberete **Zásady**.

   ![Hledání zásad](media/assign-policy-definition/search-policy.png)

2. V levém podokně stránky služby Azure Policy vyberte **Přiřazení**. Přiřazení je zásada, která byla přiřazena, aby proběhla v rámci zadaného oboru.
3. V horní části podokna **Přiřazení** vyberte **Přiřadit zásadu**.

   ![Přiřazení definice zásady](media/create-manage-policy/select-assign-policy.png)

4. Na stránce **Přiřadit zásadu** kliknutím na ![tlačítko Definice zásady](media/assign-policy-definition/definitions-button.png) vedle pole **Zásada** otevřete seznam dostupných definic. Můžete nastavit filtr pro **Typ** definic zásad na *BuiltIn* a zobrazit všechny definice zásad a přečíst si jejich popisy.

   ![Otevření dostupných definic zásad](media/create-manage-policy/open-policy-definitions.png)

5. Vyberte zásadu **Vyžadovat SQL Server verze 12.0**. Pokud se tato zásada nezobrazí rovnou, zadejte do vyhledávacího pole **Vyžadovat SQL Server verze 12.0** a stiskněte ENTER.

   ![Vyhledání zásady](media/create-manage-policy/select-available-definition.png)

6. Zobrazený **Název** se vyplní automaticky, ale můžete ho změnit. Pro účely tohoto příkladu použijte název *Vyžadovat SQL Server verze 12.0*. Volitelně můžete přidat také **Popis**. Popis obsahuje podrobnosti o způsobu, jakým toto přiřazení zásady zajišťuje, aby SQL Servery vytvořené v tomto prostředí byly verze 12.0.

7. Změnou cenové úrovně na **Standard** zajistěte, že se zásada použije na stávající prostředky.

   V rámci služby Azure Policy existují dvě cenové úrovně – *Free* a *Standard*. S úrovní Free můžete vynucovat zásady pouze u budoucích prostředků, zatímco s úrovní Standard je můžete vynucovat také u stávajících prostředků, abyste lépe porozuměli vašemu stavu dodržování předpisů. Vzhledem k tomu, že je služba Azure Policy ve verzi Preview, ještě není nezveřejněný cenový model, takže za výběr úrovně *Standard* neobdržíte žádnou fakturu. Další informace o cenách najdete v tématu [Ceny služby Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy).

8. Vyberte **Rozsah** – předplatné (nebo skupinu prostředků), které jste zaregistrovali dříve. Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat. Může sahat od předplatného až po skupinu prostředků.

   V tomto příkladu se používá předplatné **Azure Analytics Capacity Dev**. Vaše předplatné se bude lišit.

10. Vyberte **Přiřadit**.

## <a name="implement-a-new-custom-policy"></a>Implementace nové vlastní zásady

Teď, když jste přiřadili předdefinovanou definici zásady, můžete se službou Azure Policy provádět další akce. Dále vytvořte novou vlastní zásadu pro úsporu nákladů díky zajištění, že virtuální počítače vytvořené ve vašem prostředí nemůžou být v řadě G Series. Díky tomu se zamítnou všechny žádosti uživatelů ve vaší organizaci o vytvoření virtuálního počítače v řadě G Series.

1. V levém podokně v části **Vytváření obsahu** vyberte **Definice**.

   ![Definice v části Vytváření obsahu](media/create-manage-policy/definition-under-authoring.png)

2. Vyberte **+ Definice zásady**.
3. Zadejte následující:

   - Název definice zásady – *Vyžadovat nižší skladové položky virtuálních počítačů než G Series*.
   - Popis účelu definice zásady – Tato definice zásady za účelem snížení nákladů vynucuje, aby všechny virtuální počítače vytvořené v tomto rozsahu měly skladové položky nižší než G Series.
   - Předplatné, ve kterém se definice zásady nachází. V tomto případě se definice zásady nachází v předplatném **Advisor Analytics Capacity Dev**. Seznam vašich předplatných se bude lišit.
   - Zvolte některou z existujících možností nebo pro tuto definici zásady vytvořte novou kategorii.
   - Zkopírujte následující kód JSON a pak v něm podle potřeby aktualizujte:
      - Parametry zásady.
      - Pravidla a podmínky zásady, v tomto případě – velikost skladové položky virtuálního počítače se rovná řadě G Series.
      - Účinek zásady, v tomto případě – **Deny** (Zamítnutí).

    Tady je ukázka kódu JSON. Vložte svůj upravený kód na web Azure Portal.

    ```json
{
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/sku.name",
            "like": "Standard_G*"
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }
}
    ```

    *Vlastnost field* v pravidle zásady musí obsahovat některou z následujících hodnot: název, typ, umístění, značky nebo alias. Například, `"Microsoft.Compute/VirtualMachines/Size"`.

    Další ukázky kódu JSON najdete v článku [Šablony pro službu Azure Policy](json-samples.md).

4. Vyberte **Uložit**.

## <a name="create-a-policy-definition-with-rest-api"></a>Vytvoření definice zásady pomocí rozhraní REST API

Zásadu můžete vytvořit i pomocí rozhraní REST API pro definice zásad. Toto rozhraní API umožňuje vytvářet a odstraňovat definice zásad a získávat informace o existujících definicích.
Pokud chcete vytvořit definici zásady, použijte následující příklad:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

```
Přiložte podobný text žádosti jako v následujícím příkladu:

```
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>Vytvoření definice zásady pomocí PowerShellu

Než budete pokračovat k příkladu PowerShellu, ujistěte se, že máte nainstalovanou nejnovější verzi Azure PowerShellu. Parametry zásady byly přidány ve verzi 3.6.0. Pokud máte starší verzi, uvedené příklady vrátí chybu, která značí, že se parametr nenašel.

Definici zásady můžete vytvořit pomocí rutiny `New-AzureRmPolicyDefinition`.

Pokud chcete vytvořit definici zásady ze souboru, předejte cestu k souboru. Pro externí soubor použijte následující příklad:

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -DisplayName "Deny cool access tiering for storage" `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Pro místní soubor použijte následující příklad:

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -Description "Deny cool access tiering for storage" `
    -Policy "c:\policies\coolAccessTier.json"
```

Pokud chcete vytvořit definici zásady s vloženým pravidlem, použijte následující příklad:

```
$definition = New-AzureRmPolicyDefinition -Name denyCoolTiering -Description "Deny cool access tiering for storage" -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```

Výstup se ukládá v objektu `$definition`, který se používá při přiřazování zásady.
Následující příklad vytvoří definici zásady zahrnující parametry:

```
$policy = '{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying storage accounts.",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters
```

## <a name="view-policy-definitions"></a>Zobrazení definic zásad

Pokud chcete zobrazit všechny definice zásad ve svém předplatném, použijte následující příkaz:

```
Get-AzureRmPolicyDefinition
```

Příkaz vrátí všechny dostupné definice zásad, včetně předdefinovaných zásad. Všechny zásady se vrátí v následujícím formátu:

```
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Vytvoření definice zásady pomocí Azure CLI

V Azure CLI můžete vytvořit definici zásady pomocí příkazu policy definition.
Pokud chcete vytvořit definici zásady s vloženým pravidlem, použijte následující příklad:

```
az policy definition create --name denyCoolTiering --description "Deny cool access tiering for storage" --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```

## <a name="view-policy-definitions"></a>Zobrazení definic zásad

Pokud chcete zobrazit všechny definice zásad ve svém předplatném, použijte následující příkaz:

```
az policy definition list
```

Příkaz vrátí všechny dostupné definice zásad, včetně předdefinovaných zásad. Všechny zásady se vrátí v následujícím formátu:

```
{                                                            
  "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",                      
  "displayName": "Allowed locations",
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "in": "[parameters('listOfAllowedLocations')]"
      }
    },
    "then": {
      "effect": "Deny"
    }
  },
  "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Vytvoření a přiřazení definice iniciativy

Pomocí definice iniciativy můžete seskupit několik definic zásad za účelem dosažení jednoho zastřešujícího cíle. Vytvoříte definici iniciativy zajišťující soulad prostředků v rozsahu této definice s definicemi zásad, ze kterých se definice iniciativy skládá.  Další informace o definicích iniciativ najdete v tématu [Přehled služby Azure Policy](./azure-policy-introduction.md).

### <a name="create-an-initiative-definition"></a>Vytvoření definice iniciativy

1. V levém podokně v části **Vytváření obsahu** vyberte **Definice**.

   ![Výběr definic](media/create-manage-policy/select-definitions.png)

2. V horní části stránky vyberte **Definice iniciativy**. Tento výběr vás přesměruje na formulář **Definice iniciativy**.
3. Zadejte název a popis iniciativy.

   V tomto příkladu zajistíte soulad prostředků s definicemi zásad souvisejících se zajištěním zabezpečení. Proto bude název iniciativy **Zajištění zabezpečení** a popis bude: **Tato iniciativa byla vytvořená za účelem zpracování všech definic zásad souvisejících se zabezpečením prostředků**.

   ![Definice iniciativy](media/create-manage-policy/initiative-definition.png)

4. Projděte seznam **dostupných definic** a vyberte definice zásad, které chcete přidat do této iniciativy. Pro naši iniciativu **Zajištění zabezpečení** **přidejte** následující předdefinované definice zásad:
   - Vyžadovat SQL Server verze 12.0
   - Monitorovat nechráněné webové aplikace ve službě Security Center
   - Monitorovat síť s mírným nastavením ve službě Security Center
   - Monitorovat možné přidávání aplikací na seznam povolených ve službě Security Center
   - Monitorovat nešifrované disky virtuálních počítačů ve službě Security Center

   ![Definice iniciativ](media/create-manage-policy/initiative-definition-2.png)

   Po výběru ze seznamu se definice zásad zobrazí v části **Zásady a parametry**, jak je znázorněno na předchozím obrázku.

5. Prostřednictvím **Umístění definice** vyberte předplatné, do kterého se definice uloží. Vyberte **Uložit**.

### <a name="assign-an-initiative-definition"></a>Přiřazení definice iniciativy

1. Přejděte na kartu **Definice** v části **Vytváření obsahu**.
2. Vyhledejte definici iniciativy **Zajištění zabezpečení**, kterou jste vytvořili.
3. Vyberte definici iniciativy a pak vyberte **Přiřadit**.

   ![Přiřazení definice](media/create-manage-policy/assign-definition.png)

4. Vyplňte formulář **Přiřazení** zadáním následujících ukázkových údajů. Můžete použít vlastní údaje.
   - Název: Přiřazení iniciativy Zajištění zabezpečení
   - Popis: Toto přiřazení iniciativy je přizpůsobené k vynucování této skupiny definic zásad v předplatném **Azure Advisor Capacity Dev**.
   - Cenová úroveň: Standard
   - Rozsah, na který chcete toto přiřazení použít: **Azure Advisor Capacity Dev**. Můžete zvolit vlastní předplatné a skupinu prostředků.

5. Vyberte **Přiřadit**.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Vyloučení prostředku nedodržujícího předpisy nebo zamítnutého prostředku s využitím vyloučení

Ve výše uvedeném příkladu se po přiřazení definice zásady vyžadující SQL Server verze 12.0 zamítne vytvoření SQL Serveru s jinou verzí než 12.0. V této části si projdete řešení zamítnutého pokusu o vytvoření SQL Serveru pomocí žádosti o vyloučení konkrétních prostředků. Vyloučení v podstatě brání vynucování zásad. V následujícím příkladu se povolí jakákoli verze SQL Serveru. Vyloučení se může vztahovat na skupinu prostředků nebo ho můžete zúžit na jednotlivé prostředky.

1. V levém podokně vyberte **Přiřazení**.
2. Projděte všechna přiřazení zásad a otevřete přiřazení *Vyžadovat SQL Server verze 12.0*.
3. **Vyberte** vyloučení pro prostředky ve skupinách prostředků, ve kterých se pokoušíte vytvořit SQL Server. V tomto příkladu vylučte Microsoft.Sql/servers/databases: *azuremetrictest/testdb* a *azuremetrictest/testdb2*.

   ![Žádost o vyloučení](media/create-manage-policy/request-exclusion.png)

   Mezi další způsoby, jak řešit problém se zamítnutým prostředkem, patří: Oslovení kontaktu přiřazeného k zásadě, pokud je vaše potřeba vytvořit SQL Server pádně odůvodněná, a přímá úprava zásady, pokud k ní máte přístup.

4. Klikněte na **Přiřadit**.

V této části jste vyřešili zamítnutí pokusu o vytvoření SQL Serveru tím, že jste zažádali o vyloučení příslušných prostředků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v práci s dalšími kurzy, neprovádějte čištění prostředků vytvořených v rámci této příručky. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechna přiřazení a definice, které jste vytvořili výše:

1. V levém podokně vyberte **Definice** (nebo **Přiřazení**, pokud se pokoušíte odstranit přiřazení).
2. Vyhledejte novou definici iniciativy nebo zásady (nebo přiřazení), kterou jste právě vytvořili.
3. Vyberte tři tečky na konci definice nebo přiřazení a pak vyberte **Odstranit definici** (nebo **Odstranit přiřazení**).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste úspěšně provedli následující:

> [!div class="checklist"]
> * Přiřadili jste zásadu vynucující podmínku u prostředků, které vytvoříte v budoucnu.
> * Vytvořili a přiřadili jste definici iniciativy pro sledování dodržování předpisů u několika prostředků.
> * Vyřešili jste problém s prostředkem nedodržujícím předpisy nebo zamítnutým prostředkem.
> * Implementovali jste novou zásadu v rámci celé organizace.

Další informace o strukturách definic zásad najdete v tomto článku:

> [!div class="nextstepaction"]
> [Struktura definic Azure Policy](policy-definition.md)
