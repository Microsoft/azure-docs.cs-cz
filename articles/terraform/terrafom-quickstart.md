---
title: Úvod k Terraformu v Azure.
description: Začínáme s využitím Terraformu v Azure nasazením Azure Cosmos DB a Azure Container Instances.
services: terraform
author: neilpeterson
ms.service: terraform
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: nepeters
ms.openlocfilehash: 408e07e9b6214dbd223bd4ec7959b00ecc414f1e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869173"
---
# <a name="create-a-terraform-configuration-for-azure"></a>Vytvoření konfigurace Terraformu pro Azure

V tomto příkladu získáte prostředí při vytváření konfigurace Terraformu a tato konfigurace se nasazuje do Azure. Po dokončení jste nasadili, instance služby Azure Cosmos DB, Azure Container Instances a aplikace, která funguje napříč tyto dva prostředky. Tento dokument předpokládá, že je ve službě Azure Cloud Shell, který má předinstalované nástroje Terraformu dokončena veškerá práce. Pokud chcete pro seznámení se základními v příkladu na svém vlastním systému, Terraform, můžete ho nainstalovat pomocí pokynů uvedených [tady](../virtual-machines/linux/terraform-install-configure.md).

## <a name="create-first-configuration"></a>Vytvoření první konfigurace

V této části vytvoříte konfiguraci pro instanci služby Azure Cosmos DB.

Vyberte **vyzkoušet** otevřete Azure cloud shellu. Jakmile otevřené, zadejte v `code .` otevřete editor kódu cloud shell.

```azurecli-interactive
code .
```

Zkopírujte a vložte následující konfigurace Terraformu.

Tato konfigurace modely skupinu prostředků Azure, náhodné celé číslo a instance služby Azure Cosmos DB. Náhodné celé číslo se používá v názvu instance služby Cosmos DB. Několik Cosmos DB je také nakonfigurováno. Úplný seznam konfigurace Terraformu Cosmos DB, najdete v článku [Reference k Terraformu Cosmos DB](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html).

Uložte soubor jako `main.tf` až budete hotovi. Tuto operaci lze provést pomocí tří teček v horní pravé části editoru kódu.

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

[Terraformu init](https://www.terraform.io/docs/commands/init.html) příkaz inicializuje pracovní adresář. Spustit `terraform init` ve službě cloud shell terminálu přípravy pro nasazení novou konfiguraci.

```azurecli-interactive
terraform init
```

[Terraformu plán](https://www.terraform.io/docs/commands/plan.html) příkaz lze použít k ověření, že konfiguraci je ve správném formátu a vizualizace, které prostředky se vytvořil, aktualizoval nebo zničení. Výsledky můžete uložených v souboru a používá k aplikování konfigurace později.

Spustit `terraform plan` otestovat nové konfigurace Terraformu.

```azurecli-interactive
terraform plan --out plan.out
```

Použít konfiguraci pomocí [použijte terraform](https://www.terraform.io/docs/commands/apply.html) a zadáním názvu souboru s plánem. Tento příkaz nasadí prostředků ve vašem předplatném Azure.

```azurecli-interactive
terraform apply plan.out
```

Jakmile budete hotovi, uvidíte, že se vytvořila skupina prostředků a instanci služby Azure Cosmos DB umístěné ve skupině prostředků.

## <a name="update-configuration"></a>Konfigurace aktualizace

Aktualizujte konfiguraci zahrnout Instance kontejneru Azure. Kontejner spustí aplikaci, která čte a zapisuje data do služby Cosmos DB.

Zkopírujte následující konfiguraci k dolnímu okraji `main.tf` souboru. Po dokončení soubor uložte.

Dvě proměnné prostředí jsou nastaveny, `COSMOS_DB_ENDPOINT` a `COSMOS_DB_MASTERKEY`. Tyto proměnné podržte umístění a klíče pro přístup k databázi. Hodnoty pro tyto proměnné jsou získávány z instance databáze vytvořené v předchozím kroku. Tento proces se nazývá interpolace. Další informace o Terraformu umožňuje stručně popsat, naleznete v tématu [interpolace syntaxe](https://www.terraform.io/docs/configuration/interpolation.html).


Konfigurace zahrnuje také výstupního bloku, které vrací plně kvalifikovaný název domény (FQDN) instanci kontejneru.

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
    ports  = {
      port     = 80
      protocol = "TCP"
    }

    secure_environment_variables {
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

Spustit `terraform plan` vytvoření aktualizovaný plán a vizualizovat provedení změn. Měli byste vidět, že byl přidán do konfigurace prostředku služby Azure Container Instance.

```azurecli-interactive
terraform plan --out plan.out
```

Nakonec spusťte `terraform apply` uplatnit konfiguraci.

```azurecli-interactive
terraform apply plan.out
```

Po dokončení, poznamenejte si instanci kontejneru plně kvalifikovaný název domény.

## <a name="test-application"></a>Otestování aplikace

Přejděte na plně kvalifikovaný název domény instanci kontejneru. Pokud všechno, co byla správně nakonfigurovaná, měli byste vidět následující aplikace.

![Aplikace Azure vote](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete hotovi, prostředky Azure a skupinu prostředků lze odebrat pomocí [zničit terraformu](https://www.terraform.io/docs/commands/destroy.html) příkazu.

```azurecli-interactive
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Další postup

V tomto příkladu vytvoření, nasazení a zničení konfigurace Terraformu. Další informace o použití Terraformu v Azure najdete v dokumentaci poskytovatele Terraformu pro Azure.

> [!div class="nextstepaction"]
> [Zprostředkovatel Azure Terraformu](https://www.terraform.io/docs/providers/azurerm/)