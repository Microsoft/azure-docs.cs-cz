---
title: Nastavit PostgreSQL na virtuální počítač s Linuxem | Microsoft Docs
description: Zjistěte, jak nainstalovat a nakonfigurovat PostgreSQL na virtuální počítač s Linuxem v Azure
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 7741f861c5697da1e453c0d613b4b762511cf555
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
ms.locfileid: "30241007"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Instalace a konfigurace PostgreSQL v Azure
PostgreSQL je pokročilé open-source databáze podobné Oracle a DB2. Obsahuje funkce připravené pro organizace, například úplné ACID dodržování předpisů, spolehlivé zpracování transakcí a řízení více verzí souběžnosti. Podporuje také standardy, jako je ANSI SQL a SQL nebo MED (včetně obálky cizí dat Oracle, MySQL, MongoDB a mnoho dalších). Je velmi dobře rozšiřitelná s podporou pro více než 12 procedurální jazyky, GIN a GiST indexů, podporu prostorových dat a více funkcí jako NoSQL pro JSON nebo na základě klíčů hodnota aplikace.

V tomto článku se dozvíte, jak nainstalovat a nakonfigurovat PostgreSQL na virtuální počítač Azure s Linuxem.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-postgresql"></a>Nainstalujte PostgreSQL
> [!NOTE]
> Již musí mít virtuální počítač Azure s Linuxem k dokončení tohoto kurzu. Vytvoření a nastavení virtuálního počítače s Linuxem než budete pokračovat, přečtěte si téma [kurzu virtuální počítač Azure s Linuxem](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

V takovém případě použijte port 1999 jako PostgreSQL port.  

Připojte k systému Linux vytvořené prostřednictvím PuTTY virtuálních počítačů. Pokud je virtuální počítač Azure Linux používáte poprvé, přečtěte si téma [postup použití SSH se systémem Linux v Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Další informace o použití klienta PuTTY k připojení k virtuální počítač s Linuxem.

1. Spusťte následující příkaz Přepnout do kořenového adresáře (správce):
   
        # sudo su -
2. Některé distribuce mít závislosti, které je třeba nainstalovat před instalací PostgreSQL. Zkontrolujte vaše distro v tomto seznamu a spusťte příslušný příkaz:
   
   * Red Hat základní Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Debian základní Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Stáhněte PostgreSQL do kořenového adresáře a potom rozbalte balíček:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    Výše je příklad. Můžete najít podrobnější adresu stahování v [Index/pub/zdroj/](https://ftp.postgresql.org/pub/source/).
4. Pokud chcete spustit sestavení, spusťte tyto příkazy:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Pokud chcete vytvořit všechno, co se dají vytvářet, včetně dokumentace (stránky HTML a man) a další moduly (contrib), spusťte následující příkaz:
   
        # gmake install-world
   
    Měli zobrazí následující potvrzující zpráva:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Konfigurace PostgreSQL
1. (Volitelné) Vytvořte symbolický odkaz tak, aby zkrátil odkaz na PostgreSQL tak, aby neobsahoval číslo verze:
   
        # ln -s /opt/pgsql9.3.5 /opt/pgsql
2. Vytvořte adresář pro databázi:
   
        # mkdir -p /opt/pgsql_data
3. Vytvořte uživatele nekořenovými a změna profilu uživatele. Potom přepněte do tohoto nového uživatele (nazývá *postgres* v našem příkladu):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Z bezpečnostních důvodů se používá PostgreSQL nekořenovými uživatele k inicializaci, spuštění nebo vypnutí databáze.
   > 
   > 
4. Upravit *bash_profile* souboru tak, že zadáte níže uvedených příkazů. Tyto řádky přidá na konec *bash_profile* souboru:
   
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
5. Spuštění *bash_profile* souboru:
   
        $ source .bash_profile
6. Ověřte instalaci tak, že pomocí následujícího příkazu:
   
        $ which psql
   
    Pokud je instalace úspěšná, zobrazí se následující odpověď:
   
        /opt/pgsql/bin/psql
7. Můžete také zkontrolovat PostgreSQL verze:
   
        $ psql -V
8. Inicializace databáze:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    Mělo by se zobrazit následující výstup:

![Bitové kopie](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Nastavit PostgreSQL
<!--    [postgres@ test ~]$ exit -->

Spusťte následující příkazy:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Umožňuje změnit dvě proměnné v souboru /etc/init.d/postgresql. Předpona, která je nastavena na cestu instalace PostgreSQL: **/opt/pgsql**. PGDATA nastavena na cestu k úložišti dat PostgreSQL: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![Bitové kopie](./media/postgresql-install/no2.png)

Změna souboru, aby spustitelný soubor:

    # chmod +x /etc/init.d/postgresql

Spusťte PostgreSQL:

    # /etc/init.d/postgresql start

Zkontrolujte, jestli koncový bod PostgreSQL na:

    # netstat -tunlp|grep 1999

Byste měli vidět následující výstup:

![Bitové kopie](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Připojení k databázi Postgres
Přepněte na uživatele postgres znovu:

    # su - postgres

Vytvořte databázi Postgres:

    $ createdb events

Připojení k databázi události, kterou jste právě vytvořili:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Vytvářet a odstraňovat Postgres tabulky
Teď, když se připojíte k databázi, můžete v něm vytvořit tabulky.

Můžete například vytvořte novou tabulku Postgres příklad pomocí následujícího příkazu:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Nyní jste nastavili čtyři sloupce tabulky s následující názvy sloupců a omezení:

1. Sloupec "název" omezil příkazem VARCHAR jako v části 20 znaků.
2. Sloupec "jídlo" označuje položku jídlo, kterými bude každá osoba. VARCHAR omezuje tento text, který má být 30 znaků.
3. "Potvrzen" sloupec zaznamenává, jestli osoba, která má na které odpověděl společné posezení. Přípustné hodnoty jsou "Y" a "N".
4. Zobrazuje sloupec "datum" při registraci pro událost. Postgres vyžaduje, aby data se zapisují jako rrrr mm-dd.

Byste měli vidět následující, pokud tabulka byla úspěšně vytvořena:

![Bitové kopie](./media/postgresql-install/no4.png)

Struktura tabulky můžete také zkontrolovat pomocí následujícího příkazu:

![Bitové kopie](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Přidání dat do tabulky
Nejprve vložení informací do řádek:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Měli byste vidět tento výstup:

![Bitové kopie](./media/postgresql-install/no6.png)

Do tabulky také můžete přidat několik další osoby. Tady jsou některé možnosti, nebo můžete vytvořit vlastní:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Zobrazit tabulky
Zobrazit tabulku použijte následující příkaz:

    select * from potluck;

Výstup je:

![Bitové kopie](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Odstranit data v tabulce
Pokud chcete odstranit data v tabulce použijte následující příkaz:

    delete from potluck where name=’John’;

Tím se odstraní všechny informace v řádku "Jan". Výstup je:

![Bitové kopie](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Aktualizovat data v tabulce
Použijte následující příkaz k aktualizaci dat v tabulce. Pro tento jeden Sandy potvrzuje, že se účastní, tak Změníme jeho RSVP "N" na "Y":

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>Další informace o PostgreSQL
Teď, když jste dokončili instalaci PostgreSQL Linux virtuální počítač Azure, můžete sledovat pomocí v Azure. Další informace o PostgreSQL, najdete [PostgreSQL webu](http://www.postgresql.org/).

