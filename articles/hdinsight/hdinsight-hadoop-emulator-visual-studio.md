---
title: Nástroje data Lake pro Visual Studio s Hortonworks karanténě - Azure HDInsight | Microsoft Docs
description: Další informace o použití nástroje Azure Data Lake pro Visual Studio s Hortonworks izolovaný spuštěné v místní virtuální počítač. Pomocí těchto nástrojů můžete vytvořit a spustit úlohy Hive a Pig na izolovaného prostoru a zobrazit výstup úlohy a historie.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: e3434c45-95d1-4b96-ad4c-fb59870e2ff0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: larryfr
ms.openlocfilehash: a4c1f5a8100d5d4017e56ef129aa4f4826746868
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Pomocí nástroje Azure Data Lake pro Visual Studio s Hortonworks karanténě

Azure Data Lake obsahuje nástroje pro práci s obecné clusterů systému Hadoop. Tento dokument obsahuje kroky potřebné k použití nástrojů Data Lake s Hortonworks karanténě běží na místním virtuálním počítači.

Pomocí Hortonworks karanténě umožňuje pracovat s Hadoop místně na vašem vývojovém prostředí. Poté, co jste si vytvořili řešení a chcete nasadit ve velkém měřítku, můžete pak přesunout do clusteru HDInsight.

## <a name="prerequisites"></a>Požadavky

* Hortonworks karanténě spuštěných ve virtuálním počítači na vývojového prostředí. Tento dokument byl zapsán a testovány s izolovaného prostoru spuštěné v Oracle VirtualBox. Informace o nastavení izolovaný prostor najdete v tématu [začít pracovat s Hortonworks karanténě.](hadoop/apache-hadoop-emulator-get-started.md) dokument.

* Visual Studio 2013, Visual Studio 2015 nebo Visual Studio 2017 (všechny edice).

* [Azure SDK for .NET](https://azure.microsoft.com/downloads/) 2.7.1 nebo novější.

* [Nástroje Azure Data Lake pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Konfigurace hesla pro izolovaný prostor

Ujistěte se, zda je spuštěna Hortonworks karanténě. Potom postupujte podle kroků v [Začínáme v Hortonworks karanténě](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) dokumentu. Tyto kroky konfigurace heslo SSH `root` účet a Ambari `admin` účtu. Tato hesla se používají při připojování k izolovanému prostoru ze sady Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Nástroje pro připojení k izolovanému prostoru

1. Otevřete Visual Studio, vyberte **zobrazení**a potom vyberte **Průzkumníka serveru**.

2. Z **Průzkumníka serveru**, klikněte pravým tlačítkem myši **HDInsight** položku a potom vyberte **připojit k emulátoru HDInsight**.

    ![Snímek obrazovky Server Explorer, se připojit k emulátoru HDInsight zvýrazněná](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. Z **připojit k emulátoru HDInsight** dialogové okno pole, zadejte heslo, které jste nakonfigurovali pro Ambari.

    ![Snímek obrazovky dialogového okna, se zvýrazněnou textového pole hesla](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Pokračujte výběrem tlačítka **Next** (Další).

4. Použití **heslo** pole k zadání hesla, které jste nakonfigurovali pro `root` účtu. Nechte ostatní pole na výchozí hodnotu.

    ![Snímek obrazovky dialogového okna, se zvýrazněnou textového pole hesla](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Pokračujte výběrem tlačítka **Next** (Další).

5. Počkejte, než pro ověření služby ukončíte. V některých případech může ověření nezdaří a zobrazí výzvu k aktualizaci konfigurace. Pokud ověření selže, vyberte **aktualizace**a počkejte, konfigurace a ověření pro službu, kterou chcete dokončit.

    ![Snímek obrazovky dialogového okna, pomocí tlačítka Aktualizovat zvýrazněná](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]
    > Proces aktualizace používá Ambari ke změně konfigurace Hortonworks karanténě k očekávané pomocí nástroje Data Lake pro Visual Studio.

6. Po dokončení ověření vyberte **Dokončit** a dokončete konfiguraci.
    ![Snímek obrazovky dialogového okna, s tlačítkem Dokončit](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

     >[!NOTE]
     > V závislosti na rychlosti vašeho vývojového prostředí a množství paměti přidělené virtuálnímu počítači se může trvat několik minut, konfigurace a ověřte, že services.

Po následujícím postupem, nyní máte **místní cluster HDInsight** položky v Průzkumníku serveru pod **HDInsight** části.

## <a name="write-a-hive-query"></a>Zápis dotazů Hive

Hive poskytuje podobné jazyku SQL dotazovací jazyk (HiveQL) pro práci s strukturovaná data. Použijte následující postup se dozvíte, jak ke spouštění dotazů na vyžádání na místním clusteru.

1. V **Průzkumníka serveru**, pravým tlačítkem na položku pro místní cluster, který jste přidali dříve a pak vyberte **napsat dotaz Hive**.

    ![Snímek obrazovky z Průzkumníku serveru při zápisu zvýrazněná dotaz Hive](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Zobrazí se nové okno dotazu. Zde můžete rychle zapisování a odesílání dotazů na místní cluster.

2. V novém okně dotazů zadejte následující příkaz:

        select count(*) from sample_08;

    Pokud chcete spustit dotaz, vyberte **odeslání** v horní části okna. Nechte ostatní hodnoty (**Batch** a název serveru) na výchozí hodnoty.

    ![Snímek obrazovky okna dotazu se zvýrazněnou tlačítko pro odeslání](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Můžete také použít v rozevírací nabídce vedle položky **odeslání** vyberte **Upřesnit**. Rozšířené možnosti umožňují poskytují další možnosti při odeslání úlohy.

    ![Dialogové okno snímek obrazovky odeslat skript](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. Po odeslání dotazu, zobrazí se stav úlohy. Stav úlohy zobrazí informace o úloze, jako je zpracován Hadoop. **Stav úlohy** obsahuje informace o stavu úlohy. Stav se aktualizuje pravidelně nebo na ikonu aktualizace můžete ručně obnovit stav.

    ![Snímek obrazovky zobrazení úloh dialogové okno, se zvýrazněnou stav úlohy](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Po **stav úlohy** změny **dokončeno**, zobrazí se přesměruje Acyklické grafu (DAG). Tento diagram znázorňuje provádění cestu, která byla dáno Tez při zpracování dotazu Hive. Tez je výchozí modul provádění pro Hive na místním clusteru.

    > [!NOTE]
    > Tez je také výchozí, pokud používáte clustery HDInsight se systémem Linux. Není výchozí na HDInsight se systémem Windows. Pro použití existuje, je nutné přidat na řádku `set hive.execution.engine = tez;` na začátek dotaz Hive.

    Použití **výstup úlohy** odkaz k zobrazení výstupu. V takovém případě je 823, počet řádků v tabulce sample_08. Diagnostické informace o úloze můžete zobrazit pomocí **protokol úlohy** a **stáhnout protokolu YARN** odkazy.

4. Můžete také spouštět úlohy Hive interaktivně změnou **Batch** do **interaktivní**. Potom vyberte **Execute**.

    ![Snímek obrazovky interaktivní i pro spouštění tlačítka zvýrazněná](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    Interaktivní dotaz datové proudy výstup protokolu vygenerovaných během zpracování **HiveServer2 výstup** okno.

    > [!NOTE]
    > Informace jsou stejné, která je dostupná na **protokol úlohy** odkaz po dokončení úlohy.

    ![Snímek obrazovky výstupu protokolu](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Vytvoření projektu Hive

Můžete také vytvořit projekt, který obsahuje několik skriptů Hive. Projekt použijte, pokud mají související skripty nebo chcete uložit skripty v systému správy verzí.

1. V sadě Visual Studio, vyberte **soubor**, **nový**a potom **projektu**.

2. Ze seznamu projekty, rozbalte položku **šablony**, rozbalte položku **Azure Data Lake**a potom vyberte **HIVE (HDInsight)**. V seznamu šablon vyberte **Hive ukázka**. Zadejte název a umístění a potom vyberte **OK**.

    ![Zvýrazněná okno snímek obrazovky nový projekt, s Azure Data Lake, HIVE, Hive ukázka a OK](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

**Hive ukázka** projekt obsahuje dva skripty **WebLogAnalysis.hql** a **SensorDataAnalysis.hql**. Tyto skripty můžete odeslat pomocí stejné **odeslání** tlačítka v horní části okna.

## <a name="create-a-pig-project"></a>Vytvořit projekt Pig

Zatímco Hive poskytuje podobné jazyku SQL jazyk pro práci s strukturovaných dat, Pig funguje tak, že provádění transformací na data. Pig poskytuje jazyk (Pig Latin), který umožňuje vyvíjet kanálu transformací. Pig s místním clusteru, postupujte podle těchto kroků:

1. Otevřete Visual Studio a vyberte **soubor**, **nový**a potom **projektu**. Ze seznamu projekty, rozbalte položku **šablony**, rozbalte položku **Azure Data Lake**a potom vyberte **Pig (HDInsight)**. V seznamu šablon vyberte **Pig aplikace**. Zadejte název, umístění a potom vyberte **OK**.

    ![Zvýrazněná okno snímek obrazovky nový projekt, s Azure Data Lake, Pig, Pig aplikace a OK](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Zadejte následující text jako obsah **script.pig** soubor, který byl vytvořen tento projekt.

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

    Při Pig používá jiný jazyk než Hive, jak spouštět úlohy je konzistentní mezi oba jazyky prostřednictvím **odeslání** tlačítko. Výběr rozevíracího seznamu vedle položky **odeslání** zobrazí dialogové okno rozšířených pro Pig.

    ![Dialogové okno snímek obrazovky odeslat skript](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. Také se zobrazí stav úlohy a výstup, stejně jako dotaz Hive.

    ![Snímek obrazovky dokončené úlohy Pig](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Zobrazit úlohy

Nástroje data Lake taky umožňují snadno zobrazit informace o úlohách, které byly spuštěny v systému Hadoop. Pomocí následujících kroků můžete zobrazit úlohy, které byly spuštěny v místním clusteru.

1. Z **Průzkumníka serveru**, klikněte pravým tlačítkem na místní cluster a potom vyberte **zobrazit úlohy**. Zobrazí se seznam úloh, které byly odeslány do clusteru.

    ![Snímek obrazovky Server Explorer, se zvýrazněnou zobrazit úlohy](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. Seznam úloh vyberte jednu zobrazíte podrobnosti úlohy.

    ![Snímek obrazovky úlohy prohlížeče, s jedním z úlohy zvýrazněná](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    Informace zobrazené je podobná co se zobrazí po spuštění dotazu Hive nebo Pig, včetně odkazů na Zobrazit výstup a protokolovat informace.

3. Můžete také upravit a znovu odeslat úlohu z tohoto umístění.

## <a name="view-hive-databases"></a>Zobrazení Hive databáze

1. V **Průzkumníka serveru**, rozbalte **místní cluster HDInsight** položku a potom rozbalte **databáze Hive**. **Výchozí** a **xademo** se zobrazí databáze v místním clusteru. Rozšíření databáze zobrazuje tabulky v databázi.

    ![Snímek obrazovky z Průzkumníka serveru s databází rozšířit](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. Rozšiřování tabulka obsahuje sloupec pro tuto tabulku. Pokud chcete rychle zobrazit data, klikněte pravým tlačítkem na tabulku a vyberte **zobrazit prvních 100 řádků**.

    ![Snímek obrazovky z Průzkumníku serveru s Tabulka rozbalit a zobrazit prvních 100 řádků vybrané](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Vlastnosti databáze a tabulky

Můžete zobrazit vlastnosti databázi nebo tabulku. Výběr **vlastnosti** zobrazí podrobnosti pro vybranou položku v okně Vlastnosti. Například najdete informace zobrazené na následujícím snímku obrazovky:

![Snímek obrazovky Vlastnosti – okno](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Vytvoření tabulky

Chcete-li vytvořit tabulku, klikněte pravým tlačítkem na databázi a pak vyberte **Create Table**.

![Snímek obrazovky Server Explorer, se zvýrazněnou Create Table](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Potom můžete vytvořit v tabulce pomocí formuláře. V dolní části následující snímek obrazovky uvidíte nezpracovaná HiveQL, který se používá k vytvoření tabulky.

![Snímek obrazovky formulář, který slouží k vytvoření tabulky](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>Další postup

* [Učení LAN Hortonworks izolovaného prostoru](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop kurz – Začínáme s HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
