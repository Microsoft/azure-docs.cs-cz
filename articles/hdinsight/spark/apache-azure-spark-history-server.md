---
title: Použití rozšířených Server historie Sparku k ladění a Diagnostika aplikací Spark – Azure HDInsight
description: Použití rozšířených Server historie Sparku k ladění a Diagnostika aplikací Spark – Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
editor: jasonwhowell
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2018
ms.openlocfilehash: df196286297c7f8961e58d2196554b00d0281c18
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622333"
---
# <a name="use-extended-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Použít rozšířený Server historie Sparku k ladění a Diagnostika aplikací Spark

Tento článek poskytuje pokyny, jak používat rozšířené Server historie Sparku k ladění a Diagnostika aplikací Spark dokončení a spuštěná. Rozšíření aktuálně obsahuje kartu data a graf. Na kartě data uživatelů můžete zkontrolovat vstupní a výstupní data úlohy Spark. V grafu kartu uživatelé kontrolovat tok dat a přehrát graf úlohy.

## <a name="open-the-spark-history-server"></a>Otevřete Server historie Sparku

Server historie Sparku je ve webovém uživatelském rozhraní pro dokončené a spuštěné aplikace Spark. 

### <a name="to-open-the-spark-history-server-web-ui-from-azure-portal"></a>Chcete-li otevřít rozhraní Web Server historie Sparku z webu Azure portal

1. Z [webu Azure portal](https://portal.azure.com/), otevřete Spark cluster. Další informace najdete v tématu [výpisu a zobrazení clusterů](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. Z **rychlé odkazy**, klikněte na tlačítko **řídicí panel clusteru**a potom klikněte na tlačítko **Server historie Sparku**. Po zobrazení výzvy zadejte přihlašovací údaje Správce clusteru Spark. 

    ![Server historie sparku](./media/apache-azure-spark-history-server/launch-history-server.png "Server historie Sparku")

### <a name="to-open-the-spark-history-server-web-ui-by-url"></a>Chcete-li otevřít Spark historie serveru webového uživatelského rozhraní pomocí adresy URL
Otevřete Server historie Sparku tak, že přejdete na následující adresu URL, nahraďte <ClusterName> s názvem clusteru Spark zákazníka.

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

Server historie Sparku webové uživatelské rozhraní vypadá takto:

![Server historie Sparku pro HDInsight](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)


## <a name="open-the-data-tab-from-spark-history-server"></a>Otevřete kartu dat z Server historie Sparku
Vyberte ID úlohy klikněte **Data** získat zobrazení dat v nabídce Nástroje.

+ Zkontrolujte **vstupy**, **výstupy**, a **operace s tabulkou** výběrem karty samostatně.

    ![Datové karty](./media/apache-azure-spark-history-server/sparkui-data-tabs.png)

+ Kliknutím na tlačítko Kopírovat všechny řádky **kopírování**.

    ![Kopírování dat](./media/apache-azure-spark-history-server/sparkui-data-copy.png)

+ Kliknutím na tlačítko Uložit všechna data jako soubor CSV **sdíleného svazku clusteru**.

    ![Data uložení](./media/apache-azure-spark-history-server/sparkui-data-save.png)

+ Hledat zadáním klíčových slov do pole **hledání**, výsledek hledání se zobrazí okamžitě.

    ![Vyhledávání dat](./media/apache-azure-spark-history-server/sparkui-data-search.png)

+ Klikněte na záhlaví sloupce řazení tabulku, klikněte na znaménko plus rozbalit řádek zobrazíte další podrobnosti, nebo klikněte na znaménko minus sbalit řádek.

    ![Tabulka dat](./media/apache-azure-spark-history-server/sparkui-data-table.png)

+ Kliknutím na tlačítko Stáhnout jednosouborová **částečné stažení** , umístěte na pravé straně pak vybraný soubor se stáhne do proměnné, pokud soubor neexistuje žádné další, otevře se nová karta, chcete-li zobrazit chybové zprávy.

    ![Řádek dat ke stažení](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Kopírovat úplnou cestu nebo relativní cesty tak, že vyberete **Kopírovat úplnou cestu**, **kopírovat relativní cestu** , který rozbalí v nabídce ke stažení. Azure data lake storage souborů **otevřít v Průzkumníku služby Storage** spusťte Průzkumníka služby Azure Storage a vyhledejte složky při přihlášení.

    ![Kopírovat cestu k datům](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Klikněte na číslo pod tabulkou navigace stránky v těchto případech příliš mnoho řádků k zobrazení na jedné stránce. 

    ![Data stránky](./media/apache-azure-spark-history-server/sparkui-data-page.png)

+ Najeďte myší na otazník, vedle dat, čímž se zobrazí popisek, nebo klikněte na tlačítko Nápověda získáte další informace.

    ![Dat. Další informace](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Kliknutím na odeslat zpětnou vazbu s problémy **nám poskytnout zpětnou vazbu**.

    ![zpětnou vazbu grafu](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="open-the-graph-tab-from-spark-history-server"></a>Otevřete kartu grafu z Server historie Sparku
Vyberte ID úlohy klikněte **grafu** získat zobrazení grafu úlohy v nabídce Nástroje.

+ Graf úlohy generované a zkontrolujte přehled vaší úlohy. 

+ Ve výchozím nastavení, zobrazí se všechny úlohy, a může být filtrovaná podle **ID úlohy**.

    ![ID grafu úlohy](./media/apache-azure-spark-history-server/sparkui-graph-jobid.png)

+ Ve výchozím nastavení **průběh** je vybrána, uživatel může zkontrolujte tok dat tak, že vyberete **čtení/Written** v rozevíracího seznamu **zobrazení**.

    ![zobrazení grafu](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    Zobrazení uzlu grafu barvou, který zobrazuje Heat mapě.

    ![Graf heatmapu](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Přehrání úlohy po kliknutí **přehrávání** tlačítko a kdykoli ukončit kliknutím na tlačítko Zastavit. Zobrazení úloh barvou, chcete-li zobrazit různé stavy při přehrávání:

    + Zelená pro úspěšné: Úloha byla úspěšně dokončena.
    + Oranžové pro opakované: instance úlohy, které selhaly, ale nemají vliv na konečný výsledek úlohy. Tyto úlohy měl duplicitní nebo instance opakování, které mohou být úspěšně dokončeny později.
    + Červené se nezdařilo: úloha se nezdařila.
    + Modrá pro spuštění: Úloha je spuštěna.
    + Bílé pro přeskočeno nebo čeká: Úloha čeká na spuštění nebo fázi byl přeskočen.

    ![ukázka barvy grafu, spuštění](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)
 
    ![ukázka barvy Graph, se nezdařilo](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)
 
    > [!NOTE]
    > Je povolené přehrávání pro každou úlohu. Pokud úloha nemá žádné fáze nebo nebyla dokončena, přehrávání se nepodporuje.


+ Posune myši přiblížení vstup a výstup graf úlohy, nebo klikněte na **přizpůsobit** k němu přizpůsobit na obrazovku.
 
    ![Lupa grafu podle](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Najeďte myší na uzlu grafu zobrazíte popis tlačítka, když nejsou úloh, které selhaly a klikněte na fázi otevřete stránku fázi.

    ![Popis grafu](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Uzel grafu úlohy se zobrazí následující informace každou z fází:
    + ID.
    + Název nebo popis.
    + Celkový počet úloh číslo.
    + Data načtená: velikost čtení součet velikost vstupu a náhodně.
    + Zápis dat: součet velikost výstupu a shuffle velikost pro zápis.
    + Doba provádění: čas mezi časem spuštění prvního pokusu a čas dokončení posledního pokusu o.
    + Počet řádků: součet vstupní záznamy výstupní záznamy, náhodné čtení záznamů a náhodné zápis záznamů.
    + Průběh.

    > [!NOTE]
    > Ve výchozím nastavení uzlu grafu úlohy se zobrazí informace z posledního pokusu každou z fází (s výjimkou doba provedení fáze), ale během přehrávání grafu se zobrazí uzel informace každého pokusu.

    > [!NOTE]
    > Pro velikost dat pro čtení a zápisu používáme 1 MB = 1 000 KB = 1 000 * 1 000 bajtů.

+ Kliknutím na odeslat zpětnou vazbu s problémy **nám poskytnout zpětnou vazbu**.

    ![zpětnou vazbu grafu](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="faq"></a>Nejčastější dotazy

### <a name="1-revert-to-community-version"></a>1. Vrátit k verzi komunity

Pokud chcete vrátit na komunitní verze, proveďte následující kroky:

1. Otevřete cluster v Ambari. Klikněte na tlačítko **Spark2** v levém panelu.
2. Klikněte na tlačítko **Configs** kartu.
3. Rozbalte skupinu **vlastní spark2 – výchozí hodnoty**.
4. Klikněte na tlačítko **přidat vlastnost**, přidejte **spark.ui.enhancement.enabled=false**, uložte.
5. Vlastnost nastaví na **false** nyní.
6. Klikněte na tlačítko **Uložit** uložte konfiguraci.

    ![vypne funkce](./media/apache-azure-spark-history-server/sparkui-turn-off.png)

7. Klikněte na tlačítko **Spark2** v zůstane panelu, v části **Souhrn** klikněte na tlačítko **Server historie Spark2**.

    ![Restartujte server1](./media/apache-azure-spark-history-server/sparkui-restart-1.png) 

8. Restartujte server historie kliknutím **restartovat** z **Server historie Spark2**.

    ![Restartujte server2](./media/apache-azure-spark-history-server/sparkui-restart-2.png)  

9. Aktualizovat webové server historie Sparku uživatelského rozhraní, budou se vrátí zpátky na komunitní verze.

### <a name="2-upload-history-server-event"></a>2. Odeslat událost serveru historie

Pokud narazíte na chybu serveru historie, postupujte podle kroků poskytnutí události:
1. Kliknutím stáhněte události **Stáhnout** historie serveru webového uživatelského rozhraní.

    ![Stáhnout událost](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Klikněte na tlačítko **nám poskytnout zpětnou vazbu** z karty data/grafu.

    ![zpětnou vazbu grafu](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Zadejte název a popis chyby, přetáhněte soubor zip do textového pole a klikněte na tlačítko **odeslat nový problém**.

    ![soubor problém](./media/apache-azure-spark-history-server/sparkui-file-issue.png)


### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. Upgradovat soubor jar pro scénář oprav hotfix

Pokud chcete provést upgrade pomocí opravy hotfix, použijte níže uvedený skript, který bude upgradovat spark enhancement.jar*.

**upgrade_spark_enhancement.SH**:

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

**Využití**: 

`upgrade_spark_enhancement.sh https://${jar_path}`

**Příklad**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.tgz` 

**Použít bash soubor z webu Azure portal**

1. Spuštění [webu Azure Portal](https://ms.portal.azure.com)a vyberte svůj cluster.
2. Klikněte na tlačítko **akcí skriptů**, pak **odeslat novou**. Dokončení **odeslat akci skriptu** formulář a potom klikněte na **vytvořit** tlačítko.
    
    + **Typ skriptu**: vyberte **vlastní**.
    + **Název**: Zadejte název skriptu.
    + **URI skriptu bash**: nahrání souboru bash pro soukromý cluster pak zkopírujte adresu URL zde. Můžete také použijte zadaný identifikátor URI.
    
   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

    + Podívejte se na **Head** a **pracovního procesu**.
    + **Parametry**: nastavit podle parametrů využití prostředí bash.

    ![Nahrát protokoly nebo upgrade opravy hotfix](./media/apache-azure-spark-history-server/sparkui-upload2.png)


## <a name="known-issue"></a>Známý problém

1.  V současné době to funguje jenom pro cluster Spark 2.3.

2.  Vstupní a výstupní data pomocí RDD nebudou zobrazovat na kartě data.

## <a name="next-steps"></a>Další postup

* [Správa prostředků v clusteru Spark v HDInsight](apache-spark-resource-manager.md)
* [Konfigurace nastavení Sparku](apache-spark-settings.md)


## <a name="contact-us"></a>Kontaktujte nás

Pokud máte nějakou zpětnou vazbu, nebo pokud narazíte na jakékoli potíže při používání tohoto nástroje, pošlete e-mail na ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)).
