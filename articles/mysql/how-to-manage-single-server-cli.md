---
title: Správa serveru – Azure CLI – Azure Database for MySQL
description: Naučte se spravovat Azure Database for MySQL server z rozhraní příkazového řádku Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: bd068f0cf76a8edefca854d72d5240c0becaf8fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542061"
---
# <a name="manage-an-azure-database-for-mysql-single-server-using-the-azure-cli"></a>Správa samostatného serveru s Azure Database for MySQL pomocí rozhraní příkazového řádku Azure

V tomto článku se dozvíte, jak spravovat jednotlivé servery nasazené v Azure. Úlohy správy zahrnují výpočetní výkon a škálování úložiště, resetování hesla správce a zobrazení podrobností serveru.

## <a name="prerequisites"></a>Předpoklady
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. Tento článek vyžaduje, abyste v místním prostředí používali Azure CLI verze 2,0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

K účtu se budete muset přihlásit pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) . Poznamenejte si vlastnost **ID** , která se vztahuje k **ID předplatného** pro váš účet Azure.

```azurecli-interactive
az login
```

Pomocí příkazu [AZ Account set](/cli/azure/account) vyberte konkrétní předplatné ve vašem účtu. Poznamenejte si hodnotu **ID** z výstupu **AZ Login** , který se použije jako hodnota argumentu **Subscription** v příkazu. Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Pokud chcete získat veškeré předplatné, použijte příkaz [AZ Account list](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

Pokud jste ještě nevytvořili Server, můžete si ho vytvořit v tomto [rychlém](quickstart-create-mysql-server-database-using-azure-cli.md) startu.

## <a name="scale-compute-and-storage"></a>Škálování výpočetních prostředků a úložiště
Pomocí následujícího příkazu můžete snadno škálovat svou cenovou úroveň, výpočetní výkon a úložiště. Můžete zobrazit všechny operace serveru, které můžete provést [AZ MySQL server Overview](/cli/azure/mysql/server)

```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

Zde jsou uvedeny podrobnosti o argumentech výše:

**Nastavení** | **Ukázková hodnota** | **Popis**
---|---|---
name | mydemoserver | Zadejte jedinečný název serveru Azure Database for MySQL. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí se skládat ze 3 až 63 znaků.
resource-group | myresourcegroup | Zadejte název skupiny prostředků Azure.
sku-name|GP_Gen5_2|Zadejte název cenové úrovně a konfiguraci výpočtů. Postupuje podle konvence {cenové úrovně}_{COMPUTE_} {virtuální jádra} ve zkráceném znění. Další informace najdete v [cenové úrovni](./concepts-pricing-tiers.md) .
velikost úložiště | 6144 | Kapacita úložiště serveru (jednotkou jsou megabajty). Minimální 5120 a zvyšuje se v 1024 přírůstcích.

> [!Important]
> - Úložiště je možné škálovat nahoru (úložiště ale nejde škálovat dolů).
> - Škálování z úrovně Basic na pro obecné účely nebo pro paměťově optimalizovanou cenovou úroveň se nepodporuje. Ruční horizontální navýšení kapacity můžete vytvořit [pomocí skriptu bash](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404) nebo [pomocí aplikace MySQL Workbench](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134) .


## <a name="manage-mysql-databases-on-a-server"></a>Správa databází MySQL na serveru
Pomocí kteréhokoli z těchto příkazů můžete vytvořit, odstranit, vypsat a zobrazit vlastnosti databáze na serveru aplikace.

| Rutina | Využití| Description |
| --- | ---| --- |
|[AZ MySQL DB Create](/cli/azure/sql/db#az-mysql-db-create)|```az mysql db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Vytvoří databázi.|
|[AZ MySQL DB DELETE](/cli/azure/sql/db#az-mysql-db-delete)|```az mysql db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Odstraňte databázi ze serveru. Tento příkaz neodstraní váš server. |
|[AZ MySQL DB list](/cli/azure/sql/db#az-mysql-db-list)|```az mysql db list -g myresourcegroup -s mydemoserver```|zobrazí všechny databáze na serveru.|
|[AZ MySQL DB show](/cli/azure/sql/db#az-mysql-db-show)|```az mysql db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Zobrazí další podrobnosti o databázi.|

## <a name="update-admin-password"></a>Aktualizovat heslo správce
Pomocí tohoto příkazu můžete změnit heslo role správce.
```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Ujistěte se, že heslo má minimálně 8 znaků a maximálně 128 znaků.
> Heslo musí obsahovat znaky ze tří z následujících kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice a jiné než alfanumerické znaky.

## <a name="delete-a-server"></a>Odstranění serveru
Pokud byste chtěli jenom odstranit jediný server MySQL, můžete spustit příkaz [AZ MySQL server Delete](/cli/azure/mysql/server#az-mysql-server-delete) .

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Další kroky
- [Restart serveru](howto-restart-server-cli.md)
- [Obnovení serveru v nesprávném stavu](howto-restore-server-cli.md)
- [Monitorování a vyladění serveru](concepts-monitoring.md)