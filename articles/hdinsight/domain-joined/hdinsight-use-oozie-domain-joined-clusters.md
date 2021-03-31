---
title: Pracovní postupy Apache Oozie & Enterprise Security – Azure HDInsight
description: Zabezpečte Oozie pracovní postupy Apache pomocí Azure HDInsight Balíček zabezpečení podniku. Naučte se definovat pracovní postup Oozie a odeslat úlohu Oozie.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 05/14/2020
ms.openlocfilehash: dc20a258028bb76351ae70362234e7c4c8c741a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101699398"
---
# <a name="run-apache-oozie-in-azure-hdinsight-clusters-with-enterprise-security-package"></a>Spuštění Apache Oozie v clusterech Azure HDInsight s Balíček zabezpečení podniku

Apache Oozie je systém pro pracovní postupy a koordinaci, který spravuje úlohy Apache Hadoop. Oozie je integrovaný do zásobníku Hadoop a podporuje následující úlohy:

- Apache MapReduce
- Apache prasete
- Apache Hive
- Apache Sqoop

Oozie můžete použít také k plánování úloh, které jsou specifické pro systém, jako jsou programy Java nebo skripty prostředí.

## <a name="prerequisite"></a>Požadavek

Cluster Azure HDInsight Hadoop s Balíček zabezpečení podniku (ESP). Viz téma [konfigurace clusterů HDInsight s](./apache-domain-joined-configure-using-azure-adds.md)protokolem ESP.

> [!NOTE]  
> Podrobné pokyny, jak používat Oozie v clusterech bez ESP, najdete v tématu [použití pracovních postupů Apache Oozie v Azure HDInsight](../hdinsight-use-oozie-linux-mac.md)se systémem Linux.

## <a name="connect-to-an-esp-cluster"></a>Připojení k clusteru ESP

Další informace o Secure Shell (SSH) najdete v tématu [připojení ke službě HDInsight (Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Připojte se ke clusteru HDInsight pomocí SSH:

    ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```

1. K ověření úspěšného ověřování pomocí protokolu Kerberos použijte `klist` příkaz. Pokud ne, použijte `kinit` ke spuštění ověřování protokolem Kerberos.

1. Přihlaste se k bráně HDInsight a zaregistrujte token OAuth vyžadovaný pro přístup k Azure Data Lake Storage:

    ```bash
    curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
    ```

    Kód odpovědi na stav **200 OK** indikuje úspěšnou registraci. Ověřte uživatelské jméno a heslo, pokud je přijata Neautorizovaná odpověď, například 401.

## <a name="define-the-workflow"></a>Definovat pracovní postup

Definice pracovních postupů Oozie jsou napsané v jazyce hPDL (Apache Hadoop proces Definition Language). hPDL je jazyk definice procesu XML. Pro definování pracovního postupu proveďte následující kroky:

1. Nastavte pracovní prostor uživatele domény:

   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```

   Nahraďte `DomainUser` názvem doménového uživatele.
   Nahraďte `DomainUserPath` cestou k domovskému adresáři pro uživatele domény.
   Nahraďte `ClusterVersion` svou verzí datové platformy v clusteru.

2. Pomocí následujícího příkazu vytvořte a upravte nový soubor:

   ```bash
   nano workflow.xml
   ```

3. Po otevření editoru nano zadejte jako obsah souboru následující kód XML:

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

4. Nahraďte `clustername` názvem clusteru.

5. Pokud chcete soubor uložit, vyberte **CTRL + X**. Zadejte **Y**. Pak vyberte **ENTER**.

    Pracovní postup je rozdělen do dvou částí:

   - **Pověřovací.** Tato část přebírá přihlašovací údaje, které se používají k ověřování akcí Oozie:

     V tomto příkladu se používá ověřování pro akce podregistru. Další informace najdete v tématu [ověřování akcí](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

     Služba pověření umožňuje akcím Oozie zosobnit uživatele pro přístup ke službám Hadoop.

   - **Kroky.** Tato část obsahuje tři akce: omezit mapování, server pro podregistr 2 a podregistr Server 1:

     - Akce snížit mapu spustí příklad z balíčku Oozie pro mapu – zmenšuje výstup agregovaného počtu slov.

     - Akce pro podregistr Server 2 a podregistr Server 1 spouštějí dotaz na ukázkové tabulce podregistru dodávané se službou HDInsight.

     Akce podregistru používají pověření definovaná v oddílu přihlašovací údaje pro ověřování pomocí klíčového slova `cred` v elementu Action.

6. Pomocí následujícího příkazu zkopírujte `workflow.xml` soubor do `/user/<domainuser>/examples/apps/map-reduce/workflow.xml` :

    ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
    ```

7. Nahraďte `domainuser` svým uživatelským jménem pro doménu.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Zadejte soubor vlastností pro úlohu Oozie

1. Pomocí následujícího příkazu vytvořte a upravte nový soubor pro vlastnosti úlohy:

    ```bash
    nano job.properties
    ```

2. Po otevření editoru nano použijte jako obsah souboru následující kód XML:

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

   - Pokud jste `adl://home` `nameNode` Azure Data Lake Storage Gen1 jako primární úložiště clusteru, použijte identifikátor URI pro vlastnost. Pokud používáte Azure Blob Storage, přejděte na `wasb://home` . Pokud používáte Azure Data Lake Storage Gen2, přejděte na `abfs://home` .
   - Nahraďte `domainuser` svým uživatelským jménem pro doménu.
   - Nahraďte `ClusterShortName` krátkým názvem clusteru. Pokud je název clusteru například https:// *[example Link]* sechadoopcontoso.azurehdisnight.NET, `clustershortname` je prvních šest znaků clusteru: **sechad**.
   - Nahraďte `jdbcurlvalue` adresou URL JDBC z konfigurace podregistru. Příklad je JDBC: hive2://headnodehost: 10001/; transportMode = http.
   - Pokud chcete soubor uložit, vyberte CTRL + X, zadejte `Y` a pak vyberte **ENTER**.

   Tento soubor vlastností musí být přítomen místně při spouštění úloh Oozie.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Vytváření vlastních skriptů podregistru pro úlohy Oozie

Můžete vytvořit dva skripty pro podregistr Server 1 a podregistr Server 2, jak je znázorněno v následujících oddílech.

### <a name="hive-server-1-file"></a>Soubor serveru s podregistr 1

1. Vytvořte a upravte soubor pro akce serveru s podregistru 1:

    ```bash
    nano countrowshive1.hql
    ```

2. Vytvořte skript:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    select devicemake from hivesampletable limit 2;
    ```

3. Uložte soubor do systém souborů DFS (Distributed File System) Apache Hadoop (HDFS):

    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Soubor pro podregistr Server 2

1. Vytvoření a úprava pole pro akce pro podregistr Server 2:

    ```bash
    nano countrowshive2.hql
    ```

2. Vytvořte skript:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3. Uložte soubor do HDFS:

    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Odeslat Oozie úlohy

Odesílání úloh Oozie pro clustery ESP je jako odesílání úloh Oozie v clusterech bez ESP.

Další informace najdete v tématu [použití Apache Oozie s Apache Hadoop k definování a spuštění pracovního postupu pro Azure HDInsight se systémem Linux](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-an-oozie-job-submission"></a>Výsledky odeslání úlohy Oozie

Pro uživatele se spouštějí úlohy Oozie. Jak Apache Hadoop PŘÍZe, tak i protokoly auditu Apache Ranger zobrazují úlohy, které se spouštějí jako zosobněný uživatel. Výstup rozhraní příkazového řádku Oozie úlohy vypadá jako v následujícím kódu:

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
ID                        Status    Ext ID            ExtStatus                 ErrCode
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@:start:    OK    -                         OK             -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@mr-test    OK    job_1529975666160_0051    SUCCEEDED      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@myHive2    OK    job_1529975666160_0053    SUCCEEDED      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@myHive    OK     job_1529975666160_0055    SUCCEEDED      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@end       OK     -                         OK             -
-----------------------------------------------------------------------------------------------
```

V protokolech auditu Ranger pro podregistr Server 2 se zobrazuje Oozie, který spouští akci pro uživatele. Zobrazení Ranger a PŘÍZe jsou viditelná pouze pro správce clusteru.

## <a name="configure-user-authorization-in-oozie"></a>Konfigurace autorizace uživatele v Oozie

Oozie sám má konfiguraci autorizace uživatele, která může zablokovat uživatelům zastavit nebo odstranit úlohy jiných uživatelů. Chcete-li povolit tuto konfiguraci, nastavte na `oozie.service.AuthorizationService.security.enabled` `true` . 

Další informace najdete v tématu [instalace a konfigurace Apache Oozie](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html).

Pro součásti jako podregistr Server 1, kde není modul plug-in Ranger k dispozici nebo není podporován, je možné použít pouze hrubou autorizaci HDFS. Jemně odstupňovaná autorizace je k dispozici pouze prostřednictvím modulů plug-in Ranger.

## <a name="get-the-oozie-web-ui"></a>Získání webového uživatelského rozhraní Oozie

Webové uživatelské rozhraní Oozie poskytuje webové zobrazení na stav úloh Oozie v clusteru. Chcete-li získat webové uživatelské rozhraní, proveďte následující kroky v clusterech ESP:

1. Přidejte [hraniční uzel](../hdinsight-apps-use-edge-node.md) a povolte [ověřování pomocí protokolu SSH Kerberos](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Postupujte podle kroků [webového uživatelského rozhraní Oozie](../hdinsight-use-oozie-linux-mac.md) a povolte tunelování SSH na hraničním uzlu a přístup k WEBOVÉmu uživatelskému rozhraní.

## <a name="next-steps"></a>Další kroky

- [Použijte Apache Oozie s Apache Hadoop k definování a spuštění pracovního postupu pro Azure HDInsight se systémem Linux](../hdinsight-use-oozie-linux-mac.md).
- [Připojte se k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
