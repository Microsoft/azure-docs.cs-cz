---
title: Instalace TmaxSoft OpenFrame na virtuálních počítačích Azure
description: Znovu hostujte úlohy sálových počítačů IBM z/OS pomocí prostředí TmaxSoft OpenFrame na virtuálních počítačích Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 1ad6e52c421d9cfec4640d3a330b5507d6ed3e9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72436045"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Instalace TmaxSoft OpenFrame do Azure

Zjistěte, jak nastavit prostředí OpenFrame v Azure vhodné pro vývoj, ukázky, testování nebo produkční úlohy. Tento kurz vás provede každým krokem.

OpenFrame obsahuje více součástí, které vytvářejí prostředí emulace sálových počítačů v Azure. Online služby OpenFrame například nahrazují middleware sálových počítačů, jako je IBM Customer Information Control System (CICS) a OpenFrame Batch, svou komponentou TJES, nahrazuje subsystém Dílčí hodu úlohy (JES) sálového počítače IBM.

OpenFrame pracuje s jakoukoli relační databází, včetně Databáze Oracle, Microsoft SQL Serveru, IBM Db2 a MySQL. Tato instalace OpenFrame využívá relační databázi TmaxSoft Tibero. Oba OpenFrame a Tibero běží na operačním systému Linux. Tento výukový program nainstaluje CentOS 7.3, i když můžete použít jiné podporované distribuce Linuxu. Aplikační server OpenFrame a databáze Tibero jsou nainstalované na jednom virtuálním počítači (VM).

Kurz vás provede instalací součástí sady OpenFrame. Některé musí být nainstalovány samostatně.

Součásti Hlavní ho disponovatek OpenFrame:

- Požadované instalační balíčky.
- Databáze Tibero.
- Open Database Connectivity (ODBC) používají aplikace v OpenFrame ke komunikaci s databází Tibero.
- OpenFrame Base, middleware, který spravuje celý systém.
- OpenFrame Batch, řešení, které nahrazuje dávkové systémy sálového počítače.
- TACF, servisní modul, který řídí přístup uživatelů k systémům a prostředkům.
- ProSort, nástroj pro řazení pro dávkové transakce.
- OFCOBOL, kompilátor, který interpretuje mainframové programy COBOL.
- OFASM, kompilátor, který interpretuje mainframe assembler programy.
- OpenFrame Server typu C (OSC), řešení, které nahrazuje middleware sálového počítače a IBM CICS.
- Java Enterprise User Solution (JEUS), webový aplikační server, který je certifikován pro Java Enterprise Edition 6.
- OFGW, komponenta brány OpenFrame, která poskytuje naslouchací proces 3270.
- OFManager, řešení, které poskytuje funkci provozu a správy OpenFrame ve webovém prostředí.

Další požadované komponenty OpenFrame:

- OSI, řešení, které nahrazuje middleware sálových počítačů a IMS DC.
- TJES, řešení, které poskytuje prostředí JES sálového počítače.
- OFTSAM, řešení, které umožňuje (V)SAM soubory, které mají být použity v otevřeném systému.
- OFHiDB, řešení, které nahrazuje ims DB sálového počítače.
- OFPLI, kompilátor, který interpretuje mainframe pl/ i programy.
- PROTRIEVE, řešení, které provádí sálový jazyk CA-Easytrieve.
- OFMiner, řešení, které analyzuje prostředky sálových počítačů a pak je migruje do Azure.

## <a name="architecture"></a>Architektura

Následující obrázek obsahuje přehled architektonických komponent OpenFrame 7.0 nainstalovaných v tomto kurzu:

![Komponenty OpenFrame](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Požadavky na systém Azure

V následující tabulce jsou uvedeny požadavky na instalaci v Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Požadavek</th><th>Popis</th></tr>
</thead>
<tbody>
<tr><td>Podporované distribuce Linuxu v Azure
</td>
<td>
Linux x86 2.6 (32bitový, 64bitový)<br/>
Červený klobouk 7.x<br/>
CentOS 7,x<br/>
</td>
</tr>
<tr><td>Hardware
</td>
<td>Jádra: 2 (minimálně)<br/>
Paměť: 4 GB (minimálně)<br/>
Odkládací prostor: 1 GB (minimálně)<br/>
Pevný disk: 100 GB (minimálně)<br/>
</td>
</tr>
<tr><td>Volitelný software pro uživatele Windows
</td>
<td>PuTTY: Používá se v této příručce ke konfiguraci funkcí virtuálního počítače<br/>
WinSCP: Populární SFTP klient a FTP klient, který můžete použít<br/>
Eclipse pro Windows: Vývojová platforma podporovaná TmaxSoft<br/>
(Microsoft Visual Studio není v tuto chvíli podporována)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Požadavky

Naplánujte si několik dní na sestavení veškerého potřebného softwaru a dokončení všech manuálních procesů.

Než začnete, postupujte takto:

- Získejte instalační médium OpenFrame od společnosti TmaxSoft. Pokud jste stávajícím zákazníkem TmaxSoft, obraťte se na svého zástupce tmaxSoft pro licencovanou kopii. V opačném případě požádejte o zkušební verzi od [společnosti TmaxSoft](https://www.tmaxsoft.com/contact/).

- Vyžádejte si dokumentaci OpenFrame odesláním e-mailu na adresu <support@tmaxsoft.com>.

- Získejte předplatné Azure, pokud ho ještě nemáte. Můžete si také vytvořit [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

- Nepovinný parametr. Nastavte tunel VPN mezi lokalitami nebo jumpbox, který omezuje přístup k virtuálnímu počítači Azure povoleným uživatelům ve vaší organizaci. Tento krok není vyžadován, ale je to osvědčený postup.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Nastavení virtuálního počítače v Azure pro OpenFrame a Tibero

Prostředí OpenFrame můžete nastavit pomocí různých vzorů nasazení, ale následující postup ukazuje, jak nasadit aplikační server OpenFrame a databázi Tibero na jednom virtuálním počítači. Ve větších prostředích a pro značné úlohy je osvědčeným postupem nasadit databázi samostatně na vlastní virtuální počítač pro lepší výkon.

**Vytvoření virtuálního virtuálního mísa**

1. Přejděte na portál <https://portal.azure.com> Azure a přihlaste se ke svému účtu.

2. Klikněte na **Virtuální počítače**.

    ![Seznam zdrojů na webu Azure Portal](media/vm-01.png)

3. Klikněte na **Přidat**.

    ![Přidat možnost na webu Azure Portal](media/vm-02.png)

4. Vpravo od **položky Operační systémy**klepněte na tlačítko **Další**.

     ![Další možnost na Azure Portal](media/vm-03.png)

5. Klikněte na **CentOS-založené 7.3** sledovat tento walk-through přesně, nebo si můžete vybrat jinou podporovanou distribuci Linuxu.

     ![Možnosti operačního systému na webu Azure Portal](media/vm-04.png)

6. V nastavení Základy zadejte **jméno**, **Uživatelské jméno**, **Typ ověřování**, **Předplatné** (Průběžně zdatný platba je způsob **platby** AWS) a **skupinu Prostředků** (použijte existující jméno nebo vytvořte skupinu TmaxSoft).

7. Po dokončení (včetně dvojice veřejného a soukromého klíče pro **typ ověřování**) klepněte na **tlačítko Odeslat**.

> [!NOTE]
> Pokud používáte veřejný klíč SSH pro **typ ověřování**, podívejte se na kroky v další části pro generování dvojice veřejného a soukromého klíče a pokračujte kroky zde.

### <a name="generate-a-publicprivate-key-pair"></a>Generovat dvojici veřejného a soukromého klíče

Pokud používáte operační systém Windows, potřebujete PuTTYgen generovat pár veřejného a soukromého klíče.

Veřejný klíč lze volně sdílet, ale soukromý klíč by měl být zcela tajný a nikdy by neměl být sdílen s jinou stranou. Po generování klíčů, musíte vložit **ssh veřejný klíč** do konfigurace – ve skutečnosti, jeho nahrání do virtuálního počítače s Linuxem. Je uložen uvnitř\_autorizovaných \~klíčů v adresáři /.ssh domovského adresáře uživatelského účtu. Virtuální počítač s Linuxem pak je schopen rozpoznat a ověřit připojení, jakmile zadáte přidružený **soukromý klíč SSH** v klientovi SSH (v našem případě PuTTY).

Když poskytujete novým jednotlivcům přístup k virtuálnímu virtuálnímu virtuálnímu montovně: 

- Každý nový jednotlivec generuje své vlastní veřejné/soukromé klíče pomocí PuTTYgen.
- Jednotlivci ukládají své vlastní soukromé klíče samostatně a odesílají informace o veřejném klíči správci virtuálního účtu.
- Správce vloží obsah veřejného klíče do \~souboru /.ssh/authorized\_keys.
- Nový jedinec se připojuje přes PuTTY.

**Generování dvojice veřejných a soukromých klíčů**

1.  Stáhněte si PuTTYgen a <https://www.putty.org/> nainstalujte jej pomocí všech výchozích nastavení.

2.  Chcete-li otevřít PuTTYgen, vyhledejte instalační\\adresář\\PuTTY v jazyce C: Program Files PuTTY.

    ![Rozhraní PuTTY](media/puttygen-01.png)

3.  Klepněte na **tlačítko Generovat**.

    ![Dialogové okno Generátor klíčů PuTTY](media/puttygen-02.png)

4.  Po generování uložte veřejný klíč i soukromý klíč. Vložte obsah veřejného klíče do části **s veřejným klíčem SSH** v podokně **Vytvořit základy \> virtuálního počítače** (viz kroky 6 a 7 v předchozí části).

    ![Dialogové okno Generátor klíčů PuTTY](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Konfigurace funkcí virtuálního počítače

1. Na webu Azure Portal v yberte okno **Zvolte velikost,** zvolte nastavení hardwaru počítače linux, který chcete. *Minimální* požadavky na instalaci tibero a OpenFrame jsou 2 procesory a 4 GB paměti RAM, jak je znázorněno v tomto příkladu instalace:

    ![Vytvoření virtuálního počítače – základy](media/create-vm-01.png)

2. Klikněte na **3 Nastavení** a pomocí výchozího nastavení nakonfigurujte volitelné funkce.
3. Zkontrolujte platební údaje.

    ![Vytvořit virtuální počítač – nákup](media/create-vm-02.png)

4. Odešlete svůj výběr. Azure začne nasazovat virtuální počítač. Tento proces obvykle trvá několik minut.

5. Při nasazení virtuálního počítače se zobrazí jeho řídicí panel, který zobrazuje všechna nastavení, která byla vybrána během konfigurace. Poznamenejte si **veřejnou IP adresu**.

    ![tmax na řídicím panelu Azure](media/create-vm-03.png)

6. Otevřete PuTTY.

7. Do **pole Název hostitele**zadejte své uživatelské jméno a veřejnou IP adresu, kterou jste zkopírovali. Například **uživatelské\@jméno publicip**.

    ![Dialogové okno Konfigurace PuTTY](media/putty-01.png)

8. V poli **Kategorie** klepněte na **položku Připojení \> SSH \> Auth**. Zadejte cestu k souboru **soukromého klíče.**

    ![Dialogové okno Konfigurace PuTTY](media/putty-02.png)

9. Kliknutím na **Otevřít** spusťte okno PuTTY. Pokud je úspěšná, jste připojeni k novému virtuálnímu počítači CentOS spuštěného v Azure.

10. Chcete-li se přihlásit jako root uživatel, zadejte **sudo bash**.

    ![Přihlášení kořenového uživatele v příkazovém okně](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Nastavení prostředí a balíčků

Teď, když je virtuální počítač vytvořen a jste přihlášeni, musíte provést několik kroků instalace a nainstalovat požadované balíčky předinstalace.

1. Namapujte název **demo** na místní IP adresu pomocí vi`vi /etc/hosts`pro úpravu souboru hosts ( ). Za předpokladu, že naše IP je 192.168.96.148 ofdemo, je to před změnou:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     To je po změně:

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

5. Dynamicky aktualizujte parametry jádra bez restartu:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Získat požadované balíčky: Ujistěte se, že je server připojený k Internetu, stáhněte si následující balíčky a nainstalujte je:

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - Ncurses

          > [!NOTE]
          > Po instalaci balíčku ncurses vytvořte následující symbolické odkazy:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - Gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - stopa
     - ltrace
     - Gdb

7. V případě instalace Java RPM postupujte takto:

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

Tibero poskytuje několik klíčových funkcí v prostředí OpenFrame v Azure:

- Tibero se používá jako úložiště interních dat OpenFrame pro různé systémové funkce.
- Soubory VSAM, včetně KSDS, RRDS a ESDS, používají databázi Tibero interně pro ukládání dat.
- Úložiště dat TACF je uloženo v Tibero.
- Informace o katalogu OpenFrame jsou uloženy v Tibero.
- Databázi Tibero lze použít jako náhradu za IBM Db2 pro ukládání dat aplikací.

**Instalace tibero**

1. Ověřte, zda je k dispozici binární instalační soubor Tibero, a zkontrolujte číslo verze.
2. Zkopírujte software Tibero do uživatelského účtu Tibero (oframe). Například:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Otevřete\_profil .bash`vi .bash_profile`ve vi ( ) a vložte do něj následující:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Chcete-li spustit profil bash, na příkazovém řádku zadejte:

    ```
    source .bash_profile
    ```

5. Vygenerujte soubor s tipem (konfigurační soubor pro Tibero), pak jej otevřete ve vi. Například:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Změnit \$\_TB HOME/client/config/tbdsn.tbr a dát 127.0.0.1 místolocalhost, jak je znázorněno:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Vytvořte databázi. Zobrazí se výstup:

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

8. Chcete-li recyklovat Tibero, nejprve jej vypněte pomocí příkazu. `tbdown` Například:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Nyní bota Tibero pomocí `tbboot`. Například:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Chcete-li vytvořit tabulkový prostor, přistupte k databázi pomocí uživatele SYS (sys/tmax) a potom vytvořte potřebný tabulkový prostor pro výchozí svazek a TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Nyní zadejte následující příkazy SQL:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Zavádět Tibero a ověřit, že procesy Tibero jsou spuštěny:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Výstup:

![Tibero výstup](media/tibero-01.png)

## <a name="install-odbc"></a>Instalace rozhraní ODBC

Aplikace v OpenFrame komunikují s databází Tibero pomocí ROZHRANÍ ODBC API poskytovaného open-source projektem unixODBC.

Instalace rozhraní ODBC:

1. Ověřte, zda je k dispozici instalační soubor unixODBC-2.3.4.tar.gz, nebo použijte `wget unixODBC-2.3.4.tar.gz` příkaz. Například:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Rozbalte binární soubor. Například:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Přejděte do adresáře unixODBC-2.3.4 a vygenerujte Makefile pomocí informací o kontrolním počítači. Například:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Ve výchozím nastavení je unixODBC nainstalován v `--prefix` /usr /local, takže předá hodnotu pro změnu umístění. Podobně konfigurační soubory jsou nainstalovány `--sysconfdir` v /etc ve výchozím nastavení, takže předá hodnotu požadovaného umístění.

4. Spustit makefile:`[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Po kompilaci zkopírujte spustitelný soubor do adresáře programu. Například:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Pomocí vi upravte profil`vi ~/.bash_profile`bash ( ) a přidejte následující:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Použijte rozhraní ODBC. Odpovídajícím způsobem upravte následující soubory. Například:

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

8. Vytvořte symbolický odkaz a ověřte připojení databáze Tibero:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

Zobrazí se následující výstup:

![Výstup ROZHRANÍ ODBC znázorňující připojení k SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>Instalace základny OpenFrame

Aplikační server Base je nainstalován před jednotlivými službami, které OpenFrame používá ke správě systému v Azure, včetně procesů serveru zpracování transakcí.

**Instalace základny OpenFrame**

1. Ujistěte se, že instalace Tibero proběhla\_úspěšně,\_\_a\_pak\_ověřte, zda je k dispozici následující instalační soubor OpenFrame Base7 0 Linux x86 64.bin a konfigurační soubor base.properties.

2. Aktualizujte profil bash následujícími informacemi specifickými pro Tibero:

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

3. Spusťte profil bash:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Ujistěte se, že jsou spuštěny procesy Tibero. Například:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Základ](media/base-01.png)

     > [!IMPORTANT]
     > Před instalací se ujistěte, že spustíte Tibero.

5. Vygenerujte licenci na [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) a vložte licence OpenFrame Base, Batch, TACF, OSC do příslušné složky:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Stáhněte si binární soubory OpenFrame Base a base.properties:

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

7. Spusťte instalační program pomocí souboru base.properties. Například:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Po dokončení se zpráva Dokončení instalace přehraje.

8. Pomocí příkazu ověřte `ls -ltr` strukturu adresářů OpenFrame Base. Například:

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

9. Spustit základnu OpenFrame:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![výstup příkazu tmboot](media/base-02.png)

10. Ověřte, zda je stav procesu připraven pomocí příkazu tmadmin v si. RDY se zobrazí ve sloupci **stavu** pro každý z procesů:

     ![výstup příkazu tmadmin](media/base-03.png)

11. Vypnout základnu OpenFrame:

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

## <a name="install-openframe-batch"></a>Instalace dávky OpenFrame

OpenFrame Batch se skládá z několika součástí, které simulují dávková prostředí sálových počítačů a používají se ke spuštění dávkových úloh v Azure.

**Instalace dávky**

1. Zkontrolujte, zda je základní instalace úspěšná, a\_pak\_ověřte, zda je k dispozici instalační soubor OpenFrame\_\_Batch7 0 Fix2 MVS\_Linux\_x86\_64.bin a konfigurační soubor batch.properties:

2. Na příkazovém `vi batch.properties` řádku zadejte úpravu souboru batch.properties pomocí příkazu vi.

3. Následující úpravy upravte takto:

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

4. Chcete-li spustit dávkový instalační program, na příkazovém řádku zadejte:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Po dokončení instalace spusťte nainstalované sady OpenFrame zadáním `tmboot` na příkazovém řádku.

    ![tmboot výstup](media/tmboot-01.png)

6. Chcete-li zkontrolovat proces OpenFrame, zadejte `tmadmin` na příkazovém řádku.

    ![Obrazovka Správce Tmax](media/tmadmin-01.png)

7. Proveďte následující příkazy:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Pomocí `tmdown` příkazu spusťte a vypněte batch:

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

## <a name="install-tacf"></a>Instalace TACF

TACF Manager je servisní modul OpenFrame, který řídí přístup uživatelů k systémům a prostředkům prostřednictvím zabezpečení RACF.

**Instalace tacf**

1. Ověřte,\_zda je\_\_k\_dispozici\_instalační\_soubor OpenFrame Tacf7 0 Fix2 Linux x86 64.bin a konfigurační soubor tacf.properties.
2. Zkontrolujte, zda byla instalace batch úspěšná, a potom pomocí`vi tacf.properties`vi otevřete soubor tacf.properties ( ).
3. Úprava parametrů TACF:

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

     Výstup vypadá asi takto:

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

6. Na příkazovém `tmboot` řádku zadejte restartování openframe. Výstup vypadá asi takto:

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

7. Ověřte, zda je `tmadmin` stav `si` procesu připraven pomocí příkazu. Například:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     Ve sloupci **stavu** se zobrazí RDY:

    ![RDY ve sloupci stavu](media/tmboot-02.png)

8. Proveďte následující příkazy:

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

9. Vypněte server pomocí `tmdown` příkazu. Výstup vypadá asi takto:

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

## <a name="install-prosort"></a>Instalace prosortu

ProSort je nástroj používaný v dávkových transakcích pro řazení dat.

**Instalace aplikace ProSort**

1. Ujistěte se, že instalace batch byla úspěšná, a pak ověřte, zda je k dispozici instalační soubor **prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz.**

2. Spusťte instalační program pomocí souboru vlastností. Na příkazovém řádku zadejte:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Přesuňte adresář obchvatu do domovského umístění. Na příkazovém řádku zadejte:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Vytvořte podadresář licence a zkopírujte tam licenční soubor. Například:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Otevřete bash.profile v`vi .bash_profile`vi ( ) a aktualizujte jej takto:

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

6. Chcete-li spustit profil bash, zadejte na příkazovém řádku:`. .bash_profile`

7. Vytvořte konfigurační soubor. Například:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Vytvořte symbolický odkaz. Například:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Ověřte instalaci ProSort `prosort -h` provedením příkazu. Například:

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

## <a name="install-ofcobol"></a>Instalace OFCOBOL

OFCOBOL je kompilátor OpenFrame, který interpretuje programy COBOL sálového počítače. 

**Instalace OFCOBOL**

1. Ujistěte se, že instalace Batch/Online byla úspěšná,\_a\_pak ověřte, zda je k dispozici instalační soubor OpenFrame\_COBOL3 0 40\_Linux\_x86\_64.bin.

2. Chcete-li provést instalátor OFCOBOL, zadejte na příkazovém řádku:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Přečtěte si licenční smlouvu a pokračujte stisknutím klávesy Enter.

4. Přijměte licenční smlouvu. Po dokončení instalace se zobrazí následující:

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

5. Otevřete profil bash v`vi .bash_profile`vi ( ) a ověřte, že je aktualizován s ofcobol proměnných.
6. Spusťte profil bash. Na příkazovém řádku zadejte:

     ```
      source ~/.bash_profile
     ```

7. Zkopírujte licenci OFCOBOL do nainstalované složky. Například:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Přejděte na konfigurační soubor OpenFrame tjclrun.conf a otevřete jej ve vi. Například:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Zde je sekce SYSLIB před změnou:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Zde je sekce SYSLIB po změně:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Zkontrolujte soubor\_OpenFrame COBOL\_InstallLog.log ve vi a ověřte, zda nedošlo k chybám. Například:
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
10. Pomocí `ofcob --version` příkazu a zkontrolujte číslo verze k ověření instalace. Například:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Restartujte OpenFrame `tmdown/tmboot` pomocí příkazu.

## <a name="install-ofasm"></a>Instalace OFASM

OFASM je kompilátor OpenFrame, který interpretuje programy assembleru sálového počítače.

**Instalace OFASM**

1. Ujistěte se, že instalace Batch/Online byla úspěšná, a\_\_pak ověřte, zda je k dispozici instalační soubor OpenFrame\_ASM3\_0 Linux x86\_64.bin.

2. Spusťte instalační program. Například:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Přečtěte si licenční smlouvu a pokračujte stisknutím klávesy Enter.
4. Přijměte licenční smlouvu.
5. Ověřte, zda je profil bash aktualizován proměnnými OFASM. Například:

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

6. Otevřete konfigurační soubor OpenFrame tjclrun.conf ve vi a upravte jej následujícím způsobem:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Zde je sekce [SYSLIB] *před* změnou:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Zde je sekce [SYSLIB] *po* změně:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Otevřete soubor\_OpenFrame ASM\_InstallLog.log ve vi a ověřte, zda nedošlo k chybám. Například:

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

8. Restartujte OpenFrame vydáním jednoho z následujících příkazů:

     ```
     tmdown / tmboot
     ```

     —nebo—

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Instalace osc

OSC je prostředí OpenFrame podobné IBM CICS, které podporuje vysokorychlostní OLTP transakce a další funkce správy.

**Instalace OSC**

1. Ujistěte se, že základní instalace proběhla\_úspěšně,\_a\_pak\_\_ověřte, zda je k dispozici instalační soubor OpenFrame OSC7 0 Fix2 Linux x86\_64.bin a konfigurační soubor osc.properties.
2. Upravte následující parametry v souboru osc.properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Spusťte instalační program pomocí souboru vlastností, jak je znázorněno:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Po dokončení se zobrazí zpráva "Instalace dokončena".

4. Ověřte, zda je profil bash aktualizován proměnnými OSC.
5. Projděte si\_soubor InstallLog.log OpenFrame OSC7\_0\_Fix2.\_ Výsledek by měl vypadat přibližně takto:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Pomocí vi otevřete konfigurační soubor ofsys.seq. Například:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. V \#oddílech \#BASE a BATCH upravte parametry podle obrázku.

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

8. Zkopírujte licenční soubor. Například:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Chcete-li spustit a vypnout OSC, inicializovat oblast `osctdlinit OSCOIVP1` CICS sdílené paměti zadáním na příkazovém řádku.

10. Spuštění `oscboot` mačkáte OSC. Výstup vypadá asi takto:

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

11. Chcete-li ověřit, zda je `tmadmin` stav procesu připraven, použijte příkaz v si. Všechny procesy by měly ve **sloupci stavu** zobrazit RDY.

    ![Procesy zobrazující RDY](media/tmadmin-02.png)

12. Vypněte OSC pomocí `oscdown` příkazu.

## <a name="install-jeus"></a>Instalace JEUS

JEUS (Java Enterprise User Solution) poskytuje prezentační vrstvu webového aplikačního serveru OpenFrame.

Před instalací JEUS, nainstalujte balíček Apache Ant, který poskytuje knihovny a nástroje příkazového řádku potřebné k instalaci JEUS.

**Instalace Apache Ant**

1. Stáhnout Ant binární `wget` pomocí příkazu. Například:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Pomocí `tar` nástroje extrahujte binární soubor a přesuňte jej do příslušného umístění. Například:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Pro efektivitu vytvořte symbolický odkaz:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Otevřete profil bash ve`vi .bash_profile`vi ( )a aktualizujte jej následujícími proměnnými:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Použijte proměnnou upraveného prostředí. Například:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Instalace jeusu**

1. Rozšiřte instalační program `tar` pomocí nástroje. Například:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Vytvořte složku **jeus** (`mkdir jeus7`) a rozbalte binární soubor.
3. Přecházejte do **instalačního** adresáře (nebo použijte parametr JEUS pro vlastní prostředí). Například:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Spusťte `ant clean-all` před provedením sestavení. Výstup vypadá asi takto:

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

5.  Vytvořte zálohu souboru domain-config-template.properties. Například:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Otevřete soubor domain-config-template.properties ve vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Změnit `jeus.password=jeusadmin nodename=Tmaxsoft` na`jeus.password=tmax1234 nodename=ofdemo`

8. Spusťte `ant install` příkaz k sestavení JEUS.
9.  Aktualizujte soubor\_profilu .bash proměnnými JEUS, jak je znázorněno na obrázku:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Spusťte profil bash. Například:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Nepovinné*. Vytvořte alias pro snadné vypnutí a spuštění komponent JEUS:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Chcete-li ověřit instalaci, spusťte server pro správu domény, jak je znázorněno:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Ověřte pomocí webového přihlášení pomocí syntaxe:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Zobrazí se <http://192.168.92.133:9736/webadmin/login.> například přihlašovací obrazovka:
    
     ![Přihlašovací obrazovka jeus WebAdmin](media/jeus-01.png)

     > [!NOTE]
     > Pokud narazíte na nějaké problémy se zabezpečením portu,`systemctl stop firewall`otevřete port 9736 nebo vypněte bránu firewall ( ).

14. Chcete-li změnit název hostitele pro server1, klepněte na tlačítko **Zamknout & upravit**a potom klepněte na příkaz **Server1**. V okně Server změňte název hostitele takto:

    1.  Změnit **název názvu** na **ofdemo**.
    2.  Na pravé straně okna klikněte na **OK.**
    3.  Klepněte na **tlačítko Použít změny** na levé dolní straně okna a pro popis zadejte Změnit název *hostitele*.

    ![Obrazovka JEUS WebAdmin](media/jeus-02.png)

15. Na potvrzovací obrazovce ověřte, zda je konfigurace úspěšná.

    ![jeus_domain server](media/jeus-03.png)

16. Spusťte proces spravovaného serveru "server1" pomocí následujícího příkazu:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Instalace OFGW

OFGW je brána OpenFrame, která podporuje komunikaci mezi emulátorem terminálu 3270 a základnou OSI a spravuje relace mezi emulátorem terminálu a OSI.

**Instalace OFGW**

1. Ujistěte se, že jeus byl úspěšně nainstalován,\_\_pak\_ověřte, že ofGW7 0 1 Generic.bin instalační soubor je k dispozici.
2. Spusťte instalační program. Například:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Pro odpovídající výzvy použijte následující umístění:
     -   Domovský adresář JEUS
     -   Název domény JEUS
     -   Název serveru JEUS
     -   Tibero řidič
     -   ID uzlu Tmax ofdemo

4. Přijměte zbývající výchozí hodnoty a stisknutím klávesy Enter ukončete instalační program.

5. Ověřte, zda adresa URL pro OFGW funguje podle očekávání:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Zobrazí se následující obrazovka:

    ![Webový terminál OpenFrame](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Instalace ofmanageru

OFManager poskytuje funkce provozu a správy pro OpenFrame ve webovém prostředí.

**Instalace ofmanageru**

1. Ověřte, zda\_je k dispozici instalační soubor SOUBORU SOUBORU OBECNÁ.BIN OFManager7.
2. Spusťte instalační program. Například:

     ```
     OFManager7_Generic.bin
     ```

3.  Pokračujte stisknutím klávesy Enter a pak přijměte licenční smlouvu.
4.  Zvolte instalační složku.
5.  Přijměte výchozí hodnoty.
6.  Zvolte Tibero jako databázi.
7.  Stisknutím klávesy Enter ukončete instalační program.
8.  Ověřte, zda adresa URL pro ofmanager funguje podle očekávání:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Zobrazí se úvodní obrazovka:

![Přihlašovací obrazovka Tmax OpenFrame Manager](media/ofmanager-01.png)

Tím se dokončí instalace komponent OpenFrame.

## <a name="next-steps"></a>Další kroky

Pokud uvažujete o migraci sálových počítačů, náš rozšiřující se partnerský ekosystém je vám k dispozici. Podrobné pokyny k výběru partnerského řešení naleznete v [platformě Modernization Alliance](https://datamigration.microsoft.com/).

-   [Začínáme s Azure](https://docs.microsoft.com/azure/)
-   [Dokumentace k hostitelským integračním serveru (HIS)](https://docs.microsoft.com/host-integration-server/)
-   [Průvodce výtahem a směnami virtuálního datového centra Azure](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
