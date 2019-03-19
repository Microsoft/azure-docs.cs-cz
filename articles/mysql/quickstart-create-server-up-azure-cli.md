---
title: Rychlý start – vytvoření Azure Database for MySQL pomocí jednoduchého příkazu Azure CLI – az mysql nahoru (preview)
description: Úvodní příručka k vytvoření Azure Database for MySQL pomocí Azure CLI (rozhraní příkazového řádku) se příkaz.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2019
ms.custom: mvc
ms.openlocfilehash: aa0d2a9e990faa8d99355744824f34e26aeb519e
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137908"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Rychlý start: Vytvoření Azure Database for MySQL pomocí jednoduchého příkazu Azure CLI – az mysql nahoru (preview)

> [!IMPORTANT]
> [Az mysql nahoru](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) rozhraní příkazového řádku Azure je ve verzi preview.

Azure Database for MySQL je spravovaná služba, která umožňuje spouštět, spravovat a škálovat vysoce dostupné databáze MySQL v cloudu. Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. V tomto rychlém startu se dozvíte, jak používat [az mysql nahoru](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) příkaz pro vytvoření Azure Database for MySQL pomocí Azure CLI. Kromě vytvoření serveru, `az mysql up` příkaz vytvoří ukázkovou databázi, uživatele root v databázi, otevře bránu firewall služby Azure a vytvoří výchozí pravidla brány firewall pro klientské počítače. To pomáhá urychlit proces vývoje.

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

## <a name="create-an-azure-database-for-mysql-server"></a>Vytvoření serveru Azure Database for MySQL

Použití příkazů, nainstalujte [db-up](/cli/azure/ext/db-up) rozšíření. Pokud je vrácena chyba, ujistěte se, že máte nainstalovanou nejnovější verzi Azure CLI. Zobrazit [instalace Azure CLI](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Vytvoření serveru Azure Database for MySQL pomocí následujícího příkazu:

```azurecli
az mysql up
```

Vytvoří se následující výchozí hodnoty (Pokud jste ručně je přepsat):

**Nastavení** | **Výchozí hodnota** | **Popis**
---|---|---
název-serveru | Vygenerované systémem | Jedinečný název, který identifikuje váš server Azure Database for MySQL.
resource-group | Vygenerované systémem | Novou skupinu prostředků Azure.
sku-name | GP_Gen5_2 | Název skladové jednotky. Dodržuje konvenci {cenová úroveň}\_{výpočetní generace}\_{virtuální jádra} ve zkráceném zápisu. Výchozí hodnota je server s 2 virtuální jádra pro obecné účely Gen5. Najdete v našich [stránce s cenami](https://azure.microsoft.com/pricing/details/mysql/) Další informace o úrovních.
backup-retention | 7 | Určuje, jak dlouho se mají uchovávat zálohy. Jednotkou jsou dny.
geo-redundant-backup | Zakázáno | Určuje, jestli pro tento server mají nebo nemají být povolené geograficky redundantní zálohy.
location | westus2 | Lokace Azure pro server.
ssl-enforcement | Zakázáno | Určuje, jestli pro tento server má nebo nemá být povolený protokol SSL.
velikost úložiště | 5120 | Kapacita úložiště serveru (jednotkou jsou megabajty).
version | 5.7 | Hlavní verze MySQL.
admin-user | Vygenerované systémem | Uživatelské jméno pro přihlášení správce.
admin-password | Vygenerované systémem | Heslo uživatele, který je správcem.

> [!NOTE]
> Další informace o `az mysql up` příkazu a její další parametry, najdete v článku [dokumentace k Azure CLI](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up).

Po vytvoření serveru se dodává s následujícími nastaveními:

- Vytvoření pravidla brány firewall s názvem "devbox". Rozhraní příkazového řádku Azure se pokusí zjistit IP adresu počítače `az mysql up` příkaz je spouštět z disku a povolených položek tuto IP adresu.
- "Povolit přístup ke službám Azure" nastavená na ON. Toto nastavení lze konfigurovat bránu firewall serveru tak, aby přijímal připojení ze všech prostředcích Azure, včetně prostředků není ve vašem předplatném.
- `wait_timeout` Parametr je nastaven na 8 hodin
- Vytvoří prázdnou databázi s názvem "sampledb"
- Vytvoření nového uživatele s názvem "root" s oprávněními k "sampledb"

> [!NOTE]
> Azure Database for MySQL komunikuje přes port 3306. Když se připojujete z podnikové sítě, nemusí být odchozí provoz přes port 3306 povolený bránou firewall vaší sítě. Pořádejte oddělení IT, otevřete port 3306 pro připojení k serveru.

## <a name="get-the-connection-information"></a>Získání informací o připojení

Po `az mysql up` dokončení příkazu, je vrácen seznam připojovacích řetězců oblíbených programovacích jazyků. Tyto řetězce připojení jsou předem nakonfigurovaná s konkrétní atributy váš nově vytvořený server Azure Database for MySQL.

Můžete použít [az mysql show-connection-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) příkazu zobrazte výpis tyto řetězce připojení znovu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte všechny prostředky, které jste vytvořili v tomto rychlém startu pomocí následujícího příkazu. Tento příkaz odstraní serveru Azure Database for MySQL a skupinu prostředků.

```azurecli
az mysql down --delete-group
```

Pokud chcete jenom odstranit nově vytvořený server, můžete spustit [az mysql dolů](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) příkazu.

```azurecli
az mysql down
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Návrh databáze MySQL pomocí rozhraní příkazového řádku Azure](./tutorial-design-database-using-cli.md)
