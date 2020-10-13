---
title: Nastavení PostgreSQL na virtuálním počítači se systémem Linux
description: Informace o tom, jak nainstalovat a nakonfigurovat PostgreSQL na virtuálním počítači se systémem Linux v Azure
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: fdde7613627c9fec0694f3985f78cf10e52f59c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397092"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Instalace a konfigurace PostgreSQL v Azure
PostgreSQL je pokročilá open source databáze podobná Oracle a DB2. Zahrnuje funkce připravené pro podnikové prostředí, jako je plně KYSELé dodržování předpisů, spolehlivé zpracování transakcí a řízení souběžnosti s více verzemi. Podporuje také standardy, jako je ANSI SQL a SQL/MED (včetně obálek cizích dat pro Oracle, MySQL, MongoDB a mnoho dalších). Je vysoce rozšiřitelná s podporou více než 12 procedurálních jazyků, GIN a registrových indexů, podpory prostorových dat a několika NoSQL funkcí podobných funkcím pro aplikace založené na formátu JSON nebo klíč-hodnota.

V tomto článku se dozvíte, jak nainstalovat a nakonfigurovat PostgreSQL na virtuálním počítači Azure se systémem Linux.


## <a name="install-postgresql"></a>Nainstalovat PostgreSQL
> [!NOTE]
> Aby bylo možné dokončit tento kurz, musíte mít již virtuální počítač Azure se systémem Linux. Pokud chcete před pokračováním vytvořit a nastavit virtuální počítač se systémem Linux, přečtěte si [kurz k virtuálním počítačům Azure Linux](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

V takovém případě jako port PostgreSQL použijte port 1999.  

Připojte se k virtuálnímu počítači se systémem Linux, který jste vytvořili prostřednictvím výstupu. Pokud používáte virtuální počítač Azure Linux poprvé, přečtěte si téma [Použití SSH se systémem Linux v Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a Naučte se používat výstupy pro připojení k virtuálnímu počítači Linux.

1. Spusťte následující příkaz, který přepne do kořenového adresáře (správce):

    ```console
    # sudo su -
    ```

2. Některé distribuce mají závislosti, které je třeba nainstalovat před instalací PostgreSQL. V tomto seznamu vyhledejte distribuce a spusťte příslušný příkaz:
   
   * Základní operační systém Red Hat:

        ```console
        # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y
        ```

   * Debian Base Linux:

        ```console
        # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y
        ```

   * SUSE Linux:

        ```console
        # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y
        ```

3. Stáhněte si PostgreSQL do kořenového adresáře a potom balíček rozbalte:

    ```console
    # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

    # tar jxvf  postgresql-9.3.5.tar.bz2
    ```

    Výše je příklad. Podrobnější adresu ke stažení najdete v [indexu/Pub/source/](https://ftp.postgresql.org/pub/source/).

4. Chcete-li spustit sestavení, spusťte tyto příkazy:

    ```console
    # cd postgresql-9.3.5

    # ./configure --prefix=/opt/postgresql-9.3.5
    ```

5. Pokud chcete sestavit všechno, co se dá sestavit, včetně dokumentace (HTML a Man Pages) a dalších modulů ( `contrib` ), spusťte následující příkaz:

    ```console
    # gmake install-world
    ```

    Měla by se zobrazit následující potvrzovací zpráva:

    ```output
    PostgreSQL, contrib, and documentation successfully made. Ready to install.
    ```

## <a name="configure-postgresql"></a>Konfigurace PostgreSQL
1. Volitelné Vytvořte symbolický odkaz, který zkrátí odkaz na PostgreSQL, aby neobsahoval číslo verze:

    ```console
    # ln -s /opt/postgresql-9.3.5 /opt/pgsql
    ```

2. Vytvořte adresář pro databázi:

    ```console
    # mkdir -p /opt/pgsql_data
    ```

3. Vytvoření nekořenového uživatele a změna profilu daného uživatele. Pak v našem příkladu přepněte na tohoto nového uživatele (s názvem *Postgres* ):

    ```console
    # useradd postgres
   
    # chown -R postgres.postgres /opt/pgsql_data
   
    # su - postgres
    ```
   
   > [!NOTE]
   > Z bezpečnostních důvodů používá PostgreSQL k inicializaci, spuštění nebo vypnutí databáze uživatele, který není rootem.
   > 
   > 
4. Zadáním následujících příkazů upravte soubor *bash_profile* . Tyto řádky budou přidány na konec souboru *bash_profile* :

    ```config
    cat >> ~/.bash_profile <<EOF
    export PGPORT=1999
    export PGDATA=/opt/pgsql_data
    export LANG=en_US.utf8
    export PGHOME=/opt/pgsql
    export PATH=\$PATH:\$PGHOME/bin
    export MANPATH=\$MANPATH:\$PGHOME/share/man
    export DATA=`date +"%Y%m%d%H%M"`
    export PGUSER=postgres
    alias rm='rm -i'
    alias ll='ls -lh'
    EOF
    ```

5. Spusťte soubor *bash_profile* :

    ```console
    $ source .bash_profile
    ```

6. Ověřte instalaci pomocí následujícího příkazu:

    ```console
    $ which psql
    ```

    Pokud je instalace úspěšná, zobrazí se následující odpověď:

    ```output
    /opt/pgsql/bin/psql
    ```

7. Můžete také zjistit verzi PostgreSQL:

    ```sql
    $ psql -V
    ```

8. Inicializovat databázi:

    ```console
    $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
    ```

    Měl by se zobrazit následující výstup:

![Snímek obrazovky, který zobrazuje výstup po inicializaci databáze.](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Nastavení PostgreSQL
<!--    [postgres@ test ~]$ exit -->

Spusťte následující příkazy:

```console
# cd /root/postgresql-9.3.5/contrib/start-scripts

# cp linux /etc/init.d/postgresql
```

Upravte v souboru/etc/init.d/PostgreSQL dvě proměnné. Předpona je nastavena na instalační cestu PostgreSQL: **/opt/PgSQL**. PGDATA je nastavená na cestu úložiště dat PostgreSQL: **/opt/pgsql_data**.

```config
# sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

# sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql
```

![Snímek obrazovky, který zobrazuje předponu instalace a datový adresář.](./media/postgresql-install/no2.png)

Změňte soubor tak, aby byl spustitelný jako spustitelný:

```console
# chmod +x /etc/init.d/postgresql
```

Spustit PostgreSQL:

```console
# /etc/init.d/postgresql start
```

Ověřte, zda je koncový bod PostgreSQL zapnutý:

```console
# netstat -tunlp|grep 1999
```

Měl by se zobrazit následující výstup:

![Snímek obrazovky zobrazující koncový bod PostgreSQL je zapnutý.](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Připojení k databázi Postgres
Přepněte znovu na uživatele Postgres:

```console
# su - postgres
```

Vytvořte databázi Postgres:

```console
$ createdb events
```

Připojte se k databázi událostí, kterou jste právě vytvořili:

```console
$ psql -d events
```

## <a name="create-and-delete-a-postgres-table"></a>Vytvoření a odstranění tabulky Postgres
Teď, když jste se připojili k databázi, můžete v ní vytvořit tabulky.

Vytvořte například novou ukázkovou tabulku Postgres pomocí následujícího příkazu:

```sql
CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);
```

Nyní jste nastavili tabulku se čtyřmi sloupci s následujícími názvy a omezeními:

1. Sloupec "Name" byl omezen příkazem VARCHAR, který má být kratší než 20 znaků.
2. Sloupec "food" označuje položku potraviny, kterou budou jednotliví jednotlivci přinášet. VARCHAR omezuje tento text na méně než 30 znaků.
3. Sloupec potvrzeno označuje, zda má osoba protokol RSVP k sezení. Přijatelné hodnoty jsou "Y" a "N".
4. Sloupec Date (datum) se zobrazí při registraci k události. Postgres vyžaduje, aby data byla zapsána jako rrrr-mm-dd.

Pokud se tabulka úspěšně vytvořila, mělo by se zobrazit následující:

![Snímek obrazovky zobrazující zprávu, která se zobrazí po úspěšném vytvoření tabulky](./media/postgresql-install/no4.png)

Strukturu tabulky můžete také ověřit pomocí následujícího příkazu:

![Snímek obrazovky zobrazující příkaz pro kontrolu struktury tabulky](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Přidání dat do tabulky
Nejprve vložte informace do řádku:

```sql
INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');
```

Měli byste vidět tento výstup:

![Snímek obrazovky, který zobrazuje informace o řádku, které jste přidali.](./media/postgresql-install/no6.png)

Do tabulky můžete přidat i několik dalších lidí. Tady je několik možností, nebo můžete vytvořit vlastní:

```sql
INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');
```

### <a name="show-tables"></a>Zobrazit tabulky
Tabulku můžete zobrazit pomocí následujícího příkazu:

```sql
select * from potluck;
```

Výstup bude následující:

![Snímek obrazovky, který zobrazuje výstup příkazu pro zobrazení tabulky](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Odstranění dat v tabulce
Pomocí následujícího příkazu odstraňte data v tabulce:

```sql
delete from potluck where name=’John’;
```

Tím se odstraní všechny informace v řádku "Jan". Výstup bude následující:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Aktualizace dat v tabulce
K aktualizaci dat v tabulce použijte následující příkaz. V takovém případě Oranžovohnědá potvrdí, že se účastní, takže změníme protokol RSVP z "N" na "Y":

```sql
UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';
```

## <a name="get-more-information-about-postgresql"></a>Získat další informace o PostgreSQL
Teď, když jste dokončili instalaci PostgreSQL na virtuálním počítači Azure Linux, můžete využít jeho používání v Azure. Další informace o PostgreSQL najdete na [webu PostgreSQL](https://www.postgresql.org/).
