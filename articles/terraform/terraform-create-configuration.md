---
title: Rychlý Start – vytvoření konfigurace Terraformu pro Azure
description: Začněte s Terraformu v Azure nasazením Azure Cosmos DB a Azure Container Instances.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: quickstart
ms.date: 10/26/2019
ms.openlocfilehash: 07c1839afcc29c7089540c587a3a32eae14944ef
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969858"
---
# <a name="quickstart-create-a-terraform-configuration-for-azure"></a>Rychlý Start: Vytvoření konfigurace Terraformu pro Azure

V tomto rychlém startu vytvoříte prostředky Azure pomocí Terraformu. Postup v tomto článku vás provede vytvořením následujících prostředků:

> [!div class="checklist"]
> * Instance Azure Cosmos DB
> * Instance kontejneru Azure
> * Aplikace, která funguje v těchto dvou prostředcích

## <a name="create-first-configuration"></a>Vytvořit první konfiguraci

V této části vytvoříte konfiguraci pro instanci Azure Cosmos DB.

1. Přihlaste se na web [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otevřete Azure Cloud Shell.

1. Spusťte Editor Cloud Shell:

    ```bash
    code main.tf
    ```

1. Konfigurace v tomto kroku modeluje několik prostředků Azure. Mezi tyto prostředky patří skupina prostředků Azure a instance Azure Cosmos DB. K vytvoření jedinečného názvu instance Cosmos DB slouží náhodné celé číslo. Nakonfigurovali jste taky několik nastavení Cosmos DB. Další informace najdete v referenčních informacích k [Cosmos DB terraformu](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html). Zkopírujte následující konfiguraci Terraformu a vložte ji do editoru:

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

1. Uložte soubor ( **&lt;Ctrl > S**) a ukončete editor ( **&lt;Ctrl > Q**).

## <a name="run-the-configuration"></a>Spuštění konfigurace

V této části použijete několik příkazů Terraformu ke spuštění konfigurace.

1. Příkaz [terraformu init](https://www.terraform.io/docs/commands/init.html) inicializuje pracovní adresář. Spusťte následující příkaz v Cloud Shell:

    ```bash
    terraform init
    ```

1. K ověření syntaxe konfigurace lze použít příkaz [terraformu Plan](https://www.terraform.io/docs/commands/plan.html) . Parametr `-out` přesměruje výsledky do souboru. Výstupní soubor lze použít později pro použití konfigurace. Spusťte následující příkaz v Cloud Shell:

    ```bash
    terraform plan --out plan.out
    ```

1. K aplikování konfigurace se používá příkaz [terraformu Apply](https://www.terraform.io/docs/commands/apply.html) . Byl zadán výstupní soubor z předchozího kroku. Tento příkaz způsobí vytvoření prostředků Azure. Spusťte následující příkaz v Cloud Shell:

    ```bash
    terraform apply plan.out
    ```

1. Pokud chcete ověřit výsledky v rámci Azure Portal, přejděte do nové skupiny prostředků. Nová instance Azure Cosmos DB se nachází v nové skupině prostředků.

## <a name="update-configuration"></a>Aktualizovat konfiguraci

V této části se dozvíte, jak aktualizovat konfiguraci tak, aby zahrnovala instanci kontejneru Azure. Kontejner spustí aplikaci, která čte a zapisuje data do Cosmos DB.

1. Spusťte Editor Cloud Shell:

    ```bash
    code main.tf
    ```

1. Konfigurace v tomto kroku nastavuje dvě proměnné prostředí: `COSMOS_DB_ENDPOINT` a `COSMOS_DB_MASTERKEY`. Tyto proměnné uchovávají umístění a klíč pro přístup k databázi. Hodnoty pro tyto proměnné jsou získány z instance databáze vytvořené v předchozím kroku. Tento proces se označuje jako interpolace. Další informace o interpolaci Terraformu naleznete v tématu [syntax interpolace](https://www.terraform.io/docs/configuration/interpolation.html). Konfigurace obsahuje taky výstupní blok, který vrací plně kvalifikovaný název domény (FQDN) instance kontejneru. Zkopírujte a vložte následující kód do editoru:

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

1. Uložte soubor ( **&lt;Ctrl > S**) a ukončete editor ( **&lt;Ctrl > Q**).

1. Jak jste pracovali v předchozí části, spusťte následující příkaz, který provede vizuální změny, které se mají provést:

    ```bash
    terraform plan --out plan.out
    ```

1. Spusťte příkaz `terraform apply` pro použití konfigurace.

    ```bash
    terraform apply plan.out
    ```

1. Poznamenejte si plně kvalifikovaný název domény instance kontejneru.

## <a name="test-application"></a>Otestování aplikace

Chcete-li otestovat aplikaci, přejděte k plně kvalifikovanému názvu domény instance kontejneru. Mělo by se zobrazit výsledky podobné následujícímu výstupu:

![Hlasovací aplikace Azure](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku.

Pokud chcete odebrat prostředky Azure vytvořené v tomto kurzu, spusťte příkaz [terraformu Destroy](https://www.terraform.io/docs/commands/destroy.html) :

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nainstalujte a nakonfigurujte terraformu pro zřizování prostředků Azure](../virtual-machines/linux/terraform-install-configure.md).