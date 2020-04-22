---
title: Nastavení PostgreSQL na virtuálním počítači s Linuxem
description: Přečtěte si, jak nainstalovat a nakonfigurovat PostgreSQL na virtuálním počítači s Linuxem v Azure
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: d86e42dcc16d108cc82c9d245c7919145cef365f
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759334"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Instalace a konfigurace PostgreSQL v Azure
PostgreSQL je pokročilá open-source databáze podobná Oracle a DB2. Obsahuje funkce připravené pro podniky, jako je úplná shoda ACID, spolehlivé transakční zpracování a řízení souběžnosti s více verzemi. Podporuje také standardy jako ANSI SQL a SQL/MED (včetně zahraničních datových obalů pro Oracle, MySQL, MongoDB a mnoho dalších). Je vysoce rozšiřitelný s podporou více než 12 procedurálních jazyků, gina a GiST indexů, podpory prostorových dat a více funkcí podobných NoSQL pro json nebo aplikace založené na klíči.

V tomto článku se dozvíte, jak nainstalovat a nakonfigurovat PostgreSQL na virtuálním počítači Azure se systémem Linux.


## <a name="install-postgresql"></a>Instalace PostgreSQL
> [!NOTE]
> K dokončení tohoto kurzu už musíte mít virtuální počítač Azure se systémem Linux. Pokud chcete vytvořit a nastavit virtuální počítač s Linuxem, než budete pokračovat, přečtěte si [kurz virtuálního počítače Azure Linux .](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

V tomto případě použijte port 1999 jako port PostgreSQL.  

Připojte se k virtuálnímu počítači S Linuxem, který jste vytvořili přes PuTTY. Pokud používáte virtuální počítač Azure Linux poprvé, přečtěte si téma [Použití SSH s Linuxem v Azure,](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kde se dozvíte, jak používat PuTTY k připojení k virtuálnímu počítači s Linuxem.

1. Spuštěním následujícího příkazu přepněte do kořenového adresáře (admin):
   
        # sudo su -
2. Některé distribuce mají závislosti, které je nutné nainstalovat před instalací PostgreSQL. Zkontrolujte, zda vaše distro v tomto seznamu a spustit příslušný příkaz:
   
   * Red Hat základna Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Debian základní Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Stáhněte si PostgreSQL do kořenového adresáře a poté rozbalte balíček:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    Výše uvedené je příkladem. Podrobnější adresu ke stažení naleznete v [indexu /pub/source/](https://ftp.postgresql.org/pub/source/).
4. Chcete-li spustit sestavení, spusťte tyto příkazy:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Pokud chcete vytvořit vše, co lze sestavit, včetně dokumentace (HTML a`contrib`man stránky) a další moduly ( ), spusťte následující příkaz místo:
   
        # gmake install-world
   
    Měli byste obdržet následující potvrzovací zprávu:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Konfigurace PostgreSQL
1. (Nepovinné) Vytvořte symbolický odkaz pro zkrácení odkazu PostgreSQL tak, aby neobsahoval číslo verze:
   
        # ln -s /opt/postgresql-9.3.5 /opt/pgsql
2. Vytvořte adresář pro databázi:
   
        # mkdir -p /opt/pgsql_data
3. Vytvořte nekořenového uživatele a upravte jeho profil. Poté přepněte na tohoto nového uživatele (v našem příkladu se nazývá *postgres):*
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Z bezpečnostních důvodů PostgreSQL používá uživatele, který není root, k inicializaci, spuštění nebo vypnutí databáze.
   > 
   > 
4. Upravte *soubor bash_profile* zadáním níže uvedených příkazů. Tyto řádky budou přidány na konec *bash_profile* souboru:
   
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
5. Spusťte *bash_profile* soubor:
   
        $ source .bash_profile
6. Ověřte instalaci pomocí následujícího příkazu:
   
        $ which psql
   
    Pokud je instalace úspěšná, zobrazí se následující odpověď:
   
        /opt/pgsql/bin/psql
7. Můžete také zkontrolovat verzi PostgreSQL:
   
        $ psql -V

8. Inicializovat databázi:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    Měli byste obdržet následující výstup:

![image](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Nastavit PostgreSQL
<!--    [postgres@ test ~]$ exit -->

Spusťte následující příkazy:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Upravte dvě proměnné v souboru /etc/init.d/postgresql. Předpona je nastavena na instalační cestu PostgreSQL: **/opt/pgsql**. PGDATA je nastavena na cestu pro ukládání dat PostgreSQL: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/postgresql-install/no2.png)

Změňte soubor tak, aby byl spustitelný:

    # chmod +x /etc/init.d/postgresql

Spustit PostgreSQL:

    # /etc/init.d/postgresql start

Zkontrolujte, zda je koncový bod PostgreSQL zapnutý:

    # netstat -tunlp|grep 1999

Měl by se zobrazit následující výstup:

![image](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Připojení k databázi Postgres
Přepněte na postgres uživatele ještě jednou:

    # su - postgres

Vytvořte databázi Postgres:

    $ createdb events

Připojte se k databázi událostí, kterou jste právě vytvořili:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Vytvoření a odstranění tabulky Postgres
Nyní, když jste se připojili k databázi, můžete v ní vytvořit tabulky.

Můžete například vytvořit novou příklad postgresové tabulky pomocí následujícího příkazu:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Nyní jste nastavili tabulku se čtyřmi sloupci s následujícími názvy sloupců a omezeními:

1. Sloupec "název" byl omezen příkazem VARCHAR, aby byl dlouhý pod 20 znaků.
2. Sloupec "jídlo" označuje potravinovou položku, kterou každá osoba přinese. FUNKCE VARCHAR omezuje tento text na méně než 30 znaků.
3. Sloupec "potvrzeno" zaznamenává, zda má osoba rsvp'd na potluck. Přijatelné hodnoty jsou "Y" a "N".
4. Sloupec "datum" zobrazuje, kdy se zaregistrovali k události. Postgres vyžaduje, aby data byla zapsána jako yyyy-mm-dd.

Pokud byla tabulka úspěšně vytvořena, měli byste vidět následující:

![image](./media/postgresql-install/no4.png)

Strukturu tabulky můžete také zkontrolovat pomocí následujícího příkazu:

![image](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Přidání dat do tabulky
Nejprve vložte informace do řádku:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Měli byste vidět tento výstup:

![image](./media/postgresql-install/no6.png)

Můžete přidat pár dalších lidí na stůl stejně. Zde jsou některé možnosti, nebo si můžete vytvořit vlastní:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Zobrazit tabulky
K zobrazení tabulky použijte následující příkaz:

    select * from potluck;

Výstup bude následující:

![image](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Odstranění dat v tabulce
K odstranění dat v tabulce použijte následující příkaz:

    delete from potluck where name=’John’;

Tím odstraníte všechny informace v řádku "Jan". Výstup bude následující:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Aktualizace dat v tabulce
Pomocí následujícího příkazu aktualizujte data v tabulce. Pro tento jeden, Sandy potvrdil, že se účastní, takže změníme RSVP z "N" na "Y":

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>Získat více informací o PostgreSQL
Teď, když jste dokončili instalaci PostgreSQL v virtuálním počítači Azure Linux, můžete si ho vychutnat v Azure. Chcete-li se dozvědět více o PostgreSQL, navštivte [webové stránky PostgreSQL](https://www.postgresql.org/).

