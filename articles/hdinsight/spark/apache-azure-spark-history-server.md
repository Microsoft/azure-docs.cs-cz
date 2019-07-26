---
title: Použití rozšířeného serveru pro historii Spark k ladění a diagnostice aplikací Spark – Azure HDInsight
description: Použití rozšířeného serveru pro historii Spark k ladění a diagnostice aplikací Spark – Azure HDInsight.
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 641fc41c25746a67b4b1fe3d5316df17f14f113c
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377257"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Použití serveru historie rozšířených Apache Spark k ladění a diagnostice Apache Spark aplikací

Tento článek popisuje, jak používat server pro rozšířené Apache Spark pro ladění a diagnostikování dokončených a spuštěných aplikací Spark. Rozšíření zahrnuje kartu data a kartu graf a diagnostika. Na kartě **data** mohou uživatelé kontrolovat vstupní a výstupní data úlohy Spark. Na kartě **graf** mohou uživatelé kontrolovat tok dat a znovu přehrát graf úlohy. Na kartě **Diagnostika** může uživatel odkazovat na překrytí **dat**, **časově zkosit** a **analýzu využití prováděcího modulu**.

## <a name="get-access-to-apache-spark-history-server"></a>Získat přístup k serveru historie Apache Spark

Server historie Apache Spark je webové uživatelské rozhraní pro kompletní a běžící aplikace Spark. 

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>Otevřete webové uživatelské rozhraní serveru Apache Spark historie z Azure Portal

1. Z [Azure Portal](https://portal.azure.com/)otevřete cluster Spark. Další informace najdete v tématech [seznam a zobrazení clusterů](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Z **panelu Rychlé odkazy**klikněte na **řídicí panel clusteru**a pak klikněte na **Server historie Spark**. Po zobrazení výzvy zadejte přihlašovací údaje správce pro cluster Spark. 

    ![Server historie Sparku](./media/apache-azure-spark-history-server/launch-history-server.png "Server historie Sparku")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Otevřete webové uživatelské rozhraní serveru historie Sparku podle adresy URL
Přejděte na následující adresu URL, kde otevřete Server historie Spark, který `<ClusterName>` se nahrazuje názvem clusteru Spark Customer.

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

Webové uživatelské rozhraní serveru historie Sparku vypadá takto:

![Server historie HDInsight Spark](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)


## <a name="data-tab-in-spark-history-server"></a>Karta data v historii serveru Spark
Vyberte ID úlohy a potom v nabídce Nástroje klikněte na **data** , aby se zobrazila data.

+ Zkontrolujte **vstupy**a výstupy a **operace s tabulkami** , a to tak, že karty vyberete samostatně.

    ![Karty dat](./media/apache-azure-spark-history-server/sparkui-data-tabs.png)

+ Kliknutím na tlačítko **Kopírovat**zkopírujte všechny řádky.

    ![Kopírování dat](./media/apache-azure-spark-history-server/sparkui-data-copy.png)

+ Uložte všechna data jako soubor CSV kliknutím na tlačítko **CSV**.

    ![Uložení dat](./media/apache-azure-spark-history-server/sparkui-data-save.png)

+ Hledání je prováděno zadáním klíčových slov do pole **hledání**, výsledek hledání se zobrazí okamžitě.

    ![Hledání dat](./media/apache-azure-spark-history-server/sparkui-data-search.png)

+ Kliknutím na záhlaví sloupce seřadíte tabulku, kliknutím na znaménko plus rozbalíte řádek a zobrazíte další podrobnosti, nebo kliknutím na znaménko mínus řádek sbalíte.

    ![Tabulka dat](./media/apache-azure-spark-history-server/sparkui-data-table.png)

+ Stažení jednoho souboru kliknutím na tlačítko **částečné stažení** , které je napravo, pak bude vybraný soubor stažen do místního umístění, pokud soubor již neexistuje, otevře novou kartu pro zobrazení chybových zpráv.

    ![Řádek pro stažení dat](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Úplnou cestu nebo relativní cestu zkopírujte výběrem možnosti **Kopírovat úplnou cestu**a **Kopírovat relativní cestu** , která se rozbalí z nabídky stáhnout. Pro soubory Azure Data Lake Storage **otevřete v Průzkumník služby Azure Storage** spustí Průzkumník služby Azure Storage a po přihlášení Najděte složku do složky.

    ![Cesta pro kopírování dat](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Kliknutím na číslo pod tabulkou můžete procházet stránky, když se na jedné stránce zobrazí příliš mnoho řádků. 

    ![Datová stránka](./media/apache-azure-spark-history-server/sparkui-data-page.png)

+ Poklikáním na otazník vedle položky data zobrazíte popis tlačítka nebo kliknutím na otazník získáte další informace.

    ![Další informace o datech](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Kliknutím na **poskytnout zpětnou**vazbu pošlete nám problémy s problémy.

    ![názor na graf](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="graph-tab-in-apache-spark-history-server"></a>Karta graf na serveru historie Apache Spark
Vyberte ID úlohy a potom kliknutím na **graf** v nabídce nástroje Získejte zobrazení grafu úlohy.

+ Přehled úlohy najdete v grafu vygenerované úlohy. 

+ Ve výchozím nastavení se zobrazí všechny úlohy a můžete je filtrovat podle **ID úlohy**.

    ![ID úlohy grafu](./media/apache-azure-spark-history-server/sparkui-graph-jobid.png)

+ Ve výchozím nastavení je vybraná možnost **průběh** . uživatel může kontrolu toku dat vybrat v rozevíracím seznamu **Zobrazit** **nebo zapsat** .

    ![zobrazení grafu](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    Uzel grafu se zobrazí v barvě, která zobrazuje heatmapu.

    ![heatmapu grafu](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Začněte tím, že kliknete na tlačítko **přehrávání** a kdykoli kliknete na tlačítko Zastavit. Zobrazený úkol v barvě pro zobrazení jiného stavu při přehrávání:

  + Zelená pro úspěšná: Úloha se úspěšně dokončila.
  + Oranžová pro opakování: Instance úloh, které selhaly, ale neovlivňují konečný výsledek úlohy. Tyto úlohy měly duplicitní nebo opakované instance, které mohou být později úspěšné.
  + Modrá pro běh: Úloha je spuštěna.
  + Bílá pro čekání nebo přeskočení: Úloha čeká na spuštění, nebo byla fáze přeskočena.
  + Červená neúspěšná: Úloha se nezdařila.

    ![Ukázka barvy grafu, spuštění](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)
 
    Vynechaná fáze se zobrazuje bíle.
    ![Ukázka barvy grafu, přeskočení](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Ukázka barvy grafu, selhání](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)
 
    > [!NOTE]  
    > Přehrávání jednotlivých úloh je povoleno. Pro nekompletní úlohu není přehrávání podporované.


+ Myš se posouvá a přiblíží se k přiblížení nebo zobrazení grafu úloh nebo  klikněte na tlačítko přiblížit, aby se vešlo na obrazovku.
 
    ![přizpůsobit zobrazení grafu](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Když najedete myší na uzel grafu, zobrazí se popis tlačítka, když dojde k selhání úloh, a kliknutím na fáze na otevřít stránku fáze.

    ![Popis grafu](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ V případě, že se v grafu úlohy zobrazí následující podmínky, budou mít fáze popis tlačítka a malá ikona.
  + Zešikmení dat: velikost přečtených dat > Průměrná velikost čtení dat všech úkolů v této fázi * 2 a velikost pro čtení dat > 10 MB.
  + Časové zkosení: doba provádění > Průměrná doba provádění všech úkolů v této fázi * 2 a doba spuštění > 2 minuty.

    ![ikona zkosení grafu](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ Uzel grafu úlohy zobrazí následující informace o každé fázi:
  + ID.
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

+ Kliknutím na **poskytnout zpětnou**vazbu pošlete nám problémy s problémy.

    ![názor na graf](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="diagnosis-tab-in-apache-spark-history-server"></a>Karta Diagnostika v serveru historie Apache Spark
Vyberte ID úlohy a potom  kliknutím na diagnóza v nabídce nástroje Získejte zobrazení diagnostiky úlohy. Karta Diagnostika zahrnuje analýzu **dat**, **zkosení času**a **analýzy využití prováděcího modulu**.
    
+ Výběrem karet v uvedeném pořadí ověřte, jak se mají **Přezkosit data**, **Zkosit**a **analyzovat využití prováděcích** modulů.

    ![Karty diagnostiky](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Zkosit data
Klikněte na kartu **zkosení dat** . na základě zadaných parametrů se zobrazí odpovídající šikmé úkoly. 

+ **Zadejte parametry** – první oddíl zobrazuje parametry, které se používají ke zjištění zkosení dat. Předdefinované pravidlo je: Přečtená data úkolu jsou delší než 3 časy průměrných čtených dat úkolu a přečtená data jsou větší než 10 MB. Pokud chcete definovat vlastní pravidlo pro úkoly, které jsou pro úlohy zkreslené, můžete zvolit parametry, oddíl **zkosených fází**a **znaků zkosení** se odpovídajícím způsobem aktualizuje.

+ **Zkosený stupeň** – druhý oddíl zobrazuje fáze, které mají úkoly, které splňují kritéria uvedená výše. Pokud ve fázi existuje více než jeden zkosený úkol, tabulka zkosených fází zobrazí pouze nejvýraznější úlohu (např. největší data pro zkosení dat).

    ![Section2í dat – zkosit](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **Zkosit graf** – když je vybraný řádek v tabulce fáze zkosení, zobrazí se v grafu zkosení další podrobnosti o distribuci úloh na základě doby čtení a spuštění dat. Zkreslené úkoly jsou označeny červeně a normální úlohy jsou označeny modře. Z důvodu výkonu se v grafu zobrazí jenom 100 ukázkových úkolů. Podrobnosti o úloze se zobrazí v pravém dolním panelu.

    ![Section3í dat – zkosit](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Časové zkosení
Na kartě **časové zkosení** se zobrazují zkreslené úkoly na základě doby spuštění úkolu. 

+ **Zadejte parametry** – první oddíl zobrazuje parametry, které se používají ke zjištění zešikmení času. Výchozí kritéria pro detekci časového zkosení jsou: doba provádění úlohy je větší než 3 krát Průměrná doba spuštění a doba provádění úlohy je větší než 30 sekund. Parametry můžete změnit podle svých potřeb. Graf **zkosených fází** a **zkosení** zobrazuje odpovídající informace o fázích a úlohách stejně jako karta pro **zkosení dat** výše.

+ Klikněte na možnost **Časová zkosená**a následně filtrovaný výsledek se zobrazí v části **zkosený fáze** podle parametrů nastavených v oddílu **určení parametrů**. Klikněte na jednu položku v části **šikmá fáze** a pak se na odpovídající graf připravuje v section3 a podrobnosti úlohy se zobrazí v pravém dolním panelu.

    ![Section2 časové zkosení](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Analýza využití prováděcího modulu
Graf využití prováděcího modulu, který vizualizuje skutečné přidělení prováděcího modulu úlohy Spark a stav spuštění.  

+ Klikněte na možnost **Analýza využití prováděcího modulu**, potom čtyři typy křivky o použití prováděcího modulu jsou koncepty, včetně **přidělených prováděcích**modulů, **spuštěných**prováděcích modulů, nečinných prováděcích modulů a **maximálního počtu instancí** U přidělených prováděcích modulů dojde ke zvýšení nebo snížení přidělených prováděcích modulů každý "prováděcí modul" nebo "exekutor" odebraný ", a proto můžete na kartě úlohy zaškrtnout možnost časová osa události.

    ![Karta prováděcích modulů](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ Kliknutím na ikonu barvy vyberte nebo zrušte výběr odpovídajícího obsahu ve všech konceptech.

    ![Vybrat graf](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)


## <a name="faq"></a>Nejčastější dotazy

### <a name="1-revert-to-community-version"></a>1. Vrátit se k verzi komunity

Chcete-li se vrátit k verzi komunity, proveďte následující kroky:

1. Otevřete cluster v Ambari. Klikněte na **Spark2** na levém panelu.
2. Klikněte na kartu **Konfigurace** .
3. Rozbalte **vlastní spark2 skupiny – výchozí**.
4. Klikněte na **Přidat vlastnost**, přidejte **Spark. UI. vylepšení. Enabled = FALSE**, uložte.
5. Vlastnost nastaví na **hodnotu false** nyní.
6. Kliknutím na **Uložit** uložte konfiguraci.

    ![funkce se vypne.](./media/apache-azure-spark-history-server/sparkui-turn-off.png)

7. Klikněte na **Spark2** na levém panelu a na kartě **Souhrn** klikněte na **Server historie Spark2**.

    ![restartovat Server1](./media/apache-azure-spark-history-server/sparkui-restart-1.png) 

8. Kliknutím na **restartovat** **Server historie Spark2**restartujte server historie.

    ![restartovat Server2](./media/apache-azure-spark-history-server/sparkui-restart-2.png)  

9. Aktualizuje webové uživatelské rozhraní serveru historie Sparku, bude se vrátit na verzi komunity.

### <a name="2-upload-history-server-event"></a>2. Událost serveru pro odeslání historie

Pokud se při spuštění do historie serveru zobrazí chyba, postupujte podle pokynů pro zadání této události:
1. Událost ke stažení kliknutím na **Stáhnout** ve webovém uživatelském rozhraní serveru historie.

    ![událost stažení](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Klikněte na tlačítko **poskytnout zpětnou vazbu** z karty data/graf.

    ![názor na graf](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Zadejte název a popis chyby, přetáhněte soubor zip do pole upravit a pak klikněte na **Odeslat nový problém**.

    ![problém se souborem](./media/apache-azure-spark-history-server/sparkui-file-issue.png)


### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. Upgrade souboru jar pro scénář oprav hotfix

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
2. Klikněte na **akce skriptu**a pak na **Odeslat novou**. Vyplňte formulář **Odeslat akci skriptu** a klikněte na tlačítko **vytvořit** .
    
    + **Typ skriptu**: vyberte **vlastní**.
    + **Název**: zadejte název skriptu.
    + **Bash skriptu URI**: Nahrajte soubor bash do privátního clusteru a pak sem zkopírujte adresu URL. Případně použijte poskytnutý identifikátor URI.
    
   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

   + Podívejte se na **vedoucí** a **pracovní proces**.
   + **Parametry**: Nastavte parametry podle bash využití.

     ![Odeslat protokol nebo upgradovat opravu hotfix](./media/apache-azure-spark-history-server/sparkui-upload2.png)


## <a name="known-issues"></a>Známé problémy

1.  V současné době funguje pouze pro cluster Spark 2,3 a 2,4.

2.  Vstupní/výstupní data pomocí RDD se nezobrazí na kartě data.

## <a name="next-steps"></a>Další kroky

* [Správa prostředků pro cluster Apache Spark v HDInsight](apache-spark-resource-manager.md)
* [Konfigurace nastavení Apache Spark](apache-spark-settings.md)


## <a name="contact-us"></a>Kontaktujte nás

Pokud máte nějakou zpětnou vazbu nebo pokud narazíte na jiné problémy při používání tohoto nástroje, pošlete e-mail na adresu[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)().
