---
title: Data Lake Tools for Visual Studio s Hortonworksem izolovaným prostorem (sandbox) – Azure HDInsight
description: Naučte se používat nástroje Azure Data Lake pro Visual Studio s izolovaným prostorem Hortonworks běžícím na místním virtuálním počítači. Pomocí těchto nástrojů můžete vytvářet a spouštět úlohy podregistru a vepřového prostoru v izolovaném prostoru (sandbox) a zobrazit výstup a historii úlohy.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: hrasheed
ms.openlocfilehash: 580712c4d1815b1c994fd62be78e8d7f3f1c9a01
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70809421"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Použití nástrojů Azure Data Lake pro Visual Studio s izolovaným prostorem Hortonworks

Azure Data Lake obsahuje nástroje pro práci s obecnými clustery Apache Hadoop. Tento dokument popisuje kroky potřebné k použití Data Lakech nástrojů s izolovaným prostorem Hortonworks spuštěným na místním virtuálním počítači.

Použití izolovaného prostoru Hortonworks vám umožňuje pracovat se systémem Hadoop místně ve vašem vývojovém prostředí. Až vytvoříte řešení a chcete ho nasadit ve velkém měřítku, můžete přejít na cluster HDInsight.

## <a name="prerequisites"></a>Požadavky

* Hortonworks izolovaný prostor (sandbox) běžící ve virtuálním počítači na vašem vývojovém prostředí. Tento dokument byl napsán a testován pomocí izolovaného prostoru (sandboxu) spuštěného v Oracle VirtualBox. Informace o nastavení izolovaného prostoru (sandbox) najdete v tématu [Začínáme s izolovaným prostorem (Hortonworks).](hadoop/apache-hadoop-emulator-get-started.md) dokumentů.

* Visual Studio.

* [Sada Azure SDK pro .NET](https://azure.microsoft.com/downloads/) 2.7.1 nebo novější.

* [Nástroje pro Azure Data Lake pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Konfigurace hesel pro izolovaný prostor

Ujistěte se, že je spuštěný izolovaný prostor (Hortonworks). Pak postupujte podle kroků v dokumentu [Hortonworks sandboxu](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) v části Začínáme. Tyto kroky nakonfigurují heslo pro účet SSH `root` a účet Apache Ambari. `admin` Tato hesla se používají, když se připojíte k izolovanému prostoru ze sady Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Připojit nástroje k izolovanému prostoru

1. Otevřete Visual Studio, vyberte **Zobrazit**a pak vyberte **Průzkumník serveru**.

2. Z **Průzkumník serveru**klikněte pravým tlačítkem na položku **HDInsight** a pak vyberte **připojit k emulátoru HDInsight**.

    ![Snímek obrazovky Průzkumník serveru se zvýrazněnou možností připojit k emulátoru HDInsight](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. V dialogovém okně **připojit k emulátoru HDInsight** zadejte heslo, které jste nakonfigurovali pro Ambari.

    ![Snímek obrazovky dialogového okna se zvýrazněným textovým polem pro heslo Ambari](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Pokračujte výběrem tlačítka **Next** (Další).

4. Pomocí pole **heslo** zadejte heslo, které jste nakonfigurovali pro `root` účet. U ostatních polí ponechte výchozí hodnotu.

    ![Snímek obrazovky dialogového okna se zvýrazněným textovým polem pro kořenové heslo](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Pokračujte výběrem tlačítka **Next** (Další).

5. Počkejte, až se dokončí ověřování služeb. V některých případech se může stát, že se ověření nezdaří a zobrazí se výzva k aktualizaci konfigurace. Pokud se ověření nepovede, vyberte **aktualizovat**a počkejte na dokončení konfigurace a ověření služby.

    ![Snímek obrazovky dialogového okna se zvýrazněným tlačítkem aktualizovat](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]  
    > Proces aktualizace používá Ambari ke změně konfigurace izolovaného prostoru Hortonworks na to, co se očekává pomocí nástrojů Data Lake Tools for Visual Studio.

6. Po dokončení ověření vyberte **Dokončit** a dokončete konfiguraci.
    ![Snímek obrazovky dialogového okna s zvýrazněným tlačítkem Dokončit](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

     >[!NOTE]  
     > V závislosti na rychlosti vývojového prostředí a množství paměti přidělené virtuálnímu počítači může trvat několik minut, než se nakonfiguruje a ověří služby.

Po provedení těchto kroků teď máte v Průzkumník serveru v části **HDInsight** **místní clusterovou položku HDInsight** .

## <a name="write-an-apache-hive-query"></a>Zapsat Apache Hive dotaz

Podregistr poskytuje dotazovací jazyk HiveQL (SQL like) pro práci se strukturovanými daty. Pomocí následujícího postupu se naučíte, jak spouštět dotazy na vyžádání v místním clusteru.

1. V **Průzkumník serveru**klikněte pravým tlačítkem myši na položku pro místní cluster, který jste dříve přidali, a pak vyberte **zapsat dotaz na podregistr**.

    ![Snímek obrazovky Průzkumník serveru se zvýrazněným zápisem dotazu na podregistr](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Zobrazí se nové okno dotazu. Tady můžete rychle napsat a odeslat dotaz do místního clusteru.

2. V okně Nový dotaz zadejte následující příkaz:

        select count(*) from sample_08;

    Chcete-li spustit dotaz, vyberte v horní části okna možnost **Odeslat** . Ostatní hodnoty (název**Batch** a Server) ponechte na výchozích hodnotách.

    ![Snímek obrazovky s oknem dotazů s zvýrazněným tlačítkem Odeslat](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Můžete také použít rozevírací nabídku vedle možnosti **Odeslat** a vybrat **Upřesnit**. Rozšířené možnosti umožňují při odeslání úlohy zadat další možnosti.

    ![Snímek obrazovky s podoknem pro odeslání skriptu dialog box](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. Po odeslání dotazu se zobrazí stav úlohy. Stav úlohy zobrazuje informace o úloze, která se zpracovává pomocí Hadoop. Stav **úlohy** poskytuje stav úlohy. Stav se pravidelně aktualizuje nebo můžete ručně aktualizovat stav pomocí ikony aktualizovat.

    ![Snímek obrazovky s dialogovým oknem zobrazení úlohy se zvýrazněným stavem úlohy](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Jakmile se **stav úlohy** změní na **dokončený**, zobrazí se SMĚROVANÝ acyklického graf (DAG). Tento diagram popisuje cestu spuštění, která byla určena nástrojem tez při zpracování dotazu na podregistr. Tez je výchozí spouštěcí modul pro podregistr v místním clusteru.

    > [!NOTE]  
    > Apache Tez je také výchozí, když používáte clustery HDInsight se systémem Linux. Nejedná se o výchozí hodnotu v HDInsight se systémem Windows. Pokud ho chcete použít, je nutné přidat řádek `set hive.execution.engine = tez;` na začátek dotazu na podregistr.

    Výstup můžete zobrazit pomocí odkazu **výstup úlohy** . V tomto případě je to 823, počet řádků v tabulce sample_08. Diagnostické informace o úloze můžete zobrazit pomocí **protokolu úlohy** a **Stáhnout odkazy protokolu nitě** .

4. Úlohy podregistru můžete také spustit interaktivně tím, že změníte pole **Batch** na **interaktivní**. Pak vyberte **provést**.

    ![Snímek obrazovky s zvýrazněnými tlačítky pro interaktivní a spouštěné](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    Interaktivní dotaz streamuje výstupní protokol generovaný během zpracování do okna **výstupu HiveServer2** .

    > [!NOTE]  
    > Tyto informace jsou stejné, jaké jsou k dispozici v odkazu **protokolu úlohy** po dokončení úlohy.

    ![Snímek obrazovky s výstupním protokolem](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Vytvoření projektu podregistru

Můžete také vytvořit projekt, který obsahuje více skriptů podregistru. Použijte projekt, pokud máte související skripty nebo chcete ukládat skripty do systému správy verzí.

1. V aplikaci Visual Studio zvolte **soubor**, **Nový**a pak **projekt**.

2. V seznamu projektů rozbalte **šablony**, rozbalte **Azure Data Lake**a pak vyberte **podregistr (HDInsight)** . V seznamu šablon vyberte možnost **Ukázka podregistru**. Zadejte název a umístění a pak vyberte **OK**.

    ![Snímek obrazovky s novým projektem s Azure Data Lake, podregistrem, ukázkou podregistru a zvýrazněnou možností OK](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

**Ukázkový projekt podregistru** obsahuje dva skripty: **WebLogAnalysis. HQL** a **SensorDataAnalysis. HQL**. Tyto skripty můžete odeslat pomocí stejného tlačítka pro **odeslání** v horní části okna.

## <a name="create-an-apache-pig-project"></a>Vytvoření projektu Apache prasete

I když podregistr poskytuje jazyk podobný jazyku SQL pro práci se strukturovanými daty, prase funguje na základě transformací dat. Prase poskytuje jazyk (prasečí Latin), který umožňuje vývoj kanálu transformací. Chcete-li použít prase s místním clusterem, postupujte podle následujících kroků:

1. Otevřete Visual Studio a vyberte **soubor**, **Nový**a pak **projekt**. V seznamu projektů rozbalte **šablony**, rozbalte **Azure Data Lake**a pak vyberte **prase (HDInsight)** . V seznamu šablon vyberte možnost **aplikace prasete**. Zadejte název, umístění a pak vyberte **OK**.

    ![Snímek obrazovky s novým projektem s Azure Data Lake, vepřovým prasetem, aplikací pro vepřové prase a zvýrazněnou možností OK](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Jako obsah souboru **skriptu. prase** , který byl vytvořen pomocí tohoto projektu, zadejte následující text.

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

    I když prase používá jiný jazyk než podregistr, jak spouštění úloh je konzistentní mezi oběma jazyky, a to prostřednictvím tlačítka **Odeslat** . Po výběru rozevíracího seznamu vedle položky **Odeslat** se zobrazí dialogové okno pro zobrazení pro prase s rozšířeným odesláním.

    ![Snímek obrazovky s dialogovým oknem pro odeslání skriptu pro vepřové okno](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. Zobrazí se také stav a výstup úlohy, stejně jako dotaz na podregistr.

    ![Snímek obrazovky dokončené úlohy prasete](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Zobrazit úlohy

Nástroje Data Lake také umožňují snadno zobrazit informace o úlohách, které byly spuštěny v systému Hadoop. Pomocí následujícího postupu můžete zobrazit úlohy, které byly spuštěny v místním clusteru.

1. Z **Průzkumník serveru**klikněte pravým tlačítkem myši na místní cluster a pak vyberte **Zobrazit úlohy**. Zobrazí se seznam úloh, které byly odeslány do clusteru.

    ![Snímek obrazovky Průzkumník serveru se zvýrazněnými zobrazeními úloh](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. V seznamu úloh vyberte jednu pro zobrazení podrobností o úloze.

    ![Snímek obrazovky prohlížeče úloh s jednou zvýrazněnou úlohou](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    Zobrazené informace se podobají tomu, co vidíte po spuštění dotazů na podregistr nebo na základě vepřového dotazu, včetně odkazů na zobrazení výstupů a informací o protokolu.

3. Úlohu můžete také upravit a znovu odeslat z tohoto místa.

## <a name="view-hive-databases"></a>Zobrazení databází podregistru

1. V **Průzkumník serveru**rozbalte položku **místní cluster HDInsight** a potom rozbalte možnost **databáze podregistru**. Zobrazí se **výchozí** databáze a databáze **xademo** v místním clusteru. Rozbalením databáze se zobrazí tabulky v databázi.

    ![Snímek obrazovky Průzkumník serveru s rozbalenými databázemi](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. Rozbalením tabulky se zobrazí sloupce pro tuto tabulku. Data můžete rychle zobrazit tak, že kliknete pravým tlačítkem na tabulku a vyberete **Zobrazit prvních 100 řádků**.

    ![Snímek obrazovky Průzkumník serveru s vybranými tabulkami rozbalenými a zobrazenými horními 100 řádky](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Vlastnosti databáze a tabulky

Můžete zobrazit vlastnosti databáze nebo tabulky. Výběr **vlastností** zobrazí podrobnosti o vybrané položce v okně Vlastnosti. Podívejte se například na informace uvedené na následujícím snímku obrazovky:

![Snímek obrazovky s okno Vlastnosti](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Vytvoření tabulky

Tabulku vytvoříte tak, že kliknete pravým tlačítkem na databázi a pak vyberete **vytvořit tabulku**.

![Snímek obrazovky Průzkumník serveru s zvýrazněnou možností vytvořit tabulku](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Tabulku pak můžete vytvořit pomocí formuláře. V dolní části následujícího snímku obrazovky vidíte nezpracovaná HiveQL, která se používá k vytvoření tabulky.

![Snímek obrazovky formuláře použitého k vytvoření tabulky](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>Další postup

* [Učení LAN Hortonworks sandboxu](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Kurz Apache Hadoop – Začínáme s HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
