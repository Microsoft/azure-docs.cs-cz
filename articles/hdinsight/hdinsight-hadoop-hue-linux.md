---
title: Odstín pomocí Hadoop v clusterech se systémem HDInsight Linux – Azure
description: Naučte se instalovat odstín v clusterech HDInsight a pomocí tunelového propojení směrovat požadavky do odstínu. Pomocí odstínu můžete procházet úložiště a spustit podregistr nebo prase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/31/2020
ms.openlocfilehash: ef30672e250e598688d1b81fd33fe0a995e78c7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087720"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Instalace a použití odstínu v clusterech HDInsight Hadoop

Naučte se instalovat odstín v clusterech HDInsight a pomocí tunelového propojení směrovat požadavky do odstínu.

## <a name="what-is-hue"></a>Co je odstín?

Odstín je sada webových aplikací používaných pro interakci s clusterem Apache Hadoop. Pomocí odstínu můžete procházet úložiště přidružené k clusteru Hadoop (WASB, v případě clusterů HDInsight), spouštět úlohy podregistru a skripty pro vepřové prostředí a tak dále. Následující komponenty jsou k dispozici s odstínem instalací v clusteru HDInsight Hadoop.

* Editor podregistru beeswax
* Apache prasete
* Metastore Manager
* Apache Oozie
* Prohlížeč (který mluví s WASB výchozím kontejnerem)
* Prohlížeč úloh

> [!WARNING]  
> Komponenty dodávané s clusterem HDInsight jsou plně podporované a podpora Microsoftu vám pomůžou izolovat a řešit problémy související s těmito součástmi.
>
> Vlastní komponenty získají komerčně přiměřenou podporu, která vám může pomoct s dalším řešením tohoto problému. To může vést k vyřešení problému nebo požádá vás o zapojení dostupných kanálů pro technologie Open Source, ve kterých se najde hlubokou odbornost pro danou technologii. Například existuje mnoho webů komunity, které lze použít, například: [Microsoft Q&stránku s otázkou pro HDInsight](https://docs.microsoft.com/answers/topics/azure-hdinsight.html), [https://stackoverflow.com](https://stackoverflow.com) . Projekty Apache také obsahují projektové weby [https://apache.org](https://apache.org) , například: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Instalace odstínu pomocí akcí skriptů

Pro akci skriptu použijte informace v následující tabulce. Konkrétní pokyny k používání akcí skriptů najdete v tématu [Přizpůsobení clusterů HDInsight pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md) .

> [!NOTE]  
> K instalaci odstínu u clusterů HDInsight je doporučená velikost hlavnímu uzlu aspoň A4 (8 jader, 14 GB paměti).

|Vlastnost |Hodnota |
|---|---|
|Typ skriptu:|– Vlastní|
|Name|Nainstalovat odstín|
|Identifikátor URI skriptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|Typ (typy) uzlů:|Head|

## <a name="use-hue-with-hdinsight-clusters"></a>Použití odstínu u clusterů HDInsight

V pravidelných clusterech můžete mít jenom jeden uživatelský účet s odstínem. V případě přístupu s více uživateli povolte [balíček zabezpečení podniku](./domain-joined/hdinsight-security-overview.md) v clusteru. Tunelové propojení SSH je jediným způsobem, jak získat přístup k odstínům clusteru po jeho spuštění. Tunelové propojení prostřednictvím SSH umožňuje provoz přejít přímo na hlavnímu uzlu clusteru, kde je spuštěný odstín. Po dokončení zřizování clusteru použijte následující postup k použití odstínu v clusteru HDInsight.

> [!NOTE]  
> Doporučujeme použít webový prohlížeč Firefox a postupovat podle následujících pokynů.

1. Použijte informace v části [použití tunelového propojení SSH pro přístup k webovému uživatelskému rozhraní Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie a jinému webovému uživatelskému rozhraní](hdinsight-linux-ambari-ssh-tunnel.md) k vytvoření tunelu SSH z klientského systému do clusteru HDInsight a pak nakonfigurujte webový prohlížeč tak, aby používal tunel SSH jako proxy.

1. Připojte se ke clusteru pomocí [příkazu SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Po připojení k získání plně kvalifikovaného názvu domény primárního hlavnímu uzlu použijte následující příkaz:

    ```bash
    hostname -f
    ```

    Tato akce vrátí název podobný následujícímu:

    ```output
    myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    ```

    Toto je název hostitele primární hlavnímu uzlu, kde se nachází web odstín.

1. Pomocí prohlížeče otevřete portál odstínů na adrese `http://HOSTNAME:8888` . Nahraďte název hostitele názvem, který jste získali v předchozím kroku.

   > [!NOTE]  
   > Při prvním přihlášení se zobrazí výzva k vytvoření účtu pro přihlášení k portálu odstínování. Přihlašovací údaje, které tady zadáte, budou omezené na portál a nesouvisejí s přihlašovacími údaji uživatele správce nebo SSH, které jste zadali při zřizování clusteru.

    ![Přihlašovací okno portálu pro barevný interval HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Zadat přihlašovací údaje pro portál odstínů")

### <a name="run-a-hive-query"></a>Spuštění dotazu Hive

1. Z portálu odstínů vyberte **editory dotazů**a pak vyberte **podregistr** . otevře se Editor podregistru.

    ![Portál pro odstíny HDInsight – použití editoru podregistru](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Použít podregistr")

2. Na kartě **pomoc** v části **databáze**by se měla zobrazit **hivesampletable**. Toto je ukázková tabulka, která se dodává se všemi clustery Hadoop v HDInsight. Do pravého podokna zadejte ukázkový dotaz a na kartě **výsledky** v podokně níže se zobrazí výstup, jak je znázorněno na snímku obrazovky.

    ![Dotaz na podregistr na portálu pro odstíny HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Spustit dotaz na podregistr")

    Můžete také použít kartu **graf** k zobrazení vizuální reprezentace výsledku.

### <a name="browse-the-cluster-storage"></a>Procházení úložiště clusteru

1. Z portálu odstínů vyberte v pravém horním rohu řádku nabídky možnost **Prohlížeč souborů** .
2. Ve výchozím nastavení se prohlížeč souborů otevře v adresáři **/User/MyUser** . Vyberte lomítko přímo před adresářem uživatelů v cestě, abyste přešli do kořenového adresáře kontejneru úložiště Azure přidruženého ke clusteru.

    ![Prohlížeč souborů portálu pro odstíny HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Použití prohlížeče souborů")

3. Kliknutím pravým tlačítkem na soubor nebo složku zobrazíte dostupné operace. Pomocí tlačítka **nahrát** v pravém horním rohu nahrajte soubory do aktuálního adresáře. Pomocí tlačítka **Nový** vytvořte nové soubory nebo adresáře.

> [!NOTE]  
> Prohlížeč souborů odstínu může zobrazit jenom obsah výchozího kontejneru přidruženého ke clusteru HDInsight. Žádné další účty úložiště nebo kontejnery, které jste mohli přidružit ke clusteru, nebudou přístupné pomocí prohlížeče souborů. Další kontejnery spojené s clusterem budou ale vždy přístupné pro úlohy podregistru. Pokud například zadáte příkaz `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` v editoru podregistru, můžete zobrazit také obsah dalších kontejnerů. V tomto příkazu není **newcontainer** výchozím kontejnerem přidruženým ke clusteru.

## <a name="important-considerations"></a>Důležité informace

1. Skript použitý k instalaci odstínu se nainstaluje jenom na primární hlavnímu uzlu clusteru.

1. Během instalace se restartuje více služeb Hadoop (HDFS, PŘÍZe, MR2, Oozie), aby se aktualizovala konfigurace. Až se skript dokončí instalací odstínu, může trvat nějakou dobu, než se spustí další služby Hadoop. To může mít na začátku vliv na výkon odstínování. Po spuštění všech služeb bude odstín plně funkční.

1. Odstín nerozumí Apache Tez úlohám, což je aktuální výchozí nastavení pro podregistr. Pokud chcete jako spouštěcí modul podregistru použít MapReduce, aktualizujte skript tak, aby ve skriptu použil následující příkaz:

   `set hive.execution.engine=mr;`

1. U clusterů se systémem Linux můžete mít scénář, ve kterém jsou služby spuštěné na primárním hlavnímu uzlu, zatímco Správce prostředků může běžet na sekundárním počítači. V takovém případě může dojít k chybám (zobrazené níže) při použití funkce odstín k zobrazení podrobností o SPUŠTĚNých úlohách v clusteru. Po dokončení úlohy však můžete zobrazit podrobnosti o úloze.

   ![Ukázka zprávy o chybě v portálu pro odstínování](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Chyba portálu pro odstínování")

   Důvodem je známý problém. Jako alternativní řešení upravte Ambari tak, aby se aktivní Správce prostředků taky spouštěla na primárním hlavnímu uzlu.

1. Odstín rozumí WebHDFS, zatímco clustery HDInsight používají Azure Storage pomocí `wasbs://` . Vlastní skript, který se používá pro akci skriptu, nainstaluje WebWasb, což je služba kompatibilní s WebHDFS pro komunikaci s WASB. Takže i když se na portálu pro odstíny říká HDFS (například když přesunete myš přes **Prohlížeč souborů**), mělo by se interpretovat jako WASB.

## <a name="next-steps"></a>Další kroky

[Nainstalujte R v clusterech HDInsight](hdinsight-hadoop-r-scripts-linux.md). Pomocí vlastního nastavení clusteru můžete v clusterech HDInsight Hadoop nainstalovat R. R je open source jazyk a prostředí pro statistické výpočty. Poskytuje stovky integrovaných statistických funkcí a jejich vlastního programovacího jazyka, který kombinuje aspekty funkčního a objektově orientovaného programování. Poskytuje také rozsáhlé grafické možnosti.
