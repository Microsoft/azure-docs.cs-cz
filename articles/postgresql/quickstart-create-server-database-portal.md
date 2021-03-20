---
title: 'Rychlý Start: vytvoření serveru-Azure Portal-Azure Database for PostgreSQL-Single server'
description: V této příručce pro rychlý Start vytvoříte a spravujete Azure Database for PostgreSQL Server pomocí Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 000ab3e3911c65554622a48d34abda79d60411df
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96492382"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Rychlý Start: vytvoření serveru Azure Database for PostgreSQL pomocí Azure Portal

Azure Database for PostgreSQL je spravovaná služba, pomocí které spouštíte, spravujete a škálujete vysoce dostupné databáze PostgreSQL v cloudu. V tomto rychlém startu se dozvíte, jak vytvořit jeden Azure Database for PostgreSQL Server a jak se k němu připojit.

## <a name="prerequisites"></a>Předpoklady
Vyžaduje se předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-azure-database-for-postgresql-server"></a>Vytvoření serveru Azure Database for PostgreSQL
Pokud chcete vytvořit databázi Azure Database for PostgreSQL jednoho serveru, přejdete na [Azure Portal](https://portal.azure.com/) . Vyhledejte a vyberte *Azure Database for PostgreSQL servery*.

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/search-postgres.png" alt-text="Najít Azure Database for PostgreSQL.":::

1. Vyberte **Přidat**.

2. Na stránce vytvořit Azure Database for PostgreSQL vyberte  **jeden server**.

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/select-single-server.png" alt-text="Vybrat jeden server":::

3. Nyní do formuláře **základy** zadejte následující informace.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="Snímek obrazovky, který zobrazuje kartu základy pro vytvoření jednoho serveru.":::

   |Nastavení|Navrhovaná hodnota|Popis|
   |:---|:---|:---|
   |Předplatné|název vašeho předplatného|Vyberte požadované předplatné Azure.|
   |Skupina prostředků|*myresourcegroup*| Nová nebo existující skupina prostředků z vašeho předplatného.|
   |Název serveru |*mydemoserver*|Jedinečný název, který identifikuje váš server Azure Database for PostgreSQL. Název domény *Postgres.Database.Azure.com* je připojen k názvu serveru, který zadáte. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí obsahovat 3 až 63 znaků.|
   |Zdroj dat | Žádné | Pokud chcete vytvořit nový server úplně od začátku, vyberte **Žádný**. **Zálohování** vyberte jenom v případě, že obnovujete z geografické zálohy existujícího serveru.|
   |Uživatelské jméno správce |*myadmin*| Zadejte uživatelské jméno správce serveru. Nemůže začínat **pg_** a tyto hodnoty nejsou povolené: **azure_superuser**, **azure_pg_admin**, **admin**, **Administrator**, **root**, **Guest** ani **Public**.|
   |Heslo |vaše heslo| Nové heslo pro uživatele správce serveru. Musí obsahovat 8 až 128 znaků ze tří z následujících kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice (0 – 9) a jiné než alfanumerické znaky (například!, $, #,%).|
   |Umístění|Vaše požadované umístění| V rozevíracím seznamu vyberte umístění.|
   |Verze|Nejnovější hlavní verze| Nejnovější hlavní verze PostgreSQL, pokud nemáte jiné specifické požadavky.|
   |Výpočty + úložiště | *použít výchozí*| Výchozí cenová úroveň je **pro obecné účely**  se **4 virtuální jádra** a **100 GB** úložiště. Uchovávání záloh je nastaveno na **7 dní** s možností **geograficky redundantního** zálohování.<br/>Seznamte se s [cenami](https://azure.microsoft.com/pricing/details/postgresql/server/) a v případě potřeby aktualizujte výchozí hodnoty.|


   > [!NOTE]
   > Zvažte použití cenové úrovně Basic, pokud je pro vaše zatížení vhodné světlé výpočetní prostředky a vstupně-výstupní operace. Upozorňujeme, že servery vytvořené v cenové úrovni Basic se nemůžou později škálovat na Pro obecné účely nebo paměť optimalizované pro paměť.

5. Vyberte možnost **zkontrolovat + vytvořit** a zkontrolujte výběr. Vyberte **Vytvořit**, aby se server zřídil. Tato operace může trvat několik minut.
    > [!NOTE]
    > Vytvoří se prázdná databáze **Postgres**. Najdete v ní také databázi **azure_maintenance** , která se používá k oddělení procesů spravované služby od uživatelských akcí. Nemůžete získat přístup k databázi **azure_maintenance** .

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/deployment-success.png" alt-text="nasazení bylo úspěšné.":::

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/postgres-doc-feedback)

## <a name="configure-a-firewall-rule"></a>Konfigurace pravidla brány firewall
Ve výchozím nastavení není server, který vytvoříte, veřejně přístupný. Musíte udělit oprávnění k vaší IP adrese. Přejděte na prostředek serveru v Azure Portal a vyberte **zabezpečení připojení** z nabídky na levé straně pro prostředek serveru. Pokud si nejste jistí, jak najít prostředek, přečtěte si téma [otevření prostředků](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="Snímek obrazovky, který zobrazuje pravidla brány firewall pro zabezpečení připojení.":::

Vyberte **Přidat aktuální IP adresu klienta** a pak vyberte **Uložit**. Můžete přidat další IP adresy nebo zadat rozsah IP adres pro připojení k serveru z těchto IP adres. Další informace najdete v tématu [pravidla brány firewall v Azure Database for PostgreSQL](./concepts-firewall-rules.md).

> [!NOTE]
> Pokud se chcete vyhnout problémům s připojením, ověřte, jestli vaše síť povoluje odchozí přenosy přes port 5432. Azure Database for PostgreSQL používá tento port.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/postgres-doc-feedback)

## <a name="connect-to-the-server-with-psql"></a>Připojení k serveru pomocí psql

Můžete použít [psql](http://postgresguide.com/utilities/psql.html) nebo [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html), které jsou oblíbenými klienty PostgreSQL. V tomto rychlém startu se připojíme pomocí psql v [Azure Cloud Shell](../cloud-shell/overview.md) v rámci Azure Portal.

1. Poznamenejte si název serveru, přihlašovací jméno správce serveru, heslo a ID předplatného nově vytvořeného serveru z části **Přehled** vašeho serveru.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/overview-new.png" alt-text="získat informace o připojení.":::


2. Otevřete Azure Cloud Shell na portálu tak, že vyberete ikonu v levé horní části.

   > [!NOTE]
   > Pokud Cloud Shell poprvé otevíráte, zobrazí se výzva k vytvoření skupiny prostředků a účtu úložiště. Jedná se o jednorázový krok, který se automaticky připojí pro všechny relace.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="Snímek obrazovky, který zobrazuje informace o serveru a ikonu pro otevření Azure Cloud Shell.":::

3. Spusťte následující příkaz v terminálu Azure Cloud Shell. Nahraďte hodnoty skutečným názvem serveru a přihlašovacím jménem uživatele správce. Použijte prázdnou databázi **Postgres** s uživatelem s oprávněními správce v tomto formátu: `<admin-username>@<servername>` .

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   Tady je postup, jak vypadá v Cloud Shell terminálu:

   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. Ve stejném Azure Cloud Shell terminálu vytvořte databázi s názvem **Host**.

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Přepněte připojení na nově vytvořenou databázi **hostů** .

   ```bash
   \c guest
   ```
6. Zadejte `\q` a potom stisknutím klávesy ENTER zavřete psql.

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Úspěšně jste vytvořili Azure Database for PostgreSQL Server ve skupině prostředků. Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, můžete je odstranit odstraněním skupiny prostředků nebo PostgreSQL serveru.

Odstranění skupiny prostředků:

1. V Azure Portal vyhledejte a vyberte **skupiny prostředků**.
2. V seznamu Skupina prostředků vyberte název vaší skupiny prostředků.
3. Na stránce **Přehled** skupiny prostředků vyberte **Odstranit skupinu prostředků**.
4. V potvrzovacím dialogovém okně zadejte název vaší skupiny prostředků a pak vyberte **Odstranit**.

Pokud chcete server odstranit, vyberte na stránce **Přehled** na vašem serveru tlačítko **Odstranit** :

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="Snímek obrazovky zobrazující tlačítko pro odstranění serveru":::

[Máte problémy? Dejte nám prosím jistotu.](https://aka.ms/postgres-doc-feedback)

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Migrace databáze pomocí exportu a importu](./howto-migrate-using-export-and-import.md) <br/>

> [!div class="nextstepaction"]
> [Návrh databáze](./tutorial-design-database-using-azure-portal.md#create-tables-in-the-database)

[Nemůžete najít, co hledáte? Dejte nám prosím jistotu.](https://aka.ms/postgres-doc-feedback)