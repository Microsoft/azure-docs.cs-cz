---
title: Reference k nastavení konfigurace Azure HDInsight
description: Zaveďte konfiguraci rozšíření Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: devx-track-python
ms.openlocfilehash: a9a444c2557ea17114123cbd5084cbbd9fe6dac6
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259968"
---
# <a name="azure-hdinsight-configuration-settings-reference"></a>Reference k nastavení konfigurace Azure HDInsight

Rozšíření nástrojů pro podregistr Spark & pro Visual Studio Code je vysoce konfigurovatelné. Tato stránka popisuje klíčová nastavení, se kterými můžete pracovat.  

Obecné informace o práci s nastavením v VS Code najdete v tématu [nastavení uživatele a pracovního prostoru](https://code.visualstudio.com/docs/getstarted/settings)a referenční informace o [proměnných](https://code.visualstudio.com/docs/editor/variables-reference) pro podporu předdefinovaných proměnných.

## <a name="open-the-azure-hdinsight-configuration"></a>Otevření konfigurace Azure HDInsight

1. Nejprve otevřete složku pro vytvoření nastavení pracovního prostoru.
2. Stiskněte **kombinaci kláves CTRL + SHIFT + P** nebo přejděte k **zobrazení**  ->  **palety příkazů...** , aby se zobrazily všechny příkazy.
3. **Konfigurace sady** výsledků hledání.
4. Rozbalte **rozšíření** v levém adresáři a vyberte **Konfigurace HDInsight**.

 ![bitová kopie konfigurace HDI](./media/HDInsight-config-for-vscode/HDInsight-config-for-vscode.png)

## <a name="general-settings"></a>Obecná nastavení   

|  Vlastnost   | Výchozí | Description   |
| ----- | ----- |----- |
| HDInsight: prostředí Azure | Azure | Prostředí Azure |
| HDInsight: zakázat odkaz na otevřený průzkum | Zaškrtnuto | Povolit nebo zakázat otevírání průzkumu HDInsight |
| HDInsight: povolení přeskočení instalace Pyspark | Nezaškrtnuto | Povolit nebo zakázat přeskočení instalace pyspark |
| HDInsight: zapnutí tipů pro přihlášení | Nezaškrtnuto | Když je tato možnost zaškrtnutá, při přihlašování do Azure se zobrazí výzva. |
| HDInsight: předchozí verze rozšíření | Zobrazí číslo verze aktuálního rozšíření. | Zobrazit předchozí verzi rozšíření|
| HDInsight: rodina písem s výsledky | – Apple-System, BlinkMacSystemFont, Segoe WPC, Segoe UI, HelveticaNeue-Light, Ubuntu, Droid Sans, Sans-Serif | Nastavte rodinu písem pro mřížku výsledků. Nastavte na prázdné, aby se použilo písmo editoru. |
| HDInsight: velikost písma výsledků | 13 |Nastavte velikost písma pro Gird výsledků; Nastavte na prázdné, aby se použila velikost editoru. |
| Cluster HDInsight: propojený cluster | -- | Adresy URL propojených clusterů. Můžete taky upravit soubor JSON, který se má nastavit. |
| Podregistr HDInsight: použití lokalizace | Nezaškrtnuto | Volitelné Možnosti konfigurace pro lokalizaci do konfigurovaného národního prostředí VSCode (je třeba restartovat VSCode, aby se nastavení projevilo)|
| Podregistr HDInsight: kopírování vložených hlaviček | Nezaškrtnuto | Volitelné Možnost konfigurace pro kopírování výsledků z zobrazení výsledků |
| Podregistr HDInsight: kopírovat odstranit nový řádek | Zaškrtnuto | Volitelné Možnosti konfigurace pro kopírování víceřádkových výsledků z zobrazení výsledků |
| ›Ový formát podregistru HDInsight: zarovnání definic sloupců ve sloupcích | Nezaškrtnuto | Měla by být zarovnána definice sloupce |
| ›Ový formát podregistru HDInsight: DataType – velká písmena | žádné | Měly by se formátovat datové typy jako velká písmena, malá písmena nebo žádná (neformátovaná). |
| ›, Formát podklíče HDInsight: velká písmena | žádné | Měla by se formátovat klíčová slova jako velká písmena, malá písmena nebo žádná (neformátovaná) |
| Formát › podregistru HDInsight: před dalším příkazem umístěte čárky. | Nezaškrtnuto | Měly by být umístěny na začátku každého příkazu v seznamu (například mycolumn2) místo na konci ' mycolumn1 '.|
| Formát › podregistru HDInsight: umístit odkazy na příkazy SELECT na nový řádek | Nezaškrtnuto | Mají se odkazy na objekty v příkazu SELECT rozdělit na samostatné řádky? Například pro ' SELECT C1, C2 z T1 ' budou na samostatné řádky, C1 i C2.
| Podregistr HDInsight: protokolování informací o ladění | Nezaškrtnuto | Volitelné Protokoluje výstup ladění do konzoly VS Code (příkaz help-> přepínač Vývojářské nástroje). 
| Podregistr HDInsight: výchozí otevřené zprávy | Zaškrtnuto | True – podokno zprávy se ve výchozím nastavení otevře. hodnota false pro uzavřenou|
| Podregistr HDInsight: rodina písem s výsledky | – Apple-System, BlinkMacSystemFont, Segoe WPC, Segoe UI, HelveticaNeue-Light, Ubuntu, Droid Sans, Sans-Serif | Nastavte rodinu písem pro mřížku výsledků. Nastavte na prázdné, aby se použilo písmo editoru. |
| Podregistr HDInsight: velikost písma výsledků | 13 | Nastavit velikost písma pro mřížku výsledků; Nastavte na prázdné, aby se použila velikost editoru. |
| Podregistr HDInsight › Uložit jako CSV: zahrnout hlavičky | Zaškrtnuto | Volitelné V případě hodnoty true jsou při ukládání výsledků ve formátu CSV zahrnuty záhlaví sloupců. |
| Podregistr HDInsight: zkratky | -- | Zkratky související s oknem výsledků |
| Podregistr HDInsight: zobrazení času dávky| Nezaškrtnuto | Volitelné Má se zobrazit doba spuštění pro jednotlivé dávky |
| Podregistr HDInsight: výběr rozděleného podokna | generace | Volitelné Možnosti konfigurace, ve kterých se mají otevírat nové podokna výsledků ve sloupci |
| Odeslání úlohy HDInsight: cluster conf | -- | Konfigurace clusteru |
| Odeslání úlohy HDInsight: Livy conf | -- | Konfigurace Livy. PŘÍSPĚVKY a dávky |
| HDInsight Jupyter: připojení výsledků| Zaškrtnuto | Bez ohledu na to, jestli se mají výsledky připojit k oknu výsledků, se můžete vyjasnit a zobrazit. |
| HDInsight Jupyter: jazyky | -- | Výchozí nastavení pro jednotlivé jazyky |
| Protokol HDInsight Jupyter ›: verbose | Nezaškrtnuto | Pokud Povolit podrobné protokolování |
| Jupyter › pro HDInsight: spouštěcí argumenty | Může přidat položku. | argumenty příkazového řádku Jupyter poznámkového bloku Každý argument je samostatná položka v poli. Úplný seznam typu "Jupyter Poznámkový blok"-Help "v okně terminálu. |
| Jupyter › pro HDInsight: složka po spuštění | $ {workspaceRoot} |-- |
| HDInsight Jupyter: rozšíření Python je povolené. | Zaškrtnuto | Při odesílání interaktivních úloh pySpark použijte Python-Interactive-Window rozšíření MS-Python. V opačném případě použijte vlastní okno Jupyter |
| HDInsight Spark.NET: 7z | C:\Program Files\7-Zip | <cesta k 7z.exe> |
| Spark.NET HDInsight: HADOOP_HOME | D:\winutils | <cesta k bin\winutils.exe> jenom v operačním systému Windows |
| Spark.NET HDInsight: JAVA_HOME | C:\Program Files\Java\ jdk1.8.0_201 \ | Cesta k domovskému Java|
| Spark.NET HDInsight: SCALA_HOME | C:\Program Files (x86) \scala\ | Cesta k Scala domů |
| Spark.NET HDInsight: SPARK_HOME | D:\spark-2.3.3-bin-hadoop2.7\ | Cesta k domu Spark |
| Podregistr: zachovejte záložky výsledků dotazu | Nezaškrtnuto | PersistQueryResultTabs podregistru |
| Podregistr: výběr rozděleného podokna | generace | Volitelné Možnosti konfigurace, ve kterých se mají otevírat nové podokna výsledků ve sloupci |
| Podregistr Interactive: Kopírování spustitelné složky | Nezaškrtnuto | Pokud kopírujete složku Interactive Service runtime pro podregistr do složky TMP uživatele |
| HQL Interactive Server: port obálky | 13424 | Port interaktivní služby pro podregistr |
| HQL Language Server: port obálka jazyka | 12342 | Servery portů služby jazyka pro podregistr naslouchají. |
| HQL Language Server: maximální počet problémů | 100 | Určuje maximální počet problémů vyprodukovaných serverem. |
| Synapse Spark Compute: prostředí Azure synapse Spark COMPUTE | trhnout | Výpočetní prostředí Azure synapse Spark |
| Odeslání úlohy fondu synapse Spark: Livy conf | -- | Konfigurace Livy. PŘÍSPĚVKY a dávky
| Odeslání úlohy fondu Spark synapse: synapse, clusterový fond Sparku conf | -- | Konfigurace fondu synapse Spark |


## <a name="next-steps"></a>Další kroky

- Informace o Azure HDInsight pro VSCode najdete v tématu věnovaném [nástroji Spark & podregistr pro Visual Studio Code Tools](https://docs.microsoft.com/sql/big-data-cluster/spark-hive-tools-vscode).
- Video, které ukazuje použití podregistru Spark & pro Visual Studio Code, najdete v tématu věnovaném [registru spark & pro Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).