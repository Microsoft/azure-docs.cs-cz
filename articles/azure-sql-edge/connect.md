---
title: Připojení a dotazování na Edge Azure SQL
description: Naučte se připojit k Edge SQL Azure a dotazovat se na něj.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/25/2020
ms.openlocfilehash: 4548d4956b4cd01886fb1be9a530cc1627f76b2c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90888242"
---
# <a name="connect-and-query-azure-sql-edge"></a>Připojení a dotazování na Edge Azure SQL

Po nasazení kontejneru v Azure SQL Edge se můžete připojit k databázovému stroji z kteréhokoli z těchto umístění:

- Uvnitř kontejneru
- Z jiného kontejneru Docker běžícího na stejném hostiteli
- Z hostitelského počítače
- Ze všech ostatních klientských počítačů v síti

## <a name="tools-to-connect-to-azure-sql-edge"></a>Nástroje pro připojení k Edge SQL Azure

Můžete se připojit k instanci instance Azure SQL Edge z kteréhokoli z těchto běžných nástrojů:

* [Sqlcmd](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools): nástroje klienta Sqlcmd jsou již zahrnuty v imagi kontejneru Azure SQL Edge. Pokud se připojíte ke spuštěnému kontejneru pomocí interaktivního prostředí bash, můžete spouštět nástroje místně. Klientské nástroje SQL nejsou na platformě ARM64 k dispozici, protože nejsou zahrnuté ve verzi ARM64 kontejnerů SQL Edge. 
* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)

Pokud se chcete připojit k databázovému stroji Azure SQL Edge ze síťového počítače, potřebujete následující:

- **IP adresa nebo síťový název hostitelského počítače**: Toto je hostitelský počítač, na kterém je spuštěný kontejner Azure SQL Edge.
- **Mapování portů hostitele kontejneru Azure SQL Edge**: Jedná se o mapování portu kontejneru Docker na port v hostiteli. V rámci kontejneru je Azure SQL Edge vždycky namapovaný na port 1433. Tuto možnost můžete změnit, pokud chcete. Pokud chcete změnit číslo portu, aktualizujte **možnosti vytvoření kontejneru** pro modul Azure SQL Edge v Azure IoT Edge. V následujícím příkladu je port 1433 na kontejneru namapován na port 1600 na hostiteli.

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

- **Heslo SA pro instanci Azure SQL Edge**: Jedná se o hodnotu zadanou pro `SA_PASSWORD` proměnnou prostředí během nasazování Azure SQL Edge.

## <a name="connect-to-the-database-engine-from-within-the-container"></a>Připojení k databázovému stroji v rámci kontejneru

[Nástroje příkazového řádku SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) jsou zahrnuté v imagi kontejneru Azure SQL Edge. Pokud se k kontejneru připojíte pomocí interaktivního příkazového řádku, můžete spustit nástroje místně. Klientské nástroje SQL nejsou na platformě ARM64 k dispozici, protože nejsou zahrnuté ve verzi ARM64 kontejnerů SQL Edge. 

1. Pomocí `docker exec -it` příkazu spusťte interaktivní prostředí bash v běžícím kontejneru. V následujícím příkladu `e69e056c702d` je ID kontejneru.

    ```bash
    docker exec -it <Azure SQL Edge container ID or name> /bin/bash
    ```

    > [!TIP]
    > Nemusíte vždycky zadávat celé ID kontejneru. Stačí zadat dostatek znaků k jednoznačné identifikaci. Takže v tomto příkladu může být stačit použít `e6` nebo `e69` místo úplného ID.

2. Když jste uvnitř kontejneru, připojte se místně pomocí nástroje Sqlcmd. Nástroj Sqlcmd není ve výchozím nastavení v cestě, takže musíte zadat úplnou cestu.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. Až budete hotovi s nástrojem Sqlcmd, zadejte `exit` .

4. Až budete hotovi s interaktivním příkazovým řádkem, zadejte `exit` . Váš kontejner bude i nadále běžet po ukončení interaktivního prostředí bash.

## <a name="connect-to-azure-sql-edge-from-another-container-on-the-same-host"></a>Připojení k Edge Azure SQL z jiného kontejneru na stejném hostiteli

Vzhledem k tomu, že dva kontejnery, které jsou spuštěny na stejném hostiteli, jsou ve stejné síti Docker, můžete k nim snadno přistupovat pomocí názvu kontejneru a adresy portu pro službu. Pokud se například připojujete k instanci Azure SQL Edge z jiného modulu Pythonu (kontejneru) na stejném hostiteli, můžete použít připojovací řetězec podobný následujícímu. (Tento příklad předpokládá, že Azure SQL Edge je nakonfigurovaný tak, aby naslouchal na výchozím portu.)

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-azure-sql-edge-from-another-network-machine"></a>Připojení k Azure SQL Edge z jiného síťového počítače

Možná se budete chtít připojit k instanci Azure SQL Edge z jiného počítače v síti. Provedete to tak, že použijete IP adresu hostitele Docker a port hostitele, na který je kontejner Edge SQL Azure namapovaný. Pokud je například IP adresa hostitele Docker *xxx.xxx.xxx.xxx*a kontejner Azure SQL Edge je namapován na port hostitele *1600*, adresa serveru pro instanci Azure SQL Edge by byla *xxx. xxx. xxx. xxx, 1600*. Aktualizovaný skript jazyka Python:

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Pokud se chcete připojit k instanci Azure SQL Edge pomocí SQL Server Management Studio běžícího na počítači s Windows, přečtěte si téma [SQL Server Management Studio](https://docs.microsoft.com/sql/linux/sql-server-linux-manage-ssms).

Pokud se chcete připojit k instanci Azure SQL Edge pomocí Visual Studio Code na počítači se systémem Windows, Mac nebo Linux, přečtěte si téma [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode).

Pokud se chcete připojit k instanci Azure SQL Edge pomocí Azure Data Studio na počítači se systémem Windows, Mac nebo Linux, přečtěte si téma [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-server).

## <a name="next-steps"></a>Další kroky

[Připojení a dotazování](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Instalace SQL Server nástrojů v systému Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)
