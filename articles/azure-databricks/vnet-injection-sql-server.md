---
title: Dotaz na kontejner SQL Serveru Linux Docker pomocí Azure Databricks
description: Tento článek popisuje, jak nasadit Azure Databricks do vaší virtuální sítě, označované také jako vkládání virtuální sítě.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 460079248e6cbd939c36b84f94cac41dce4dda2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73747662"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Kurz: Dotaz na kontejner SQL Server Linux Docker ve virtuální síti z poznámkového bloku Azure Databricks

Tento kurz vás naučí, jak integrovat Azure Databricks s kontejnerem SQL Server Linux Docker ve virtuální síti. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nasazení pracovního prostoru Azure Databricks do virtuální sítě
> * Instalace virtuálního počítače s Linuxem do veřejné sítě
> * Instalace Dockeru
> * Instalace Microsoft SQL Serveru do kontejneru dockeru na Linuxu
> * Dotaz na SQL Server pomocí jdbc z poznámkového bloku Databricks

## <a name="prerequisites"></a>Požadavky

* Vytvořte [pracovní prostor Databricks ve virtuální síti](quickstart-create-databricks-workspace-vnet-injection.md).

* Nainstalujte [Ubuntu pro Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Stáhnout aplikaci [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Vytvoření virtuálního počítače s Linuxem

1. Na webu Azure Portal vyberte ikonu virtuálních **počítačů**. Potom vyberte **+ Přidat**.

    ![Přidání nového virtuálního počítače Azure](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Na kartě **Základy** zvolte Ubuntu Server 18.04 LTS a změňte velikost virtuálního počítače na B2s. Zvolte uživatelské jméno a heslo správce.

    ![Karta Základy nové konfigurace virtuálního počítače](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Přejděte na kartu **Síť.** Zvolte virtuální síť a veřejnou podsíť, která obsahuje váš cluster Azure Databricks. Vyberte **Revize + vytvořit**a pak **Vytvořit** pro nasazení virtuálního počítače.

    ![Karta Síť nové konfigurace virtuálního počítače](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Po dokončení nasazení přejděte na virtuální počítač. Všimněte si veřejné IP adresy a virtuální sítě/podsítě v **přehledu**. Vyberte **veřejnou IP adresu**

    ![Přehled virtuálních strojů](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Změňte **přiřazení** na **statický** a zadejte **popisek názvu DNS**. Vyberte **Uložit**a restartujte virtuální počítač.

    ![Konfigurace veřejné IP adresy](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. V části **Nastavení**vyberte kartu **Síť** . Všimněte si, že skupina zabezpečení sítě, která byla vytvořena během nasazení Azure Databricks je přidružena k virtuálnímu počítači. Vyberte **Přidat pravidlo příchozího portu**.

7. Přidejte pravidlo pro otevření portu 22 pro SSH. Použijte následující nastavení:
    
    |Nastavení|Navrhovaná hodnota|Popis|
    |-------|---------------|-----------|
    |Zdroj|IP adresy|Adresy IP určují, že příchozí přenosy z určité zdrojové adresy IP budou tímto pravidlem povoleny nebo odepřeny.|
    |Zdrojové IP adresy|<svou veřejnou ip\>|Zadejte svou veřejnou IP adresu. Svou veřejnou IP adresu najdete tak, že navštívíte [bing.com](https://www.bing.com/) a vyhledáte **"moje IP"**.|
    |Rozsahy zdrojových portů|*|Povolit provoz z libovolného portu.|
    |Cíl|IP adresy|Adresy IP určují, že toto pravidlo povoluje nebo zamítá odchozí provoz pro určitou zdrojovou adresu IP.|
    |Cílové IP adresy|<vaše virtuální vm veřejné IP\>|Zadejte veřejnou IP adresu virtuálního počítače. Najdete to na stránce **Přehled** vašeho virtuálního počítače.|
    |Rozsahy cílových portů|22|Otevřený port 22 pro SSH.|
    |Priorita|290|Dejte pravidlu prioritu.|
    |Name (Název)|ssh-databricks-tutorial-vm|Pojmenujte pravidlo.|


    ![Přidání pravidla příchozího zabezpečení pro port 22](./media/vnet-injection-sql-server/open-port.png)

8. Přidejte pravidlo pro otevření portu 1433 pro SQL s následujícím nastavením:

    |Nastavení|Navrhovaná hodnota|Popis|
    |-------|---------------|-----------|
    |Zdroj|Všechny|Zdroj určuje, že příchozí přenosy z určité zdrojové adresy IP budou tímto pravidlem povoleny nebo odepřeny.|
    |Rozsahy zdrojových portů|*|Povolit provoz z libovolného portu.|
    |Cíl|IP adresy|Adresy IP určují, že toto pravidlo povoluje nebo zamítá odchozí provoz pro určitou zdrojovou adresu IP.|
    |Cílové IP adresy|<vaše virtuální vm veřejné IP\>|Zadejte veřejnou IP adresu virtuálního počítače. Najdete to na stránce **Přehled** vašeho virtuálního počítače.|
    |Rozsahy cílových portů|1433|Otevřete port 22 pro SQL Server.|
    |Priorita|300|Dejte pravidlu prioritu.|
    |Name (Název)|sql-databricks-tutorial-vm|Pojmenujte pravidlo.|

    ![Přidání pravidla příchozího zabezpečení pro port 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Spuštění serveru SQL Server v kontejneru Dockeru

1. Otevřete [Ubuntu pro Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab), nebo jakýkoli jiný nástroj, který vám umožní SSH do virtuálního stroje. Přejděte na virtuální počítač na webu Azure portal a vyberte **Připojit,** abyste získali příkaz SSH, který potřebujete k připojení.

    ![Připojení k virtuálnímu počítači](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Zadejte příkaz do terminálu Ubuntu a zadejte heslo správce, které jste vytvořili při konfiguraci virtuálního počítače.

    ![Ubuntu terminál SSH přihlášení](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Pomocí následujícího příkazu nainstalujte Docker do virtuálního počítače.

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

    Zkontrolujte obrázky.

    ```bash
    sudo docker images
    ```

5. Spusťte kontejner z bitové kopie.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Ověřte, zda je kontejner spuštěn.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Vytvoření databáze SQL

1. Sem otevřete SQL Server Management Studio a připojte se k serveru pomocí názvu serveru a ověřování SQL. Přihlašovací uživatelské jméno je **SA** a heslo je heslo nastavené v příkazu Docker. Heslo v ukázkovém `Password1234`příkazu je .

    ![Připojení k serveru SQL Server pomocí aplikace SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. Po úspěšném připojení vyberte **Nový dotaz** a zadejte následující fragment kódu, abyste vytvořili databázi, tabulku a vložili do tabulky některé záznamy.

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

    ![Dotaz na vytvoření databáze serveru SQL Server](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Dotaz na SQL Server z Azure Databricks

1. Přejděte do pracovního prostoru Azure Databricks a ověřte, že jste vytvořili cluster jako součást předpokladů. Potom vyberte **Vytvořit poznámkový blok**. Pojmenujte poznámkový blok, jako jazyk vyberte *Python* a vyberte cluster, který jste vytvořili.

    ![Nové nastavení poznámkového bloku Databricks](./media/vnet-injection-sql-server/create-notebook.png)

2. Pomocí následujícího příkazu můžete příkazpingem interní IP adresy virtuálního počítače SQL Server. Tento příkaz ping by měl být úspěšný. Pokud ne, ověřte, zda je kontejner spuštěn, a zkontrolujte konfiguraci skupiny zabezpečení sítě (NSG).

    ```python
    %sh
    ping 10.179.64.4
    ```

    Můžete také použít příkaz nslookup ke kontrole.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Po úspěšném vytvoření příkazu ping na sql server můžete dotazovat na databázi a tabulky. Spusťte následující kód pythonu:

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

Když už nepotřebujete, odstraňte skupinu prostředků, pracovní prostor Azure Databricks a všechny související prostředky. Odstraněníúlohy zabrání zbytečné fakturaci. Pokud plánujete v budoucnu používat pracovní prostor Azure Databricks, můžete cluster zastavit a restartovat později. Pokud nebudete nadále používat tento pracovní prostor Azure Databricks, odstraňte všechny prostředky, které jste vytvořili v tomto kurzu pomocí následujících kroků:

1. V levé nabídce na webu Azure Portal klikněte na **Skupiny prostředků** a potom klikněte na název skupiny prostředků, kterou jste vytvořili.

2. Na stránce skupiny prostředků vyberte **Odstranit**, zadejte název prostředku, který chcete odstranit, do textového pole a pak znovu vyberte **Odstranit.**

## <a name="next-steps"></a>Další kroky

Přejdete k dalšímu článku, kde se dozvíte, jak extrahovat, transformovat a načíst data pomocí Azure Databricks.
> [!div class="nextstepaction"]
> [Kurz: Extrahujte, transformujte a načtěte data pomocí Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
