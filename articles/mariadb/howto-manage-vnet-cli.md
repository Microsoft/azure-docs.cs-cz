---
title: Správa koncových bodů VNet – Azure CLI – Azure Database for MariaDB
description: Tento článek popisuje, jak vytvořit a spravovat Azure Database for MariaDB koncových bodů a pravidel služby VNet pomocí příkazového řádku Azure CLI.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8eaf87865fb2fc70251e1e417361333cfd750d6e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783660"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Vytvoření a správa koncových bodů služby virtuální sítě Azure Database for MariaDB pomocí Azure CLI

Pravidla a koncové body služeb virtuální sítě rozšiřují privátní adresní prostor virtuální sítě na server Azure Database for MariaDB. Pomocí praktických příkazů rozhraní příkazového řádku Azure (CLI) můžete vytvářet, aktualizovat, odstraňovat, vypisovat a zobrazovat koncové body a pravidla služby virtuální sítě pro správu serveru. Přehled koncových bodů služby virtuální sítě Azure Database for MariaDB, včetně omezení, najdete v tématu [koncové body služby virtuální sítě v Azure Database for MariaDB serveru](concepts-data-access-security-vnet.md). Koncové body služby virtuální sítě jsou k dispozici ve všech podporovaných oblastech pro Azure Database for MariaDB.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Potřebujete [Azure Database for MariaDB Server a databázi](quickstart-create-mariadb-server-database-using-azure-cli.md).

- Tento článek vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

> [!NOTE]
> Podpora koncových bodů služby virtuální sítě je určená jenom pro Pro obecné účely a paměťově optimalizované servery.

## <a name="configure-vnet-service-endpoints"></a>Konfigurace koncových bodů služby virtuální sítě

Příkazy [AZ Network VNet](/cli/azure/network/vnet) se používají ke konfiguraci virtuálních sítí.

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account#az_account_set) konkrétní ID předplatného. Zástupnou hodnotu id předplatného nahraďte vlastností **id** z výstupu příkazu **az login** pro vaše předplatné.

- Účet musí mít potřebná oprávnění k vytvoření virtuální sítě a koncového bodu služby.

Koncové body služby je možné konfigurovat na virtuálních sítích nezávisle na uživateli s oprávněním k zápisu do virtuální sítě.

Aby bylo možné zabezpečit prostředky služeb Azure pro virtuální síť, musí mít uživatel pro přidávané podsítě oprávnění k Microsoft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/. Toto oprávnění je ve výchozím nastavení součástí předdefinovaných rolí správců služeb a může se upravit vytvořením vlastních rolí.

Další informace o [předdefinovaných rolích](../role-based-access-control/built-in-roles.md) a přiřazení konkrétních oprávnění k [vlastním rolím](../role-based-access-control/custom-roles.md).

Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud jsou virtuální síť a prostředky služeb Azure v různých předplatných, musí být prostředky ve stejném tenantovi Active Directory (AD). Zajistěte, aby oba odběry měly zaregistrovaný poskytovatel prostředků **Microsoft. SQL** . Další informace najdete v tématu [Resource-Manager – registrace][resource-manager-portal] .

> [!IMPORTANT]
> Před konfigurací koncových bodů služby se důrazně doporučuje přečíst si tento článek o konfiguracích koncových bodů a požadavcích služby. **Koncový bod služby Virtual Network:** [Koncový bod služby Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejíž hodnoty vlastností zahrnují jeden nebo více formálních názvů typů služeb Azure. Koncové body služeb virtuální sítě používají název typu služby **Microsoft. SQL**, který odkazuje na službu Azure s názvem SQL Database. Tato značka služby se vztahuje také na služby Azure SQL Database, Azure Database for MariaDB, PostgreSQL a MySQL. Je důležité si uvědomit, že pokud použijete značku služby **Microsoft. SQL** pro koncový bod služby virtuální sítě, nakonfiguruje se provoz koncového bodu služby pro všechny služby Azure Database, včetně Azure SQL Database, Azure Database for PostgreSQL, Azure Database for MariaDB a Azure Database for MySQL serverů v podsíti.

### <a name="sample-script"></a>Ukázkový skript

Tento ukázkový skript slouží k vytvoření serveru Azure Database for MariaDB, vytvoření virtuální sítě a koncového bodu služby virtuální sítě a zabezpečení serveru v podsíti s pravidlem virtuální sítě. V tomto ukázkovém skriptu změňte uživatelské jméno a heslo správce. Nahraďte SubscriptionID použitou v `az account set --subscription` příkazu vlastním identifikátorem předplatného.

```azurecli-interactive
# To find the name of an Azure region in the CLI run this command: az account list-locations
# Substitute  <subscription id> with your identifier
az account set --subscription <subscription id>

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a MariaDB server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mariadb server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2

# Get available service endpoints for Azure region output is JSON
# Use the command below to get the list of services supported for endpoints, for an Azure region, say "westus".
az network vnet list-endpoint-services \
-l westus

# Add Azure SQL service endpoint to a subnet *mySubnet* while creating the virtual network *myVNet* output is JSON
az network vnet create \
-g myresourcegroup \
-n myVNet \
--address-prefixes 10.0.0.0/16 \
-l westus

# Creates the service endpoint
az network vnet subnet create \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet \
--address-prefix 10.0.1.0/24 \
--service-endpoints Microsoft.SQL

# View service endpoints configured on a subnet
az network vnet subnet show \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet

# Create a VNet rule on the sever to secure it to the subnet Note: resource group (-g) parameter is where the database exists. VNet resource group if different should be specified using subnet id (URI) instead of subnet, VNet pair.
az mariadb server vnet-rule create \
-n myRule \
-g myresourcegroup \
-s mydemoserver \
--vnet-name myVNet \
--subnet mySubnet
```

<!-- 
In this sample script, change the highlighted lines to customize the admin username and password. Replace the SubscriptionID used in the `az account set --subscription` command with your own subscription identifier.
[!code-azurecli-interactive[main](../../cli_scripts/mariadb/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MariaDB, VNet, VNet service endpoint, and VNet rule.")]
-->

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name myresourcegroup
```


<!--
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
-->

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md