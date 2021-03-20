---
title: Použití pravidel virtuální sítě – Azure CLI-Azure Database for PostgreSQL-Single server
description: Tento článek popisuje, jak vytvořit a spravovat koncové body služby virtuální sítě a pravidla pro Azure Database for PostgreSQL pomocí příkazového řádku Azure CLI.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 07e0f7cf2834b3984d9207fa18f3b0e32340e216
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94660878"
---
# <a name="create-and-manage-vnet-service-endpoints-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Vytvoření a správa koncových bodů služby virtuální sítě pro Azure Database for PostgreSQL pro jeden server pomocí Azure CLI
Koncovými body a pravidly služby Virtual Network (VNet) přesahují privátní adresní prostor Virtual Network na server Azure Database for PostgreSQL. Pomocí praktických příkazů rozhraní příkazového řádku Azure (CLI) můžete vytvářet, aktualizovat, odstraňovat, vypisovat a zobrazovat koncové body a pravidla služby virtuální sítě pro správu serveru. Přehled koncových bodů služby virtuální sítě Azure Database for PostgreSQL, včetně omezení, najdete v tématu [koncové body služby virtuální sítě v Azure Database for PostgreSQL serveru](concepts-data-access-and-security-vnet.md). Koncové body služby virtuální sítě jsou k dispozici ve všech podporovaných oblastech pro Azure Database for PostgreSQL.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady
Postup pro krokování této příručky:
- Nainstalujte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) nebo použijte Azure Cloud Shell v prohlížeči.
- Vytvořte [Azure Database for PostgreSQL Server a databázi](quickstart-create-server-database-azure-cli.md).

    > [!NOTE]
    > Podpora koncových bodů služby virtuální sítě je určená jenom pro Pro obecné účely a paměťově optimalizované servery.
    > V případě partnerského vztahu virtuálních sítí platí, že pokud přenos prochází přes společnou bránu virtuální sítě s koncovými body služby a měl by se přesměrovat na partnera, vytvořte prosím pravidlo seznamu ACL/virtuální sítě, které povolí službě Azure Virtual Machines ve virtuální síti brány pro přístup k serveru Azure Database for PostgreSQL.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Tento článek vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Konfigurace koncových bodů služby virtuální sítě pro Azure Database for PostgreSQL
Příkazy [AZ Network VNet](/cli/azure/network/vnet) se používají ke konfiguraci virtuálních sítí.

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account#az-account-set) konkrétní ID předplatného. Zástupnou hodnotu id předplatného nahraďte vlastností **id** z výstupu příkazu **az login** pro vaše předplatné.

- Účet musí mít potřebná oprávnění k vytvoření virtuální sítě a koncového bodu služby.

Koncové body služby je možné konfigurovat na virtuálních sítích nezávisle na uživateli s oprávněním k zápisu do virtuální sítě.

Aby bylo možné zabezpečit prostředky služeb Azure pro virtuální síť, musí mít uživatel pro přidávané podsítě oprávnění k Microsoft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/. Toto oprávnění je ve výchozím nastavení součástí předdefinovaných rolí správců služeb a může se upravit vytvořením vlastních rolí.

Další informace o [předdefinovaných rolích](../role-based-access-control/built-in-roles.md) a přiřazení konkrétních oprávnění k [vlastním rolím](../role-based-access-control/custom-roles.md).

Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud jsou virtuální síť a prostředky služeb Azure v různých předplatných, musí být prostředky ve stejném tenantovi Active Directory (AD). Zajistěte, aby oba odběry měly zaregistrovaný poskytovatel prostředků **Microsoft. SQL** . Další informace najdete v tématu [registrace Resource-Manager-][resource-manager-portal].

> [!IMPORTANT]
> Před spuštěním ukázkového skriptu níže nebo konfigurací koncových bodů služby se důrazně doporučuje přečíst si článek o konfiguracích a důvodech koncového bodu služby. **Koncový bod služby Virtual Network:** [Koncový bod služby Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejíž hodnoty vlastností zahrnují jeden nebo více formálních názvů typů služeb Azure. Koncové body služeb virtuální sítě používají název typu služby **Microsoft. SQL**, který odkazuje na službu Azure s názvem SQL Database. Tato značka služby platí také pro Azure SQL Database služby Azure Database for PostgreSQL a MySQL. Je důležité si uvědomit, že pokud použijete značku služby **Microsoft. SQL** pro koncový bod služby virtuální sítě, nakonfiguruje se provoz koncového bodu služby pro všechny služby Azure Database, včetně Azure SQL Database, Azure Database for PostgreSQL a Azure Database for MySQL serverů v podsíti. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Ukázkový skript pro vytvoření databáze Azure Database for PostgreSQL, vytvoření virtuální sítě a koncového bodu služby virtuální sítě a zabezpečení serveru v podsíti s pravidlem virtuální sítě
V tomto ukázkovém skriptu změňte zvýrazněné řádky a upravte uživatelské jméno a heslo správce. Nahraďte SubscriptionID použitou v `az account set --subscription` příkazu vlastním identifikátorem předplatného.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
