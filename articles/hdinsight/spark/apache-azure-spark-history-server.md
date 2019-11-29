---
title: Rozšířený server pro historii Sparku pro ladění aplikací – Azure HDInsight
description: Použití rozšířeného serveru pro historii Spark k ladění a diagnostice aplikací Spark – Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 7e9ab0e41086a4c9478f95c5a56754640feeab4e
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561830"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Použití serveru historie rozšířených Apache Spark k ladění a diagnostice Apache Spark aplikací

Tento článek popisuje, jak používat server pro rozšířené Apache Spark pro ladění a diagnostikování dokončených a spuštěných aplikací Spark. Rozšíření zahrnuje kartu data a kartu graf a diagnostika. Na kartě **data** mohou uživatelé kontrolovat vstupní a výstupní data úlohy Spark. Na kartě **graf** mohou uživatelé kontrolovat tok dat a znovu přehrát graf úlohy. Na kartě **Diagnostika** může uživatel odkazovat na překrytí **dat**, **zkosení času**a **analýzu využití prováděcího modulu**.

## <a name="get-access-to-apache-spark-history-server"></a>Získat přístup k serveru historie Apache Spark

Server historie Apache Spark je webové uživatelské rozhraní pro kompletní a běžící aplikace Spark.

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>Otevřete webové uživatelské rozhraní serveru Apache Spark historie z Azure Portal

1. Z [Azure Portal](https://portal.azure.com/)otevřete cluster Spark. Další informace najdete v tématech [seznam a zobrazení clusterů](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Z **řídicích panelů clusteru**vyberte možnost **Server historie Spark**. Po zobrazení výzvy zadejte přihlašovací údaje správce pro cluster Spark.

    ![portál – spustit historii serveru Spark](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Server historie Sparku")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Otevřete webové uživatelské rozhraní serveru historie Sparku podle adresy URL

Otevřete Server historie Sparku tak, že přejdete na `https://CLUSTERNAME.azurehdinsight.net/sparkhistory`, kde název_clusteru je název vašeho clusteru Spark.

Webové uživatelské rozhraní serveru historie Spark může vypadat podobně jako:

![Server historie HDInsight Spark](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="data-tab-in-spark-history-server"></a>Karta data v historii serveru Spark

Vyberte ID úlohy a potom v nabídce Nástroje vyberte **data** , aby se zobrazila data.

+ Zkontrolujte **vstupy**a **výstupy**a **operace s tabulkami** , a to tak, že karty vyberete samostatně.

    ![Data pro karty aplikace Spark](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Kopírovat všechny řádky výběrem **kopie**tlačítka

    ![Data pro kopii aplikace Spark](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Uložte všechna data jako soubor CSV tak, že vyberete tlačítko **CSV**.

    ![Data pro uložení aplikace Spark](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Hledání je prováděno zadáním klíčových slov do pole **hledání**, výsledek hledání se zobrazí okamžitě.

    ![Data pro vyhledávání aplikace Spark](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Vyberte záhlaví sloupce k seřazení tabulky, vyberte znaménko plus pro rozbalení řádku, aby se zobrazily další podrobnosti, nebo vyberte znaménko mínus pro sbalení řádku.

    ![Data pro tabulku aplikace Spark](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Stažení samostatného souboru výběrem **částečného stažení tlačítka Stáhnout** , vybrané soubory se stáhnou do místní, pokud soubor už neexistuje, otevře se nová karta, kde se zobrazí chybové zprávy.

    ![Data pro řádek pro stažení aplikace Spark](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Úplnou cestu nebo relativní cestu zkopírujte výběrem možnosti **Kopírovat úplnou cestu**a **Kopírovat relativní cestu** , která se rozbalí z nabídky stáhnout. Pro soubory Azure Data Lake Storage **otevřete v Průzkumník služby Azure Storage** spustí Průzkumník služby Azure Storage a při přihlášení Najděte složku.

    ![Data pro kopírovací cestu aplikace Spark](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Vyberte číslo pod tabulkou pro procházení stránek, když je na jedné stránce zobrazeno příliš mnoho řádků.

    ![Data pro stránku aplikace Spark](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Najeďte myší na otazník vedle položky data, abyste zobrazili popis, nebo vyberte otazník a získejte další informace.

    ![Data pro aplikaci Spark další informace](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Kliknutím na **poskytnout zpětnou**vazbu pošlete nám problémy s problémy.

    ![Sparkový graf nám zadejte zpětnou vazbu](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Karta graf na serveru historie Apache Spark

Vyberte ID úlohy a potom kliknutím na **graf** v nabídce nástroje Získejte zobrazení grafu úlohy.

+ Přehled úlohy najdete v grafu vygenerované úlohy.

+ Ve výchozím nastavení se zobrazí všechny úlohy a můžete je filtrovat podle **ID úlohy**.

    ![ID úlohy grafu aplikace Spark a grafu úlohy](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ Ve výchozím nastavení je vybraná možnost **průběh** . uživatel může kontrolu toku dat vybrat v rozevíracím seznamu **Zobrazit** **nebo zapsat** .

    ![Zobrazení aplikace Spark a grafu úlohy](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    Uzel grafu se zobrazí v barvě, která zobrazuje heatmapu.

    ![Heatmapu grafu aplikací a úloh Spark](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Přehrajte úlohu tak, že vyberete tlačítko pro **přehrávání** a ukončíte ho kliknutím na tlačítko Zastavit. Zobrazený úkol v barvě pro zobrazení jiného stavu při přehrávání:

    |Barva |Popis |
    |---|---|
    |Zelená|Úloha se úspěšně dokončila.|
    |Orange|Instance úloh, které selhaly, ale neovlivňují konečný výsledek úlohy. Tyto úlohy měly duplicitní nebo opakované instance, které mohou být později úspěšné.|
    |Modrá|Úloha je spuštěna.|
    |Bílá|Úloha čeká na spuštění, nebo byla fáze přeskočena.|
    |Červená|Úloha se nezdařila.|

    ![Ukázka barvy grafu aplikace Spark a grafu úlohy, která běží](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

    Vynechaná fáze se zobrazuje bíle.
    ![ukázka barvy grafu aplikace Spark a grafu úlohy, přeskočit](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Ukázka barvy grafu aplikace Spark a grafu úlohy, selhání](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

    > [!NOTE]  
    > Přehrávání jednotlivých úloh je povoleno. Pro nekompletní úlohu není přehrávání podporované.

+ Myš se posouvá a přiblíží se k přiblížení nebo zobrazení grafu úloh nebo klikněte na tlačítko **přiblížit** , aby se vešlo na obrazovku.

    ![Přiblížení grafu aplikace Spark a grafu úloh](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Když najedete myší na uzel grafu, zobrazí se popis tlačítka, když dojde k selhání úloh, a kliknutím na fáze na otevřít stránku fáze.

    ![Popis aplikace Spark a grafu úlohy](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ V případě, že se v grafu úlohy zobrazí následující podmínky, budou mít fáze popis tlačítka a malá ikona.
  + Zešikmení dat: velikost přečtených dat > Průměrná velikost čtení dat všech úkolů v této fázi * 2 a velikost pro čtení dat > 10 MB.
  + Časové zkosení: doba provádění > Průměrná doba provádění všech úkolů v této fázi * 2 a doba spuštění > 2 minuty.

    ![Ikona zkosení grafu aplikace Spark a grafu úlohy](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ Uzel grafu úlohy zobrazí následující informace o každé fázi:
  + Účet.
  + Název nebo popis
  + Celkové číslo úlohy
  + Přečtená data: součet velikosti vstupní velikosti a náhodné velikosti čtení.
  + Zápis dat: součet velikosti výstupu a náhodné velikosti zápisu.
  + Doba spuštění: čas mezi časem zahájení prvního pokusu a časem dokončení posledního pokusu.
  + Počet řádků: součet vstupních záznamů, výstupních záznamů, náhodného čtení záznamů a náhodného zápisu záznamů.
  + Přejde.

    > [!NOTE]  
    > Ve výchozím nastavení uzel grafu úlohy zobrazí informace z posledního pokusu o každou fázi (kromě doby spuštění fáze), ale během uzlu grafu přehrávání se zobrazí informace o každém pokusu.

    > [!NOTE]  
    > Pro velikost dat čtení a zápisu používáme 1 MB = 1000 KB = 1000 * 1000 bajtů.

+ Sdělte nám svůj názor s problémy tím, že si vyberete poslat **svůj názor**.

    ![Zpětná vazba grafu aplikace Spark a úlohy](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="diagnosis-tab-in-apache-spark-history-server"></a>Karta Diagnostika v serveru historie Apache Spark

Vyberte ID úlohy a potom v nabídce Nástroje vyberte **diagnóza** a získejte zobrazení diagnostiky úlohy. Karta Diagnostika zahrnuje analýzu **dat**, **zkosení času**a **analýzy využití prováděcího modulu**.

+ Vybíráním karet si Projděte přehledy o překrytí **dat**, **časovém intervalu**a **analýze využití prováděcího modulu** .

    ![SparkUI karta pro zkosení dat diagnostiky](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Zkosit data

Vyberte kartu **zkosení dat** . na základě zadaných parametrů se zobrazí odpovídající šikmé úkoly.

+ **Zadejte parametry** – první oddíl zobrazuje parametry, které se používají ke zjištění zkosení dat. Předdefinované pravidlo je: čtení dat úkolu je více než třikrát průměrně přečtených dat úkolu a přečtených dat úlohy je více než 10 MB. Pokud chcete definovat vlastní pravidlo pro úkoly, které jsou pro úlohy zkreslené, můžete zvolit parametry, oddíl **zkosených fází**a **znaků zkosení** se odpovídajícím způsobem aktualizuje.

+ **Zkosený stupeň** – druhý oddíl zobrazuje fáze, které mají úlohy, které splňují kritéria uvedená výše. Pokud ve fázi existuje více než jeden zkosený úkol, tabulka zkosených fází zobrazí pouze nejvýraznější úlohu (např. největší data pro zkosení dat).

    ![sparkui karta pro zkosení dat diagnostiky](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **Zkosit graf** – když je vybraný řádek v tabulce fáze zkosení, zobrazí se v grafu zkosení další podrobnosti o distribuci úloh na základě doby čtení a spuštění dat. Zkreslené úkoly jsou označeny červeně a normální úlohy jsou označeny modře. Z důvodu výkonu se v grafu zobrazí jenom 100 ukázkových úkolů. Podrobnosti o úloze se zobrazí v pravém dolním panelu.

    ![graf zkosení sparkui pro fázi 10](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Časové zkosení

Na kartě **časové zkosení** se zobrazují zkreslené úkoly na základě doby spuštění úkolu.

+ **Zadejte parametry** – první oddíl zobrazuje parametry, které se používají ke zjištění zešikmení času. Výchozí kritéria pro detekci časového zkosení jsou: doba provádění úlohy je větší než tři časy doby spuštění a doba provádění úlohy je větší než 30 sekund. Parametry můžete změnit podle svých potřeb. Graf **zkosených fází** a **zkosení** zobrazuje odpovídající informace o fázích a úlohách stejně jako karta pro **zkosení dat** výše.

+ Vyberte možnost **časové zkosení**a následně filtrovaný výsledek se zobrazí v části **zkosený fáze** podle parametrů nastavených v oddílu **určení parametrů**. Vyberte jednu položku v části **šikmá fáze** . odpovídající graf je koncept v section3 a podrobnosti úlohy se zobrazí v pravém dolním panelu.

    ![oddíl asymetrie doby diagnostiky sparkui](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Analýza využití prováděcího modulu

Graf využití prováděcího modulu, který vizualizuje skutečné přidělení prováděcího modulu úlohy Spark a stav spuštění.  

+ Vyberte možnost **Analýza využití prováděcího modulu**, pak čtyři typy křivky o použití prováděcího modulu jsou koncepty, včetně **přidělených prováděcích**modulů, **spuštěných**prováděcích modulů, **nečinných prováděcích**modulů a **maximálního počtu instancí prováděcí** U přidělených prováděcích modulů dojde ke zvýšení nebo snížení přidělených prováděcích modulů každý "prováděcí modul" nebo "exekutor" odebraný ", a proto můžete na kartě úlohy zaškrtnout možnost časová osa události.

    ![karta prováděcích modulů diagnostiky sparkui](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ Vyberte ikonu barvy a vyberte nebo zrušte výběr odpovídajícího obsahu ve všech konceptech.

    ![sparkui – výběr grafu pro diagnostiku](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>Časté otázky

### <a name="1-revert-to-community-version"></a>1. vrátit se k verzi komunity

Chcete-li se vrátit k verzi komunity, proveďte následující kroky:

1. Otevřete cluster v Ambari.
1. Přejděte na **Spark2** > **config** > **Custom Spark2-Defaults**.
1. Vyberte **Přidat vlastnost...** , přidejte **Spark. UI. vylepšení. Enabled = FALSE**, uložte.
1. Vlastnost nastaví na **hodnotu false** nyní.
1. Kliknutím na **Uložit** uložte konfiguraci.

    ![Funkce Apache Ambari se vypne.](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. V levém panelu vyberte **Spark2** , na kartě **Souhrn** vyberte **Server historie Spark2**.

    ![Souhrnné zobrazení Apache Ambari Spark2](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. Restartujte server historie výběrem možnosti **restartovat** **Server historie Spark2**.

    ![Restart Ambari Spark2 pro Apache](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  
1. Aktualizuje webové uživatelské rozhraní serveru historie Sparku, bude se vrátit na verzi komunity.

### <a name="2-upload-history-server-event"></a>2. odeslání události serveru historie

Pokud se při spuštění do historie serveru zobrazí chyba, postupujte podle pokynů pro zadání této události:

1. Stáhněte událost tak, že vyberete **Stáhnout** ve webovém uživatelském rozhraní serveru historie.

    ![Stažení serveru historie Spark2](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Vyberte možnost **poskytnout nám svůj názor** z karty data/graf.

    ![Sparkový graf nám poskytuje svůj názor](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Zadejte název a popis chyby, přetáhněte soubor zip do pole upravit a pak klikněte na **Odeslat nový problém**.

    ![Příklad problému se souborem Apache Spark](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. upgrade souboru jar pro scénář oprav hotfix

Pokud chcete upgradovat pomocí opravy hotfix, použijte následující skript, který bude upgradovat Spark-Enhancement. jar *.

**upgrade_spark_enhancement. sh**:

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

**Použití**:

`upgrade_spark_enhancement.sh https://${jar_path}`

**Příklad**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

**Použití souboru bash z Azure Portal**

1. Spusťte [Azure Portal](https://ms.portal.azure.com)a vyberte svůj cluster.
2. Dokončete [akci skriptu](../hdinsight-hadoop-customize-cluster-linux.md) s následujícími parametry:

    |Vlastnost |Hodnota |
    |---|---|
    |Typ skriptu|– Vlastní|
    |Name (Název)|UpgradeJar|
    |Identifikátor URI skriptu bash|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Typ (typy) uzlů|Vedoucí pracovník|
    |Parametry|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Azure Portal odeslat akci skriptu](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Známé problémy

+ V současné době funguje pouze pro cluster Spark 2,3 a 2,4.

+ Vstupní/výstupní data pomocí RDD se nezobrazují na kartě data.

## <a name="next-steps"></a>Další kroky

+ [Správa prostředků pro cluster Apache Spark v HDInsight](apache-spark-resource-manager.md)
+ [Konfigurace nastavení Apache Spark](apache-spark-settings.md)

## <a name="contact-us"></a>Kontaktujte nás

Pokud máte nějakou zpětnou vazbu nebo při použití tohoto nástroje máte nějaké problémy, pošlete e-mail na adresu ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)).
