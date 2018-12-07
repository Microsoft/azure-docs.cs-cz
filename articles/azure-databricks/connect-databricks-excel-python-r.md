---
title: 'Připojení k Azure Databricks z Excelu, Python nebo R '
description: Zjistěte, jak použít ovladač Simba pro připojení Azure Databricks do aplikace Excel, Python nebo R.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: c57550a8b683ad8f184884374c4f09216417fc40
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52995621"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Připojení k Azure Databricks z Excelu, Python nebo R

V tomto článku se dozvíte, jak použít ovladač Databricks ODBC pro připojení k Azure Databricks pomocí aplikace Microsoft Excel, Python nebo R jazyka. Po vytvoření připojení můžete přístup k datům v Azure Databricks od klientů aplikace Excel, Python nebo R. Klienty můžete použít také pokud chcete hlouběji analyzovat data. 

## <a name="prerequisites"></a>Požadavky

* Musíte mít pracovní prostor služby Azure Databricks, Spark cluster a ukázková data spojená s vaším clusterem. Pokud již nemáte tyto požadavky, projděte si rychlý start na [spuštění úlohy Spark job v Azure Databricks pomocí webu Azure portal](quickstart-create-databricks-workspace-portal.md).

* Stáhnout ovladač Databricks ODBC z [stránka pro stahování ovladačů Databricks](https://databricks.com/spark/odbc-driver-download). 32bitové nebo 64bitové verze v závislosti na aplikaci nainstalujte z ve které chcete připojit k Azure Databricks. Například pro připojení z aplikace Excel, instalaci 32bitové verze ovladače. Pro připojení z jazyků R a Python, nainstalujte 64bitovou verzi ovladače.

* Nastavte osobní přístupový token v Databricks. Pokyny najdete v tématu [Token správu](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="set-up-a-dsn"></a>Nastavení názvu DSN

Název zdroje dat (DSN) obsahuje informace o konkrétní zdroj. Ovladač ODBC musí tento název zdroje dat pro připojení ke zdroji dat. V této části nastavíte názvu DSN, který je možné pomocí ovladače Databricks ODBC pro připojení k Azure Databricks od klientů, jako je Microsoft Excel, Python nebo R.

1. V pracovním prostoru Azure Databricks přejděte ke clusteru Databricks.

    ![Otevřít Databricks pro cluster](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "otevřít Databricks pro cluster")

2. V části **konfigurace** klikněte na tlačítko **JDBC/ODBC** kartu a zkopírujte hodnoty **název hostitele serveru** a **cesta HTTP**. Tyto hodnoty k dokončení kroků v tomto článku budete potřebovat.

    ![Získat konfiguraci Databricks](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Databricks získat konfiguraci")

3. Na počítači, spusťte **zdroje dat ODBC** aplikace (32bitová nebo 64bitová verze) v závislosti na aplikaci. Pro připojení z Excelu, použijte 32bitovou verzi. Pro připojení z jazyků R a Python, použijte 64bitovou verzi.

    ![Spuštění rozhraní ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "spuštění aplikace rozhraní ODBC")

4. V části **uživatelské DSN** klikněte na tlačítko **přidat**. V **vytvořit nový zdroj dat** dialogové okno, vyberte **ovladač ODBC Spark Simba**a potom klikněte na tlačítko **Dokončit**.

    ![Spuštění rozhraní ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "spuštění aplikace rozhraní ODBC")

5. V **ovladač ODBC Spark Simba** dialogového okna zadejte následující hodnoty:

    ![Konfigurace názvu DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "konfigurace DSN")

    Následující tabulka obsahuje informace na tyto hodnoty zadat v dialogovém okně.
    
    |Pole  | Hodnota  |
    |---------|---------|
    |**Název zdroje dat**     | Zadejte název datového zdroje.        |
    |**Hostitele**     | Zadejte hodnotu, kterou jste zkopírovali z pracovního prostoru Databricks pro *název hostitele serveru*.        |
    |**Port**     | Zadejte *443*.        |
    |**Ověřování** > **mechanismus**     | Vyberte *uživatelské jméno a heslo*.        |
    |**Uživatelské jméno**     | Zadejte *token*.        |
    |**Heslo**     | Zadejte hodnotu tokenu, který jste zkopírovali z pracovního prostoru Databricks. |
    
    V dialogovém okně Nastavení názvu zdroje dat proveďte následující kroky.
    
    * Klikněte na tlačítko **HTTP Options**. V dialogovém okně, které se otevře, vložte tuto hodnotu pro *cesta HTTP* , který jste zkopírovali z pracovního prostoru Databricks. Klikněte na **OK**.
    * Klikněte na tlačítko **možnosti SSL**. V dialogovém okně, které se otevře, vyberte **povolit šifrování SSL** zaškrtávací políčko. Klikněte na **OK**.
    * Klikněte na tlačítko **testování** chcete otestovat připojení k Azure Databricks. Nastavení uložíte kliknutím na **OK**.
    * V **správce zdrojů dat ODBC** dialogové okno, klikněte na tlačítko **OK**.

Teď máte vaše DSN nastavení. V následujících částech použijte tento název zdroje dat pro připojení k Azure Databricks z aplikace Excel, Python nebo R.

## <a name="connect-from-microsoft-excel"></a>Připojení z aplikace Microsoft Excel

V této části si vyžádat data z Azure Databricks do aplikace Microsoft Excel pomocí DSN, který jste vytvořili dříve. Než začnete, ujistěte se, že máte v počítači nainstalovanou aplikaci Microsoft Excel. Můžete použít zkušební verzi aplikace Excel z [odkaz zkušební verze Microsoft Excelu](https://products.office.com/excel).

1. Prázdný sešit otevřete v aplikaci Microsoft Excel. Z **Data** pásu karet, klikněte na tlačítko **získat Data**. Klikněte na tlačítko **z jiných zdrojů** a potom klikněte na tlačítko **z rozhraní ODBC**.

    ![Spuštění rozhraní ODBC z aplikace Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "spuštění ODBC z Excelu")

2. V **z rozhraní ODBC** dialogové okno, vyberte název zdroje dat, který jste vytvořili dříve a pak klikněte na tlačítko **OK**.

    ![Vyberte název zdroje dat](./media/connect-databricks-excel-python-r/excel-select-dsn.png "vyberte název zdroje dat")

3. Pokud se výzva k zadání přihlašovacích údajů, uživatelské jméno zadejte **token**. Zadání hesla zadejte hodnotu tokenu, který jste získali z pracovního prostoru Databricks.

    ![Zadejte přihlašovací údaje pro Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "vyberte název zdroje dat")

4. V okně Navigátor vyberte tabulku, ve službě Databricks, který chcete načíst do Excelu a potom klikněte na **načíst**. 

    ![Načtení do aplikace Excel dta](./media/connect-databricks-excel-python-r/excel-load-data.png "dta načíst do Excelu")

Jakmile budete mít data v Excelovém sešitu, můžete provádět analytičtější operace v něm.

## <a name="connect-from-r"></a>Připojení z jazyka R

> [!NOTE]
> Tato část obsahuje informace o tom, jak integrovat klientem R Studio spuštěná v počítači s Azure Databricks. Pokyny, jak používat R Studio v Azure Databricks pro cluster sám najdete v tématu [R Studio v Azure Databricks](https://docs.azuredatabricks.net/spark/latest/sparkr/rstudio.html).

V této části pomocí jazyka R integrované vývojové prostředí s referenčními daty, které jsou k dispozici v Azure Databricks. Než začnete, musíte mít nainstalovaný v počítači.

* Integrované vývojové prostředí jazyka R. Tento článek používá RStudio Desktop. Můžete nainstalovat z [stažení sady R Studio](https://www.rstudio.com/products/rstudio/download/).
* Pokud používáte přihlašovací stránce RStudio pro Desktop jako prostředí (IDE), také nainstalovat Microsoft R Client z [ https://aka.ms/rclient/ ](https://aka.ms/rclient/). 

Otevřete RStudio a proveďte následující kroky:

- Odkaz `RODBC` balíčku. To umožňuje připojení k Azure Databricks pomocí DSN, který jste vytvořili dříve.
- Připojení pomocí DSN.
- Spusťte dotaz SQL na data v Azure Databricks. V následujícím fragmentu kódu *radio_sample_data* je tabulka, která již existuje v Azure Databricks.
- Provádění některých operací u dotazu k ověření výstupu. 

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

V této části použijete Python IDE (například IDLE) s referenčními daty, které jsou k dispozici v Azure Databricks. Než začnete, zajistěte splnění následujících požadavků:

* Instalace Pythonu z [tady](https://www.python.org/downloads/). Instalace Pythonu z tohoto odkazu se nainstaluje taky nečinnosti.

* Z příkazového řádku na počítači, nainstalujte `pyodbc` balíčku. Spusťte následující příkaz:

      pip install pyodbc

Otevřete nečinný a proveďte následující kroky:

- Import `pyodbc` balíčku. To umožňuje připojení k Azure Databricks pomocí DSN, který jste vytvořili dříve.
- Připojení pomocí DSN, který jste vytvořili dříve.
-  Spustíte dotaz SQL pomocí připojení, že kterou jste vytvořili. V následujícím fragmentu kódu *radio_sample_data* je tabulka, která již existuje v Azure Databricks.
- Provádění operací na dotaz k ověření výstupu.

Následující fragment kódu provádí tyto úlohy:

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit = True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)

```

## <a name="next-steps"></a>Další postup

* Další informace o zdrojích ve kterém můžete importovat data do Azure Databricks najdete v tématu [zdroje dat pro Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#)


