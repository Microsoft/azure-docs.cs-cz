---
title: Použití rozšířených funkcí na serveru Historie Apache Spark k ladění aplikací – Azure HDInsight
description: Pomocí rozšířených funkcí na serveru Apache Spark History Server můžete ladit a diagnostikovat aplikace Spark – Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 5cf1986711479f7330b0cd477744d9f4e2ac6459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548930"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Použití rozšířených funkcí serveru Apache Spark History Server k ladění a diagnostice aplikací Spark

V tomto článku se ukazuje, jak používat rozšířené funkce serveru Apache Spark History Server k ladění a diagnostice dokončených nebo spuštěných aplikací Spark. Rozšíření obsahuje kartu **Data,** kartu **Graf** a kartu **Diagnostika.** Na kartě **Data** můžete zkontrolovat vstupní a výstupní data úlohy Spark. Na kartě **Graf** můžete zkontrolovat tok dat a přehrát graf úlohy. Na kartě **Diagnostika** můžete odkazovat na funkce **Zkosení dat**, **Časové zkosení**a **Analýza využití prováděcího modulu.**

## <a name="get-access-to-the-spark-history-server"></a>Získání přístupu k serveru historie Spark

Spark History Server je webové uživatelské uživatelské nastavení pro dokončené a spuštěné aplikace Spark. Můžete ji otevřít buď z webu Azure portal nebo z adresy URL.

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>Otevření webového uživatelského uživatelského uživatelského nastavení serveru Spark History Server z webu Azure Portal

1. Na [portálu Azure](https://portal.azure.com/)otevřete cluster Spark. Další informace naleznete v [tématu Seznam a zobrazení clusterů](../hdinsight-administer-use-portal-linux.md#showClusters).
2. V **řídicích panelech clusteru**vyberte **server historie Spark**. Po zobrazení výzvy zadejte přihlašovací údaje správce pro cluster Spark.

    ![Spusťte Spark History Server z portálu Azure.](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Server historie spark")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Otevření webového uživatelského uživatelského uživatelského nastavení serveru Historie Spark podle adresy URL

Otevřete Spark History Server `https://CLUSTERNAME.azurehdinsight.net/sparkhistory`procházením do , kde **CLUSTERNAME** je název clusteru Spark.

Webové uživatelské uživatelské nastavení serveru Spark History Server může vypadat podobně jako tento obrázek:

![Stránka Serveru historie spark.](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>Použití karty Data na serveru historie Spark

Vyberte ID úlohy a pak v yberte **Data** v nabídce nástroje, abyste viděli zobrazení dat.

+ Výběrem jednotlivých karet **zkontrolujte vstupy**, **výstupy**a **operace tabulky.**

    ![Karty Dat na stránce Data for Spark Application .](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Zkopírujte všechny řádky výběrem tlačítka **Kopírovat.**

    ![Zkopírujte data na stránce aplikace Spark.](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Uložte všechna data jako . csv výběrem tlačítka **CSV.**

    ![Uložit data jako . soubor CSV ze stránky Data pro aplikaci Spark.](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Vyhledejte data zadáním klíčových slov do pole **Hledat.** Výsledky hledání se zobrazí okamžitě.

    ![Hledání dat na stránce Data for Spark Application .](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Vyberte záhlaví sloupce pro seřazení tabulky. Výběrem znaménku plus rozbalte řádek, abyste zobrazili další podrobnosti. Vyberte znaménko mínus, chcete-li sbalit řádek.

    ![Tabulka dat na stránce Data for Spark Application.](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Stáhněte si jeden soubor výběrem tlačítka **Částečné stažení** vpravo. Vybraný soubor se stáhne místně. Pokud soubor již neexistuje, otevře se nová karta, která zobrazí chybové zprávy.

    ![Řádek pro stahování dat na stránce Data for Spark Application.](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Zkopírujte úplnou nebo relativní cestu výběrem **volby Kopírovat úplnou cestu** nebo **Kopírovat relativní cestu,** která se rozbalí z nabídky stahování. U souborů Azure Data Lake Storage vyberte **Otevřít v Průzkumníkovi úložiště Azure,** abyste spustili Azure Storage Explorer a našli složku po přihlášení.

    ![Kopírovat volby Úplná cesta a Zkopírovat relativní cesta na stránce Data pro aplikaci Spark.](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Pokud je na jedné stránce příliš mnoho řádků, vyberte čísla stránek v dolní části tabulky, na které se chcete procházet.

    ![Čísla stránek na stránce Data pro aplikaci Spark.](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Další informace získáte tak, že najeďte na položku otazník vedle **položky Data pro aplikaci Spark,** chcete-li zobrazit popis.

    ![Další informace získáte na stránce Data for Spark Application .](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  Pokud chcete poslat zpětnou vazbu k problémům, vyberte **Možnost Poskytnout nám zpětnou vazbu**.

    ![Poskytněte zpětnou vazbu ze stránky Data for Spark Application .](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>Použití karty Graf na serveru historie Spark

+ Vyberte ID úlohy a pak v nabídce nástroje vyberte **Graf,** abyste viděli graf úlohy. Ve výchozím nastavení se v grafu zobrazí všechny úlohy. Výsledky můžete filtrovat pomocí rozevírací nabídky **ID úlohy.**

    ![Rozevírací nabídka ID úlohy na stránce Spark Application & Job Graph.](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ **Ve** výchozím nastavení je vybrán průběh. Zkontrolujte tok dat tak, že v rozevírací nabídce **Zobrazení** vyberete **Číst** nebo **Zapsat.**

    ![Zkontrolujte tok dat na stránce Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ Barva pozadí každého úkolu odpovídá tepelné mapě.

   ![Tepelná mapa na stránce Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |Barvy |Popis |
    |---|---|
    |Green|Úloha byla úspěšně dokončena.|
    |Orange|Úkol se nezdařil, ale to nemá vliv na konečný výsledek úlohy. Tyto úkoly mají duplicitní nebo opakovat instance, které mohou být úspěšné později.|
    |Blue|Úloha je spuštěna.|
    |White|Úloha čeká na spuštění nebo je fáze přeskočena.|
    |Červený|Úloha se nezdařila.|

     ![Spuštění úlohy na stránce Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     Přeskočené fáze se zobrazují bíle.
    ![Přeskočená úloha na stránce Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Neúspěšná úloha na stránce Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > Přehrávání je k dispozici pro dokončené úlohy. Chcete-li úlohu přehrát, vyberte tlačítko **Přehrávání.** Úlohu kdykoli zastavte výběrem tlačítka stop. Při přehrávání úlohy se každý úkol zobrazí podle barvy. Přehrávání není podporováno pro neúplné úlohy.

+ Posunutím zobrazení přiblížíte nebo oddálíte graf úlohy nebo vyberte **možnost Lupa, aby** se vešla na obrazovku.

    ![Vyberte Lupa, aby se vešla na stránku Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Když se úkoly nezdaří, najeďte na uzel grafu, abyste viděli popisek, a pak vyberte vymezenou plochu, kterou chcete otevřít na nové stránce.

    ![Zobrazení popisku na stránce Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Na stránce Spark Application & Job Graph se ve fázích zobrazí popisky a malé ikony, pokud úkoly splňují tyto podmínky:
  + Zkosení dat: Velikost čtení dat > průměrná velikost čtení dat všech úloh v této fázi * 2 *a* velikost čtení dat > 10 MB.
  + Časové zkosení: Doba provádění > průměrnou dobu provádění všech úkolů v této fázi * 2 *a* doba provádění > 2 minuty.

    ![Zkosená ikona úlohy na stránce Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ Uzel grafu úloh y zobrazí následující informace o jednotlivých fázích:
  + ID
  + Název nebo popis
  + Celkové číslo úkolu
  + Čtení dat: součet vstupní velikosti a náhodného čtení
  + Zápis dat: součet velikosti výstupu a velikosti náhodného zápisu
  + Čas spuštění: čas mezi počátečním časem prvního pokusu a časem dokončení posledního pokusu
  + Počet řádků: součet vstupních záznamů, výstupních záznamů, náhodného čtení záznamů a náhodného zápisu záznamů
  + Průběh

    > [!NOTE]  
    > Ve výchozím nastavení se v uzlu grafu úlohzobrazí informace z posledního pokusu každé fáze (s výjimkou doby provádění fáze). Ale během přehrávání uzel grafu úlohy zobrazí informace o každém pokusu.

    > [!NOTE]  
    > Pro čtení dat a velikosti zápisu dat používáme 1MB = 1000 KB = 1000 * 1000 bajtů.

+ Pošlete nám svůj názor na problémy výběrem **možnosti Poskytnout nám zpětnou vazbu**.

    ![Možnost zpětné vazby na stránce Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>Použití karty Diagnostika na serveru historie Spark

Vyberte ID úlohy a pak v nabídce nástroje vyberte **Diagnóza,** abyste zobrazili zobrazení diagnostiky úlohy. Karta **Diagnostika** obsahuje **zkosení dat**, **časové zkosení**a **analýzu využití prováděcího modulu**.

+ Výběrem karet zkontrolujte **zkosení dat**, **časové zkosení**a **analýzu využití prováděcího** modulu.

    ![Karta Zkosení dat na kartě Diagnostika.](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Zkosení dat

Vyberte kartu **Zkosení dat.** Odpovídající zkosené úkoly se zobrazí na základě zadaných parametrů.

#### <a name="specify-parameters"></a>Zadat parametry

V části **Zadat parametry** se zobrazí parametry, které se používají ke zjištění zkosení dat. Výchozí pravidlo je: Čtená data úlohy jsou větší než třikrát než data průměrnéúlohy a přečtená data úkolu jsou větší než 10 MB. Pokud chcete definovat vlastní pravidlo pro zkosené úkoly, můžete zvolit parametry. Části **Zkosená plocha** a **zkosený graf** se odpovídajícím způsobem aktualizují.

#### <a name="skewed-stage"></a>Zkosená plocha

Oddíl **Zkosená vymezená plocha** zobrazuje fáze, které mají zkosené úkoly splňující zadaná kritéria. Pokud je ve fázi více než jeden zkosený úkol, zobrazí se v části **Zkosená vymezená plocha** pouze nejvíce zkosený úkol (to znamená největší data pro zkosení dat).

![Větší zobrazení karty Zkosení dat na kartě Diagnóza.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>Zkosený graf

Když vyberete řádek v tabulce **Zkosení vymezené plochy,** **graf zkosení** zobrazí další podrobnosti distribuce úkolů na základě doby čtení dat a času spuštění. Zkosené úkoly jsou označeny červeně a normální úkoly jsou označeny modře. Z hlediska výkonu graf zobrazí až 100 ukázkových úloh. Podrobnosti úkolu jsou zobrazeny v pravém dolním panelu.

![Zkosený graf pro fázi 10 v ui Spark.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Zkosení času

Karta **Zkosení času** zobrazuje zkosené úkoly na základě času provedení úkolu.

#### <a name="specify-parameters"></a>Zadat parametry

V části **Zadat parametry** se zobrazí parametry, které se používají ke zjištění časového zkosení. Výchozí pravidlo je: Doba provádění úloh je větší než třikrát průměrná doba provádění a doba provádění úloh je větší než 30 sekund. Parametry můžete změnit podle svých potřeb. **Zkosená plocha** a **zkosený graf** zobrazují odpovídající informace o fázích a úkolech, stejně jako na kartě **Zkosení dat.**

Když vyberete **Možnost Zkosení času**, filtrovaný výsledek se zobrazí v části **Zkosená plocha** podle parametrů nastavených v části Zadat **parametry.** Když vyberete jednu položku v části **Zkosená plocha,** odpovídající graf se zpracuje ve třetí části a podrobnosti úkolu se zobrazí v pravém dolním panelu.

![Karta Čas zkosení na kartě Diagnóza.](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>Grafy analýzy využití prováděcích kroužků

**Graf využití prováděcího modulu** zobrazuje skutečné přidělení a stav prováděcího modulu úlohy.  

Vyberete-li **možnost Analýza využití prováděcího modulu**, budou vypracovány čtyři různé křivky týkající se využití prováděcího modulu: **Přidělené prováděcí moduly**, **Spuštěné prováděcí moduly**, **nečinné prováděcí moduly**a **Maximální instance prováděcích modulů**. Každý **přidaný exekutor** nebo **odebraný vykonavatel** události zvýší nebo sníží přidělené prováděcí moduly. **Časovou osu událostí** můžete zkontrolovat na kartě **Úlohy** pro další porovnání.

![Karta Analýza využití prováděcího modulu na kartě Diagnóza.](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

Vyberte ikonu barvy, kterou chcete vybrat, nebo zrušte výběr odpovídajícího obsahu ve všech pracovních úkosech.

 ![Vyberte graf na kartě Analýza využití executoru.](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>Nejčastější dotazy

### <a name="how-do-i-revert-to-the-community-version"></a>Jak se vrátím k verzi komunity?

Chcete-li se vrátit k verzi komunity, postupujte takto.

1. Otevřete cluster v Ambari.
1. Přejděte na **Spark2** > **Configs**.
1. Vyberte **vlastní výchozí hodnoty spark2**.
1. Vyberte **Přidat vlastnost ...**.
1. Přidejte **spark.ui.enhancement.enabled=false**a uložte ji.
1. Vlastnost nastaví na **false** nyní.
1. Chcete-li uložit konfiguraci, vyberte **uložit.**

    ![Vypněte funkci v Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. V levém panelu vyberte **Spark2.** Potom na kartě **Souhrn** vyberte **Spark2 History Server**.

    ![Souhrnné zobrazení v Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. Chcete-li restartovat spark history server, vyberte tlačítko **Spuštěno** napravo od **serveru Historie Spark2**a v rozevírací nabídce vyberte **Restartovat.**

    ![Restartujte Spark History Server v Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. Aktualizujte webové uživatelské uživatelské informace serveru Historie Spark. Vrátí se ke komunitní verzi.

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>Jak nahraju událost serveru Historie Spark a nahlásím ji jako problém?

Pokud na serveru Historie sparku narazíte na chybu, proveďte následující kroky k nahlášení události.

1. Stáhněte si událost výběrem **download** v uživatelském rozhraní Serveru historie Spark.

    ![Stáhněte si událost v uzdu Spark History Server.](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Vyberte **Poskytnout nám zpětnou vazbu** ze stránky **Spark Application & Job Graph.**

    ![Poskytnutí zpětné vazby na stránku Spark Application & Job Graph](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Zadejte název a popis chyby. Potom přetáhněte soubor ZIP do pole úprav a vyberte **Odeslat nový problém**.

    ![Nahrajte a odešlete nový problém.](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>Jak lze inovovat soubor .jar ve scénáři opravy hotfix?

Chcete-li provést upgrade pomocí opravy hotfix, použijte `spark-enhancement.jar*`následující skript, který provede upgrade .

**upgrade_spark_enhancement.sh**:

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

#### <a name="usage"></a>Využití

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>Příklad

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>Použití souboru bash z webu Azure Portal

1. Spusťte [portál Azure](https://ms.portal.azure.com)a vyberte svůj cluster.
2. Dokončete [akci skriptu](../hdinsight-hadoop-customize-cluster-linux.md) s následujícími parametry.

    |Vlastnost |Hodnota |
    |---|---|
    |Typ skriptu|- Vlastní|
    |Name (Název)|UpgradeJar|
    |Bash skript URI|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Typ uzlu|Hlava, Pracovník|
    |Parametry|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Akce odeslat skript na Portálu Azure](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Známé problémy

+ V současné době spark History Server funguje pouze pro Spark 2.3 a 2.4.

+ Vstupní a výstupní data, která používají RDD, se na kartě **Data** nezobrazí.

## <a name="next-steps"></a>Další kroky

+ [Správa prostředků pro cluster Apache Spark na HDInsightu](apache-spark-resource-manager.md)
+ [Konfigurace nastavení Apache Sparku](apache-spark-settings.md)

## <a name="feedback"></a>Váš názor

Pokud máte nějakou zpětnou vazbu nebo narazíte na nějaké[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)problémy při používání tohoto nástroje, pošlete e-mail na adresu ( ).
