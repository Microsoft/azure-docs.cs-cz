---
title: 'Rychlý Start: vytvoření Azure Database for MySQL pomocí AZ MySQL up'
description: Průvodce rychlým startem k vytvoření Azure Database for MySQL serveru pomocí příkazu Azure CLI (rozhraní příkazového řádku)
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d89cc41ed26124ae4ad2e6689be6d59278c3d9da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542163"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Rychlý Start: vytvoření Azure Database for MySQL pomocí jednoduchého příkazu rozhraní příkazového řádku Azure – AZ MySQL up (Preview)

> [!IMPORTANT]
> Příkaz [AZ MySQL up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI je ve verzi Preview.

Azure Database for MySQL je spravovaná služba, která umožňuje spouštět, spravovat a škálovat vysoce dostupné databáze MySQL v cloudu. Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. V tomto rychlém startu se dozvíte, jak pomocí příkazu [AZ MySQL up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) vytvořit server Azure Database for MySQL pomocí rozhraní příkazového řádku Azure CLI. Kromě vytvoření serveru `az mysql up` vytvoří příkaz ukázkovou databázi, root user v databázi, otevře bránu firewall pro služby Azure a vytvoří výchozí pravidla brány firewall pro klientský počítač. To pomáhá urychlit proces vývoje.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Tento článek vyžaduje, abyste v místním prostředí používali Azure CLI verze 2,0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

K účtu se budete muset přihlásit pomocí příkazu [AZ Login](/cli/azure/authenticate-azure-cli) . Z výstupu příkazu si poznamenejte vlastnost **id** pro odpovídající název předplatného.

```azurecli
az login
```

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account) konkrétní ID předplatného. Nahraďte vlastnost **ID předplatného** z výstupu **AZ Login** pro vaše předplatné na zástupný symbol ID předplatného.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Vytvoření serveru Azure Database for MySQL

Chcete-li použít příkazy, nainstalujte rozšíření [DB](/cli/azure/ext/db-up) . Pokud se vrátí chyba, ujistěte se, že máte nainstalovanou nejnovější verzi rozhraní příkazového řádku Azure CLI. Viz Instalace rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Pomocí následujícího příkazu vytvořte server Azure Database for MySQL:

```azurecli
az mysql up
```

Server se vytvoří s následujícími výchozími hodnotami (Pokud je nepřepíšete ručně):

**Nastavení** | **Výchozí hodnota** | **Popis**
---|---|---
název-serveru | Vygenerované systémem | Jedinečný název, který identifikuje váš server Azure Database for MySQL.
resource-group | Vygenerované systémem | Nová skupina prostředků Azure.
sku-name | GP_Gen5_2 | Název sku. Dodržuje konvenci {cenová úroveň}\_{výpočetní generace}\_{virtuální jádra} ve zkráceném zápisu. Výchozím nastavením je Pro obecné účely Server Gen5 se 2 virtuální jádra. Další informace o úrovních najdete na naší [stránce s cenami](https://azure.microsoft.com/pricing/details/mysql/) .
backup-retention | 7 | Určuje, jak dlouho se mají uchovávat zálohy. Jednotkou jsou dny.
geo-redundant-backup | Zakázáno | Určuje, jestli pro tento server mají nebo nemají být povolené geograficky redundantní zálohy.
location | westus2 | Lokace Azure pro server.
ssl-enforcement | Povoleno | Zda má být pro tento server povolen nebo nikoli protokol SSL.
velikost úložiště | 5120 | Kapacita úložiště serveru (jednotkou jsou megabajty).
verze | 5.7 | Hlavní verze MySQL.
admin-user | Vygenerované systémem | Uživatelské jméno pro přihlášení správce.
admin-password | Vygenerované systémem | Heslo uživatele, který je správcem.

> [!NOTE]
> Další informace o `az mysql up` příkazu a jeho dalších parametrech najdete v dokumentaci k rozhraní příkazového [řádku Azure CLI](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up).

Po vytvoření serveru se objeví následující nastavení:

- Vytvoří se pravidlo brány firewall nazvané "devbox". Rozhraní příkazového řádku Azure CLI se pokusí zjistit IP adresu počítače, `az mysql up` ze kterého se spouští příkaz, a umožňuje tuto IP adresu.
- Možnost "povolení přístupu ke službám Azure" je nastavená na ZAPNUTo. Toto nastavení nakonfiguruje bránu firewall serveru tak, aby přijímala připojení ze všech prostředků Azure, včetně prostředků, které se ve vašem předplatném nepoužívá.
- `wait_timeout`Parametr je nastavený na 8 hodin.
- Vytvoří se prázdná databáze s názvem "sampledb".
- Vytvoří se nový uživatel s názvem root s oprávněními k SampleDB.

> [!NOTE]
> Azure Database for MySQL komunikuje přes port 3306. Pokud se připojujete z podnikové sítě, nemusí být odchozí provoz přes port 3306 bránou firewall vaší sítě povolený. Vaše IT oddělení otevře port 3306 pro připojení k vašemu serveru.

## <a name="get-the-connection-information"></a>Získání informací o připojení

Po `az mysql up` dokončení příkazu se vám vrátí seznam připojovacích řetězců pro oblíbené programovací jazyky. Tyto připojovací řetězce jsou předem nakonfigurované s konkrétními atributy nově vytvořeného serveru Azure Database for MySQL.

Pomocí příkazu [AZ MySQL show-Connection-String](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) můžete tyto připojovací řetězce znovu zobrazit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Všechny prostředky, které jste vytvořili v rychlém startu, vyčistěte pomocí následujícího příkazu. Tento příkaz odstraní Server Azure Database for MySQL a skupinu prostředků.

```azurecli
az mysql down --delete-group
```

Pokud byste chtěli odstranit nově vytvořený server, můžete spustit příkaz [AZ MySQL Down](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) .

```azurecli
az mysql down
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Návrh databáze MySQL pomocí Azure CLI](./tutorial-design-database-using-cli.md)
