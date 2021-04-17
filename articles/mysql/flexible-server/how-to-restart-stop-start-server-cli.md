---
title: Restart/Stop/Start-Azure Portal-Azure Database for MySQL flexibilní Server
description: Tento článek popisuje, jak restartovat/zastavit/spustit operace v Azure Database for MySQL prostřednictvím rozhraní příkazového řádku Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: c9e646ad40c669e51f052ac9888cdd7c6883a848
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509049"
---
# <a name="restartstopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Restartování/zastavení/spuštění Azure Database for MySQL-flexibilního serveru (Preview)

> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

V tomto článku se dozvíte, jak provést restart, spustit a zastavit flexibilní Server pomocí Azure CLI.

## <a name="prerequisites"></a>Požadavky
- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Nainstalujte nebo upgradujte Azure CLI na nejnovější verzi. Viz Instalace rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).
-  Přihlaste se k účtu Azure pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) . Poznamenejte si vlastnost **ID** , která se vztahuje k **ID předplatného** pro váš účet Azure.

    ```azurecli-interactive
    az login
    ````

- Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém chcete vytvořit server pomocí ```az account set``` příkazu.
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Vytvořte flexibilní Server MySQL, pokud jste ho ještě nevytvořili pomocí ```az mysql flexible-server create``` příkazu.

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="stop-a-running-server"></a>Zastavení běžícího serveru
Chcete-li zastavit server, spusťte  ```az mysql flexible-server stop``` příkaz. Pokud používáte [místní kontext](/cli/azure/config/param-persist), nemusíte zadávat žádné argumenty.

**Využívání**
```azurecli
az mysql flexible-server stop  [--name]
                               [--resource-group]
                               [--subscription]
```

**Příklad bez místního kontextu:**
```azurecli
az mysql flexible-server stop  --resource-group --name myservername
```

**Příklad s místním kontextem:**
```azurecli
az mysql flexible-server stop
```

## <a name="start-a-stopped-server"></a>Spuštění zastaveného serveru
Pokud chcete spustit server, spusťte  ```az mysql flexible-server start``` příkaz. Pokud používáte [místní kontext](/cli/azure/config/param-persist), nemusíte zadávat žádné argumenty.

**Využívání**
```azurecli
az mysql flexible-server start [--name]
                               [--resource-group]
                               [--subscription]
```

**Příklad bez místního kontextu:**
```azurecli
az mysql flexible-server start  --resource-group --name myservername
```

**Příklad s místním kontextem:**
```azurecli
az mysql flexible-server start
```

> [!IMPORTANT]
> Po úspěšném restartování serveru jsou nyní všechny operace správy k dispozici pro flexibilní Server.

## <a name="restart-a-server"></a>Restart serveru
Chcete-li restartovat server, spusťte  ```az mysql flexible-server restart``` příkaz. Pokud používáte [místní kontext](/cli/azure/config/param-persist), nemusíte zadávat žádné argumenty.

**Využívání**
```azurecli
az mysql flexible-server restart [--name]
                                 [--resource-group]
                                 [--subscription]
```

**Příklad bez místního kontextu:**
```azurecli
az mysql flexible-server restart  --resource-group --name myservername
```

**Příklad s místním kontextem:**
```azurecli
az mysql flexible-server restart
```


> [!IMPORTANT]
> Po úspěšném restartování serveru jsou nyní všechny operace správy k dispozici pro flexibilní Server.

## <a name="next-steps"></a>Další kroky
- Další informace o [sítích v Azure Database for MySQL flexibilním serveru](./concepts-networking.md)
- [Vytvářejte a spravujte Azure Database for MySQL flexibilní serverovou virtuální síť pomocí Azure Portal](./how-to-manage-virtual-network-portal.md).

