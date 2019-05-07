---
title: Nainstalujte TmaxSoft OpenFrame na virtuálních počítačích Azure
description: Změna hostitele úloh mainframových IBM z/OS pomocí TmaxSoft OpenFrame prostředí v Azure Virtual Machines (VM).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: c7e6e7e2023c333207a3a17c3b6711d92de7d044
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65187796"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Instalace TmaxSoft OpenFrame v Azure

Zjistěte, jak nastavit prostředí v Azure OpenFrame vhodný pro vývoj ukázky, testování a produkční úlohy. Tento kurz vás provede jednotlivé kroky.

OpenFrame zahrnuje více součástí, které vytvoří prostředí emulace sálové počítače v Azure. Například OpenFrame služeb online services nahradit sálové počítače middlewaru například IBM zákazníka informace ovládacího prvku systému (CICS) a OpenFrame Batch součástí TJES, nahradí sálové počítače IBM úlohy položka Subsystem (JES).

OpenFrame funguje s jakoukoli relační databázi, včetně Oracle Database, Microsoft SQL Server, IBM Db2 a MySQL. Tato instalace OpenFrame používá TmaxSoft Tibero relační databáze. OpenFrame i Tibero spustit v operačním systému Linux. V tomto kurzu se nainstaluje CentOS 7.3, i když můžete použít jiné podporované distribuce Linuxu. OpenFrame aplikační server a databázi Tibero jsou nainstalované na jeden virtuální počítač (VM).

Tento kurz vás provede instalaci součástí OpenFrame suite. Některé nutné nainstalovat samostatně.

OpenFrame hlavní součásti:

- Instalační balíčky.
- Tibero databáze.
- Připojení ODBC (Open Database) používá ke komunikaci s databází Tibero aplikací v OpenFrame.
- Základ OpenFrame, middleware, který spravuje celý systém.
- OpenFrame Batch, řešení, které nahradí sálových počítačích služby batch.
- TACF, modul služby, které řídí přístup uživatelů k systémům a prostředkům.
- ProSort, nástroj řazení pro dávkové transakce.
- OFCOBOL, kompilátor, který interpretuje sálovými COBOL programy.
- OFASM, kompilátor, který interpretuje sálovými assembler programy.
- OpenFrame serveru typ jazyka C (OSC), řešení, které nahrazuje sálové počítače middlewaru a IBM CICS.
- Java Enterprise uživatele řešení (JEUS), serveru webových aplikací, který má certifikaci pro Java Enterprise Edition 6.
- OFGW, součásti OpenFrame brány, která poskytuje 3270 naslouchací proces.
- OFManager, řešení, které poskytuje OpenFrame pro operace a Správa funkce v prostředí webu.

Další požadované součásti OpenFrame:

- OSI, řešení, které nahrazuje sálové počítače middlewaru a IMS řadiče domény.
- TJES, řešení, která poskytuje sálových JES prostředí.
- OFTSAM, řešení, která umožňuje soubory SAM (V) k použití v rámci otevřít systému.
- OFHiDB, řešení, které nahradí sálovými vaší databáze IMS.
- OFPLI, kompilátor, který sálovými interpretuje uživatele PL / mi programy.
- PROTRIEVE, řešení, které spustí jazyk mainframových Easytrieve certifikační Autority.
- OFMiner, řešení, které analyzuje prostředky sálové počítače a přenese je do Azure.

## <a name="architecture"></a>Architektura

Následující obrázek poskytuje přehled o OpenFrame 7.0 architekturálních komponentách, které jsou nainstalované v tomto kurzu:

![OpenFrame komponenty](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Požadavky na systém Azure

V následující tabulce jsou uvedeny požadavky pro instalaci na Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Požadavek</th><th>Popis</th></tr>
</thead>
<tbody>
<tr><td>Podporované distribuce systému Linux v Azure
</td>
<td>
Linux x86 2.6 (32 bitů, 64 bitů)<br/>
Red Hat 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>Hardware
</td>
<td>Počet jader: 2 (minimálně)<br/>
Paměť: 4 GB (minimálně)<br/>
Záměna prostoru: 1 GB (minimálně)<br/>
Pevný disk: 100 GB (minimálně)<br/>
</td>
</tr>
<tr><td>Volitelný software pro uživatele Windows
</td>
<td>PuTTY: V tomto průvodci používá ke konfiguraci funkce virtuálních počítačů<br/>
WinSCP: Oblíbené SFTP klienta a klient FTP můžete použít<br/>
Eclipse pro Windows: Vývojová platforma nepodporuje TmaxSoft<br/>
(V současnosti není podporované sady Microsoft Visual Studio)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Požadavky

Naplánujte výdaje za několik dnů a Poskládejte si veškerý požadovaný software a dokončete všechny ručně prováděné procesy.

Než začnete, postupujte takto:

- Získání instalačního média OpenFrame z TmaxSoft. Pokud jste stávající zákazník TmaxSoft, obraťte se na zástupce TmaxSoft licencovanou kopii. V opačném případě požádat o zkušební verzi z [TmaxSoft](https://www.tmaxsoft.com/contact/).

- Požádat o dokumentaci OpenFrame zasláním e-mailu <support@tmaxsoft.com>.

- Získejte předplatné Azure, pokud ho ještě nemáte. Můžete také vytvořit [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) předtím, než začnete.

- Volitelné. Nastavte tunel VPN typu site-to-site nebo jumpbox, který omezuje přístup k virtuálnímu počítači Azure pro povolené uživatele ve vaší organizaci. Tento krok není povinný, ale je osvědčeným postupem.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Nastavit na Virtuálním počítači Azure pro OpenFrame a Tibero

Můžete nastavit OpenFrame prostředí pomocí různých vzorů nasazení, ale následující postup ukazuje, jak nasadit OpenFrame aplikační server a databáze Tibero na jeden virtuální počítač. Ve větších prostředí a pro úlohy pořádnou osvědčeným postupem je nasadit databázi samostatně na vlastním virtuálním počítači pro zajištění lepšího výkonu.

**Vytvoření virtuálního počítače**

1. Přejděte na webu Azure portal na <http://portal.azure.com> a přihlaste se ke svému účtu.

2. Klikněte na tlačítko **virtuálních počítačů**.

    ![Seznam prostředků na webu Azure portal](media/vm-01.png)

3. Klikněte na tlačítko **Add** (Přidat).

    ![Přidat možnost na webu Azure portal](media/vm-02.png)

4. Napravo od **operačních systémů**, klikněte na tlačítko **Další**.

     ![Další možnost na webu Azure portal](media/vm-03.png)

5. Klikněte na tlačítko **založené na CentOS 7.3** postupovat podle tohoto návodu přesně, nebo můžete zvolit jiné podporované distribuce Linuxu.

     ![Možnosti operačního systému na webu Azure portal](media/vm-04.png)

6. V **Základy** nastavení, zadejte **název**, **uživatelské jméno**, **typ ověřování**, **předplatné** (Průběžné platby je styl AWS platby), a **skupiny prostředků** (použijte již existující nebo vytvořte skupinu TmaxSoft).

7. Po dokončení (včetně pár veřejného a privátního klíče pro **typ ověřování**), klikněte na tlačítko **odeslat**.

> [!NOTE]
> Pokud používáte veřejný klíč SSH pro **typ ověřování**, najdete v článku kroky v další části ke generování dvojice veřejného/soukromého klíče a poté pokračovat zde uvedené kroky.

### <a name="generate-a-publicprivate-key-pair"></a>Vygenerovat pár veřejného a privátního klíče

Pokud používáte operační systém Windows, potřebujete nástroje PuTTYgen ke generování dvojice veřejného/soukromého klíče.

Veřejný klíč lze volně sdílet, ale privátní klíč by měl být udržen v tajnosti zcela a nikdy nebude sdílet s jinou stranou. Po vygenerování klíče, je nutné vkládat **veřejný klíč SSH** do konfigurace – v důsledku toho pak ho nahrát do virtuálního počítače s Linuxem. Uloží se uvnitř oprávnění\_klíče v rámci \~/.ssh directory domovský adresář uživatelského účtu. Virtuálního počítače s Linuxem je pak možné rozpoznat a ověření připojení po zadání přidružený **privátní klíč SSH** v klientovi SSH (v našem případě PuTTY).

Při poskytování přístupu k virtuálnímu počítači nové jednotlivce: 

- Nové jednotlivých vygeneruje vlastní veřejného a privátního klíče pomocí nástroje PuTTYgen.
- Jednotlivci ukládat vlastní privátní klíče samostatně a odeslat informace o veřejném klíči do Správce virtuálního počítače.
- Vloží obsah veřejného klíče správce \~/.ssh/authorized\_souboru klíče.
- Nového uživatele se připojí pomocí PuTTY.

**Ke generování dvojice veřejného/soukromého klíče**

1.  Stáhněte si nástroje PuTTYgen z <https://www.putty.org/> a nainstalujte ho pomocí výchozích nastavení.

2.  Otevřete PuTTYgen, vyhledejte PuTTY instalačním adresáři C:\\Program Files\\PuTTY.

    ![Rozhraní puTTY](media/puttygen-01.png)

3.  Klikněte na tlačítko **generovat**.

    ![Dialogové okno puTTY Key Generator](media/puttygen-02.png)

4.  Po vygenerování uložte veřejný klíč a privátní klíč. Vložte obsah veřejného klíče v **veřejný klíč SSH** část **vytvořit virtuální počítač \> Základy** podokně (viz kroky 6 a 7 v předchozí části).

    ![Dialogové okno puTTY Key Generator](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Konfigurace funkce virtuálních počítačů

1. Na webu Azure portal v **zvolte velikost** okně vyberte nastavení hardwaru počítače Linux chcete. *Minimální* požadavky pro instalaci Tibero a OpenFrame jsou 2 procesory a 4 GB paměti RAM, jak je uvedeno v tomto příkladu instalaci:

    ![Vytvoření virtuálního počítače – základy](media/create-vm-01.png)

2. Klikněte na tlačítko **3 nastavení** a použít výchozí nastavení nakonfigurovat volitelné funkce.
3. Zkontrolujte své platební údaje.

    ![Vytvoření virtuálního počítače – nákupu](media/create-vm-02.png)

4. Odešlete svůj výběr. Azure zahájí nasazení virtuálního počítače. Tento proces obvykle trvá několik minut.

5. Při nasazení virtuálního počítače, zobrazí se jeho řídicí panel zobrazuje všechna nastavení, které byly vybrány během konfigurace. Poznamenejte si, **veřejnou IP adresu**.

    ![Tmax na řídicí panel Azure](media/create-vm-03.png)

6. Otevřete PuTTY.

7. Pro **název hostitele**, zadejte své uživatelské jméno a jeho veřejná IP adresa je zkopírována. Například **uživatelské jméno\@veřejné IP adresy**.

    ![Dialogové okno Konfigurace puTTY](media/putty-01.png)

8. V **kategorie** klikněte **připojení \> SSH \> Auth**. Zadejte cestu k vaší **privátní klíč** souboru.

    ![Dialogové okno Konfigurace puTTY](media/putty-02.png)

9. Klikněte na tlačítko **otevřít** spusťte okno PuTTY. V případě úspěchu, jste připojení k novému virtuálnímu počítači CentOS běžící v Azure.

10. Chcete-li přihlásit jako uživatel root, zadejte **sudo bash**.

    ![Přihlášení uživatele root v příkazovém okně](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Nastavení prostředí a balíčků

Teď, když se vytvoří virtuální počítač a jste přihlášení, musíte několika kroky instalace a nainstalujte požadované balíčky předinstalační.

1. Namapování **ofdemo** na místní IP adresu pomocí editoru vi upravit soubor hostitelů (`vi /etc/hosts`). Za předpokladu, že IP je 192.168.96.148 ofdemo, toto je před provedením změny:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Toto je po provedení změny:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Vytváření skupin a uživatelů:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Změna hesla pro uživatele oframe7:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Aktualizujte parametry jádra v /etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Obnovit parametry jádra dynamicky bez restartování:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Získáte požadované balíčky: Ujistěte se, že je server připojen k Internetu, stažení následující balíčky a pak je nainstalujte:

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - ncurses

          > [!NOTE]
          > Po instalaci balíčku ncurses, vytvořte následující symbolické odkazy:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - strace
     - ltrace
     - gdb

7. V případě instalace ot. / min Java postupujte takto:

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>Instalace databáze Tibero

Tibero obsahuje několik klíčových funkcí v prostředí OpenFrame v Azure:

- Tibero slouží jako interní datové úložiště OpenFrame pro různé funkce systému.
- JAKO soubory, včetně KSDS RRDS a ESDS, databázi Tibero používají interně pro datové úložiště.
- Úložiště dat TACF se ukládají do Tibero.
- Informace katalogu OpenFrame jsou uloženy v Tibero.
- Databáze Tibero může sloužit jako náhradu za IBM Db2 k ukládání dat aplikací.

**Chcete-li nainstalovat Tibero**

1. Ověřte, zda je k dispozici Tibero binární instalační soubor a zkontrolujte číslo verze.
2. Zkopírujte Tibero software s uživatelským účtem Tibero (oframe). Příklad:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Otevřete .bash\_profilu v vi (`vi .bash_profile`) a vložte následující:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Ke spuštění profilu bash, zadejte na příkazovém řádku:

    ```
    source .bash_profile
    ```

5. Generovat soubor tip (konfigurační soubor pro Tibero) a potom ho otevřete v editoru vi. Příklad:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Upravit \$TB\_HOME/client/config/tbdsn.tbr a místo toho umístit 127.0.0.1 oflocalhost, jak je znázorněno:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Vytvoření databáze. Zobrazí se následující výstup:

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. Recyklace Tibero, nejdřív vypněte ho pomocí `tbdown` příkazu. Příklad:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Nyní spusťte pomocí Tibero `tbboot`. Příklad:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Pokud chcete vytvořit tabulkový prostor, přístup k databázi pomocí SYS uživatele (sys tmax), pak vytvořte nezbytné tabulkový prostor pro výchozí svazek a TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Teď zadejte následující příkazy SQL:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Spuštění Tibero a ověřte, že Tibero procesy, které jsou spuštěny:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Výstup:

![Tibero výstupu](media/tibero-01.png)

## <a name="install-odbc"></a>Nainstalujte ovladač ODBC

Aplikace v OpenFrame komunikaci s databází Tibero pomocí rozhraní ODBC API poskytované unixODBC open source projektu.

Instalace ODBC:

1. Ověřte, že soubor Instalační služby systému unixODBC 2.3.4.tar.gz je k dispozici, nebo použijte `wget unixODBC-2.3.4.tar.gz` příkazu. Příklad:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Rozbalte binární soubor. Příklad:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Přejděte do adresáře unixODBC 2.3.4 a generovat soubor pravidel pomocí kontrola, zda informace o počítači. Příklad:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Ve výchozím nastavení, unixODBC nainstalovaný v/usr/local, takže `--prefix` předá hodnotu, chcete-li změnit umístění. Podobně, konfigurační soubory jsou nainstalovány v/etc ve výchozím nastavení, takže `--sysconfdir` předá hodnotu do požadovaného umístění.

4. Spusťte soubor pravidel: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Zkopírujte spustitelný soubor v adresáři program za kompilace. Příklad:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Úprava profilu bash pomocí editoru vi (`vi ~/.bash_profile`) a přidejte následující:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Použití rozhraní ODBC. Následující soubory odpovídajícím způsobem upravte. Příklad:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. Vytvořte symbolický odkaz a ověřit připojení k databázi Tibero:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

Zobrazí se následující výstup:

![ODBC výstup zobrazuje připojení k SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>Instalační základna OpenFrame

Základní aplikační server je nainstalována před jednotlivých služeb, které OpenFrame používá ke správě systému v Azure, včetně transakce zpracování serverové procesy.

**Chcete-li nainstalovat OpenFrame Base**

1. Ujistěte se, že Tibero instalace proběhla úspěšně. pak ověřte, že následující OpenFrame\_Base7\_0\_Linux\_x86\_64. bin instalační soubor a konfigurační soubor base.properties jsou k dispozici.

2. Aktualizace profilu bash s následujícími informacemi Tibero konkrétní:

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. Spusťte prostředí bash profilu:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Ujistěte se, že jsou spuštěny Tibero procesy. Příklad:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Základní](media/base-01.png)

     > [!IMPORTANT]
     > Ujistěte se, že začnete Tibero před instalací.

5. Generovat licence na [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) a základní OpenFrame, Batch, TACF řečeno OSC licence v příslušné složce:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Stáhněte si soubory binární soubor a base.properties OpenFrame Base:

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. Spusťte instalační program s použitím souboru base.properties. Příklad:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Po dokončení instalace dokončena zpráva je seznamech.

8. Ověřit pomocí struktury adresáře OpenFrame Base `ls -ltr` příkazu. Příklad:

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. Spusťte OpenFrame Base:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![výstup příkazu tmboot](media/base-02.png)

10. Ověřte, zda že je připravena pomocí příkazu tmadmin v si stav procesu. RDY se zobrazí v **stav** sloupec pro každý z procesů:

     ![výstup příkazu tmadmin](media/base-03.png)

11. Vypněte OpenFrame Base:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>Nainstalujte OpenFrame Batch

OpenFrame Batch se skládá z několika součástí, které simulují mainframových prostředí služby batch a se používá ke spouštění dávkových úloh Hive v Azure.

**Chcete-li nainstalovat služby Batch**

1. Ujistěte se, že základní instalace proběhla úspěšně. pak ověřte, že OpenFrame\_Batch7\_0\_Fix2\_MVS\_Linux\_x86\_64. bin instalační soubor a konfigurační soubor batch.Properties jsou k dispozici:

2. Na příkazovém řádku zadejte `vi batch.properties` úpravy souboru batch.properties pomocí editoru vi.

3. Upravte parametry následujícím způsobem:

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. Chcete-li spustit instalační program služby batch, zadejte na příkazovém řádku:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Po dokončení instalace spusťte nainstalovaný sad OpenFrame zadáním `tmboot` příkazového řádku.

    ![tmboot výstupu](media/tmboot-01.png)

6. Typ `tmadmin` příkazového řádku ke kontrole OpenFrame procesu.

    ![Správce Tmax obrazovky](media/tmadmin-01.png)

7. Spusťte následující příkazy:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Použití `tmdown` příkaz ke spuštění a vypnutí služby Batch:

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>Nainstalujte TACF

Správce TACF je modul služby OpenFrame, která řídí přístup uživatelů k systémům a prostředkům prostřednictvím RACF zabezpečení.

**Chcete-li nainstalovat TACF**

1. Ověřte, že OpenFrame\_Tacf7\_0\_Fix2\_Linux\_x86\_64. bin instalační soubor a konfigurační soubor tacf.properties jsou k dispozici.
2. Ujistěte se, že Batch instalace byla úspěšně dokončena a pak otevřete soubor tacf.properties pomocí editoru vi (`vi tacf.properties`).
3. Upravte parametry TACF:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. Po dokončení instalačního programu TACF použijte proměnné prostředí TACF. Na příkazovém řádku zadejte:

     ```
     source \~/.bash\_profile
     ```

5. Spusťte instalační program TACF. Na příkazovém řádku zadejte:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     Výstup bude vypadat přibližně takto:

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. Na příkazovém řádku zadejte `tmboot` OpenFrame restartovat. Výstup bude vypadat přibližně takto:

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. Ověřte, že stav procesu připravený pomocí `tmadmin` v `si` příkazu. Příklad:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     V **stav** se zobrazí RDY sloupci:

    ![RDY ve sloupci Stav](media/tmboot-02.png)

8. Spusťte následující příkazy:

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. Vypnutí serveru pomocí `tmdown` příkazu. Výstup bude vypadat přibližně takto:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>Instalace ProSort

ProSort je nástroj sloužící k řazení dat v dávkové transakce.

**Chcete-li nainstalovat ProSort**

1. Ujistěte se, že Batch instalace byla úspěšná a potom ověřte, že **prosort bin prosort\_2sp3 linux64. 2123 opt.tar.gz** soubor instalačního programu je k dispozici.

2. Spusťte instalační program s použitím vlastnosti souboru. Na příkazovém řádku zadejte:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Přesuňte prosort adresář domovské umístění. Na příkazovém řádku zadejte:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Vytvořte podadresář s licencí a zkopírujte existuje soubor s licencí. Příklad:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Otevřít v editoru vi bash.profile (`vi .bash_profile`) a aktualizujte ji následujícím způsobem:

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. Ke spuštění profilu bash, na příkazovém řádku zadejte: `. .bash_profile`

7. Vytvořte konfigurační soubor. Příklad:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Vytvořte symbolický odkaz. Příklad:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Ověření ProSort instalace spuštěním `prosort -h` příkazu. Příklad:

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>Nainstalujte OFCOBOL

OFCOBOL je OpenFrame Kompilátor interpretuje sálovými COBOL programy. 

**Chcete-li nainstalovat OFCOBOL**

1. Ujistěte se, že bylo úspěšné instalace služby Batch a Online a potom ověřte, že OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64. bin instalační soubor je k dispozici.

2. Ke spuštění instalačního programu OFCOBOL, na příkazovém řádku zadejte:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Přečtěte si licenční smlouvu a pokračujte stisknutím klávesy Enter.

4. Přijměte podmínky licenční smlouvy. Po dokončení instalace se zobrazí následující položky:

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Otevřít v editoru vi profilu bash (`vi .bash_profile`) a ověřit, která se aktualizuje s použitím OFCOBOL proměnných.
6. Spuštění profilu bash. Na příkazovém řádku zadejte:

     ```
      source ~/.bash_profile
     ```

7. Zkopírujte OFCOBOL licenci ke složce nainstalovaný. Příklad:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Přejděte na konfigurační soubor tjclrun.conf OpenFrame a otevřete v editoru vi. Příklad:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Tady je oddíl SYSLIB před provedením změny:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Tady je oddíl SYSLIB po provedení změny:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Zkontrolujte OpenFrame\_COBOL\_InstallLog.log souboru v editoru vi a ověřte, že neexistují žádné chyby. Příklad:
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. Použití `ofcob --version` příkaz a zkontrolujte číslo verze na ověření instalace. Příklad:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Restartovat pomocí OpenFrame `tmdown/tmboot` příkazu.

## <a name="install-ofasm"></a>Nainstalujte OFASM

OFASM je OpenFrame Kompilátor interpretuje sálovými assembler programy.

**Chcete-li nainstalovat OFASM**

1. Ujistěte se, že bylo úspěšné instalace služby Batch a Online a potom ověřte, že OpenFrame\_ASM3\_0\_Linux\_x86\_64. bin instalační soubor je k dispozici.

2. Spusťte instalační program. Příklad:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Přečtěte si licenční smlouvu a pokračujte stisknutím klávesy Enter.
4. Přijměte podmínky licenční smlouvy.
5. Ověřte, že dojde k aktualizaci profilu bash s OFASM proměnné. Příklad:

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Otevřete konfigurační soubor tjclrun.conf OpenFrame v vi a upravit následujícím způsobem:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Tady je v části [SYSLIB] *před* změny:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Tady je v části [SYSLIB] *po* změny:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Otevřete OpenFrame\_ASM\_InstallLog.log souboru v editoru vi a ověřte, že neexistují žádné chyby. Příklad:

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. Restartujte OpenFrame pomocí jedné z následujících příkazů:

     ```
     tmdown / tmboot
     ```

     – nebo –

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Nainstalujte OSC

OSC je podobný CICS IBM, který podporuje vysokorychlostní transakce OLTP a další funkce pro správu OpenFrame prostředí.

**Chcete-li nainstalovat OSC**

1. Ujistěte se, že základní instalace proběhla úspěšně. pak ověřte, že OpenFrame\_OSC7\_0\_Fix2\_Linux\_x86\_64. bin instalační soubor a osc.properties konfiguračního souboru k dispozici.
2. Upravte tyto parametry v souboru osc.properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Spusťte instalační program s použitím vlastnosti souboru, jak je znázorněno:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Až budete hotovi, zobrazí se zpráva "Instalace dokončena".

4. Ověřte, že s proměnnými OSC dojde k aktualizaci profilu bash.
5. Zkontrolujte OpenFrame\_OSC7\_0\_Fix2\_InstallLog.log souboru. Výsledek by měl vypadat přibližně takto:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Otevřete konfigurační soubor ofsys.seq pomocí editoru vi. Příklad:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. V \#základní a \#BATCH oddíly, upravit parametry, jak je znázorněno.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. Zkopírujte soubor s licencí. Příklad:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Ke spuštění a vypnutí OSC, inicializovat oblasti sdílené paměti CICS zadáním `osctdlinit OSCOIVP1` příkazového řádku.

10. Spustit `oscboot` při spouštění OSC. Výstup bude vypadat přibližně takto:

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. Chcete-li ověřit, že stav procesu připravený, použijte `tmadmin` v incidentech příkaz. Všechny procesy zobrazeno RDY v **stav** sloupce.

    ![Zobrazení RDY procesy](media/tmadmin-02.png)

12. Vypnout OSC pomocí `oscdown` příkazu.

## <a name="install-jeus"></a>Nainstalujte JEUS

JEUS (Java Enterprise uživatele řešení) poskytuje prezentační vrstvy serveru OpenFrame webových aplikací.

Před instalací JEUS, nainstalujte si balíček nástrojů Apache Ant, který poskytuje knihovny a nástroje příkazového řádku, které jsou potřebné k instalaci JEUS.

**Chcete-li nainstalovat Apache Ant**

1. Stažení nástrojů Ant binární pomocí `wget` příkazu. Příklad:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Použití `tar` nástroj extrahovat binárního souboru a přesunout ho na příslušné místo. Příklad:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Z důvodu efektivity vytvořte symbolický odkaz:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Otevřít v editoru vi profilu bash (`vi .bash_profile`) a aktualizujte pomocí následující proměnné:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Použití proměnné upravené prostředí. Příklad:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Chcete-li nainstalovat JEUS**

1. Rozbalte instalační program s použitím `tar` nástroj. Příklad:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Vytvoření **jeus** složky (`mkdir jeus7`) a rozbalte ho do binárního souboru.
3. Přejděte **nastavení** adresáře (nebo použijte parametr JEUS pro vlastní prostředí). Příklad:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Spustit `ant clean-all` než se pustíte do sestavení. Výstup bude vypadat přibližně takto:

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  Vytvořte záložní kopii souboru domény config-template.properties. Příklad:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Otevřete soubor domain-config-template.properties v vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Změna `jeus.password=jeusadmin nodename=Tmaxsoft` do `jeus.password=tmax1234 nodename=ofdemo`

8. Spustit `ant install` příkazu sestavte JEUS.
9.  Aktualizovat .bash\_soubor profilu s použitím JEUS proměnných, jak je znázorněno:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Spuštění profilu bash. Příklad:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Volitelné*. Vytvořte alias pro snadné vypnutí a spouštění součástí JEUS:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Ověření instalace, spusťte serveru pro správu domény, jak je znázorněno:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Ověřte webové přihlášení pomocí syntaxe:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Například <http://192.168.92.133:9736/webadmin/login.> přihlašovací obrazovky:
    
     ![JEUS WebAdmin přihlašovací obrazovka](media/jeus-01.png)

     > [!NOTE]
     > Pokud zaznamenáte problémy se zabezpečením na portu, otevřete port 9736 nebo zakázat bránu firewall (`systemctl stop firewall`).

14. Chcete-li změnit název hostitele pro server1, klikněte na tlačítko **Zamknout & Upravit**, pak klikněte na tlačítko **server1**. V okně serveru změňte název hostitele následujícím způsobem:

    1.  Změna **Nodename** k **ofdemo**.
    2.  Klikněte na tlačítko **OK** na pravé straně okna.
    3.  Klikněte na tlačítko **použít změny** v levé dolní části okna a popis, zadejte *změnit název hostitele*.

    ![JEUS WebAdmin obrazovky](media/jeus-02.png)

15. Ověřte, že konfigurace byla úspěšně dokončena na potvrzovací obrazovce.

    ![jeus_domain Server screen](media/jeus-03.png)

16. Spusťte proces spravovaného serveru "server1" pomocí následujícího příkazu:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Nainstalujte OFGW

OFGW je OpenFrame brány, která podporuje komunikaci mezi emulátoru terminálu 3270 a základní OSI a spravuje relace mezi emulátoru terminálu a OSI.

**Chcete-li nainstalovat OFGW**

1. Ujistěte se, že byl úspěšně nainstalován JEUS a potom ověřte, že OFGW7\_0\_1\_Generic.bin instalační soubor je k dispozici.
2. Spusťte instalační program. Příklad:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Pomocí následujících umístění na odpovídající výzvy:
     -   JEUS domovský adresář
     -   JEUS Domain Name
     -   Název serveru JEUS
     -   Ovladač Tibero
     -   ID uzlu Tmax ofdemo

4. Přijměte ostatní výchozí hodnoty a pak stisknutím klávesy Enter ukončete instalační program.

5. Ověřte, že adresa URL pro OFGW funguje podle očekávání:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Zobrazí se následující obrazovka:

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Nainstalujte OFManager

OFManager poskytuje operaci a funkce pro správu pro OpenFrame ve webovém prostředí.

**Chcete-li nainstalovat OFManager**

1. Ověřte, že OFManager7\_Generic.bin instalační soubor je k dispozici.
2. Spusťte instalační program. Příklad:

     ```
     OFManager7_Generic.bin
     ```

3.  Stisknutím klávesy Enter pokračovat a přijměte licenční smlouvu.
4.  Zvolte složku instalace.
5.  Přijměte výchozí hodnoty.
6.  Zvolte Tibero jako databáze.
7.  Stisknutím klávesy Enter ukončete instalační program.
8.  Ověřte, že adresa URL pro OFManager funguje podle očekávání:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Zobrazí se na úvodní obrazovce:

![Správce OpenFrame Tmax přihlašovací obrazovka](media/ofmanager-01.png)

Tím končí instalace součásti OpenFrame.

## <a name="next-steps"></a>Další postup

Pokud zvažujete migrace mainframů, je k dispozici při náš rostoucí partnerský ekosystém. Podrobné informace o výběru partnerského řešení, najdete [platformy modernizaci Alliance](https://www.platformmodernization.org/pages/mainframe.aspx).

-   [Začínáme s Azure](https://docs.microsoft.com/azure/)
-   [Host Integration Server (HIS) dokumentace](https://docs.microsoft.com/host-integration-server/)
-   [Průvodce Lift and Shift Azure virtuální datové centrum](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
