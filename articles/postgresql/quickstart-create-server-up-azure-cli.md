---
title: 'Úvodní příručka: Vytvoření serveru - az postgres up - Azure Database for PostgreSQL - Single Server'
description: Průvodce rychlým startem k vytvoření Azure Database for PostgreSQL – jeden server pomocí příkazu Azure CLI (rozhraní příkazového řádku).
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: fe15c02286223ec0829b31664811b7f589cf16aa
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74774828"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Úvodní příručka: K vytvoření databáze Azure pro PostgreSQL – jeden server použijte příkaz Azure CLI, az postgres up (preview)

> [!IMPORTANT]
> Příkaz [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI je ve verzi Preview.

Azure Database for PostgreSQL je spravovaná služba, která umožňuje spouštět, spravovat a škálovat vysoce dostupné databáze PostgreSQL v cloudu. Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Tento rychlý start ukazuje, jak pomocí příkazu [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) vytvořit azure databázi pro postgresql server pomocí azure cli. Kromě vytvoření serveru `az postgres up` příkaz vytvoří ukázkovou databázi, kořenového uživatele v databázi, otevře bránu firewall pro služby Azure a vytvoří výchozí pravidla brány firewall pro klientský počítač. Tyto výchozí hodnoty pomáhají urychlit proces vývoje.

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

Tento článek vyžaduje, abyste místně spouštěli azure CLI verze 2.0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

Budete se muset přihlásit ke svému účtu pomocí příkazu [az login.](/cli/azure/authenticate-azure-cli?view=interactive-log-in) Všimněte si vlastnosti **ID** z výstupu příkazu pro odpovídající název předplatného.

```azurecli
az login
```

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account) konkrétní ID předplatného. Nahraďte vlastnost **ID předplatného** z výstupu **přihlášení az** pro vaše předplatné do zástupného symbolu ID předplatného.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Vytvoření serveru Azure Database for PostgreSQL

Chcete-li použít příkazy, nainstalujte rozšíření [db-up.](/cli/azure/ext/db-up) Pokud je vrácena chyba, ujistěte se, že jste nainstalovali nejnovější verzi rozhraní příkazového příkazu Azure. Viz [Instalace azure cli](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Vytvořte databázi Azure pro postgresql server pomocí následujícího příkazu:

```azurecli
az postgres up
```

Server je vytvořen s následujícími výchozími hodnotami (pokud je ručně nepřepíšete):

**Nastavení** | **Výchozí hodnota** | **Popis**
---|---|---
název-serveru | Systém generován | Jedinečný název, který identifikuje váš server Azure Database for PostgreSQL.
resource-group | Systém generován | Nová skupina prostředků Azure.
sku-name | GP_Gen5_2 | Název sku. Dodržuje konvenci {cenová úroveň}\_{výpočetní generace}\_{virtuální jádra} ve zkráceném zápisu. Výchozí je server Gen5 pro obecné účely se 2 virtuálními jádry. Další informace o úrovních najdete na naší stránce s [cenami.](https://azure.microsoft.com/pricing/details/postgresql/)
backup-retention | 7 | Jak dlouho je záloha zachována. Jednotkou jsou dny.
geo-redundant-backup | Zakázáno | Určuje, jestli pro tento server mají nebo nemají být povolené geograficky redundantní zálohy.
location | westus2 | Lokace Azure pro server.
ssl-enforcement | Zakázáno | Určuje, jestli pro tento server má nebo nemá být povolený protokol SSL.
velikost úložiště | 5120 | Kapacita úložiště serveru (jednotkou jsou megabajty).
version | 10 | Hlavní verze PostgreSQL.
admin-user | Systém generován | Uživatelské jméno správce.
admin-password | Systém generován | Heslo uživatele, který je správcem.

> [!NOTE]
> Další informace o `az postgres up` příkazu a jeho další parametry naleznete v [dokumentaci k rozhraní příkazového řádku Azure](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up).

Jakmile je server vytvořen, je dodáván s následujícím nastavením:

- Je vytvořeno pravidlo brány firewall s názvem "devbox". Azure CLI pokusí zjistit IP adresu počítače `az postgres up` příkaz je spuštěn z a whitelists, že IP adresu.
- "Povolit přístup ke službám Azure" je nastavena na ZAPNUTO. Toto nastavení konfiguruje bránu firewall serveru tak, aby přijímala připojení ze všech prostředků Azure, včetně prostředků, které nejsou ve vašem předplatném.
- Je vytvořena prázdná databáze s názvem "sampledb"
- Je vytvořen nový uživatel s názvem "root" s oprávněními k "sampledb"

> [!NOTE]
> Azure Database for PostgreSQL komunikuje přes port 5432. Pokud se připojujete z podnikové sítě, nemusí být odchozí provoz přes port 5432 bránou firewall vaší sítě povolený. Připojte se k serveru, otevřete své IT oddělení 5432.

## <a name="get-the-connection-information"></a>Získání informací o připojení

Po `az postgres up` dokončení příkazu je vrácen seznam připojovacích řetězců pro oblíbené programovací jazyky. Tyto připojovací řetězce jsou předem nakonfigurované s konkrétními atributy nově vytvořené databáze Azure pro postgreSQL server.

Pomocí příkazu [az postgres show-connection-string](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string) můžete tyto připojovací řetězce znovu vypsat.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte všechny prostředky, které jste vytvořili v rychlém startu pomocí následujícího příkazu. Tento příkaz odstraní Azure Database for PostgreSQL server a skupinu prostředků.

```azurecli
az postgres down --delete-group
```

Pokud byste chtěli odstranit nově vytvořený server, můžete spustit [příkaz az postgres dolů.](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down)

```azurecli
az postgres down
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Migrace vaší databáze pomocí exportu a importu](./howto-migrate-using-export-and-import.md)
