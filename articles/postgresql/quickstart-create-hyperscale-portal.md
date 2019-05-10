---
title: Azure Database for PostgreSQL – velkokapacitní (Citus) (preview) rychlý start
description: Rychlý start k vytvoření a dotazování distribuovaných tabulkách v databázi Azure pro PostgreSQL Hyperškálovatelného (Citus) (preview).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: 4271d94f07125a870cc4aa859b01db819d583f40
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406449"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>Rychlý start: Vytvoření Azure Database for PostgreSQL – velkokapacitní (Citus) (preview) na webu Azure Portal

Azure Database for PostgreSQL je spravovaná služba, pomocí které spouštíte, spravujete a škálujete vysoce dostupné databáze PostgreSQL v cloudu. V tomto rychlém startu se dozvíte, jak vytvořit službu Azure Database for PostgreSQL – velkokapacitní (Citus) (preview) skupiny serverů pomocí webu Azure portal. Prozkoumáte distribuovaných dat: horizontálního dělení tabulky napříč uzly ingestovat ukázkových dat a spuštění dotazů, které jsou spuštěny na několika uzlech.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Vytvoření Azure Database for PostgreSQL

Server Azure Database for PostgreSQL vytvoříte pomocí tohoto postupu:
1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Na stránce **Nový** vyberte **Databáze** a na stránce **Databáze** vyberte **Azure Database for PostgreSQL**.
3. Možnost nasazení, klikněte na tlačítko **vytvořit** tlačítko **skupinu serverů Hyperškálovatelného (Citus) – ve verzi PREVIEW.**
4. Do formuláře podrobností o novém serveru zadejte následující informace:
   - Skupina prostředků: klikněte na tlačítko **vytvořit nový** odkaz pod textové pole pro toto pole. Zadejte název, například **myresourcegroup**.
   - Název skupiny pro server: Zadejte jedinečný název pro novou skupinu serverů, který bude použit také pro subdoménu serveru.
   - Uživatelské jméno správce: Zadejte jedinečné uživatelské jméno, to se později použijí pro připojení k databázi.
   - Heslo: musí být alespoň osm znaků a musí obsahovat znaky ze tří z těchto kategorií – velká písmena anglické, anglické abecedy, malá písmena, číslice (0 – 9) a jiné než alfanumerické znaky (!, $, #, % atd.)
   - Umístění: použijte umístění co nejblíže vašim uživatelům jim zajistili nejrychlejší přístup k datům.

   > [!IMPORTANT]
   > Zde zadané jméno správce serveru a heslo se vyžadují k přihlášení na server a jeho databáze dále v tomto rychlém startu. Tyto informace si zapamatujte nebo poznamenejte pro pozdější použití.

5. Klikněte na tlačítko **konfigurovat skupinu serverů**. Ponechejte nastavení v této části beze změny a klikněte na tlačítko **Uložit**.
6. Klikněte na tlačítko **revize + vytvořit** a potom **vytvořit** ke zřízení serveru. Zřizování trvá několik minut.
7. Monitorování nasazení přesměruje na stránku. Změny za provozu stavu z **nasazení probíhá** k **vaše nasazení je kompletní**, klikněte na tlačítko **výstupy** položky nabídky na levé straně stránky.
8. Stránka výstupy bude obsahovat název hostitele koordinátor s tlačítkem vedle něj zkopírovat hodnotu do schránky. Poznamenejte si tyto údaje pro pozdější použití.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurace pravidla brány firewall na úrovni serveru

Azure Database for PostgreSQL – velkokapacitní (Citus) (preview) používá službu brány firewall na úrovni serveru. Ve výchozím nastavení brána firewall brání všem externím aplikacím a nástrojům v připojení k koordinační uzel a kterékoli databázi uvnitř. Přidáme pravidlo k otevření brány firewall pro konkrétní rozsah IP adres.

1. Z **výstupy** části, kde jste dříve zkopírovali název hostitele koordinační uzel, klikněte na tlačítko zpět do **přehled** položky nabídky.

2. Název skupiny škálování vašeho nasazení bude obsahovat předponu "sg-". Vyhledá v seznamu prostředků a klikněte na něj.

3. Klikněte na tlačítko **brány Firewall** pod **zabezpečení** v levé nabídce.

4. Klikněte na odkaz **+ přidat pravidlo brány firewall pro aktuální IP adresa klienta**. Nakonec klikněte na tlačítko **Uložit** tlačítko.

5. Klikněte na **Uložit**.

   > [!NOTE]
   > Server Azure PostgreSQL komunikuje přes port 5432. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 5432 bránou firewall vaší sítě povolený. Pokud je to tak, nebudete se moct připojit k serveru služby Azure SQL Database, dokud vaše IT oddělení neotevře port 5432.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Připojení k databázi pomocí nástroje psql ve službě Cloud Shell

Teď se pomocí nástroje příkazového řádku [psql](https://www.postgresql.org/docs/current/app-psql.html) připojíme k serveru Azure Database for PostgreSQL.
1. Pomocí ikony terminálu v horním navigačním podokně spusťte službu Azure Cloud Shell.

   ![Azure Database for PostgreSQL – ikona terminálu Azure Cloud Shell](./media/quickstart-create-hyperscale-portal/psql-cloud-shell.png)

2. Služba Azure Cloud Shell se otevře v prohlížeči a umožní vám zadat příkazy Bash.

   ![Azure Database for PostgreSQL – příkazový řádek Bash služby Azure Shell](./media/quickstart-create-hyperscale-portal/psql-bash.png)

3. V příkazovém řádku služby Cloud Shell se pomocí příkazů psql připojte k serveru Azure Database for PostgreSQL. Následující formát se používá pro připojení k serveru Azure Database for PostgreSQL s nástrojem [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Například následující příkaz se připojí k výchozí databázi s názvem **citus** na váš server PostgreSQL **mydemoserver.postgres.database.azure.com** pomocí přihlašovacích údajů pro přístup. Po zobrazení výzvy zadejte heslo správce serveru.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="create-and-distribute-tables"></a>Vytvoření a distribuce tabulky

Po připojení na koordinační uzel hyperškálovatelného pomocí nástroje psql se začít provádět některé základní úlohy.

V rámci Hyperškálovatelného existuje servery jsou tři typy tabulek:

- Distribuované nebo horizontálně dělené tabulky (rozprostřete umožňující škálování pro výkon a paralelizace)
- Referenční tabulky (udržuje několik kopií)
- Místní tabulky (často používají pro správu vnitřní tabulky)

V tomto rychlém startu primárně zaměříme na distribuované tabulky a získání obeznámeni s nimi.

Datový model, My budeme pracovat s je jednoduchý: data uživatele a událostí z Githubu. Události patří vytvoření forku, potvrzením git související s organizace a další.

Po připojení pomocí nástroje psql Pojďme vytvořte našimi tabulkami. V konzole nástroje psql spustit:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

`payload` Pole `github_events` má nastavení JSONB datatype. JSONB je datového typu JSON v binárním formátu v Postgres. To umožňuje snadno ukládat více flexibilní schéma v jednom sloupci.

Můžete vytvořit Postgres `GIN` index u tohoto typu, které se budou indexovat každý klíč a hodnotu v něm. S indexem, stane se rychle a snadno se dotaz na datovou část s různých podmínek. Nyní pokračujme a vytvořte několik indexů předtím, než načteme data. V psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Další používají tyto tabulky Postgres na koordinační uzel a řekněte Hyperškálovatelného do horizontálního oddílu je napříč zaměstnanců. Uděláte to tak, dotaz pro každou tabulku zadání klíče horizontálního oddílu spustíme ji. V uvedeném příkladě necháme události a uživatelé tabulky na horizontální oddíl `user_id`:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Jsme připraveni načíst data. Stáhněte si dvě ukázkové soubory [uživatelé.csv](https://examples.citusdata.com/users.csv) a [události.csv](https://examples.citusdata.com/events.csv). Po stažení souborů, připojte k databázi pomocí nástroje psql, dbejte na to spouštět nástroj psql z adresáře, který obsahuje soubory, které jste stáhli. Načtení dat pomocí `\copy` příkaz:

```sql
\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Spouštění dotazů

Teď je čas zábavné části, ve skutečnosti spuštěné některé dotazy. Začněme jednoduchou `count (*)` chcete zobrazit množství dat, můžeme načíst:

```sql
SELECT count(*) from github_events;
```

Který pracoval hezky. Jsme budete vraťte se na tento druh agregace za chvilku, ale prozatím Podívejme se na několik dalších dotazů. V rámci JSONB `payload` sloupec je dobré bit dat, ale to se liší podle typu události. `PushEvent` události obsahují velikostí, která obsahuje počet jedinečných potvrzení pro vložení. Jsme slouží k vyhledání celkový počet potvrzení za hodinu:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Zatím jste dotazy podílejí githubu\_události výhradně, ale jsme je v kombinaci s githubem\_uživatelů. Protože jsme horizontálně dělené uživatelů a události na stejný identifikátor (`user_id`), řádky obou tabulek s odpovídajícím identifikátorem ID uživatele bude [umístěny společně](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) na stejném databázových uzlů a můžete snadno připojit.

Pokud se nám spojit `user_id`, hyperškálovatelný systém může doručit bez vyžádání provádění spojení do horizontálních oddílů pro spuštění paralelně na pracovních uzlech. Můžete například zjistit třeba, uživatelé, kteří si vytvořili největší počet úložišť:

```sql
SELECT login, count(*)
FROM github_events ge
JOIN github_users gu
ON ge.user_id = gu.user_id
WHERE event_type = 'CreateEvent' AND
      payload @> '{"ref_type": "repository"}'
GROUP BY login
ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině serveru. Pokud jste Neočekáváme, že v budoucnu potřeba tyto prostředky, odstraňte skupinu serveru. Stisknutím klávesy **odstranit** tlačítko **přehled** stránce pro skupinu serverů. Po zobrazení výzvy na místní stránce Potvrďte název serveru skupiny a klikněte na poslední **odstranit** tlačítko.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak zřídit skupinu serverů Hyperškálovatelného (Citus). K němu připojili pomocí nástroje psql, vytvoří schéma a distribuovat data.

Potom postupujte podle kurzu vytvářet škálovatelné aplikace s více tenanty.
> [!div class="nextstepaction"]
> [Návrh databáze pro více Tenantů](https://aka.ms/hyperscale-tutorial-multi-tenant)
