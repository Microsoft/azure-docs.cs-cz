---
title: Šifrování v infrastruktuře – Azure Portal-Azure Database for MySQL
description: Naučte se nastavit a spravovat dvojité šifrování infrastruktury pro vaše Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 3f9c22a690859b459b6bb748c3b1001c4aa7660d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93241748"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-mysql"></a>Dvojité šifrování infrastruktury pro Azure Database for MySQL

Naučte se, jak nastavit a spravovat dvojité šifrování infrastruktury pro váš Azure Database for MySQL.

## <a name="prerequisites"></a>Předpoklady

* Musíte mít předplatné Azure a mít oprávnění správce k tomuto předplatnému.

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---portal"></a>Vytvoření serveru Azure Database for MySQL s využitím šifrování infrastruktury – portál

Pomocí těchto kroků můžete vytvořit Azure Database for MySQL server s šifrováním s dvojitou infrastrukturou v Azure Portal:

1. V levém horním rohu portálu vyberte **vytvořit prostředek** (+).

2. Vyberte **databáze**  >  **Azure Database for MySQL**. Službu můžete vyhledat také zadáním **MySQL** do vyhledávacího pole.

   :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="Možnost Azure Database for MySQL":::

3. Zadejte základní informace o serveru. Chcete-li nastavit parametr, vyberte **Další nastavení** a povolit zaškrtávací políčko **infrastruktura – dvojité šifrování** .

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-selected.png" alt-text="Výběry Azure Database for MySQL":::

4. Vyberte **zkontrolovat + vytvořit** a zřiďte Server.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-summary.png" alt-text="Souhrn Azure Database for MySQL":::

5. Po vytvoření serveru můžete ověřit šifrované šifrování infrastruktury tím, že zkontrolujete stav v okně **datový šifrovací** Server.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-validation.png" alt-text="Ověřování Azure Database for MySQL":::

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---cli"></a>Vytvoření serveru Azure Database for MySQL s použitím šifrování infrastruktury – rozhraní příkazového řádku

Pomocí těchto kroků vytvořte Azure Database for MySQL server s dvojitým šifrováním infrastruktury z CLI:

Tento příklad vytvoří skupinu prostředků s názvem `myresourcegroup` v `westus` umístění.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
Následující příklad vytvoří server MySQL 5.7 v umístění USA – západ, `mydemoserver` ve vaší skupině prostředků `myresourcegroup` a s přihlašovacím jménem správce serveru `myadmin`. Toto je **pro obecné účely** server **Gen 4** s **2 virtuální jádra**. Tím se taky povolí dvojité šifrování infrastruktury pro vytvořený server. Nahraďte položku `<server_admin_password>` vlastní hodnotou.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7 --infrastructure-encryption <Enabled/Disabled>
```

## <a name="next-steps"></a>Další kroky

 Další informace o šifrování dat najdete v tématu [Azure Database for MySQL dvojité šifrování infrastruktury dat](concepts-Infrastructure-double-encryption.md).
