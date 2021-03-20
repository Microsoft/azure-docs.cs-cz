---
title: 'Rychlý Start: vytvoření serveru Azure Portal-Azure Database for PostgreSQL-flexibilní Server'
description: Příručka rychlý Start pro vytvoření a správu Azure Database for PostgreSQL flexibilního serveru pomocí uživatelského rozhraní Azure Portal
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 978fcb8c8cf9d2ffd929da5b76d7fd2ff109f420
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92535854"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---flexible-server-in-the-azure-portal"></a>Rychlý Start: vytvoření Azure Database for PostgreSQL-flexibilního serveru v Azure Portal

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Azure Database for PostgreSQL je spravovaná služba, pomocí které spouštíte, spravujete a škálujete vysoce dostupné databáze PostgreSQL v cloudu. V tomto rychlém startu se dozvíte, jak vytvořit Azure Database for PostgreSQL flexibilní Server během asi pěti minut pomocí Azure Portal.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Otevřete webový prohlížeč a přejděte na [portál](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.

## <a name="create-an-azure-database-for-postgresql-server"></a>Vytvoření serveru Azure Database for PostgreSQL

Server Azure Database for PostgreSQL se vytvoří s konfigurovanou sadou [výpočetních prostředků a prostředků úložiště](./concepts-compute-storage.md). Server se vytvoří v rámci [skupiny prostředků Azure](../../azure-resource-manager/management/overview.md).

Server Azure Database for PostgreSQL vytvoříte pomocí tohoto postupu:

1. V levém horním rohu portálu vyberte **vytvořit prostředek** (+).

2. Vyberte **databáze**  >  **Azure Database for PostgreSQL**.

    :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="Azure Database for PostgreSQL v nabídce":::

3. Vyberte možnost **flexibilního nasazení serveru** .

   :::image type="content" source="./media/quickstart-create-database-portal/2-select-deployment-option.png" alt-text="Výběr možnosti Azure Database for PostgreSQL – flexibilní nasazení serveru":::

4. Ve formuláři **základy** vyplňte následující informace:

    :::image type="content" source="./media/quickstart-create-database-portal/3-create-basics.png" alt-text="Vytvoření serveru":::

    Nastavení|Navrhovaná hodnota|Popis
    ---|---|---
    Předplatné|Název vašeho předplatného|Předplatné Azure, které chcete použít pro váš server. Pokud máte více předplatných, vyberte předplatné, ve kterém se má prostředek účtovat.
    Skupina prostředků|*myresourcegroup*| Název nové skupiny prostředků nebo některé ze stávajících ve vašem předplatném.
    Název serveru |*mydemoserver*|Jedinečný název, který identifikuje váš server Azure Database for PostgreSQL. K zadanému názvu serveru se připojí název domény *postgres.database.azure.com*. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí se skládat z 3 až 63 znaků.
    Uživatelské jméno správce |*myadmin*| Váš vlastní přihlašovací účet, který budete používat pro připojení k serveru. Přihlašovací jméno správce nemůže být **azure_superuser**, **azure_pg_admin**, **admin**, **Administrator**, **root**, **Guest** ani **Public**. Nemůže začínat na **pg_**.
    Heslo |Vaše heslo| Nové heslo pro účet správce serveru. Musí mít 8 až 128 znaků. Heslo musí obsahovat znaky ze tří z těchto kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice (0–9) a jiné než alfanumerické znaky (!, $, #, % apod.).
    Umístění|Oblast nejbližší vašim uživatelům| Umístění co nejblíže vašim uživatelům.
    Verze|Nejnovější hlavní verze| Nejnovější hlavní verze PostgreSQL, pokud nemáte jiné specifické požadavky.
    Výpočty + úložiště | **Pro obecné účely**, **4 virtuální jádra**, **512 GB**, **7 dní** | Konfigurace výpočtů, úložiště a zálohování pro nový server. Vyberte **Konfigurovat Server**. Výchozími hodnotami pro **výpočetní vrstvu**, **Vcore**, **úložiště** a **dobu uchování zálohy** jsou *pro obecné účely*, *4 virtuální jádra*, *512 GB* a *7 dní* . Tyto posuvníky můžete ponechat beze změny nebo je upravit. Vyberte **OK** a uložte tento výběr cenové úrovně. Další snímek zachycuje tyto výběry.

    :::image type="content" source="./media/quickstart-create-database-portal/4-pricing-tier.png" alt-text="Podokno cenová úroveň":::
    
5. Konfigurace možností sítě

    Na kartě síť můžete zvolit, jak je server dostupný. Azure Database for PostgreSQL vytváří bránu firewall na úrovni serveru. Ta brání externím aplikacím a nástrojům v připojení k serveru a kterékoli databázi na serveru, pokud nevytvoříte pravidlo k otevření brány firewall pro konkrétní IP adresy. Doporučujeme, aby byl server veřejně přístupný:

    :::image type="content" source="./media/quickstart-create-database-portal/5-networking.png" alt-text="Podokno síť":::

    A pak ho omezte na vlastní IP adresu klienta:

    :::image type="content" source="./media/quickstart-create-database-portal/6-add-client-ip.png" alt-text="Vyberte Přidat aktuální IP adresu klienta.":::

6. Vyberte možnost **zkontrolovat + vytvořit** a zkontrolujte výběr. Vyberte **Vytvořit**, aby se server zřídil. Tato operace může trvat několik minut.

7. Pokud chcete monitorovat proces nasazení, na panelu nástrojů vyberte ikonu **Oznámení** (zvonek). Po dokončení nasazení můžete vybrat **Připnout na řídicí panel** a vytvořit dlaždici pro tento server na řídicím panelu webu Azure Portal, která slouží jako zástupce stránky **Přehled** serveru. Výběr **Přejít k prostředku** otevře stránku **Přehled** serveru.

    :::image type="content" source="./media/quickstart-create-database-portal/7-notifications.png" alt-text="Podokno oznámení":::

   Ve výchozím nastavení se v rámci vašeho serveru vytvoří databáze **postgres**. Databáze [postgres](https://www.postgresql.org/docs/12/static/app-initdb.html) je výchozí databáze určená pro uživatele, nástroje a aplikace třetích stran. (Další výchozí databází je **azure_maintenance**. Její funkcí je oddělit procesy spravovaných služeb od akcí uživatelů. K této databázi nemáte přístup.)

    > [!NOTE]
    > Připojení k serveru Azure Database for PostgreSQL komunikují přes port 5432. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 5432 bránou firewall vaší sítě povolený. V takovém případě se k serveru nemůžete připojit, dokud vaše IT oddělení neotevře port 5432.
    >

## <a name="get-the-connection-information"></a>Získání informací o připojení

Při vytváření vašeho serveru Azure Database for PostgreSQL se vytvoří i výchozí databáze **postgres**. Pro připojení ke svému databázovému serveru potřebujete úplný název serveru a přihlašovací údaje správce. Pravděpodobně jste si tyto hodnoty poznamenali v dřívější části tohoto článku Rychlý start. Pokud ne, název serveru a přihlašovací informace můžete snadno vyhledat na stránce **Přehled** serveru na portálu.

Otevřete stránku **Přehled** vašeho serveru. Poznamenejte si **Název serveru** a **Přihlašovací jméno správce serveru**. Přejeďte kurzorem přes jednotlivá pole a vpravo od textu se zobrazí symbol kopírování. Podle potřeby hodnoty zkopírujte výběrem symbolu kopírování.

 :::image type="content" source="./media/quickstart-create-database-portal/8-server-name.png" alt-text="Stránka Přehled serveru":::

## <a name="connect-to-the-postgresql-database-using-psql"></a>Připojení k databázi PostgreSQL pomocí nástroje psql

Existuje řada aplikací, které můžete použít pro připojení k vašemu serveru Azure Database for PostgreSQL. Pokud má klientský počítač nainstalovaný systém PostgreSQL, můžete se připojit k serveru Azure PostgreSQL pomocí místní instance nástroje [psql](https://www.postgresql.org/docs/current/static/app-psql.html). Teď se pomocí nástroje příkazového řádku psql připojíme k serveru Azure PostgreSQL.

1. Spusťte následující příkaz psql pro připojení k serveru Azure Database for PostgreSQL:

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Třeba tento příkaz provádí pomocí přihlašovacích údajů pro přístup připojení k výchozí databázi s názvem **postgres** na serveru PostgreSQL **mydemoserver.postgres.database.azure.com**. Zadejte heslo `<server_admin_password>`, které jste zvolili při zobrazení výzvy k zadání hesla.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Jakmile budete připojeni, nástroj psql zobrazí příkazový řádek postgres, kde můžete zadávat příkazy jazyka SQL. Ve výstupu počátečního připojení se může zobrazit upozornění, protože nástroj psql, který používáte, může mít jinou verzi než na serveru Azure Database for PostgreSQL.

   Příklad výstupu nástroje psql:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Pokud brána firewall není nakonfigurovaná k povolení IP adresy vašeho klienta, dojde k následující chybě:
   >
   > "psql: závažná chyba: není k dispozici žádná pg_hba. conf pro hostitele `<IP address>` , uživatele" myadmin ", databázi" Postgres ", protokol SSL je povinný: připojení SSL je povinné. Zadejte možnosti SSL a zkuste to znovu.
   >
   > Ověřte, že je v kroku pravidla brány firewall povolená IP adresa klienta.

2. Vytvořte prázdnou databázi s názvem mypgsqldb zadáním následujícího příkazu na příkazovém řádku:

    ```bash
    CREATE DATABASE mypgsqldb;
    ```

3. Na příkazovém řádku spusťte následující příkaz pro přepnutí připojení na nově vytvořenou databázi **mypgsqldb**:

    ```bash
    \c mypgsqldb
    ```

4. Zadejte `\q` a pak stisknutím klávesy Enter ukončete nástroj psql.

Připojili jste se k serveru Azure Database for PostgreSQL pomocí nástroje psql a vytvořili jste prázdnou uživatelskou databázi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky, které jste vytvořili v rámci tohoto rychlého startu, můžete vyčistit jedním ze dvou způsobů. Můžete odstranit skupinu prostředků Azure, což zahrnuje odstranění všech prostředků v této skupině prostředků. Pokud chcete ostatní prostředky zachovat beze změny, odstraňte pouze prostředek serveru.

> [!TIP]
> Další rychlé starty v této kolekci jsou postavené na tomto rychlém startu. Pokud chcete pokračovat v práci s dalšími rychlými starty, neodstraňujte prostředky, které jste vytvořili v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí těchto kroků odstraňte prostředky vytvořené tímto rychlým startem na portálu.

Odstranění celé skupiny prostředků, včetně nově vytvořeného serveru:

1. Vyhledejte skupinu prostředků na portálu. V nabídce na levé straně vyberte **Skupiny prostředků**. Potom vyberte název vaší skupiny prostředků, jako je **myresourcegroup** v tomto příkladu.

2. Na stránce vaší skupiny prostředků vyberte **Odstranit**. Do textového pole zadejte název vaší skupiny prostředků, jako je například **myresourcegroup**, a potvrďte tak odstranění. Vyberte **Odstranit**.

Odstranění pouze nově vytvořeného serveru:

1. Vyhledejte váš server na portálu, pokud ho ještě nemáte otevřený. V nabídce na levé straně vyberte **Všechny prostředky**. Pak vyhledejte server, který jste vytvořili.

2. Na stránce **Přehled** vyberte **Odstranit**.

    :::image type="content" source="./media/quickstart-create-database-portal/9-delete.png" alt-text="Tlačítko Odstranit":::

3. Potvrďte název serveru, který chcete odstranit, a zobrazte jeho databáze, které tím ovlivníte. Do textového pole zadejte název vašeho serveru, jako je například **mydemoserver**. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Nasazení aplikace Django pomocí App Service a PostgreSQL](tutorial-django-app-service-postgres.md)