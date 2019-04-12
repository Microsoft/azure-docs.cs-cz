---
title: Vytváření a správa Azure Database pro MariaDB pravidel brány firewall pomocí Azure CLI
description: Tento článek popisuje, jak vytvářet a spravovat Azure Database pro MariaDB pravidla brány firewall pomocí příkazového řádku Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 562987b953f0a8a20a917e208f43557bd768c0a0
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495224"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Vytváření a správa Azure Database pro MariaDB pravidla brány firewall pomocí Azure CLI
Pravidla brány firewall na úrovni serveru můžete použít ke správě přístupu ke službě Azure Database pro MariaDB Server z konkrétní IP adresu nebo rozsah IP adres. Použití vhodné příkazy rozhraní příkazového řádku Azure, můžete vytvořit, aktualizovat, odstranit, seznam a zobrazit pravidla brány firewall pro správu serveru. Přehled služby Azure Database pro MariaDB brány firewall naleznete v tématu [– Azure Database pro MariaDB pravidla brány firewall serveru](./concepts-firewall-rules.md).

Pravidel virtuální sítě (VNet) je také možné zabezpečit přístup k vašemu serveru. Další informace o [vytváření a Správa virtuální sítě služby koncových bodů a pravidel pomocí Azure CLI](howto-manage-vnet-cli.md).

## <a name="prerequisites"></a>Požadavky
* [Nainstalovat rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
* [– Azure Database pro MariaDB serveru a databáze](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Příkazy pravidlo brány firewall:
**Az mariadb server firewall-rule** je pomocí příkazu z příkazového řádku Azure vytvářet, odstraňovat, seznamu, zobrazit a aktualizovat pravidla brány firewall.

Příkazy:
- **vytvoření**: Vytvoření pravidla brány firewall serveru Azure MariaDB.
- **Odstranit**: Odstranění pravidla brány firewall serveru Azure MariaDB.
- **Seznam**: Seznam pravidel brány firewall serveru Azure MariaDB.
- **Zobrazit**: Zobrazí podrobnosti o serveru Azure MariaDB pravidlo brány firewall.
- **update**: Aktualizujte pravidlo brány firewall serveru Azure MariaDB.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Přihlaste se k Azure a seznamu Azure Database pro MariaDB servery
Bezpečně připojit pomocí rozhraní příkazového řádku Azure pomocí svého účtu Azure **az login** příkazu.

1. Z příkazového řádku spusťte následující příkaz:
   ```azurecli
   az login
   ```
   Tento příkaz vypíše kód, který použijete v dalším kroku.

2. Použít webový prohlížeč a otevřete tak stránku [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin)a pak zadejte kód.

3. Na řádku přihlaste se pomocí přihlašovacích údajů Azure.

4. Po přihlášení je ověřen, seznam předplatných je vytištěna v konzole. Zkopírujte ID z požadovaného předplatného nastavit aktuální předplatné použít. Použití [az účet sady](/cli/azure/account#az-account-set) příkazu.
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Seznam databází Azure Database pro MariaDB serverů pro vaše předplatné a skupinu prostředků, pokud si nejste jistí názvy. Použití [seznam serverů mariadb az](/cli/azure/mariadb/server#az-mariadb-server-list) příkazu.

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Poznámka: atribut name v seznamu, který je třeba zadat MariaDB server pro práci na. V případě potřeby potvrďte podrobnosti pro tento server a použití atribut name, abyste zajistili, že je správný. Použití [az mariadb server show](/cli/azure/mariadb/server#az-mariadb-server-show) příkazu.

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Výpis pravidel brány firewall v Azure Database pro MariaDB Server 
Použijte název serveru a název skupiny prostředků a seznamu existující pravidla brány firewall serveru na serveru. Použití [az mariadb server brány firewall seznamu](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list) příkazu.  Všimněte si, že je zadán atribut název serveru v **– server** přepnutí a nikoli v **– název** přepnout. 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Výstup obsahuje seznam pravidel, pokud existuje, ve formátu JSON formátu (ve výchozím nastavení). Můžete použít **– výstupní tabulky** přepínač k vypsání výsledků v přehlednějším tvaru tabulky.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Vytvoření pravidla brány firewall v Azure Database pro MariaDB Server
Pomocí Azure MariaDB název serveru a název skupiny prostředků, vytvořte nové pravidlo brány firewall na serveru. Použití [vytvořit bránu firewall serveru mariadb az](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) příkazu. Zadejte název pravidla, počáteční IP adresa a koncová IP adresa (pro poskytnutí přístupu k rozsahu IP adres) pro pravidlo.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Pokud chcete povolit přístup pro jednu IP adresu, zadejte stejnou IP adresu jako počáteční IP adresa a koncová IP adresa, jako v následujícím příkladu.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Pokud chcete umožnit aplikacím z Azure IP adresy pro připojení k Azure Database pro MariaDB server, zadejte IP adresu 0.0.0.0 jako počáteční IP adresa a koncová IP adresa, jako v následujícím příkladu.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

Po úspěšném nasazení každý vytvořit příkaz, že výstup obsahuje podrobnosti pravidla brány firewall, které jste vytvořili, ve formátu JSON (ve výchozím nastavení). Pokud dojde k selhání, zobrazí výstup text chybové zprávy.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Aktualizovat pravidlo brány firewall v Azure Database pro MariaDB server 
Pomocí Azure MariaDB, název serveru a název skupiny prostředků, aktualizovat stávající pravidlo brány firewall na serveru. Použití [aktualizace brány firewall serveru mariadb az](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update) příkazu. Zadejte název stávající pravidlo brány firewall jako vstup, stejně jako počáteční IP adresy a koncové IP atributů k aktualizaci.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Po úspěšném nasazení výstup tohoto příkazu jsou uvedeny podrobnosti pravidla brány firewall, které jste aktualizovali, ve formátu JSON (ve výchozím nastavení). Pokud dojde k selhání, zobrazí výstup text chybové zprávy.

> [!NOTE]
> Pokud neexistuje pravidlo brány firewall, je pravidlo vytvořené pomocí příkazu update.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Zobrazit brány firewall podrobnosti pravidla v Azure Database pro MariaDB Server
Existující brány firewall pomocí Azure MariaDB název serveru a název skupiny prostředků, zobrazí podrobnosti pravidla ze serveru. Použití [az mariadb server brány firewall show](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show) příkazu. Zadejte název stávající pravidlo brány firewall jako vstup.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Po úspěšném nasazení výstup tohoto příkazu jsou uvedeny podrobnosti pravidla brány firewall, které jste zadali, ve formátu JSON (ve výchozím nastavení). Pokud dojde k selhání, zobrazí výstup text chybové zprávy.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Odstranit pravidlo brány firewall v Azure Database pro MariaDB Server
Stávající pravidlo brány firewall pomocí Azure MariaDB název serveru a název skupiny prostředků, odeberte ze serveru. Použití [odstranit bránu firewall serveru mariadb az](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete) příkazu. Zadejte název stávající pravidlo brány firewall.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Po úspěšném nasazení neexistuje žádný výstup. Nebude úspěšná zobrazí text chybové zprávy.

## <a name="next-steps"></a>Další postup
- Zjistit více o [– Azure Database for pravidla brány firewall serveru MariaDB](./concepts-firewall-rules.md).
- [Vytváření a správa Azure Database pro MariaDB pravidla brány firewall pomocí webu Azure portal](./howto-manage-firewall-portal.md).
- Dále zabezpečit přístup k vašemu serveru pomocí [vytváření a Správa virtuální sítě služby koncových bodů a pravidel pomocí Azure CLI](howto-manage-vnet-cli.md).
