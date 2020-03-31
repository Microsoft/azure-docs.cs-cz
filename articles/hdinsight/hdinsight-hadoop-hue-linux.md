---
title: Odstín s Hadoopem na clusterech založených na SYSTÉMU HDInsight na Linuxu – Azure
description: Přečtěte si, jak nainstalovat Hue do clusterů HDInsight a pomocí tunelového propojení směrovat požadavky do Hue. Pomocí hue můžete procházet úložiště a spouštět Hive nebo Pig.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 69acfd4f2edab9be1b1dcfbb52eafbd00aec712f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934560"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Instalace a použití hue na clusterech HDInsight Hadoop

Přečtěte si, jak nainstalovat Hue do clusterů HDInsight a pomocí tunelového propojení směrovat požadavky do Hue.

## <a name="what-is-hue"></a>Co je Hue?

Hue je sada webových aplikací používaných k interakci s clusterem Apache Hadoop. Hue můžete použít k procházení úložiště přidruženého ke clusteru Hadoop (WASB, v případě clusterů HDInsight), spuštění úloh Hive a skriptů Pig a tak dále. Následující součásti jsou k dispozici s instalacemi Hue v clusteru HDInsight Hadoop.

* Editor úlu včelaře
* Apache prase
* Metastore manažer
* Apač Oozie
* FileBrowser (který mluví s WASB výchozí kontejner)
* Job Browser

> [!WARNING]  
> Součásti dodávané s clusterem HDInsight jsou plně podporovány a podpora společnosti Microsoft pomůže izolovat a vyřešit problémy související s těmito součástmi.
>
> Vlastní součásti obdrží komerčně přiměřenou podporu, která vám pomůže dále řešit problém. To může vést k vyřešení problému nebo s žádostí o zapojení dostupných kanálů pro technologie s otevřeným zdrojovým kódem, kde jsou nalezeny hluboké odborné znalosti pro tuto technologii. Existuje například mnoho komunitních webů, které lze použít, například: [https://stackoverflow.com](https://stackoverflow.com) [Fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight). Také Apache projekty [https://apache.org](https://apache.org)mají projektové weby na , například: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Instalace odstínu pomocí akcí skriptu

Informace v následující tabulce použijte pro akci skriptu. Konkrétní pokyny k používání akcí skriptu najdete v tématu [Přizpůsobení clusterů HDInsight pomocí akcí skriptu.](hdinsight-hadoop-customize-cluster-linux.md)

> [!NOTE]  
> Chcete-li nainstalovat Hue na HDInsight clustery, doporučená velikost headnode je alespoň A4 (8 jader, 14 GB paměti).

|Vlastnost |Hodnota |
|---|---|
|Typ skriptu:|- Vlastní|
|Name (Název)|Instalace odstínu|
|Bash skript URI|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|Typ uzlu(y):|Head|

## <a name="use-hue-with-hdinsight-clusters"></a>Použití hue s clustery HDInsight

Tunelové propojení SSH je jediný způsob, jak získat přístup k Hue v clusteru, jakmile je spuštěn. Tunelové propojení přes SSH umožňuje přenos přejít přímo do hlavního uzlu clusteru, kde je spuštěn Hue. Po dokončení zřizování clusteru použijte následující kroky k použití hue v clusteru HDInsight.

> [!NOTE]  
> Doporučujeme používat webový prohlížeč Firefox, abyste postupovali podle níže uvedených pokynů.

1. Pomocí informací v [aplikaci Use SSH Tunneling získáte přístup k webovému uživatelskému rozhraní Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie a dalšíwebové uživatelské rozhraní](hdinsight-linux-ambari-ssh-tunnel.md) k vytvoření tunelu SSH z klientského systému do clusteru HDInsight a k nakonfiguraci webového prohlížeče tak, aby používal tunel SSH jako proxy server.

1. Pomocí [příkazu ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru. Upravte níže uvedený příkaz nahrazením názvu clusteru názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Po připojení použijte následující příkaz k získání plně kvalifikovaného názvu domény primárního headnode:

    ```bash
    hostname -f
    ```

    Tím se vrátí název podobný následujícímu:

        myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Toto je název hostitele primárního headnode, kde se nachází web Hue.

1. Pomocí prohlížeče otevřete portál `http://HOSTNAME:8888`Hue na adrese . Nahraďte název hostname s názvem, který jste získali v předchozím kroku.

   > [!NOTE]  
   > Při prvním přihlášení budete vyzváni k vytvoření účtu pro přihlášení k portálu Hue. Pověření, která zde zadáte, budou omezena na portál a nebudou souviset s pověřeními uživatele správce nebo SSH, která jste zadali při zřizování clusteru.

    ![Okno pro přihlášení portálu odstínu HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Určení přihlašovacích údajů pro portál Hue")

### <a name="run-a-hive-query"></a>Spuštění dotazu Hive

1. Na portálu Hue vyberte **Editory dotazů**a pak vyberte **Hive,** chcete-li otevřít editor Hive.

    ![Portál hue HDInsight používá editor úlů](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Použít úl")

2. Na kartě **Assist** v části **Databáze**byste měli vidět **hivesampletable**. Toto je ukázková tabulka, která je dodávána se všemi clustery Hadoop na HDInsight. Zadejte ukázkový dotaz do pravého podokna a výstup na kartě **Výsledky** najdete v podokně níže, jak je znázorněno v zachycení obrazovky.

    ![Dotaz podregistru portálu hue HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Spustit dotaz Hive")

    Na kartě **Graf** můžete také zobrazit vizuální znázornění výsledku.

### <a name="browse-the-cluster-storage"></a>Procházení úložiště clusteru

1. Na portálu Hue vyberte **prohlížeč souborů** v pravém horním rohu řádku nabídek.
2. Ve výchozím nastavení se prohlížeč souborů otevře v adresáři **/user/myuser.** Vyberte lomítko přímo před adresářem uživatele v cestě k kořenovému adresáři kontejneru úložiště Azure přidruženého ke clusteru.

    ![Prohlížeč souborů portálu hue HDInsight](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Použití prohlížeče souborů")

3. Kliknutím pravým tlačítkem myši na soubor nebo složku zobrazíte dostupné operace. Pomocí tlačítka **Nahrát** v pravém rohu můžete nahrávat soubory do aktuálního adresáře. Pomocí tlačítka **Nový** můžete vytvářet nové soubory nebo adresáře.

> [!NOTE]  
> Prohlížeč souborů Hue může zobrazovat pouze obsah výchozího kontejneru přidruženého ke clusteru HDInsight. Všechny další účty úložiště nebo kontejnery, které jste mohli přidružit ke clusteru, nebudou přístupné pomocí prohlížeče souborů. Další kontejnery přidružené ke clusteru však budou vždy přístupné pro úlohy Hive. Pokud například zadáte příkaz `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` v editoru Hive, zobrazí se také obsah dalších kontejnerů. V tomto příkazu **newcontainer** není výchozí kontejner přidružený ke clusteru.

## <a name="important-considerations"></a>Důležité informace

1. Skript použitý k instalaci Hue nainstaluje pouze na primární headnode clusteru.

1. Během instalace je restartováno více služeb Hadoop (HDFS, YARN, MR2, Oozie) pro aktualizaci konfigurace. Po dokončení skriptu instalace Hue, může nějakou dobu trvat, než se spustí další služby Hadoop. To může mít vliv na výkon Hue zpočátku. Jakmile se všechny služby spustí, Hue bude plně funkční.

1. Hue nerozumí úlohy Apache Tez, což je aktuální výchozí pro Hive. Pokud chcete použít MapReduce jako modul spuštění Hive, aktualizujte skript tak, aby používal následující příkaz ve skriptu:

        set hive.execution.engine=mr;

1. S clustery Linux, můžete mít scénář, kde jsou vaše služby spuštěny na primární headnode, zatímco Správce prostředků může být spuštěn na sekundární. Takový scénář může mít za následek chyby (viz níže) při použití Hue zobrazit podrobnosti o spuštění úloh v clusteru. Můžete však zobrazit podrobnosti o úloze po dokončení úlohy.

   ![Ukázková zpráva o chybě portálu Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Chyba portálu Hue")

   To je způsobeno známým problémem. Jako zástupné řešení upravte Ambari tak, aby aktivní Správce prostředků také běží na primárním headnode.

1. Hue rozumí WebHDFS, zatímco clustery HDInsight používají Azure Storage pomocí `wasbs://`. Takže vlastní skript používaný s akcí skriptu nainstaluje WebWasb, což je služba kompatibilní s WebHDFS pro rozhovor s WASB. Takže, i když portál Hue říká HDFS v místech (jako když se pohybujete myší přes **prohlížeč souborů),** měl by být interpretován jako WASB.

## <a name="next-steps"></a>Další kroky

[Nainstalujte R do clusterů HDInsight](hdinsight-hadoop-r-scripts-linux.md). Pomocí vlastního nastavení clusteru nainstalujte R do clusterů HDInsight Hadoop. R je open source jazyk a prostředí pro statistické výpočty. Poskytuje stovky vestavěných statistických funkcí a vlastní programovací jazyk, který kombinuje aspekty funkčního a objektově orientovaného programování. Poskytuje také rozsáhlé grafické možnosti.
