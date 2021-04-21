---
title: Poznámkové bloky synapse Studio
description: V tomto článku se dozvíte, jak vytvořit a vyvíjet notebooky Azure synapse Studio, které umožňují přípravu a vizualizaci dat.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/19/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: c28bc47945882e6b7bbd39d1ba8524a1f7491ceb
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835198"
---
# <a name="create-develop-and-maintain-synapse-studio-notebooks-in-azure-synapse-analytics"></a>Vytváření, vývoj a údržba poznámkových bloků synapse Studio v Azure synapse Analytics

Poznámkový blok synapse Studio je webové rozhraní, které umožňuje vytvářet soubory, které obsahují živý kód, vizualizace a mluvený text. Poznámkové bloky jsou vhodné místo pro ověřování nápadů a k získání přehledu z vašich dat můžete využít rychlé experimenty. Poznámkové bloky se také často používají při přípravě dat, vizualizaci dat, strojovém učení a dalších scénářích s velkými objemy dat.

Pomocí poznámkového bloku Azure synapse Studio můžete:

* Začněte s nulovým úsilím nastavení.
* Zajistěte zabezpečení dat pomocí integrovaných funkcí podnikového zabezpečení.
* Analyzujte data napříč nezpracovanými formáty (CSV, txt, JSON atd.), zpracovanými formáty souborů (Parquet, rozdílových Lake, ORC atd.) a SQL tabelárních datových souborů pro Spark a SQL.
* Získejte produktivitu s využitím vylepšených možností vytváření a integrovaných vizualizací dat.

Tento článek popisuje, jak používat notebooky v Azure synapse Studio.

## <a name="preview-of-the-new-notebook-experience"></a>Náhled nového prostředí poznámkového bloku
Synapse tým přenesl novou součást poznámkových bloků do synapse studia, aby poskytoval konzistentní prostředí poznámkového bloku pro zákazníky Microsoftu a maximalizoval možnosti zjistitelnosti, produktivity, sdílení a spolupráce. Nové prostředí poznámkového bloku je připravené na verzi Preview. Pokud ho chcete zapnout, podívejte se na tlačítko **funkce ve verzi Preview** na panelu nástrojů Poznámkový blok. Následující tabulka zachycuje porovnání funkcí mezi existujícím poznámkovým blokem (označovaným jako "klasický notebook") s novou verzí Preview.  

|Funkce|Klasický notebook|Náhled poznámkového bloku|
|--|--|--|
|% běh| Nepodporováno | &#9745;|
|% Historie| Nepodporováno |&#9745;
|% zatížení| Nepodporováno |&#9745;|
|%% HTML| Nepodporováno |&#9745;|
|Přesunutí buňky přetažením myší| Nepodporováno |&#9745;|
|Formátování textové buňky pomocí tlačítek panelu nástrojů|&#9745;| Není k dispozici |
|Operace vrácení buňky zpět| &#9745;| Není k dispozici |


## <a name="create-a-notebook"></a>Vytvoření poznámkového bloku

Existují dva způsoby, jak vytvořit Poznámkový blok. Můžete vytvořit nový Poznámkový blok nebo importovat existující Poznámkový blok do pracovního prostoru Azure synapse z **Průzkumník objektů**. Poznámkové bloky Azure synapse Studio můžou rozpoznávat standardní soubory Jupyter Notebook IPYNB.

![vytvoření poznámkového bloku pro import](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>Vývoj poznámkových bloků

Poznámkové bloky se skládají z buněk, což jsou jednotlivé bloky kódu nebo textu, které mohou být spuštěny nezávisle nebo jako skupina.

### <a name="add-a-cell"></a>Přidat buňku

Existuje několik způsobů, jak přidat novou buňku do poznámkového bloku.

# <a name="classical-notebook"></a>[Klasický notebook](#tab/classical)

1. Rozbalte tlačítko horní levý **+ buňka** a vyberte **Přidat znaková buňka** nebo **Přidat textovou buňku**.

    ![Přidání buňky s tlačítkem do buňky](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Najeďte myší na místo mezi dvěma buňkami a vyberte **přidat kód** nebo **Přidat text**.

    ![přidávání buněk mezi místy](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. [V režimu příkazu použijte klávesové zkratky](#shortcut-keys-under-command-mode). Stisknutím **klávesy** vložte buňku nad aktuální buňku. Stisknutím tlačítka **B** vložte buňku pod aktuální buňku.


# <a name="preview-notebook"></a>[Náhled poznámkového bloku](#tab/preview)

1. Rozbalte tlačítko vlevo nahoře **+ buňka** a vyberte **buňku kódu** nebo **buňku Markdownu**.
    ![Přidání – Azure-Poznámkový blok – buňka s tlačítkem-buňka](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)
2. Vyberte znaménko plus na začátku buňky a vyberte **buňku kódu** nebo **buňku Markdownu**.

    ![Přidání – Azure-notebook – buňka – mezi místy](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-2.png)

3. Použijte [klávesové zkratky aznb v režimu příkazu](#shortcut-keys-under-command-mode). Stisknutím **klávesy** vložte buňku nad aktuální buňku. Stisknutím tlačítka **B** vložte buňku pod aktuální buňku.

---

### <a name="set-a-primary-language"></a>Nastavení primárního jazyka

Poznámkové bloky Azure synapse Studio podporují čtyři jazyky Apache Spark:

* pySpark (Python)
* Spark (Scala)
* SparkSQL
* .NET pro Apache Spark (C#)

V rozevíracím seznamu na horním panelu příkazů můžete nastavit primární jazyk pro nové přidané buňky.

   ![výchozí – synapse – jazyk](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Použít více jazyků

V jednom poznámkovém bloku můžete použít více jazyků zadáním správného příkazu jazyka Magic na začátku buňky. V následující tabulce jsou uvedeny příkazy Magic pro přepínání jazyků buněk.

|Magic – příkaz |Jazyk | Description |  
|---|------|-----|
|%% pyspark| Python | Spustí dotaz **Pythonu** v kontextu Sparku.  |
|%% Spark| Scala | Spustí dotaz **Scala** proti kontextu Spark.  |  
|%% SQL| SparkSQL | Spustí dotaz **SparkSQL** proti kontextu Spark.  |
|%% CSharp | .NET pro Spark C # | Spustí dotaz **.NET pro Spark C#** proti kontextu Spark. |

Následující obrázek je příkladem, jak můžete napsat dotaz PySpark pomocí příkazu **%% PySpark** Magic nebo dotazu SparkSQL s příkazem **%% SQL** Magic v poznámkovém bloku **Spark (Scala)** . Všimněte si, že primární jazyk pro Poznámkový blok je nastavený na pySpark.

   ![Příkazy synapse Spark Magic](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages&quot;></a>Použití dočasných tabulek k odkazování na data napříč jazyky

V poznámkovém bloku synapse Studio nejde odkazovat na data ani proměnné přímo v různých jazycích. Ve Sparku může být na dočasné tabulce odkazováno napříč jazyky. Tady je příklad, jak číst datový `Scala` rámec v `PySpark` a `SparkSQL` použít dočasnou tabulku Spark jako alternativní řešení.

1. V buňce 1 si přečtěte datový rámec z konektoru fondu SQL pomocí Scala a vytvořte dočasnou tabulku.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.sqlanalytics(&quot;mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. V buňce 2 se Dotazujte na data pomocí Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. V buňce 3 použijte data v PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>Vývojové prostředí – styly IntelliSense

Poznámkové bloky Azure synapse Studio jsou integrované s editorem Monako a umožňují technologii IntelliSense ve stylu IDE pro Editor buněk. Zvýrazňování syntaxe, značka chyby a automatické dokončování kódu vám pomůžou psát kód a identifikovat problémy rychleji.

Funkce IntelliSense mají různé úrovně splatnosti pro různé jazyky. Pomocí následující tabulky můžete zjistit, co je podporováno.

|Jazyky| Zvýrazňování syntaxe | Značka chyby syntaxe  | Dokončování kódu syntaxe | Dokončování kódu proměnné| Dokončování kódu systémové funkce| Dokončování kódu uživatelské funkce| Inteligentní odsazení | Skládání kódu|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Spark (Scala)|Yes|Yes|Yes|Yes|-|-|-|Yes|
|SparkSQL|Yes|Yes|-|-|-|-|-|-|
|.NET for Spark (C#)|Yes|-|-|-|-|-|-|-|



### <a name="code-snippets"></a>Fragmenty kódu

Poznámkové bloky Azure synapse Studio poskytují fragmenty kódu, které usnadňují zadávání běžných používaných vzorů kódu, jako je například konfigurace relace Spark, čtení dat jako Spark dataframe nebo kreslení grafů pomocí matplotlib atd.

Fragmenty kódu se zobrazují v [IntelliSense](#ide-style-intellisense) i v kombinaci s jinými návrhy. Obsah fragmentů kódu je zarovnán s jazykem buňky kódu. Dostupné fragmenty můžete zobrazit zadáním **fragmentu** nebo libovolných klíčových slov v názvu fragmentu v editoru buněk kódu. Například zadáním **číst** můžete zobrazit seznam fragmentů kódu pro čtení dat z různých zdrojů dat.

![Fragmenty kódu synapse](./media/apache-spark-development-using-notebooks/synapse-code-snippets.gif#lightbox)



### <a name="format-text-cell-with-toolbar-buttons"></a>Formátování textové buňky pomocí tlačítek panelu nástrojů

# <a name="classical-notebook"></a>[Klasický notebook](#tab/classical)

Pomocí tlačítek formát v panelu nástrojů textové buňky můžete provádět běžné akce Markdownu. Zahrnuje tučný text, italicizing text, vkládání fragmentů kódu, vkládání neuspořádaného seznamu, vkládání seřazeného seznamu a vkládání obrázku z adresy URL.

  ![Panel nástrojů textové buňky synapse](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

# <a name="preview-notebook"></a>[Náhled poznámkového bloku](#tab/preview)

Panel nástrojů formátování tlačítka není k dispozici pro prostředí poznámkového bloku Preview. 

---

### <a name="undo-cell-operations"></a>Vrátit operace s buňkami

# <a name="classical-notebook"></a>[Klasický notebook](#tab/classical)

Vyberte tlačítko **zpět** nebo stisknutím **kombinace kláves CTRL + Z** Odvolejte poslední operaci buňky. Nyní můžete vrátit zpět až 20 posledních 20 historických akcí. 

   ![Synapse buňky pro vrácení zpět](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)
# <a name="preview-notebook"></a>[Náhled poznámkového bloku](#tab/preview)

Pro prostředí poznámkového bloku Preview zatím není k dispozici operace vrácení buňky zpět. 

---

### <a name="move-a-cell"></a>Přesunutí buňky

# <a name="classical-notebook"></a>[Klasický notebook](#tab/classical)

Vyberte tři tečky (...) pro přístup k nabídce Další akce buňky úplně vpravo. Pak vyberte **přesunout buňku nahoru** nebo **přesunout buňku dolů** , aby se přesunula aktuální buňka. 

[V režimu příkazu](#shortcut-keys-under-command-mode)můžete také použít klávesové zkratky. Stisknutím **kombinace kláves CTRL + ALT + ↑** přesunete aktuální buňku. Stisknutím **kombinace kláves CTRL + ALT + ↓** přesunete aktuální buňku dolů.

   ![přesunout do buňky](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

# <a name="preview-notebook"></a>[Náhled poznámkového bloku](#tab/preview)

Klikněte na levou stranu buňky a přetáhněte ji na požadovanou pozici. 
    ![Synapse přesunout buňky](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

### <a name="delete-a-cell"></a>Odstranění buňky

# <a name="classical-notebook"></a>[Klasický notebook](#tab/classical)

Pokud chcete odstranit buňku, vyberte tři tečky (...) pro přístup k nabídce Další akce buňky úplně vpravo a pak vyberte **Odstranit buňku**. 

[V režimu příkazu](#shortcut-keys-under-command-mode)můžete také použít klávesové zkratky. Aktuální buňku odstraníte stisknutím klávesy **d, d** .
  
   ![Odstranění buňky](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

# <a name="preview-notebook"></a>[Náhled poznámkového bloku](#tab/preview)

Pokud chcete odstranit buňku, vyberte tlačítko Odstranit na pravé straně buňky. 

[V režimu příkazu](#shortcut-keys-under-command-mode)můžete také použít klávesové zkratky. Aktuální buňku odstraníte stisknutím klávesy **SHIFT + D** . 

   ![Azure – Poznámkový blok – odstranění-a-buňka](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

### <a name="collapse-a-cell-input"></a>Sbalení vstupu buňky

# <a name="classical-notebook"></a>[Klasický notebook](#tab/classical)

Kliknutím na tlačítko se šipkou v dolní části aktuální buňky ho sbalíte. Chcete-li ho rozbalit, vyberte tlačítko se šipkou, zatímco je buňka sbalená.

   ![sbalení – vstup z buňky](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

# <a name="preview-notebook"></a>[Náhled poznámkového bloku](#tab/preview)

Vyberte tři  tečky (...) na panelu nástrojů buňky a **vstup** pro sbalení vstupu aktuální buňky. Pokud ho chcete rozbalit, vyberte **skrytý vstup** , zatímco je buňka sbalená.

   ![Azure-notebook – sbalení-buňka – vstup](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

### <a name="collapse-a-cell-output"></a>Sbalit výstup buňky

# <a name="classical-notebook"></a>[Klasický notebook](#tab/classical)

Vyberte tlačítko **sbalit výstup** v levém horním rohu aktuální buňky a sbalte ho. Chcete-li ji rozbalit, vyberte možnost **Zobrazit výstup buňky** , zatímco je výstup buňky sbalen.

   ![sbalení – výstup z buňky](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

# <a name="preview-notebook"></a>[Náhled poznámkového bloku](#tab/preview)

Vyberte tři  tečky (...) na panelu nástrojů a ve **výstupu** , abyste mohli sbalit výstup aktuální buňky. Pokud ho chcete rozbalit, vyberte stejné tlačítko, zatímco je výstup buňky skrytý.

   ![Azure-notebook – sbalení-buňka-Output](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

## <a name="run-notebooks"></a>Spouštění poznámkových bloků

Buňky kódu můžete na svém poznámkovém bloku spustit jednotlivě nebo najednou. Stav a průběh každé buňky je reprezentován v poznámkovém bloku.

### <a name="run-a-cell"></a>Spuštění buňky

Existuje několik způsobů, jak kód spustit v buňce.

1. Najeďte myší na buňku, kterou chcete spustit, a vyberte tlačítko **Spustit buňku** nebo stiskněte klávesy **CTRL + ENTER**.

   ![spustit – buňka-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. [V režimu příkazu použijte klávesové zkratky](#shortcut-keys-under-command-mode). Stisknutím **SHIFT + ENTER** spusťte aktuální buňku a vyberte buňku níže. Stisknutím **kombinace kláves ALT + ENTER** spusťte aktuální buňku a vložte novou buňku níže.

---

### <a name="run-all-cells"></a>Spustit všechny buňky
Kliknutím na tlačítko **Spustit vše** spustíte všechny buňky v aktuálním poznámkovém bloku v sekvenci.

   ![Spustit – všechny buňky](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


### <a name="run-all-cells-above-or-below"></a>Spustit všechny buňky výše nebo níže

# <a name="classical-notebook"></a>[Klasický notebook](#tab/classical)

Chcete-li získat přístup k nabídce Akce jiné buňky úplně vpravo, vyberte tři tečky (**...**). Pak vyberte **Spustit buňky výše** a spusťte tak všechny buňky nad aktuální sekvencí. Vyberte **Spustit buňky níže** , aby se spouštěly všechny buňky pod aktuálním pořadím.

   ![Run-Cells-nad nebo – níže](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)

# <a name="preview-notebook"></a>[Náhled poznámkového bloku](#tab/preview)

Rozbalte tlačítko rozevírací seznam u položky **Spustit vše** a potom vyberte **Spustit buňky výše** a spusťte tak všechny buňky nad aktuální sekvencí. Vyberte **Spustit buňky níže** , aby se spouštěly všechny buňky pod aktuálním pořadím.

   ![Azure – Poznámkový blok-Run-Cells-nad nebo – níže](./media/apache-spark-development-using-notebooks/synapse-aznb-run-cells-above-or-below.png)

---

### <a name="cancel-all-running-cells"></a>Zrušit všechny běžící buňky

# <a name="classical-notebook"></a>[Klasický notebook](#tab/classical)
Kliknutím na tlačítko **Zrušit vše** zrušíte běžící buňky nebo buňky čekající ve frontě. 
   ![Zrušit vše – buňky](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

# <a name="preview-notebook"></a>[Náhled poznámkového bloku](#tab/preview)

Kliknutím na tlačítko **Zrušit vše** zrušíte běžící buňky nebo buňky čekající ve frontě. 
   ![Azure – Poznámkový blok – zrušit všechny buňky](./media/apache-spark-development-using-notebooks/synapse-aznb-cancel-all.png) 

---



### <a name="notebook-reference"></a>Odkaz na Poznámkový blok

# <a name="classical-notebook"></a>[Klasický notebook](#tab/classical)

Nepodporováno

# <a name="preview-notebook"></a>[Náhled poznámkového bloku](#tab/preview)

Pomocí ```%run <notebook path>``` příkazu Magic můžete odkazovat na jiný Poznámkový blok v kontextu aktuálního poznámkového bloku. Všechny proměnné definované v referenčním poznámkovém bloku jsou k dispozici v aktuálním poznámkovém bloku. ```%run``` příkaz Magic podporuje vnořené volání, ale nepodporuje rekurzivní volání. Pokud je hloubka příkazu větší než pět, zobrazí se výjimka. ```%run``` příkaz aktuálně podporuje pouze cestu k poznámkovému bloku jako parametr. 

Příklad: ``` %run /path/notebookA ```.

> [!NOTE]
> Odkaz na Poznámkový blok není v kanálu synapse podporován.
>
>

---

### <a name="variable-explorer"></a>Průzkumník proměnných

# <a name="classical-notebook"></a>[Klasický notebook](#tab/classical)

Nepodporováno

# <a name="preview-notebook"></a>[Náhled poznámkového bloku](#tab/preview)

Synapse Poznámkový blok poskytuje integrovaný Průzkumníka proměnných, kde můžete zobrazit seznam názvů proměnných, typ, délku a hodnotu v aktuální relaci Spark pro buňky PySpark (Python). Další proměnné se zobrazí automaticky tak, jak jsou definovány v buňkách kódu. Kliknutím na záhlaví každého sloupce se řadí proměnné v tabulce.

Můžete vybrat tlačítko **proměnné** na panelu příkazů poznámkového bloku a otevřít nebo skrýt Průzkumníka proměnných.

![Azure – Poznámkový blok – proměnná – Průzkumník](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-variable-explorer.png)


---

### <a name="cell-status-indicator"></a>Indikátor stavu buňky

Podrobný stav spuštění buňky se zobrazí pod buňkou, která vám umožní zobrazit jeho aktuální průběh. Po dokončení spuštění buňky se zobrazí souhrn spuštění s celkovou dobou trvání a časem ukončení a bude se uchovávat pro budoucí použití.

![stav buňky](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Indikátor průběhu Sparku

Notebook Azure synapse Studio je čistě založený na Sparku. Buňky kódu jsou spouštěny na Apache Sparkm fondu bez serveru vzdáleně. Indikátor průběhu úlohy Spark je k dispozici s pruhem průběhu v reálném čase, který vám pomůže pochopit stav provádění úlohy.
Počet úloh na každou úlohu nebo fázi vám pomůžou identifikovat paralelní úroveň úlohy Sparku. Můžete také procházet hlubší uživatelské rozhraní Spark konkrétní úlohy (nebo fáze) prostřednictvím výběru odkazu na název úlohy (nebo fáze).


![Spark – indikátor průběhu](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Konfigurace relace Spark

Můžete zadat dobu trvání, číslo a velikost prováděcích modulů, které se mají přiřadit aktuální relaci Sparku v části **konfigurovat relaci**. Restartujte relaci Spark, aby se projevily změny v konfiguraci. Všechny proměnné poznámkového bloku uložené v mezipaměti jsou vymazány.

[![Správa relací](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Příkaz pro konfiguraci relace Spark Magic
Nastavení relace Spark můžete zadat také prostřednictvím příkazu Magic **%% Configure**. Aby se nastavení projevilo, je třeba spustit relaci Spark. Doporučujeme na začátku poznámkového bloku spustit **konfiguraci%%** . Tady je ukázka, https://github.com/cloudera/livy#request-body kde najdete úplný seznam platných parametrů. 

```
%%configure -f
{
    to config the session.
    "driverMemory":"2g",
    "driverCores":3,
    "executorMemory":"2g",
    "executorCores":2,
    "jars":["myjar1.jar","myjar.jar"],
    "conf":{
        "spark.driver.maxResultSize":"10g"
    }
}
```
> [!NOTE]
> V kanálu synapse se nepodporuje příkaz konfigurace relace Sparku pro Magic.
>
>

## <a name="bring-data-to-a-notebook"></a>Přenesení dat do poznámkového bloku

Data můžete načíst z Azure Blob Storage Azure Data Lake Store Gen 2 a SQL, jak je znázorněno na následujících ukázkách kódu.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Čtení sdíleného svazku clusteru z Azure Data Lake Store Gen2 jako Spark dataframe

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Čtení sdíleného svazku clusteru z Azure Blob Storage jako Spark dataframe

```python

from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)
```

### <a name="read-data-from-the-primary-storage-account"></a>Čtení dat z primárního účtu úložiště

K datům v primárním účtu úložiště můžete přistupovat přímo. Není nutné zadávat tajné klíče. V Průzkumník dat klikněte pravým tlačítkem na soubor a vyberte **Nový Poznámkový blok** pro zobrazení nového poznámkového bloku s automaticky generovaným nástrojem pro extrakci dat.

![data na buňku](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="save-notebooks"></a>Ukládání poznámkových bloků

V pracovním prostoru můžete uložit jeden Poznámkový blok nebo všechny poznámkové bloky.

1. Pokud chcete uložit změny, které jste provedli v jednom poznámkovém bloku, vyberte tlačítko **publikovat** na panelu příkazů poznámkového bloku.

   ![Publisher – Poznámkový blok](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Pokud chcete uložit všechny poznámkové bloky v pracovním prostoru, vyberte na panelu příkazů pracovní prostor tlačítko **publikovat vše** . 

   ![publikovat – vše](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Ve vlastnostech poznámkového bloku můžete nakonfigurovat, jestli se má při ukládání zahrnout výstup buňky.

   ![vlastnosti poznámkového bloku](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Příkazy magic
V poznámkových blocích Azure synapse Studio můžete používat známé příkazy Jupyter Magic. Zkontrolujte následující seznam jako aktuální dostupné příkazy Magic. Řekněte nám [své případy použití na GitHubu](https://github.com/MicrosoftDocs/azure-docs/issues/new) , abychom mohli pokračovat v sestavování dalších příkazů Magic pro splnění vašich požadavků.

> [!NOTE]
> V kanálu synapse jsou podporovány pouze následující příkazy Magic:%% pyspark,%% Spark,%% CSharp,%% SQL. 
>
>

# <a name="classical-notebook"></a>[Klasický notebook](#tab/classical)

Dostupné magicy řádků: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Dostupné buňky Magic: [%% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages), [%% CSharp](#use-multiple-languages),[%% Configure](#spark-session-config-magic-command)



# <a name="preview-notebook"></a>[Náhled poznámkového bloku](#tab/preview)

Dostupné magicy řádků: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [% history](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history),% [Run](#notebook-reference), [% Load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

Dostupné buňky Magic: [%% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages),% [% CSharp](#use-multiple-languages),%% [HTML](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html), [%% Konfigurovat](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>Integrace poznámkového bloku

### <a name="add-a-notebook-to-a-pipeline"></a>Přidání poznámkového bloku do kanálu

Kliknutím na tlačítko **Přidat do kanálu** v pravém horním rohu přidáte Poznámkový blok do existujícího kanálu nebo vytvoříte nový kanál.

![Přidání poznámkového bloku do kanálu](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Určení buňky parametrů

# <a name="classical-notebook"></a>[Klasický notebook](#tab/classical)

Chcete-li parametrizovat svůj Poznámkový blok, vyberte tři tečky (...) pro přístup k nabídce Další akce buňky úplně vpravo. Pak vyberte položku **Přepnout parametr buňka** k označení buňky jako buňky Parameters.

![přepínač-Parameter](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

# <a name="preview-notebook"></a>[Náhled poznámkového bloku](#tab/preview)

Chcete-li parametrizovat svůj Poznámkový blok, vyberte tři tečky (...) pro přístup k **dalším příkazům** na panelu nástrojů buňky. Pak vyberte položku **Přepnout parametr buňka** k označení buňky jako buňky Parameters.

![Azure – Poznámkový blok – přepínač-Parameter](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

Azure Data Factory vyhledá buňku Parameters a považuje tuto buňku za výchozí hodnoty parametrů předaných v době spuštění. Spouštěcí modul přidá novou buňku pod buňkou Parameters se vstupními parametry, aby bylo možné přepsat výchozí hodnoty. Pokud není určena buňka Parameters, vložená buňka se vloží do horní části poznámkového bloku.


### <a name="assign-parameters-values-from-a-pipeline"></a>Přiřazení hodnot parametrů z kanálu

Po vytvoření poznámkového bloku s parametry ho můžete spustit z kanálu pomocí aktivity poznámkového bloku Azure synapse. Po přidání aktivity na plátno kanálu budete moci nastavit hodnoty parametrů v části **základní parametry** na kartě **Nastavení** . 

![Přiřadit parametr](./media/apache-spark-development-using-notebooks/assign-parameter.png)

Při přiřazování hodnot parametrů můžete použít [Jazyk výrazu kanálu](../../data-factory/control-flow-expression-language-functions.md) nebo [systémové proměnné](../../data-factory/control-flow-system-variables.md).



## <a name="shortcut-keys"></a>Klávesové zkratky

Podobně jako Jupyter poznámkové bloky mají poznámkové bloky Azure synapse Studio modální uživatelské rozhraní. Klávesnice funguje jinak v závislosti na tom, v jakém režimu je buňka poznámkového bloku. Poznámkové bloky synapse Studio podporují pro danou buňku kódu tyto dva režimy: režim příkazů a režim úprav.

1. Buňka je v režimu příkazu, když není k dispozici žádný textový kurzor, který je vyzván k zadání. Když je buňka v režimu příkazu, můžete Poznámkový blok upravit jako celek, ale ne psát do jednotlivých buněk. Stisknutím `ESC` nebo pomocí myši vyberte mimo oblast editoru buňky režim příkazu.

   ![režim příkazu](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. Režim úprav je označen textovým kurzorem, který vás vyzve k zadání v oblasti editoru. Když je buňka v režimu úprav, můžete zadat text do buňky. Stisknutím `Enter` nebo pomocí myši přejděte na oblast editoru buňky a vyberte režim úprav.
   
   ![rezim-uprav](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>Klávesové zkratky v režimu příkazu

# <a name="classical-notebook"></a>[Klasický notebook](#tab/classical)

Pomocí následujících klávesových zkratek můžete snáze procházet a spouštět kód v poznámkových blocích Azure synapse.

| Akce |Zástupci poznámkových bloků synapse Studio  |
|--|--|
|Spustit aktuální buňku a vybrat níže | Shift+Enter |
|Spustit aktuální buňku a vložit níže | Alt+Enter |
|Vybrat buňku výše| Nahoru |
|Vybrat buňku níže| Dolů |
|Vložit buňku výše| A |
|Vložit buňku níže| B |
|Zvětšit vybrané buňky výše| Shift + šipka nahoru |
|Zvětšit vybrané buňky níže| Shift + šipka dolů|
|Přesunout buňku nahoru| CTRL + ALT + ↑ |
|Přesunout buňku dolů| CTRL + ALT + ↓ |
|Odstranit vybrané buňky| D, D |
|Přepnout do režimu úprav| Enter |

# <a name="preview-notebook"></a>[Náhled poznámkového bloku](#tab/preview)

| Akce |Zástupci poznámkových bloků synapse Studio  |
|--|--|
|Spustit aktuální buňku a vybrat níže | Shift+Enter |
|Spustit aktuální buňku a vložit níže | Alt+Enter |
|Spustit aktuální buňku| Ctrl+Enter |
|Vybrat buňku výše| Nahoru |
|Vybrat buňku níže| Dolů |
|Vybrat předchozí buňku| K |
|Vybrat další buňku| J |
|Vložit buňku výše| A |
|Vložit buňku níže| B |
|Odstranit vybrané buňky| Shift + D |
|Přepnout do režimu úprav| Enter |

---

### <a name="shortcut-keys-under-edit-mode"></a>Klávesové zkratky v režimu úprav


Pomocí následujících klávesových zkratek můžete v režimu úprav snadněji Procházet a spouštět kód v poznámkových blocích Azure synapse.

| Akce |Zástupci poznámkových bloků synapse Studio  |
|--|--|
|Přesunout kurzor nahoru | Nahoru |
|Přesunout kurzor dolů|Dolů|
|Zpět|CTRL + Z|
|Opakovat|CTRL + Y|
|Komentář/odkomentovat|CTRL +/|
|Odstranit slovo před|Ctrl + Backspace|
|Odstranit slovo za|CTRL + Delete|
|Přejít na začátek buňky|Ctrl + Home|
|Přejít na konec buňky |CTRL + END|
|Přejít o jedno slovo doleva|Ctrl + šipka vlevo|
|Přejít o jedno slovo doprava|Ctrl + šipka vpravo|
|Vybrat vše|CTRL + A|
|Rážce| CTRL +]|
|Zmenšit odsazení|CTRL + [|
|Přepnout do režimu příkazu| Esc |

---

## <a name="next-steps"></a>Další kroky
- [Podívejte se na ukázkové poznámkové bloky synapse](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Rychlý Start: Vytvoření fondu Apache Spark ve službě Azure synapse Analytics pomocí nástrojů pro web](../quickstart-apache-spark-notebook.md)
- [Co je Apache Spark ve službě Azure synapse Analytics](apache-spark-overview.md)
- [Použití .NET pro Apache Spark se službou Azure Synapse Analytics](spark-dotnet.md)
- [Dokumentace k rozhraní .NET pro Apache Spark](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)