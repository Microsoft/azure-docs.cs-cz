---
title: Zabezpečené pracovní postupy Apache Oozie s balíčkem Enterprise Security Package – Azure HDInsight
description: Zabezpečené pracovní postupy Apache Oozie pomocí Azure HDInsight Enterprise Security Package. Zjistěte, jak definovat pracovní postup Oozie a odešlete úlohu Oozie.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0ab225d3579ed6a56c753f0c581709408c65f358
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436275"
---
# <a name="run-apache-oozie-in-hdinsight-hadoop-clusters-with-enterprise-security-package"></a>Spustit Apache Oozie v HDInsight Hadoop clusterů s balíčkem Enterprise Security Package

Apache Oozie je systém koordinace a pracovního postupu, který spravuje úlohy Apache Hadoop. Oozie integrován do zásobníku Hadoop a podporuje následující úlohy:
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Oozie můžete také použít k plánování úloh, které jsou specifické pro systém, jako jsou programy v jazyce Java nebo skripty prostředí.

## <a name="prerequisite"></a>Požadavek

- Cluster Azure HDInsight Hadoop s Enterprise Security Package (ESP). Zobrazit [konfigurace HDInsight clustery s ESP](./apache-domain-joined-configure-using-azure-adds.md).

    > [!NOTE]  
    > Podrobné pokyny týkající se použití Oozie v clusterech bez ESP, naleznete v tématu [použití Apache Oozie pracovních postupů v Azure HDInsight založených na Linuxu](../hdinsight-use-oozie-linux-mac.md).

## <a name="connect-to-an-esp-cluster"></a>Připojení ke clusteru ESP

Další informace o Secure Shell (SSH) najdete v tématu [připojení k HDInsight (Hadoop) pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Připojte se ke clusteru HDInsight pomocí SSH:  
 ```bash
ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
 ```

2. Pokud chcete ověřit úspěšné ověřování protokolem Kerberos, použijte `klist` příkazu. Pokud ne, použijte `kinit` spustit ověřování protokolem Kerberos.

3. Přihlaste se k HDInsight bránu k registraci tokenu OAuth, které jsou nutné pro přístup k Azure Data Lake Storage:   
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    Stavový kód odpovědi **200 OK** označuje úspěšnou registraci. Zkontrolujte uživatelské jméno a heslo, pokud neoprávněné odpověď, jako je například 401.

## <a name="define-the-workflow"></a>Definování pracovního postupu
Definice pracovního postupu Oozie jsou napsané v Apache Hadoop procesu Definition Language (hPDL). hPDL je jazyk definice procesu XML. Proveďte následující kroky k definování pracovního postupu:

1.  Nastavte pracovní prostor uživatele domény:
 ```bash
hdfs dfs -mkdir /user/<DomainUser>
cd /home/<DomainUserPath>
cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
tar -xvf oozie-examples.tar.gz
hdfs dfs -put examples /user/<DomainUser>/
 ```
Nahraďte `DomainUser` s doména uživatelské jméno. Nahraďte `DomainUserPath` s cestou domovský adresář pro uživatele domény. Nahraďte `ClusterVersion` s vaší verze clusteru Hortonworks Data Platform (HDP).

2.  Pomocí následujícího příkazu vytvořte a upravte nový soubor:
 ```bash
nano workflow.xml
 ```

3. Jakmile se otevře nano editor, zadejte následující kód XML jako obsah souboru:
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
4. Nahraďte `clustername` s názvem clusteru. 

5. K uložení souboru, vyberte kombinaci kláves Ctrl + X. Zadejte `Y`. Potom vyberte **Enter**.

    Pracovní postup je rozdělena na dva oddíly:
    *   **Části přihlašovací údaje.** Tato část má přihlašovací údaje, které se používají k ověřování Oozie akce:

       Tento příklad používá ověřování pro Hive akce. Další informace najdete v tématu [ověřování akce](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

       Přihlašovací údaje služby umožňuje Oozie zosobnit uživatele pro přístup ke službám Hadoop.

    *   **Část akce.** Tato část obsahuje tři akce: pro redukci map, Hive server 2 a Hive server 1:

      - Příklad z balíčku pro Oozie pro redukci map spustí akce, které Vypíše počet agregovaných slov redukci map.

       - Hive server 2 a Hive server 1 akce na vzorovou tabulkou Hive s HDInsight spustí dotaz.

        Hive akce používají přihlašovací údaje definované v části s přihlašovacími údaji pro ověřování pomocí klíčového slova `cred` v prvku akce.

6. Použijte následující příkaz pro kopírování `workflow.xml` soubor `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`:
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

7. Nahraďte `domainuser` se svým uživatelským jménem pro doménu.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Definování vlastnosti souboru pro úlohu Oozie

1. Pomocí následujícího příkazu vytvořte a upravte nový soubor pro vlastnosti úlohy:

   ```bash
   nano job.properties
   ```

2. Jakmile se otevře nano editor, použijte následující kód XML jako obsah souboru:

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
       jdbcPrincipal=hive/hn0-<ClusterShortName>.<Domain>.com@<Domain>.COM
       jdbcURL=[jdbcurlvalue]
       hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
       hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```
  
   a. Nahraďte `domainuser` se svým uživatelským jménem pro doménu.  
   b. Nahraďte `ClusterShortName` s krátkým názvem clusteru. Například, pokud je název clusteru https:// *[Příklad odkaz]* sechadoopcontoso.azurehdisnight.net, `clustershortname` je prvních šest znaků clusteru: **sechad**.  
   c. Nahraďte `jdbcurlvalue` s adresou URL JDBC z konfigurace Hive. Příkladem je jdbc:hive2: / / headnodehost:10001 /; režim = http.      
   d. K uložení souboru, vyberte kombinaci kláves Ctrl + X, zadejte `Y`a pak vyberte **Enter**.

   Tento soubor vlastnosti musí být k dispozici místně při spuštění úlohy Oozie.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Vytvoření vlastních skriptů Hive pro úlohy Oozie

Můžete vytvořit dvě skriptů Hive pro Hive server 1 a Hive server 2, jak je znázorněno v následujících částech.

### <a name="hive-server-1-file"></a>Soubor Hive server 1

1.  Vytvoření a úprava souboru pro Hive server 1 akce:
    ```bash
    nano countrowshive1.hql
    ```

2.  Vytvořte skript:
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  Uložte soubor do Apache HDFS Hadoop Distributed File System ():
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Soubor Hive server 2

1.  Vytvoření a úprava pole pro Hive server 2 akce:
    ```bash
    nano countrowshive2.hql
    ```

2.  Vytvořte skript:
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  Uložte soubor do rozhraní HDFS:
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Odesílání úloh Oozie

Odesílání úloh Oozie pro clustery ESP je jako odesílání Oozie úloh v clusterech bez ESP.

Další informace najdete v tématu [použití Apache Oozie s Hadoopem Apache k definování a spuštění workflowu v Azure HDInsight založených na Linuxu](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-an-oozie-job-submission"></a>Výsledky odeslání úlohy Oozie
Oozie úloha pro daného uživatele. Takže Apache Hadoop YARN a Apache Rangeru auditu zobrazit protokoly úloh spuštěn jako zosobněného uživatele. Rozhraní příkazového řádku výstup úlohy Oozie by měl vypadat jako v následujícím kódu:



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

V protokolech auditu Ranger Hive server 2 akce zobrazit Oozie spuštění akce uživatele. Zobrazení Ranger a YARN jsou viditelné pouze pro správce clusteru.

## <a name="configure-user-authorization-in-oozie"></a>Konfigurace autorizace uživatelů v Oozie

Oozie sám o sobě má konfiguraci autorizace uživatele, který může zablokovat uživatelům možnost zastavení nebo odstranění úlohy jinými uživateli. Chcete-li povolit tuto konfiguraci, nastavte `oozie.service.AuthorizationService.security.enabled` k `true`. 

Další informace najdete v tématu [Apache Oozie instalace a konfigurace](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html).

Pro komponenty, jako jsou serveru Hive 1, kdy modul plug-in Ranger není k dispozici nebo se nepodporuje je možné pouze hrubých HDFS autorizace. Detailní autorizace je k dispozici pouze prostřednictvím Ranger moduly plug-in.

## <a name="get-the-oozie-web-ui"></a>Získat Oozie webového uživatelského rozhraní

Webové uživatelské rozhraní Oozie poskytuje webové zobrazení stavu úlohy Oozie v clusteru. Pokud chcete získat ve webovém uživatelském rozhraní, proveďte následující kroky v clusterech ESP:

1. Přidat [hraniční uzel](../hdinsight-apps-use-edge-node.md) a povolit [SSH ověřování pomocí protokolu Kerberos](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Postupujte podle [Oozie webového uživatelského rozhraní](../hdinsight-use-oozie-linux-mac.md) postup pro povolení tunelového propojení SSH k hraničnímu uzlu a přístup k webovým Uživatelským rozhraním.

## <a name="next-steps"></a>Další postup
* [Použití Apache Oozie s Hadoopem Apache k definování a spuštění workflowu v Azure HDInsight založených na Linuxu](../hdinsight-use-oozie-linux-mac.md).
* [Použití Apache Oozie coordinator podle času](../hdinsight-use-oozie-coordinator-time.md).
* [Připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
