---
title: 'Rychlý Start: Connect-MySQL Workbench-Azure Database for MySQL-flexibilní Server'
description: V tomto rychlém startu najdete postup použití aplikace MySQL Workbench pro připojení a dotazování dat z Azure Database for MySQL-flexibilního serveru.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: d604ca8c8979ec98b990f8002ce29d0df92ac2af
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947300"
---
# <a name="quickstart-use-mysql-workbench-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server-preview"></a>Rychlý Start: použití aplikace MySQL Workbench pro připojení a dotazování dat v Azure Database for MySQL-flexibilním serveru (Preview)


> [!IMPORTANT] 
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Tento rychlý Start ukazuje, jak se připojit k Azure Database for MySQL flexibilnímu serveru pomocí aplikace MySQL Workbench.

## <a name="prerequisites"></a>Předpoklady

Tento rychlý start jako výchozí bod využívá prostředky vytvořené v některém z těchto průvodců:

- [Vytvoření Azure Database for MySQL flexibilního serveru pomocí Azure Portal](./quickstart-create-server-portal.md)
- [Vytvoření Azure Database for MySQL flexibilního serveru pomocí Azure CLI](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>Příprava pracovní stanice klienta
- Pokud jste vytvořili flexibilní Server s *privátním přístupem (Integration VNET)*, budete se muset připojit k serveru z prostředku ve stejné virtuální síti jako váš server. Můžete vytvořit virtuální počítač a přidat ho do virtuální sítě vytvořené pomocí flexibilního serveru. Přečtěte si téma [Vytvoření a správa Azure Database for MySQL flexibilní serverové virtuální sítě pomocí Azure CLI](./how-to-manage-virtual-network-cli.md).
- Pokud jste vytvořili flexibilní Server s *veřejným přístupem (povolenými IP adresami)*, můžete přidat místní IP adresu do seznamu pravidel brány firewall na serveru. Přečtěte si téma [Vytvoření a správa Azure Database for MySQL flexibilních pravidel brány firewall serveru pomocí Azure CLI](./how-to-manage-firewall-cli.md).

- Stáhněte aplikaci MySQL Workbench z [webu MySQL](https://dev.mysql.com/downloads/workbench/) a nainstalujte ji na svém počítači.

## <a name="get-connection-information"></a>Získání informací o připojení

Získejte informace o připojení potřebné pro připojení k flexibilnímu serveru. Potřebujete plně kvalifikovaný název serveru a přihlašovací údaje pro přihlášení.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo v Azure Portal vyberte **všechny prostředky**a potom vyhledejte server, který jste vytvořili (například **mydemoserver**).
3. Vyberte název serveru.
4. Na panelu **Přehled** serveru si poznamenejte **Název serveru** a **Přihlašovací jméno správce serveru**. Pokud zapomenete své heslo, můžete ho na tomto panelu také resetovat.
<!--- :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connect-to-the-server-using-mysql-workbench"></a>Připojení k serveru pomocí aplikace MySQL Workbench

Připojení k Azure Database for MySQL flexibilnímu serveru pomocí MySQL Workbench:

1. Spusťte na svém počítači aplikaci MySQL Workbench.

2. V dialogovém okně pro **nastavení nového připojení** zadejte na kartě **Parametry** následující informace:

    :::image type="content" source="./media/connect-workbench/2-setup-new-connection.png" alt-text="Azure Database for MySQL Flexible Server name":::

    | **Parametry** | **Navrhovaná hodnota** | **Popis pole** |
    |---|---|---|
    |    Název připojení | Ukázkové připojení | Zadejte popisek pro toto připojení. |
    | Způsob připojení | Standardní (TCP/IP) | Standard (TCP/IP) je dostačující. |
    | Název hostitele | *název serveru* | Zadejte hodnotu názvu serveru, kterou jste použili dříve při vytváření služby Azure Database for MySQL. Ukázkový server v příkladu je mydemoserver.mysql.database.azure.com. Použijte plně kvalifikovaný název domény (\*.mysql.database.azure.com), jak je znázorněno v příkladu. Pokud si název vašeho serveru nepamatujete, získejte informace o připojení pomocí postupu v předchozí části.  |
    | Port | 3306 | Při připojování ke službě Azure Database for MySQL vždy používejte port 3306. |
    | Uživatelské jméno |  *přihlašovací jméno správce serveru* | Zadejte přihlašovací uživatelské jméno správce serveru, které jste zadali dříve při vytváření služby Azure Database for MySQL. Naše ukázkové uživatelské jméno je myadmin. Pokud si uživatelské jméno nepamatujete, získejte informace o připojení pomocí postupu v předchozí části.
    | Heslo | vaše heslo | Kliknutím na tlačítko **Uložit v trezoru...** heslo uložte. |

3. Pokud chcete otestovat, jestli jsou všechny parametry správně nakonfigurované, klikněte na **Test připojení**.

4. Kliknutím na tlačítko **OK** uložte připojení.

5. Ve výpisu **připojení k MySQL** klikněte na dlaždici odpovídající vašemu serveru a počkejte na navázání připojení.

    Otevře se nová karta SQL s prázdným editorem, do kterého můžete zadávat dotazy.

> [!NOTE]
> Pro Azure Database for MySQL flexibilní Server je vyžadováno šifrované připojení pomocí protokolu TLS 1,2. I když se pro MySQL Workbench pro připojení k vašemu serveru nevyžaduje žádná další konfigurace s certifikáty TLS/SSL, doporučujeme, abyste provedli vazbu certifikátu TLS/SSL s MySQL Workbench. Další informace najdete v tématu [připojení pomocí protokolu TLS/SSL](./how-to-connect-tls-ssl.md) .

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>Vytvoření tabulky, vložení dat, načtení dat, aktualizace dat a odstranění dat

1. Pro ilustraci nějakých ukázkových dat zkopírujte vzorový kód SQL a vložte ho do prázdné karty SQL.

    Tento kód vytvoří prázdnou databázi quickstartdb a pak vytvoří ukázkovou tabulku inventory. Vloží několik řádků a potom řádky načte. Změní data pomocí příkazu update a znovu načte řádky. Nakonec odstraní řádek a znovu načte řádky.

    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;

    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);

    -- Read
    SELECT * FROM inventory;

    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;

    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    Snímek obrazovky ukazuje příklad kódu SQL v aplikaci MySQL Workbench a výstup po jeho spuštění.

    :::image type="content" source="./media/connect-workbench/3-workbench-sql-tab.png" alt-text="Azure Database for MySQL Flexible Server name":::

2. Pokud chcete spustit ukázkový kód SQL, klikněte na ikonu blesku na panelu nástrojů karty **Soubor SQL**.
3. Všimněte si výsledků na třech kartách v části **Mřížka výsledků** uprostřed stránky.
4. Všimněte si seznamu **Výstup** v dolní části stránky. V něm se zobrazí stavy jednotlivých příkazů.

Nyní jste se připojili k Azure Database for MySQL flexibilnímu serveru pomocí MySQL Workbench a k datům jste dotazováni pomocí jazyka SQL.

## <a name="next-steps"></a>Další kroky
- [Šifrované připojení pomocí TLS 1,2 (Transport Layer Security) v Azure Database for MySQL-flexibilním serveru](./how-to-connect-tls-ssl.md).
- Přečtěte si další informace o [sítích v Azure Database for MySQL flexibilním serveru](./concepts-networking.md).
- [Vytvářejte a spravujte Azure Database for MySQL flexibilní pravidla brány firewall serveru pomocí Azure Portal](./how-to-manage-firewall-portal.md).
- [Vytvářejte a spravujte Azure Database for MySQL flexibilní serverovou virtuální síť pomocí Azure Portal](./how-to-manage-virtual-network-portal.md).
