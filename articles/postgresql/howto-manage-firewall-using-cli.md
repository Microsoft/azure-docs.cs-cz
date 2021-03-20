---
title: Správa pravidel brány firewall – Azure CLI – Azure Database for PostgreSQL – samostatný server
description: Tento článek popisuje, jak vytvořit a spravovat pravidla brány firewall v Azure Database for PostgreSQL jednom serveru pomocí příkazového řádku Azure CLI.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3a559b8c65ab57b0144b807a3b4cc1faa912d430
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93422735"
---
# <a name="create-and-manage-firewall-rules-in-azure-database-for-postgresql---single-server-using-azure-cli"></a>Vytvoření a Správa pravidel brány firewall na Azure Database for PostgreSQLm samostatném serveru pomocí Azure CLI
Pravidla brány firewall na úrovni serveru se dají použít ke správě přístupu k serveru Azure Database for PostgreSQL z konkrétní IP adresy nebo rozsahu IP adres. Pomocí pohodlných příkazů rozhraní příkazového řádku Azure můžete vytvářet, aktualizovat, odstraňovat, zobrazovat a zobrazovat pravidla brány firewall pro správu serveru. Přehled Azure Database for PostgreSQL pravidel brány firewall najdete v tématu [pravidla brány firewall pro Azure Database for PostgreSQL serveru](concepts-firewall-rules.md).

Pravidla Virtual Network (VNet) je možné použít také k zabezpečení přístupu k serveru. Přečtěte si další informace o [vytváření a správě koncových bodů a pravidel služby Virtual Network pomocí Azure CLI](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Předpoklady
Pokud chcete projít tento průvodce, budete potřebovat:
- Nainstalujte nástroj příkazového řádku [Azure CLI](/cli/azure/install-azure-cli) nebo použijte Azure Cloud Shell v prohlížeči.
- [Server a databáze Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Konfigurace pravidel brány firewall pro Azure Database for PostgreSQL
Příkazy [AZ Postgres server firewall-Rule](/cli/azure/postgres/server/firewall-rule) slouží ke konfiguraci pravidel brány firewall.

## <a name="list-firewall-rules"></a>Vypsat pravidla brány firewall 
Pokud chcete zobrazit seznam existujících pravidel brány firewall serveru, spusťte příkaz [AZ Postgres server firewall-Rule list](/cli/azure/postgres/server/firewall-rule) .
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Výstup vypíše pravidla brány firewall, pokud jsou ve výchozím nastavení ve formátu JSON. Jako výstup můžete použít přepínač `--output table` pro čitelnější formát tabulky.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Vytvoření pravidla brány firewall
Chcete-li vytvořit nové pravidlo brány firewall na serveru, spusťte příkaz [AZ Postgres server firewall-Rule Create](/cli/azure/postgres/server/firewall-rule) . 


Chcete-li umožnit přístup k IP adrese jednotného přihlašování, zadejte v části a stejnou adresu, `--start-ip-address` `--end-ip-address` jako v tomto příkladu, a nahraďte zde uvedenou IP adresu konkrétní adresou IP.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Pokud chcete aplikacím z IP adres Azure umožnit připojení k vašemu Azure Database for PostgreSQL serveru, zadejte IP adresu 0.0.0.0 jako počáteční IP adresu a koncovou IP adresu, jako v tomto příkladu.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup jenom na autorizované uživatele.
> 

Po úspěšném provedení příkazu zobrazí výstup příkazu Podrobnosti pravidla brány firewall, které jste vytvořili, ve výchozím nastavení ve formátu JSON. Pokud dojde k selhání, zobrazí se ve výstupu chybová zpráva.

## <a name="update-firewall-rule"></a>Aktualizovat pravidlo brány firewall 
Aktualizujte stávající pravidlo brány firewall na serveru pomocí příkazu [AZ Postgres server firewall-Rule Update](/cli/azure/postgres/server/firewall-rule) . Zadejte název existujícího pravidla brány firewall jako vstup a zahajte aktualizace atributů počáteční IP a koncová IP adresa.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
Po úspěšném provedení příkazu zobrazí výstup příkazu podrobnosti o pravidlu brány firewall, které jste aktualizovali, ve výchozím nastavení ve formátu JSON. Pokud dojde k selhání, zobrazí se ve výstupu chybová zpráva.
> [!NOTE]
> Pokud pravidlo brány firewall neexistuje, bude vytvořeno příkazem Update.

## <a name="show-firewall-rule-details"></a>Zobrazit podrobnosti pravidla brány firewall
Můžete také zobrazit podrobnosti o existujícím pravidlu brány firewall na úrovni serveru spuštěním příkazu [AZ Postgres server firewall-Rule show](/cli/azure/postgres/server/firewall-rule) .
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Po úspěšném provedení příkazu zobrazí výstup příkazu podrobnosti o pravidlu brány firewall, které jste zadali, ve výchozím nastavení ve formátu JSON. Pokud dojde k selhání, zobrazí se ve výstupu chybová zpráva.

## <a name="delete-firewall-rule"></a>Odstranit pravidlo brány firewall
Chcete-li odvolat přístup pro rozsah IP adres na server, odstraňte existující pravidlo brány firewall spuštěním příkazu [AZ Postgres server firewall-Rule Delete](/cli/azure/postgres/server/firewall-rule) . Zadejte název existujícího pravidla brány firewall.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Po úspěšném provedení neproběhne žádný výstup. Po selhání se vrátí text chybové zprávy.

## <a name="next-steps"></a>Další kroky
- Podobně můžete pomocí webového prohlížeče [vytvořit a spravovat Azure Database for PostgreSQL pravidla brány firewall pomocí Azure Portal](howto-manage-firewall-using-portal.md).
- Další informace o [pravidlech brány firewall serveru Azure Database for PostgreSQL](concepts-firewall-rules.md).
- [Vytvořením a správou koncových bodů a pravidel služby Virtual Network pomocí rozhraní příkazového řádku Azure](howto-manage-vnet-using-cli.md)získáte další zabezpečený přístup k serveru.
- Nápovědu k připojení k serveru Azure Database for PostgreSQL najdete v tématu [knihovny připojení pro Azure Database for PostgreSQL](concepts-connection-libraries.md).
