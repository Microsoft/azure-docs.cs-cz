---
title: 'Úvodní příručka: Vytvoření azure databáze pro MySQL pomocí az mysql nahoru'
description: Průvodce rychlým startem k vytvoření Azure Database for MySQL server pomocí příkazu Azure CLI (rozhraní příkazového řádku).
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc
ms.openlocfilehash: 7b81e88fe6f658fdf4c1857c6082100894c6f2f6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067723"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Úvodní příručka: Vytvoření databáze Azure pro MySQL pomocí jednoduchého příkazu Azure CLI – az mysql up (preview)

> [!IMPORTANT]
> Příkaz [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI je ve verzi Preview.

Azure Database for MySQL je spravovaná služba, která umožňuje spouštět, spravovat a škálovat vysoce dostupné databáze MySQL v cloudu. Azure CLI se používá k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Tento rychlý start ukazuje, jak pomocí příkazu [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) vytvořit azure databázi pro server MySQL pomocí azure cli. Kromě vytvoření serveru `az mysql up` příkaz vytvoří ukázkovou databázi, kořenového uživatele v databázi, otevře bránu firewall pro služby Azure a vytvoří výchozí pravidla brány firewall pro klientský počítač. To pomáhá urychlit proces vývoje.

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

Tento článek vyžaduje, abyste místně spouštěli azure CLI verze 2.0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

Budete se muset přihlásit ke svému účtu pomocí příkazu [az login.](/cli/azure/authenticate-azure-cli?view=interactive-log-in) Z výstupu příkazu si poznamenejte vlastnost **id** pro odpovídající název předplatného.

```azurecli
az login
```

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account) konkrétní ID předplatného. Nahraďte vlastnost **ID předplatného** z výstupu **přihlášení az** pro vaše předplatné do zástupného symbolu ID předplatného.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Vytvoření serveru Azure Database for MySQL

Chcete-li použít příkazy, nainstalujte rozšíření [db-up.](/cli/azure/ext/db-up) Pokud je vrácena chyba, ujistěte se, že jste nainstalovali nejnovější verzi rozhraní příkazového příkazu Azure. Viz [Instalace azure cli](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Vytvořte databázi Azure pro server MySQL pomocí následujícího příkazu:

```azurecli
az mysql up
```

Server je vytvořen s následujícími výchozími hodnotami (pokud je ručně nepřepíšete):

**Nastavení** | **Výchozí hodnota** | **Popis**
---|---|---
název-serveru | Systém generován | Jedinečný název, který identifikuje váš server Azure Database for MySQL.
resource-group | Systém generován | Nová skupina prostředků Azure.
sku-name | GP_Gen5_2 | Název sku. Dodržuje konvenci {cenová úroveň}\_{výpočetní generace}\_{virtuální jádra} ve zkráceném zápisu. Výchozí je server Gen5 pro obecné účely se 2 virtuálními jádry. Další informace o úrovních najdete na naší stránce s [cenami.](https://azure.microsoft.com/pricing/details/mysql/)
backup-retention | 7 | Určuje, jak dlouho se mají uchovávat zálohy. Jednotkou jsou dny.
geo-redundant-backup | Zakázáno | Určuje, jestli pro tento server mají nebo nemají být povolené geograficky redundantní zálohy.
location | westus2 | Lokace Azure pro server.
ssl-enforcement | Zakázáno | Určuje, zda má být pro tento server povolen protokol SSL.
velikost úložiště | 5120 | Kapacita úložiště serveru (jednotkou jsou megabajty).
version | 5.7 | Hlavní verze MySQL.
admin-user | Systém generován | Uživatelské jméno pro přihlášení správce.
admin-password | Systém generován | Heslo uživatele, který je správcem.

> [!NOTE]
> Další informace o `az mysql up` příkazu a jeho další parametry naleznete v [dokumentaci k rozhraní příkazového řádku Azure](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up).

Jakmile je server vytvořen, je dodáván s následujícím nastavením:

- Je vytvořeno pravidlo brány firewall s názvem "devbox". Azure CLI pokusí zjistit IP adresu počítače `az mysql up` příkaz je spuštěn z a whitelists, že IP adresu.
- "Povolit přístup ke službám Azure" je nastavena na ZAPNUTO. Toto nastavení konfiguruje bránu firewall serveru tak, aby přijímala připojení ze všech prostředků Azure, včetně prostředků, které nejsou ve vašem předplatném.
- Parametr `wait_timeout` je nastaven na 8 hodin
- Je vytvořena prázdná databáze s názvem "sampledb"
- Je vytvořen nový uživatel s názvem "root" s oprávněními k "sampledb"

> [!NOTE]
> Azure Database for MySQL komunikuje přes port 3306. Při připojování z podnikové sítě nemusí být odchozí provoz přes port 3306 bránou firewall sítě povolen. Připojte se k serveru, otevřete své IT oddělení.

## <a name="get-the-connection-information"></a>Získání informací o připojení

Po `az mysql up` dokončení příkazu je vrácen seznam připojovacích řetězců pro oblíbené programovací jazyky. Tyto připojovací řetězce jsou předem nakonfigurované s konkrétními atributy nově vytvořené databáze Azure pro server MySQL.

Pomocí příkazu [az mysql show-connection-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) můžete tyto připojovací řetězce znovu vypsat.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte všechny prostředky, které jste vytvořili v rychlém startu pomocí následujícího příkazu. Tento příkaz odstraní Azure Database for MySQL server a skupinu prostředků.

```azurecli
az mysql down --delete-group
```

Pokud byste chtěli odstranit nově vytvořený server, můžete spustit [příkaz az mysql down.](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down)

```azurecli
az mysql down
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Návrh databáze MySQL pomocí rozhraní příkazového příkazu Azure](./tutorial-design-database-using-cli.md)
