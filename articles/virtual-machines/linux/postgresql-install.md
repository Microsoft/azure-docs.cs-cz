---
title: Nastavit PostgreSQL na virtuální počítač s Linuxem | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat a nakonfigurovat PostgreSQL na virtuální počítač s Linuxem v Azure
services: virtual-machines-linux
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 1a747363-0cc5-4ba3-9be7-084dfeb04651
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: zarhoads
ms.openlocfilehash: dc7bb0eab9004b9c818a4a7cbbf6102f01b24f45
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465221"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Instalace a konfigurace PostgreSQL v Azure
PostgreSQL je podobný Oracle a DB2 pokročilé open source databáze. Obsahuje připravené pro podnikové funkce, jako je řízení souběžnosti více verzí, plně odpovídající zásadám ACID dodržování předpisů a spolehlivé zpracování transakcí. Podporuje také standardy, jako je ANSI SQL a SQL/MED (včetně obálky cizí dat Oracle, MySQL, MongoDB a mnoha dalších). Je velmi dobře rozšiřitelná s podporou více než 12 postupu jazycích, GIN a GiST indexů, podporu prostorových dat a více funkcí až pravděpodobnosti NoSQL pro JSON nebo aplikace pro systém hodnotou klíče.

V tomto článku se dozvíte, jak nainstalovat a konfigurace PostgreSQL v Azure virtuální počítač s Linuxem.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-postgresql"></a>Instalace PostgreSQL
> [!NOTE]
> Musíte již mít virtuální počítač Azure s Linuxem k dokončení tohoto kurzu. Vytvoření a nastavení virtuálního počítače s Linuxem, než budete pokračovat, přečtěte si téma [Linuxový virtuální počítač Azure kurz](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

V takovém případě použijte port 1999 jako PostgreSQL port.  

Připojte se k Linuxový virtuální počítač vytvořený pomocí PuTTY. Pokud to je poprvé, kdy používáte Linuxový virtuální počítač Azure, přečtěte si téma [jak použít SSH s Linuxem v Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) informace o použití klienta PuTTY k připojení k virtuálnímu počítači s Linuxem.

1. Spusťte následující příkaz pro přepnutí do kořenového adresáře (správce):
   
        # sudo su -
2. Některých distribucích mají závislosti, které je třeba nainstalovat před instalací PostgreSQL. Zkontrolujte vaše distribuce v tomto seznamu a spusťte příslušný příkaz:
   
   * Základní systému Red Hat Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Debian základní Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Stáhněte si PostgreSQL do kořenového adresáře a potom rozbalte balíček:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    Toto je příklad. Podrobnější adresu stahování v najdete [Index/pub/zdroje/](https://ftp.postgresql.org/pub/source/).
4. Pokud chcete spustit sestavení, spusťte tyto příkazy:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Pokud chcete sestavit vše, co je možné sestavit, včetně dokumentace (HTML a man stránky) a dalších modulů (contrib), spusťte následující příkaz:
   
        # gmake install-world
   
    By zobrazila následující potvrzující zpráva:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Konfigurace PostgreSQL
1. (Volitelné) Vytvořte symbolický odkaz ke zkrácení PostgreSQL odkaz tak, aby nezahrnovala číslo verze:
   
        # ln -s /opt/pgsql9.3.5 /opt/pgsql
2. Vytvořte adresář pro databázi:
   
        # mkdir -p /opt/pgsql_data
3. Vytvoření uživatele nekořenovými a upravte profil daného uživatele. Přepněte se do tohoto nového uživatele (volá *postgres* v našem příkladu):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Z bezpečnostních důvodů PostgreSQL používá uživatel nekořenovými inicializovat, spuštění nebo vypnutí databáze.
   > 
   > 
4. Upravit *bash_profile* souboru zadáním následujících příkazů. Tyto řádky se přidají do konce *bash_profile* souboru:
   
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
5. Spustit *bash_profile* souboru:
   
        $ source .bash_profile
6. Ověření instalace pomocí následujícího příkazu:
   
        $ which psql
   
    Pokud je instalace úspěšná, zobrazí se následující odpověď:
   
        /opt/pgsql/bin/psql
7. Verze PostgreSQL, můžete také zkontrolovat:
   
        $ psql -V
8. Inicializujte databázi:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    Mělo by se zobrazit následující výstup:

![image](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Nastavit PostgreSQL
<!--    [postgres@ test ~]$ exit -->

Spusťte následující příkazy:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Upravte v souboru /etc/init.d/postgresql dvě proměnné. Předpona, která je nastavena na cestu instalace postgresql: **/opt/pgsql**. PGDATA nastavena cesta k úložišti dat postgresql: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/postgresql-install/no2.png)

Změna souboru k němu spustitelný:

    # chmod +x /etc/init.d/postgresql

Spusťte PostgreSQL:

    # /etc/init.d/postgresql start

Zkontrolujte, jestli koncového bodu postgresql na:

    # netstat -tunlp|grep 1999

Měl by se zobrazit následující výstup:

![image](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Připojení k databázi Postgres
Ještě jednou přepnutí na uživatele postgres:

    # su - postgres

Vytvořte databázi Postgres:

    $ createdb events

Připojení k databázi události, kterou jste právě vytvořili:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Vytvářet a odstraňovat tabulky Postgres
Teď, když jste se připojili k databázi, můžete vytvořit tabulky v ní.

Například vytvořte novou tabulku Postgres příklad pomocí následujícího příkazu:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Nyní jste nastavili Čtyřsloupcový Dvoucestný tabulku s následujícími názvy sloupců a omezení:

1. Sloupce "name" omezil příkazem VARCHAR bude pod 20 znaků.
2. Sloupec "potravin" označuje položku food, každý uživatel, který přinese. VARCHAR omezuje tento text, který má být v části 30 znaků.
3. "Potvrzené" sloupce zaznamenává, jestli osoba pořádá na společnou večeři. Přípustné hodnoty jsou "Y" a "N".
4. Zobrazí sloupec "datum" při registraci na událost. Postgres vyžaduje, že se data zapisují jako rrrr mm-dd.

Měli byste vidět následující, pokud vaše tabulka byla úspěšně vytvořena:

![image](./media/postgresql-install/no4.png)

Struktura tabulky můžete také zkontrolovat pomocí následujícího příkazu:

![image](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Přidání dat do tabulky
Nejprve vložení informací do řádku:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Byste měli vidět tento výstup:

![image](./media/postgresql-install/no6.png)

Tabulky také můžete přidat několik více lidí. Tady jsou některé možnosti, nebo můžete vytvořit svoje vlastní:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Zobrazit tabulky
Použijte následující příkaz pro zobrazení tabulky:

    select * from potluck;

Výstup bude:

![image](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Odstranit data v tabulce
Pomocí následujícího příkazu odstraňte data v tabulce:

    delete from potluck where name=’John’;

Tím se odstraní všechny informace v řádku "John". Výstup bude:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Aktualizace dat v tabulce
Použijte následující příkaz k aktualizaci dat v tabulce. Pro tento jeden Sandy potvrzuje, že se účast na akci, takže jsme se změní jeho RSVP "N", "Y":

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>Získejte další informace o PostgreSQL
Teď, když jste dokončili instalace PostgreSQL jsou ve virtuálním počítači Azure s Linuxem, budete moct využívat používat v Azure. Další informace o PostgreSQL, přejděte [PostgreSQL webu](http://www.postgresql.org/).

