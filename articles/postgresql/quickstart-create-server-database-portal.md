---
title: 'Rychlý Start: vytvoření serveru-Azure Portal-Azure Database for PostgreSQL-Single server'
description: V této příručce pro rychlý Start vytvoříte a spravujete Azure Database for PostgreSQL Server pomocí Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/27/2020
ms.openlocfilehash: 6e43d00722dd86934c8f95e06a3b8b590b263d61
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91705156"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Rychlý Start: vytvoření serveru Azure Database for PostgreSQL pomocí Azure Portal

Azure Database for PostgreSQL je spravovaná služba, pomocí které spouštíte, spravujete a škálujete vysoce dostupné databáze PostgreSQL v cloudu. V tomto rychlém startu se dozvíte, jak vytvořit jeden Azure Database for PostgreSQL Server během asi pěti minut pomocí Azure Portal.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Otevřete webový prohlížeč a přejděte na [portál](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.

## <a name="create-an-azure-database-for-postgresql-server"></a>Vytvoření serveru Azure Database for PostgreSQL

Server Azure Database for PostgreSQL se vytvoří s konfigurovanou sadou [výpočetních prostředků a prostředků úložiště](./concepts-pricing-tiers.md). Server se vytvoří v rámci [skupiny prostředků Azure](../azure-resource-manager/management/overview.md).

Vytvoření serveru Azure Database for PostgreSQL:

1. V levém horním rohu portálu vyberte **vytvořit prostředek** .

2. Vyberte **databáze**  >  **Azure Database for PostgreSQL**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="Snímek obrazovky, který zobrazuje Azure Database for PostgreSQL v nabídce":::

3. Vyberte možnost nasazení na **jeden server** .

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/select-deployment-option.png" alt-text="Snímek obrazovky, který zobrazuje Azure Database for PostgreSQL v nabídce":::

4. Ve formuláři **základy** vyplňte následující informace.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="Snímek obrazovky, který zobrazuje Azure Database for PostgreSQL v nabídce":::

   Nastavení|Navrhovaná hodnota|Popis
   ---|---|---
   Předplatné|Název vašeho předplatného|Předplatné Azure, které chcete použít pro váš server. Pokud máte více předplatných, zvolte předplatné, ve kterém se vám prostředek účtuje.
   Skupina prostředků|*myresourcegroup*| Název nové skupiny prostředků nebo některé ze stávajících ve vašem předplatném.
   Název serveru |*mydemoserver*|Jedinečný název, který identifikuje váš server Azure Database for PostgreSQL. Název domény *Postgres.Database.Azure.com* je připojen k názvu serveru, který zadáte. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí obsahovat 3 až 63 znaků.
   Zdroj dat | **Žádný** | Pokud chcete vytvořit nový server úplně od začátku, vyberte **Žádný**. ( **Zálohování** vyberete, pokud jste vytvořili server z geografické zálohy existujícího serveru Azure Database for PostgreSQL.)
   Uživatelské jméno správce |*myadmin*| Váš vlastní přihlašovací účet, který budete používat pro připojení k serveru. Přihlašovací jméno správce nemůže být **azure_superuser**, **azure_pg_admin**, **admin**, **Administrator**, **root**, **Guest**ani **Public**. Nemůže začínat na **pg_**.
   Heslo |Vaše heslo| Nové heslo pro účet správce serveru. Musí obsahovat 8 až 128 znaků ze tří z následujících kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice (0 – 9) a jiné než alfanumerické znaky (například!, $, #,%).
   Umístění|Oblast nejbližší vašim uživatelům| Umístění co nejblíže vašim uživatelům.
   Verze|Nejnovější hlavní verze| Nejnovější hlavní verze PostgreSQL, pokud nemáte jiné specifické požadavky.
   Výpočty + úložiště | **Obecné účely**, **Gen 5**, **2 virtuální jádra**, **5 GB**, **7 dní**, **Geograficky redundantní** | Konfigurace výpočtů, úložiště a zálohování pro nový server. Vyberte **Konfigurovat Server**. V dalším kroku vyberte příslušnou cenovou úroveň. Další informace najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/postgresql/server/). Pokud chcete povolit zálohování serveru v geograficky redundantním úložišti, vyberte z **možností redundance zálohy**možnost **geograficky redundantní** . Vyberte **OK**.

   > [!NOTE]
   > Zvažte použití cenové úrovně Basic, pokud je pro vaše zatížení vhodné světlé výpočetní prostředky a vstupně-výstupní operace. Upozorňujeme, že servery vytvořené v cenové úrovni Basic se nemůžou později škálovat na Pro obecné účely nebo paměť optimalizované pro paměť. 
   
5. Vyberte možnost **zkontrolovat + vytvořit** a zkontrolujte výběr. Vyberte **Vytvořit**, aby se server zřídil. Tato operace může trvat několik minut.

6. Pokud chcete monitorovat proces nasazení, na panelu nástrojů vyberte ikonu **Oznámení** (zvonek). Po dokončení nasazení vyberte **Přejít k prostředku** a otevřete stránku **Přehled** serveru.

Vytvoří se prázdná databáze **Postgres**. Najdete v ní také databázi **azure_maintenance** , která se používá k oddělení procesů spravované služby od uživatelských akcí. Nemůžete získat přístup k databázi **azure_maintenance** .

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurace pravidla brány firewall na úrovni serveru
Ve výchozím nastavení není server, který vytvoříte, veřejně přístupný. Musíte udělit oprávnění k vaší IP adrese. Přejděte na prostředek serveru v Azure Portal a vyberte **zabezpečení připojení** z nabídky na levé straně pro prostředek serveru. Pokud si nejste jistí, jak najít prostředek, přečtěte si téma [otevření prostředků](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="Snímek obrazovky, který zobrazuje Azure Database for PostgreSQL v nabídce":::
  
Vyberte **Přidat aktuální IP adresu klienta**a pak vyberte **Uložit**. Můžete přidat další IP adresy nebo zadat rozsah IP adres pro připojení k serveru z těchto IP adres. Další informace najdete v tématu [pravidla brány firewall v Azure Database for PostgreSQL](./concepts-firewall-rules.md).
   
> [!NOTE]
> Pokud se chcete vyhnout problémům s připojením, ověřte, jestli vaše síť povoluje odchozí přenosy přes port 5432. Azure Database for PostgreSQL používá tento port.  

## <a name="connect-to-azure-database-for-postgresql-server-by-using-psql"></a>Připojení k Azure Database for PostgreSQL serveru pomocí psql

Můžete použít [psql](http://postgresguide.com/utilities/psql.html) nebo [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html), které jsou oblíbenými klienty PostgreSQL. V tomto rychlém startu se připojíme pomocí psql v [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) v rámci Azure Portal.

1. Poznamenejte si název serveru, přihlašovací jméno správce serveru, heslo a ID předplatného nově vytvořeného serveru z části **Přehled** vašeho serveru.

2. Otevřete Azure Cloud Shell na portálu tak, že vyberete ikonu v levé horní části.

   > [!NOTE]
   > Pokud Cloud Shell poprvé otevíráte, zobrazí se výzva k vytvoření skupiny prostředků a účtu úložiště. Jedná se o jednorázový krok, který se automaticky připojí pro všechny relace. 

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="Snímek obrazovky, který zobrazuje Azure Database for PostgreSQL v nabídce":::

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

## <a name="clean-up-resources"></a>Vyčištění prostředků
Úspěšně jste vytvořili Azure Database for PostgreSQL Server ve skupině prostředků. Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, můžete je odstranit odstraněním skupiny prostředků nebo PostgreSQL serveru. 

Odstranění skupiny prostředků:

1. V Azure Portal vyhledejte a vyberte **skupiny prostředků**. 
2. V seznamu Skupina prostředků vyberte název vaší skupiny prostředků.
3. Na stránce **Přehled** skupiny prostředků vyberte **Odstranit skupinu prostředků**.
4. V potvrzovacím dialogovém okně zadejte název vaší skupiny prostředků a pak vyberte **Odstranit**.

Pokud chcete server odstranit, vyberte na stránce **Přehled** na vašem serveru tlačítko **Odstranit** :

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="Snímek obrazovky, který zobrazuje Azure Database for PostgreSQL v nabídce":::

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Migrace databáze pomocí exportu a importu](./howto-migrate-using-export-and-import.md)
