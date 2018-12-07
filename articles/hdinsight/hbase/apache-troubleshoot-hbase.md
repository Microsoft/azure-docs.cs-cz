---
title: Řešení potíží s HBase pomocí Azure HDInsight
description: Získejte odpovědi na běžné dotazy týkající se práce s HBase a Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: b39c01e76ba3ec21f0cd2d16b86da5664e1d5002
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014663"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Řešení potíží s Apache HBase pomocí Azure HDInsight

Další informace o nejčastější problémy a jejich řešení při práci s datovými částmi Apache HBase v Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Jak spustit hbck příkaz sestavy s více oblastmi nepřiřazené?

Běžné chybovou zprávu, může se zobrazit při spuštění `hbase hbck` příkaz je "více oblastí se nepřiřazený nebo otvory v řetězu certifikátů oblastí."

V uživatelském rozhraní hlavní server HBase můžete zobrazit počet oblastí, které jsou nevyvážené napříč všemi servery oblasti. Potom můžete spustit `hbase hbck` příkazu zobrazte otvory v řetězci oblasti.

Děr může být způsobeno offline oblastí, takže přiřazení nejprve opravte. 

Přenést do normálního stavu nepřiřazené oblastí, proveďte následující kroky:

1. Přihlaste se ke clusteru HDInsight HBase pomocí SSH.
2. Chcete-li připojit pomocí prostředí Apache ZooKeeper, spusťte `hbase zkcli` příkazu.
3. Spustit `rmr /hbase/regions-in-transition` příkazu nebo `rmr /hbase-unsecure/regions-in-transition` příkazu.
4. Pro ukončení `hbase zkcli` prostředí, použijte `exit` příkazu.
5. Otevřete uživatelské rozhraní Apache Ambari a potom restartujte službu aktivní hlavní server HBase.
6. Spustit `hbase hbck` příkaz znovu (bez jakýchkoli možností). Zkontrolujte výstup tohoto příkazu k zajištění, že jsou přiřazení všech oblastech.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Jak se při použití hbck příkazy pro oblast přiřazení opravit vypršení časového limitu?

### <a name="issue"></a>Problém

Potenciální příčinu vypršení časového limitu problémy při použití `hbck` příkaz může být, že jsou ve stavu "v přechodném stavu" několika oblastech po dlouhou dobu. Těmito oblastmi můžete zobrazit jako offline v Uživatelském rozhraní hlavní server HBase. Vzhledem k tomu, že vysoký počet oblastí se pokoušíte přejít, hlavní server HBase může být vypršení časového limitu a být schopen převést zpět do online režimu v těchto oblastech.

### <a name="resolution-steps"></a>Postup řešení

1. Přihlaste se ke clusteru HDInsight HBase pomocí SSH.
2. Chcete-li připojit pomocí prostředí Apache ZooKeeper, spusťte `hbase zkcli` příkazu.
3. Spustit `rmr /hbase/regions-in-transition` nebo `rmr /hbase-unsecure/regions-in-transition` příkazu.
4. Chcete-li ukončit `hbase zkcli` prostředí, použijte `exit` příkazu.
5. V uživatelském rozhraní Ambari restartujte službu aktivní hlavní server HBase.
6. Spustit `hbase hbck -fixAssignments` příkaz znovu.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Jak můžu platnost zakázat HDFS Nouzový režim v clusteru?

### <a name="issue"></a>Problém

V nouzovém režimu v clusteru HDInsight se zasekla místní Apache HDFS Hadoop Distributed File System ().

### <a name="detailed-description"></a>Podrobný popis

Tato chyba může být způsobena chybou při spuštění následujícího příkazu HDFS:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

Chyby, které se můžete setkat při pokusu spustit příkaz vypadá takto:

```apache
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
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

Dolů na byla změněna velikost clusteru HDInsight velmi několika uzlů. Počet uzlů je níže nebo blízko faktor replikace HDFS.

### <a name="resolution-steps"></a>Postup řešení 

1. Získejte stav HDFS v clusteru HDInsight spuštěním následujících příkazů:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   ```

   ```apache
   hdiuser@hn0-spark2:~$ hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
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
2. Také můžete zkontrolovat integritu HDFS v clusteru HDInsight pomocí následujících příkazů:

   ```apache
   hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
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

3. Pokud zjistíte, že neexistují žádné chybějící, poškozený, nebo under-replikované bloků nebo že můžete tyto bloky ignorovat, spusťte následující příkaz se název uzlu mimo nouzový režim:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Jak opravit připojení JDBC nebo SQLLine problémy s Apache Phoenix?

### <a name="resolution-steps"></a>Postup řešení

Pro připojení k Apache Phoenix, je nutné zadat IP adresu z aktivního uzlu Apache ZooKeeper. Zajistěte, aby službě ZooKeeper a které sqlline.py se pokouší o připojení je zprovozněný.
1. Přihlaste se ke clusteru HDInsight pomocí SSH.
2. Zadejte následující příkaz:
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > Získat IP adresu z aktivního uzlu ZooKeeper z uživatelského rozhraní Ambari. Přejděte na **HBase** > **rychlé odkazy** > **ZK\* (aktivní)** > **Zookeeper informace**. 

3. Pokud sqlline.py připojí k Phoenix a nemá časový limit, spusťte následující příkaz k ověření dostupnosti a stavu Phoenix:

   ```apache
           !tables
           !quit
   ```      
4. Pokud tento příkaz funguje, neexistuje žádný problém. Zadané uživatelem IP adresy můžou být nesprávné. Nicméně pokud příkaz pozastaví po delší dobu a pak se zobrazí následující chyba, přejděte ke kroku 5.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Spuštěním následujících příkazů z hlavního uzlu (hn0) Chcete-li diagnostikovat stav systému Phoenix. Tabulka se katalogu:

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   Příkaz by měla vrátit chybu podobnou této: 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. V Uživatelském rozhraní Apache Ambari proveďte následující kroky a restartujte službu HMaster na všechny uzly ZooKeeper:

    1. V **Souhrn** část HBase, přejděte na **HBase** > **aktivní hlavní server HBase**. 
    2. V **součásti** části, restartujte službu hlavní server HBase.
    3. Tento postup opakujte pro všechny zbývající **pohotovostní hlavní server HBase** služby. 

Může trvat až pět minut, než služba hlavní server HBase stabilizovat a dokončit proces obnovení. Po několika minutách opakujte příkazy sqlline.py potvrdit, že systém. Tabulka katalogu je v provozu, a to může být dotázán. 

Když v systému. Tabulka katalogu je zpět na normální, problém s připojením k Phoenix by měly být automaticky opraveny.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Co způsobí, že se hlavní server nemůže spustit?

### <a name="error"></a>Chyba 

Vyvolá se chyba atomického přejmenování.

### <a name="detailed-description"></a>Podrobný popis

Během procesu spuštění dokončí HMaster mnoho kroků inicializace. Patří mezi ně přesouvá data z nuly (.tmp) složku ke složce data. Taky sleduje HMaster složce zápisu dávky protokolů (WALs) jestli jsou všechny servery reagovat oblasti a tak dále. 

Během spouštění HMaster provádí základní `list` příkaz na těchto složek. Pokud v každém okamžiku HMaster neočekávaný soubor v některém z těchto složek, vyvolá výjimku a nespustí.  

### <a name="probable-cause"></a>Pravděpodobná příčina

Protokoly serveru oblasti zkuste k identifikaci na časové ose vytvoření souboru a potom se podívejte, pokud došlo k chybovému ukončení procesu v době, kdy byl soubor vytvořen. (Kontakt podpory HBase usnadní vám to). To pomáhá nám poskytovat robustnější mechanismus, tak, aby se můžete vyhnout se dosažení této chyby a zajistit bezproblémové proces vypnutí.

### <a name="resolution-steps"></a>Postup řešení

Zkontrolujte zásobník volání a snažte se zjistit, které složce může být příčinou problému (například může být WALs složku nebo TMP). V Průzkumníku cloudu nebo s použitím příkazů HDFS, zkuste najít soubor problém. Obvykle se jedná \*-renamePending.json souboru. ( \*-RenamePending.json soubor je soubor deníku, který se používá k implementaci operaci atomické přejmenování v ovladači WASB. Z důvodu chyby v této implementaci těchto souborů může zůstat po procesů a tak dále.) Vynucené odstranění tohoto souboru v Průzkumníku cloudu nebo s použitím příkazů HDFS. 

V některých případech můžou být dostupné taky dočasný soubor pojmenovaný podobně jako *$$$. $$$* na tomto místě. Je nutné použít HDFS `ls` příkaz, který tento soubor; nelze naleznete v souboru v Průzkumníku cloudu. Pokud chcete odstranit tento soubor, použijte příkaz HDFS `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.  

Po spuštění těchto příkazů HMaster měli spustit okamžitě. 

### <a name="error"></a>Chyba

Žádná adresa serveru je uvedena v *hbase: meta* pro oblasti xxx.

### <a name="detailed-description"></a>Podrobný popis

Může zobrazit zpráva v clusteru s Linuxem, která označuje, že *hbase: meta* tabulka není online. Spuštění `hbck` hlásit, který "hbase: metadata tabulky replicaId 0 nebyl nalezen v libovolné oblasti." Problémem může být, že HMaster nebylo možné inicializovat po restartování HBase. V protokoly HMaster se může zobrazit zpráva: "v hbase není uvedená žádná adresa serveru: metadata pro oblasti hbase: zálohování \<název oblasti\>".  

### <a name="resolution-steps"></a>Postup řešení

1. V prostředí HBase zadejte následující příkazy (skutečné hodnoty pro změnu podle vhodnosti):  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Odstranit *hbase: obor názvů* položka. Tato položka může být stejnou chybu, která je hlášen při *hbase: obor názvů* kontrole tabulky.

3. Abyste vyvolali HBase ve spuštěném stavu, v uživatelském rozhraní Ambari, restartujte službu Active HMaster.  

4. V prostředí HBase zobrazíte všechny tabulky v režimu offline, spusťte následující příkaz:

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>Další čtení

[Nelze zpracovat na tabulku HBase](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Chyba

HMaster vyprší časový limit se podobně jako závažnou výjimku "java.io.IOException: 300000ms vypršel časový limit čekání na obor názvů tabulka, která má být přiřazena."

### <a name="detailed-description"></a>Podrobný popis

Tomuto problému může dojít, pokud máte mnoho tabulek a oblasti, které nebyly byla při restartování služby HMaster vyprázdněna. Restartování může selhat a zobrazí se vám předchozí chybovou zprávu.  

### <a name="probable-cause"></a>Pravděpodobná příčina

Jedná se o známý problém službou HMaster. Úlohy po spuštění obecné clusteru může trvat dlouhou dobu. HMaster vypne protože ještě není přiřazená tabulce oboru názvů. K tomu dochází jenom v situacích, ve kterém velké množství unflushed dat existuje a není dostatečná vypršení časového limitu 5 minut.
  
### <a name="resolution-steps"></a>Postup řešení

1. V Uživatelském rozhraní Apache Ambari, přejděte na **HBase** > **Configs**. V souboru vlastní hbase-site.xml přidejte následující nastavení: 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Znovu spusťte požadované služby (HMaster a případně dalších služeb HBase).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Co způsobí, že selhání restartování na oblastním serveru?

### <a name="issue"></a>Problém

Selhání restartování na oblastním serveru může být bráněno následující osvědčené postupy. Doporučujeme, abyste při plánování restartování serverů oblasti HBase pozastavení úlohy heavy aktivity. Pokud aplikace i nadále připojit se servery pro oblast, když probíhá vypnutí, operace restartování serveru oblasti sníží se o několik minut. Je také vhodné nejprve vyprázdnit všechny tabulky. Referenční dokumentace o tom, jak vyprázdnění tabulky, najdete v části [HDInsight HBase: jak čas restartování clusteru Apache HBase můžete zlepšit vyčištění tabulek](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Pokud spustíte operace restartování na servery oblasti HBase v uživatelském rozhraní Apache Ambari, můžete okamžitě zjistit, že servery oblasti byl vypnut, ale není to hned restartovat. 

Zde je, co se děje na pozadí: 

1. Ambari agent odešle požadavek na zastavení serveru oblasti.
2. Ambari agent počká 30 sekund pro oblastním serveru řádně ukončit. 
3. Pokud vaše aplikace i nadále připojit k serveru oblasti, server nebude vypnout okamžitě. Předtím, než dojde k vypnutí vyprší časový limit 30 sekund. 
4. Po 30 sekund, odesílá Ambari agent ukončit platnost (`kill -9`) příkaz na oblastním serveru. To můžete zobrazit v protokolu agenta ambari (v /var/log/adresáři příslušné pracovní uzel):

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
Z důvodu náhlé ukončení nemusí být uvolněna port spojených s procesem, i když proces serveru oblasti je zastavená. Tato situace může vést k AddressBindException při spouštění serveru oblasti, jak je znázorněno v následující protokoly. Můžete to ověřit v server.log oblast v adresáři /var/log/hbase na pracovní uzly, kde oblastním serveru nepodaří spustit. 

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

1. Se snaží snížit zatížení na serveru oblasti HBase před zahájení restartování. 
2. Můžete také (Pokud kroku 1 vám nepomohly), zkuste ručně restartovat servery oblasti na pracovní uzly pomocí následujících příkazů:

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

### <a name="see-also"></a>Viz také
[Řešení potíží pomocí Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
