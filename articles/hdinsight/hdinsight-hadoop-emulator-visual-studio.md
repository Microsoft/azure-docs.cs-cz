---
title: Nástroje data Lake pro Visual Studio s Hortonworks Sandbox – Azure HDInsight
description: Další informace o použití nástroje Azure Data Lake pro Visual Studio s Hortonworks sandbox spuštěna na místním virtuálním počítači. Pomocí těchto nástrojů můžete vytvořit a spouštět úlohy Hive a Pig izolovaného prostoru a zobrazit výstup úloh a historii.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: hrasheed
ms.openlocfilehash: e6a3fab0e6b3b092ddb55043882c4d284268abfc
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51006843"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Použití nástrojů Azure Data Lake pro Visual Studio s Hortonworks Sandbox

Azure Data Lake obsahuje nástroje pro práci s obecný clusterů Hadoop. Tento dokument obsahuje kroky potřebné k použití nástrojů Data Lake s Hortonworks Sandbox běží na místním virtuálním počítači.

Použití Hortonworks Sandbox umožňuje pracovat s Hadoopem místně na vašem vývojovém prostředí. Poté, co jste vyvinuli řešení a chcete ji nasadit ve velkém měřítku, můžete pak přesunout do clusteru HDInsight.

## <a name="prerequisites"></a>Požadavky

* Hortonworks Sandbox běžící na virtuálním počítači ve svém vývojovém prostředí. Tento dokument byl zapsán a testovat pomocí izolovaného prostoru spuštění v Oracle VirtualBox. Informace o nastavení izolovaného prostoru, najdete v článku [začít pracovat s Hortonworks sandbox.](hadoop/apache-hadoop-emulator-get-started.md) dokument.

* Visual Studio 2013, Visual Studio 2015 nebo Visual Studio 2017 (libovolná edice).

* [Sady Azure SDK for .NET](https://azure.microsoft.com/downloads/) 2.7.1 nebo novější.

* [Nástroje Azure Data Lake pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Nakonfigurujte hesla pro izolovaný prostor

Ujistěte se, že Hortonworks Sandbox běží. Postupujte podle pokynů v [zahájení práce během Hortonworks Sandbox](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) dokumentu. Tyto kroky nastavit heslo pro SSH `root` účet a Ambari `admin` účtu. Tato hesla se používají, když se připojíte k izolovanému prostoru ze sady Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Připojte se k izolovanému prostoru nástroje

1. Otevřít Visual Studio, vyberte **zobrazení**a pak vyberte **Průzkumníka serveru**.

2. Z **Průzkumníka serveru**, klikněte pravým tlačítkem myši **HDInsight** položku a pak vyberte **připojit k emulátoru HDInsight**.

    ![Snímek obrazovky z Průzkumníku serveru se připojit k emulátoru HDInsight zvýrazněnou](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. Z **připojit k emulátoru HDInsight** dialogového okna zadejte heslo, které jste nakonfigurovali pro Ambari.

    ![Snímek obrazovky dialogového okna, se zvýrazněným textového pole hesla](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Pokračujte výběrem tlačítka **Next** (Další).

4. Použití **heslo** pole k zadání hesla, které jste nakonfigurovali pro `root` účtu. Ostatní pole na výchozí hodnotu nechte.

    ![Snímek obrazovky dialogového okna, se zvýrazněným textového pole hesla](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Pokračujte výběrem tlačítka **Next** (Další).

5. Vyčkat, než ověření služby, které mají dokončit. V některých případech může ověření nezdaří, vyzve se aktualizovat konfiguraci. Pokud se ověření nezdaří, vyberte **aktualizace**a počkejte, konfigurace a ověření pro službu na dokončení.

    ![Snímek obrazovky dialogového okna, se zvýrazněným tlačítkem Aktualizovat](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]
    > Proces aktualizace využívá Ambari k úpravě konfigurace Hortonworks Sandbox na co se očekává nástroje Data Lake Tools pro Visual Studio.

6. Po dokončení ověřování vyberte **Dokončit** a dokončete konfiguraci.
    ![Snímek obrazovky dialogového okna, se zvýrazněným tlačítkem Dokončit](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

     >[!NOTE]
     > V závislosti na rychlosti vývojového prostředí a množství paměti přidělené virtuálnímu počítači může trvat několik minut, než se konfigurace a ověření služby.

Po provedení těchto kroků, teď máte **místní cluster HDInsight** položku v Průzkumníku serveru pod **HDInsight** oddílu.

## <a name="write-a-hive-query"></a>Napsat dotaz Hive

Hive poskytuje SQL jako dotazovací jazyk (HiveQL) pro práci se strukturovanými daty. Pomocí následujících kroků zjistěte, jak spouštět dotazy na vyžádání k místnímu clusteru.

1. V **Průzkumníka serveru**, klikněte pravým tlačítkem na položku pro místní cluster, který jste přidali dříve a pak vyberte **napsat dotaz Hive**.

    ![Snímek obrazovky z Průzkumníku serveru zápisu zvýrazněnou dotaz Hive](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Zobrazí se nové okno dotazu. Tady můžete rychle zapisování a odesílání dotazů do místního clusteru.

2. V novém okně dotazu zadejte následující příkaz:

        select count(*) from sample_08;

    Chcete-li spustit dotaz, vyberte **odeslat** v horní části okna. Nechte ostatní hodnoty (**Batch** a název serveru) na výchozích hodnotách.

    ![Snímek obrazovky okna dotazu se zvýrazněným tlačítkem Odeslat](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Můžete také použít v rozevírací nabídce vedle položky **odeslat** vyberte **Upřesnit**. Rozšířené možnosti umožňují poskytují další možnosti při odeslání úlohy.

    ![Dialogové okno snímek obrazovky odeslat skript](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. Po odeslání dotazu se zobrazí stav úlohy. Stav úlohy zobrazuje informace o úloze během zpracování hadoop. **Stav úlohy** obsahuje informace o stavu úlohy. Stav se pravidelně aktualizuje, nebo na ikonu aktualizace můžete ručně aktualizovat stav.

    ![Snímek obrazovky zobrazení úloh dialogovém okně se zvýrazněnou stav úlohy](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Po **stav úlohy** změní na **dokončeno**, zobrazí se orientovaného Acyklického grafu (DAG). Tento diagram znázorňuje cesta spuštění, která byla určena podle Tez při zpracování dotazu Hive. Tez je výchozí prováděcí modul pro Hive na místním clusteru.

    > [!NOTE]
    > Tez je také výchozí, když používáte clusterech HDInsight založených na Linuxu. Není výchozí v HDInsight se systémem Windows. Jeho použití, je nutné přidat řádek `set hive.execution.engine = tez;` na začátek dotazu Hive.

    Použití **výstup úlohy** odkaz k zobrazení výstupu. V takovém případě je 823, počet řádků v tabulce sample_08. Diagnostické informace o úloze můžete zobrazit pomocí **protokol úloh** a **stáhnout protokol YARN** odkazy.

4. Můžete také spouštět úlohy Hive interaktivně pomocí změny **Batch** pole **interaktivní**. Potom vyberte **Execute**.

    ![Snímek obrazovky s interaktivní a spustit tlačítka zvýrazněnou](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    Interaktivního dotazu streamuje výstup protokolu vygenerovaných během zpracování **výstup serveru HiveServer2** okna.

    > [!NOTE]
    > Informace jsou stejné, který je k dispozici **protokol úloh** odkaz po dokončení úlohy.

    ![Snímek obrazovky výstupu protokolu](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Vytvořte projekt Hive

Můžete také vytvořit projekt, který obsahuje několik skriptů Hive. Projekt použijte, pokud mají související skripty nebo chcete uložit skripty v systému správy verzí.

1. V sadě Visual Studio, vyberte **souboru**, **nový**a potom **projektu**.

2. Ze seznamu projektů, rozbalte **šablony**, rozbalte **Azure Data Lake**a pak vyberte **HIVE (HDInsight)**. V seznamu šablon vyberte **Hive ukázka**. Zadejte název a umístění a pak vyberte **OK**.

    ![Zvýrazněný okno snímek obrazovky nového projektu s Azure Data Lake, HIVE, ukázka Hive a OK](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

**Hive ukázka** projekt obsahuje dva skripty **WebLogAnalysis.hql** a **SensorDataAnalysis.hql**. Tyto skripty můžete odeslat pomocí stejného **odeslat** tlačítko v horní části okna.

## <a name="create-a-pig-project"></a>Vytvořte projekt Pig

Zatímco Hive poskytuje jazyk typu SQL pro práci se strukturovanými daty, Pig funguje tak, že provádění transformací na datech. Pig poskytuje jazyk (Pig Latin), který umožňuje vývoj kanál transformace. Použití Pigu se službou místního clusteru, postupujte podle těchto kroků:

1. Otevřít Visual Studio a vyberte **souboru**, **nový**a potom **projektu**. Ze seznamu projektů, rozbalte **šablony**, rozbalte **Azure Data Lake**a pak vyberte **Pig (HDInsight)**. V seznamu šablon vyberte **Pig aplikace**. Zadejte název, umístění a pak vyberte **OK**.

    ![Zvýrazněný okno snímek obrazovky nového projektu s Azure Data Lake, Pig, Pig, aplikace a OK](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Zadejte následující text jako obsah **script.pig** soubor, který byl vytvořen s tímto projektem.

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

    Zatímco Pig používá jiný jazyk než Hive, jak spouštět úlohy, které je konzistentní mezi oba jazyky prostřednictvím **odeslat** tlačítko. Výběrem rozevírací nabídky vedle **odeslat** zobrazí dialogové okno rozšířeného odeslání pro Pig.

    ![Dialogové okno snímek obrazovky odeslat skript](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. Také se zobrazí stav úlohy a výstup, stejně jako dotaz Hive.

    ![Snímek obrazovky dokončené úlohy Pig](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Zobrazit úlohy

Nástroje data Lake taky umožňují snadno zobrazit informace o úlohách, které byly spuštěny na systému Hadoop. Chcete-li zobrazit úlohy spuštěné v místním clusteru, postupujte následovně.

1. Z **Průzkumníka serveru**, klikněte pravým tlačítkem na místním clusteru a pak vyberte **zobrazit úlohy**. Zobrazí se seznam úloh, které byly odeslány do clusteru.

    ![Snímek obrazovky z Průzkumníku serveru se zvýrazněnou zobrazit úlohy](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. Seznam úloh vyberte z nich se má zobrazit podrobnosti o úloze.

    ![Snímek obrazovky úlohy prohlížeče, jedním z úlohy zvýrazněnou](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    Zobrazené informace je podobný naleznete po spuštění dotazu Hive a Pig, včetně odkazů na Zobrazit výstup a protokolovat informace.

3. Můžete také upravit a znovu spustit úlohu z tohoto umístění.

## <a name="view-hive-databases"></a>Zobrazení databáze Hive

1. V **Průzkumníka serveru**, rozbalte **místní cluster HDInsight** položku a potom rozbalte **databáze Hive**. **Výchozí** a **xademo** databáze v místním clusteru se zobrazí. Rozšíření databáze ukazuje tabulky v databázi.

    ![Snímek obrazovky z Průzkumníka serveru s databází rozšířit](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. Rozbalení tabulky zobrazí sloupce pro tabulku. Pokud chcete rychle zobrazit data, klikněte pravým tlačítkem na tabulku a vyberte **zobrazit prvních 100 řádků**.

    ![Snímek obrazovky z Průzkumníku serveru s tabulky rozbalit a zobrazit prvních 100 řádků vybrané](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Vlastnosti databáze a tabulky

Můžete zobrazit vlastnosti databáze nebo tabulky. Výběr **vlastnosti** zobrazí podrobnosti pro vybranou položku v okně Vlastnosti. Viz například informace zobrazené na následujícím snímku obrazovky:

![Snímek obrazovky vlastností okna](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Vytvoření tabulky

Pokud chcete vytvořit tabulku, klikněte pravým tlačítkem na databázi a pak vyberte **Create Table**.

![Snímek obrazovky z Průzkumníku serveru, se zvýrazněným Create Table](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Potom můžete vytvořit v tabulce pomocí formuláře. V dolní části na následujícím snímku obrazovky vidíte nezpracovaná HiveQL, který se používá k vytvoření této tabulky.

![Snímek obrazovky formulář, který slouží k vytvoření tabulky](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>Další postup

* [Učení LAN Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Kurz Hadoopu – Začínáme s HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
