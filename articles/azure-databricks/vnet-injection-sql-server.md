---
title: Dotaz kontejneru Dockeru Linux SQL serveru ve virtuální síti ze Azure Databricks Poznámkový blok
description: Tento článek popisuje, jak nasadit Azure Databricks k virtuální síti, označované také jako virtuální síť vkládání.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 345e07fac30f4ad0c8e9918cb8a1ff0fb8aeb811
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770635"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Kurz: Dotaz kontejneru Dockeru Linux SQL serveru ve virtuální síti ze Azure Databricks Poznámkový blok

V tomto kurzu se naučíte, jak integrovat Azure Databricks s kontejnerem Dockeru Linux SQL serveru ve virtuální síti. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Do virtuální sítě nasadíte, pracovnímu prostoru Azure Databricks
> * Nainstalujte virtuální počítač s Linuxem ve veřejné síti
> * Instalace Dockeru
> * Microsoft SQL Server nainstalovat na kontejneru dockeru v Linuxu
> * Zadat dotaz na SQL Server pomocí JDBC z poznámkového bloku Databricks

## <a name="prerequisites"></a>Požadavky

* Vytvoření [pracovního prostoru Databricks ve virtuální síti](quickstart-create-databricks-workspace-vnet-injection.md).

* Nainstalujte [Ubuntu pro Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Stáhnout aplikaci [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Vytvoření virtuálního počítače s Linuxem

1. Na webu Azure Portal, vyberte ikonu **virtuálních počítačů**. Vyberte **+ přidat**.

    ![Přidat nový virtuální počítač Azure](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Na **Základy** kartu, vyberte Ubuntu Server 16.04 LTS. Změňte velikost virtuálního počítače na B1ms, který má jeden virtuální procesory a 2 GB paměti RAM. Minimální požadavek pro kontejner Dockeru Linux SQL Server je 2 GB. Zvolte správce, uživatelské jméno a heslo.

    ![Základní informace o kartě nové konfigurace virtuálního počítače](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Přejděte **sítě** kartu. Zvolte virtuální sítě a veřejné podsítě, která obsahuje váš cluster Azure Databricks. Vyberte **revize + vytvořit**, pak **vytvořit** nasazení virtuálního počítače.

    ![Karta síť se nová konfigurace virtuálního počítače](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Po dokončení nasazení přejděte k virtuálnímu počítači. Všimněte si, že veřejná IP adresa a virtuální síť/podsíť v **přehled**. Vyberte **veřejná IP adresa**

    ![Přehled virtuálních počítačů](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Změnit **přiřazení** k **statické** a zadejte **popisku názvu DNS**. Vyberte **Uložit**a restartujte virtuální počítač.

    ![Konfigurace veřejné IP adresy](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Vyberte **sítě** kartu **nastavení**. Všimněte si, že skupina zabezpečení sítě, který jste vytvořili během nasazení Azure Databricks souvisí s virtuálním počítačem. Vyberte **přidat pravidlo portu pro příchozí spojení**.

7. Přidáte pravidlo pro otevření portu 22 pro SSH. Použijte následující nastavení:
    
    |Nastavení|Navrhovaná hodnota|Popis|
    |-------|---------------|-----------|
    |Zdroj|IP adresy|IP adresy určuje, že příchozí provoz z konkrétního zdroje IP adres budou povolené nebo zakázané tímto pravidlem.|
    |Zdrojové IP adresy|< vaše veřejné IP adresy\>|Zadejte veřejnou IP adresu. Veřejné IP adresy můžete najít návštěvou [bing.com](https://www.bing.com/) a vyhledáte **"Moje IP adresa"**.|
    |Rozsahy zdrojových portů|*|Povolte provoz z jakéhokoli portu.|
    |Cíl|IP adresy|IP adresy určuje, že odchozí přenosy pro konkrétní zdroj IP adres budou povolené nebo zakázané tímto pravidlem.|
    |Cílové IP adresy|< veřejnou IP adresu vašeho virtuálního počítače\>|Zadejte veřejnou IP adresu virtuálního počítače. Tento nástroj naleznete na **přehled** stránku vašeho virtuálního počítače.|
    |Rozsahy cílových portů|22|Otevřete port 22 pro SSH.|
    |Priorita|290|Zadejte pro pravidlo s prioritou.|
    |Název|ssh-databricks-tutorial-vm|Zadejte název pravidla.|


    ![Přidat příchozí pravidlo zabezpečení pro port 22](./media/vnet-injection-sql-server/open-port.png)

8. Přidáte pravidlo pro otevření portu 1433 pro SQL s následujícím nastavením:

    |Nastavení|Navrhovaná hodnota|Popis|
    |-------|---------------|-----------|
    |Zdroj|IP adresy|IP adresy určuje, že příchozí provoz z konkrétního zdroje IP adres budou povolené nebo zakázané tímto pravidlem.|
    |Zdrojové IP adresy|10.179.0.0/16|Zadejte rozsah adres vaší virtuální sítě.|
    |Rozsahy zdrojových portů|*|Povolte provoz z jakéhokoli portu.|
    |Cíl|IP adresy|IP adresy určuje, že odchozí přenosy pro konkrétní zdroj IP adres budou povolené nebo zakázané tímto pravidlem.|
    |Cílové IP adresy|< veřejnou IP adresu vašeho virtuálního počítače\>|Zadejte veřejnou IP adresu virtuálního počítače. Tento nástroj naleznete na **přehled** stránku vašeho virtuálního počítače.|
    |Rozsahy cílových portů|1433|Otevřete port 22 pro SQL Server.|
    |Priorita|300|Zadejte pro pravidlo s prioritou.|
    |Název|sql-databricks-tutorial-vm|Zadejte název pravidla.|

    ![Přidat příchozí pravidlo zabezpečení pro port 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Spusťte SQL Server v kontejneru Dockeru

1. Otevřít [Ubuntu pro Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab), nebo jakýkoli jiný nástroj, který vám umožní SSH k virtuálnímu počítači. Přejděte ke svému virtuálnímu počítači v Azure portal a vyberte **připojit** získat příkaz SSH, je nutné se připojit.

    ![Připojení k virtuálnímu počítači](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Zadejte příkaz, v terminálu Ubuntu a zadejte heslo správce, který jste vytvořili při konfiguraci virtuálního počítače.

    ![Ubuntu terminálu SSH přihlášení](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Použijte následující příkaz k instalaci Dockeru ve virtuálním počítači.

    ```bash
    sudo apt-get install docker.io
    ```

    Ověřte instalaci Dockeru pomocí následujícího příkazu:

    ```bash
    sudo docker --version
    ```

4. Nainstalujte bitovou kopii.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Zkontrolujte bitové kopie.

    ```bash
    sudo docker images
    ```

5. Spusťte kontejner z image.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Ověřte, že je kontejner spuštěný.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Vytvoření databáze SQL

1. Otevřete SQL Server Management Studio a připojte se k serveru pomocí názvu serveru a ověřování SQL. Přihlašovací uživatelské jméno je **SA** a heslo je heslo v příkazu Docker. Heslo v ukázkovém příkazu se `Password1234`.

    ![Připojení k SQL serveru pomocí aplikace SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. Po úspěšném připojení, vyberte **nový dotaz** a zadejte následující fragment kódu pro vytvoření databáze, tabulky a vložit některé záznamy v tabulce.

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![Dotaz pro vytvoření databáze SQL serveru](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Dotaz SQL serveru z Azure Databricks

1. Přejděte do pracovního prostoru Azure Databricks a ověřte, že jste vytvořili cluster jako součást požadavků. Vyberte **vytvoření poznámkového bloku**. Pojmenujte Poznámkový blok, vyberte *Python* jako jazyk a vyberte cluster, který jste vytvořili.

    ![Nové nastavení poznámkového bloku Databricks](./media/vnet-injection-sql-server/create-notebook.png)

2. Použijte následující příkaz pro odeslání příkazu ping interní IP adresa virtuálního počítače systému SQL Server. Toto pingnutí by měl být úspěšné. Pokud ne, ověřte, zda je spuštěna kontejneru a zkontrolujte konfiguraci sítě (NSG) skupiny zabezpečení.

    ```python
    %sh
    ping 10.179.64.4
    ```

    Příkaz nslookup můžete také zkontrolovat.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Jakmile jste úspěšně použití příkazu ping u SQL serveru, se můžete dotazovat databáze a tabulky. Spuštění kódu pythonu následující:

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, pracovní prostor Azure Databricks a všechny související prostředky. Odstraněním úlohy se vyhnete zbytečným fakturace. Pokud plánujete používat pracovní prostor Azure Databricks v budoucnu, můžete zastavit clusteru a počítač restartovat později. Pokud nebudete nadále používat tento pracovní prostor Azure Databricks, odstraňte všechny prostředky, které jste vytvořili v tomto kurzu pomocí následujících kroků:

1. V nabídce vlevo na webu Azure Portal klikněte na **skupiny prostředků** a pak klikněte na název skupiny prostředků, kterou jste vytvořili.

2. Na stránce skupiny prostředků, vyberte **odstranit**, zadejte název prostředku, který chcete odstranit v textovém poli a pak vyberte **odstranit** znovu.

## <a name="next-steps"></a>Další postup

Přejděte k dalším článku se naučíte, jak extrakce, transformace a načítání dat pomocí Azure Databricks.
> [!div class="nextstepaction"]
> [Kurz: Extrakce, transformace a načítání dat pomocí Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
