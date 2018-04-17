---
title: Použití akce skriptu k instalaci Solr na HDInsight se systémem Linux - Azure | Microsoft Docs
description: Zjistěte, jak nainstalovat Solr na systémem Linux HDInsight Hadoop clustery pomocí akcí skriptů.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cc93ed5c-a358-456a-91a4-f179185c0e98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: 8a642b569389d960e7a3a1fa2c7cb0f2d87bd809
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Na nainstalovat a používat Solr clusterů systému HDInsight Hadoop

Informace o instalaci Solr v Azure HDInsight pomocí akce skriptu. Solr je platforma pro efektivní vyhledávání a poskytuje možnosti vyhledávání na úrovni podniku na data spravovaná přes Hadoop.

> [!IMPORTANT]
    > Kroky v tomto dokumentu vyžadují clusteru služby HDInsight, který používá Linux. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!IMPORTANT]
> Ukázkový skript v tomto dokumentu nainstaluje Solr 4.9 s konkrétní konfigurací. Pokud chcete konfigurovat Solr cluster s různých kolekcí, horizontálních oddílů, schémata, repliky, atd., musíte upravit skript a Solr binární soubory.

## <a name="whatis"></a>Co je Solr

[Apache Solr](http://lucene.apache.org/solr/features.html) je platforma enterprise vyhledávání, která umožňuje efektivní fulltextové vyhledávání na data. Při Hadoop umožňuje ukládání a správě obrovské množství dat, Apache Solr poskytuje možnosti vyhledávání lze snadno obnovit data.

> [!WARNING]
> Microsoft podporuje součásti, které jsou součástí clusteru HDInsight.
>
> Vlastní komponenty, například Solr, přijímat vyvineme podporu o pomoci při další řešení problému. Podporu společnosti Microsoft nemusí být schopni vyřešit problémy s vlastními komponentami. Budete muset zaujmout komunit s otevřeným zdrojem o pomoc. Například existuje mnoho komunity webů, které lze použít jako: [fórum MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Také Apache projekty mají na projektu serverů [ http://apache.org ](http://apache.org), například: [Hadoop](http://hadoop.apache.org/).

## <a name="what-the-script-does"></a>Funkci skriptu

Tento skript provede tyto změny do clusteru HDInsight:

* Nainstaluje Solr 4.9 do `/usr/hdp/current/solr`
* Vytvoří uživatele, **solrusr**, který se používá ke spouštění služby Solr
* Nastaví **solruser** jako vlastník `/usr/hdp/current/solr`
* Přidá [Upstart](http://upstart.ubuntu.com/) konfigurace, který se automaticky spouští Solr.

## <a name="install"></a>Nainstalujte Solr pomocí akcí skriptů

Ukázkový skript pro instalaci Solr v clusteru HDInsight je k dispozici v následujícím umístění:

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Chcete-li vytvořit cluster, který má nainstalovaný Solr, použijte postup v [Tvorba clusterů HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md) dokumentu. K instalaci Solr během procesu vytváření použijte následující kroky:

1. Z __clusteru Souhrn__ část, select__Advanced settings__, pak __skript akce__. K naplnění formuláře použijte následující informace:

   * **NÁZEV**: Zadejte popisný název akce skriptu.
   * **IDENTIFIKÁTOR URI SKRIPTU**: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
   * **HEAD**: zaškrtnete tuto možnost,
   * **PRACOVNÍ**: zaškrtnete tuto možnost,
   * **ZOOKEEPER**: zaškrtnete tuto možnost, chcete-li nainstalovat na uzlu Zookeeper
   * **Parametry**: to pole ponechat prázdné

2. V dolní části **skript akce** pomocí **vyberte** tlačítko Uložit konfiguraci. Nakonec použijte **Další** tlačítko se vrátíte do __souhrn clusteru__

3. Z __clusteru Souhrn__ vyberte __vytvořit__ k vytvoření clusteru.

## <a name="usesolr"></a>Jak používat Solr v prostředí HDInsight

> [!IMPORTANT]
> Postup v této části ukazují základní funkce Solr. Další informace o použití Solr, najdete v článku [Apache Solr lokality](http://lucene.apache.org/solr/).

### <a name="index-data"></a>Data indexu

Pomocí následujícího postupu můžete přidat do Solr příklad a pak zadat dotaz:

1. Připojte se ke clusteru HDInsight pomocí protokolu SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

     > [!IMPORTANT]
     > Kroky později v tomto dokumentu používají tunelového propojení protokolu SSL pro připojení k Solr webového uživatelského rozhraní. Při těchto krocích, musí vytvořit tunelové propojení protokolu SSL a pak nakonfigurujte prohlížeč tak, aby ho použít.
     >
     > Další informace najdete v tématu [používání tunelového propojení SSH s HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentu.

2. Tak, aby měl Solr index ukázková data, použijte následující příkazy:

    ```bash
    cd /usr/hdp/current/solr/example/exampledocs
    java -jar post.jar solr.xml monitor.xml
    ```

    Tento výstup se vrátí do konzoly:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    `post.jar` Nástroj přidá **solr.xml** a **monitor.xml** dokumentů do indexu.
  
3. Zpracovat dotaz rozhraní API REST Solr, použijte následující příkaz:

    ```bash
    curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"
    ```

    Tento příkaz vyhledá **collection1** pro všechny dokumenty odpovídající **\*:\*** (kódovaná jako \*% 3A\* v řetězci dotazu). V následujícím dokumentu JSON je příklad odpovědi:

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }

### <a name="using-the-solr-dashboard"></a>Solr řídicího panelu

Řídicí panel Solr je webové uživatelské rozhraní, které umožňuje pracovat s Solr prostřednictvím webového prohlížeče. Řídicí panel Solr nebude vystavena, přímo na Internetu z clusteru HDInsight. Tunelové propojení SSH můžete použít k přístupu. Další informace o používání tunelového propojení SSH naleznete v části [používání tunelového propojení SSH s HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentu.

Po vytvoření tunelu SSH pomocí řídicího panelu Solr pomocí následujících kroků:

1. Určete název hostitele pro primární headnode:

   1. Použití SSH se připojit k hlavnímu uzlu clusteru. Například, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

       Další informace o používání SSH najdete v tématu [použití SSH s HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

   2. Chcete-li získat plně kvalifikovaný název hostitele, použijte následující příkaz:

        ```bash
        hostname -f
        ```

        Tento příkaz vrátí hodnotu podobná následující název hostitele:

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

        Hodnota vrácená, uložte, protože se později používá.

2. V prohlížeči se připojte k **http://HOSTNAME:8983/solr/#/**, kde **HOSTNAME** je název, který jste nadefinovali v předchozím kroku.

    Požadavek je směrován prostřednictvím tunelu SSH k Solr webového uživatelského rozhraní v clusteru. Podobně jako na následujícím obrázku se zobrazí stránce:

    ![Obrázek Solr řídicí panel](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

3. V levém podokně, použijte **základní selektor** rozevíracího seznamu vyberte **collection1**. Několik položek by je se níže zobrazí **collection1**.

4. Z níže uvedené položky **collection1**, vyberte **dotazu**. K naplnění stránky hledání použijte následující hodnoty:

   * V **q** textové pole, zadejte  **\*:**\*. Tento dotaz vrací všechny dokumenty, které jsou uloženy v Solr. Pokud chcete vyhledat konkrétní řetězec v rámci dokumenty, můžete zadat sem tento řetězec.
   * V **wt** textové pole, vyberte formát výstupu. Výchozí hodnota je **json**.

     Nakonec vyberte **spuštění dotazu** tlačítko v dolní části pate vyhledávání.

     ![Použití akce skriptu k přizpůsobení clusteru](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

     Výstup vrací dva dokumenty, které jste přidali dříve do indexu. Výstup se podobá následující dokumentu JSON:

           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }

### <a name="starting-and-stopping-solr"></a>Spuštění a zastavení Solr

Ručně zastavit a spustit Solr, použijte následující příkazy:

```bash
sudo stop solr
sudo start solr
```

## <a name="backup-indexed-data"></a>Zálohování indexovaná data

Solr data zálohovat na výchozí úložiště pro váš cluster pomocí následujících kroků:

1. Připojte se ke clusteru pomocí protokolu SSH, potom použijte následující příkaz k získat název hostitele pro hlavního uzlu:

    ```bash
    hostname -f
    ```

2. Použijte následující příkaz pro vytvoření snímku indexovaná data. Nahraďte **HOSTNAME** s názvem vrácená z předchozí příkaz:

    ```bash
    curl http://HOSTNAME:8983/solr/replication?command=backup
    ```

    Odpověď je podobná následující kód XML:

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

3. Přejděte do adresáře `/usr/hdp/current/solr/example/solr`. Není podadresáři sem pro každou kolekci. Každý adresář kolekce obsahuje `data` adresář, který obsahuje snímek kolekce.

4. Pokud chcete vytvořit komprimovaný archiv složky snímku, použijte následující příkaz:

    ```bash
    tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855
    ```

    Nahraďte `snapshot.20150806185338855` hodnoty s názvem snímku pro vaše kolekce.

    Tento příkaz vytvoří archiv s názvem **snapshot.20150806185338855.tgz**, který obsahuje obsah **snapshot.20150806185338855** adresáře.

5. Pak můžete uložit do archivu do primárního úložiště do clusteru pomocí následujícího příkazu:

    ```bash
    hdfs dfs -put snapshot.20150806185338855.tgz /example/data
    ```

Další informace o práci s Solr zálohování a obnovení najdete v tématu [ https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups ](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups).

## <a name="next-steps"></a>Další postup

* [Nainstalujte Giraph clustery HDInsight](hdinsight-hadoop-giraph-install-linux.md). Přizpůsobení clusteru použijte k instalaci Giraph clusterů systému HDInsight Hadoop. Giraph umožňuje provádět grafu zpracování pomocí Hadoop a lze použít s Azure HDInsight.

* [Instalace aplikace Hue v clusterech HDInsight](hdinsight-hadoop-hue-linux.md). Instalace aplikace Hue na clusterů systému HDInsight Hadoop pomocí přizpůsobení clusteru. HUE je sada webových aplikací používaných pro interakci s clusterem Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
