---
title: Vytvářet a spravovat databáze Azure pro koncové body služby PostgreSQL virtuální sítě a pravidla pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: Tento článek popisuje postup vytvoření a správě Azure databáze pro koncové body služby PostgreSQL virtuální sítě a pravidla pomocí příkazového řádku Azure CLI.
services: postgresql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 7312000d1f22af3eb0091b46caac2c9607231513
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736622"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-using-azure-cli"></a>Vytváření a Správa databáze Azure pro koncové body služby PostgreSQL VNet pomocí rozhraní příkazového řádku Azure
Koncové body služby virtuální sítě (VNet) a pravidla rozšířit privátního adresního prostoru virtuální sítě k vaší databázi Azure pro PostgreSQL server. Příkazy pohodlné rozhraní příkazového řádku Azure (CLI), můžete vytvořit, aktualizovat, odstranit, seznamu a zobrazit koncové body služby virtuální sítě a pravidla ke správě serveru. Přehled Azure databáze pro koncové body služby PostgreSQL virtuální sítě, včetně omezení, najdete v části [databáze Azure pro koncové body služby serveru VNet PostgreSQL](concepts-data-access-and-security-vnet.md). Koncové body služby virtuální sítě jsou k dispozici ve verzi public preview ve všech oblastech podporovaných pro databázi Azure pro PostgreSQL.

## <a name="prerequisites"></a>Požadavky
Chcete-li krok tímto průvodcem postupy, je třeba:
- Nainstalujte [Azure CLI 2.0](/cli/azure/install-azure-cli) nástroj příkazového řádku nebo pomocí prostředí cloudové služby Azure v prohlížeči.
- [Databáze Azure pro PostgreSQL server a databáze](quickstart-create-server-database-azure-cli.md).

> [!NOTE]
> Podpora pro koncové body služby virtuální sítě je pouze pro obecné účely a paměťově optimalizované servery.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Konfigurace koncových bodů služby virtuální sítě pro databázi Azure pro PostgreSQL
[Az sítě vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) příkazy se používají ke konfiguraci virtuální sítě.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Pokud používáte rozhraní příkazového řádku místně, musíte se přihlásit ke svému účtu pomocí příkazu [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in). Z výstupu příkazu si poznamenejte vlastnost **id** pro odpovídající název předplatného.
```azurecli-interactive
az login
```
Nainstalujte rozšíření rozhraní příkazového řádku pro databázi Azure pro koncové body služby PostgreSQL virtuální sítě pomocí `az extension add --name rdbms-vnet` příkaz. 
```azurecli-interactive
az extension add --name rdbms-vnet
```

Spustit `az extension list` příkazu ověřte instalaci rozšíření rozhraní příkazového řádku.
```azurecli-interactive
az extension list
```
Výstup příkazu obsahuje seznam všech nainstalovaných rozšíření. Databáze Azure pro rozšíření PostgreSQL rozhraní příkazového řádku je:

 {"extensionType": "whl", "name": "rdbms virtuální síť", "verze": "10.0.0"}

> [!NOTE]
> Odinstalovat rozšíření rozhraní příkazového řádku, spusťte `az extension remove -n rdbms-vnet` příkaz. 

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account#az_account_set) konkrétní ID předplatného. Zástupnou hodnotu id předplatného nahraďte vlastností **id** z výstupu příkazu **az login** pro vaše předplatné.

- Účet musí mít potřebná oprávnění k vytvoření virtuální sítě a koncový bod služby.

Koncové body služby můžete nakonfigurovat virtuální sítě nezávisle, uživatele, který má oprávnění k zápisu do virtuální sítě.

Pokud chce uživatel svázat prostředky služeb Azure s virtuální sítí, musí mít pro přidávané podsítě oprávnění k Microsoft.Network/JoinServicetoaSubnet. Toto oprávnění je ve výchozím nastavení součástí předdefinovaných rolí správců služeb a může se upravit vytvořením vlastních rolí.

Další informace o [předdefinovaných rolích](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) a přiřazení konkrétních oprávnění k [vlastním rolím](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud virtuální sítí a Azure prostředky služby jsou v různých předplatných, prostředky musí být v rámci stejné klienta Active Directory (AD), v době této verze preview.

> [!IMPORTANT]
> Doporučujeme před spuštěním následující ukázka skriptu, přečtěte si tento článek o konfigurace koncového bodu služby a důležité informace a konfiguraci koncových bodů služby. **Koncový bod služby virtuální sítě:** A [koncový bod služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejichž hodnoty vlastností obsahovat jeden nebo více názvy typů formální služby Azure. Koncové body služby virtuální síť použít název typu služby **Microsoft.Sql**, které odkazuje na službu Azure s názvem databáze SQL. Tato značka služby platí také pro Azure SQL Database, databáze Azure pro služby PostgreSQL a MySQL. Je důležité si uvědomit, při použití **Microsoft.Sql** služby značky pro koncový bod služby virtuální sítě konfiguruje přenosů koncový bod služby pro všechny služby Azure Database, včetně Azure SQL Database, databáze Azure pro PostgreSQL a Azure databáze MySQL servery na podsíti. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Ukázkový skript pro vytvoření Azure databáze pro databázi PostgreSQL, vytvořte virtuální síť, koncový bod služby virtuální sítě a zabezpečení serveru k podsíti s pravidlem, virtuální sítě
V tomto ukázkovém skriptu změňte zvýrazněné řádky a upravte uživatelské jméno a heslo správce. Nahraďte SubscriptionID použít v `az account set --subscription` s vlastní identifikátor předplatného.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]
