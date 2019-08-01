---
title: Seznámení s Terraformu v Azure.
description: Začněte s Terraformu v Azure nasazením Azure Cosmos DB a Azure Container Instances.
services: terraform
author: neilpeterson
ms.service: azure
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: nepeters
ms.openlocfilehash: 57ab3fbc584932cb7d08bda76530bbe95ce61a6f
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699092"
---
# <a name="create-a-terraform-configuration-for-azure"></a>Vytvoření konfigurace Terraformu pro Azure

V tomto příkladu získáte zkušenosti s vytvořením konfigurace Terraformu a nasazením této konfigurace do Azure. Po dokončení budete mít nasazenou instanci Azure Cosmos DB, instanci kontejneru Azure a aplikaci, která funguje v těchto dvou prostředcích. V tomto dokumentu se předpokládá, že je dokončená veškerá práce v Azure Cloud Shell, která má předinstalované nástroje Terraformu Tool. Pokud chcete pracovat s příkladem ve vašem vlastním systému, Terraformu se dá nainstalovat pomocí pokynů, které najdete [tady](../virtual-machines/linux/terraform-install-configure.md).

## <a name="create-first-configuration"></a>Vytvořit první konfiguraci

V této části vytvoříte konfiguraci pro instanci Azure Cosmos DB.

Vyberte **vyzkoušet a teď** otevřete Azure Cloud Shell. Po otevření zadejte `code .` do a otevřete Editor kódu pro Cloud Shell.

```azurecli-interactive
code .
```

Zkopírujte a vložte následující konfiguraci Terraformu.

Tato konfigurace modeluje skupinu prostředků Azure, náhodné celé číslo a instanci Azure Cosmos DB. V názvu Cosmos DB instance se používá náhodné celé číslo. Nakonfigurovali jste taky několik nastavení Cosmos DB. Úplný seznam Cosmos DB konfiguracích Terraformu najdete v [referenčních informacích Cosmos DB terraformu](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html).

`main.tf` Po dokončení soubor uložte. Tuto operaci lze provést pomocí tří teček v pravé horní části editoru kódu.

```azurecli-interactive
resource "azurerm_resource_group" "vote-resource-group" {
  name     = "vote-resource-group"
  location = "westus"
}

resource "random_integer" "ri" {
  min = 10000
  max = 99999
}

resource "azurerm_cosmosdb_account" "vote-cosmos-db" {
  name                = "tfex-cosmos-db-${random_integer.ri.result}"
  location            = "${azurerm_resource_group.vote-resource-group.location}"
  resource_group_name = "${azurerm_resource_group.vote-resource-group.name}"
  offer_type          = "Standard"
  kind                = "GlobalDocumentDB"

  consistency_policy {
    consistency_level       = "BoundedStaleness"
    max_interval_in_seconds = 10
    max_staleness_prefix    = 200
  }

  geo_location {
    location          = "westus"
    failover_priority = 0
  }
}
```

Příkaz [terraformu init](https://www.terraform.io/docs/commands/init.html) inicializuje pracovní adresář. Spusťte `terraform init` v terminálu služby Cloud Shell, abyste se připravili na nasazení nové konfigurace.

```azurecli-interactive
terraform init
```

Pomocí příkazu [plán terraformu](https://www.terraform.io/docs/commands/plan.html) můžete ověřit, jestli je konfigurace správně naformátovaná, a vizualizovat, které prostředky se vytvoří, aktualizuje nebo zničí. Výsledky mohou být uloženy v souboru a později použity pro použití konfigurace.

Spusťte `terraform plan` pro otestování nové konfigurace terraformu.

```azurecli-interactive
terraform plan --out plan.out
```

Použijte konfiguraci s použitím [terraformu použít](https://www.terraform.io/docs/commands/apply.html) a zadáním názvu souboru plánu. Tento příkaz nasadí prostředky v předplatném Azure.

```azurecli-interactive
terraform apply plan.out
```

Jakmile budete hotovi, uvidíte, že se vytvořila skupina prostředků a instance Azure Cosmos DB umístěná ve skupině prostředků.

## <a name="update-configuration"></a>Aktualizovat konfiguraci

Aktualizujte konfiguraci tak, aby zahrnovala instanci kontejneru Azure. Kontejner spustí aplikaci, která čte a zapisuje data do Cosmos DB.

Zkopírujte následující konfiguraci do dolní `main.tf` části souboru. Po dokončení soubor uložte.

Jsou nastaveny `COSMOS_DB_ENDPOINT` dvě proměnné prostředí a `COSMOS_DB_MASTERKEY`. Tyto proměnné uchovávají umístění a klíč pro přístup k databázi. Hodnoty pro tyto proměnné jsou získány z instance databáze vytvořené v posledním kroku. Tento proces se označuje jako interpolace. Další informace o interpolaci Terraformu naleznete v tématu [syntax interpolace](https://www.terraform.io/docs/configuration/interpolation.html).


Konfigurace obsahuje taky výstupní blok, který vrací plně kvalifikovaný název domény (FQDN) instance kontejneru.

```azurecli-interactive
resource "azurerm_container_group" "vote-aci" {
  name                = "vote-aci"
  location            = "${azurerm_resource_group.vote-resource-group.location}"
  resource_group_name = "${azurerm_resource_group.vote-resource-group.name}"
  ip_address_type     = "public"
  dns_name_label      = "vote-aci"
  os_type             = "linux"

  container {
    name   = "vote-aci"
    image  = "microsoft/azure-vote-front:cosmosdb"
    cpu    = "0.5"
    memory = "1.5"
    ports {
      port     = 80
      protocol = "TCP"
    }

    secure_environment_variables = {
      "COSMOS_DB_ENDPOINT"  = "${azurerm_cosmosdb_account.vote-cosmos-db.endpoint}"
      "COSMOS_DB_MASTERKEY" = "${azurerm_cosmosdb_account.vote-cosmos-db.primary_master_key}"
      "TITLE"               = "Azure Voting App"
      "VOTE1VALUE"          = "Cats"
      "VOTE2VALUE"          = "Dogs"
    }
  }
}

output "dns" {
  value = "${azurerm_container_group.vote-aci.fqdn}"
}
```

Spusťte `terraform plan` , chcete-li vytvořit aktualizovaný plán a vizualizovat změny, které mají být provedeny. Měli byste vidět, že se do konfigurace přidal prostředek instance kontejneru Azure.

```azurecli-interactive
terraform plan --out plan.out
```

Nakonec spusťte příkaz `terraform apply` pro použití konfigurace.

```azurecli-interactive
terraform apply plan.out
```

Až se dokončí, poznamenejte si plně kvalifikovaný název domény instance kontejneru.

## <a name="test-application"></a>Otestování aplikace

Přejděte k plně kvalifikovanému názvu domény instance kontejneru. Pokud byl vše správně nakonfigurováno, měla by se zobrazit následující aplikace.

![Hlasovací aplikace Azure](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení se prostředky a skupiny prostředků Azure dají odebrat pomocí příkazu [terraformu Destroy](https://www.terraform.io/docs/commands/destroy.html) .

```azurecli-interactive
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Další kroky

V tomto příkladu jste vytvořili, nasadili a zničili konfiguraci Terraformu. Další informace o používání Terraformu v Azure najdete v dokumentaci k poskytovateli Azure Terraformu.

> [!div class="nextstepaction"]
> [Poskytovatel Azure Terraformu](https://www.terraform.io/docs/providers/azurerm/)
