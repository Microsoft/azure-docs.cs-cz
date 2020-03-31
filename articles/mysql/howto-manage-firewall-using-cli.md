---
title: Správa pravidel brány firewall – Azure CLI – Azure Database for MySQL
description: Tento článek popisuje, jak vytvořit a spravovat pravidla brány firewall Azure Database for MySQL pomocí příkazového řádku Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 11aa4a80deba4df14c239e69910ea38bac1b9c55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063513"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Vytváření a správa pravidel brány firewall Azure Database for MySQL pomocí příkazového příkazového příkazu Azure
Pravidla brány firewall na úrovni serveru lze použít ke správě přístupu k databázi Azure pro MySQL Server z určité IP adresy nebo rozsahu IP adres. Pomocí pohodlných příkazů Azure CLI můžete vytvářet, aktualizovat, odstraňovat, seznamovat a zobrazovat pravidla brány firewall pro správu serveru. Přehled bran azure databáze pro mySQL firewall najdete v [tématu Azure Database for MySQL server firewall rules](./concepts-firewall-rules.md).

Pravidla virtuální sítě (VNet) lze také použít k zabezpečení přístupu k serveru. Další informace o [vytváření a správě koncových bodů a pravidel služby Virtuální síť pomocí azure cli](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Požadavky
* [Nainstalovat rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
* [Databáze Azure pro mysql server a databázi](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Příkazy pravidel brány firewall:
Příkaz **az mysql server firewall-rule** se používá z azure CLI k vytvoření, odstranění, seznam, zobrazit a aktualizovat pravidla brány firewall.

Příkazy:
- **create**: Vytvořte pravidlo brány firewall serveru Azure MySQL.
- **delete**: Odstranění pravidla brány firewall serveru Azure MySQL.
- **Seznam**: Seznam pravidel brány firewall serveru Azure MySQL.
- **zobrazit**: Zobrazit podrobnosti pravidla brány firewall serveru Azure MySQL.
- **aktualizace**: Aktualizace pravidla brány firewall serveru Azure MySQL.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Přihlaste se do Azure a uveďte svou databázi Azure pro servery MySQL
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

5. Seznam databází Azure pro servery MySQL pro vaše předplatné a skupinu prostředků, pokud si nejste jisti názvy. Použijte příkaz [az mysql server list.](/cli/azure/mysql/server#az-mysql-server-list)

    ```azurecli-interactive
    az mysql server list --resource-group myresourcegroup
    ```

   Poznamenejte si atribut name v seznamu, na kterém je třeba zadat server MySQL, na kterém chcete pracovat. V případě potřeby potvrďte podrobnosti pro tento server a pomocí atributu name ujistěte se, že je správný. Použijte příkaz [az mysql server show.](/cli/azure/mysql/server#az-mysql-server-show)

    ```azurecli-interactive
    az mysql server show --resource-group myresourcegroup --name mydemoserver
    ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Seznam pravidel brány firewall v azure databázi pro MySQL Server 
Pomocí názvu serveru a názvu skupiny prostředků uveďte existující pravidla brány firewall serveru na serveru. Použijte příkaz [az mysql server firewall list.](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-list)  Všimněte si, že atribut název serveru je určen v přepínači **--server** a ne v přepínači **--name.** 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Výstup uvádí pravidla, pokud existuje, ve formátu JSON (ve výchozím nastavení). Přepínač **--output table** můžete použít k výstupu výsledků ve čitelnějším formátu tabulky.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Vytvoření pravidla brány firewall v databázi Azure pro MySQL Server
Pomocí názvu serveru Azure MySQL a názvu skupiny prostředků vytvořte na serveru nové pravidlo brány firewall. Použijte příkaz [az mysql server firewall create.](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) Zadejte název pravidla, stejně jako počáteční IP a koncovou IP adresu (pro poskytnutí přístupu k rozsahu IP adres) pro pravidlo.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Chcete-li povolit přístup pro jednu adresu IP, zadejte stejnou adresu IP jako počáteční IP a koncovou IP adresu, jako v tomto příkladu.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Chcete-li aplikacím z IP adres Azure povolit připojení k databázi Azure pro server MySQL, zadejte IP adresu 0.0.0.0 jako počáteční IP a koncovou IP adresu, jako v tomto příkladu.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

Po úspěchu každý výstup příkazu vytvořit uvádí podrobnosti o pravidle brány firewall, které jste vytvořili, ve formátu JSON (ve výchozím nastavení). Pokud dojde k selhání, výstup zobrazí text chybové zprávy místo.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Aktualizace pravidla brány firewall na Azure Database pro server MySQL 
Pomocí názvu serveru Azure MySQL a názvu skupiny prostředků aktualizujte existující pravidlo brány firewall na serveru. Použijte příkaz [az mysql server firewall update.](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-update) Zadejte název existujícího pravidla brány firewall jako vstup, stejně jako počáteční atributy IP a end IP, které mají být aktualizovány.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Po úspěchu je ve výstupu příkazu uvedeny podrobnosti o pravidle brány firewall, které jste aktualizovali ve formátu JSON (ve výchozím nastavení). Pokud dojde k selhání, výstup zobrazí text chybové zprávy místo.

> [!NOTE]
> Pokud pravidlo brány firewall neexistuje, je pravidlo vytvořeno příkazem update.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Zobrazit podrobnosti pravidla brány firewall v azure databázi pro MySQL Server
Pomocí názvu serveru Azure MySQL a názvu skupiny prostředků zobrazte existující podrobnosti pravidla brány firewall ze serveru. Použijte [příkaz az mysql server firewall show.](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-show) Zadejte název existujícího pravidla brány firewall jako vstup.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Po úspěchu je ve výstupu příkazu uvedeny podrobnosti o zadaném pravidle brány firewall ve formátu JSON (ve výchozím nastavení). Pokud dojde k selhání, výstup zobrazí text chybové zprávy místo.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Odstranění pravidla brány firewall v databázi Azure pro MySQL Server
Pomocí názvu serveru Azure MySQL a názvu skupiny prostředků odeberte ze serveru existující pravidlo brány firewall. Použijte příkaz [odstranit bránu firewall az mysql server.](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-delete) Zadejte název existujícího pravidla brány firewall.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Po úspěchu neexistuje žádný výstup. Po selhání se zobrazí text chybové zprávy.

## <a name="next-steps"></a>Další kroky
- Další informace o [pravidlech brány firewall Azure Database for MySQL Server](./concepts-firewall-rules.md).
- [Vytvořte a spravujte pravidla brány firewall Azure Database for MySQL pomocí portálu Azure](./howto-manage-firewall-using-portal.md).
- Další zabezpečení přístupu k serveru [vytvořením a správou koncových bodů služby virtuální sítě a pravidel pomocí azure CLI](howto-manage-vnet-using-cli.md).