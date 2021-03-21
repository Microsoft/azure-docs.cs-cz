---
title: 'Rychlý Start: Connect-MySQL Workbench-Azure Database for MySQL'
description: V tomto rychlém startu najdete postup pro použití aplikace MySQL Workbench k připojení a dotazování dat ze služby Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 5e27cfec0a3f0a58c1e94a822e0c831f4efa1b32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94535533"
---
# <a name="quickstart-use-mysql-workbench-to-connect-and-query-data-in-azure-database-for-mysql"></a>Rychlý Start: použití aplikace MySQL Workbench k připojení a dotazování dat v Azure Database for MySQL

Tento rychlý start ukazuje, jak se připojit ke službě Azure Database for MySQL pomocí aplikace MySQL Workbench.

## <a name="prerequisites"></a>Předpoklady

Tento rychlý start jako výchozí bod využívá prostředky vytvořené v některém z těchto průvodců:
- [Vytvoření serveru Azure Database for MySQL pomocí webu Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Vytvoření serveru Azure Database for MySQL pomocí Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT] 
> Ujistěte se, že IP adresa, ze které se připojujete, přidala pravidla brány firewall serveru pomocí [Azure Portal](./howto-manage-firewall-using-portal.md) nebo rozhraní příkazového [řádku Azure CLI](./howto-manage-firewall-using-cli.md) .

## <a name="install-mysql-workbench"></a>Instalace aplikace MySQL Workbench
Stáhněte aplikaci MySQL Workbench z [webu MySQL](https://dev.mysql.com/downloads/workbench/) a nainstalujte ji na svém počítači.

## <a name="get-connection-information"></a>Získání informací o připojení
Získejte informace o připojení potřebné pro připojení ke službě Azure Database for MySQL. Potřebujete plně kvalifikovaný název serveru a přihlašovací údaje.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).

2. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyhledejte vytvořený server (například **mydemoserver**).

3. Klikněte na název serveru.

4. Na panelu **Přehled** serveru si poznamenejte **Název serveru** a **Přihlašovací jméno správce serveru**. Pokud zapomenete své heslo, můžete ho na tomto panelu také resetovat.
 :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Název serveru Azure Database for MySQL":::

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Připojení k serveru pomocí aplikace MySQL Workbench 
Připojení k serveru Azure MySQL pomocí nástroje s grafickým uživatelským rozhraním MySQL Workbench:

1.    Spusťte na svém počítači aplikaci MySQL Workbench. 

2.    V dialogovém okně pro **nastavení nového připojení** zadejte na kartě **Parametry** následující informace:

:::image type="content" source="./media/connect-workbench/2-setup-new-connection.png" alt-text="nastavení nového připojení":::

| **Nastavení** | **Navrhovaná hodnota** | **Popis pole** |
|---|---|---|
|    Název připojení | Ukázkové připojení | Zadejte popisek pro toto připojení. |
| Způsob připojení | Standardní (TCP/IP) | Standard (TCP/IP) je dostačující. |
| Název hostitele | *název serveru* | Zadejte hodnotu názvu serveru, kterou jste použili dříve při vytváření služby Azure Database for MySQL. Ukázkový server v příkladu je mydemoserver.mysql.database.azure.com. Použijte plně kvalifikovaný název domény (\*.mysql.database.azure.com), jak je znázorněno v příkladu. Pokud si název vašeho serveru nepamatujete, získejte informace o připojení pomocí postupu v předchozí části.  |
| Port | 3306 | Při připojování ke službě Azure Database for MySQL vždy používejte port 3306. |
| Uživatelské jméno |  *přihlašovací jméno správce serveru* | Zadejte přihlašovací uživatelské jméno správce serveru, které jste zadali dříve při vytváření služby Azure Database for MySQL. Uživatelské jméno v našem příkladu je myadmin@mydemoserver. Pokud si uživatelské jméno nepamatujete, získejte informace o připojení pomocí postupu v předchozí části. Formát je *UserName \@ servername*.
| Heslo | vaše heslo | Kliknutím na tlačítko **Uložit v trezoru...** heslo uložte. |

3.   Pokud chcete otestovat, jestli jsou všechny parametry správně nakonfigurované, klikněte na **Test připojení**. 

4.   Kliknutím na tlačítko **OK** uložte připojení. 

5.   Ve výpisu **připojení k MySQL** klikněte na dlaždici odpovídající vašemu serveru a počkejte na navázání připojení.

        Otevře se nová karta SQL s prázdným editorem, do kterého můžete zadávat dotazy.
    
        > [!NOTE]
        > Ve výchozím nastavení se na serveru Azure Database for MySQL vyžaduje a vynucuje zabezpečení připojení protokolem SSL. Přestože aplikace MySQL Workbench obvykle pro připojení k serveru nevyžaduje žádnou další konfiguraci s certifikáty SSL, doporučujeme vytvořit vazbu certifikace SSL CA s aplikací MySQL Workbench. Další informace o stažení certifikace a vytvoření její vazby najdete v tématu [Konfigurace připojení SSL v aplikaci pro zabezpečené připojení k Azure Database for MySQL](./howto-configure-ssl.md).  Pokud potřebujete SSL zakázat, přejděte na web Azure Portal, klikněte na stránku Zabezpečení připojení a deaktivujte přepínací tlačítko Vynutit připojení SSL.

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
    
    :::image type="content" source="media/connect-workbench/3-workbench-sql-tab.png" alt-text="Karta SQL aplikace MySQL Workbench pro spuštění vzorového kódu SQL":::

2. Pokud chcete spustit ukázkový kód SQL, klikněte na ikonu blesku na panelu nástrojů karty **Soubor SQL**.
3. Všimněte si výsledků na třech kartách v části **Mřížka výsledků** uprostřed stránky. 
4. Všimněte si seznamu **Výstup** v dolní části stránky. V něm se zobrazí stavy jednotlivých příkazů. 

Nyní jste se připojili ke službě Azure Database for MySQL pomocí aplikace MySQL Workbench a s využitím jazyka SQL jste dotázali data.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit všechny prostředky používané v rámci tohoto rychlého startu, odstraňte skupinu prostředků pomocí následujícího příkazu:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Migrace vaší databáze pomocí exportu a importu](./concepts-migrate-import-export.md)
