---
title: 'Připojení k Datovým cihlám Azure z Excelu, Pythonu nebo R '
description: Přečtěte si, jak pomocí ovladače Simba připojit Azure Databricks k Excelu, Pythonu nebo R.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: f7494d36cf9b16ac6c7a1287a6ff96dd2285c6e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73601951"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Připojení k Datovým cihlám Azure z Excelu, Pythonu nebo R

V tomto článku se dozvíte, jak pomocí ovladače Databricks ODBC připojit Azure Databricks s Jazykem Microsoft Excel, Python nebo R. Jakmile navážete připojení, můžete získat přístup k datům v Azure Databricks z klientů Excel, Python nebo R. Můžete také použít klienty k další analýze dat. 

## <a name="prerequisites"></a>Požadavky

* Musíte mít pracovní prostor Azure Databricks, cluster Spark a ukázková data přidružená k vašemu clusteru. Pokud ještě nemáte tyto požadavky, dokončete rychlý start na [spuštění úlohy Spark na Azure Databricks pomocí portálu Azure](quickstart-create-databricks-workspace-portal.md).

* Stáhněte si ovladač Databricks ODBC ze [stránky pro stažení ovladače Databricks](https://databricks.com/spark/odbc-driver-download). Nainstalujte 32bitovou nebo 64bitovou verzi v závislosti na aplikaci, ze které se chcete připojit k Azure Databricks. Chcete-li se například připojit z aplikace Excel, nainstalujte 32bitovou verzi ovladače. Chcete-li se připojit z R a Pythonu, nainstalujte 64bitovou verzi ovladače.

* Nastavte osobní přístupový token v Databricks. Pokyny naleznete v tématu [Správa tokenů](/azure/databricks/dev-tools/api/latest/authentication).

## <a name="set-up-a-dsn"></a>Nastavení dsn

Název zdroje dat (DSN) obsahuje informace o konkrétním zdroji dat. Ovladač ROZHRANÍ ODBC potřebuje tento dsn pro připojení ke zdroji dat. V této části nastavíte DSN, který lze použít s ovladačem Databricks ODBC pro připojení k Azure Databricks z klientů, jako je Microsoft Excel, Python nebo R.

1. Z pracovního prostoru Azure Databricks přejděte do clusteru Databricks.

    ![Otevřít cluster Databricks](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Otevřít cluster Databricks")

2. Na kartě **Konfigurace** klikněte na kartu **JDBC/ODBC** a zkopírujte hodnoty pro **Název_hostitele serveru** a **Cestu HTTP**. Tyto hodnoty potřebujete k dokončení kroků v tomto článku.

    ![Získat konfiguraci Databricks](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Získat konfiguraci Databricks")

3. V počítači spusťte aplikaci **ODBC Data Sources** (32bitová nebo 64bitová) v závislosti na aplikaci. Chcete-li se připojit z Excelu, použijte 32bitovou verzi. Chcete-li se připojit z R a Pythonu, použijte 64bitovou verzi.

    ![Spuštění rozhraní ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "Spuštění aplikace ODBC")

4. Na kartě **User DSN** klikněte na **Add**. V dialogovém okně **Vytvořit nový zdroj dat** vyberte ovladač **ODBC aplikace Simba Spark**a klepněte na tlačítko **Dokončit**.

    ![Spuštění rozhraní ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "Spuštění aplikace ODBC")

5. V dialogovém okně **Ovladač OdBC Simba Spark** zadejte následující hodnoty:

    ![Konfigurace dsn](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "Konfigurace dsn")

    V následující tabulce jsou uvedeny informace o hodnotách, které mají být uvedeny v dialogovém okně.
    
    |Pole  | Hodnota  |
    |---------|---------|
    |**Název zdroje dat**     | Zadejte název zdroje dat.        |
    |**Hostitelé**     | Zadejte hodnotu, kterou jste zkopírovali z pracovního prostoru Databricks pro *název_hostitele serveru*.        |
    |**Port**     | Zadejte *443*.        |
    |**Mechanismus ověřování** > **Mechanism**     | Vyberte *uživatelské jméno a heslo*.        |
    |**Uživatelské jméno**     | Zadejte *token*.        |
    |**Heslo**     | Zadejte hodnotu tokenu, kterou jste zkopírovali z pracovního prostoru Databricks. |
    
    V dialogovém okně nastavení dsn proveďte následující další kroky.
    
    * Klepněte na **položku Možnosti protokolu HTTP**. V dialogovém okně, které se otevře, vložte hodnotu *cesty HTTP,* kterou jste zkopírovali z pracovního prostoru Databricks. Klikněte na tlačítko **OK**.
    * Klepněte na **položku Možnosti ssl**. V dialogovém okně, které se otevře, zaškrtněte políčko **Povolit ssl.** Klikněte na tlačítko **OK**.
    * Kliknutím na **Test** otestujte připojení k Azure Databricks. Chcete-li uložit konfiguraci, klepněte na tlačítko **OK.**
    * V dialogovém okně **Správce zdroje dat rozhraní ODBC** klepněte na tlačítko **OK**.

Nyní máte nastavenou dsn. V dalších částech se pomocí tohoto dsn připojit k Azure Databricks z Excelu, Pythonu nebo R.

## <a name="connect-from-microsoft-excel"></a>Připojení z aplikace Microsoft Excel

V této části najdete data z Azure Databricks do Microsoft Excelu pomocí DSN, které jste vytvořili dříve. Než začnete, ujistěte se, že máte v počítači nainstalovanou aplikaci Microsoft Excel. Zkušební verzi aplikace Excel můžete použít z [odkazu pro zkušební verzi aplikace Microsoft Excel](https://products.office.com/excel).

1. Otevřete prázdný sešit v aplikaci Microsoft Excel. Na pásu **karet Data** klikněte na **Získat data**. Klepněte na **položku Z jiných zdrojů** a potom na **položku OdBC**.

    ![Spuštění rozhraní ODBC z Aplikace Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Spuštění rozhraní ODBC z Aplikace Excel")

2. V dialogovém okně **Z rozhraní ODBC** vyberte dsn, který jste vytvořili dříve, a klepněte na tlačítko **OK**.

    ![Vybrat DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Vybrat DSN")

3. Pokud se zobrazí výzva k zadání pověření, zadejte **token**uživatelského jména . Pro heslo zadejte hodnotu tokenu, kterou jste načetli z pracovního prostoru Databricks.

    ![Zadejte pověření pro Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "Vybrat DSN")

4. V okně navigátoru vyberte tabulku v položkách Databricks, kterou chcete načíst do Excelu, a klepněte na tlačítko **Načíst**. 

    ![Načtení dta do aplikace Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "Načtení dta do aplikace Excel")

Jakmile budete mít data v sešitu aplikace Excel, můžete s ním provádět analytické operace.

## <a name="connect-from-r"></a>Připojit z R

> [!NOTE]
> Tato část obsahuje informace o tom, jak integrovat klienta R Studio spuštěného na ploše s Azure Databricks. Pokyny, jak používat R Studio v samotném clusteru Azure Databricks, najdete v [tématu R Studio na Azure Databricks](/azure/databricks/spark/latest/sparkr/rstudio).

V této části použijete rozhraní IDE jazyka R k odkazování na data dostupná v Azure Databricks. Než začnete, musíte mít v počítači nainstalovány následující informace.

* Ide pro jazyk R. Tento článek používá RStudio pro stolní počítače. Můžete jej nainstalovat z [R Studio ke stažení](https://www.rstudio.com/products/rstudio/download/).
* Pokud používáte RStudio pro stolní počítače jako ide, nainstalujte také Microsoft R Client z [https://aka.ms/rclient/](https://aka.ms/rclient/). 

Otevřete RStudio a proveďte následující kroky:

- Odkaz `RODBC` na balíček. To vám umožní připojit se k Azure Databricks pomocí DSN, které jste vytvořili dříve.
- Navázat připojení pomocí DSN.
- Spusťte dotaz SQL na data v Azure Databricks. V následujícím fragmentu *je radio_sample_data* tabulka, která už v Azure Databricks existuje.
- Proveďte některé operace na dotaz k ověření výstupu. 

Následující fragment kódu provádí tyto úkoly:

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

V této části použijete IDE Pythonu (například IDLE) k odkazování na data dostupná v Azure Databricks. Než začnete, vyplňte následující požadavky:

* Nainstalujte Python [odtud](https://www.python.org/downloads/). Instalace Pythonu z tohoto odkazu také nainstaluje IDLE.

* Z příkazového řádku v počítači nainstalujte balíček. `pyodbc` Spusťte následující příkaz:

      pip install pyodbc

Otevřete IDLE a proveďte následující kroky:

- Importujte `pyodbc` balíček. To vám umožní připojit se k Azure Databricks pomocí DSN, které jste vytvořili dříve.
- Navázat připojení pomocí DSN, které jste vytvořili dříve.
-  Spusťte dotaz SQL pomocí vytvořeného připojení. V následujícím fragmentu *je radio_sample_data* tabulka, která už v Azure Databricks existuje.
- Proveďte operace s dotazem k ověření výstupu.

Následující fragment kódu provádí tyto úkoly:

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

* Další informace o zdrojích, odkud můžete importovat data do Azure Databricks, najdete [v tématu Zdroje dat pro Azure Databricks](/azure/databricks/data/data-sources/index)


