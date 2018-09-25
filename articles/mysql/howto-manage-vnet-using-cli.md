---
title: Vytvoření a správě Azure Database for MySQL VNet koncové body služby a pravidla pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak vytvořit a spravovat Azure Database for MySQL VNet koncové body služby a pravidla pomocí příkazového řádku Azure CLI.
services: mysql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 08/15/2018
ms.openlocfilehash: 2fb29c7b46760a24324f2bbfb7bc9815778fc355
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996889"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Vytvoření a správě Azure Database for MySQL VNet koncových bodů služby pomocí rozhraní příkazového řádku Azure
Koncové body služeb virtuální sítě (VNet) a pravidla rozšiřují privátní adresní prostor virtuální sítě pro váš server Azure Database for MySQL. Pomocí pohodlné příkazů rozhraní příkazového řádku Azure (CLI), můžete vytvářet, aktualizovat, odstraňovat, seznamu a zobrazit koncové body služby virtuální sítě a pravidla ke správě serveru. Přehled služby Azure Database pro koncové body služby virtuální sítě MySQL, včetně omezení, naleznete v tématu [– Azure Database for koncové body služby virtuální sítě serveru MySQL](concepts-data-access-and-security-vnet.md). Koncové body služby virtuální sítě jsou k dispozici ve všech podporovaných oblastí pro službu Azure Database for MySQL.

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v této příručce s postupy, musíte:
- Nainstalujte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) nebo použít Azure Cloud Shell v prohlížeči.
- [– Azure Database for MySQL serveru a databáze](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!NOTE]
> Podpora pro koncové body služby virtuální sítě je pouze pro servery pro obecné účely a optimalizovaný pro paměť.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Konfigurace koncových bodů služby virtuální sítě pro službu Azure Database for MySQL
[Az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) příkazy se používají ke konfiguraci virtuální sítě.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

Pokud používáte rozhraní příkazového řádku místně, musíte se přihlásit ke svému účtu pomocí příkazu [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest). Z výstupu příkazu si poznamenejte vlastnost **id** pro odpovídající název předplatného.
```azurecli-interactive
az login
```

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Ve svém účtu vyberte pomocí příkazu [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) konkrétní ID předplatného. Zástupnou hodnotu id předplatného nahraďte vlastností **id** z výstupu příkazu **az login** pro vaše předplatné.

- Účet musí mít potřebná oprávnění k vytvoření virtuální sítě a koncového bodu služby.

Koncové body služby může být ve virtuálních sítích nezávisle na sobě konfigurovat uživatel s oprávněním k zápisu do virtuální sítě.

Pokud chce uživatel svázat prostředky služeb Azure s virtuální sítí, musí mít pro přidávané podsítě oprávnění k Microsoft.Network/JoinServicetoaSubnet. Toto oprávnění je ve výchozím nastavení součástí předdefinovaných rolí správců služeb a může se upravit vytvořením vlastních rolí.

Další informace o [předdefinovaných rolích](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) a přiřazení konkrétních oprávnění k [vlastním rolím](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud virtuální síť a prostředky služeb Azure v různých předplatných, by měl být prostředky ve stejném tenantovi Active Directory (AD).

> [!IMPORTANT]
> Doporučujeme před spuštěním následující ukázka skriptu, přečtěte si tento článek týkající se konfigurace koncového bodu služby a důležité informace o nebo konfigurace koncových bodů služby. **Koncový bod služby virtuální sítě:** A [koncový bod služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejichž hodnoty vlastností zahrnují jeden nebo víc názvů typu formální služby Azure. Koncové body služeb virtuální sítě použít název typu služby **Microsoft.Sql**, která odkazuje na službu Azure SQL Database s názvem. Tuto značku služby platí také pro Azure SQL Database, Azure Database for PostgreSQL a MySQL. Je důležité při použití zásad skupiny pamatujte **Microsoft.Sql** značka služby do koncového bodu služby virtuální sítě konfiguruje provoz koncový bod služby pro všechny služby Azure Database, včetně Azure SQL Database, Azure Database for PostgreSQL a Servery Azure Database for MySQL v podsíti. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Ukázkový skript k vytvoření databáze Azure Database for MySQL, vytvořte virtuální síť, koncový bod služby virtuální sítě a zabezpečení serveru k podsíti se pravidlo virtuální sítě
V tomto ukázkovém skriptu změňte zvýrazněné řádky a upravte uživatelské jméno a heslo správce. Nahraďte ID předplatného použité v `az account set --subscription` příkaz vlastní identifikátor předplatného.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
