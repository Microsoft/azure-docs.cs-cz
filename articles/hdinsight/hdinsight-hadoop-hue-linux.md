---
title: Odstín pomocí Hadoop v clusterech se systémem HDInsight Linux – Azure
description: Naučte se instalovat odstín v clusterech HDInsight a pomocí tunelového propojení směrovat požadavky do odstínu. Pomocí odstínu můžete procházet úložiště a spustit podregistr nebo prase.
keywords: odstín Hadoop
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: hrasheed
ms.openlocfilehash: 67f338b583ef428b8dd04e859a5204fd708ce434
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962013"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Instalace a použití odstínu v clusterech HDInsight Hadoop

Naučte se instalovat odstín v clusterech HDInsight a pomocí tunelového propojení směrovat požadavky do odstínu.

## <a name="what-is-hue"></a>Co je odstín?
Odstín je sada webových aplikací používaných pro interakci s clusterem Apache Hadoop. Pomocí odstínu můžete procházet úložiště přidružené k clusteru Hadoop (WASB, v případě clusterů HDInsight), spouštět úlohy podregistru a skripty pro vepřové prostředí a tak dále. Následující komponenty jsou k dispozici s odstínem instalací v clusteru HDInsight Hadoop.

* Editor podregistru beeswax
* Apache Pig
* Metastore Manager
* Apache Oozie
* Prohlížeč (který mluví s WASB výchozím kontejnerem)
* Prohlížeč úloh

> [!WARNING]  
> Komponenty dodávané s clusterem HDInsight jsou plně podporované a podpora Microsoftu vám pomůžou izolovat a řešit problémy související s těmito součástmi.
>
> Vlastní komponenty získají komerčně přiměřenou podporu, která vám může pomoct s dalším řešením tohoto problému. To může vést k vyřešení problému nebo požádá vás o zapojení dostupných kanálů pro technologie Open Source, ve kterých se najde hlubokou odbornost pro danou technologii. Například existuje mnoho webů komunity, které lze použít, například: [Fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Projekty Apache také obsahují projektové weby [https://apache.org](https://apache.org), například: [Hadoop](https://hadoop.apache.org/).
>
>

## <a name="install-hue-using-script-actions"></a>Instalace odstínu pomocí akcí skriptů

Skript pro instalaci odstínu v clusteru HDInsight se systémem Linux je k dispozici na adrese https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Tento skript můžete použít k instalaci odstínu v clusterech s buď Azure Storage objekty BLOB (WASB), nebo Azure Data Lake Storage jako výchozí úložiště.

V této části najdete pokyny, jak skript použít při zřizování clusteru pomocí Azure Portal.

> [!NOTE]  
> Azure PowerShell, rozhraní příkazového řádku Azure Classic, sadu HDInsight .NET SDK nebo šablony Azure Resource Manager můžete použít také k aplikování akcí skriptu. Můžete také použít akce skriptů pro již spuštěné clustery. Další informace najdete v tématu [Přizpůsobení clusterů HDInsight pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Spusťte zřizování clusteru pomocí postupu v části [zřízení clusterů HDInsight v systému Linux](hdinsight-hadoop-provision-linux-clusters.md), ale Nedokončujte zřizování.

   > [!NOTE]  
   > K instalaci odstínu u clusterů HDInsight je doporučená velikost hlavnímu uzlu aspoň A4 (8 jader, 14 GB paměti).
   >
   >
2. V okně **volitelná konfigurace** vyberte **akce skriptu**a zadejte informace, jak je znázorněno níže:

    ![Zadání parametrů akce skriptu pro odstín](./media/hdinsight-hadoop-hue-linux/hdi-hue-script-action.png "Zadání parametrů akce skriptu pro odstín")

   * **NÁZEV**: Zadejte popisný název akce skriptu.
   * **IDENTIFIKÁTOR URI SKRIPTU**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **POZICE**: Tuto možnost Ověřte.
   * **PRACOVNÍ PROCES**: Nechte prázdné.
   * **ZOOKEEPER**: Nechte prázdné.
   * **PARAMETRY**: Nechte prázdné.
3. V dolní části **akcí skriptu**uložte konfiguraci pomocí tlačítka pro **Výběr** . Nakonec použijte tlačítko **Vybrat** v dolní části okna **volitelná konfigurace** a uložte volitelné informace o konfiguraci.
4. Pokračujte ve zřizování clusteru, jak je popsáno v tématu [zřizování clusterů HDInsight v systému Linux](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="use-hue-with-hdinsight-clusters"></a>Použití odstínu u clusterů HDInsight

Tunelové propojení SSH je jediným způsobem, jak ke stínům v clusteru přistupovat po jeho spuštění. Tunelové propojení prostřednictvím SSH umožňuje provoz přejít přímo na hlavnímu uzlu clusteru, kde je spuštěný odstín. Po dokončení zřizování clusteru použijte následující postup k použití odstínu v clusteru HDInsight Linux.

> [!NOTE]  
> Doporučujeme použít webový prohlížeč Firefox a postupovat podle následujících pokynů.
>
>

1. Použijte informace v části [použití tunelového propojení SSH pro přístup k webovému uživatelskému rozhraní Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie a jinému webovému uživatelskému rozhraní](hdinsight-linux-ambari-ssh-tunnel.md) k vytvoření tunelu SSH z klientského systému do clusteru HDInsight a pak nakonfigurujte webový prohlížeč tak, aby používal Tunel SSH jako proxy

2. Po vytvoření tunelu SSH a nakonfigurování prohlížeče na provoz proxy prostřednictvím je nutné najít název hostitele primárního hlavního uzlu. Můžete to udělat tak, že se připojíte ke clusteru pomocí SSH na portu 22. Například `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` Pokud **uživatelské** jméno je vaše uživatelské jméno SSH a **název_clusteru** je název vašeho clusteru.

    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Po připojení k získání plně kvalifikovaného názvu domény primárního hlavnímu uzlu použijte následující příkaz:

        hostname -f

    Tato akce vrátí název podobný následujícímu:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Toto je název hostitele primární hlavnímu uzlu, kde se nachází web odstín.
4. Pomocí prohlížeče otevřete portál pro odstínování na adrese http\/:/hostname: 8888. Nahraďte název hostitele názvem, který jste získali v předchozím kroku.

   > [!NOTE]  
   > Při prvním přihlášení se zobrazí výzva k vytvoření účtu pro přihlášení k portálu odstínování. Přihlašovací údaje, které tady zadáte, budou omezené na portál a nesouvisejí s přihlašovacími údaji uživatele správce nebo SSH, které jste zadali při zřizování clusteru.
   >
   >

    ![Přihlášení k portálu pro Odstínování](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Zadat přihlašovací údaje pro portál odstínů")

### <a name="run-a-hive-query"></a>Spuštění dotazu Hive
1. Na portálu pro odstínování klikněte na **editory dotazů**a potom kliknutím na **podregistr** otevřete Editor podregistru.

    ![Použít podregistr](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Použít podregistr")
2. Na kartě **pomoc** v části **databáze**by se měla zobrazit **hivesampletable**. Toto je ukázková tabulka, která se dodává se všemi clustery Hadoop v HDInsight. Do pravého podokna zadejte ukázkový dotaz a na kartě **výsledky** v podokně níže se zobrazí výstup, jak je znázorněno na snímku obrazovky.

    ![Spustit dotaz na podregistr](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Spustit dotaz na podregistr")

    Můžete také použít kartu **graf** k zobrazení vizuální reprezentace výsledku.

### <a name="browse-the-cluster-storage"></a>Procházení úložiště clusteru
1. Z portálu pro odstínování klikněte na **Prohlížeč souborů** v pravém horním rohu řádku nabídek.
2. Ve výchozím nastavení se prohlížeč souborů otevře v adresáři **/User/MyUser** . Klikněte na lomítko přímo před adresářem uživatelů v cestě, abyste přešli do kořenového adresáře kontejneru úložiště Azure přidruženého ke clusteru.

    ![Použití prohlížeče souborů](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Použití prohlížeče souborů")
3. Kliknutím pravým tlačítkem na soubor nebo složku zobrazíte dostupné operace. Pomocí tlačítka **nahrát** v pravém horním rohu nahrajte soubory do aktuálního adresáře. Pomocí tlačítka **Nový** vytvořte nové soubory nebo adresáře.

> [!NOTE]  
> Prohlížeč souborů odstínu může zobrazit jenom obsah výchozího kontejneru přidruženého ke clusteru HDInsight. Žádné další účty úložiště nebo kontejnery, které jste mohli přidružit ke clusteru, nebudou přístupné pomocí prohlížeče souborů. Další kontejnery spojené s clusterem budou ale vždy přístupné pro úlohy podregistru. Pokud například zadáte příkaz `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` v editoru podregistru, můžete zobrazit také obsah dalších kontejnerů. V tomto příkazu není **newcontainer** výchozím kontejnerem přidruženým ke clusteru.
>
>

## <a name="important-considerations"></a>Důležité informace
1. Skript použitý k instalaci odstínu se nainstaluje jenom na primární hlavnímu uzlu clusteru.

2. Během instalace se restartuje více služeb Hadoop (HDFS, PŘÍZe, MR2, Oozie), aby se aktualizovala konfigurace. Až se skript dokončí instalací odstínu, může trvat nějakou dobu, než se spustí další služby Hadoop. To může mít na začátku vliv na výkon odstínování. Po spuštění všech služeb bude odstín plně funkční.
3. Odstín nerozumí Apache Tez úlohám, což je aktuální výchozí nastavení pro podregistr. Pokud chcete jako spouštěcí modul podregistru použít MapReduce, aktualizujte skript tak, aby ve skriptu použil následující příkaz:

        set hive.execution.engine=mr;

4. U clusterů se systémem Linux můžete mít scénář, ve kterém jsou služby spuštěné na primárním hlavnímu uzlu, zatímco Správce prostředků může běžet na sekundárním počítači. V takovém případě může dojít k chybám (zobrazené níže) při použití funkce odstín k zobrazení podrobností o SPUŠTĚNých úlohách v clusteru. Po dokončení úlohy však můžete zobrazit podrobnosti o úloze.

   ![Chyba portálu pro Odstínování](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Chyba portálu pro Odstínování")

   Důvodem je známý problém. Jako alternativní řešení upravte Ambari tak, aby se aktivní Správce prostředků taky spouštěla na primárním hlavnímu uzlu.
5. Odstín rozumí WebHDFS, zatímco clustery HDInsight používají Azure Storage `wasb://`pomocí. Vlastní skript, který se používá pro akci skriptu, nainstaluje WebWasb, což je služba kompatibilní s WebHDFS pro komunikaci s WASB. Takže i když se na portálu pro odstíny říká HDFS (například když přesunete myš přes **Prohlížeč souborů**), mělo by se interpretovat jako WASB.

## <a name="next-steps"></a>Další kroky
* [Nainstalujte Apache Giraph v clusterech HDInsight](hdinsight-hadoop-giraph-install-linux.md). K instalaci Giraph do clusterů HDInsight Hadoop použijte vlastní nastavení clusteru. Giraph vám umožňuje provádět zpracování grafů pomocí Hadoop a dá se použít s Azure HDInsight.
* [Nainstalujte R v clusterech HDInsight](hdinsight-hadoop-r-scripts-linux.md). Pomocí vlastního nastavení clusteru můžete v clusterech HDInsight Hadoop nainstalovat R. R je open source jazyk a prostředí pro statistické výpočty. Poskytuje stovky integrovaných statistických funkcí a jejich vlastního programovacího jazyka, který kombinuje aspekty funkčního a objektově orientovaného programování. Poskytuje také rozsáhlé grafické možnosti.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
