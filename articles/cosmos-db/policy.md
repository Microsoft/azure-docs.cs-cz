---
title: Použití Azure Policy k implementaci zásad správného řízení a řízení prostředků Azure Cosmos DB
description: Naučte se používat Azure Policy k implementaci zásad správného řízení a řízení prostředků Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 1390f5db6e0f0370788bef60d5a2cafee1e8a96d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93080647"
---
# <a name="use-azure-policy-to-implement-governance-and-controls-for-azure-cosmos-db-resources"></a>Použití Azure Policy k implementaci zásad správného řízení a řízení prostředků Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

[Azure Policy](../governance/policy/overview.md) pomáhá vyhovět standardům zásad správného řízení organizace, vyhodnocovat dodržování předpisů prostředků a implementovat automatickou nápravu. Mezi běžné případy použití patří zabezpečení, Správa nákladů a konzistence konfigurace.

Azure Policy poskytuje předdefinované definice zásad. Můžete vytvořit vlastní definice zásad pro scénáře, které nejsou řešeny integrovanými definicemi zásad. Další podrobnosti najdete v [dokumentaci k Azure Policy](../governance/policy/overview.md) .

> [!IMPORTANT]
> Azure Policy se vynutilo na úrovni poskytovatele prostředků pro služby Azure. Sady Cosmos DB SDK můžou provádět většinu operací správy v databázi, kontejneru a propustnosti prostředků, které obcházejí poskytovatele prostředků Cosmos DB, takže se ignorují všechny zásady vytvořené pomocí Azure Policy. Aby bylo zajištěno vynucování zásad, přečtěte si téma [prevence změn ze sady Azure Cosmos DB SDK](role-based-access-control.md#prevent-sdk-changes) .

## <a name="assign-a-built-in-policy-definition"></a>Přiřazení definice předdefinované zásady

Definice zásad popisují podmínky dodržování předpisů prostředků a efekt, který se má provést, pokud je splněna podmínka. _Přiřazení_ zásad se vytvářejí z _definic_ zásad. Můžete použít předdefinované nebo vlastní definice zásad pro prostředky Azure Cosmos DB. Přiřazení zásad mají rozsah pro skupinu pro správu Azure, předplatné Azure nebo skupinu prostředků a používají se pro prostředky v rámci vybraného oboru. Volitelně můžete z oboru vyloučit konkrétní prostředky.

Přiřazení zásad můžete vytvořit pomocí šablony [Azure Portal](../governance/policy/assign-policy-portal.md), [Azure POWERSHELL](../governance/policy/assign-policy-powershell.md), [Azure CLI](../governance/policy/assign-policy-azurecli.md)nebo [ARM](../governance/policy/assign-policy-template.md).

Pokud chcete vytvořit přiřazení zásady z předdefinované definice zásad pro Azure Cosmos DB, použijte postup v tématu [vytvoření přiřazení zásady s Azure Portal](../governance/policy/assign-policy-portal.md) článkem.

V kroku vyberte definici zásady, `Cosmos DB` do vyhledávacího pole zadejte a vyfiltrujte seznam dostupných definicí integrovaných zásad. Vyberte jednu z dostupných integrovaných definic zásad a pak zvolte **Vybrat** a pokračujte v vytváření přiřazení zásad.

> [!TIP]
> Pomocí předdefinovaných názvů definic zásad, které jsou uvedené v podokně **dostupné definice** pomocí Azure PowerShell, Azure CLI nebo šablon ARM, můžete také vytvořit přiřazení zásad.

:::image type="content" source="./media/policy/available-definitions.png" alt-text="Vyhledat Azure Cosmos DB předdefinované definice zásad":::

## <a name="create-a-custom-policy-definition"></a>Vytvoření vlastní definice zásad

V případě konkrétních scénářů, které nejsou řešeny pomocí integrovaných zásad, můžete vytvořit [vlastní definici zásad](../governance/policy/tutorials/create-custom-policy-definition.md). Později vytvoříte _přiřazení_ zásady z vlastní _definice_ zásady.

### <a name="property-types-and-property-aliases-in-policy-rules"></a>Typy vlastností a aliasy vlastností v pravidlech zásad

Pomocí [vlastních kroků definice zásad](../governance/policy/tutorials/create-custom-policy-definition.md) Identifikujte vlastnosti prostředku a aliasy vlastností, které jsou nutné k vytvoření pravidel zásad.

Chcete-li identifikovat aliasy specifických vlastností Azure Cosmos DB, použijte obor názvů `Microsoft.DocumentDB` s jednou z metod, které jsou uvedeny v článku o krocích definice vlastních zásad.

#### <a name="use-the-azure-cli"></a>Použijte rozhraní příkazového řádku Azure:
```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for namespace Microsoft.DocumentDB
az provider show --namespace Microsoft.DocumentDB --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

#### <a name="use-azure-powershell"></a>Použít Azure PowerShell:
```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.DocumentDB namespace
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.DocumentDB').Aliases
```

Tyto příkazy vypíše seznam názvů aliasů vlastností pro vlastnost Azure Cosmos DB. Níže je výpis z výstupu:

```json
[
  "Microsoft.DocumentDB/databaseAccounts/sku.name",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*]",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*].id",
  "Microsoft.DocumentDB/databaseAccounts/isVirtualNetworkFilterEnabled",
  "Microsoft.DocumentDB/databaseAccounts/consistencyPolicy.defaultConsistencyLevel",
  "Microsoft.DocumentDB/databaseAccounts/enableAutomaticFailover",
  "Microsoft.DocumentDB/databaseAccounts/Locations",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*]",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*].locationName",
  "..."
]
```

V [pravidlech definice vlastních zásad](../governance/policy/tutorials/create-custom-policy-definition.md#policy-rule)můžete použít libovolný název aliasu této vlastnosti.

Následuje příklad definice zásady, která kontroluje, jestli je účet Azure Cosmos DB nakonfigurovaný pro více umístění pro zápis. Definice vlastní zásady zahrnuje dvě pravidla: jednu pro kontrolu konkrétního typu aliasu vlastnosti a druhou pro konkrétní vlastnost typu v tomto případě pole, ve kterém je uložené nastavení vícenásobného umístění pro zápis. Obě pravidla používají názvy aliasů.

```json
"policyRule": {
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DocumentDB/databaseAccounts"
      },
      {
        "field": "Microsoft.DocumentDB/databaseAccounts/enableMultipleWriteLocations",
        "notEquals": true
      }
    ]
  },
  "then": {
    "effect": "Audit"
  }
}
```

Vlastní definice zásad se dají použít k vytvoření přiřazení zásad stejným způsobem, jako jsou použité definice předdefinovaných zásad.

## <a name="policy-compliance"></a>Dodržování zásad

Po vytvoření přiřazení zásad Azure Policy vyhodnotí prostředky v oboru přiřazení. Vyhodnotí se _dodržování předpisů_ u jednotlivých prostředků. _Účinek_ zadaný v zásadách se pak použije na prostředky, které nedodržují předpisy.

Výsledky dodržování předpisů a podrobnosti o nápravě můžete zkontrolovat v [Azure Portal](../governance/policy/how-to/get-compliance-data.md#portal) nebo prostřednictvím rozhraní příkazového [řádku Azure CLI](../governance/policy/how-to/get-compliance-data.md#command-line) nebo [protokolu Azure monitor](../governance/policy/how-to/get-compliance-data.md#azure-monitor-logs).

Následující snímek obrazovky ukazuje dva příklady přiřazení zásad.

Jedno přiřazení vychází z předdefinované definice zásady, která kontroluje, jestli jsou prostředky Azure Cosmos DB nasazené jenom do oblastí Azure, které jsou povolené. Dodržování prostředků zobrazuje výsledek vyhodnocení zásad (kompatibilní nebo nekompatibilní) pro prostředky v oboru.

Druhé přiřazení je založené na definici vlastní zásady. Toto přiřazení kontroluje, že účty Cosmos DB jsou nakonfigurovány pro více umístění pro zápis.

Po nasazení přiřazení zásad zobrazí řídicí panel dodržování výsledků hodnocení. Všimněte si, že to může trvat až 30 minut po nasazení přiřazení zásady. Kromě toho je [možné na vyžádání spustit kontroly vyhodnocení zásad](../governance/policy/how-to/get-compliance-data.md#on-demand-evaluation-scan) hned po vytvoření přiřazení zásad.

Snímek obrazovky ukazuje následující výsledky vyhodnocení dodržování předpisů pro Azure Cosmos DB účty v oboru:

- Žádná ze dvou účtů vyhovuje zásadám, které Virtual Network (VNet) musí nakonfigurovat filtrování.
- Žádná ze dvou účtů nedodržuje zásady, které vyžadují, aby byl účet nakonfigurovaný pro více umístění pro zápis.
- Žádná ze dvou účtů nedodržuje zásady, které byly nasazeny do povolených oblastí Azure.

:::image type="content" source="./media/policy/compliance.png" alt-text="Výsledky dodržování předpisů pro přiřazení Azure Policy v seznamu":::

Chcete-li opravit prostředky, které nedodržují předpisy, přečtěte si [článek o nápravě prostředků pomocí Azure Policy](../governance/policy/how-to/remediate-resources.md).

## <a name="next-steps"></a>Další kroky

- [Projděte si ukázkové definice vlastních zásad pro Azure Cosmos DB](https://github.com/Azure/azure-policy/tree/master/samples/CosmosDB), včetně výše uvedených zásad pro víc umístění zápisu a filtrování virtuální sítě.
- [Vytvoření přiřazení zásady v Azure Portal](../governance/policy/assign-policy-portal.md)
- [Přečtěte si Azure Policy předdefinované definice zásad pro Azure Cosmos DB](./policy-reference.md)