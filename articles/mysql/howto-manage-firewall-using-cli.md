---
title: Vytvoření a správě Azure Database for MySQL pravidel brány firewall pomocí Azure CLI
description: Tento článek popisuje, jak vytvořit a spravovat Azure Database for MySQL pravidel firewallu pomocí příkazového řádku Azure CLI.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: e6bb06d8ae46afbb946754113e1d81a90e3ddc57
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423687"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Vytvoření a správě Azure Database for MySQL pravidla brány firewall pomocí Azure CLI
Pravidla brány firewall na úrovni serveru umožňuje správcům spravovat přístup ke službě Azure Database for MySQL Server z konkrétní IP adresu nebo rozsah IP adres. Použití vhodné příkazy rozhraní příkazového řádku Azure, můžete vytvořit, aktualizovat, odstranit, seznam a zobrazit pravidla brány firewall pro správu serveru. Přehled služby Azure Database pro MySQL brány firewall, naleznete v tématu [– Azure Database for pravidla firewallu serveru MySQL](./concepts-firewall-rules.md)

## <a name="prerequisites"></a>Požadavky
* [Instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).
* [– Azure Database for MySQL serveru a databáze](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Příkazy pravidlo brány firewall:
**Az mysql server firewall-rule** je pomocí příkazu z příkazového řádku Azure vytvářet, odstraňovat, seznamu, zobrazit a aktualizovat pravidla brány firewall.

Příkazy:
- **vytvoření**: vytvoření pravidla brány firewall na serveru Azure MySQL.
- **Odstranit**: odstranění pravidla brány firewall na serveru Azure MySQL.
- **seznam**: seznam pravidla firewallu pro server Azure MySQL.
- **Zobrazit**: Zobrazit podrobnosti o serveru Azure MySQL pravidlo brány firewall.
- **Aktualizovat**: aktualizovat pravidlo brány firewall na serveru Azure MySQL.

## <a name="log-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Připojte se k Azure a seznam databáze Azure pro servery MySQL
Bezpečně připojit pomocí rozhraní příkazového řádku Azure pomocí svého účtu Azure **az login** příkazu.

1. Z příkazového řádku spusťte následující příkaz:
```azurecli
az login
```
Tento příkaz vypíše kód, který použijete v dalším kroku.

2. Použít webový prohlížeč a otevřete tak stránku [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin)a pak zadejte kód.

3. V příkazovém řádku se přihlaste pomocí vašich přihlašovacích údajů Azure.

4. Po přihlášení je ověřen, seznam předplatných je vytištěna v konzole. Zkopírujte ID z požadovaného předplatného nastavit aktuální předplatné použít. Použití [az účet sady](/cli/azure/account#az-account-set) příkazu.
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Seznam databází Azure Database for MySQL serverů pro vaše předplatné a skupinu prostředků, pokud si nejste jistí názvy. Použití [az mysql server list](/cli/azure/mysql/server#az-mysql-server-list) příkazu.

   ```azurecli-interactive
   az mysql server list --resource-group myresourcegroup
   ```

   Poznámka: atribut name v seznamu, které budete muset zadat pro práci na serveru MySQL. V případě potřeby potvrďte podrobnosti pro tento server a použití atribut name, abyste zajistili, že je správný. Použití [az mysql server show](/cli/azure/mysql/server#az-mysql-server-show) příkazu.

   ```azurecli-interactive
   az mysql server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Výpis pravidel brány firewall na – Azure Database for MySQL serveru 
Použijte název serveru a název skupiny prostředků a seznamu existující pravidla brány firewall serveru na serveru. Použití [az mysql server firewall seznamu](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-list) příkazu.  Všimněte si, že je zadán atribut název serveru v **– server** přepnutí a nikoli v **– název** přepnout. 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Výstup obsahuje seznam pravidel, pokud existuje, ve formátu JSON formátu (ve výchozím nastavení). Můžete použít **– výstupní tabulky** přepínač k vypsání výsledků v přehlednějším tvaru tabulky.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Vytvoření pravidla brány firewall databáze Azure pro MySQL Server
Pomocí názvu serveru Azure MySQL a názvu skupiny prostředků, vytvořte nové pravidlo brány firewall na serveru. Použití [vytvořit az mysql server firewall](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) příkazu. Zadejte název pravidla, počáteční IP adresa a koncová IP adresa (pro poskytnutí přístupu k rozsahu IP adres) pro pravidlo.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Pokud chcete povolit přístup pro jednu IP adresu, zadejte stejnou IP adresu jako počáteční IP adresa a koncová IP adresa, jako v následujícím příkladu.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Pokud chcete umožnit aplikacím z Azure IP adres pro připojení k Azure Database for MySQL server, zadejte IP adresu 0.0.0.0 jako počáteční IP adresa a koncová IP adresa, jako v následujícím příkladu.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

Po úspěšném nasazení každý vytvořit příkaz, že výstup obsahuje podrobnosti pravidla brány firewall, které jste vytvořili, ve formátu JSON (ve výchozím nastavení). Pokud dojde k selhání, zobrazí výstup text chybové zprávy.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Aktualizovat pravidlo brány firewall databáze Azure pro MySQL server 
Pomocí názvu serveru Azure MySQL a názvu skupiny prostředků, aktualizujte stávající pravidlo brány firewall na serveru. Použití [az mysql server firewall aktualizace](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-update) příkazu. Zadejte název stávající pravidlo brány firewall jako vstup, stejně jako počáteční IP adresy a koncové IP atributů k aktualizaci.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Po úspěšném nasazení výstup tohoto příkazu jsou uvedeny podrobnosti pravidla brány firewall, které jste aktualizovali, ve formátu JSON (ve výchozím nastavení). Pokud dojde k selhání, zobrazí výstup text chybové zprávy.

> [!NOTE]
> Pokud neexistuje pravidlo brány firewall, je pravidlo vytvořené pomocí příkazu update.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Zobrazit brány firewall podrobnosti pravidla na databázi Azure pro MySQL Server
Pomocí názvu serveru Azure MySQL a názvu skupiny prostředků, zobrazí podrobnosti pravidla ze serveru pro existující bránu firewall. Použití [az mysql server firewall show](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-show) příkazu. Zadejte název stávající pravidlo brány firewall jako vstup.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Po úspěšném nasazení výstup tohoto příkazu jsou uvedeny podrobnosti pravidla brány firewall, které jste zadali, ve formátu JSON (ve výchozím nastavení). Pokud dojde k selhání, zobrazí výstup text chybové zprávy.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Odstranit pravidlo brány firewall databáze Azure pro MySQL Server
Pomocí názvu serveru Azure MySQL a názvu skupiny prostředků, odeberte stávající pravidlo brány firewall ze serveru. Použití [az mysql server firewall odstranit](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-delete) příkazu. Zadejte název stávající pravidlo brány firewall.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Po úspěšném nasazení neexistuje žádný výstup. Nebude úspěšná zobrazí text chybové zprávy.

## <a name="next-steps"></a>Další postup
- Zjistit více o [– Azure Database for MySQL Server firewall pravidla](./concepts-firewall-rules.md).
- [Vytvoření a správě Azure Database for MySQL – pravidla brány firewall pomocí webu Azure portal](./howto-manage-firewall-using-portal.md).
