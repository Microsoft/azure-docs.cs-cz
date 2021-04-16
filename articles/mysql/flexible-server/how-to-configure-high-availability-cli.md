---
title: Správa redundantní vysoké dostupnosti zóny – Azure CLI – Azure Database for MySQL flexibilní Server
description: Tento článek popisuje, jak nakonfigurovat redundantní vysokou dostupnost zóny v Azure Database for MySQL flexibilním serveru pomocí Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/1/2021
ms.custom: references_regions
ms.openlocfilehash: fb53ad309c741fc898bcf3e27347038c0e382ea4
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509059"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-with-azure-cli"></a>Správa redundantní vysoké dostupnosti zóny v Azure Database for MySQL flexibilním serveru pomocí Azure CLI

> [!NOTE]
> Azure Database for MySQL flexibilní Server je ve verzi Public Preview. 

Tento článek popisuje, jak povolit nebo zakázat konfiguraci redundantního vysoké dostupnosti zóny v době vytváření serveru v flexibilním serveru. Po vytvoření serveru můžete zakázat redundantní vysokou dostupnost zóny. Zapnutí redundantní vysoké dostupnosti zóny po vytvoření serveru se nepodporuje.

Funkce vysoké dostupnosti zřídí fyzicky samostatnou primární a pohotovostní repliku v různých zónách. Další informace najdete v [dokumentaci k konceptům vysoké dostupnosti](./concepts/../concepts-high-availability.md). Povolení nebo zákaz vysoké dostupnosti nemění vaše další nastavení, včetně konfigurace virtuální sítě, nastavení brány firewall a uchovávání záloh. Zakázání vysoké dostupnosti nemá vliv na připojení a operace vaší aplikace.

> [!IMPORTANT]
> Redundantní vysoká dostupnost zóny je k dispozici v omezené sadě oblastí. Seznam podporovaných oblastí najdete [tady](https://docs.microsoft.com/azure/mysql/flexible-server/overview#azure-regions). 

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

## <a name="enable-high-availability-during-server-creation"></a>Povolit vysokou dostupnost během vytváření serveru
Server můžete vytvářet jenom pomocí cenových úrovní pro obecné účely nebo paměťově optimalizované s vysokou dostupností. Můžete povolit vysokou dostupnost serveru pouze během doby vytváření.

**Využívání**

```azurecli
az mysql flexible-server create [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**Příklad:**
```azurecli
az mysql flexible-server create --name myservername --sku-name Standard-D2ds_v4 --resource-group myresourcegroup --high-availability Enabled
```

## <a name="disable-high-availability"></a>Zakázat vysokou dostupnost

Vysokou dostupnost můžete vypnout pomocí příkazu [AZ MySQL Flexible-Server Update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update) . Vypnutí vysoké dostupnosti se podporuje jenom v případě, že se Server vytvořil s vysokou dostupností. 

```azurecli
az mysql flexible-server update [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**Příklad:**
```azurecli
az mysql flexible-server update --resource-group myresourcegroup --name myservername --high-availability Disabled
```


## <a name="next-steps"></a>Další kroky

-   Informace o [kontinuitě podnikových aplikací](./concepts-business-continuity.md)
-   Přečtěte si o [vysoké dostupnosti zóny jako redundantní](./concepts-high-availability.md) .
