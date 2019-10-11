---
title: Dotazování kontejneru Docker SQL Server Linux s Azure Databricks
description: Tento článek popisuje, jak nasadit Azure Databricks do vaší virtuální sítě, označované taky jako vkládání virtuální sítě.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 773ffe264446e6a4d9ef2e88634e4f2c9b8aeb45
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273975"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Kurz: dotazování kontejneru Docker SQL Server Linux ve virtuální síti z poznámkového bloku Azure Databricks

V tomto kurzu se naučíte, jak integrovat Azure Databricks s kontejnerem SQL Server Linux Docker ve virtuální síti. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nasazení pracovního prostoru Azure Databricks do virtuální sítě
> * Instalace virtuálního počítače se systémem Linux ve veřejné síti
> * Nainstalovat Docker
> * Nainstalovat Microsoft SQL Server do kontejneru Docker platformy Linux
> * Dotazování SQL Server pomocí JDBC z poznámkového bloku datacihly

## <a name="prerequisites"></a>Požadavky

* Vytvořte [pracovní prostor datacihly ve virtuální síti](quickstart-create-databricks-workspace-vnet-injection.md).

* Nainstalujte [Ubuntu pro Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Stáhněte si [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Vytvoření virtuálního počítače se systémem Linux

1. V Azure Portal vyberte ikonu pro **Virtual Machines**. Pak vyberte **+ Přidat**.

    ![Přidat nový virtuální počítač Azure](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Na kartě **základy** vyberte Ubuntu Server 16,04 LTS. Změňte velikost virtuálního počítače na B1ms, který má jednu VCPU a 2 GB paměti RAM. Minimální požadavek na kontejner Docker SQL Server pro Linux je 2 GB. Vyberte uživatelské jméno a heslo správce.

    ![Karta základy nové konfigurace virtuálního počítače](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Přejděte na kartu **sítě** . vyberte virtuální síť a veřejnou podsíť, která zahrnuje váš cluster Azure Databricks. Vyberte **zkontrolovat + vytvořit**a pak **vytvořit** a nasaďte virtuální počítač.

    ![Karta sítě pro novou konfiguraci virtuálního počítače](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Až se nasazení dokončí, přejděte k virtuálnímu počítači. Všimněte si veřejné IP adresy a virtuální sítě/podsítě v **přehledu**. Vybrat **veřejnou IP adresu**

    ![Přehled virtuálních počítačů](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Změňte **přiřazení** na **statické** a zadejte **popisek názvu DNS**. Vyberte **Uložit**a restartujte virtuální počítač.

    ![Konfigurace veřejné IP adresy](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. V části **Nastavení**vyberte kartu **síť** . Všimněte si, že skupina zabezpečení sítě, která se vytvořila během nasazení Azure Databricks, je přidružená k virtuálnímu počítači. Vyberte **Přidat pravidlo portu pro příchozí spojení**.

7. Přidejte pravidlo pro otevření portu 22 pro SSH. Použijte následující nastavení:
    
    |Nastavením|Navrhovaná hodnota|Popis|
    |-------|---------------|-----------|
    |Zdroj|IP adresy|IP adresy určují, že toto pravidlo povolí nebo zakáže příchozí přenosy z konkrétní zdrojové IP adresy.|
    |Zdrojové IP adresy|< veřejné IP adresy @ no__t-0|Zadejte veřejnou IP adresu. Veřejnou IP adresu najdete na webu [Bing.com](https://www.bing.com/) a hledáním možnosti **"moje IP adresa"** .|
    |Rozsahy zdrojových portů|*|Povolí provoz z libovolného portu.|
    |Tabulka|IP adresy|IP adresy určují, že toto pravidlo povolí nebo odepře odchozí přenosy pro konkrétní zdrojovou IP adresu.|
    |Cílové IP adresy|< veřejné IP adresy virtuálního počítače @ no__t-0|Zadejte veřejnou IP adresu vašeho virtuálního počítače. Najdete ho na stránce **Přehled** na vašem virtuálním počítači.|
    |Rozsahy cílových portů|22|Otevřete port 22 pro SSH.|
    |Priorita|290|Zadejte prioritu pravidla.|
    |Name|SSH-datacihly – kurz – virtuální počítač|Zadejte název pravidla.|


    ![Přidat příchozí pravidlo zabezpečení pro port 22](./media/vnet-injection-sql-server/open-port.png)

8. Přidejte pravidlo pro otevření portu 1433 pro SQL s následujícím nastavením:

    |Nastavením|Navrhovaná hodnota|Popis|
    |-------|---------------|-----------|
    |Zdroj|IP adresy|IP adresy určují, že toto pravidlo povolí nebo zakáže příchozí přenosy z konkrétní zdrojové IP adresy.|
    |Zdrojové IP adresy|10.179.0.0/16|Zadejte rozsah adres pro virtuální síť.|
    |Rozsahy zdrojových portů|*|Povolí provoz z libovolného portu.|
    |Tabulka|IP adresy|IP adresy určují, že toto pravidlo povolí nebo odepře odchozí přenosy pro konkrétní zdrojovou IP adresu.|
    |Cílové IP adresy|< veřejné IP adresy virtuálního počítače @ no__t-0|Zadejte veřejnou IP adresu vašeho virtuálního počítače. Najdete ho na stránce **Přehled** na vašem virtuálním počítači.|
    |Rozsahy cílových portů|1433|Otevřete port 22 pro SQL Server.|
    |Priorita|300|Zadejte prioritu pravidla.|
    |Name|SQL-datacihly – kurz – virtuální počítač|Zadejte název pravidla.|

    ![Přidat příchozí pravidlo zabezpečení pro port 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Spuštění SQL Server v kontejneru Docker

1. Otevřete [Ubuntu pro Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)nebo jakýkoli jiný nástroj, který vám umožní SSH do virtuálního počítače. V Azure Portal přejděte na svůj virtuální počítač a vyberte **připojit** , abyste získali příkaz SSH, který potřebujete připojit.

    ![Připojit k virtuálnímu počítači](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Zadejte příkaz do terminálu Ubuntu a zadejte heslo správce, které jste vytvořili při konfiguraci virtuálního počítače.

    ![Přihlášení k Ubuntu terminálovému protokolu SSH](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. K instalaci Docker na virtuální počítač použijte následující příkaz.

    ```bash
    sudo apt-get install docker.io
    ```

    Ověřte instalaci Docker pomocí následujícího příkazu:

    ```bash
    sudo docker --version
    ```

4. Nainstalujte bitovou kopii.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Podívejte se na obrázky.

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

1. Otevřete SQL Server Management Studio a připojte se k serveru pomocí názvu serveru a ověřování SQL. Přihlašovací uživatelské jméno je **SA** a heslo je nastaveno na heslo v příkazu Docker. Heslo v příkazu příkladu je `Password1234`.

    ![Připojení k SQL Server pomocí SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. Po úspěšném připojení vyberte **Nový dotaz** a zadejte následující fragment kódu k vytvoření databáze, tabulky a vložení některých záznamů v tabulce.

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

    ![Dotaz pro vytvoření databáze SQL Server](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Dotaz SQL Server z Azure Databricks

1. Přejděte do pracovního prostoru Azure Databricks a ověřte, že jste cluster vytvořili jako součást požadavků. Pak vyberte **vytvořit Poznámkový blok**. Poznámkový blok pojmenujte, jako jazyk vyberte *Python* a vyberte cluster, který jste vytvořili.

    ![Nové nastavení poznámkového bloku datacihly](./media/vnet-injection-sql-server/create-notebook.png)

2. Pomocí následujícího příkazu otestujte interní IP adresu SQL Server virtuálního počítače. Tento test by měl být úspěšný. Pokud ne, ověřte, že je kontejner spuštěný, a zkontrolujte konfiguraci skupiny zabezpečení sítě (NSG).

    ```python
    %sh
    ping 10.179.64.4
    ```

    K revizi můžete použít také příkaz nslookup.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Po úspěšném provedení příkazu příkazového testu SQL Server můžete zadat dotaz na databázi a tabulky. Spusťte následující kód Pythonu:

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

Pokud už je nepotřebujete, odstraňte skupinu prostředků, pracovní prostor Azure Databricks a všechny související prostředky. Odstranění úlohy se vyhne zbytečnému fakturaci. Pokud plánujete použít pracovní prostor Azure Databricks v budoucnu, můžete cluster zastavit a restartovat ho později. Pokud nebudete nadále používat tento Azure Databricks pracovní prostor, odstraňte všechny prostředky, které jste vytvořili v tomto kurzu, pomocí následujících kroků:

1. V nabídce na levé straně Azure Portal klikněte na **skupiny prostředků** a pak klikněte na název skupiny prostředků, kterou jste vytvořili.

2. Na stránce skupiny prostředků vyberte **Odstranit**, do textového pole zadejte název prostředku, který chcete odstranit, a pak vyberte **Odstranit** znovu.

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku, kde se dozvíte, jak extrahovat, transformovat a načítat data pomocí Azure Databricks.
> [!div class="nextstepaction"]
> [Kurz: extrakce, transformace a načtení dat pomocí Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
