---
title: Hue with Hadoop on HDInsight Linux-based clusters - Azure
description: Learn how to install Hue on HDInsight clusters and use tunneling to route the requests to Hue. Use Hue to browse storage and run Hive or Pig.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 69acfd4f2edab9be1b1dcfbb52eafbd00aec712f
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934560"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Install and use Hue on HDInsight Hadoop clusters

Learn how to install Hue on HDInsight clusters and use tunneling to route the requests to Hue.

## <a name="what-is-hue"></a>What is Hue?

Hue is a set of Web applications used to interact with an Apache Hadoop cluster. You can use Hue to browse the storage associated with a Hadoop cluster (WASB, in the case of HDInsight clusters), run Hive jobs and Pig scripts, and so on. The following components are available with Hue installations on an HDInsight Hadoop cluster.

* Beeswax Hive Editor
* Apache Pig
* Metastore manager
* Apache Oozie
* FileBrowser (which talks to WASB default container)
* Job Browser

> [!WARNING]  
> Components provided with the HDInsight cluster are fully supported and Microsoft Support will help to isolate and resolve issues related to these components.
>
> Custom components receive commercially reasonable support to help you to further troubleshoot the issue. This might result in resolving the issue OR asking you to engage available channels for the open source technologies where deep expertise for that technology is found. For example, there are many community sites that can be used, like: [MSDN forum for HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Also Apache projects have project sites on [https://apache.org](https://apache.org), for example: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Install Hue using Script Actions

Use the information in the table below for your Script Action. See [Customize HDInsight clusters with Script Actions](hdinsight-hadoop-customize-cluster-linux.md) for specific instructions on using Script Actions.

> [!NOTE]  
> To install Hue on HDInsight clusters, the recommended headnode size is at least A4 (8 cores, 14 GB memory).

|Vlastnost |Hodnota |
|---|---|
|Script type:|- Custom|
|Name (Název)|Instalace rozhraní Hue|
|Bash script URI|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|Node type(s):|Hlavní uzel|

## <a name="use-hue-with-hdinsight-clusters"></a>Use Hue with HDInsight clusters

SSH Tunneling is the only way to access Hue on the cluster once it is running. Tunneling via SSH allows the traffic to go directly to the headnode of the cluster where Hue is running. After the cluster has finished provisioning, use the following steps to use Hue on an HDInsight cluster.

> [!NOTE]  
> We recommend using Firefox web browser to follow the instructions below.

1. Use the information in [Use SSH Tunneling to access Apache Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie, and other web UI's](hdinsight-linux-ambari-ssh-tunnel.md) to create an SSH tunnel from your client system to the HDInsight cluster, and then configure your Web browser to use the SSH tunnel as a proxy.

1. Use [ssh command](./hdinsight-hadoop-linux-use-ssh-unix.md) to connect to your cluster. Edit the command below by replacing CLUSTERNAME with the name of your cluster, and then enter the command:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Once connected, use the following command to obtain the fully qualified domain name of the primary headnode:

    ```bash
    hostname -f
    ```

    This will return a name similar to the following:

        myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    This is the hostname of the primary headnode where the Hue website is located.

1. Use the browser to open the Hue portal at `http://HOSTNAME:8888`. Replace HOSTNAME with the name you obtained in the previous step.

   > [!NOTE]  
   > When you log in for the first time, you will be prompted to create an account to log in to the Hue portal. Přihlašovací údaje, které tady zadáte, budou omezené na portál a nesouvisejí s přihlašovacími údaji uživatele správce nebo SSH, které jste zadali při zřizování clusteru.

    ![Přihlašovací okno portálu pro barevný interval HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Zadat přihlašovací údaje pro portál odstínů")

### <a name="run-a-hive-query"></a>Spuštění dotazu Hive

1. Z portálu odstínů vyberte **editory dotazů**a pak vyberte **podregistr** . otevře se Editor podregistru.

    ![Portál pro odstíny HDInsight – použití editoru podregistru](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Použití Hivu")

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

        set hive.execution.engine=mr;

1. U clusterů se systémem Linux můžete mít scénář, ve kterém jsou služby spuštěné na primárním hlavnímu uzlu, zatímco Správce prostředků může běžet na sekundárním počítači. V takovém případě může dojít k chybám (zobrazené níže) při použití funkce odstín k zobrazení podrobností o SPUŠTĚNých úlohách v clusteru. Po dokončení úlohy však můžete zobrazit podrobnosti o úloze.

   ![Ukázka zprávy o chybě v portálu pro odstínování](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Chyba portálu pro odstínování")

   Důvodem je známý problém. Jako alternativní řešení upravte Ambari tak, aby se aktivní Správce prostředků taky spouštěla na primárním hlavnímu uzlu.

1. Odstín rozumí WebHDFS, zatímco clustery HDInsight používají Azure Storage pomocí `wasbs://`. Vlastní skript, který se používá pro akci skriptu, nainstaluje WebWasb, což je služba kompatibilní s WebHDFS pro komunikaci s WASB. Takže i když se na portálu pro odstíny říká HDFS (například když přesunete myš přes **Prohlížeč souborů**), mělo by se interpretovat jako WASB.

## <a name="next-steps"></a>Další kroky

[Nainstalujte R v clusterech HDInsight](hdinsight-hadoop-r-scripts-linux.md). Pomocí vlastního nastavení clusteru můžete v clusterech HDInsight Hadoop nainstalovat R. R je open source jazyk a prostředí pro statistické výpočty. Poskytuje stovky integrovaných statistických funkcí a jejich vlastního programovacího jazyka, který kombinuje aspekty funkčního a objektově orientovaného programování. Poskytuje také rozsáhlé grafické možnosti.
