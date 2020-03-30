---
title: Použití pravidel virtuální sítě – Azure CLI – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje, jak vytvořit a spravovat koncové body služby virtuální sítě a pravidla pro Azure Database for PostgreSQL pomocí příkazového řádku Azure CLI.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 5e80b48e43e72bc3eda16a5dcc64f980065c923f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974125"
---
# <a name="create-and-manage-vnet-service-endpoints-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Vytvoření a správa koncových bodů služby virtuální sítě pro Azure Database for PostgreSQL – jeden server pomocí rozhraní příkazového příkazového příkazu Azure
Koncové body a pravidla služeb virtuální sítě (Virtuální síť) rozšiřují privátní adresní prostor virtuální sítě na váš server Azure Database for PostgreSQL. Pomocí pohodlných příkazů rozhraní Příkazového řádku Azure (CLI) můžete vytvářet, aktualizovat, odstraňovat, vypsat a zobrazovat koncové body služby virtuální sítě a pravidla pro správu serveru. Přehled koncových bodů služby Azure Database for PostgreSQL VNet, včetně omezení, najdete v [tématu Azure Database for PostgreSQL Server Virtuální síť koncových bodů](concepts-data-access-and-security-vnet.md). Koncové body služby Virtuální sítě jsou dostupné ve všech podporovaných oblastech pro Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Požadavky
Chcete-li projít tento návod, co potřebujete:
- Nainstalujte [nastavení příkazového příkazu k řešení Azure](/cli/azure/install-azure-cli) nebo použijte Azure Cloud Shell v prohlížeči.
- [Databáze Azure pro postgreSQL server a databázi](quickstart-create-server-database-azure-cli.md).

> [!NOTE]
> Podpora koncových bodů služby virtuální sítě je jenom pro servery optimalizované pro obecné účely a paměť.
> V případě partnerského vztahu virtuální sítě, pokud provoz protéká společnou bránou virtuální sítě s koncovými body služby a má tok do druhé strany, vytvořte pravidlo ACL/Virtuální sítě, které umožní virtuálním počítačům Azure ve virtuální síti brány přístup k databázi Azure pro postgreSQL server.


## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Konfigurace koncových bodů služby Vnet pro Azure Database for PostgreSQL
Příkazy [virtuální sítě az](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) se používají ke konfiguraci virtuálních sítí.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

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
> Důrazně doporučujeme přečíst si tento článek o konfiguraci koncového bodu služby a důležité informace před spuštěním ukázkový skript níže nebo konfigurace koncových bodů služby. **Koncový bod služby Virtuální síť:** [Koncový bod služby Virtuální síť](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejíž hodnoty vlastností zahrnují jeden nebo více formálních názvů typů služby Azure. Koncové body služeb virtuální sítě používají název typu služby **Microsoft.Sql**, který odkazuje na službu Azure s názvem SQL Database. Tato značka služby platí také pro Azure SQL Database, Azure Database for PostgreSQL a MySQL služby. Je důležité si uvědomit, že při použití značky služby **Microsoft.Sql** na koncový bod služby virtuální sítě konfiguruje provoz koncových bodů služby pro všechny služby Azure Database, včetně Azure SQL Database, Azure Database for PostgreSQL a Azure Database for MySQL servery v podsíti. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Ukázkový skript pro vytvoření databáze Azure pro databázi PostgreSQL, vytvoření koncového bodu virtuální sítě, služby Virtuální sítě a zabezpečení serveru do podsítě pomocí pravidla virtuální sítě
V tomto ukázkovém skriptu změňte zvýrazněné řádky a upravte uživatelské jméno a heslo správce. Nahraďte Id předplatného `az account set --subscription` použitého v příkazu vlastním identifikátorem předplatného.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md