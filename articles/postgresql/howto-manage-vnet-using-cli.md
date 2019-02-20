---
title: Vytvoření a správě Azure Database for PostgreSQL VNet koncové body služby a pravidla pomocí Azure CLI
description: Tento článek popisuje, jak vytvořit a spravovat Azure Database for PostgreSQL VNet koncové body služby a pravidla pomocí příkazového řádku Azure CLI.
author: mbolz
ms.author: mbolz
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: 67ca81e385bbb7aaddddc6af9aa9bcbd45005e20
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416730"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-using-azure-cli"></a>Vytváření a správa Azure Database pro koncové body služby virtuální sítě PostgreSQL pomocí Azure CLI
Koncové body služeb virtuální sítě (VNet) a pravidla rozšiřují privátní adresní prostor virtuální sítě pro váš server Azure Database for PostgreSQL. Pomocí pohodlné příkazů rozhraní příkazového řádku Azure (CLI), můžete vytvářet, aktualizovat, odstraňovat, seznamu a zobrazit koncové body služby virtuální sítě a pravidla ke správě serveru. Přehled služby Azure Database pro koncové body služby virtuální sítě PostgreSQL, včetně omezení, naleznete v tématu [– Azure Database for koncové body služby virtuální sítě serveru PostgreSQL](concepts-data-access-and-security-vnet.md). Koncové body služby virtuální sítě jsou k dispozici ve všech podporovaných oblastí pro službu Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v této příručce s postupy, musíte:
- Nainstalujte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) nebo použít Azure Cloud Shell v prohlížeči.
- [– Azure Database for PostgreSQL server a databáze](quickstart-create-server-database-azure-cli.md).

> [!NOTE]
> Podpora pro koncové body služby virtuální sítě je pouze pro servery pro obecné účely a optimalizovaný pro paměť.
> V případě VNet peering, pokud provoz se přenášejí prostřednictvím společnou bránu virtuální sítě s koncovými body služby a má tok a partnerským zařízením, vytvořte prosím pravidlo seznamu ACL/virtuální síť umožňuje službě Azure Virtual Machines ve virtuální síti Brána pro přístup k Azure Database for PostgreSQL server.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Konfigurace koncových bodů služby virtuální sítě pro službu Azure Database for PostgreSQL
[Az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) příkazy se používají ke konfiguraci virtuální sítě.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

Pokud používáte rozhraní příkazového řádku místně, musíte se přihlásit ke svému účtu pomocí příkazu [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest). Z výstupu příkazu si poznamenejte vlastnost **id** pro odpovídající název předplatného.
```azurecli-interactive
az login
```

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Ve svém účtu vyberte pomocí příkazu [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) konkrétní ID předplatného. Zástupnou hodnotu id předplatného nahraďte vlastností **id** z výstupu příkazu **az login** pro vaše předplatné.

- Účet musí mít potřebná oprávnění k vytvoření virtuální sítě a koncového bodu služby.

Koncové body služby může být ve virtuálních sítích nezávisle na sobě konfigurovat uživatel s oprávněním k zápisu do virtuální sítě.

Svázat prostředky služeb Azure k virtuální síti, uživatel musí mít pro přidávané podsítě oprávnění "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/". Toto oprávnění je ve výchozím nastavení součástí předdefinovaných rolí správců služeb a může se upravit vytvořením vlastních rolí.

Další informace o [předdefinovaných rolích](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) a přiřazení konkrétních oprávnění k [vlastním rolím](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud virtuální síť a prostředky služeb Azure v různých předplatných, by měl být prostředky ve stejném tenantovi Active Directory (AD).

> [!IMPORTANT]
> Doporučujeme před spuštěním následující ukázka skriptu, přečtěte si tento článek týkající se konfigurace koncového bodu služby a důležité informace o nebo konfigurace koncových bodů služby. **Koncový bod pro služby virtuální sítě:** A [koncový bod služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejichž hodnoty vlastností zahrnují jeden nebo víc názvů typu formální služby Azure. Koncové body služeb virtuální sítě použít název typu služby **Microsoft.Sql**, která odkazuje na službu Azure SQL Database s názvem. Tuto značku služby platí také pro Azure SQL Database, Azure Database for PostgreSQL a MySQL. Je důležité při použití zásad skupiny pamatujte **Microsoft.Sql** značka služby do koncového bodu služby virtuální sítě konfiguruje provoz koncový bod služby pro všechny služby Azure Database, včetně Azure SQL Database, Azure Database for PostgreSQL a Servery Azure Database for MySQL v podsíti. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Ukázkový skript k vytvoření databáze Azure Database for postgresql – vytvoření virtuální sítě, koncový bod služby virtuální sítě a zabezpečení serveru k podsíti se pravidlo virtuální sítě
V tomto ukázkovém skriptu změňte zvýrazněné řádky a upravte uživatelské jméno a heslo správce. Nahraďte ID předplatného použité v `az account set --subscription` příkaz vlastní identifikátor předplatného.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]
