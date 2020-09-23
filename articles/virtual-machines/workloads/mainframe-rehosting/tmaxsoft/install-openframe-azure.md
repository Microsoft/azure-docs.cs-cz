---
title: Instalace TmaxSoft OpenFrame na Azure Virtual Machines
description: Naučte se, jak nastavit prostředí OpenFrame v Azure, které je vhodné pro vývoj, ukázky, testování nebo produkční úlohy.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: bd5b20d8e713e07b52eb1d6cbc57f01b9e5c1a95
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987474"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Instalace TmaxSoft OpenFrame v Azure

Naučte se, jak nastavit prostředí OpenFrame v Azure, které je vhodné pro vývoj, ukázky, testování nebo produkční úlohy. Tento kurz vás provede jednotlivými kroky.

OpenFrame zahrnuje několik komponent, které vytvářejí prostředí emulace sálového počítače v Azure. Například OpenFrame online služby nahradit middleware sálového počítače, jako je třeba CICS (Customer Information Control System) a OpenFrame Batch, se svou komponentou TJES nahrazuje podsystém položek úloh v sálovém počítači IBM (JES).

OpenFrame funguje se všemi relačními databázemi, včetně Oracle Database, Microsoft SQL Server, IBM Db2 a MySQL. Tato instalace OpenFrame používá relační databázi TmaxSoft Tibero. OpenFrame i Tibero se spouštějí v operačním systému Linux. Tento kurz nainstaluje CentOS 7,3, i když můžete použít jiné podporované distribuce systému Linux. Aplikační server OpenFrame a databáze Tibero jsou nainstalované na jednom virtuálním počítači (VM).

Tento kurz vás provede instalací součástí sady OpenFrame Suite. Některé se musí instalovat samostatně.

Hlavní komponenty OpenFrame:

- Požadované instalační balíčky.
- Databáze Tibero
- Rozhraní ODBC (Open Database Connectivity) používá aplikace v OpenFrame ke komunikaci s databází Tibero.
- OpenFrame Base, middleware, který spravuje celý systém.
- OpenFrame Batch, řešení, které nahrazuje systémy dávek tohoto sálového počítače.
- TACF, modul služby, který řídí přístup uživatelů k systémům a prostředkům.
- Prořazení, nástroj pro řazení pro transakce Batch.
- OFCOBOL, kompilátor, který interpretuje programy COBOL sálového počítače.
- OFASM, kompilátor, který interpretuje programy assembleru sálového počítače.
- OpenFrame Server Type C (OSC), řešení, které nahrazuje middleware sálového počítače a IBM CICS.
- Java Enterprise User Solution (JEUS), server webové aplikace, který je certifikovaný pro Java Enterprise Edition 6.
- OFGW, komponenta brány OpenFrame, která poskytuje naslouchací proces 3270.
- OFManager řešení, které poskytuje funkce pro operace a správu OpenFrame ve webovém prostředí.

Další požadované součásti OpenFrame:

- OSI, řešení, které nahrazuje middleware sálového počítače a řadiče pro IMS.
- TJES řešení, které poskytuje prostředí JES sálového počítače.
- OFTSAM řešení, které umožňuje používat soubory SAM (V) v otevřeném systému.
- OFHiDB, řešení, které nahrazuje databázi IMS v rámci sálového počítače.
- OFPLI, kompilátor, který interpretuje programy PL/I v rámci sálového počítače.
- PROTRIEVE, řešení, které spouští certifikační autoritu pro sálový jazyk – Easytrieve.
- OFMiner, řešení, které analyzuje prostředky sálových počítačů a pak je migruje do Azure.

## <a name="architecture"></a>Architektura

Následující obrázek poskytuje přehled součástí architektury OpenFrame 7,0 instalovaných v tomto kurzu:

![Součásti OpenFrame](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Požadavky na systém Azure

V následující tabulce jsou uvedené požadavky na instalaci v Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Požadavek</th><th>Description</th></tr>
</thead>
<tbody>
<tr><td>Podporovaná distribuce systému Linux v Azure
</td>
<td>
Linux x86 2,6 (32 bitů, 64-bit)<br/>
Red Hat 7. x<br/>
CentOS 7. x<br/>
</td>
</tr>
<tr><td>Hardware
</td>
<td>Jádra: 2 (minimální)<br/>
Paměť: 4 GB (minimální)<br/>
Odkládací místo: 1 GB (minimální)<br/>
Pevný disk: 100 GB (minimální)<br/>
</td>
</tr>
<tr><td>Volitelný software pro uživatele systému Windows
</td>
<td>Výstup: používá se v této příručce ke konfiguraci funkcí virtuálních počítačů.<br/>
WinSCP: oblíbený klient SFTP a klient FTP, který můžete použít<br/>
Zatmění pro Windows: vývojová platforma podporovaná nástrojem TmaxSoft<br/>
(Microsoft Visual Studio se v tuto chvíli nepodporuje.)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Požadavky

Naplánujte si útratu několika dní a sestavte veškerý požadovaný software a dokončete všechny ruční procesy.

Než začnete, udělejte toto:

- Získejte instalační médium OpenFrame z TmaxSoft. Pokud jste stávající zákazník TmaxSoft, obraťte se na zástupce TmaxSoft, kde najdete licencovanou kopii. Jinak si vyžádejte zkušební verzi z [TmaxSoft](https://www.tmaxsoft.com/contact/).

- Vyžádejte si dokumentaci k OpenFrame odesláním e-mailu na adresu <support@tmaxsoft.com> .

- Získejte předplatné Azure, pokud ho ještě nemáte. Můžete si také vytvořit [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

- Nepovinný parametr. Nastavte tunel VPN typu Site-to-site nebo JumpBox, který omezí přístup k virtuálnímu počítači Azure na povolené uživatele ve vaší organizaci. Tento krok není povinný, ale je to doporučený postup.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Nastavení virtuálního počítače v Azure pro OpenFrame a Tibero

Prostředí OpenFrame můžete nastavit pomocí různých vzorů nasazení, ale následující postup ukazuje, jak nasadit aplikační server OpenFrame a databázi Tibero na jednom virtuálním počítači. V větších prostředích a pro úlohy výraznou je osvědčeným postupem, jak nasadit databázi samostatně na vlastním virtuálním počítači, aby se zajistil vyšší výkon.

**Vytvoření virtuálního počítače**

1. Přejít na Azure Portal <https://portal.azure.com> a přihlásit se ke svému účtu.

2. Klikněte na **Virtuální počítače**.

    ![Seznam prostředků v Azure Portal](media/vm-01.png)

3. Klikněte na **Přidat**.

    ![Přidat možnost v Azure Portal](media/vm-02.png)

4. Napravo od **operačních systémů**klikněte na **Další**.

     ![Další možnost v Azure Portal](media/vm-03.png)

5. Pokud chcete postupovat přesně podle tohoto návodu, klikněte na **CentOS 7,3** , nebo můžete zvolit jinou podporovanou distribuci systému Linux.

     ![Možnosti operačního systému v Azure Portal](media/vm-04.png)

6. V nastavení **základní informace** zadejte **název**, **uživatelské jméno**, **typ ověřování**, **předplatné** (průběžné platby je AWS styl platby) a **skupinu prostředků** (použijte existující skupinu TmaxSoft).

7. Po dokončení (včetně páru veřejného a privátního klíče pro **typ ověřování**) klikněte na **Odeslat**.

> [!NOTE]
> Pokud používáte veřejný klíč SSH pro **typ ověřování**, přečtěte si postup v části Další informace, jak vygenerovat pár veřejného a privátního klíče, a pak pokračujte v tomto postupu.

### <a name="generate-a-publicprivate-key-pair"></a>Vygenerovat pár veřejného a privátního klíče

Pokud používáte operační systém Windows, potřebujete PuTTYgen vygenerovat pár veřejného a privátního klíče.

Veřejný klíč může být volně sdílený, ale privátní klíč by měl být uložený bez tajných kódů a neměl by být nikdy sdílen s jinou stranou. Po vygenerování klíčů je nutné vložit **veřejný klíč SSH** do konfigurace – v důsledku toho se nahraje do virtuálního počítače se systémem Linux. Je uložený uvnitř autorizovaných \_ klíčů v \~ adresáři/.ssh domovského adresáře uživatelského účtu. Virtuální počítač se systémem Linux pak dokáže rozpoznat a ověřit připojení, jakmile v klientovi SSH poskytnete přidružený **privátní klíč SSH** (v našem případě do výstupu).

Když těmto VIRTUÁLNÍm počítačům udělíte přístup novým jednotlivcůům: 

- Každý nový jednotlivec generuje vlastní veřejný a privátní klíč pomocí PuTTYgen.
- Jednotliví uživatelé ukládají vlastní privátní klíče samostatně a odesílají informace o veřejném klíči správci virtuálního počítače.
- Správce vloží obsah veřejného klíče do \~ \_ souboru klíčů/.ssh/Authorized.
- Nová osoba se připojí prostřednictvím výstupu.

**Vytvoření páru veřejného a privátního klíče**

1.  Stáhněte si PuTTYgen z aplikace <https://www.putty.org/> a nainstalujte ji pomocí všech výchozích nastavení.

2.  Chcete-li otevřít PuTTYgen, vyhledejte instalační adresář pro výstup v jazyce C: \\ Program Files \\ .

    ![Rozhraní výstupu](media/puttygen-01.png)

3.  Klikněte na **Generovat**.

    ![Dialogové okno generátoru klíčů pro výstupy](media/puttygen-02.png)

4.  Po generaci uložte veřejný klíč i privátní klíč. Do části **veřejný klíč SSH** v podokně s ** \> informacemi o vytvoření virtuálního počítače** vložte obsah veřejného klíče (viz kroky 6 a 7 v předchozí části).

    ![Dialogové okno generátoru klíčů pro výstupy](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Konfigurace funkcí virtuálních počítačů

1. V Azure Portal v okně **zvolit velikost** vyberte požadovaná nastavení hardwaru pro Linux. *Minimální* požadavky pro instalaci Tibero a OpenFrame jsou 2 procesory a 4 GB paměti RAM, jak je znázorněno v tomto příkladu instalace:

    ![Vytvoření základních informací o virtuálním počítači](media/create-vm-01.png)

2. Klikněte na **3 nastavení** a použijte výchozí nastavení a nakonfigurujte volitelné funkce.
3. Podívejte se na podrobnosti o platbě.

    ![Vytvořit virtuální počítač – koupit](media/create-vm-02.png)

4. Odešlete své výběry. Azure začne nasadit virtuální počítač. Tento proces obvykle trvá několik minut.

5. Po nasazení virtuálního počítače se zobrazí jeho řídicí panel zobrazující všechna nastavení, která byla vybrána během konfigurace. Poznamenejte si **veřejnou IP adresu**.

    ![Tmax na řídicím panelu Azure](media/create-vm-03.png)

6. Otevřete PuTTY.

7. Jako **název hostitele**zadejte svoje uživatelské jméno a veřejnou IP adresu, kterou jste zkopírovali. Například **uživatelské jméno \@ publicip**.

    ![Dialogové okno Konfigurace výstupu](media/putty-01.png)

8. V poli **kategorie** klikněte na **připojení \> SSH \> ověřování**. Zadejte cestu k souboru **privátního klíče** .

    ![Dialogové okno Konfigurace výstupu](media/putty-02.png)

9. Kliknutím na **Otevřít otevřete** okno výstupu. V případě úspěchu budete připojeni k novému VIRTUÁLNÍmu počítači s CentOS běžícímu v Azure.

10. Pokud se chcete přihlásit jako uživatel root, zadejte **sudo bash**.

    ![Přihlášení uživatele root user v příkazovém okně](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Nastavení prostředí a balíčků

Teď, když je virtuální počítač vytvořený a jste přihlášeni, musíte provést několik kroků instalace a nainstalovat požadované předinstalační balíčky.

1. Namapujte název **ofdemo** na místní IP adresu pomocí VI pro úpravu souboru Hosts ( `vi /etc/hosts` ). Za předpokladu, že naše IP adresa je 192.168.96.148 ofdemo, je to před změnou:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Tato změna je po provedení změny:

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

3. Změňte heslo pro uživatele oframe7:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Aktualizujte parametry jádra v/etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Aktualizovat parametry jádra dynamicky bez restartování:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Získat požadované balíčky: Ujistěte se, že je server připojený k Internetu, Stáhněte si následující balíčky a nainstalujte je:

     - dos2unix
     - glibc
     - glibc. i686 glibc. x86 \_ 64
     - libaio
     - ncurses

          > [!NOTE]
          > Po instalaci balíčku ncurses vytvořte následující symbolické odkazy:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - RSZ
     - RSZ – c + +
     - libaio-devel. x86 \_ 64
     - strace
     - ltrace
     - GDB

7. V případě instalace Java ot./min. proveďte tyto kroky:

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

Tibero nabízí několik klíčových funkcí v prostředí OpenFrame v Azure:

- Tibero se používá jako interní úložiště dat OpenFrame pro různé systémové funkce.
- Soubory VSAM, včetně KSDS, RRDS a ESDS, používejte interně databázi Tibero pro ukládání dat.
- Úložiště dat TACF je uloženo v Tibero.
- Informace o katalogu OpenFrame jsou uložené v Tibero.
- Databázi Tibero je možné použít jako náhradu pro IBM Db2 k ukládání dat aplikací.

**Instalace Tibero**

1. Ověřte, zda je k dispozici soubor binární instalace Tibero, a zkontrolujte číslo verze.
2. Zkopírujte software Tibero do uživatelského účtu Tibero (oframe). Příklad:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Otevřete profil. bash \_ v VI ( `vi .bash_profile` ) a vložte do něj následující:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Pokud chcete spustit profil bash, zadejte na příkazovém řádku tento příkaz:

    ```
    source .bash_profile
    ```

5. Vygenerujte soubor s tipem (konfigurační soubor pro Tibero) a pak ho otevřete v VI. Příklad:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Upravte \$ TB \_ Home/Client/config/tbdsn. TBR a vložte adresu 127.0.0.1 místo toho oflocalhost, jak je znázorněno níže:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Vytvořte databázi. Objeví se následující výstup:

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

8. Pokud chcete Tibero recyklovat, nejdřív ho vypínejte pomocí `tbdown` příkazu. Příklad:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Nyní spusťte Tibero pomocí `tbboot` . Příklad:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Pokud chcete vytvořit tabulkový prostor, přejděte k databázi pomocí SYS User (sys/Tmax) a pak vytvořte potřebný tabulkový prostor pro výchozí svazek a TACF:

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

12. Spusťte Tibero a ověřte, zda jsou spuštěny procesy Tibero:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Výstup:

![Výstup Tibero](media/tibero-01.png)

## <a name="install-odbc"></a>Instalace rozhraní ODBC

Aplikace v OpenFrame komunikují s databází Tibero pomocí rozhraní ODBC API, které poskytuje projekt Open-Source unixODBC.

Instalace rozhraní ODBC:

1. Ověřte, zda je k dispozici instalační soubor unixODBC-2.3.4. tar. gz, nebo použijte `wget unixODBC-2.3.4.tar.gz` příkaz. Příklad:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Rozbalte binární soubor. Příklad:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Přejděte do adresáře unixODBC-2.3.4 a vygenerujte soubor pravidel pomocí kontroly informací o počítači. Příklad:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Ve výchozím nastavení je unixODBC nainstalován v/usr/local, takže `--prefix` předává hodnotu pro změnu umístění. Podobně ve výchozím nastavení jsou konfigurační soubory nainstalovány ve/etc, takže `--sysconfdir` předává hodnotu požadovaného umístění.

4. Spustit soubor pravidel: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Po zkompilování zkopírujte spustitelný soubor do adresáře programu. Příklad:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Pomocí VI upravte profil bash ( `vi ~/.bash_profile` ) a přidejte následující:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Použijte rozhraní ODBC. Upravte následující soubory odpovídajícím způsobem. Příklad:

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

8. Vytvořte symbolický odkaz a ověřte připojení k databázi Tibero:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

Zobrazí se následující výstup:

![Výstup ODBC ukazující připojení k SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>Nainstalovat OpenFrame Base

Základní aplikační server je nainstalován před jednotlivými službami, které OpenFrame používá ke správě systému v Azure, včetně procesů serveru pro zpracování transakcí.

**Instalace OpenFrame Base**

1. Zajistěte, aby byla instalace Tibero úspěšná, a pak ověřte, že jsou k dispozici následující OpenFrame soubor s příponou \_ Base7 \_ 0 \_ Linux \_ x86 \_ 64. bin a konfigurační soubor Base. Properties.

2. Aktualizujte profil bash pomocí následujících informací týkajících se Tibero:

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
4. Ujistěte se, že jsou spuštěné procesy Tibero. Příklad:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Základ](media/base-01.png)

     > [!IMPORTANT]
     > Před instalací se ujistěte, že jste spustili Tibero.

5. Vygenerujte licenci na adrese [TechNet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) a do příslušné složky umístěte licence OpenFrame Base, Batch, TACF a osc:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Stáhněte si základní binární soubor OpenFrame a základní soubory. Properties:

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

7. Spusťte instalační program pomocí souboru Base. Properties. Příklad:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Po dokončení bude zpráva o dokončení instalace dokončena.

8. Pomocí příkazu ověřte strukturu adresáře základní OpenFrame `ls -ltr` . Příklad:

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

9. Začátek OpenFrame Base:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![výstup příkazu tmboot](media/base-02.png)

10. Ověřte, jestli je stav procesu připravený, pomocí příkazu tmadmin v si. RDY se zobrazí ve sloupci **stav** u každého procesu:

     ![výstup příkazu tmadmin](media/base-03.png)

11. Vypnout OpenFrame Base:

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

## <a name="install-openframe-batch"></a>Nainstalovat dávku OpenFrame

OpenFrame Batch se skládá z několika komponent, které simulují prostředí Batch z sálového počítače a slouží ke spouštění dávkových úloh v Azure.

**Instalace dávky**

1. Zajistěte, aby základní instalace proběhla úspěšně, a pak ověřte, že \_ \_ \_ \_ soubor instalačního programu OpenFrame Batch7 0 Fix2 MVS \_ Linux \_ x86 \_ 64. bin a zda jsou k dispozici konfigurační soubor Batch. Properties:

2. Na příkazovém řádku zadejte `vi batch.properties` a upravte soubor Batch. Properties pomocí VI.

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

4. Pokud chcete spustit instalační program služby Batch, zadejte na příkazovém řádku tento příkaz:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Po dokončení instalace spusťte nainstalovaná OpenFrame sady zadáním `tmboot` příkazu do příkazového řádku.

    ![výstup tmboot](media/tmboot-01.png)

6. `tmadmin`Do příkazového řádku zadejte příkaz pro kontrolu OpenFrame procesu.

    ![Obrazovka správce Tmax](media/tmadmin-01.png)

7. Spusťte následující příkazy:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Pomocí `tmdown` příkazu spusťte a vypněte dávku:

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

## <a name="install-tacf"></a>Nainstalovat TACF

TACF Manager je modul služby OpenFrame, který řídí přístup uživatelů k systémům a prostředkům prostřednictvím zabezpečení RACF.

**Instalace TACF**

1. Ověřte, zda je \_ k \_ \_ \_ dispozici instalační soubor OpenFrame Tacf7 0 Fix2 Linux \_ x86 \_ 64. bin a konfigurační soubor TACF. Properties.
2. Zajistěte, aby byla instalace dávky úspěšná, a pak pomocí VI otevřete soubor TACF. Properties ( `vi tacf.properties` ).
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

     Výstup bude vypadat nějak takto:

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

6. Na příkazovém řádku zadejte příkaz `tmboot` pro restartování OpenFrame. Výstup bude vypadat nějak takto:

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

7. Ověřte, že je stav procesu připravený pomocí `tmadmin` příkazu v `si` příkazu. Příklad:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     Ve sloupci **stav** se zobrazí RDY:

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

9. Vypněte server pomocí `tmdown` příkazu. Výstup bude vypadat nějak takto:

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

## <a name="install-prosort"></a>Nainstalovat prořazení

Prořazení je nástroj používaný v transakcích služby Batch pro řazení dat.

**Instalace prořazení**

1. Zajistěte, aby byla instalace dávky úspěšná, a pak ověřte, že je k dispozici soubor instalačního programu **prořazení-bin \_ 2sp3-linux64-2123-opt. tar. gz** .

2. Spusťte instalační program pomocí souboru vlastností. Na příkazovém řádku zadejte:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Přesuňte adresář prořazení do domovského umístění. Na příkazovém řádku zadejte:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Vytvořte podadresář licence a zkopírujte do něj soubor s licencí. Příklad:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Otevřete bash. profil v VI ( `vi .bash_profile` ) a aktualizujte ho následujícím způsobem:

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

6. Profil bash spustíte tak, že na příkazovém řádku zadáte: `. .bash_profile`

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

9. Ověřte instalaci prořazením spuštěním `prosort -h` příkazu. Příklad:

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

## <a name="install-ofcobol"></a>Nainstalovat OFCOBOL

OFCOBOL je OpenFrame kompilátor, který interpretuje programy COBOL sálového počítače. 

**Instalace OFCOBOL**

1. Ujistěte se, že se instalace dávky nebo online zdařila, a pak ověřte, zda \_ \_ je k \_ dispozici instalační soubor OpenFrame COBOL3 0 40 \_ Linux \_ x86 \_ 64. bin.

2. Instalační program OFCOBOL spustíte tak, že na příkazovém řádku zadáte:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Přečtěte si licenční smlouvu a pokračujte stisknutím klávesy ENTER.

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

5. Otevřete profil bash v VI ( `vi .bash_profile` ) a ověřte, jestli je aktualizovaný pomocí proměnných OFCOBOL.
6. Spusťte profil bash. Na příkazovém řádku zadejte:

     ```
      source ~/.bash_profile
     ```

7. Zkopírujte licenci OFCOBOL do nainstalované složky. Příklad:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Přejít do konfiguračního souboru OpenFrame tjclrun. conf a otevřete ho v VI. Příklad:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Toto je část SYSLIB před změnou:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Tady je část SYSLIB po změně:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Zkontrolujte soubor OpenFrame \_ COBOL \_ InstallLog. log v VI a ověřte, že neexistují žádné chyby. Příklad:
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
10. Pomocí `ofcob --version` příkazu a zkontrolujte číslo verze a ověřte instalaci. Příklad:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Restartujte OpenFrame pomocí `tmdown/tmboot` příkazu.

## <a name="install-ofasm"></a>Nainstalovat OFASM

OFASM je kompilátor OpenFrame, který interpretuje programy assembleru sálového počítače.

**Instalace OFASM**

1. Ujistěte se, že se instalace dávky nebo online zdařila, a pak ověřte, zda \_ \_ je k \_ dispozici instalační soubor OpenFrame ASM3 0 Linux \_ x86 \_ 64. bin.

2. Spusťte instalační program. Příklad:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Přečtěte si licenční smlouvu a pokračujte stisknutím klávesy ENTER.
4. Přijměte licenční smlouvu.
5. Ověřte, že se profil bash aktualizuje pomocí proměnných OFASM. Příklad:

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

6. Otevřete konfigurační soubor OpenFrame tjclrun. conf v VI a upravte ho následujícím způsobem:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Toto je část [SYSLIB] *před* změnou:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Toto je část [SYSLIB] *po* změně:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Otevřete soubor OpenFrame \_ ASM \_ InstallLog. log v VI a ověřte, že nedošlo k chybám. Příklad:

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

8. Restartujte OpenFrame tím, že vydáváte jeden z následujících příkazů:

     ```
     tmdown / tmboot
     ```

     —nebo—

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Instalace OSC

OSC je prostředí OpenFrame podobné IBM CICS, které podporuje vysokorychlostní transakce OLTP a další funkce správy.

**Instalace OSC**

1. Zajistěte, aby byla základní instalace úspěšná, a pak ověřte, že \_ \_ jsou k \_ dispozici instalační soubor OpenFrame OSC7 0 Fix2 \_ Linux \_ x86 \_ 64. bin a soubor osc. Properties.
2. V souboru osc. Properties upravte následující parametry:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Spusťte instalační program pomocí souboru vlastností, jak je znázorněno níže:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Po dokončení se zobrazí zpráva "instalace byla dokončena".

4. Ověřte, že se profil bash aktualizoval pomocí proměnných OSC.
5. Zkontrolujte soubor OpenFrame \_ OSC7 \_ 0 \_ Fix2 \_ InstallLog. log. Měl by vypadat přibližně takto:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. K otevření konfiguračního souboru ofsys. SEQ použijte VI. Příklad:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. V \# \# sekcích Base a Batch Upravte parametry podle obrázku.

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

9. Chcete-li spustit a vypnout příkaz OSC, inicializujte sdílenou paměť oblasti CICS zadáním `osctdlinit OSCOIVP1` na příkazovém řádku.

10. Spusťte příkaz `oscboot` pro spuštění systému osc. Výstup bude vypadat nějak takto:

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

11. Chcete-li ověřit, zda je stav procesu připraven, použijte `tmadmin` příkaz v poli si. Všechny procesy by měly zobrazit RDY ve sloupci **stav** .

    ![Procesy zobrazující RDY](media/tmadmin-02.png)

12. Ukončete příkaz OSC pomocí `oscdown` příkazu.

## <a name="install-jeus"></a>Nainstalovat JEUS

JEUS (uživatelské řešení Java Enterprise) poskytuje prezentační vrstvu serveru webové aplikace OpenFrame.

Před instalací JEUS nainstalujte balíček Apache Ant, který poskytuje knihovny a nástroje příkazového řádku potřebné k instalaci JEUS.

**Instalace Apache Ant**

1. Stáhněte si binární soubor ANT pomocí `wget` příkazu. Příklad:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Použijte `tar` Nástroj k extrakci binárního souboru a jeho přesunutí do vhodného umístění. Příklad:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. V případě efektivity vytvořte symbolický odkaz:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Otevřete profil bash v VI ( `vi .bash_profile` ) a aktualizujte ho pomocí těchto proměnných:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Použijte upravenou proměnnou prostředí. Příklad:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Instalace JEUS**

1. Rozbalte instalační program pomocí `tar` nástroje. Příklad:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Vytvořte složku **jeus** ( `mkdir jeus7` ) a rozbalte binární soubor.
3. Přejděte do **instalačního** adresáře (nebo použijte parametr JEUS pro vlastní prostředí). Příklad:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Provede `ant clean-all` se před provedením sestavení. Výstup bude vypadat nějak takto:

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

5.  Vytvořte zálohu souboru Domain-config-Template. Properties. Příklad:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Otevřete soubor doména-config-Template. Properties v VI:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Změnit `jeus.password=jeusadmin nodename=Tmaxsoft` na `jeus.password=tmax1234 nodename=ofdemo`

8. Spusťte `ant install` příkaz pro sestavení JEUS.
9.  Aktualizujte \_ soubor profilu. bash pomocí proměnných JEUS, jak je znázorněno níže:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Spusťte profil bash. Příklad:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Volitelné*. Vytvoření aliasu pro snadné vypnutí a spouštění součástí JEUS:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Chcete-li ověřit instalaci, spusťte server pro správu domény, jak je znázorněno níže:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Ověřit podle přihlášení k webu pomocí syntaxe:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Zobrazí se například <http://192.168.92.133:9736/webadmin/login.> přihlašovací obrazovka:
    
     ![Přihlašovací obrazovka webadmin JEUS](media/jeus-01.png)

     > [!NOTE]
     > Pokud dojde k problémům se zabezpečením portů, otevřete port 9736 nebo zakažte bránu firewall ( `systemctl stop firewall` ).

14. Chcete-li změnit název hostitele pro Server1, klikněte na tlačítko **uzamknout & upravit**a potom klikněte na možnost **Server1**. V okně Server změňte název hostitele následujícím způsobem:

    1.  Změňte **Node** na **ofdemo**.
    2.  Klikněte na tlačítko **OK** na pravé straně okna.
    3.  Klikněte na **použít změny** v levé dolní části okna a pro Popis zadejte *název hostitele*.

    ![Obrazovka webadmin JEUS](media/jeus-02.png)

15. Ověřte, zda je konfigurace na potvrzovací obrazovce úspěšná.

    ![obrazovka serveru jeus_domain](media/jeus-03.png)

16. Spusťte proces spravovaného serveru "Server1" pomocí následujícího příkazu:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Nainstalovat OFGW

OFGW je OpenFrame brána, která podporuje komunikaci mezi emulátorem terminálu 3270 a základem OSI a spravuje relace mezi emulátorem terminálu a OSI.

**Instalace OFGW**

1. Ujistěte se, že JEUS byl úspěšně nainstalován, a pak ověřte, \_ zda \_ \_ je k dispozici soubor instalace Generic. bin OFGW7 0 1.
2. Spusťte instalační program. Příklad:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Pro odpovídající výzvy použijte následující umístění:
     -   Domovský adresář JEUS
     -   Název domény JEUS
     -   Název serveru JEUS
     -   Ovladač Tibero
     -   ID uzlu Tmax ofdemo

4. Přijměte zbývající výchozí hodnoty a stisknutím klávesy ENTER ukončete instalační program.

5. Ověřte, že adresa URL pro OFGW funguje podle očekávání:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Zobrazí se následující obrazovka:

    ![WebTerminal OpenFrame](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Nainstalovat OFManager

OFManager poskytuje operace a funkce správy pro OpenFrame ve webovém prostředí.

**Instalace OFManager**

1. Ověřte, zda \_ je k dispozici soubor instalačního programu OFManager7 Generic. bin.
2. Spusťte instalační program. Příklad:

     ```
     OFManager7_Generic.bin
     ```

3.  Pokračujte stisknutím klávesy ENTER a pak přijměte licenční smlouvu.
4.  Vyberte složku pro instalaci.
5.  Přijměte výchozí hodnoty.
6.  Jako databázi vyberte Tibero.
7.  Stisknutím klávesy ENTER ukončíte instalační program.
8.  Ověřte, že adresa URL pro OFManager funguje podle očekávání:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Zobrazí se obrazovka Start:

![Přihlašovací obrazovka Tmax OpenFrame Manageru](media/ofmanager-01.png)

Tím se dokončí instalace součástí OpenFrame.

## <a name="next-steps"></a>Další kroky

Pokud zvažujete migraci v rámci sálového počítače, máte k dispozici náš rozšiřující partnerský ekosystém, který vám může pomáhat. Podrobné pokyny k výběru partnerského řešení najdete v tématu věnovaném nástroji pro [modernizaci platforem](https://datamigration.microsoft.com/).

-   [Začínáme s Azure](../../../../index.yml)
-   [Dokumentace k Host Integration Server (její)](/host-integration-server/)
-   [Průvodce načtením a posunutím virtuálního datového centra Azure](/archive/blogs/azurecat/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide)
