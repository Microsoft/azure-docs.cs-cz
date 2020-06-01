---
title: Připojení a dotazování na Azure SQL Edge (Preview)
description: Informace o připojení a dotazování na Azure SQL Edge (Preview)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e7034c3f664eeba802341510b109ba9cc57845a8
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235135"
---
# <a name="connect-and-query-azure-sql-edge-preview"></a>Připojení a dotazování na Azure SQL Edge (Preview)

Po nasazení kontejneru Azure SQL Edge se můžete připojit ke stroji SQL Database z libovolného z následujících umístění.

- Uvnitř kontejneru
- Z jiného kontejneru Docker běžícího na stejném hostiteli.
- Z hostitelského počítače
- Ze všech ostatních klientských počítačů v síti.

## <a name="tools-to-connect-to-azure-sql-edge"></a>Nástroje pro připojení k Edge SQL Azure

Připojení k instanci Azure SQL Edge je možné provést ze všech běžných nástrojů uvedených níže.

* nástroje [Sqlcmd](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) -Sqlcmd Client Tools jsou již zahrnuty v imagi kontejneru Azure SQL Edge. Pokud se připojíte ke spuštěnému kontejneru pomocí interaktivního prostředí bash, můžete spouštět nástroje místně.
* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)

Pokud se chcete připojit k databázovému stroji Azure SQL Edge ze síťového počítače, budete potřebovat následující:

- *IP adresa nebo síťový název hostitelského počítače* – Toto je hostitelský počítač, ve kterém je spuštěný kontejner Azure SQL Edge.
- *Mapování portů hostitele kontejneru Azure SQL Edge* – Toto je mapování portů pro port Docker kontejneru na port na hostiteli. V rámci kontejneru SQL Edge je vždycky namapovaný na port 1433. To se dá změnit v rámci nasazení Azure SQL Edge. Chcete-li změnit číslo portu, aktualizujte položku "možnosti vytvoření kontejneru" pro modul SQL Edge v Azure IoT Edge. V níže uvedeném příkladu je port 1433 na kontejneru namapován na port 1600 na hostiteli.

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- *Heslo SA pro instanci SQL Edge* – jedná se o hodnotu zadanou pro **SA_PASSWORD** proměnnou prostředí během nasazování SQL Edge.

## <a name="connecting-to-the-database-engine-from-within-the-container"></a>Připojení k databázovému stroji v rámci kontejneru

[Nástroje příkazového řádku SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) jsou zahrnuté v imagi kontejneru Azure SQL Edge. Pokud se k kontejneru připojíte pomocí interaktivního příkazového řádku, můžete spouštět nástroje místně.

1. Pomocí `docker exec -it` příkazu spusťte interaktivní prostředí bash v běžícím kontejneru. V následujícím příkladu `e69e056c702d` je ID kontejneru.

    ```bash
    docker exec -it <Azure SQL Edge container id or name> /bin/bash
    ```

    > [!TIP]
    > Nemusíte vždycky zadávat celé ID kontejneru. Stačí zadat dostatek znaků k jednoznačné identifikaci. V tomto příkladu může být stačit použít `e6` nebo `e69` místo úplného ID.

2. V rámci kontejneru se připojte místně pomocí nástroje Sqlcmd. Nástroj Sqlcmd není ve výchozím nastavení v cestě, takže musíte zadat úplnou cestu.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. Po dokončení příkazu Sqlcmd zadejte `exit` .

4. Po dokončení interaktivního příkazového řádku zadejte `exit` . Váš kontejner bude i nadále běžet po ukončení interaktivního prostředí bash.

## <a name="connect-to-sql-edge-from-another-container-on-the-same-host"></a>Připojení k SQL Edge z jiného kontejneru na stejném hostiteli

Vzhledem k tomu, že dva kontejnery běžící na stejném hostiteli jsou ve stejné síti Docker, dají se snadno použít s názvem kontejneru a adresou portu pro službu. Například pokud se připojujete k instanci SQL Edge z jiného modulu Pythonu (kontejneru) na stejném hostiteli, můžete použít připojovací řetězec podobný následujícímu. Následující příklad předpokládá, že je SQL Edge nakonfigurované pro naslouchání na výchozím portu.

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-sql-edge-from-another-network-machine"></a>Připojení k SQL Edge z jiného síťového počítače

Pokud se chcete připojit k instanci SQL Edge z jiného počítače v síti, musíte použít IP adresu hostitele Docker a port hostitele, ke kterému je namapovaný kontejner SQL Edge. Pokud je například IP adresa hostitele Docker * xxx.xxx.xxx.xxx "a kontejner SQL Edge je namapovaný na port hostitele *1600*, adresa serveru pro instanci SQL Edge by byla **xxx. xxx. xxx. xxx, 1600**. Aktualizovaný skript jazyka Python by byl

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Pokud se chcete připojit k instanci SQL Edge pomocí SQL Server Management Studio běžícího na počítači s Windows, přečtěte si téma [SQL Server Management Studio](https://docs.microsoft.com/sql/linux/sql-server-linux-manage-ssms).

Pokud se chcete připojit k instanci SQL Edge pomocí Visual Studio Code na počítači se systémem Windows, Mac nebo Linux, přečtěte si [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode).

Pokud se chcete připojit k instanci SQL Edge pomocí Azure Data Studio v počítači se systémem Windows, Mac nebo Linux, přečtěte si [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-server).

## <a name="see-also"></a>Viz také

[Připojení a dotazování](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Instalace SQL Server nástrojů v systému Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)
