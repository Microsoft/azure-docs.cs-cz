---
title: Pracovní postupy Apache Oozie & podnikovézabezpečení – Azure HDInsight
description: Zabezpečte pracovní postupy Apache Oozie pomocí balíčku Azure HDInsight Enterprise Security Package. Naučte se definovat pracovní postup Oozie a odeslat úlohu Oozie.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seodec18
ms.date: 12/09/2019
ms.openlocfilehash: 9ef54707f7fac3dd1328e29f6d05f62c1dee2561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194899"
---
# <a name="run-apache-oozie-in-hdinsight-hadoop-clusters-with-enterprise-security-package"></a>Spusťte Apache Oozie v clusterech HDInsight Hadoop s balíčkem enterprise security

Apache Oozie je workflow a koordinační systém, který spravuje Apache Hadoop pracovních míst. Oozie je integrován s zásobníkem Hadoop a podporuje následující úlohy:

- Apache MapReduce
- Apache prase
- Apache Úl
- Apačský sqoop

Oozie můžete také použít k plánování úloh, které jsou specifické pro systém, jako jsou programy Java nebo skripty prostředí.

## <a name="prerequisite"></a>Požadavek

Cluster Azure HDInsight Hadoop s balíčkem podnikového zabezpečení (ESP). Viz [Konfigurace clusterů HDInsight pomocí protokolu ESP](./apache-domain-joined-configure-using-azure-adds.md).

> [!NOTE]  
> Podrobné pokyny, jak používat Oozie v clusterech mimo ESP, najdete [v tématu Použití pracovních postupů Apache Oozie v Azure HDInsight založeném na Linuxu](../hdinsight-use-oozie-linux-mac.md).

## <a name="connect-to-an-esp-cluster"></a>Připojení ke clusteru ESP

Další informace o zabezpečeném prostředí (SSH) najdete v [tématu Připojení k HDInsight (Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Připojte se ke clusteru HDInsight pomocí SSH:

    ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```

1. Chcete-li ověřit úspěšné ověřování `klist` protokolem Kerberos, použijte příkaz. Pokud ne, `kinit` použijte ke spuštění ověřování protokolem Kerberos.

1. Přihlaste se k bráně HDInsight a zaregistrujte token OAuth potřebný pro přístup k úložišti Azure Data Lake:

    ```bash
    curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
    ```

    Kód odpovědi na stav **200 OK** označuje úspěšnou registraci. Zkontrolujte uživatelské jméno a heslo, pokud je přijata neautorizovaná odpověď, například 401.

## <a name="define-the-workflow"></a>Definování pracovního postupu

Definice pracovního postupu Oozie jsou napsány v jazyce definice procesu Apache Hadoop (hPDL). hPDL je jazyk definice procesu XML. Postupujte podle následujících kroků a definujte pracovní postup:

1. Nastavení pracovního prostoru uživatele domény:

   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```

   Nahraďte `DomainUser` uživatelským jménem domény.
   Nahraďte `DomainUserPath` cestu domovského adresáře pro uživatele domény.
   Nahraďte `ClusterVersion` verzí datové platformy clusteru.

2. K vytvoření a úpravě nového souboru použijte následující příkaz:

   ```bash
   nano workflow.xml
   ```

3. Po otevření nano editoru zadejte jako obsah souboru následující XML:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://<active-headnode-name>-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
    ```

4. Nahraďte `clustername` se názvem clusteru.

5. Chcete-li soubor uložit, vyberte **Ctrl+X**. Zadejte **Y**. Pak vyberte **Enter**.

    Pracovní postup je rozdělen do dvou částí:

   - **Pověření.** Tato část přebírá pověření, které se používají pro ověřování akcí Oozie:

     Tento příklad používá ověřování pro akce Hive. Další informace naleznete v [tématu Ověřování akcí](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

     Služba pověření umožňuje akcím Oozie zosobnit uživatele pro přístup ke službám Hadoop.

   - **Akce.** Tato část obsahuje tři akce: map-reduce, Hive server 2 a Hive server 1:

     - Akce map-reduce spustí příklad z balíčku Oozie pro snížení mapy, který výstupy agregované počet slov.

     - Akce Serveru Hive 2 a Hive server 1 spustit dotaz na ukázkové tabulce Hive dodaný s HDInsight.

     Akce Hive používají pověření definovaná v části pověření pro `cred` ověřování pomocí klíčového slova v prvku akce.

6. Chcete-li soubor zkopírovat do následujícího příkazu, `workflow.xml` použijte `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`následující příkaz:

    ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
    ```

7. Nahraďte `domainuser` ji uživatelským jménem pro doménu.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Definování souboru vlastností úlohy Oozie

1. K vytvoření a úpravě nového souboru pro vlastnosti úlohy použijte následující příkaz:

    ```bash
    nano job.properties
    ```

2. Po otevření editoru nano použijte jako obsah souboru následující XML:

   ```bash
   nameNode=adl://home
   jobTracker=headnodehost:8050
   queueName=default
   examplesRoot=examples
   oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
   hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
   hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
   oozie.use.system.libpath=true
   user.name=[domainuser]
   jdbcPrincipal=hive/<active-headnode-name>.<Domain>.com@<Domain>.COM
   jdbcURL=[jdbcurlvalue]
   hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
   hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   - `adl://home` Uri pro `nameNode` vlastnost, pokud máte Azure Data Lake Storage Gen1 jako primární úložiště clusteru. Pokud používáte Azure Blob Storage, změňte to na `wasb://home`. Pokud používáte Azure Data Lake Storage Gen2, `abfs://home`změňte to na .
   - Nahraďte `domainuser` ji uživatelským jménem pro doménu.  
   - Nahraďte `ClusterShortName` krátkýnázev clusteru. Pokud je například název clusteru https:// *[příklad odkazu]* sechadoopcontoso.azurehdisnight.net, `clustershortname` je prvních šest znaků clusteru: **sechad**.  
   - Nahraďte `jdbcurlvalue` ji adresou URL JDBC z konfigurace Hive. Příkladem je jdbc:hive2://headnodehost:10001/;transportMode=http.
   - Chcete-li soubor uložit, vyberte `Y`Ctrl+X, zadejte a pak vyberte **Enter**.

   Tento soubor vlastností musí být k dispozici místně při spuštění úloh Oozie.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Vytvořit vlastní hive skripty pro úlohy Oozie

Můžete vytvořit dva skripty Hive pro Server Hive 1 a Hive server 2, jak je znázorněno v následujících částech.

### <a name="hive-server-1-file"></a>Soubor hive serveru 1

1. Vytvoření a úprava souboru pro akce serveru Hive server 1:

    ```bash
    nano countrowshive1.hql
    ```

2. Vytvořte skript:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    select devicemake from hivesampletable limit 2;
    ```

3. Uložte soubor do distribuovaného souborového systému Apache Hadoop (HDFS):

    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Soubor hive serveru 2

1. Vytvoření a úprava pole pro akce serveru Hive server 2:

    ```bash
    nano countrowshive2.hql
    ```

2. Vytvořte skript:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3. Uložit soubor do HDFS:

    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Pracovní příležitosti pro Submit Oozie

Odeslání úloh Oozie pro clustery ESP je jako odesílání úloh Oozie v clusterech bez ESP.

Další informace najdete [v tématu Použití Apache Oozie s Apache Hadoop definovat a spustit pracovní postup na Linux-založené Azure HDInsight](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-an-oozie-job-submission"></a>Výsledky z nabídky pracovních míst Oozie

Úlohy Oozie jsou spuštěny pro uživatele. Takže jak Apache Hadoop YARN a Apache Ranger audit protokoly ukazují úlohy jsou spuštěny jako zosobněný uživatel. Výstup rozhraní příkazového řádku úlohy Oozie vypadá takto:

```output
Job ID : 0000015-180626011240801-oozie-oozi-W
------------------------------------------------------------------------------------------------
Workflow Name : map-reduce-wf
App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
Status        : SUCCEEDED
Run           : 0
User          : alicetest
Group         : -
Created       : 2018-06-26 19:25 GMT
Started       : 2018-06-26 19:25 GMT
Last Modified : 2018-06-26 19:30 GMT
Ended         : 2018-06-26 19:30 GMT
CoordAction ID: -

Actions
------------------------------------------------------------------------------------------------
ID                      Status  Ext ID          ExtStatus   ErrCode
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@:start:    OK  -           OK      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@mr-test    OK  job_1529975666160_0051  SUCCEEDED   -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@myHive2    OK  job_1529975666160_0053  SUCCEEDED   -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@myHive OK  job_1529975666160_0055  SUCCEEDED   -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@end    OK  -           OK      -
-----------------------------------------------------------------------------------------------
```

Protokoly auditu Ranger pro Hive server 2 akce ukazují Oozie spuštění akce pro uživatele. Zobrazení Ranger a YARN jsou viditelné pouze pro správce clusteru.

## <a name="configure-user-authorization-in-oozie"></a>Konfigurace autorizace uživatele v Oozie

Oozie sám o sobě má uživatelskou autorizaci konfiguraci, která může blokovat uživatele od zastavení nebo odstranění úloh jiných uživatelů. Chcete-li tuto konfiguraci povolit, nastavte `oozie.service.AuthorizationService.security.enabled` možnost na `true`. 

Další informace naleznete v tématu [Apache Oozie Installation and Configuration](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html).

Pro součásti, jako je Hive server 1, kde modul plug-in Ranger není k dispozici nebo podporován, je možné pouze hrubozrnné oprávnění HDFS. Jemně odstupňovaná autorizace je k dispozici pouze prostřednictvím modulů plug-in Ranger.

## <a name="get-the-oozie-web-ui"></a>Get the Oozie web UI

Webové uživatelské uživatelské uživatelské informace společnosti Oozie poskytuje webový pohled na stav úloh Oozie v clusteru. Chcete-li získat webové uživatelské uživatelské informace, postupujte v clusterech ESP následujícím způsobem:

1. Přidejte [hraniční uzel](../hdinsight-apps-use-edge-node.md) a povolte ověřování protokolu [SSH Kerberos](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Postupujte podle kroků [webového uživatelského uživatelského nastavení Oozie](../hdinsight-use-oozie-linux-mac.md) a povolte tunelování SSH do hraničního uzlu a získejte přístup k webovému uživatelskému uživatelskému uživatelskému nastavení.

## <a name="next-steps"></a>Další kroky

- [Pomocí Apache Oozie s Apache Hadoop definujte a spusťte pracovní postup na Linuxu založeném na Azure HDInsight](../hdinsight-use-oozie-linux-mac.md).
- [Připojte se k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
