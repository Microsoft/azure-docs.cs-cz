---
title: Nástroje datového jezera pro Visual Studio & Hortonworks – Azure HDInsight
description: Zjistěte, jak používat nástroje Azure Data Lake pro Visual Studio s parkem hortonworks spuštěným v místním virtuálním počítači. Pomocí těchto nástrojů můžete vytvářet a spouštět úlohy Hive a Pig v izolovaném prostoru a zobrazit výstup úlohy a historii.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: e128aaf6e1726b7a1341fefc6df3cdafd3beb880
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73500154"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Použití nástrojů Azure Data Lake pro Visual Studio s parkoviskou parků Hortonworks

Azure Data Lake obsahuje nástroje pro práci s obecnými clustery Apache Hadoop. Tento dokument obsahuje kroky potřebné k použití nástrojů Data Lake s parkištěm Hortonworks sandboxem spuštěným v místním virtuálním počítači.

Použití sandboxu Hortonworks umožňuje pracovat s Hadoopem lokálně na vývojovém prostředí. Po vyvinutí řešení a chcete nasadit ve velkém měřítku, pak můžete přejít do clusteru HDInsight.

## <a name="prerequisites"></a>Požadavky

* Sandbox Hortonworks, spuštěný ve virtuálním počítači ve vývojovém prostředí. Tento dokument byl napsán a testován pomocí izolovaného prostoru spuštěného v sadě Oracle VirtualBox. Informace o nastavení izolovaného prostoru najdete v tématu [Začínáme s pískovištěm Hortonworks.](hadoop/apache-hadoop-emulator-get-started.md) Dokumentu.

* Visual Studio.

* Sada [Azure SDK pro .NET](https://azure.microsoft.com/downloads/) 2.7.1 nebo novější.

* Nástroje [Azure Data Lake pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Konfigurace hesel pro izolované hospo-

Ujistěte se, že je spuštěna sandbox Hortonworks. Potom postupujte podle kroků v dokumentu [Začínáme v hortonworksském pískovišti.](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) Tyto kroky nakonfigurují heslo pro účet SSH `root` a účet Apache Ambari. `admin` Tato hesla se používají při připojení k izolovanému prostoru z visual studia.

## <a name="connect-the-tools-to-the-sandbox"></a>Připojení nástrojů k pískovišti

1. Otevřete Visual Studio, vyberte **Zobrazit**a pak vyberte **Průzkumník serveru**.

2. V **Průzkumníkovi serveru**klikněte pravým tlačítkem myši na **položku HDInsight** a potom vyberte **příkaz Připojit k emulátoru HDInsight**.

    ![Průzkumník serveru se zvýrazněným emulátorem Připojit k HDInsightu](./media/hdinsight-hadoop-emulator-visual-studio/connect-hdinsight-emulator.png)

3. V dialogovém okně **Připojit k emulátoru HDInsight** zadejte heslo, které jste nakonfigurovali pro Ambari.

    ![Snímek obrazovky s dialogovým oknem se zvýrazněným textovým polem ambari](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Pokračujte výběrem tlačítka **Next** (Další).

4. Pole **Heslo** slouží k zadání hesla, `root` které jste pro účet nakonfigurovali. Ostatní pole ponechejte ve výchozí hodnotě.

    ![Snímek obrazovky s dialogovým oknem se zvýrazněným textovým polem root password](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password1.png)

    Pokračujte výběrem tlačítka **Next** (Další).

5. Počkejte na dokončení ověření služeb. V některých případech může dojít k selhání ověření a vyzve vás k aktualizaci konfigurace. Pokud se ověření nezdaří, vyberte **aktualizovat**a počkejte na dokončení konfigurace a ověření.

    ![Snímek obrazovky s dialogovým oknem se zvýrazněným tlačítkem Aktualizovat](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update-window.png)

    > [!NOTE]  
    > Proces aktualizace používá Ambari upravit hortonworks izolovaného prostoru konfigurace na co je očekáváno nástroje datového jezera pro Visual Studio.

6. Po dokončení ověření dokončete konfiguraci výběrem **možnosti Dokončit.**
    ![Snímek obrazovky s dialogovým oknem se zvýrazněným tlačítkem Dokončit](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect-dialog.png)

     >[!NOTE]  
     > V závislosti na rychlosti vývojového prostředí a množství paměti přidělené virtuálnímu počítači může trvat několik minut konfigurace a ověření služeb.

Po provedení těchto kroků máte nyní položku **místního clusteru HDInsight** v Průzkumníkovi serveru v části **HDInsight.**

## <a name="write-an-apache-hive-query"></a>Napsat dotaz Apache Hive

Hive poskytuje dotazovací jazyk podobný SQL (HiveQL) pro práci se strukturovanými daty. Pomocí následujících kroků se dozvíte, jak spustit dotazy na vyžádání v místním clusteru.

1. V **Průzkumníkovi serveru**klepněte pravým tlačítkem myši na položku místního clusteru, který jste přidali dříve, a vyberte příkaz **Write a Hive Query**.

    ![Snímek obrazovky Průzkumníka serveru se zvýrazněným zápisem do hive](./media/hdinsight-hadoop-emulator-visual-studio/write-apache-hive-query.png)

    Zobrazí se nové okno dotazu. Zde můžete rychle napsat a odeslat dotaz do místního clusteru.

2. V novém okně dotazu zadejte následující příkaz:

        select count(*) from sample_08;

    Chcete-li dotaz spustit, vyberte **odeslat** v horní části okna. Ostatní hodnoty **(Batch** a název serveru) ponechejte na výchozí hodnotě.

    ![Snímek obrazovky s oknem dotazu se zvýrazněným tlačítkem Odeslat](./media/hdinsight-hadoop-emulator-visual-studio/query-window-submit-hive.png)

    Můžete také použít rozevírací nabídku vedle **možnosti Odeslat** a vybrat **možnost Upřesnit**. Rozšířené možnosti umožňují poskytnout další možnosti při odeslání úlohy.

    ![Snímek obrazovky s podregistrum dialogového okna Odeslat skript](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-hive.png)

3. Po odeslání dotazu se zobrazí stav úlohy. Stav úlohy zobrazuje informace o úloze při jeho zpracování hadoopem. **Stav úlohy** poskytuje stav úlohy. Stav je pravidelně aktualizován, nebo můžete použít ikonu aktualizace k ruční aktualizaci stavu.

    ![Snímek obrazovky s dialogovým oknem Zobrazení úlohse zvýrazněným stavem úlohy](./media/hdinsight-hadoop-emulator-visual-studio/job-view-dialog-box1.png)

    Po změně **stavu úlohy** na **Dokončeno**se zobrazí směrný acyklický graf (DAG). Tento diagram popisuje cestu spuštění, která byla určena Tez při zpracování dotazu Hive. Tez je výchozí modul provádění pro Hive v místním clusteru.

    > [!NOTE]  
    > Apache Tez je také výchozí, když používáte linuxové clustery HDInsight. Není výchozí na Windows-založené HDInsight. Chcete-li jej použít, musíte `set hive.execution.engine = tez;` přidat řádek na začátek dotazu Hive.

    K zobrazení výstupu použijte odkaz **Výstup úlohy.** V tomto případě je to 823, počet řádků v tabulce sample_08. Diagnostické informace o úloze můžete zobrazit pomocí odkazů **Protokol úloh** a Stáhnout **protokol yarn.**

4. Úlohy Hive můžete také interaktivně spouštět změnou pole **Dávka** na **Interaktivní**. Pak vyberte **Spustit**.

    ![Snímek obrazovky se zvýrazněným tlačítkem Interaktivní a Spustit](./media/hdinsight-hadoop-emulator-visual-studio/hdi-interactive-query.png)

    Interaktivní dotaz streamuje výstupní protokol generovaný během zpracování do okna **Výstup HiveServer2.**

    > [!NOTE]  
    > Informace jsou stejné, které jsou k dispozici z odkazu **Protokol úloh** po dokončení úlohy.

    ![Snímek obrazovky s výstupem HiveServer2](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output-box.png)

## <a name="create-a-hive-project"></a>Vytvoření projektu Hive

Můžete také vytvořit projekt, který obsahuje více skriptů Hive. Projekt použijte v případě, že máte související skripty nebo chcete skripty ukládat do systému správy verzí.

1. V Sadě Visual Studio vyberte **Soubor**, **Nový**a potom **aplikaci Project**.

2. Ze seznamu projektů rozbalte **šablony**, rozbalte **Azure Data Lake**a pak vyberte **HIVE (HDInsight).** Ze seznamu šablon vyberte **ukázku úlu**. Zadejte název a umístění a pak vyberte **OK**.

    ![Okno Nový projekt s Azure Data Lake, Ukázkou úlu a OK](./media/hdinsight-hadoop-emulator-visual-studio/new-apache-hive-project.png)

Ukázkový projekt **Hive** obsahuje dva skripty, **WebLogAnalysis.hql** a **SensorDataAnalysis.hql**. Tyto skripty můžete odeslat pomocí stejného tlačítka **Odeslat** v horní části okna.

## <a name="create-an-apache-pig-project"></a>Vytvořte projekt Apache Pig

Zatímco Hive poskytuje jazyk podobný SQL pro práci se strukturovanými daty, Pig funguje tak, že provádí transformace na datech. Prase poskytuje jazyk (Pig Latin), který vám umožní vyvinout potrubí transformací. Chcete-li použít pig s místním clusterem, postupujte takto:

1. Otevřete Visual Studio a vyberte **Soubor**, **Nový**a potom **Projekt**. Ze seznamu projektů rozbalte **šablony**, rozbalte **Azure Data Lake**a pak vyberte Pig **(HDInsight).** Ze seznamu šablon vyberte **možnost Prasečí aplikace**. Zadejte název, umístění a pak vyberte **OK**.

    ![Snímek obrazovky s oknem Nový projekt se zvýrazněným datovým jezerem Azure, prase, prasečí aplikací a ok](./media/hdinsight-hadoop-emulator-visual-studio/new-apche-pig-project.png)

2. Jako obsah souboru **script.pig,** který byl vytvořen pomocí tohoto projektu, zadejte následující text.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Zatímco Pig používá jiný jazyk než Hive, způsob spuštění úloh je konzistentní mezi oběma jazyky, pomocí tlačítka **Odeslat.** Když vyberete rozevírací nabídku vedle **odeslat,** zobrazí se dialogové okno Rozšířené odeslání pro prase.

    ![Snímek obrazovky s prasetem dialogového okna Odeslat skript](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-pig1.png)

3. Stav úlohy a výstup je také zobrazen, stejně jako dotaz Hive.

    ![Snímek obrazovky s dokončenou úlohou prasete](./media/hdinsight-hadoop-emulator-visual-studio/completed-apache-pig.png)

## <a name="view-jobs"></a>Zobrazit úlohy

Nástroje data lake také umožňují snadno zobrazit informace o úlohách, které byly spuštěny na Hadoopu. Pomocí následujících kroků zobrazíte úlohy, které byly spuštěny v místním clusteru.

1. V **Průzkumníkovi serveru**klepněte pravým tlačítkem myši na místní cluster a vyberte příkaz **Zobrazit úlohy**. Zobrazí se seznam úloh, které byly odeslány do clusteru.

    ![Snímek obrazovky Průzkumníka serveru se zvýrazněným zobrazením úloh](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-view-jobs.png)

2. Ze seznamu úloh vyberte jednu z nich, chcete-li zobrazit podrobnosti o úloze.

    ![Snímek obrazovky s job prohlížečem se zvýrazněnou jednou z úloh](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-job-details.png)

    Zobrazené informace jsou podobné tomu, co vidíte po spuštění dotazu Hive nebo Pig, včetně odkazů pro zobrazení informací o výstupu a protokolu.

3. Můžete také upravit a znovu odeslat úlohu odtud.

## <a name="view-hive-databases"></a>Zobrazit databáze Hive

1. V **Průzkumníkovi serveru**rozbalte položku **místního clusteru HDInsight** a potom rozbalte **položku Databáze hive**. Zobrazí **Default** se výchozí a **xademo** databáze v místním clusteru. Rozbalení databáze zobrazí tabulky v databázi.

    ![Snímek obrazovky Průzkumníka serveru s rozbalením databází](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases-view.png)

2. Rozbalením tabulky se zobrazí sloupce pro tuto tabulku. Chcete-li data rychle zobrazit, klepněte pravým tlačítkem myši na tabulku a vyberte **zobrazit prvních 100 řádků**.

    ![Průzkumník serveru s vybranou možností Zobrazit prvních 100 řádků](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-top-100-rows.png)

### <a name="database-and-table-properties"></a>Vlastnosti databáze a tabulky

Můžete zobrazit vlastnosti databáze nebo tabulky. **Výběrvlastnosti** zobrazí podrobnosti o vybrané položce v okně vlastností. Podívejte se například na informace zobrazené na následujícím snímku obrazovky:

![Snímek obrazovky okna Vlastnosti](./media/hdinsight-hadoop-emulator-visual-studio/hdi-properties-window.png)

### <a name="create-a-table"></a>Vytvoření tabulky

Chcete-li vytvořit tabulku, klepněte pravým tlačítkem myši na databázi a vyberte příkaz **Vytvořit tabulku**.

![Snímek obrazovky Průzkumníka serveru se zvýrazněnou možností Vytvořit tabulku](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-create-table.png)

Tabulku pak můžete vytvořit pomocí formuláře. V dolní části následujícího snímku obrazovky můžete vidět nezpracovaný HiveQL, který se používá k vytvoření tabulky.

![Snímek obrazovky formuláře použitého k vytvoření tabulky](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form-box.png)

## <a name="next-steps"></a>Další kroky

* [Učení lana Hortonworks Pískoviště](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Apache Hadoop tutorial - Začínáme s HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
