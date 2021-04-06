---
title: 'Rychlý Start: nové přiřazení zásad s Terraformu'
description: V tomto rychlém startu použijete syntaxi Terraformu a HCL k vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy.
ms.date: 10/27/2020
ms.topic: quickstart
ms.openlocfilehash: dc4dae2dc6e43e7532117bf64af3ce97ddc7c496
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93106391"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-terraform"></a>Rychlý Start: vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, pomocí Terraformu

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků.
Tento rychlý start vás provede procesem vytvoření přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky.

Na konci tohoto procesu úspěšně identifikujete virtuální počítače, které nepoužívají spravované disky. _Neodpovídají_ přiřazení zásad.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- [Terraformu](https://www.terraform.io/) verze 0.12.0 nebo vyšší nakonfigurovaná ve vašem prostředí.
  Pokyny najdete v tématu [Konfigurace terraformu pomocí Azure Cloud Shell](/azure/developer/terraform/get-started-cloud-shell).
- Tento rychlý Start vyžaduje, abyste spustili Azure CLI verze 2.13.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-the-terraform-configuration-variable-and-output-file"></a>Vytvořit konfiguraci, proměnnou a výstupní soubor Terraformu

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte **virtuální počítače auditu, které nepoužívají definici Managed disks** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Tato definice zásady identifikuje prostředky, které nesplňují podmínky nastavené v definici zásady.

Nejdřív nakonfigurujte konfiguraci, proměnnou a výstupní soubory Terraformu. Terraformu prostředky pro Azure Policy použít [poskytovatele Azure](https://www.terraform.io/docs/providers/azurerm/index.html).

1. Vytvořte novou složku s názvem `policy-assignment` a změňte do ní adresáře.

1. Vytvořte `main.tf` pomocí následujícího kódu:

   ```hcl
   provider "azurerm" {
       version = "~>2.0"
       features {}
   }
   
   resource "azurerm_policy_assignment" "auditvms" {
       name = "audit-vm-manageddisks"
       scope = var.cust_scope
       policy_definition_id = "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d"
       description = "Shows all virtual machines not using managed disks"
       display_name = "Audit VMs without managed disks Assignment"
   }
   ```
1. Vytvořte `variables.tf` pomocí následujícího kódu:

   ```hcl
   variable "cust_scope" {
       default = "{scope}"
   }
   ```

   Obor určuje, pro které prostředky nebo seskupení prostředků se toto přiřazení zásady bude vynucovat. Může být v rozsahu od skupiny pro správu k individuálnímu prostředku. Nezapomeňte nahradit `{scope}` jedním z následujících způsobů:

   - Předplatné: `/subscriptions/{subscriptionId}`
   - Skupina prostředků: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
   - Partner `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

1. Vytvořte `output.tf` pomocí následujícího kódu:

   ```hcl
   output "assignment_id" {
       value = azurerm_policy_assignment.auditvms.id
   }
   ```

## <a name="initialize-terraform-and-create-plan"></a>Inicializovat Terraformu a vytvořit plán

Dále inicializujte Terraformu, abyste stáhli potřebné poskytovatele a pak vytvořili plán.

1. Spusťte příkaz [terraformu init](https://www.terraform.io/docs/commands/init.html) . Tento příkaz stáhne moduly Azure potřebné k vytvoření prostředků Azure v konfiguraci Terraformu.

   ```bash
   terraform init
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-initialize.png" alt-text="Snímek obrazovky s příkazem terraformu init, který ukazuje stažení modulu azurerm a zprávy o úspěchu":::

1. Ověřování pomocí [Azure CLI](/cli/azure/) pro terraformu. Další informace najdete v tématu [poskytovatel Azure: ověřování pomocí Azure CLI](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html).

   ```bash
   az login
   ```

1. Vytvořte plán spouštění pomocí příkazu [terraformu Plan](https://www.terraform.io/docs/commands/plan.html) a parametr **out** .

   ```bash
   terraform plan -out assignment.tfplan
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-plan-out.png" alt-text="Snímek obrazovky s parametrem příkazového řádku terraformu a out a s ním se zobrazí prostředek Azure, který se vytvoří.":::

   > [!NOTE]
   > Informace o zachování plánů provádění a zabezpečení najdete v tématu [plán terraformu: upozornění zabezpečení](https://www.terraform.io/docs/commands/plan.html#security-warning).

## <a name="apply-the-terraform-execution-plan"></a>Použití plánu provádění Terraformu

Nakonec použijte plán spuštění.

Spusťte příkaz [terraformu Apply](https://www.terraform.io/docs/commands/apply.html) a zadejte `assignment.tfplan` již vytvořenou.

```bash
terraform apply assignment.tfplan
```

:::image type="content" source="./media/assign-policy-terraform/terraform-apply.png" alt-text="Snímek obrazovky s příkazem terraformu Applu a výsledným vytvořením prostředku":::

Při použití možnosti použít Hotovo Prostředky: 1 Přidání, 0 změněno, 0 zničeno. zpráva: přiřazení zásad je nyní vytvořeno. Vzhledem k tomu `outputs.tf` , že jsme soubor definovali, je vráceno také _\_ ID přiřazení_ .

## <a name="identify-non-compliant-resources"></a>Zjištění nevyhovujících prostředků

Chcete-li zobrazit prostředky, které nedodržují předpisy v rámci tohoto nového přiřazení, použijte _\_ ID přiřazení_ vrácené `terraform apply` . Spuštěním následujícího příkazu načtete ID prostředků neodpovídajících prostředků, které jsou ve výstupu, do souboru JSON:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Vaše výsledky budou vypadat přibližně jako v následujícím příkladu:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Tyto výsledky jsou srovnatelné s tím, co se obvykle zobrazuje v části **Nekompatibilní prostředky** v zobrazení na webu Azure Portal.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat vytvořené přiřazení, použijte Azure CLI nebo převratte plán spuštění Terraformu s `terraform destroy` .

- Azure CLI

  ```azurecli-interactive
  az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
  ```

- Terraform

  ```bash
  terraform destroy assignment.tfplan
  ```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady pro identifikaci prostředků, které nedodržují předpisy, ve vašem prostředí Azure.

Další informace o přiřazování zásad k ověření, že jsou nové prostředky kompatibilní, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)
