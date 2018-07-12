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
ms.date: 06/01/2018
ms.openlocfilehash: fd8b21d1273b1bd02b0a949894be53cdc4a5c3c0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38597876"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Vytvoření a správě Azure Database for MySQL VNet koncových bodů služby pomocí rozhraní příkazového řádku Azure
Koncové body služeb virtuální sítě (VNet) a pravidla rozšiřují privátní adresní prostor virtuální sítě pro váš server Azure Database for MySQL. Pomocí pohodlné příkazů rozhraní příkazového řádku Azure (CLI), můžete vytvářet, aktualizovat, odstraňovat, seznamu a zobrazit koncové body služby virtuální sítě a pravidla ke správě serveru. Přehled služby Azure Database pro koncové body služby virtuální sítě MySQL, včetně omezení, naleznete v tématu [– Azure Database for koncové body služby virtuální sítě serveru MySQL](concepts-data-access-and-security-vnet.md). Koncové body služby virtuální sítě jsou k dispozici ve verzi public preview ve všech podporovaných oblastí pro službu Azure Database for MySQL.

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v této příručce s postupy, musíte:
- Nainstalujte [příkazového řádku Azure CLI 2.0](/cli/azure/install-azure-cli) nástroj příkazového řádku nebo pomocí Azure Cloud Shell v prohlížeči.
- [– Azure Database for MySQL serveru a databáze](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!NOTE]
> Podpora pro koncové body služby virtuální sítě je pouze pro servery pro obecné účely a optimalizovaný pro paměť.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Konfigurace koncových bodů služby virtuální sítě pro službu Azure Database for MySQL
[Az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) příkazy se používají ke konfiguraci virtuální sítě.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Pokud používáte rozhraní příkazového řádku místně, musíte se přihlásit ke svému účtu pomocí příkazu [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in). Z výstupu příkazu si poznamenejte vlastnost **id** pro odpovídající název předplatného.
```azurecli-interactive
az login
```

Instalace rozšíření rozhraní příkazového řádku pro službu Azure Database pro koncové body služby virtuální sítě MySQL pomocí `az extension add --name rdbms-vnet` příkazu. 
```azurecli-interactive
az extension add --name rdbms-vnet
```

Spustit `az extension list` příkaz k ověření instalace rozšíření rozhraní příkazového řádku.
```azurecli-interactive
az extension list
```
Výstup tohoto příkazu Vypíše seznam všech nainstalovaná rozšíření. Azure Database for MySQL CLI rozšíření je:

 {"extensionType": "whl", "name": "relační databázový systém virtuální síť", "verze": "10.0.0"}

> [!NOTE]
> Chcete-li odinstalovat rozšíření rozhraní příkazového řádku, spusťte `az extension remove -n rdbms-vnet` příkazu. 

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account#az_account_set) konkrétní ID předplatného. Zástupnou hodnotu id předplatného nahraďte vlastností **id** z výstupu příkazu **az login** pro vaše předplatné.

- Účet musí mít potřebná oprávnění k vytvoření virtuální sítě a koncového bodu služby.

Koncové body služby může být ve virtuálních sítích nezávisle na sobě konfigurovat uživatel s oprávněním k zápisu do virtuální sítě.

Pokud chce uživatel svázat prostředky služeb Azure s virtuální sítí, musí mít pro přidávané podsítě oprávnění k Microsoft.Network/JoinServicetoaSubnet. Toto oprávnění je ve výchozím nastavení součástí předdefinovaných rolí správců služeb a může se upravit vytvořením vlastních rolí.

Další informace o [předdefinovaných rolích](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) a přiřazení konkrétních oprávnění k [vlastním rolím](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud virtuální síť a prostředky služeb Azure v různých předplatných, by měl být prostředky ve stejném tenantovi Active Directory (AD), v době v této verzi preview.

> [!IMPORTANT]
> Doporučujeme před spuštěním následující ukázka skriptu, přečtěte si tento článek týkající se konfigurace koncového bodu služby a důležité informace o nebo konfigurace koncových bodů služby. **Koncový bod služby virtuální sítě:** A [koncový bod služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejichž hodnoty vlastností zahrnují jeden nebo víc názvů typu formální služby Azure. Koncové body služeb virtuální sítě použít název typu služby **Microsoft.Sql**, která odkazuje na službu Azure SQL Database s názvem. Tuto značku služby platí také pro Azure SQL Database, Azure Database for PostgreSQL a MySQL. Je důležité při použití zásad skupiny pamatujte **Microsoft.Sql** značka služby do koncového bodu služby virtuální sítě konfiguruje provoz koncový bod služby pro všechny služby Azure Database, včetně Azure SQL Database, Azure Database for PostgreSQL a Servery Azure Database for MySQL v podsíti. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Ukázkový skript k vytvoření databáze Azure Database for MySQL, vytvořte virtuální síť, koncový bod služby virtuální sítě a zabezpečení serveru k podsíti se pravidlo virtuální sítě
V tomto ukázkovém skriptu změňte zvýrazněné řádky a upravte uživatelské jméno a heslo správce. Nahraďte ID předplatného použité v `az account set --subscription` příkaz vlastní identifikátor předplatného.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
