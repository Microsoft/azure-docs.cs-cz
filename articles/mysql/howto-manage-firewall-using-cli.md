---
title: Vytvářet a spravovat databáze Azure pro pravidla brány firewall MySQL pomocí rozhraní příkazového řádku Azure
description: Tento článek popisuje postup vytvoření a správě Azure databáze MySQL pravidla brány firewall pomocí příkazového řádku Azure CLI.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 1bb60a1d6f14f0dc81047a2ffdd514724954fb14
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266053"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Vytvářet a spravovat databáze Azure pro pravidla brány firewall MySQL pomocí rozhraní příkazového řádku Azure
Pravidla brány firewall na úrovni serveru umožňují správcům řídit přístup k databázi Azure pro Server databáze MySQL z konkrétní IP adresu nebo rozsah IP adres. Pomocí vhodného rozhraní příkazového řádku Azure, můžete vytvořit, aktualizovat, odstranit, seznamu a zobrazit pravidla brány firewall ke správě serveru. Přehled informací o Azure databáze MySQL brány firewall, najdete v článku [databáze Azure pro pravidla brány firewall serveru MySQL](./concepts-firewall-rules.md)

## <a name="prerequisites"></a>Požadavky
* [Instalace rozhraní příkazového řádku Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).
* [Azure databáze MySQL server a databáze](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Příkazy pravidlo brány firewall:
**Az mysql pravidla brány firewall-** příkaz z příkazového řádku Azure slouží k vytvoření, odstranění, seznam, zobrazit a aktualizovat pravidla brány firewall.

Příkazy:
- **vytvoření**: vytvoření pravidla brány firewall serveru Azure MySQL.
- **Odstranit**: odstranění pravidla brány firewall serveru Azure MySQL.
- **seznam**: seznam pravidla brány firewall serveru Azure MySQL.
- **Zobrazit**: Zobrazit podrobnosti o serveru databáze MySQL Azure pravidlo brány firewall.
- **Aktualizovat**: aktualizovat pravidlo brány firewall serveru Azure MySQL.

## <a name="log-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Seznam vaší databázi Azure pro servery, MySQL a přihlaste se k Azure
Bezpečně připojit pomocí rozhraní příkazového řádku Azure s vaším účtem Azure **az přihlášení** příkaz.

1. Na příkazovém řádku spusťte následující příkaz:
```azurecli
az login
```
Tento příkaz vypíše kódu pro použití v dalším kroku.

2. Použít webový prohlížeč otevře se stránka [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin)a pak zadejte kód.

3. V příkazovém řádku se přihlaste pomocí vašich přihlašovacích údajů Azure.

4. Po vaše přihlášení oprávnění, seznam předplatného je vytištěna v konzole. Zkopírujte ID požadované předplatné nastavte aktuální předplatné použít. Použití [nastaven účet az](/cli/azure/account#az_account_set) příkaz.
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Zobrazí seznam Azure databáze MySQL serverů pro vaše předplatné nebo skupině prostředků, pokud si nejste jistí názvy. Použití [seznam serverů mysql az](/cli/azure/mysql/server#az_mysql_server_list) příkaz.

   ```azurecli-interactive
   az mysql server list --resource-group myresourcegroup
   ```

   Poznámka: v seznamu, který je třeba zadat server MySQL pro práci na atribut názvu. V případě potřeby potvrďte podrobnosti pro tento server a ujistěte se, že je správně pomocí atribut názvu. Použití [az mysql serveru zobrazit](/cli/azure/mysql/server#az_mysql_server_show) příkaz.

   ```azurecli-interactive
   az mysql server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Seznam pravidel brány firewall v databázi Azure pro Server databáze MySQL 
Pomocí názvu serveru a název skupiny prostředků, seznamu existující pravidla brány firewall serveru na serveru. Použití [az mysql server brány firewall seznamu](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_list) příkaz.  Všimněte si, že je atribut název serveru zadaný v **– server** přepínače a nikoli v **– název** přepínače. 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Výstup obsahuje pravidla, pokud existuje, ve formátu JSON formátu (ve výchozím nastavení). Můžete použít **– výstupní tabulku** přepínače k vypsání výsledků v přehlednějším tvaru tabulky.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Vytvořte pravidlo brány firewall v databázi Azure pro Server databáze MySQL
Pomocí Azure MySQL název serveru a název skupiny prostředků, vytvořte nové pravidlo brány firewall na serveru. Použití [vytvoření brány firewall serveru mysql az](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create) příkaz. Zadejte název pravidla a také počáteční IP adresa a koncovou IP (pro poskytování přístupu k rozsahu IP adres) pro pravidlo.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Povolit přístup pro jednu IP adresu, zadejte stejnou IP adresu jako počáteční IP a koncové IP adresy, jako v následujícím příkladu.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Povolit aplikacím z Azure IP adres se připojit k vaší databázi Azure pro server databáze MySQL, zadejte IP adresu 0.0.0.0 jako počáteční IP a koncové IP adresy, jako v následujícím příkladu.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

Po úspěšné vytvářet každý příkaz výstup uvádí podrobnosti o pravidlo brány firewall, které jste vytvořili, ve formátu JSON (ve výchozím nastavení). Pokud dojde k selhání, ukazuje výstup text chybové zprávy.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Aktualizace databáze Azure pravidlo brány firewall pro server databáze MySQL 
Pomocí Azure MySQL název serveru a název skupiny prostředků, aktualizujte existující pravidlo brány firewall na serveru. Použití [az mysql server brány firewall aktualizace](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_update) příkaz. Zadejte název existující pravidla brány firewall jako vstup, jakož i spuštění IP adresy a koncové IP atributů k aktualizaci.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Po úspěšné výstupu příkazu jsou uvedeny podrobnosti o pravidlo brány firewall, které byly aktualizovány, ve formátu JSON (ve výchozím nastavení). Pokud dojde k selhání, ukazuje výstup text chybové zprávy.

> [!NOTE]
> Pokud pravidlo brány firewall neexistuje, vytvoří se pravidlo příkaz aktualizace.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Zobrazit brány firewall podrobnosti pravidla na databázi Azure pro Server databáze MySQL
Pomocí Azure MySQL název serveru a název skupiny prostředků, zobrazit existující brány firewall pravidla podrobnosti ze serveru. Použití [az mysql server brány firewall zobrazit](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_show) příkaz. Zadejte název existující pravidla brány firewall jako vstup.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Po úspěšné výstupu příkazu jsou uvedeny podrobnosti o pravidlo brány firewall, které jste zadali, ve formátu JSON (ve výchozím nastavení). Pokud dojde k selhání, ukazuje výstup text chybové zprávy.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Odstranit pravidlo brány firewall databáze Azure pro Server databáze MySQL
Pomocí Azure MySQL název serveru a název skupiny prostředků, odeberte existující pravidlo brány firewall ze serveru. Použití [odstranit az mysql serverová brána firewall](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_delete) příkaz. Zadejte název existující pravidlo brány firewall.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Po úspěšné neexistuje žádný výstup. Při selhání zobrazí text chybové zprávy.

## <a name="next-steps"></a>Další postup
- Lépe porozumět o [databáze Azure pro pravidla brány firewall serveru MySQL](./concepts-firewall-rules.md).
- [Vytvářet a spravovat databáze Azure pro pravidla brány firewall MySQL pomocí portálu Azure](./howto-manage-firewall-using-portal.md).
