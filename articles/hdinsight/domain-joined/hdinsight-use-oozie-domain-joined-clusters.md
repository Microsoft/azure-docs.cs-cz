---
title: Pracovní postupy Hadoop Oozie v prostředí clusterů připojených k doméně HDInsight
description: Použití Hadoop Oozie v domény se systémem Linux HDInsight připojené k Enterprise Security Package. Zjistěte, jak definovat pracovní postup Oozie a odešlete úlohu Oozie.
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: omidm
ms.openlocfilehash: 9e5b7303830f2064f764d2de023b4a3ff9b0ea9f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449870"
---
#<a name="run-apache-oozie-in-domain-joined-hdinsight-hadoop-clusters"></a>Spouštění Apache Oozie v clusterech HDInsight Hadoop připojené k doméně
Oozie je pracovní postup a koordinaci systém, který spravuje úlohy platformy Hadoop. Oozie integrován do zásobníku Hadoop a podporuje následující úlohy:
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Oozie můžete také použít k plánování úloh, které jsou specifické pro systém, jako jsou programy v jazyce Java nebo skripty prostředí.

##<a name="prerequisites"></a>Požadavky:
- Cluster HDInsight Hadoop připojených k doméně. Zobrazit [clusterů HDInsight připojených k doméně nakonfigurovat](./apache-domain-joined-configure-using-azure-adds.md)

    > [!NOTE]
    > Podrobné pokyny pro použití Oozie v doméně připojené k clusterů najdete v sekci [to](../hdinsight-use-oozie-linux-mac.md)

##<a name="connecting-to-domain-joined-cluster"></a>Připojení k doméně s clusterem připojeným
Další informace o SSH najdete v části [ověřování: HDInsight připojený k doméně](../hdinsight-hadoop-linux-use-ssh-unix.md).
- Připojte se ke clusteru HDInsight pomocí protokolu SSH:
     ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```
Chcete-li ověřit, pokud se ověřování protokolem Kerberos bylo úspěšné, použijte `klist` příkazu. Pokud ne, použijte `kinit` k zahájení ověřování protokolem Kerberos.

- Přihlaste se k HDInsight bránu k registraci tokenu oAuth, vyžaduje se pro přístup k Azure Data Lake Store (ADLS)
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    Stavový kód odpovědi _200 OK_ označuje úspěšnou registraci. Zkontrolujte uživatelské jméno a heslo, pokud je odpověď neoprávněné přijaté (401).

## <a name="define-the-workflow"></a>Definování pracovního postupu
Definice pracovního postupu Oozie jsou napsané v Hadoop procesu Definition Language (hPDL), což je jazyk definice procesu XML. Následující postup použijte k definování pracovního postupu:

-   Nastavení pracovního prostoru uživatele domény:
 ```bash
hdfs dfs -mkdir /user/<DomainUser>
cd /home/<DomainUserPath>
cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
tar -xvf oozie-examples.tar.gz
hdfs dfs -put examples /user/<DomainUser>/
 ```
Nahraďte `DomainUser` s doména uživatelské jméno. Nahraďte `DomainUserPath` s cestou domovský adresář pro uživatele domény. Nahraďte `ClusterVersion` s vaší verzí HDP clusteru.

-   Pomocí následujícího příkazu vytvořte a upravte nový soubor:
 ```bash
nano workflow.xml
 ```

- Jakmile se otevře nano editor, zadejte následující kód XML jako obsah souboru:
 ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://hn0-<clustername>.<Domain>.com:9083</value>
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
Nahraďte `clustername` s názvem clusteru. 

K uložení souboru, vyberte kombinaci kláves Ctrl + X, zadejte `Y`a pak vyberte **Enter**.

Pracovní postup je rozdělena na dva oddíly:
*   Část přihlašovacích údajů: Části přihlašovacích údajů trvá v přihlašovacích údajích, které se použijí pro ověřování oozie akce:

    V tomto příkladu se používá ověřování pro Hive akce. Další informace najdete v tématu [to]( https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

    Přihlašovací údaje služby umožňuje oozie zosobnit uživatele pro přístup ke službám Hadoop.

*   Akce oddílu: Máme tři akce tady map reduce, hive server 2 akce a akce server 1 hive:

    Spuštění map-reduce příklad z balíčku oozie pro redukci map která vytvoří výstup počet agregovaných slov.

    Hive server 2 a hive server 1 akce provede jednoduchý dotaz na tabulku hivesample s HDInsight.

    Hive akce používají přihlašovací údaje definované v části s přihlašovacími údaji pro ověřování s použitím klíčového slova `cred` v prvku akce

- Použijte následující příkaz pro kopírování `workflow.xml` soubor `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`:
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

    Nahraďte `domainuser` se svým uživatelským jménem pro doménu.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Definování vlastnosti souboru pro úlohu Oozie

1.  Pomocí následujícího příkazu vytvořte a upravte nový soubor pro vlastnosti úlohy:
     ```bash
    nano job.properties
     ```

2.   Jakmile se otevře nano editor, použijte následující kód XML jako obsah souboru:

    ```bash
        nameNode=adl://home
        jobTracker=headnodehost:8050
        queueName=default
        examplesRoot=examples
        oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
        hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
        hiveScript2=${nameNode}/user/${user.name}/countrowshive1.hql
        oozie.use.system.libpath=true
        user.name=[domainuser]
        jdbcPrincipal=hive/hn0-<ClusterShortName>.<Domain>.com@<Domain>.COM
        jdbcURL=[jdbcurlvalue]
        hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
        hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
    ```
    

   * Nahraďte `domainuser` se svým uživatelským jménem pro doménu.
   * Nahraďte `ClusterShortName` s shortname pro cluster. Pokud název clusteru je https://sechadoopcontoso.azurehdisnight.net, `clustershortname` je prvních šesti písmen pro cluster: sechad
   * Nahraďte `jdbcurlvalue` s adresou url JDBC z konfigurace hive. Například jdbc:hive2: / / headnodehost:10001 /; režim = http.
    
   * K uložení souboru, vyberte kombinaci kláves Ctrl + X, zadejte `Y`a pak vyberte **Enter**.

   * Tento soubor vlastnosti musí být k dispozici místně při spuštění úlohy oozie

## <a name="creating-custom-hive-scripts-for-the-oozie-job"></a>Vytváření skriptů vlastní hive pro úlohu Oozie
Skriptů 2 hive pro hive server 1 a hive server 2 lze vytvořit následujícím způsobem:
-   Soubor Server 1 Hive:
1.  Použijte následující příkaz k vytvoření a úprava souboru pro hive server 1 akce:
    ```bash
    nano countrowshive1.xml
    ```

2.  Vytvořte skript
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  Uložte soubor do rozhraní Hdfs
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

-   Soubor Hive Server 2:
1.  Použijte následující příkaz k vytvoření a úprava pole pro hive server 2 akce:
    ```bash
    nano countrowshive2.xml
    ```

2.  Vytvořte skript
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  Uložte soubor do rozhraní Hdfs
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submission-of-oozie-jobs"></a>Odeslání úloh Oozie
Odesílání úloh oozie pro clustery připojené k doméně je podobný odesílání úloh oozie v clustery připojené k doméně.

Další informace najdete v tématu [odesílat a spravovat úlohy](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-oozie-job-submission"></a>Výsledky odeslání úlohy Oozie
Protože oozie úlohy spouštějí na jménem uživatele, Yarn a Ranger auditu zobrazit protokoly úloh při provádění jako zosobněného uživatele. Rozhraní příkazového řádku výstup úlohy oozie vypadat následovně:


```bash
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

*    Ranger protokoly auditu pro hive server 2 ukazuje oozie akce provádění akce jménem uživatele. Ranger a Yarn zobrazení bude viditelná jenom pro správce clusteru.

## <a name="configuration-of-user-authorization-in-oozie"></a>Konfigurace autorizace uživatelů v Oozie
Oozie samostatně má konfiguraci ověření uživatele, která můžete zablokovat uživatelům možnost zastavit, ukončuje úloh jiný uživatel. To se povoluje nastavením `oozie.service.AuthorizationService.security.enabled` k `true`. 

Další podrobnosti o tom najdete v části dokumentace Oozie - [konfigurace autorizace uživatele]( https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html):

U součástí, například hive server 1, pokud modul plug-in Ranger není k dispozici nebo podporované je možné pouze hrubých hdfs autorizace. Bez problémů jemněji autorizace je pouze k dispozici prostřednictvím modulů plug-in ranger.

## <a name="oozie-web-ui"></a>Oozie webového uživatelského rozhraní
Webové uživatelské rozhraní Oozie poskytuje webové zobrazení stavu úlohy Oozie v clusteru. V doméně připojené clustery, které je potřeba:

1. Přidat [hraniční uzel](../hdinsight-apps-use-edge-node.md) a povolit [SSH ověřování pomocí protokolu Kerberos](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. Postupujte podle [Oozie webového uživatelského rozhraní](../hdinsight-use-oozie-linux-mac.md) postup pro povolení tunelového propojení SSH k hraničnímu uzlu a přístup k webovým Uživatelským rozhraním.

## <a name="next-steps"></a>Další postup
* [Použití Oozie s Hadoopem k definování a spuštění workflowu v Azure HDInsight založených na Linuxu](../hdinsight-use-oozie-linux-mac.md)
* [Použití koordinátoru Oozie podle času](../hdinsight-use-oozie-coordinator-time.md)
* [Spouštění dotazů Hive pomocí SSH na clusterech HDInsight připojených k doméně](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
