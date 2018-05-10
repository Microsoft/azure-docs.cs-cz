---
title: Vytvářet a spravovat databáze Azure pro pravidla brány firewall PostgreSQL pomocí rozhraní příkazového řádku Azure
description: Tento článek popisuje postup vytvoření a správě Azure databáze pro pravidla brány firewall PostgreSQL pomocí příkazového řádku Azure CLI.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 05/4/2018
ms.openlocfilehash: ba5533184331b3692882b224b77ad1f38e970661
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Vytvářet a spravovat databáze Azure pro pravidla brány firewall PostgreSQL pomocí rozhraní příkazového řádku Azure
Pravidla brány firewall na úrovni serveru umožňují správcům řídit přístup k databázi Azure pro PostgreSQL Server z konkrétní IP adresu nebo rozsah IP adres. Pomocí vhodného rozhraní příkazového řádku Azure, můžete vytvořit, aktualizovat, odstranit, seznamu a zobrazit pravidla brány firewall ke správě serveru. Přehled Azure databáze pro pravidla brány firewall PostgreSQL najdete [databáze Azure pro pravidla brány firewall serveru PostgreSQL](concepts-firewall-rules.md)

## <a name="prerequisites"></a>Požadavky
Chcete-li krok tímto průvodcem postupy, je třeba:
- Nainstalujte [Azure CLI 2.0](/cli/azure/install-azure-cli) nástroj příkazového řádku nebo pomocí prostředí cloudové služby Azure v prohlížeči.
- [Databáze Azure pro PostgreSQL server a databáze](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Konfigurace pravidel brány firewall pro databázi Azure pro PostgreSQL
[Az postgres pravidla brány firewall-](/cli/azure/postgres/server/firewall-rule) příkazy se používají ke konfiguraci pravidel brány firewall.

## <a name="list-firewall-rules"></a>Seznam pravidel brány firewall 
Pro zobrazení seznamu existující pravidla brány firewall serveru, spusťte [seznamu pravidlo brány firewall serveru postgres az](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_list) příkaz.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Výstup obsahuje seznam pravidel brány firewall, pokud existuje, ve výchozím nastavení ve formátu JSON formátu. Můžete použít přepínač `--output table` pro přehlednějším tvaru tabulky jako výstup.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Vytvořte pravidlo brány firewall
Chcete-li vytvořit nové pravidlo brány firewall na serveru, spusťte [az postgres pravidla brány firewall-vytvořit](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) příkaz. 

Zadáním 0.0.0.0, jako `--start-ip-address` a 255.255.255.255 jako `--end-ip-address` rozsah, v následujícím příkladu umožňuje všechny IP adresy pro přístup k serveru **mydemoserver.postgres.database.azure.com**
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
Povolit přístup k singulární IP adresu, zadejte stejnou adresu v `--start-ip-address` a `--end-ip-address`, protože v tomto příkladu.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Povolit aplikace z Azure IP adresy pro připojení k vaší databázi Azure pro PostgreSQL server, zadejte IP adresu 0.0.0.0 jako počáteční IP a koncové IP adresy, jako v následujícím příkladu.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

Po úspěšné výstupu příkazu jsou uvedeny podrobnosti o pravidlo brány firewall, které jste vytvořili, ve výchozím nastavení ve formátu JSON. Pokud dojde k selhání, ukazuje výstup chybovou zprávu.

## <a name="update-firewall-rule"></a>Aktualizovat pravidla brány firewall 
Aktualizovat existující pravidlo brány firewall na serveru pomocí [aktualizace pravidlo brány firewall serveru postgres az](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_update) příkaz. Zadejte název existující pravidla brány firewall jako vstup a spusťte IP adresy a koncové IP atributů k aktualizaci.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.255
```
Po úspěšné výstupu příkazu jsou uvedeny podrobnosti o pravidlo brány firewall, které jste aktualizovali, ve výchozím nastavení ve formátu JSON. Pokud dojde k selhání, ukazuje výstup chybovou zprávu.
> [!NOTE]
> Pokud pravidlo brány firewall neexistuje, získá vytvořené pomocí příkazu update.

## <a name="show-firewall-rule-details"></a>Zobrazit podrobnosti o pravidlech brány firewall
Můžete také zobrazit podrobnosti existující pravidlo brány firewall na úrovni serveru spuštěním [zobrazit pravidlo brány firewall serveru postgres az](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_show) příkaz.
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Po úspěšné výstupu příkazu jsou uvedeny podrobnosti o pravidlo brány firewall, které jste zadali, ve výchozím nastavení ve formátu JSON. Pokud dojde k selhání, ukazuje výstup chybovou zprávu.

## <a name="delete-firewall-rule"></a>Odstranit pravidlo brány firewall
K odvolání přístupu pro rozsah adres IP na server, odstraňte existující pravidlo brány firewall tak, že spustíte [odstranit az postgres pravidla brány firewall-](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_delete) příkaz. Zadejte název existující pravidlo brány firewall.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Po úspěšné neexistuje žádný výstup. Při selhání je vrácen text chybové zprávy.

## <a name="next-steps"></a>Další postup
- Podobně můžete použít webový prohlížeč, aby [vytvořit a spravovat databáze Azure pro pravidla brány firewall PostgreSQL pomocí portálu Azure](howto-manage-firewall-using-portal.md).
- Lépe porozumět o [databáze Azure pro pravidla brány firewall serveru PostgreSQL](concepts-firewall-rules.md).
- Pomoc při připojování k databázi Azure pro PostgreSQL server najdete v tématu [knihovny připojení pro databázi Azure pro PostgreSQL](concepts-connection-libraries.md).
