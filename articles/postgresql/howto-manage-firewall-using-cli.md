---
title: Správa pravidel brány firewall – Azure CLI – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje, jak vytvořit a spravovat pravidla brány firewall v Azure Database for PostgreSQL – jeden server pomocí příkazového řádku Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4af0fb288961689fb051bab8091c838f793cfcc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74765643"
---
# <a name="create-and-manage-firewall-rules-in-azure-database-for-postgresql---single-server-using-azure-cli"></a>Vytváření a správa pravidel brány firewall v Azure Database for PostgreSQL – jeden server pomocí azure CLI
Pravidla brány firewall na úrovni serveru lze použít ke správě přístupu k databázi Azure pro PostgreSQL Server z konkrétní IP adresy nebo rozsahu IP adres. Pomocí pohodlných příkazů Azure CLI můžete vytvářet, aktualizovat, odstraňovat, seznamovat a zobrazovat pravidla brány firewall pro správu serveru. Přehled pravidel brány firewall Azure Database for PostgreSQL najdete v [tématu Azure Database for PostgreSQL Server firewall rules](concepts-firewall-rules.md).

Pravidla virtuální sítě (VNet) lze také použít k zabezpečení přístupu k serveru. Další informace o [vytváření a správě koncových bodů a pravidel služby Virtuální síť pomocí azure cli](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Požadavky
Chcete-li projít tento návod, co potřebujete:
- Nainstalujte nástroj příkazového řádku [Azure CLI](/cli/azure/install-azure-cli) nebo použijte Azure Cloud Shell v prohlížeči.
- [Databáze Azure pro postgreSQL server a databázi](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Konfigurace pravidel brány firewall pro Azure Database for PostgreSQL
Příkazy [az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) se používají ke konfiguraci pravidel brány firewall.

## <a name="list-firewall-rules"></a>Seznam pravidel brány firewall 
Chcete-li vypsat existující pravidla brány firewall serveru, spusťte příkaz az postgres server firewall-rule.To list the existing server firewall rules, spusťte příkaz [az postgres server firewall-rule.To](/cli/azure/postgres/server/firewall-rule) list the existing server firewall rules, spusťte příkaz az postgres server firewall
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Výstup uvádí pravidla brány firewall, pokud existuje, ve výchozím nastavení ve formátu JSON. Jako výstup můžete `--output table` použít přepínač pro čitelnější formát tabulky.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Vytvoření pravidla brány firewall
Chcete-li na serveru vytvořit nové pravidlo brány firewall, spusťte příkaz vytvořit pravidlo [brány firewall az postgres serveru.](/cli/azure/postgres/server/firewall-rule) 

```
To allow access to a singular IP address, provide the same address in the `--start-ip-address` and `--end-ip-address`, as in this example, replacing the IP shown here with your specific IP.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Chcete-li aplikacím z IP adres Azure povolit připojení k serveru Azure Database for PostgreSQL, zadejte IP adresu 0.0.0.0 jako počáteční IP a koncovou IP adresu, jako v tomto příkladu.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

Po úspěchu je ve výstupu příkazu uvedeny podrobnosti o vytvořeném pravidle brány firewall ve výchozím nastavení ve formátu JSON. Pokud dojde k selhání, výstup zobrazí chybovou zprávu místo.

## <a name="update-firewall-rule"></a>Aktualizovat pravidlo brány firewall 
Aktualizujte existující pravidlo brány firewall na serveru pomocí [příkazu aktualizace pravidla brány firewall serveru az postgres.](/cli/azure/postgres/server/firewall-rule) Zadejte název existujícího pravidla brány firewall jako vstup a počáteční atributy IP a end IP, které chcete aktualizovat.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
Po úspěchu je ve výstupu příkazu uvedeny podrobnosti o pravidle brány firewall, které jste aktualizovali, ve výchozím nastavení ve formátu JSON. Pokud dojde k selhání, výstup zobrazí chybovou zprávu místo.
> [!NOTE]
> Pokud pravidlo brány firewall neexistuje, vytvoří se příkazem update.

## <a name="show-firewall-rule-details"></a>Zobrazit podrobnosti pravidla brány firewall
Můžete také zobrazit podrobnosti o existujícím pravidle brány firewall na úrovni serveru spuštěním [příkazu az postgres server firewall-rule show.](/cli/azure/postgres/server/firewall-rule)
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Po úspěchu je ve výstupu příkazu uvedeny podrobnosti o zadaném pravidle brány firewall ve výchozím nastavení ve formátu JSON. Pokud dojde k selhání, výstup zobrazí chybovou zprávu místo.

## <a name="delete-firewall-rule"></a>Odstranit pravidlo brány firewall
Chcete-li odvolat přístup k serveru v rozsahu IP adres, odstraňte existující pravidlo brány firewall spuštěním příkazu [delete příkazu delete az postgres server firewall-rule.](/cli/azure/postgres/server/firewall-rule) Zadejte název existujícího pravidla brány firewall.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Po úspěchu neexistuje žádný výstup. Po selhání je vrácen text chybové zprávy.

## <a name="next-steps"></a>Další kroky
- Podobně můžete pomocí webového prohlížeče [vytvořit a spravovat pravidla brány firewall Azure Database for PostgreSQL pomocí portálu Azure](howto-manage-firewall-using-portal.md).
- Další informace o [pravidlech brány firewall Azure Database for PostgreSQL Server](concepts-firewall-rules.md).
- Další zabezpečení přístupu k serveru [vytvořením a správou koncových bodů služby virtuální sítě a pravidel pomocí azure CLI](howto-manage-vnet-using-cli.md).
- Nápovědu k připojení k serveru Azure Database for PostgreSQL najdete v [tématu Connection libraries for Azure Database for PostgreSQL](concepts-connection-libraries.md).
