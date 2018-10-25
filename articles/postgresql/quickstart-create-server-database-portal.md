---
title: Rychlý start – Vytvoření serveru Azure Database for PostgreSQL pomocí webu Azure Portal
description: Úvodní příručka k vytvoření a správě serveru Azure Database for PostgreSQL pomocí uživatelského rozhraní webu Azure Portal.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 03/20/2018
ms.openlocfilehash: 4e14cde99aaf74b5058e4f9d55c386151036594e
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987796"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-in-the-azure-portal"></a>Rychlý start: Vytvoření serveru Azure Database for PostgreSQL na webu Azure Portal

Azure Database for PostgreSQL je spravovaná služba, pomocí které spouštíte, spravujete a škálujete vysoce dostupné databáze PostgreSQL v cloudu. Tento rychlý start ukazuje, jak přibližně během pěti minut vytvořit server Azure Database for PostgreSQL pomocí webu Azure Portal.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Otevřete webový prohlížeč a přejděte na [portál](https://portal.azure.com/). Zadejte přihlašovací údaje pro přihlášení k portálu. Výchozím zobrazením je váš řídicí panel služby.

## <a name="create-an-azure-database-for-postgresql-server"></a>Vytvoření serveru Azure Database for PostgreSQL

Server Azure Database for PostgreSQL se vytvoří s konfigurovanou sadou [výpočetních prostředků a prostředků úložiště](./concepts-pricing-tiers.md). Server se vytvoří v rámci [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md).

Server Azure Database for PostgreSQL vytvoříte pomocí tohoto postupu:
1. Vyberte tlačítko **Vytvořit prostředek** (+) v levém horním rohu portálu.

2. Vyberte **Databáze** > **Azure Database for PostgreSQL**.

    ![Možnost Azure Database for PostgreSQL](./media/quickstart-create-database-portal/1-create-database.png)

3. Do formuláře podrobností o novém serveru zadejte následující informace:

    ![Vytvoření serveru](./media/quickstart-create-database-portal/3-create.png)

    Nastavení|Navrhovaná hodnota|Popis
    ---|---|---
    Název serveru |*mydemoserver*|Jedinečný název, který identifikuje váš server Azure Database for PostgreSQL. K zadanému názvu serveru se připojí název domény *postgres.database.azure.com*. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí se skládat z 3 až 63 znaků.
    Předplatné|Název vašeho předplatného|Předplatné Azure, které chcete použít pro váš server. Pokud máte více předplatných, zvolte předplatné, ve kterém se vám prostředek účtuje.
    Skupina prostředků|*myresourcegroup*| Název nové skupiny prostředků nebo některé ze stávajících ve vašem předplatném.
    Výběr zdroje | *Prázdné* | Vyberte *Prázdné* a vytvořte nový server od začátku. (Pokud vytváříte server z geografické zálohy existujícího serveru Azure Database for PostgreSQL, vyberte *Záloha*.)
    Přihlašovací jméno správce serveru |*myadmin*| Váš vlastní přihlašovací účet, který budete používat pro připojení k serveru. Přihlašovací jméno správce nemůže být **azure_superuser**, **azure_pg_admin**, **admin**, **administrator**, **root**, **guest** ani **public**. Nemůže začínat na **pg_**.
    Heslo |Vaše heslo| Nové heslo pro účet správce serveru. Musí mít 8 až 128 znaků. Heslo musí obsahovat znaky ze tří z těchto kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice (0–9) a jiné než alfanumerické znaky (!, $, #, % apod.).
    Umístění|Oblast nejbližší vašim uživatelům| Umístění co nejblíže vašim uživatelům.
    Verze|Nejnovější hlavní verze| Nejnovější hlavní verze PostgreSQL, pokud nemáte jiné specifické požadavky.
    Cenová úroveň | **Obecné účely**, **Gen 4**, **2 virtuální jádra**, **5 GB**, **7 dní**, **Geograficky redundantní** | Konfigurace výpočtů, úložiště a zálohování pro nový server. Vyberte **Cenová úroveň**. Potom vyberte kartu **Obecné účely**. *Gen 4*, *2 virtuální jádra*, *5 GB* a *7 dní* jsou výchozí hodnoty pro **Výpočetní generaci**, **Virtuální jádra**, **Úložiště** a **Období uchování zálohy**. Můžete ponechat tyto posuvníky tak, jak jsou. Pokud chcete povolit zálohování serveru v geograficky redundantním úložišti, vyberte v **Možnosti redundance zálohy** možnost **Geograficky redundantní**. Vyberte **OK** a uložte tento výběr cenové úrovně. Další snímek zachycuje tyto výběry.

    > [!IMPORTANT]
    > Zde zadané přihlašovací jméno a heslo správce serveru se vyžadují pro přihlášení k serveru a jeho databázím dále v tomto rychlém startu. Tyto informace si zapamatujte nebo poznamenejte pro pozdější použití.

    ![Podokno Cenová úroveň](./media/quickstart-create-database-portal/2-pricing-tier.png)

4. Vyberte **Vytvořit**, aby se server zřídil. Tato operace může trvat několik minut.

5. Pokud chcete monitorovat proces nasazení, na panelu nástrojů vyberte ikonu **Oznámení** (zvonek). Po dokončení nasazení můžete vybrat **Připnout na řídicí panel** a vytvořit dlaždici pro tento server na řídicím panelu webu Azure Portal, která slouží jako zástupce stránky **Přehled** serveru. Výběr **Přejít k prostředku** otevře stránku **Přehled** serveru.

    ![Podokno Oznámení](./media/quickstart-create-database-portal/3-notifications.png)
   
   Ve výchozím nastavení se v rámci vašeho serveru vytvoří databáze **postgres**. Databáze [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) je výchozí databáze určená pro uživatele, nástroje a aplikace třetích stran. (Další výchozí databází je **azure_maintenance**. Její funkcí je oddělit procesy spravovaných služeb od akcí uživatelů. K této databázi nemáte přístup.)

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurace pravidla brány firewall na úrovni serveru

Azure Database for PostgreSQL vytváří bránu firewall na úrovni serveru. Ta brání externím aplikacím a nástrojům v připojení k serveru a kterékoli databázi na serveru, pokud nevytvoříte pravidlo k otevření brány firewall pro konkrétní IP adresy. 

1. Po dokončení nasazení najděte váš server. V případě potřeby ho můžete vyhledat. Vyberte například **Všechny prostředky** z nabídky na levé straně. Zadejte název vašeho serveru, například ukázkový **mydemoserver**. Vyhledáte tak nově vytvořený server. Vyberte název vašeho serveru ze seznamu výsledků hledání. Otevře se stránka **Přehled** vašeho serveru a poskytne vám možnosti další konfigurace.
 
    ![Vyhledání názvu serveru](./media/quickstart-create-database-portal/4-locate.png)

2. Na stránce serveru vyberte **Zabezpečení připojení**.

3. Pod nadpisem **Pravidla brány firewall** vyberte prázdné textové pole ve sloupci **Název pravidla** a začněte vytvářet pravidlo brány firewall. 

   Vyplňte do textového pole název a začátek a konec rozsahu IP klientů, kteří budou přistupovat k vašemu serveru. Pokud se jedná jednu IP adresu, použijte stejnou hodnotu pro počáteční IP adresu a koncovou IP adresu.

   ![Nastavení pravidel brány firewall](./media/quickstart-create-database-portal/5-firewall-2.png)
     

4. Na horním panelu nástrojů na stránce **Zabezpečení připojení** vyberte **Uložit**. Než budete pokračovat, počkejte na zobrazení oznámení o úspěšném dokončení aktualizace zabezpečení připojení.

    > [!NOTE]
    > Připojení k serveru Azure Database for PostgreSQL komunikují přes port 5432. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 5432 bránou firewall vaší sítě povolený. V takovém případě se k serveru nemůžete připojit, dokud vaše IT oddělení neotevře port 5432.
    >

## <a name="get-the-connection-information"></a>Získání informací o připojení

Při vytváření vašeho serveru Azure Database for PostgreSQL se vytvoří i výchozí databáze **postgres**. Pro připojení ke svému databázovému serveru potřebujete úplný název serveru a přihlašovací údaje správce. Pravděpodobně jste si tyto hodnoty poznamenali v dřívější části tohoto článku Rychlý start. Pokud ne, název serveru a přihlašovací informace můžete snadno vyhledat na stránce **Přehled** serveru na portálu.

Otevřete stránku **Přehled** vašeho serveru. Poznamenejte si **Název serveru** a **Přihlašovací jméno správce serveru**. Přejeďte kurzorem přes jednotlivá pole a vpravo od textu se zobrazí symbol kopírování. Podle potřeby hodnoty zkopírujte výběrem symbolu kopírování.

 ![Stránka Přehled serveru](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-the-postgresql-database-by-using-psql-in-cloud-shell"></a>Připojení k databázi PostgreSQL pomocí psql ve službě Cloud Shell

Existuje řada aplikací, které můžete použít pro připojení k vašemu serveru Azure Database for PostgreSQL. Nejprve si ukážeme, jak se k serveru připojit pomocí nástroje příkazového řádku psql. Můžete použít webový prohlížeč a Azure Cloud Shell, jak je zde popsáno, aniž by bylo nutné instalovat další software. Pokud máte nástroj psql nainstalovaný místně na svém počítači, můžete se připojit i z něj.

1. V horním navigačním podokně vyberte symbol terminálu a otevřete Cloud Shell.

   ![Symbol terminálu Azure Cloud Shell](./media/quickstart-create-database-portal/7-cloud-console.png)

2. V prohlížeči se otevře Cloud Shell, kde můžete zadávat příkazy prostředí Bash.

   ![Příkazový řádek Bash služby Cloud Shell](./media/quickstart-create-database-portal/8-bash.png)

3. V příkazovém řádku služby Cloud Shell se zadáním příkazového řádku psql připojte k databázi na vašem serveru Azure Database for PostgreSQL.

    Pokud se chcete připojit k serveru Azure Database for PostgreSQL pomocí nástroje [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html), použijte následující formát:
    ```bash
    psql --host=<yourserver> --port=<port> --username=<server admin login> --dbname=<database name>
    ```

    Například následující příkaz se připojí k ukázkovému serveru:

    ```bash
    psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    ```

    Parametr psql |Hodnota|Popis
    ---|---|---
    --host | Název serveru | Hodnota názvu serveru, kterou jste použili dříve při vytváření serveru Azure Database for PostgreSQL. Ukázkový server v příkladu je **mydemoserver.postgres.database.azure.com**. Použijte plně kvalifikovaný název domény (**\*.postgres.database.azure.com), jak je znázorněno v příkladu. Pokud si název vašeho serveru nepamatujete, získejte informace o připojení pomocí postupu v předchozí části. 
    --port | 5432 | Port, který se použije pro připojení k serveru Azure Database for PostgreSQL. 
    --username | Přihlašovací jméno správce serveru |Přihlašovací uživatelské jméno správce serveru, které jste zadali dříve při vytváření serveru Azure Database for PostgreSQL. Pokud si své uživatelské jméno nepamatujete, získejte informace o připojení pomocí postupu v předchozí části. Formát je *username@servername*.
    --dbname | *postgres* | Výchozí systémem vygenerovaný název databáze vytvořený pro první připojení. Později vytvoříte vlastní databázi.

    Po spuštění příkazu psql s vlastními hodnotami parametrů se zobrazí výzva k zadání hesla správce serveru. Jedná se o stejné heslo, které jste zadali při vytváření serveru. 

    Parametr psql |Navrhovaná hodnota|Popis
    ---|---|---
    heslo | Vaše heslo správce | Na příkazovém řádku Bash se zadávané znaky hesla nezobrazí. Po zadání všech znaků stiskněte klávesu Enter, abyste se ověřili a připojili.

    Jakmile budete připojeni, nástroj psql zobrazí příkazový řádek postgres, kde můžete zadávat příkazy jazyka SQL. Ve výstupu počátečního připojení se může zobrazit varování, protože nástroj psql ve službě Cloud Shell může mít jinou verzi než na serveru Azure Database for PostgreSQL. 
    
    Příklad výstupu nástroje psql:
    ```bash
    psql (9.5.7, server 9.6.2)
    WARNING: psql major version 9.5, server major version 9.6.
        Some psql features might not work.
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
    Type "help" for help.
   
    postgres=> 
    ```

    > [!TIP]
    > Pokud brána firewall není nakonfigurovaná k povolení IP adres služby Cloud Shell, dojde k následující chybě:
    > 
    > "psql: FATAL: no pg_hba.conf entry for host "0.0.0.0", user "myadmin", database "postgres", SSL on FATAL: SSL connection is required. (psql: ZÁVAŽNÁ CHYBA: nenalezen žádný záznam pg_hba.conf pro hostitele 0.0.0.0, uživatele myadmin, databázi postgres, ZÁVAŽNÁ CHYBA SSL: vyžaduje se připojení SSL.) Zadejte možnosti SSL a zkuste to znovu.
    > 
    > Pokud chcete chybu vyřešit, ujistěte se, že konfigurace serveru odpovídá postupu v části Konfigurace pravidla brány firewall na úrovni serveru tohoto článku.

4. Vytvořte prázdnou databázi s názvem mypgsqldb zadáním následujícího příkazu na příkazovém řádku:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```
    Dokončení příkazu může trvat několik minut. 

5. Na příkazovém řádku spusťte následující příkaz pro přepnutí připojení na nově vytvořenou databázi **mypgsqldb**:
    ```bash
    \c mypgsqldb
    ```

6. Zadejte `\q` a pak stisknutím klávesy Enter ukončete nástroj psql. Jakmile budete hotovi, můžete Cloud Shell zavřít.

Připojili jste se k serveru Azure Database for PostgreSQL pomocí nástroje psql ve službě Cloud Shell a vytvořili prázdnou uživatelskou databázi. Pokračujte k další části a připojte se pomocí dalšího běžného nástroje pgAdmin.

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Připojení k serveru PostgreSQL pomocí nástroje pgAdmin

pgAdmin je opensourcový nástroj používaný se systémem PostgreSQL. Nástroj pgAdmin můžete nainstalovat z [webu pgAdmin](https://www.pgadmin.org/). Verze nástroje pgAdmin, kterou používáte, se může lišit od verze používané v tomto rychlém startu. Pokud potřebujete další pokyny, přečtěte si dokumentaci nástroje pgAdmin.

1. Na klientském počítači otevřete aplikaci pgAdmin.

2. Na panelu nástrojů přejděte na **Objekt**, najeďte myší na **Vytvořit** a vyberte **Server**.

3. V dialogovém okně **Vytvořit – server** na kartě **Obecné** zadejte jedinečný popisný název serveru, jako například **mydemoserver**.

    ![Karta Obecné](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)

4. V dialogovém okně **Vytvořit – server** na kartě **Připojení** vyplňte tabulku nastavení.

   ![Karta Připojení](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)

    Parametr pgAdmin |Hodnota|Popis
    ---|---|---
    Název nebo adresa hostitele | Název serveru | Hodnota názvu serveru, kterou jste použili dříve při vytváření serveru Azure Database for PostgreSQL. Ukázkový server v příkladu je **mydemoserver.postgres.database.azure.com**. Použijte plně kvalifikovaný název domény (**\*.postgres.database.azure.com), jak je znázorněno v příkladu. Pokud si název vašeho serveru nepamatujete, získejte informace o připojení pomocí postupu v předchozí části. 
    Port | 5432 | Port, který se použije pro připojení k serveru Azure Database for PostgreSQL. 
    Databáze údržby | *postgres* | Výchozí systémem vygenerovaný název databáze.
    Uživatelské jméno | Přihlašovací jméno správce serveru | Přihlašovací uživatelské jméno správce serveru, které jste zadali dříve při vytváření serveru Azure Database for PostgreSQL. Pokud si uživatelské jméno nepamatujete, získejte informace o připojení pomocí postupu v předchozí části. Formát je *username@servername*.
    Heslo | Vaše heslo správce | Heslo, které jste si zvolili při vytváření serveru dříve v tomto rychlém startu.
    Role | Ponechte prázdné | V tuto chvíli není nutné zadávat název role. Ponechte toto pole prázdné.
    Režim SSL | *Vyžadovat* | Na kartě SSL nástroje pgAdmin můžete nastavit režim SSL. Ve výchozím nastavení jsou všechny servery Azure Database for PostgreSQL vytvořené se zapnutým vynucováním SSL. Pokud chcete vynucování SSL vypnout, přečtěte si téma popisující [vynucování SSL](./concepts-ssl-connection-security.md).
    
5. Vyberte **Uložit**.

6. V levém podokně **Prohlížeč** rozbalte uzel **Servery**. Vyberte server, například **mydemoserver**. Kliknutím se k němu připojte.

7. Rozbalte uzel serveru a pod ním pak rozbalte **Databáze**. V seznamu by měla být vaše stávající databáze *postgres* a případné další databáze, které jste vytvořili. Na jednom serveru se službou Azure Database for PostgreSQL můžete vytvořit víc databází.

8. Klikněte pravým tlačítkem na **Databáze**, zvolte nabídku **Vytvořit** a pak vyberte **Databáze**.

9. Do pole **Databáze** zadejte libovolný název databáze, například **mypgsqldb2**.

10. Ze seznamu vyberte **Vlastníka** databáze. Zvolte přihlašovací jméno správce serveru, jako je **myadmin** v příkladu.

   ![Vytvoření databáze v nástroji pgAdmin](./media/quickstart-create-database-portal/11-pgadmin-database.png)

11. Vyberte **Uložit** a vytvořte novou prázdnou databázi.

12. V podokně **Prohlížeč** se vytvořená databáze zobrazí v seznamu databází pod názvem vašeho serveru.


## <a name="clean-up-resources"></a>Vyčištění prostředků
Prostředky, které jste vytvořili v rámci tohoto rychlého startu, můžete vyčistit jedním ze dvou způsobů. Můžete odstranit [skupinu prostředků Azure](../azure-resource-manager/resource-group-overview.md), což zahrnuje odstranění všech prostředků v této skupině prostředků. Pokud chcete ostatní prostředky zachovat beze změny, odstraňte pouze prostředek serveru.

> [!TIP]
> Další rychlé starty v této kolekci jsou postavené na tomto rychlém startu. Pokud chcete pokračovat v práci s dalšími rychlými starty, neodstraňujte prostředky, které jste vytvořili v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí těchto kroků odstraňte prostředky vytvořené tímto rychlým startem na portálu.

Odstranění celé skupiny prostředků, včetně nově vytvořeného serveru:
1. Vyhledejte skupinu prostředků na portálu. V nabídce na levé straně vyberte **Skupiny prostředků**. Potom vyberte název vaší skupiny prostředků, jako je **myresourcegroup** v tomto příkladu.

2. Na stránce vaší skupiny prostředků vyberte **Odstranit**. Pro potvrzení odstranění zadejte do textového pole název vaší skupiny prostředků, jako je **myresourcegroup** v tomto příkladu. Vyberte **Odstranit**.

Odstranění pouze nově vytvořeného serveru:
1. Vyhledejte váš server na portálu, pokud ho ještě nemáte otevřený. V nabídce na levé straně vyberte **Všechny prostředky**. Pak vyhledejte server, který jste vytvořili.

2. Na stránce **Přehled** vyberte **Odstranit**.

    ![Tlačítko Odstranit](./media/quickstart-create-database-portal/12-delete.png)

3. Potvrďte název serveru, který chcete odstranit, a zobrazte jeho databáze, které tím ovlivníte. Do textového pole zadejte název vašeho serveru, jako je **mydemoserver** v tomto příkladu. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Migrace vaší databáze pomocí exportu a importu](./howto-migrate-using-export-and-import.md)
