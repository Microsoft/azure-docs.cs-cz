---
title: 'Připojení k Azure Databricks z Excelu, Pythonu nebo R '
description: Naučte se, jak pomocí ovladače Simba připojit Azure Databricks k Excelu, Pythonu nebo R.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: f7494d36cf9b16ac6c7a1287a6ff96dd2285c6e2
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73601951"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Připojení k Azure Databricks z Excelu, Pythonu nebo R

V tomto článku se dozvíte, jak pomocí ovladače ODBC datacihly připojit Azure Databricks k jazyku Microsoft Excelu, Pythonu nebo R. Po navázání připojení můžete k datům v Azure Databricks přistupovat z klientů Excel, Python nebo R. K další analýze dat můžete také použít klienty. 

## <a name="prerequisites"></a>Požadavky

* Musíte mít Azure Databricks pracovní prostor, cluster Spark a ukázková data přidružená k vašemu clusteru. Pokud tyto požadavky ještě nemáte, dokončete rychlý Start v článku [spuštění úlohy Spark na Azure Databricks pomocí Azure Portal](quickstart-create-databricks-workspace-portal.md).

* Stáhněte si ovladač ODBC ovladače datacihly ze [stránky pro stažení ovladače datacihly](https://databricks.com/spark/odbc-driver-download). Nainstalujte 32 nebo 64 bitovou verzi v závislosti na aplikaci, ze které se chcete připojit k Azure Databricks. Například pro připojení z aplikace Excel nainstalujte 32 verzi ovladače. Pokud se chcete připojit z R a Pythonu, nainstalujte 64 verzi ovladače.

* Nastavení osobního přístupového tokenu v datacihlech Pokyny najdete v tématu [Správa tokenů](/azure/databricks/dev-tools/api/latest/authentication).

## <a name="set-up-a-dsn"></a>Nastavení názvu DSN

Název zdroje dat (DSN) obsahuje informace o konkrétním zdroji dat. Ovladač ODBC potřebuje tento název DSN pro připojení ke zdroji dat. V této části nastavíte název DSN, který se dá použít s ovladačem ODBC datacihly pro připojení k Azure Databricks z klientů, jako je Microsoft Excel, Python nebo R.

1. V pracovním prostoru Azure Databricks přejděte do clusteru datacihly.

    ![Otevřít cluster datacihly](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Otevřít cluster datacihly")

2. Na kartě **Konfigurace** klikněte na kartu **JDBC/ODBC** a zkopírujte hodnoty pro **název hostitele serveru** a **cestu http**. Tyto hodnoty budete potřebovat k dokončení kroků v tomto článku.

    ![Získat konfiguraci datacihlů](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Získat konfiguraci datacihlů")

3. V počítači spusťte v závislosti na aplikaci aplikace **zdroje dat ODBC** (32-bit nebo 64-bit). K připojení z Excelu použijte 32 verzi. Pokud se chcete připojit z R a Pythonu, použijte 64 verzi.

    ![Spustit rozhraní ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "Spuštění aplikace ODBC")

4. Na kartě **uživatelský název DSN** klikněte na **Přidat**. V dialogovém okně **vytvořit nový zdroj dat** vyberte **ovladač Simba Spark ODBC**a pak klikněte na **Dokončit**.

    ![Spustit rozhraní ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "Spuštění aplikace ODBC")

5. V dialogovém okně **ovladač Simba Spark ODBC** zadejte následující hodnoty:

    ![Konfigurace DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "Konfigurace DSN")

    Následující tabulka obsahuje informace o hodnotách, které mají být uvedeny v dialogovém okně.
    
    |Pole  | Hodnota  |
    |---------|---------|
    |**Název zdroje dat**     | Zadejte název zdroje dat.        |
    |**Hostitelé:**     | Zadejte hodnotu, kterou jste zkopírovali z pracovního prostoru datacihly pro *název hostitele serveru*.        |
    |**Port**     | Zadejte *443*.        |
    |**Ověřovací** > **mechanismus**     | Vyberte *uživatelské jméno a heslo*.        |
    |**Uživatelské jméno**     | Zadejte *token*.        |
    |**Heslo**     | Zadejte hodnotu tokenu, kterou jste zkopírovali z pracovního prostoru datacihly. |
    
    V dialogovém okně nastavení DSN proveďte následující další kroky.
    
    * Klikněte na možnost **http možnosti**. V dialogovém okně, které se otevře, vložte hodnotu pro *cestu http* , kterou jste zkopírovali z pracovního prostoru datacihly. Klikněte na tlačítko **OK**.
    * Klikněte na **Možnosti SSL**. V dialogu, který se otevře, zaškrtněte políčko **Povolit protokol SSL** . Klikněte na tlačítko **OK**.
    * Kliknutím na **test** otestujte připojení k Azure Databricks. Konfiguraci uložíte kliknutím na **OK** .
    * V dialogovém okně **Správce zdrojů dat ODBC** klikněte na tlačítko **OK**.

Teď máte nastavený název DSN. V dalších částech se tento název DSN používá pro připojení k Azure Databricks z Excelu, Pythonu nebo R.

## <a name="connect-from-microsoft-excel"></a>Připojit z aplikace Microsoft Excel

V této části vyžádáte data z Azure Databricks do Microsoft Excelu pomocí DSN, který jste vytvořili dříve. Než začnete, ujistěte se, že máte v počítači nainstalovanou aplikaci Microsoft Excel. Můžete použít zkušební verzi aplikace Excel z [zkušebního připojení aplikace Microsoft Excel](https://products.office.com/excel).

1. Otevřete prázdný sešit v aplikaci Microsoft Excel. Na pásu karet **data** klikněte na **získat data**. Klikněte na **z jiných zdrojů** a pak klikněte na **z ODBC**.

    ![Spuštění ODBC z Excelu](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Spuštění ODBC z Excelu")

2. V dialogovém okně **z ODBC** vyberte název DSN, který jste vytvořili dříve, a pak klikněte na **OK**.

    ![Vybrat DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Vybrat DSN")

3. Pokud se zobrazí výzva k zadání přihlašovacích údajů, zadejte do tohoto uživatelského jména **token**. Pro heslo zadejte hodnotu tokenu, kterou jste získali z pracovního prostoru datacihly.

    ![Zadání přihlašovacích údajů pro datacihly](./media/connect-databricks-excel-python-r/excel-databricks-token.png "Vybrat DSN")

4. V okně navigátor vyberte tabulku v datacihlách, kterou chcete načíst do Excelu, a pak klikněte na **načíst**. 

    ![Načíst do Excelu odložených dolů](./media/connect-databricks-excel-python-r/excel-load-data.png "Načíst do Excelu odložených dolů")

Jakmile budete mít data v excelovém sešitu, můžete na něm provádět analytické operace.

## <a name="connect-from-r"></a>Připojit z R

> [!NOTE]
> V této části najdete informace o tom, jak integrovat klienta R studia běžícího na ploše s Azure Databricks. Pokyny k použití R studia na samotném Azure Databricks clusteru najdete v tématu [R Studio v Azure Databricks](/azure/databricks/spark/latest/sparkr/rstudio).

V této části použijete prostředí IDE jazyka R k odkazování na data, která jsou k dispozici v Azure Databricks. Než začnete, musíte mít na počítači nainstalovanou následující.

* Rozhraní IDE pro jazyk R. Tento článek používá RStudio pro Desktop. Můžete ji nainstalovat z [webu R Studio Download](https://www.rstudio.com/products/rstudio/download/).
* Pokud používáte RStudio pro desktop jako integrované vývojové prostředí (IDE), nainstalujte také Microsoft R Client z [https://aka.ms/rclient/](https://aka.ms/rclient/). 

Otevřete RStudio a proveďte následující kroky:

- Odkázat na balíček `RODBC`. To vám umožní připojit se k Azure Databricks pomocí DSN, který jste vytvořili dříve.
- Navažte připojení pomocí DSN.
- Spusťte dotaz SQL na datech v Azure Databricks. V následujícím fragmentu kódu je *radio_sample_data* tabulka, která již existuje v Azure Databricks.
- Provedením některých operací s dotazem ověřte výstup. 

Následující fragment kódu provádí tyto úlohy:

    # reference the 'RODBC' package
    require(RODBC)
    
    # establish a connection using the DSN you created earlier
    conn <- odbcConnect("<ENTER DSN NAME HERE>")
    
    # run a SQL query using the connection you created
    res <- sqlQuery(conn, "SELECT * FROM radio_sample_data")
    
    # print out the column names in the query output
    names(res) 
        
    # print out the number of rows in the query output
    nrow (res)

## <a name="connect-from-python"></a>Připojení z Pythonu

V této části použijete prostředí Python IDE (například nečinné) k odkazování na data dostupná v Azure Databricks. Než začnete, dokončete následující požadavky:

* Nainstalujte Python odsud [.](https://www.python.org/downloads/) Instalace Pythonu z tohoto odkazu nainstaluje taky nečinné.

* Z příkazového řádku v počítači nainstalujte balíček `pyodbc`. Spusťte následující příkaz:

      pip install pyodbc

Otevřete nečinné a proveďte následující kroky:

- Importujte balíček `pyodbc`. To vám umožní připojit se k Azure Databricks pomocí DSN, který jste vytvořili dříve.
- Navažte připojení pomocí DSN, který jste vytvořili dříve.
-  Spusťte dotaz SQL pomocí připojení, které jste vytvořili. V následujícím fragmentu kódu je *radio_sample_data* tabulka, která již existuje v Azure Databricks.
- Provede operace s dotazem k ověření výstupu.

Následující fragment kódu provádí tyto úlohy:

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit=True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)
```

## <a name="next-steps"></a>Další kroky

* Další informace o zdrojích, ze kterých můžete importovat data do Azure Databricks, najdete v tématu [zdroje dat pro Azure Databricks](/azure/databricks/data/data-sources/index)


