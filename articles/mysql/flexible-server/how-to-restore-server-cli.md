---
title: Obnovení Azure Database for MySQL – flexibilní Server s využitím Azure CLI
description: Tento článek popisuje, jak provádět operace obnovení v Azure Database for MySQL prostřednictvím rozhraní příkazového řádku Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 61a8439b770d8909e04d1b80a5219810dc72aa34
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509048"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-with-azure-cli"></a>Obnovení Azure Database for MySQL flexibilního serveru s využitím Azure CLI k určitému bodu v čase


> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Tento článek poskytuje podrobný postup, jak provádět obnovování v čase v flexibilním serveru pomocí zálohování.

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

## <a name="restore-a-server-from-backup-to-a-new-server"></a>Obnovení serveru ze zálohy na nový server

Pro obnovení serveru na nejstarší existující zálohu můžete spustit následující příkaz.

**Použití**
```azurecli
az mysql flexible-server restore --restore-time
                                 --source-server
                                 [--ids]
                                 [--location]
                                 [--name]
                                 [--no-wait]
                                 [--resource-group]
                                 [--subscription]
```

**Příklad:** Obnoví Server z tohoto ```2021-03-03T13:10:00Z``` záložního snímku.

```azurecli
az mysql server restore \
--name mydemoserver-restored \
--resource-group myresourcegroup \
--restore-point-in-time "2021-03-03T13:10:00Z" \
--source-server mydemoserver
```
Doba potřebná k obnovení bude záviset na velikosti dat uložených na serveru.

## <a name="perform-post-restore-tasks"></a>Provádění úloh po obnovení
Po dokončení obnovení byste měli provést následující úlohy, aby se uživatelé a aplikace mohli zálohovat a spustit:

- Pokud má nový server nahradit původní server, přesměrujte klienty a klientské aplikace na nový server.
- Aby se uživatelé mohli připojit, zajistěte, aby byla k dismístě vhodná pravidla VNet. Tato pravidla se nekopírují z původního serveru.
- Zajistěte, aby byla zajištěna příslušná přihlášení a oprávnění na úrovni databáze.
- Konfigurace výstrah podle vhodnosti pro nově obnovený server

## <a name="next-steps"></a>Další kroky
Další informace o [kontinuitě podnikových aplikací](concepts-business-continuity.md)

