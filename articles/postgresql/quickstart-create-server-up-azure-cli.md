---
title: 'Rychlý Start: vytvoření serveru – AZ Postgres up-Azure Database for PostgreSQL-Single server'
description: Průvodce rychlým startem pro vytvoření Azure Database for PostgreSQLho samostatného serveru pomocí příkazu Azure CLI (rozhraní příkazového řádku)
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 28007b4e17bd698e4aaa2bddbf0c8f9ffaaa8351
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606449"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Rychlý Start: použití příkazu rozhraní příkazového řádku Azure, AZ Postgres up (Preview), vytvoření serveru Azure Database for PostgreSQL-Single

> [!IMPORTANT]
> Příkaz [AZ Postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI je ve verzi Preview.

Azure Database for PostgreSQL je spravovaná služba, která umožňuje spouštět, spravovat a škálovat vysoce dostupné databáze PostgreSQL v cloudu. Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. V tomto rychlém startu se dozvíte, jak pomocí příkazu [AZ Postgres nahoru](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) vytvořit server Azure Database for PostgreSQL pomocí rozhraní příkazového řádku Azure CLI. Kromě vytvoření serveru `az postgres up` vytvoří příkaz ukázkovou databázi, root user v databázi, otevře bránu firewall pro služby Azure a vytvoří výchozí pravidla brány firewall pro klientský počítač. Tato výchozí nastavení usnadňují urychlení procesu vývoje.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Tento článek vyžaduje, abyste v místním prostředí používali Azure CLI verze 2,0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

K účtu se budete muset přihlásit pomocí příkazu [AZ Login](/cli/azure/authenticate-azure-cli) . Poznamenejte si vlastnost **ID** z výstupu příkazu pro odpovídající název předplatného.

```azurecli
az login
```

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account) konkrétní ID předplatného. Nahraďte vlastnost **ID předplatného** z výstupu **AZ Login** pro vaše předplatné na zástupný symbol ID předplatného.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Vytvoření serveru Azure Database for PostgreSQL

Chcete-li použít příkazy, nainstalujte rozšíření [DB](/cli/azure/ext/db-up) . Pokud se vrátí chyba, ujistěte se, že máte nainstalovanou nejnovější verzi rozhraní příkazového řádku Azure CLI. Viz Instalace rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Pomocí následujícího příkazu vytvořte server Azure Database for PostgreSQL:

```azurecli
az postgres up
```

Server se vytvoří s následujícími výchozími hodnotami (Pokud je nepřepíšete ručně):

**Nastavení** | **Výchozí hodnota** | **Popis**
---|---|---
název-serveru | Vygenerované systémem | Jedinečný název, který identifikuje váš server Azure Database for PostgreSQL.
resource-group | Vygenerované systémem | Nová skupina prostředků Azure.
sku-name | GP_Gen5_2 | Název sku. Dodržuje konvenci {cenová úroveň}\_{výpočetní generace}\_{virtuální jádra} ve zkráceném zápisu. Výchozím nastavením je Pro obecné účely Server Gen5 se 2 virtuální jádra. Další informace o úrovních najdete na naší [stránce s cenami](https://azure.microsoft.com/pricing/details/postgresql/) .
backup-retention | 7 | Doba uchování zálohy. Jednotkou jsou dny.
geo-redundant-backup | Zakázáno | Určuje, jestli pro tento server mají nebo nemají být povolené geograficky redundantní zálohy.
location | westus2 | Lokace Azure pro server.
ssl-enforcement | Zakázáno | Zda má být protokol TLS/SSL povolený nebo nikoli pro tento server.
velikost úložiště | 5120 | Kapacita úložiště serveru (jednotkou jsou megabajty).
verze | 10 | Hlavní verze PostgreSQL.
admin-user | Vygenerované systémem | Uživatelské jméno správce.
admin-password | Vygenerované systémem | Heslo uživatele, který je správcem.

> [!NOTE]
> Další informace o `az postgres up` příkazu a jeho dalších parametrech najdete v dokumentaci k rozhraní příkazového [řádku Azure CLI](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up).

Po vytvoření serveru se objeví následující nastavení:

- Vytvoří se pravidlo brány firewall nazvané "devbox". Rozhraní příkazového řádku Azure CLI se pokusí zjistit IP adresu počítače, `az postgres up` ze kterého se spouští příkaz, a umožňuje tuto IP adresu.
- Možnost "povolení přístupu ke službám Azure" je nastavená na ZAPNUTo. Toto nastavení nakonfiguruje bránu firewall serveru tak, aby přijímala připojení ze všech prostředků Azure, včetně prostředků, které se ve vašem předplatném nepoužívá.
- Vytvoří se prázdná databáze s názvem "sampledb".
- Vytvoří se nový uživatel s názvem root s oprávněními k SampleDB.

> [!NOTE]
> Azure Database for PostgreSQL komunikuje přes port 5432. Pokud se připojujete z podnikové sítě, nemusí být odchozí provoz přes port 5432 bránou firewall vaší sítě povolený. Vaše IT oddělení otevře port 5432 pro připojení k vašemu serveru.

## <a name="get-the-connection-information"></a>Získání informací o připojení

Po `az postgres up` dokončení příkazu se vám vrátí seznam připojovacích řetězců pro oblíbené programovací jazyky. Tyto připojovací řetězce jsou předem nakonfigurované s konkrétními atributy nově vytvořeného serveru Azure Database for PostgreSQL.

Pomocí příkazu [AZ Postgres show-Connection-String](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string) můžete tyto připojovací řetězce znovu zobrazit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Všechny prostředky, které jste vytvořili v rychlém startu, vyčistěte pomocí následujícího příkazu. Tento příkaz odstraní Server Azure Database for PostgreSQL a skupinu prostředků.

```azurecli
az postgres down --delete-group
```

Pokud byste chtěli odstranit nově vytvořený server, můžete spustit příkaz [AZ Postgres Down](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down) .

```azurecli
az postgres down
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Migrace vaší databáze pomocí exportu a importu](./howto-migrate-using-export-and-import.md)
