---
title: Správa koncových bodů VNet – Azure CLI – Azure Database for MySQL
description: Tento článek popisuje, jak vytvořit a spravovat Azure Database for MySQL koncových bodů a pravidel služby VNet pomocí příkazového řádku Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0bc686efbd07cf39d7932b175b6f9800b1ff185f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774660"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Vytvoření a správa koncových bodů služby virtuální sítě Azure Database for MySQL pomocí Azure CLI
Pravidla a koncové body služeb virtuální sítě rozšiřují privátní adresní prostor virtuální sítě na server Azure Database for MySQL. Pomocí praktických příkazů rozhraní příkazového řádku Azure (CLI) můžete vytvářet, aktualizovat, odstraňovat, vypisovat a zobrazovat koncové body a pravidla služby virtuální sítě pro správu serveru. Přehled koncových bodů služby virtuální sítě Azure Database for MySQL, včetně omezení, najdete v tématu [koncové body služby virtuální sítě v Azure Database for MySQL serveru](concepts-data-access-and-security-vnet.md). Koncové body služby virtuální sítě jsou k dispozici ve všech podporovaných oblastech pro Azure Database for MySQL.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Potřebujete [Azure Database for MySQL server a databázi](quickstart-create-mysql-server-database-using-azure-cli.md).
 
- Tento článek vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

> [!NOTE]
> Podpora koncových bodů služby virtuální sítě je určená jenom pro Pro obecné účely a paměťově optimalizované servery.
> V případě partnerského vztahu virtuálních sítí platí, že pokud přenos prochází přes společnou bránu virtuální sítě s koncovými body služby a měl by se přesměrovat na partnera, vytvořte prosím pravidlo seznamu ACL/virtuální sítě, které povolí službě Azure Virtual Machines ve virtuální síti brány pro přístup k serveru Azure Database for MySQL.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Konfigurace koncových bodů služby virtuální sítě pro Azure Database for MySQL
Příkazy [AZ Network VNet](/cli/azure/network/vnet) se používají ke konfiguraci virtuálních sítí.

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account#az_account_set) konkrétní ID předplatného. Nahraďte vlastnost **ID** z výstupu **AZ Login** pro vaše předplatné na zástupný symbol ID předplatného.

- Účet musí mít potřebná oprávnění k vytvoření virtuální sítě a koncového bodu služby.

Koncové body služby je možné konfigurovat na virtuálních sítích nezávisle na uživateli s oprávněním k zápisu do virtuální sítě.

Aby bylo možné zabezpečit prostředky služeb Azure pro virtuální síť, musí mít uživatel pro přidávané podsítě oprávnění k Microsoft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/. Toto oprávnění je ve výchozím nastavení součástí předdefinovaných rolí správců služeb a může se upravit vytvořením vlastních rolí.

Další informace o [předdefinovaných rolích](../role-based-access-control/built-in-roles.md) a přiřazení konkrétních oprávnění k [vlastním rolím](../role-based-access-control/custom-roles.md).

Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud jsou virtuální síť a prostředky služeb Azure v různých předplatných, musí být prostředky ve stejném tenantovi Active Directory (AD). Zajistěte, aby oba odběry měly zaregistrovaný poskytovatel prostředků **Microsoft. SQL** . Další informace najdete v tématu [Resource-Manager – registrace][resource-manager-portal] .

> [!IMPORTANT]
> Před spuštěním ukázkového skriptu níže nebo konfigurací koncových bodů služby se důrazně doporučuje přečíst si článek o konfiguracích a důvodech koncového bodu služby. **Koncový bod služby Virtual Network:** [Koncový bod služby Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejíž hodnoty vlastností zahrnují jeden nebo více formálních názvů typů služeb Azure. Koncové body služeb virtuální sítě používají název typu služby **Microsoft. SQL**, který odkazuje na službu Azure s názvem SQL Database. Tato značka služby platí také pro Azure SQL Database služby Azure Database for PostgreSQL a MySQL. Je důležité si uvědomit, že pokud použijete značku služby **Microsoft. SQL** pro koncový bod služby virtuální sítě, nakonfiguruje se provoz koncového bodu služby pro všechny služby Azure Database, včetně Azure SQL Database, Azure Database for PostgreSQL a Azure Database for MySQL serverů v podsíti. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Ukázkový skript pro vytvoření databáze Azure Database for MySQL, vytvoření virtuální sítě a koncového bodu služby virtuální sítě a zabezpečení serveru v podsíti s pravidlem virtuální sítě
V tomto ukázkovém skriptu změňte zvýrazněné řádky a upravte uživatelské jméno a heslo správce. Nahraďte SubscriptionID použitou v `az account set --subscription` příkazu vlastním identifikátorem předplatného.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
