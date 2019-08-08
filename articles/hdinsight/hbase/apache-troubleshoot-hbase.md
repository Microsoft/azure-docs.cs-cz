---
title: Řešení potíží s využitím služby HBA pomocí Azure HDInsight
description: Získejte odpovědi na běžné otázky týkající se práce s adaptéry HBA a Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 13a4831d946eb7e25e586cafae4cae51b49fd8a7
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780757"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Řešení potíží s Apache HBA pomocí Azure HDInsight

Přečtěte si o hlavních problémech a jejich řešeních, pokud pracujete s webovými částmi Apache HBA v Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Návody spustit sestavy příkazů hbck s více nepřiřazenými oblastmi?

Častá chybová zpráva, kterou se vám může zobrazit při spuštění `hbase hbck` příkazu, je nepřiřazené více oblastí nebo díry v řetězci oblastí.

V uživatelském rozhraní HBase Master uvidíte počet oblastí, které jsou nevyvážené napříč všemi oblastmi serverů. Pak můžete spustit `hbase hbck` příkaz pro zobrazení děr v řetězci oblasti.

Díry mohou být způsobeny offline oblastmi, proto nejprve opravte přiřazení. 

Chcete-li nepřiřazené oblasti převést zpět do normálního stavu, proveďte následující kroky:

1. Přihlaste se ke clusteru HDInsight HBA pomocí SSH.
2. Pokud se chcete připojit pomocí prostředí Apache Zookeeper, spusťte `hbase zkcli` příkaz.
3. `rmr /hbase/regions-in-transition` Spusťte příkaz`rmr /hbase-unsecure/regions-in-transition` nebo příkaz.
4. Pro ukončení `hbase zkcli` prostředí `exit` použijte příkaz.
5. Otevřete uživatelské rozhraní Apache Ambari a pak restartujte službu Active HBase Master.
6. `hbase hbck` Spusťte příkaz znovu (bez jakýchkoli možností). Zkontrolujte výstup tohoto příkazu a ujistěte se, že jsou přiřazeny všechny oblasti.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Návody opravit problémy s časovým limitem při použití příkazů hbck pro přiřazení oblastí?

### <a name="issue"></a>Problém

Možnou příčinou potíží s vypršením časového limitu `hbck` při použití příkazu může být, že několik oblastí je ve stavu "v přechodu" po dlouhou dobu. Tyto oblasti můžete zobrazit v uživatelském rozhraní HBase Master v režimu offline. Vzhledem k tomu, že se snaží přejít velký počet oblastí, HBase Master může mít časový limit a nebude možné tyto oblasti převést zpět do stavu online.

### <a name="resolution-steps"></a>Postup řešení

1. Přihlaste se ke clusteru HDInsight HBA pomocí SSH.
2. Pokud se chcete připojit pomocí prostředí Apache Zookeeper, spusťte `hbase zkcli` příkaz.
3. `rmr /hbase/regions-in-transition` Spusťte příkaz`rmr /hbase-unsecure/regions-in-transition` nebo.
4. K ukončení `hbase zkcli` prostředí `exit` použijte příkaz.
5. V uživatelském rozhraní Ambari restartujte službu Active HBase Master.
6. Znovu spusťte `hbase hbck -fixAssignments` příkaz.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Návody vynuceně zakázat bezpečný režim HDFS v clusteru?

### <a name="issue"></a>Problém

Místní Apache Hadoop systém souborů DFS (Distributed File System) (HDFS) se zablokuje v bezpečném režimu v clusteru HDInsight.

### <a name="detailed-description"></a>Podrobný popis

Tato chyba může být způsobena selháním při spuštění následujícího příkazu HDFS:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

Chyba, kterou se vám může zobrazit při pokusu o spuštění příkazu, vypadá takto:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:4010)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1102)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:630)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:640)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2313)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2309)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1724)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2307)
        at org.apache.hadoop.ipc.Client.getRpcResponse(Client.java:1552)
        at org.apache.hadoop.ipc.Client.call(Client.java:1496)
        at org.apache.hadoop.ipc.Client.call(Client.java:1396)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:233)
        at com.sun.proxy.$Proxy10.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.mkdirs(ClientNamenodeProtocolTranslatorPB.java:603)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:278)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:194)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:176)
        at com.sun.proxy.$Proxy11.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.DFSClient.primitiveMkdir(DFSClient.java:3061)
        at org.apache.hadoop.hdfs.DFSClient.mkdirs(DFSClient.java:3031)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1162)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1158)
        at org.apache.hadoop.fs.FileSystemLinkResolver.resolve(FileSystemLinkResolver.java:81)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirsInternal(DistributedFileSystem.java:1158)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirs(DistributedFileSystem.java:1150)
        at org.apache.hadoop.fs.FileSystem.mkdirs(FileSystem.java:1898)
        at org.apache.hadoop.fs.shell.Mkdir.processNonexistentPath(Mkdir.java:76)
        at org.apache.hadoop.fs.shell.Command.processArgument(Command.java:273)
        at org.apache.hadoop.fs.shell.Command.processArguments(Command.java:255)
        at org.apache.hadoop.fs.shell.FsCommand.processRawArguments(FsCommand.java:119)
        at org.apache.hadoop.fs.shell.Command.run(Command.java:165)
        at org.apache.hadoop.fs.FsShell.run(FsShell.java:297)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:90)
        at org.apache.hadoop.fs.FsShell.main(FsShell.java:350)
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

### <a name="probable-cause"></a>Pravděpodobná příčina

Cluster HDInsight se škáloval dolů na velmi málo uzlů. Počet uzlů je nižší nebo blízko faktoru replikace HDFS.

### <a name="resolution-steps"></a>Postup řešení 

1. Spuštěním následujících příkazů Získejte stav HDFS v clusteru HDInsight:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   ```

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   Safe mode is ON
   Configured Capacity: 3372381241344 (3.07 TB)
   Present Capacity: 3138625077248 (2.85 TB)
   DFS Remaining: 3102710317056 (2.82 TB)
   DFS Used: 35914760192 (33.45 GB)
   DFS Used%: 1.14%
   Under replicated blocks: 0
   Blocks with corrupt replicas: 0
   Missing blocks: 0
   Missing blocks (with replication factor 1): 0

   -------------------------------------------------
   Live datanodes (8):

   Name: 10.0.0.17:30010 (10.0.0.17)
   Hostname: 10.0.0.17
   Decommission Status : Normal
   Configured Capacity: 421547655168 (392.60 GB)
   DFS Used: 5288128512 (4.92 GB)
   Non DFS Used: 29087272960 (27.09 GB)
   DFS Remaining: 387172253696 (360.58 GB)
   DFS Used%: 1.25%
   DFS Remaining%: 91.85%
   Configured Cache Capacity: 0 (0 B)
   Cache Used: 0 (0 B)
   Cache Remaining: 0 (0 B)
   Cache Used%: 100.00%
   Cache Remaining%: 0.00%
   Xceivers: 2
   Last contact: Wed Apr 05 16:22:00 UTC 2017
   ...

   ```
2. Integritu HDFS v clusteru HDInsight můžete také ověřit pomocí následujících příkazů:

   ```apache
   hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
   ```

   ```apache
   Connecting to namenode via http://hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net:30070/fsck?ugi=hdiuser&path=%2F
   FSCK started by hdiuser (auth:SIMPLE) from /10.0.0.22 for path / at Wed Apr 05 16:40:28 UTC 2017
   ....................................................................................................

   ....................................................................................................
   ..................Status: HEALTHY
   Total size:    9330539472 B
   Total dirs:    37
   Total files:   2618
   Total symlinks:                0 (Files currently being written: 2)
   Total blocks (validated):      2535 (avg. block size 3680686 B)
   Minimally replicated blocks:   2535 (100.0 %)
   Over-replicated blocks:        0 (0.0 %)
   Under-replicated blocks:       0 (0.0 %)
   Mis-replicated blocks:         0 (0.0 %)
   Default replication factor:    3
   Average block replication:     3.0
   Corrupt blocks:                0
   Missing replicas:              0 (0.0 %)
   Number of data-nodes:          8
   Number of racks:               1
   FSCK ended at Wed Apr 05 16:40:28 UTC 2017 in 187 milliseconds

   The filesystem under path '/' is HEALTHY
   ```

3. Pokud zjistíte, že nejsou k dispozici žádné, poškozené nebo v replikovaných blocích nebo že tyto bloky lze ignorovat, spusťte následující příkaz, který převezme uzel názvu z bezpečného režimu:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Návody opravit problémy s připojením k JDBC nebo SQLLine pomocí Apache Phoenix?

### <a name="resolution-steps"></a>Postup řešení

Chcete-li se připojit pomocí Apache Phoenix, je nutné zadat IP adresu aktivního Apache ZooKeeper uzlu. Ujistěte se, že je služba ZooKeeper, ke které se pokouší připojit sqlline.py, spuštěná.
1. Přihlaste se ke clusteru HDInsight pomocí SSH.
2. Zadejte následující příkaz:
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > IP adresu aktivního uzlu ZooKeeper můžete získat z uživatelského rozhraní Ambari. Přejít na **adaptéry HBA** > **Rychlé odkazy** >  > **ZK\* (aktivní)** **Zookeeper informace**. 

3. Pokud se sqlline.py připojuje k Phoenixu a nevypršel časový limit, spusťte následující příkaz, který ověří dostupnost a stav v Phoenixu:

   ```apache
           !tables
           !quit
   ```      
4. Pokud tento příkaz funguje, nedošlo k žádnému problému. IP adresa zadaná uživatelem může být nesprávná. Pokud se ale příkaz na delší dobu zastaví a pak se zobrazí následující chyba, pokračujte krokem 5.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Spuštěním následujících příkazů z hlavního uzlu (hn0) Diagnostikujte stav systému Phoenix. Tabulka katalogu:

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   Příkaz by měl vrátit chybu podobnou následující: 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. V uživatelském rozhraní Apache Ambari proveďte následující kroky a restartujte službu HMaster na všech uzlech ZooKeeper:

    1. V části **Souhrn** adaptérů HBA, přejít na > HBA**aktivní HBase Master**. 
    2. V části **součásti** restartujte službu HBase Master.
    3. Tento postup opakujte pro všechny zbývající **pohotovostní HBase Master** služby. 

Aby služba HBase Master mohla stabilizovat a dokončit proces obnovení, může trvat až pět minut. Po několika minutách opakujte příkazy sqlline.py a potvrďte tak, že systém. Tabulka katalogu je nahoru a na ni se dá dotazovat. 

Když je systém. Tabulka katalogu je zpět na normální, problém s připojením k Phoenixu by se měl automaticky vyřešit.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Co způsobuje neúspěšné spuštění hlavního serveru?

### <a name="error"></a>Chyba 

Dojde k chybě atomické přejmenování.

### <a name="detailed-description"></a>Podrobný popis

Během procesu spuštění HMaster dokončí mnoho inicializačních kroků. Mezi ně patří přesun dat ze složky Scratch (. tmp) do složky data. HMaster také zjistí, zda neexistují žádné nereagující servery oblastí a tak dále. 

Při spuštění HMaster provede základní `list` příkaz na těchto složkách. Pokud HMaster zobrazí neočekávaný soubor v některé z těchto složek, vyvolá výjimku a nespustí se.  

### <a name="probable-cause"></a>Pravděpodobná příčina

V protokolech serveru oblastí se pokuste identifikovat časovou osu pro vytvoření souboru a potom zjistit, zda došlo k chybě procesu v době, kdy byl soubor vytvořen. (Obraťte se na podporu adaptérů HBA, které vám pomůžou.) To nám pomáhá zajistit robustnější mechanismy, abyste se vyhnuli zahnutí se na tuto chybu a zajistili bezproblémové vypnutí procesů.

### <a name="resolution-steps"></a>Postup řešení

Zkontrolujte zásobník volání a pokuste se určit, která složka může způsobovat problém (například může se jednat o složku WALs nebo složku. tmp). Potom v Průzkumníku cloudu nebo pomocí příkazů HDFS zkuste najít soubor problému. Obvykle se jedná o \*soubor-renamePending. JSON. (Soubor \*-renamePending. JSON je soubor deníku, který se používá k implementaci operace pro atomické přejmenování v ovladači WASB. Z důvodu chyb v této implementaci mohou být tyto soubory ponechány po selhání procesu a tak dále.) Vynutit – odstraňte tento soubor buď v Průzkumníku cloudu, nebo pomocí příkazů HDFS. 

V tomto umístění může být někdy také dočasný soubor s názvem něco jako *$ $ $. $ $ $.* K zobrazení tohoto souboru je `ls` nutné použít příkaz HDFS. soubor nelze v Průzkumníkovi cloudu zobrazit. K odstranění tohoto souboru použijte příkaz `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`HDFS.  

Po spuštění těchto příkazů by se měl HMaster spustit hned. 

### <a name="error"></a>Chyba

Žádná adresa serveru není uvedená v seznamu *HBA: meta* pro oblast xxx.

### <a name="detailed-description"></a>Podrobný popis

V clusteru Linux se může zobrazit zpráva s informacemi o tom, že *adaptéry HBA: meta* tabulka není online. Běh `hbck` může hlásit, že "HBA: meta Table replicaId 0 se v žádné oblasti nenašel." Tento problém může být způsoben tím, že HMaster nebylo možné inicializovat po restartování adaptérů HBA. V protokolech HMaster se může zobrazit zpráva: "Žádná adresa serveru uvedená v části hbaes: meta pro oblast HBA: název \<\>oblasti zálohy".  

### <a name="resolution-steps"></a>Postup řešení

1. V prostředí HBA zadejte následující příkazy (podle potřeby změňte skutečné hodnoty):  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Odstraňte položku *HBA: entry oboru názvů* . Tato položka může být shodná s chybou, která je hlášena při kontrole, když je prohledávána tabulka *HBA: Namespace* .

3. Pokud chcete aktivovat adaptéry HBA ve spuštěném stavu, restartujte v uživatelském rozhraní Ambari službu Active HMaster.  

4. V prostředí HBA spusťte následující příkaz a zobrazte tak všechny offline tabulky:

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>Další čtení

[Nepovedlo se zpracovat tabulku adaptérů HBA.](https://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Chyba

HMaster vyprší s závažnou výjimkou, která je podobná "Java. IO. IOException: Vypršel časový limit 300000ms čekání na přiřazení tabulky oboru názvů. "

### <a name="detailed-description"></a>Podrobný popis

K tomuto problému může dojít, pokud máte spoustu tabulek a oblastí, které se při restartování služeb HMaster nevyprázdnily. Restartování se nemusí zdařit a zobrazí se předchozí chybová zpráva.  

### <a name="probable-cause"></a>Pravděpodobná příčina

Jedná se o známý problém se službou HMaster. Obecné úlohy při spuštění clusteru můžou trvat dlouhou dobu. HMaster se ukončí, protože tabulka oboru názvů ještě není přiřazená. K tomu dochází pouze ve scénářích, ve kterých existuje velké množství nevyprázdněných dat a časový limit pět minut není dostačující.
  
### <a name="resolution-steps"></a>Postup řešení

1. V uživatelském rozhraní Apache Ambari přejít na**Konfigurace** **HBA** > . Do vlastního souboru HBase-site. xml přidejte následující nastavení: 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Restartujte požadované služby (HMaster a případně jiné služby HBA).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Co způsobí selhání restartování na serveru oblasti?

### <a name="issue"></a>Problém

Neúspěšné restartování na serveru oblasti může bránit následujícímu osvědčeným postupům. Při plánování restartování serverů oblastí HBA doporučujeme, abyste pozastavili velkou aktivitu úlohy. Pokud se aplikace i nadále připojuje se servery oblastí, když probíhá vypínání, operace restartování serveru oblasti bude pomalejší o několik minut. Je také vhodné nejprve vyprázdnit všechny tabulky. Referenční informace o tom, jak vyprázdnit tabulky [, najdete v tématu HDInsight HBA: Jak vylepšit dobu restartování clusteru Apache HBA vyprázdněním tabulek](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Pokud zahájíte operaci restartování na serverech oblasti HBA v uživatelském rozhraní Apache Ambari, okamžitě uvidíte, že se servery oblasti nedají vypnout, ale nerestartují se hned. 

Co je se děje na pozadí: 

1. Agent Ambari odešle požadavek na zastavení na server oblasti.
2. Agent Ambari počká, až 30 sekund, aby se server oblasti řádně vypnul. 
3. Pokud se vaše aplikace nadále připojuje k serveru oblastí, server se nevypne hned. Časový limit 30 sekund vyprší, než dojde k vypnutí. 
4. Po 30 sekundách pošle agent Ambari na server oblasti příkaz Force-`kill -9`Kill (). Můžete to vidět v protokolu Ambari-Agent (v adresáři/var/log/příslušného pracovního uzlu):

   ```apache
           2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
           est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
           f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
           -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
           2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
           /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
           PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
           nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
           2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
           2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
   ```
   Kvůli náhlému vypnutí nemusí být port přidružený k procesu uvolněný, i když je proces serveru oblasti zastavený. Tato situace může vést k AddressBindException při spuštění serveru oblasti, jak je znázorněno v následujících protokolech. To můžete ověřit v region-Server. log v adresáři/var/log/HBase na pracovních uzlech, kde se nepodařilo spustit server oblasti. 

   ```apache

   2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
   java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
   at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
   at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
        
   Caused by: java.lang.reflect.InvocationTargetException
   at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
   at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
   at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
   at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
   ... 5 more
        
   Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
   at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
   at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
   at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
   ... 10 more
        
   Caused by: java.net.BindException: Address already in use
   at sun.nio.ch.Net.bind0(Native Method)
   at sun.nio.ch.Net.bind(Net.java:463)
   at sun.nio.ch.Net.bind(Net.java:455)
   at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
   at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
   ... 15 more
   ```

### <a name="resolution-steps"></a>Postup řešení

1. Před zahájením restartování se pokuste snížit zatížení serverů oblastí HBA. 
2. Případně (pokud to krok 1 nepomůže), zkuste ručně restartovat servery oblastí na pracovních uzlech pomocí následujících příkazů:

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

### <a name="see-also"></a>Viz také
[Řešení potíží pomocí Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
