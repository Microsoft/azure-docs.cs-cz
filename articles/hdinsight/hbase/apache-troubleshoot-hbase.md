---
title: Řešení potíží s HBase pomocí Azure HDInsight | Microsoft Docs
description: Získejte odpovědi na časté otázky týkající se práce s HBase a Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinver
manager: ashitg
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 7/7/2017
ms.author: nitinver
ms.openlocfilehash: 04d8e37791c12078754a661f7a1aa8a76a6b3c44
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="troubleshoot-hbase-by-using-azure-hdinsight"></a>Řešení potíží s HBase pomocí Azure HDInsight

Další informace o hlavních problémů a jejich řešení při práci s Apache HBase datové části v Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Jak spouštět sestavy příkaz hbck s několika nepřiřazené oblastí?

Běžné chybová zpráva, že může dojít při spuštění `hbase hbck` příkaz je "více oblastí se nepřiřazené nebo díry v řetězu oblastí."

V uživatelském rozhraní HBase hlavní uvidíte počet oblastí, které jsou nevyvážené napříč všemi servery oblast. Potom můžete spustit `hbase hbck` příkazu zobrazte díry v řetězu oblast.

Díry může být způsobeno offline oblasti, takže opravte nejprve přiřazení. 

Aby nepřiřazené oblasti zpět k normálním stavu, proveďte následující kroky:

1. Přihlaste se ke clusteru HDInsight HBase pomocí protokolu SSH.
2. Chcete-li připojit pomocí prostředí ZooKeeper, spusťte `hbase zkcli` příkaz.
3. Spustit `rmr /hbase/regions-in-transition` příkaz nebo `rmr /hbase-unsecure/regions-in-transition` příkaz.
4. Chcete-li ukončit z `hbase zkcli` prostředí shell, použijte `exit` příkaz.
5. Otevřete uživatelské rozhraní Apache Ambari a potom restartujte službu hlavní HBase aktivní.
6. Spustit `hbase hbck` příkaz znovu (bez jakékoli možnosti). Zkontrolujte výstup tohoto příkazu do zajistit přiřazení všech oblastech.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Jak se při použití příkazů hbck oblasti přiřazení opravte problémy vypršení časového limitu?

### <a name="issue"></a>Problém

Možnou příčinou problémů vypršení časového limitu při použití `hbck` příkaz může být, že několik oblasti jsou ve stavu "v přechodném stavu" po dlouhou dobu. Jako offline v uživatelském rozhraní HBase hlavní najdete v těchto oblastech. Vzhledem k tomu, že vysoký počet oblastí se pokouší přechodu, hlavní HBase může časový limit a být schopen převést těmito oblastmi zpět do režimu online.

### <a name="resolution-steps"></a>Postup řešení

1. Přihlaste se ke clusteru HDInsight HBase pomocí protokolu SSH.
2. Chcete-li připojit pomocí prostředí ZooKeeper, spusťte `hbase zkcli` příkaz.
3. Spustit `rmr /hbase/regions-in-transition` nebo `rmr /hbase-unsecure/regions-in-transition` příkaz.
4. Chcete-li ukončit `hbase zkcli` prostředí shell, použijte `exit` příkaz.
5. V uživatelském rozhraní Ambari restartujte službu hlavní HBase aktivní.
6. Spustit `hbase hbck -fixAssignments` příkaz znovu.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Jak I vynucení zakázání HDFS nouzovém režimu v clusteru?

### <a name="issue"></a>Problém

Místní Hadoop Distributed File System (HDFS) se zasekla v nouzovém režimu v clusteru HDInsight.

### <a name="detailed-description"></a>Podrobný popis

Tato chyba může být způsobena chybou, když spustíte následující příkaz HDFS:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

Chyba, kterou můžete setkat při pokusu spustit příkaz vypadá takto:

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

Změny velikosti clusteru HDInsight se dolů k velmi několika uzlů. Počet uzlů je menší než nebo blízko Multi-Factor replikace HDFS.

### <a name="resolution-steps"></a>Postup řešení 

1. Získáte stav HDFS v clusteru HDInsight spuštěním následujících příkazů:

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

3. Pokud zjistíte, že neexistují žádné chybí, poškozený, nebo under-replikované bloky, nebo že tyto bloky můžete ignorovat, spusťte následující příkaz provést název uzlu mimo nouzový režim:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Jak opravit JDBC nebo SQLLine připojením k problémům s Apache Phoenix?

### <a name="resolution-steps"></a>Postup řešení

Pro připojení k Phoenix, je nutné zadat IP adresu aktivní uzel ZooKeeper. Ujistěte se, že služba ZooKeeper sqlline.py, které se pokouší o připojení, není spuštěná.
1. Přihlaste se ke clusteru HDInsight pomocí protokolu SSH.
2. Zadejte následující příkaz:
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > IP adresa aktivního uzlu ZooKeeper můžete získat z uživatelského rozhraní Ambari. Přejděte na **HBase** > **rychlé odkazy** > **ZK\* (aktivní)** > **Zookeeper informace**. 

3. Pokud sqlline.py připojí k Phoenix a nemá vypršení časového limitu, spusťte následující příkaz k ověření dostupnosti a stav Phoenix:

   ```apache
           !tables
           !quit
   ```      
4. Pokud tento příkaz lze použít, neexistuje žádný problém. IP adresa zadaná uživatelem je pravděpodobně nesprávná. Ale pokud příkaz pozastaví po delší dobu a potom zobrazí chybová zpráva, přejděte ke kroku 5.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Spusťte následující příkazy z hlavního uzlu (hn0), který se při diagnostice stav Phoenix systému. Tabulka katalogu:

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   Příkaz by měla vrátit chybu podobný následujícímu: 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. V uživatelském rozhraní Ambari proveďte následující kroky a restartujte službu HMaster na všechny uzly ZooKeeper:

    1. V **Souhrn** části HBase, přejděte na **HBase** > **Active HBase hlavní**. 
    2. V **součásti** část, restartujte službu hlavní HBase.
    3. Tento postup opakujte pro všechny zbývající **pohotovostní HBase hlavní** služby. 

To může trvat až pět minut, než služba HBase Master stabilizovat a dokončit proces obnovení. Po několika minutách opakujte příkazy sqlline.py, ujistěte se, že v systému. Tabulka katalogu je zapnutý, a to může být dotazován. 

Pokud v systému. Tabulka katalogu je zpět do normální, problém s připojením k Phoenix by měla být vyřešen automaticky.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Co způsobí, že hlavní server nezdaří spustit?

### <a name="error"></a>Chyba 

Dojde k selhání atomic přejmenování.

### <a name="detailed-description"></a>Podrobný popis

Během procesu spuštění dokončení HMaster mnoho kroků inicializace. Jedná se o přesouvání dat ve složce začátku (TMP) ke složce data. HMaster také zjistí složce předběžné protokolů (WALs), pokud jsou k dispozici žádné servery reagovat oblasti a tak dále. 

Během spouštění HMaster nemá základní `list` příkazu u těchto složek. Pokud kdykoli, uvidí HMaster neočekávaný soubor v žádném z těchto složek, vyvolá výjimku a nespustí.  

### <a name="probable-cause"></a>Pravděpodobná příčina

V protokolech serveru oblast pokuste se identifikovat časovou osu vytvoření souboru a zjistěte, pokud byl proces havárií v době, kdy byl vytvořen soubor. (Obraťte se na podporu HBase vám pomůže to). Tento pomůže nám poskytují robustnější mechanismy, takže se můžete vyhnout stiskne to chyb a ujistěte se proces řádné vypnutí systému.

### <a name="resolution-steps"></a>Postup řešení

Zkontrolujte zásobník volání a určete složku, ke které může být příčinou problému (například se může být složce WALs nebo ve složce tmp). V Průzkumníku cloudu, nebo pomocí příkazů HDFS, poté zkuste najít soubor problém. Obvykle se jedná \*-renamePending.json souboru. ( \*-RenamePending.json soubor je soubor deníku, který se používá k implementaci operaci atomic přejmenování v ovladači WASB. Z důvodu chyby v této implementaci těchto souborů může být zbyly poté, co mimoprocesových atd.) Vynutit odstranění tohoto souboru v Průzkumníku cloudu nebo pomocí příkazů HDFS. 

V některých případech mohou existovat i dočasný soubor s názvem něco podobného jako *$$$. $$$* v tomto umístění. Budete muset použít HDFS `ls` příkaz, který má tento soubor; nelze naleznete v souboru v Průzkumníku cloudu. K odstranění tohoto souboru, použijte příkaz HDFS `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.  

Po spuštění těchto příkazů, HMaster by měl spustit okamžitě. 

### <a name="error"></a>Chyba

Žádná adresa serveru, je uvedena ve *hbase: meta* pro oblast xxx.

### <a name="detailed-description"></a>Podrobný popis

Může se zobrazit zpráva na váš cluster Linux, která označuje, že *hbase: meta* tabulka není online. Spuštění `hbck` může zobrazit zprávu, že "hbase: meta tabulky replicaId 0 nebyl nalezen v libovolné oblasti." Tento problém může být, že HMaster nebylo možné inicializovat po restartování HBase. V protokolech HMaster může zobrazit zpráva: "žádná adresa serveru uvedené v hbase: meta pro oblast hbase: zálohování \<název oblasti\>".  

### <a name="resolution-steps"></a>Postup řešení

1. V prostředí HBase zadejte následující příkazy (skutečné hodnoty pro změnu podle vhodnosti):  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Odstranit *hbase: obor názvů* položku. Tato položka může být ke stejné chybě, která se při hlášené *hbase: obor názvů* tabulka je skenován.

3. Restartujte službu Active HMaster zprovoznit HBase v běžícím stavu, v uživatelském rozhraní Ambari.  

4. V prostředí HBase se zprovoznit všechny tabulky v režimu offline, spusťte následující příkaz:

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>Další čtení

[Nelze zpracovat tabulku HBase](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Chyba

HMaster časového limitu se podobně jako závažné výjimce "java.io.IOException: Timedout 300000ms čekání tabulky obor názvů pro přiřazení."

### <a name="detailed-description"></a>Podrobný popis

Tomuto problému může dojít, pokud máte mnoho tabulek a oblasti, které nebyly byly vyprázdněny, při restartování vaší HMaster služeb. Restartování může selhat a uvidíte uvedená chybová zpráva.  

### <a name="probable-cause"></a>Pravděpodobná příčina

Jde o známý problém se službou HMaster. Spuštění úlohy obecné clusteru může trvat dlouhou dobu. HMaster vypne vzhledem k tomu, že v tabulce oboru názvů není dosud přiřazen. K tomu dochází jenom v situacích, ve kterém velké množství dat, unflushed existuje a není dostatečná vypršení časového limitu pěti minut.
  
### <a name="resolution-steps"></a>Postup řešení

1. V uživatelském rozhraní Ambari, přejděte do **HBase** > **konfigurací**. V souboru vlastní hbase-site.xml přidejte následující nastavení: 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Restartujte službu (HMaster a případně dalších služeb HBase).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Co způsobí selhání restartování na oblast serveru?

### <a name="issue"></a>Problém

Selhání restartování v oblasti serveru může být zabránit následující osvědčené postupy. Doporučujeme, abyste pozastavit aktivity vytížen, pokud máte v úmyslu restartování serverů oblast HBase. Pokud k připojení se servery pro oblast, když probíhá vypnutí i aplikace, bude operace restartování serveru oblast pomalejší o několik minut. Je také vhodné nejprve vyprázdnit všechny tabulky. Odkaz na tom, jak vyprázdnění tabulky, najdete v části [HDInsight HBase: jak ke zlepšení čas restartování clusteru HBase pomocí Vyčištění tabulky](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Pokud spustíte operace restartování serverů oblast HBase z rozhraní Ambari, zobrazí okamžitě servery oblast byl vypnut, že nemáte hned restartovat. 

Zde je, co se děje na pozadí: 

1. Ambari agent odešle žádost o zastavení serveru oblast.
2. Ambari agent čeká 30 sekund pro oblast server korektně vypnout. 
3. Pokud vaše aplikace nadále připojit k serveru oblast, server nebude vypnout okamžitě. Předtím, než dojde k vypnutí vyprší časový limit 30 sekund. 
4. Po 30 sekund, Ambari agent odešle force-kill (`kill -9`) příkaz k serveru oblast. Můžete to vidět v protokolu agenta ambari (v /var nebo protokolu nebo adresář příslušných pracovního uzlu):

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
Z důvodu náhlému vypnutí nemusí být vydání port spojených s procesem, přestože proces serveru oblast je zastavena. Tato situace může vést k AddressBindException během spouštění serveru oblast, jak je znázorněno v následujících protokolech. Můžete to ověřit v oblasti server.log v adresáři /var/log/hbase na pracovních uzlech, které oblasti serveru nepodaří spustit. 

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

1. Pokuste se snížit zatížení serverů oblast HBase před spuštěním restartování. 
2. Můžete taky (Pokud kroku 1 vám nepomohly), pokuste se restartovat servery oblast na pracovních uzlech ručně pomocí následujících příkazů:

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

### <a name="see-also"></a>Viz také
[Řešení potíží pomocí Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
