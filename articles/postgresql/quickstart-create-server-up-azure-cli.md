---
title: Rychlý start – vytvoření Azure Database for PostgreSQL pomocí jednoduchého příkazu Azure CLI – az postgres nahoru (preview)
description: Úvodní příručka k vytvoření Azure Database for PostgreSQL pomocí Azure CLI (rozhraní příkazového řádku) se příkaz.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2019
ms.openlocfilehash: 0db49e2c370aee37cca4181cecbe4cf0b5585c51
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136440"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-using-a-simple-azure-cli-command---az-postgres-up-preview"></a>Rychlý start: Vytvoření Azure Database for PostgreSQL pomocí jednoduchého příkazu Azure CLI – az postgres nahoru (preview)

> [!IMPORTANT]
> [Az postgres nahoru](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) rozhraní příkazového řádku Azure je ve verzi preview.

Azure Database for PostgreSQL je spravovaná služba, která umožňuje spouštět, spravovat a škálovat vysoce dostupné databáze PostgreSQL v cloudu. Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. V tomto rychlém startu se dozvíte, jak používat [az postgres nahoru](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) příkaz pro vytvoření Azure Database for PostgreSQL pomocí Azure CLI. Kromě vytvoření serveru, `az postgres up` příkaz vytvoří ukázkovou databázi, uživatele root v databázi, otevře bránu firewall služby Azure a vytvoří výchozí pravidla brány firewall pro klientské počítače. To pomáhá urychlit proces vývoje.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Tento článek vyžaduje, že používáte Azure CLI verze 2.0 nebo novější místně. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

Budete se muset přihlásit ke svému účtu pomocí [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) příkazu. Z výstupu příkazu si poznamenejte vlastnost **id** pro odpovídající název předplatného.

```azurecli
az login
```

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account) konkrétní ID předplatného. Náhradní **id předplatného** vlastnost z **az login** výstupu pro vaše předplatné do zástupnou hodnotu id předplatného.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Vytvoření serveru Azure Database for PostgreSQL

Použití příkazů, nainstalujte [db-up](/cli/azure/ext/db-up) rozšíření. Pokud je vrácena chyba, ujistěte se, že máte nainstalovanou nejnovější verzi Azure CLI. Zobrazit [instalace Azure CLI](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Vytvoření Azure Database for PostgreSQL pomocí následujícího příkazu:

```azurecli
az postgres up
```

Vytvoří se následující výchozí hodnoty (Pokud jste ručně je přepsat):

**Nastavení** | **Výchozí hodnota** | **Popis**
---|---|---
název-serveru | Vygenerované systémem | Jedinečný název, který identifikuje váš server Azure Database for PostgreSQL.
resource-group | Vygenerované systémem | Novou skupinu prostředků Azure.
sku-name | GP_Gen5_2 | Název skladové jednotky. Dodržuje konvenci {cenová úroveň}\_{výpočetní generace}\_{virtuální jádra} ve zkráceném zápisu. Výchozí hodnota je server s 2 virtuální jádra pro obecné účely Gen5. Najdete v našich [stránce s cenami](https://azure.microsoft.com/pricing/details/postgresql/) Další informace o úrovních.
backup-retention | 7 | Určuje, jak dlouho se mají uchovávat zálohy. Jednotkou jsou dny.
geo-redundant-backup | Zakázáno | Určuje, jestli pro tento server mají nebo nemají být povolené geograficky redundantní zálohy.
location | westus2 | Lokace Azure pro server.
ssl-enforcement | Zakázáno | Určuje, jestli pro tento server má nebo nemá být povolený protokol SSL.
velikost úložiště | 5120 | Kapacita úložiště serveru (jednotkou jsou megabajty).
version | 10 | Hlavní verze PostgreSQL.
admin-user | Vygenerované systémem | Uživatelské jméno pro přihlášení správce.
admin-password | Vygenerované systémem | Heslo uživatele, který je správcem.

> [!NOTE]
> Další informace o `az postgres up` příkazu a její další parametry, najdete v článku [dokumentace k Azure CLI](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up).

Po vytvoření serveru se dodává s následujícími nastaveními:

- Vytvoření pravidla brány firewall s názvem "devbox". Rozhraní příkazového řádku Azure se pokusí zjistit IP adresu počítače `az postgres up` příkaz je spouštět z disku a povolených položek tuto IP adresu.
- "Povolit přístup ke službám Azure" nastavená na ON. Toto nastavení lze konfigurovat bránu firewall serveru tak, aby přijímal připojení ze všech prostředcích Azure, včetně prostředků není ve vašem předplatném.
- Vytvoří prázdnou databázi s názvem "sampledb"
- Vytvoření nového uživatele s názvem "root" s oprávněními k "sampledb"

> [!NOTE]
> Azure Database for PostgreSQL komunikuje přes port 5432. Pokud se připojujete z podnikové sítě, nemusí být odchozí provoz přes port 5432 bránou firewall vaší sítě povolený. Pořádejte oddělení IT otevřeli port 5432 pro připojení k serveru.

## <a name="get-the-connection-information"></a>Získání informací o připojení

Po `az postgres up` dokončení příkazu, je vrácen seznam připojovacích řetězců oblíbených programovacích jazyků. Tyto řetězce připojení jsou předem nakonfigurovaná s konkrétní atributy váš nově vytvořený server Azure Database for PostgreSQL.

Můžete použít [az postgres show-connection-string](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string) příkazu zobrazte výpis tyto řetězce připojení znovu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte všechny prostředky, které jste vytvořili v tomto rychlém startu pomocí následujícího příkazu. Tento příkaz odstraní serveru Azure Database for PostgreSQL a skupinu prostředků.

```azurecli
az postgres down --delete-group
```

Pokud chcete jenom odstranit nově vytvořený server, můžete spustit [az postgres dolů](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down) příkazu.

```azurecli
az postgres down
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Migrace vaší databáze pomocí exportu a importu](./howto-migrate-using-export-and-import.md)
