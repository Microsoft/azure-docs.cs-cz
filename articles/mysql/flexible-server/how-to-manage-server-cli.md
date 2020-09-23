---
title: Správa serveru – Azure CLI – Azure Database for MySQL flexibilní Server
description: Naučte se spravovat Azure Database for MySQL flexibilní Server z Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 3798396c72bc01bc20f1b4ee3ee66961fe33bff5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935080"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>Správa Azure Database for MySQLho flexibilního serveru (ve verzi Preview) pomocí Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

V tomto článku se dozvíte, jak spravovat flexibilní Server (ve verzi Preview) nasazený v Azure. Úlohy správy zahrnují výpočetní výkon a škálování úložiště, resetování hesla správce a zobrazení podrobností serveru.

## <a name="prerequisites"></a>Požadavky
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. Tento článek vyžaduje, abyste v místním prostředí používali Azure CLI verze 2,0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

K účtu se budete muset přihlásit pomocí příkazu [AZ Login](https://docs.microsoft.com/cli/azure/reference-index#az-login) . Poznamenejte si vlastnost **ID** , která se vztahuje k **ID předplatného** pro váš účet Azure.

```azurecli-interactive
az login
```

Pomocí příkazu [AZ Account set](/cli/azure/account) vyberte konkrétní předplatné ve vašem účtu. Poznamenejte si hodnotu **ID** z výstupu **AZ Login** , který se použije jako hodnota argumentu **Subscription** v příkazu. Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Pokud chcete získat veškeré předplatné, použijte příkaz [AZ Account list](https://docs.microsoft.com/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Pokud jste ještě nevytvořili flexibilní Server, vytvořte si ho prosím, abyste mohli začít s tímto průvodcem.

## <a name="scale-compute-and-storage"></a>Škálování výpočetních prostředků a úložiště

Pomocí následujícího příkazu můžete snadno škálovat výpočetní vrstvu, virtuální jádra a úložiště. Můžete zobrazit všechny operace serveru, které můžete provést [AZ MySQL Flexible-Server Server Overview](/cli/azure/mysql/server)

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

Zde jsou uvedeny podrobnosti o argumentech výše:

**Nastavení** | **Ukázková hodnota** | **Popis**
---|---|---
jméno | mydemoserver | Zadejte jedinečný název serveru Azure Database for MySQL. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí se skládat ze 3 až 63 znaků.
resource-group | myresourcegroup | Zadejte název skupiny prostředků Azure.
sku-name|Standard_D4ds_v4|Zadejte název výpočetní úrovně a velikosti. Postupuje podle konvence Standard_ {VM Size} ve zkráceném formátu. Další informace najdete v [cenové úrovni](../concepts-pricing-tiers.md) .
velikost úložiště | 6144 | Kapacita úložiště serveru (jednotkou jsou megabajty). Minimální 5120 a zvyšuje se v 1024 přírůstcích.

> [!Important]
> - Úložiště je možné škálovat nahoru (úložiště ale nejde škálovat dolů).


## <a name="manage-mysql-databases-on-a-server"></a>Spravujte databáze MySQL na serveru.
Pomocí kteréhokoli z těchto příkazů můžete vytvořit, odstranit, vypsat a zobrazit vlastnosti databáze na serveru aplikace.

| Rutina | Využití| Description |
| --- | ---| --- |
|[AZ MySQL Flexible-Server DB Create](/cli/azure/sql/db#az-mysql-flexible-server-db-create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Vytvoří databázi.|
|[AZ MySQL Flexible-Server DB DELETE](/cli/azure/sql/db#az-mysql-flexible-server-db-delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Odstraňte databázi ze serveru. Tento příkaz neodstraní váš server. |
|[AZ MySQL Flexible-Server DB list](/cli/azure/sql/db#az-mysql-flexible-server-db-list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|zobrazí všechny databáze na serveru.|
|[AZ MySQL Flexible-Server DB show](/cli/azure/sql/db#az-mysql-flexible-server-db-show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Zobrazí další podrobnosti o databázi.|

## <a name="update-admin-password"></a>Aktualizovat heslo správce
Pomocí tohoto příkazu můžete změnit heslo role správce.
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Ujistěte se, že heslo má minimálně 8 znaků a maximálně 128 znaků.
> Heslo musí obsahovat znaky ze tří z následujících kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice a jiné než alfanumerické znaky.

## <a name="delete-a-server"></a>Odstranění serveru
Pokud byste chtěli jenom odstranit flexibilní Server MySQL, můžete spustit příkaz [AZ MySQL flexibilní-Server Server Delete](/cli/azure/mysql/server#az-mysql-flexible-server-delete) .

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Další kroky
- [Informace o tom, jak spustit nebo zastavit server](how-to-stop-start-server-portal.md)
- [Informace o tom, jak spravovat virtuální síť](how-to-manage-virtual-network-cli.md)
- [Řešení potíží s připojením](how-to-troubleshoot-common-connection-issues.md)
- [Vytvoření a správa brány firewall](how-to-manage-firewall-cli.md)
