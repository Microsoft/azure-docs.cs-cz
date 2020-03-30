---
title: Úvodní příručka – vytvoření konfigurace Terraform pro Azure
description: S Terraformem v Azure můžete začít nasazením instancí Azure Cosmos DB a Azure Container Instance.
ms.topic: quickstart
ms.date: 10/26/2019
ms.openlocfilehash: 92f66fcfeac69b66eb8cdb0dc58b7a3171d45e10
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77470017"
---
# <a name="quickstart-create-a-terraform-configuration-for-azure"></a>Úvodní příručka: Vytvoření konfigurace Terraform pro Azure

V tomto rychlém startu vytvoříte prostředky Azure pomocí Terraform. Kroky v tomto článku vás provedou vytvořením následujících zdrojů:

> [!div class="checklist"]
> * Instance Azure Cosmos DB
> * Instance kontejneru Azure
> * Aplikace, která funguje napříč těmito dvěma prostředky

## <a name="create-first-configuration"></a>Vytvořit první konfiguraci

V této části vytvoříte konfiguraci pro instanci Azure Cosmos DB.

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otevřete Prostředí Cloud Shell Azure.

1. Spusťte editor Cloud Shell:

    ```bash
    code main.tf
    ```

1. Konfigurace v tomto kroku modely několik prostředků Azure. Tyto prostředky zahrnují skupinu prostředků Azure a instanci Azure Cosmos DB. Náhodné celé číslo se používá k vytvoření jedinečného názvu instance Cosmos DB. Několik Nastavení Cosmos DB jsou také nakonfigurovány. Další informace naleznete v [odkazu Cosmos DB Terraform](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html). Zkopírujte a vložte do editoru následující konfiguraci Terraform:

    ```hcl
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
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
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

1. Uložte soubor**&lt;(Ctrl>S**) a ukončete editor**&lt;(Ctrl>Q**).

## <a name="run-the-configuration"></a>Spuštění konfigurace

V této části použijete několik příkazů Terraform ke spuštění konfigurace.

1. Příkaz [terraform init](https://www.terraform.io/docs/commands/init.html) inicializuje pracovní adresář. Spusťte následující příkaz v prostředí Cloud:

    ```bash
    terraform init
    ```

1. Příkaz [terraform plan](https://www.terraform.io/docs/commands/plan.html) lze použít k ověření syntaxe konfigurace. Parametr `-out` přesměruje výsledky do souboru. Výstupní soubor lze použít později použít konfiguraci. Spusťte následující příkaz v prostředí Cloud:

    ```bash
    terraform plan --out plan.out
    ```

1. Příkaz [terraform apply](https://www.terraform.io/docs/commands/apply.html) se používá k použití konfigurace. Je určen výstupní soubor z předchozího kroku. Tento příkaz způsobí, že prostředky Azure, které mají být vytvořeny. Spusťte následující příkaz v prostředí Cloud:

    ```bash
    terraform apply plan.out
    ```

1. Pokud chcete ověřit výsledky na webu Azure Portal, přejděte na novou skupinu prostředků. Nová instance Azure Cosmos DB je v nové skupině prostředků.

## <a name="update-configuration"></a>Aktualizovat konfiguraci

Tato část ukazuje, jak aktualizovat konfiguraci tak, aby zahrnovala instanci kontejneru Azure. Kontejner spustí aplikaci, která čte a zapisuje data do Cosmos DB.

1. Spusťte editor Cloud Shell:

    ```bash
    code main.tf
    ```

1. Konfigurace v tomto kroku nastaví `COSMOS_DB_ENDPOINT` dvě `COSMOS_DB_MASTERKEY`proměnné prostředí: a . Tyto proměnné obsahovat umístění a klíč pro přístup k databázi. Hodnoty pro tyto proměnné jsou získány z instance databáze vytvořené v předchozím kroku. Tento proces se nazývá interpolace. Další informace o interpolaci Terraform naleznete v [tématu Syntaxe interpolace](https://www.terraform.io/docs/configuration/interpolation.html). Konfigurace také obsahuje výstupní blok, který vrací plně kvalifikovaný název domény (Plně kvalifikovaný název domény) instance kontejneru. Zkopírujte a vložte následující kód do editoru:

    ```hcl
    resource "azurerm_container_group" "vote-aci" {
      name                = "vote-aci"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
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
          "COSMOS_DB_ENDPOINT"  = azurerm_cosmosdb_account.vote-cosmos-db.endpoint
          "COSMOS_DB_MASTERKEY" = azurerm_cosmosdb_account.vote-cosmos-db.primary_master_key
          "TITLE"               = "Azure Voting App"
          "VOTE1VALUE"          = "Cats"
          "VOTE2VALUE"          = "Dogs"
        }
      }
    }

    output "dns" {
      value = azurerm_container_group.vote-aci.fqdn
    }
    ```

1. Uložte soubor**&lt;(Ctrl>S**) a ukončete editor**&lt;(Ctrl>Q**).

1. Stejně jako v předchozí části spusťte následující příkaz, abyste vizualizovat změny, které mají být provedeny:

    ```bash
    terraform plan --out plan.out
    ```

1. Spusťte `terraform apply` příkaz a použijte konfiguraci.

    ```bash
    terraform apply plan.out
    ```

1. Poznamenejte si instanci kontejneru FQDN.

## <a name="test-application"></a>Otestování aplikace

Chcete-li aplikaci otestovat, přejděte na hlavní název souboru služby instanci kontejneru. Výsledky by se měly zobrazit podobně jako u následujícího výstupu:

![Aplikace Azure vote](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, odstraňte prostředky vytvořené v tomto článku.

Spusťte příkaz [terraform destroy](https://www.terraform.io/docs/commands/destroy.html) a odeberte prostředky Azure vytvořené v tomto kurzu:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nainstalujte a nakonfigurujte Terraform pro zřizování prostředků Azure](terraform-install-configure.md).