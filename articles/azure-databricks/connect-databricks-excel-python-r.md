---
title: Připojit k Azure Databricks z aplikace Excel, Python nebo R | Microsoft Docs
description: Další informace o použití Simba ovladač pro připojení k Excelu, Python nebo R. Azure Databricks
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: 333ff3ac3de053eae604ffeab600df7d35874f69
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085228"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Připojit k Azure Databricks z aplikace Excel, Python nebo R

V tomto článku zjistěte, jak používat ovladač Databricks ODBC pro připojení s jazykem Microsoft Excel, Python nebo R Azure Databricks. Po vytvoření připojení můžete přístupu k datům v Azure Databricks z Excelu, Python nebo R klientů. Můžete taky klienty pro další analýzu data. 

## <a name="prerequisites"></a>Požadavky

* Musíte mít pracovní prostor služby Azure Databricks, Spark cluster a ukázkových dat přidruženého k vašemu clusteru. Pokud již nemáte tyto požadavky, proveďte rychlý start v [spustit úlohu Spark v Azure Databricks pomocí portálu Azure](quickstart-create-databricks-workspace-portal.md).

* Stáhnout ovladač Databricks ODBC z [stránky pro stažení ovladačů Databricks](https://databricks.com/spark/odbc-driver-download). Nainstalujte 32bitovou nebo 64bitovou verzi v závislosti na aplikaci z ve které chcete připojit k Azure Databricks. Například pro připojení z aplikace Excel, nainstalujte 32bitovou verzi ovladače. Pro připojení z R a Python, nainstalujte 64bitovou verzi ovladače.

* Nastavte osobní přístupový token v Databricks. Pokyny najdete v tématu [Token správu](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="set-up-a-dsn"></a>Nastavení názvu DSN

Název zdroje dat (DSN) obsahuje informace o konkrétní zdroj dat. Ovladač ODBC musí tento název zdroje dat pro připojení ke zdroji dat. V této části nastavíte názvu DSN, který lze použít pomocí ovladače Databricks ODBC pro připojení k Azure Databricks od klientů, jako je Microsoft Excel, Python nebo R.

1. V pracovním prostoru Azure Databricks přejděte na Databricks clusteru.

    ![Otevřete cluster Databricks](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "otevřete Databricks clusteru")

2. V části **konfigurace** , klikněte na **JDBC nebo ODBC** kartě a zkopírujte hodnoty **název hostitele serveru** a **cesta HTTP**. Je nutné tyto hodnoty, pokud chcete provést kroky v tomto článku.

    ![Získání konfigurace Databricks](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "získat Databricks konfigurace")

3. V počítači, spusťte **zdroje dat ODBC** aplikace (32bitová nebo 64bitová verze) v závislosti na aplikaci. Chcete-li připojit z Excelu, použijte 32bitová verze. Chcete-li připojit z R a Python, použijte 64bitovou verzi.

    ![Spuštění rozhraní ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "spuštění aplikace rozhraní ODBC")

4. V části **uživatelské DSN** , klikněte na **přidat**. V **vytvořit nový zdroj dat** dialogové okno, vyberte **ovladač ODBC Spark Simba**a potom klikněte na **Dokončit**.

    ![Spuštění rozhraní ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "spuštění aplikace rozhraní ODBC")

5. V **ovladač ODBC Spark Simba** dialogovém okně zadejte následující hodnoty:

    ![Konfigurace názvu DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "konfigurace DSN")

    Následující tabulka obsahuje informace o hodnoty, které poskytují v dialogovém okně.
    
    |Pole  | Hodnota  |
    |---------|---------|
    |**Název zdroje dat**     | Zadejte název pro zdroj dat.        |
    |**Hostitel**     | Zadejte hodnotu, kterou jste zkopírovali z pracovního prostoru Databricks pro *název hostitele serveru*.        |
    |**Port**     | Zadejte *443*.        |
    |**Ověřování** > **mechanismus**     | Vyberte *uživatelské jméno a heslo*.        |
    |**Uživatelské jméno**     | Zadejte *tokenu*.        |
    |**Heslo**     | Zadejte hodnotu tokenu, který jste zkopírovali z pracovního prostoru Databricks. |
    
    V dialogovém okně Nastavení názvu DSN, proveďte následující kroky.
    
    * Klikněte na tlačítko **HTTP Options**. V dialogovém okně, které se otevře, vložit hodnotu pro *cesta HTTP* který jste zkopírovali z pracovního prostoru Databricks. Klikněte na **OK**.
    * Klikněte na tlačítko **SSL možnosti**. V dialogovém okně, které se otevře, vyberte **povolit šifrování SSL** zaškrtávací políčko. Klikněte na **OK**.
    * Klikněte na tlačítko **testování** k testování připojení k Azure Databricks. Nastavení uložíte kliknutím na **OK**.
    * V **správce zdrojů dat ODBC** dialogové okno, klikněte na tlačítko **OK**.

Nyní máte vaší DSN nastavit. V následujících částech použijte tento název zdroje dat pro připojení k Azure Databricks z aplikace Excel, Python nebo R.

## <a name="connect-from-microsoft-excel"></a>Připojení z aplikace Microsoft Excel

V této části můžete získání dat z Azure Databricks do aplikace Microsoft Excel pomocí DSN, který jste vytvořili dříve. Než začnete, ujistěte se, že máte v počítači nainstalována aplikace Microsoft Excel. Můžete použít zkušební verzi aplikace Excel z [odkaz zkušební verze Microsoft Excelu](https://products.office.com/excel).

1. Otevření prázdné sešitu v aplikaci Microsoft Excel. Z **Data** pásu karet, klikněte na tlačítko **načíst Data**. Klikněte na tlačítko **z jiných zdrojů** a pak klikněte na **z rozhraní ODBC**.

    ![Spuštění rozhraní ODBC z Excelu](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "spusťte ODBC z Excelu")

2. V **z rozhraní ODBC** dialogové okno, vyberte názvu DSN, který jste dříve vytvořili a pak klikněte na tlačítko **OK**.

    ![Vyberte název zdroje dat](./media/connect-databricks-excel-python-r/excel-select-dsn.png "vyberte název zdroje dat")

3. Pokud budete vyzváni k zadání pověření pro uživatelské jméno zadejte **tokenu**. Heslo zadejte hodnotu tokenu, které jste získali z pracovního prostoru Databricks.

    ![Zadejte přihlašovací údaje pro Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "vyberte název zdroje dat")

4. Z okna Navigátor vyberte tabulku v Databricks, který chcete načíst do aplikace Excel a pak klikněte na tlačítko **načíst**. 

    ![Načtení dta do aplikace Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "dta zatížení do aplikace Excel")

Až budete mít data v sešitu aplikace Excel, analytické operace na něm lze provádět.

## <a name="connect-from-r"></a>Připojení z R

> [!NOTE]
> Tato část obsahuje informace o tom, jak integrovat klientem R Studio spuštěná na ploše s Azure Databricks. Pokyny k použití na samotný cluster Azure Databricks R Studio najdete v tématu [Studio R na Azure Databricks](https://docs.azuredatabricks.net/spark/latest/sparkr/rstudio.html).

V této části použijte jazyk R IDE pro referenční data k dispozici v Azure Databricks. Než začnete, musíte mít následující v počítači nainstalována.

* Rozhraní IDE pro jazyk R. Tento článek používá Rstudia pro plochu. Můžete nainstalovat z [stažení R Studio](https://www.rstudio.com/products/rstudio/download/).
* Pokud používáte Rstudia pro plochu jako vaše IDE, také nainstalovat klienta Microsoft R [ http://aka.ms/rclient/ ](http://aka.ms/rclient/). 

Otevřete Rstudia a proveďte následující kroky:

- Odkaz `RODBC` balíčku. Můžete se připojit k Azure Databricks pomocí DSN, který jste vytvořili dříve.
- Připojení pomocí DSN.
- Spusťte dotaz SQL na datech v Azure Databricks. V následujícím fragmentu kódu *radio_sample_data* je tabulku, která již existuje v Azure Databricks.
- Provádění některých operací na dotaz pro ověření výstup. 

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

V této části použijte Python IDE (například nečinnosti) pro referenční data k dispozici v Azure Databricks. Než začnete, splnit následující požadavky:

* Nainstalujte Python z [zde](https://www.python.org/downloads/). Instalaci Python z tohoto odkazu se nainstaluje také nečinnosti.

* Z příkazového řádku na počítači, nainstalujte `pyodbc` balíčku. Spusťte následující příkaz:

      pip install pyodbc

Otevřete nečinnosti a proveďte následující kroky:

- Import `pyodbc` balíčku. Můžete se připojit k Azure Databricks pomocí DSN, který jste vytvořili dříve.
- Připojení pomocí DSN, který jste vytvořili dříve.
-  Spusťte dotaz SQL pomocí připojení, že kterou jste vytvořili. V následujícím fragmentu kódu *radio_sample_data* je tabulku, která již existuje v Azure Databricks.
- Provádění operací na dotaz pro ověření výstup.

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

* Další informace o zdroji, ze kterých můžete importovat data do Azure Databricks najdete v tématu [zdroje dat pro Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#)


