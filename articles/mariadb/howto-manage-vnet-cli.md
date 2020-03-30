---
title: Správa koncových bodů virtuální sítě – Azure CLI – azure database for MariaDB
description: Tento článek popisuje, jak vytvořit a spravovat Azure Database pro koncové body a pravidla služby Virtuální sítě MariaDB pomocí příkazového řádku Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 46bfab6935d08ac28ced7f392892ade6f68a0492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530848"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Vytvoření a správa koncových bodů služby Azure Database for MariaDB VNet pomocí rozhraní příkazového příkazového příkazu Azure

Pravidla a koncové body služeb virtuální sítě rozšiřují privátní adresní prostor virtuální sítě na server Azure Database for MariaDB. Pomocí pohodlných příkazů rozhraní Příkazového řádku Azure (CLI) můžete vytvářet, aktualizovat, odstraňovat, vypsat a zobrazovat koncové body služby virtuální sítě a pravidla pro správu serveru. Přehled koncových bodů služby Azure Database for MariaDB VNet, včetně omezení, najdete v článku [Databáze Azure pro koncové body služby Virtuální sítě serveru MariaDB](concepts-data-access-security-vnet.md). Koncové body služby Virtuální sítě jsou dostupné ve všech podporovaných oblastech pro Azure Database for MariaDB.

## <a name="prerequisites"></a>Požadavky
Chcete-li projít tento návod, co potřebujete:
- Nainstalujte [nastavení příkazového příkazu k řešení Azure](/cli/azure/install-azure-cli) nebo použijte Azure Cloud Shell v prohlížeči.
- [Databáze Azure pro server MariaDB a databázi](quickstart-create-mariadb-server-database-using-azure-cli.md).

> [!NOTE]
> Podpora koncových bodů služby virtuální sítě je jenom pro servery optimalizované pro obecné účely a paměť.

## <a name="configure-vnet-service-endpoints"></a>Konfigurace koncových bodů služby Virtuální síť
Příkazy [virtuální sítě az](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) se používají ke konfiguraci virtuálních sítí.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli). 

Pokud používáte rozhraní příkazového řádku místně, musíte se přihlásit ke svému účtu pomocí příkazu [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest). Z výstupu příkazu si poznamenejte vlastnost **id** pro odpovídající název předplatného.
```azurecli-interactive
az login
```

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Ve svém účtu vyberte pomocí příkazu [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) konkrétní ID předplatného. Zástupnou hodnotu id předplatného nahraďte vlastností **id** z výstupu příkazu **az login** pro vaše předplatné.

- Účet musí mít potřebná oprávnění k vytvoření virtuální sítě a koncového bodu služby.

Koncové body služby lze konfigurovat ve virtuálních sítích nezávisle uživatelem s přístupem pro zápis do virtuální sítě.

Chcete-li zabezpečit prostředky služby Azure do virtuální sítě, musí mít uživatel oprávnění k "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" pro přidávané podsítě. Toto oprávnění je ve výchozím nastavení součástí předdefinovaných rolí správců služeb a může se upravit vytvořením vlastních rolí.

Další informace o [předdefinovaných rolích](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) a přiřazení konkrétních oprávnění k [vlastním rolím](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud jsou prostředky virtuální sítě a služby Azure v různých předplatných, prostředky by měly být pod stejným klientem služby Active Directory (AD). Ujistěte se, že obě předplatná mají registrovaného zprostředkovatele prostředků **Microsoft.Sql.** Další informace naleznete v odkazech [na registraci správce prostředků][resource-manager-portal]

> [!IMPORTANT]
> Důrazně doporučujeme přečíst si tento článek o konfiguraci koncového bodu služby a důležité informace před konfigurací koncových bodů služby. **Koncový bod služby Virtuální síť:** [Koncový bod služby Virtuální síť](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejíž hodnoty vlastností zahrnují jeden nebo více formálních názvů typů služby Azure. Koncové body služeb virtuální sítě používají název typu služby **Microsoft.Sql**, který odkazuje na službu Azure s názvem SQL Database. Tato značka služby se vztahuje také na Azure SQL Database, Azure Database pro MariaDB, PostgreSQL a MySQL služby. Je důležité si uvědomit, že při použití značky služby **Microsoft.Sql** na koncový bod služby virtuální sítě konfiguruje provoz koncových bodů služby pro všechny služby Azure Database, včetně Azure SQL Database, Azure Database for PostgreSQL, Azure Database for MariaDB a Azure Database for MySQL servery v podsíti.

### <a name="sample-script"></a>Ukázkový skript

Tento ukázkový skript se používá k vytvoření databáze Azure pro server MariaDB, vytvoření koncového bodu virtuální sítě, služby Virtuální sítě a zabezpečení serveru do podsítě pomocí pravidla virtuální sítě. V tomto ukázkovém skriptu změňte uživatelské jméno a heslo správce. Nahraďte Id předplatného `az account set --subscription` použitého v příkazu vlastním identifikátorem předplatného.

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