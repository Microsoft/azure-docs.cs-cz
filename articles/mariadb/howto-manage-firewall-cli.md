---
title: Správa pravidel brány firewall – Azure CLI – databáze Azure pro MariaDB
description: Tento článek popisuje, jak vytvořit a spravovat pravidla brány firewall Azure Database for MariaDB pomocí příkazového řádku Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 898b65f07140bca04bd97ff7314b01920b783914
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530627"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Vytvoření a správa pravidel brány firewall Azure Database for MariaDB pomocí příkazového příkazového příkazu Azure
Pravidla brány firewall na úrovni serveru lze použít ke správě přístupu k databázi Azure pro MariaDB Server z konkrétní IP adresy nebo rozsahu IP adres. Pomocí pohodlných příkazů Azure CLI můžete vytvářet, aktualizovat, odstraňovat, seznamovat a zobrazovat pravidla brány firewall pro správu serveru. Přehled bran firewall Azure Database for MariaDB najdete v tématu [Azure Database for MariaDB server firewall rules](./concepts-firewall-rules.md).

Pravidla virtuální sítě (VNet) lze také použít k zabezpečení přístupu k serveru. Další informace o [vytváření a správě koncových bodů a pravidel služby Virtuální síť pomocí azure cli](howto-manage-vnet-cli.md).

## <a name="prerequisites"></a>Požadavky
* [Nainstalovat rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
* [Databáze Azure pro server MariaDB a databázi](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Příkazy pravidel brány firewall:
Příkaz **az mariadb server firewall-rule** se používá z Azure CLI k vytvoření, odstranění, seznam, zobrazit a aktualizovat pravidla brány firewall.

Příkazy:
- **create**: Vytvořte pravidlo brány firewall serveru Azure MariaDB.
- **delete**: Odstranění pravidla brány firewall serveru Azure MariaDB.
- **Seznam**: Seznam pravidel brány firewall serveru Azure MariaDB.
- **zobrazit**: Zobrazit podrobnosti pravidla brány firewall serveru Azure MariaDB.
- **update**: Aktualizace pravidla brány firewall serveru Azure MariaDB.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Přihlášení k Azure a seznam databáze Azure pro servery MariaDB
Bezpečně připojte Azure CLI se svým účtem Azure pomocí příkazu **az login.**

1. Z příkazového řádku spusťte následující příkaz:
   ```azurecli
   az login
   ```
   Tento příkaz vyveze kód, který se použije v dalším kroku.

2. Otevřete stránku [https://aka.ms/devicelogin](https://aka.ms/devicelogin)pomocí webového prohlížeče a zadejte kód.

3. Na výzvu se přihlaste pomocí přihlašovacích údajů Azure.

4. Po autorizaci přihlášení se v konzoli vytiskne seznam předplatných. Zkopírujte ID požadovaného předplatného a nastavte aktuální předplatné, které má být používáno. Použijte příkaz [az účet nastavit.](/cli/azure/account#az-account-set)
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Seznam databází Azure pro mariadb servery pro vaše předplatné a skupinu prostředků, pokud si nejste jisti názvy. Použijte příkaz [az mariadb server list.](/cli/azure/mariadb/server#az-mariadb-server-list)

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Poznamenejte si atribut name v seznamu, na kterém je třeba zadat server MariaDB, na kterém chcete pracovat. V případě potřeby potvrďte podrobnosti pro tento server a pomocí atributu name ujistěte se, že je správný. Použijte příkaz [az mariadb server show.](/cli/azure/mariadb/server#az-mariadb-server-show)

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Seznam pravidel brány firewall v databázi Azure pro server MariaDB 
Pomocí názvu serveru a názvu skupiny prostředků uveďte existující pravidla brány firewall serveru na serveru. Použijte příkaz [az mariadb server firewall list.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list)  Všimněte si, že atribut název serveru je určen v přepínači **--server** a ne v přepínači **--name.** 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Výstup uvádí pravidla, pokud existuje, ve formátu JSON (ve výchozím nastavení). Přepínač **--output table** můžete použít k výstupu výsledků ve čitelnějším formátu tabulky.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Vytvoření pravidla brány firewall v databázi Azure pro server MariaDB
Pomocí názvu serveru Azure MariaDB a názvu skupiny prostředků vytvořte na serveru nové pravidlo brány firewall. Použijte příkaz [az mariadb server firewall create.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) Zadejte název pravidla, stejně jako počáteční IP a koncovou IP adresu (pro poskytnutí přístupu k rozsahu IP adres) pro pravidlo.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Chcete-li povolit přístup pro jednu adresu IP, zadejte stejnou adresu IP jako počáteční IP a koncovou IP adresu, jako v tomto příkladu.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Chcete-li aplikacím z IP adres Azure povolit připojení k serveru Azure Database for MariaDB, zadejte IP adresu 0.0.0.0 jako počáteční IP a koncovou IP adresu, jako v tomto příkladu.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

Po úspěchu každý výstup příkazu vytvořit uvádí podrobnosti o pravidle brány firewall, které jste vytvořili, ve formátu JSON (ve výchozím nastavení). Pokud dojde k selhání, výstup zobrazí text chybové zprávy místo.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Aktualizace pravidla brány firewall na azure databázi pro server MariaDB 
Pomocí názvu serveru Azure MariaDB a názvu skupiny prostředků aktualizujte existující pravidlo brány firewall na serveru. Použijte příkaz [aktualizace brány firewall serveru az mariadb.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update) Zadejte název existujícího pravidla brány firewall jako vstup, stejně jako počáteční atributy IP a end IP, které mají být aktualizovány.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Po úspěchu je ve výstupu příkazu uvedeny podrobnosti o pravidle brány firewall, které jste aktualizovali ve formátu JSON (ve výchozím nastavení). Pokud dojde k selhání, výstup zobrazí text chybové zprávy místo.

> [!NOTE]
> Pokud pravidlo brány firewall neexistuje, je pravidlo vytvořeno příkazem update.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Zobrazit podrobnosti pravidla brány firewall v databázi Azure pro server MariaDB
Pomocí názvu serveru Azure MariaDB a názvu skupiny prostředků zobrazte existující podrobnosti pravidla brány firewall ze serveru. Použijte příkaz [az mariadb server firewall show.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show) Zadejte název existujícího pravidla brány firewall jako vstup.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Po úspěchu je ve výstupu příkazu uvedeny podrobnosti o zadaném pravidle brány firewall ve formátu JSON (ve výchozím nastavení). Pokud dojde k selhání, výstup zobrazí text chybové zprávy místo.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Odstranění pravidla brány firewall v databázi Azure pro server MariaDB
Pomocí názvu serveru Azure MariaDB a názvu skupiny prostředků odeberte ze serveru existující pravidlo brány firewall. Použijte příkaz [delete brány firewall serveru az mariadb.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete) Zadejte název existujícího pravidla brány firewall.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Po úspěchu neexistuje žádný výstup. Po selhání se zobrazí text chybové zprávy.

## <a name="next-steps"></a>Další kroky
- Další informace o [pravidlech brány firewall Azure Database for MariaDB Server](./concepts-firewall-rules.md).
- [Vytvořte a spravujte pravidla brány firewall Azure Database for MariaDB pomocí portálu Azure](./howto-manage-firewall-portal.md).
- Další zabezpečení přístupu k serveru [vytvořením a správou koncových bodů služby virtuální sítě a pravidel pomocí azure CLI](howto-manage-vnet-cli.md).
