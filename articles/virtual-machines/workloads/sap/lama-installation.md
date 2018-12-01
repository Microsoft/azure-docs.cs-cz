---
title: Konektor SAP LaMa pro Azure | Dokumentace Microsoftu
description: Správa systémů SAP v Azure pomocí SAP LaMa
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 17/07/2018
ms.author: sedusch
ms.openlocfilehash: 2a0934fa3bb46eebba02029a8292b9bee6b12c62
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52728221"
---
# <a name="sap-lama-connector-for-azure"></a>Konektor SAP LaMa pro Azure

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Obecné podpoře: Vždy otevřete incident s řešením SAP na komponentě BC VCM LVM HYPERV Pokud potřebujete podporu pro SAP LaMa nebo konektoru služby Azure.

Mnoho zákazníků k provozu a monitorovat jejich prostředí SAP používá SAP LaMa. Od verze SAP LaMa 3.0 SP05 dodává s konektorem k Azure ve výchozím nastavení. Tento konektor můžete použít k přidělení a spuštění virtuálních počítačů, kopírovat a přemístění spravované disky a odstranit spravované disky. Tyto základní operace můžete přemístit, zkopírovat, klonování a aktualizovat systémů SAP pomocí SAP LaMa.

Tato příručka popisuje, jak nastavit Azure konektoru pro SAP LaMa, vytvořit virtuální počítače, které můžete použít k instalaci adaptivní systémů SAP a způsob jejich konfigurace.

> [!NOTE]
> Konektor je dostupná pouze v edici Enterprise LaMa SAP

## <a name="resources"></a>Zdroje a prostředky

Následující poznámky SAP se vztahují na téma o LaMa SAP v Azure:

| Poznámka: číslo | Titul |
| --- | --- |
| [2343511] |Konektor Microsoft Azure pro správu SAP na šířku (LaMa) |
| [2350235] |SAP Landscape Management 3.0 – Enterprise edition |

Přečíst také [portálu nápovědy SAP pro SAP LaMa](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Obecné poznámky

* Ujistěte se, že chcete povolit *automatické vytváření přípojný bod* v Nastavení -> Nastavení -> modul  
  Pokud SAP LaMa připojí svazky s využitím rozšíření adaptivní SAP na virtuálním počítači, musí existovat přípojného bodu, pokud toto nastavení není povolené.

* Použít samostatnou podsíť a použití dynamické IP adresy, aby se zabránilo IP nezabývá "zcizování", když nasazujete nové virtuální počítače a instance SAP neupravený  
  Pokud používáte dynamické přidělování IP adres v podsíti, která se používá také podle LaMa SAP, Příprava systému SAP s LaMa SAP může selhat. Pokud systém SAP není připraven, nejsou rezervované IP adresy a může načíst přidělené k jiným virtuálním počítačům.

* Pokud se přihlásíte na spravované hostitele, ujistěte se, že nedochází k blokování systémy souborů z právě odpojován  
  Přihlaste se k virtuální počítače s Linuxem a změnu pracovního adresáře do jiného adresáře v přípojný bod, třeba /usr/sap/AH1/ASCS00/exe, svazku nemůže být odpojeny a přemístit nebo unprepare – selže.

## <a name="set-up-azure-connector-for-sap-lama"></a>Nastavení konektoru služby Azure pro LaMa SAP

Konektor Azure je dodáván od SAP LaMa 3.0 SP05. Doporučujeme vždy nejprve nainstalovat nejnovější balíček pro podporu a opravy pro SAP LaMa 3.0. Konektor Azure používá instančního objektu k autorizaci s Microsoft Azure. Postupujte podle těchto kroků můžete vytvořit objekt služby pro správu SAP na šířku (LaMa).

1. Přejděte na https://portal.azure.com.
1. Otevře se okno Azure Active Directory
1. Klikněte na registrace aplikací
1. Klikněte na Přidat
1. Zadejte název, vyberte typ aplikace "Aplikace webového rozhraní API", zadejte přihlašovací adresu URL (například http://localhost) a klikněte na vytvořit
1. Adresa URL přihlašování se nepoužívá a může být jakákoliv platná adresa URL
1. Vyberte novou aplikaci a na kartě nastavení klikněte na tlačítko s klíči
1. Zadejte popis pro nový klíč, vyberte "Je platné stále" a klikněte na Uložit.
1. Poznamenejte si hodnotu. Používá se jako heslo pro instanční objekt
1. Poznamenejte si ID aplikace. Používá se jako uživatelské jméno pro instanční objekt služby

Instanční objekt služby nemá oprávnění pro přístup k prostředkům Azure ve výchozím nastavení. Je potřeba udělit oprávnění instančního objektu pro přístup k nim.

1. Přejděte na https://portal.azure.com.
1. Otevřete okno skupiny prostředků.
1. Vyberte skupinu prostředků, kterou chcete použít
1. Klikněte na řízení přístupu (IAM)
1. Klikněte na Přidat přiřazení role
1. Vyberte roli přispěvatele
1. Zadejte název aplikace, kterou jste vytvořili výše
1. Kliknutí na Uložit
1. Krok 3 až 8 opakujte pro všechny skupiny prostředků, kterou chcete použít v LaMa SAP

Otevřít web, který SAP LaMa a přejděte do infrastruktury. Přejděte na kartu Správce cloudu a klikněte na Přidat. Vyberte adaptér cloudu Microsoft Azure a klikněte na tlačítko Další. Zadejte následující informace:

* Popisek: Zvolte název pro instanci konektoru
* Uživatelské jméno: ID aplikace instančního objektu
* Heslo: Heslo klíče instanční objekt služby
* Adresa URL: Zachovat výchozí https://management.azure.com/
* Monitorování Interval (sekundy): By měl být alespoň 300
* ID předplatného: ID předplatného Azure
* Azure Active Directory ID Tenanta: ID tenanta Active Directory
* Proxy hostitele: název hostitele proxy serveru, pokud SAP LaMa potřebuje pro připojení k Internetu proxy server
* Port proxy serveru: TCP port proxy serveru

Klikněte na konfigurace testů a ověřit svůj vstup. Měli byste vidět

Úspěšné připojení: připojení k Microsoft cloud bylo úspěšné. Najít 7 skupiny prostředků (požadované jenom 10 skupin)

v dolní části tohoto webu.

## <a name="provision-a-new-adaptive-sap-system"></a>Zřízení nového adaptivní systému SAP

Ruční nasazení nového virtuálního počítače nebo použijte jednu z šablon Azure v [rychlém úložišti](https://github.com/Azure/azure-quickstart-templates). Obsahuje šablony pro [SAP NetWeaver ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), [aplikační servery SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)a [databáze](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-db). Tyto šablony můžete použít také ke zřízení nového hostitele jako součást kopírování/klonu systému atd.

Doporučujeme použít samostatnou podsíť pro všechny virtuální počítače, že chcete spravovat pomocí LaMa SAP a nepoužívejte dynamické IP adresy, aby se zabránilo IP adresa "zcizování" při nasazení nových virtuálních počítačů a instancí SAP neupravený.

> [!NOTE]
> Pokud je to možné odeberte všechna rozšíření virtuálních počítačů, protože by mohly způsobovat dlouho trvajícím spouštěním za účelem odpojení disku od virtuálního počítače.

Ujistěte se, že tento uživatel \<hanasid > adm, \<sapsid > adm a skupiny sapsys existovat v cílovém počítači se stejným ID a ID skupiny nebo pomocí protokolu LDAP. Povolení a spuštění serveru NFS na virtuálních počítačích, které se má použít pro spuštění SAP NetWeaver (A) SCS.

### <a name="manual-deployment"></a>Ruční nasazení

SAP LaMa komunikuje s virtuálním počítačem pomocí agenta hostitele SAP. Pokud nasadíte virtuální počítače ručně, nebo není pomocí šablony Azure Resource Manageru z úložiště rychlý start, ujistěte se, že k instalaci nejnovějšího agenta hostitele SAP a SAP adaptivní rozšíření. Další informace o úrovních požadované opravy pro Azure, viz poznámka SAP [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Ruční nasazení z virtuálního počítače s Linuxem

Vytvořit nový virtuální počítač s jedním z podporované operační systémy uvedené v Poznámka SAP [2343511]. Přidání dalších konfigurací protokolu IP pro instance SAP. Každá instance vyžaduje alespoň na IP adrese a musí být nainstalovaný pomocí virtuální název hostitele.

Instance SAP NetWeaver ASCS musí disky pro /sapmnt/\<SAPSID >, / USR/sap/\<SAPSID > a/usr/sap/trans/USR/sap/\<sapsid > adm. Aplikační servery SAP NetWeaver nepotřebují další disky. Opravdu všechno spojené s SAP instance musí být uložené na ASCS a exportuje přes systém souborů NFS. V opačném případě ho není možné přidat další aplikační servery SAP LaMa pomocí.

![SAP NetWeaver ASCS v Linuxu](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Ruční nasazení pro SAP HANA

Vytvořit nový virtuální počítač s jedním z podporované operační systémy pro SAP HANA, jak je uvedeno v Poznámka SAP [2343511]. Přidejte jednu další konfiguraci protokolu IP pro SAP HANA a jeden do každého tenanta HANA.

SAP HANA, musí mít disky /hana/shared, /hana/backup, /hana/data a /hana/log

![SAP HANA v Linuxu](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Ruční nasazení pro Oracle Database v Linuxu

Vytvořit nový virtuální počítač s jedním z podporované operační systémy pro databáze Oracle, jak je uvedeno v Poznámka SAP [2343511]. Přidáte jeden další konfiguraci IP adresy pro databázi Oracle.

Databáze Oracle vyžaduje disky pro /oracle /home/oraod1 a /home/oracle

![Oracle database v Linuxu](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Ruční nasazení pro systém Microsoft SQL Server

Vytvořit nový virtuální počítač s jedním z podporované operační systémy pro Microsoft SQL Server, jak je uvedeno v Poznámka SAP [2343511]. Přidáte jeden další konfiguraci IP adresy pro instanci systému SQL Server.

Databázový server SQL Server potřebuje pro dat z databáze a soubory protokolů a disků pro c:\usr\sap disky.

![Oracle database v Linuxu](media/lama/sap-lama-db-sql.png)

Ujistěte se, že k instalaci podporovaných ovladač Microsoft ODBC pro SQL Server na virtuálním počítači, který chcete použít k přesunutí aplikační server SAP NetWeaver nebo jako cíl kopírování nebo klonování systému.

SAP LaMa nelze přemístit samotného SQL serveru, takže musí virtuální počítač, který chcete použít k přesunutí do instance databáze nebo jako cíl kopírování nebo klonování systému SQL Server a předinstalovaným.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Nasazení virtuálního počítače pomocí šablony Azure

Stažení nejnovější k dispozici následující archivuje z [Marketplace softwaru SAP](https://support.sap.com/swdc) operačního systému virtuálních počítačů:

1. SAPCAR 7.21
1. AGENT HOSTITELE SAP 7.21
1. ADAPTIVNÍ ROZŠÍŘENÍ 1.0 EXT SAP

Také stáhnout z následující součásti [Microsoft Download Center](https://www.microsoft.com/download)

1. Microsoft Visual C++ 2010 Redistributable Package (x64) (jenom Windows)
1. Ovladač Microsoft ODBC pro SQL Server (pouze Server SQL)

Součásti jsou potřeba k nasazení šablony. Nejjednodušší způsob, jak zpřístupnit v šabloně je nahrát do účtu služby Azure storage a vytvořit sdílený přístupový podpis (SAS).

Šablony mají následující parametry:

* sapSystemId: ID The SAP systému. Používá se k vytvoření disku rozložení (například/USR/sap/\<sapsid >).

* computerName: název počítače na nový virtuální počítač. Tento parametr se používá také podle LaMa SAP. Při použití této šablony zřídit nový virtuální počítač jako součást kopie systému SAP LaMa počká, až se dá kontaktovat hostitele s tímto názvem počítače.

* osType: typ operačního systému, kterou chcete nasadit.

* Hodnota výčtu DbType: typ databáze. Tento parametr slouží k určení, kolik dalších konfigurací protokolu IP je potřeba přidat a jak by měl vypadat rozložení disků.

* sapSystemSize: velikost systému SAP, kterou chcete nasadit. Používá se k určení typu instance virtuálního počítače a velikosti.

* adminUsername: uživatelské jméno pro virtuální počítač.

* adminPassword: heslo pro virtuální počítač. Můžete také zadat veřejný klíč pro SSH.

* sshKeyData: klíč veřejné SSH pro virtuální počítače. Podporováno pouze pro operační systémy a Linux.

* subnetId: ID podsítě, kterou chcete použít.

* deployEmptyTarget: Pokud chcete využívat virtuální počítač jako cíl pro přemístit instance nebo podobné můžete nasadit prázdný cíl. V tomto případě jsou připojeny žádné další disky nebo konfigurace protokolu IP.

* sapcarLocation: nasazení umístění sapcar aplikaci, která odpovídá operačnímu systému. sapcar slouží k extrakci archivy, které zadáte v dalších parametrů.

* sapHostAgentArchiveLocation: umístění archivu Agent hostitele SAP. Agent hostitele SAP je nasazen jako součást nasazení této šablony.

* sapacExtLocation: umístění adaptivní rozšíření SAP. Poznámka SAP [2343511] uvádí minimální úrovně oprav vyžadované pro Azure.

* vcRedistLocation: umístění modulu Runtime jazyka VC, které jsou potřebné k instalaci rozšíření adaptivní SAP. Tento parametr je pouze vyžadované pro Windows.

* odbcDriverLocation: umístění ovladače ODBC, kterou chcete nainstalovat. Je podporován pouze ovladač Microsoft ODBC pro SQL Server.

* sapadmPassword: heslo pro uživatele sapadm.

* sapadmId: ID uživatele Linuxu sapadm uživatele. Nevyžaduje se pro Windows.

* sapsysGid: ID skupiny Linuxu sapsys skupiny. Nevyžaduje se pro Windows.

* _artifactsLocation: základní identifikátor URI, kde se nachází artefakty, které vyžadují tuto šablonu. Při nasazení šablony pomocí doprovodných skriptů použije privátního umístění v rámci předplatného a tato hodnota se automaticky vygeneruje. Potřeba pouze v případě nenasazujte šablony z Githubu.

* _artifactsLocationSasToken: sasToken vyžadovaných pro přístup k _artifactsLocation. Při nasazení šablony pomocí doprovodných skriptů sasToken budou automaticky generovány. Potřeba pouze v případě nenasazujte šablony z Githubu.

### <a name="sap-hana"></a>SAP HANA

V následujících příkladech předpokládáme instalace SAP HANA s ID HN1 systému a systém SAP NetWeaver v systému ID AH1. Názvy virtuálních hostitelů jsou hn1 db pro instance HANA ah1 db pro HANA tenanta používá systém SAP NetWeaver, ah1 ascs pro SAP NetWeaver ASCS a ah1-di-0 pro první aplikační server SAP Netweaveru.

#### <a name="install-sap-netweaver-ascs-for-sap-hana"></a>Instalace SAP NetWeaver ASCS pro SAP HANA

Před zahájením SAP Software zřizování správce (SWPM), budete muset připojit IP adresu virtuální název hostitele ASC. Doporučený postup je použití sapacext. Pokud připojíte pomocí sapacext adresu IP, ujistěte se, že po restartování znovu připojit IP adresu.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

Spusťte SWPM a použijte *ah1 ascs* pro *název hostitele Instance ASCS*.

![Linux][Logo_Linux] Linux  
Profil agenta hostitele SAP, které se nacházejí v /usr/sap/hostctrl/exe/host_profile přidejte následující parametr profilu. Další informace viz poznámka SAP [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-hana"></a>Instalace SAP HANA

Pokud nainstalujete SAP HANA pomocí hdblcm nástroj příkazového řádku, pomocí parametru--název hostitele zadejte virtuální název hostitele. Budete muset přidat IP adresu virtuální název hostitele databáze do síťové rozhraní. Doporučený postup je použití sapacext. Pokud připojíte pomocí sapacext adresu IP, ujistěte se, že po restartování znovu připojit IP adresu.

Přidejte další název virtuálního hostitele a IP adresa pro název, který používá aplikační servery pro připojení k HANA tenanta.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Spusťte instalaci instance databáze SWPM na virtuální počítač pro server aplikace, nikoli na virtuální počítač HANA. Použití *ah1 db* pro *hostitele databáze* v dialogovém okně *databáze systému SAP*.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Instalace aplikační Server SAP NetWeaver pro SAP HANA

Než začnete SAP Software zřizování správce (SWPM), budete muset připojit IP adresu virtuální název hostitele serveru aplikace. Doporučený postup je použití sapacext. Pokud připojíte pomocí sapacext adresu IP, ujistěte se, že po restartování znovu připojit IP adresu.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

Doporučujeme použít SAP NetWeaver profilu parametr databází/hdb/hdb_use_ident nastavit identitu, která se používá k vyhledání klíč v objektu userstore HDB. Můžete ručně přidat tento parametr po instalaci instance databáze s SWPM nebo spustit SWPM s

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Pokud ji nastavíte ručně, musíte také vytvořit nové položky HDB objektu userstore.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Použití *ah1-di-0* pro *název hostitele Instance Pa adresy* v dialogovém okně *primární instanci serveru aplikace*.

#### <a name="post-installation-steps-for-sap-hana"></a>Kroky po instalaci pro SAP HANA

Ujistěte se, že pro zálohování SYSTEMDB a všech databázích tenantů před pokusem o kopii tenanta, tenanta přesunout nebo vytvořit systémové replikace.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

V následujících příkladech předpokládáme instalace systému SAP NetWeaver systémem ID AS1. Názvy virtuálních hostitelů jsou as1 db pro instanci systému SQL Server používá systém SAP NetWeaver, as1 ascs pro SAP NetWeaver ASCS a as1-di-0 pro první aplikační server SAP Netweaveru.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Instalace SAP NetWeaver ASCS pro SQL Server

Před zahájením SAP Software zřizování správce (SWPM), budete muset připojit IP adresu virtuální název hostitele ASC. Doporučený postup je použití sapacext. Pokud připojíte pomocí sapacext adresu IP, ujistěte se, že po restartování znovu připojit IP adresu.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Spusťte SWPM a použijte *as1 ascs* pro *název hostitele Instance ASCS*.

#### <a name="install-sql-server"></a>Instalace systému SQL Server

Budete muset přidat IP adresu virtuální název hostitele databáze do síťové rozhraní. Doporučený postup je použití sapacext. Pokud připojíte pomocí sapacext adresu IP, ujistěte se, že po restartování znovu připojit IP adresu.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Spusťte instalaci instance databáze SWPM na virtuálním počítači SQL serveru. Použít SAPINST_USE_HOSTNAME =*as1 db* přepsání název hostitele pro připojení k serveru SQL Server. Pokud jste nasadili virtuální počítač pomocí šablony Azure Resource Manageru, je nutné nastavit adresář použít pro datové soubory databáze do *C:\sql\data* a soubor protokolu databáze do *C:\sql\log*.

Ujistěte se, že uživatel *NT AUTHORITY\SYSTEM* má přístup k SQL serveru a role serveru *sysadmin*. Další informace viz poznámka SAP [1877727] a [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Instalace aplikační Server SAP NetWeaver

Než začnete SAP Software zřizování správce (SWPM), budete muset připojit IP adresu virtuální název hostitele serveru aplikace. Doporučený postup je použití sapacext. Pokud připojíte pomocí sapacext adresu IP, ujistěte se, že po restartování znovu připojit IP adresu.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Použití *as1-di-0* pro *název hostitele Instance Pa adresy* v dialogovém okně *primární instanci serveru aplikace*.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="errors-and-warnings-during-discover"></a>Chyby a upozornění během zjišťování

* Vyberte oprávnění bylo odepřeno
  * [Microsoft] [Ovladač ODBC SQL Server] [SQL Server] Vyberte oprávnění bylo odepřeno na objekt log_shipping_primary_databases, databázi msdb, schématu dbo. [SOAPFaultException]  
  Vyberte oprávnění bylo odepřeno na objekt log_shipping_primary_databases, databázi msdb, schématu dbo.
  * Řešení  
    Ujistěte se, že *NT AUTHORITY\SYSTEM* má přístup k SQL serveru. Viz poznámka SAP [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Chyby a upozornění pro instanci ověření

* Došlo k výjimce v ověřování HDB úložiště userstore  
  * Zobrazit Log Vieweru.  
    com.sap.nw.lm.aci.monitor.api.validation.RuntimeValidationException: výjimka ve validátoru s ID "RuntimeHDBConnectionValidator" (ověření: "VALIDATION_HDB_USERSTORE"): nepovedlo se načíst hdbuserstore  
    Úložiště userstore HANA není ve správném umístění
  * Řešení  
    Ujistěte se, že tento /usr/sap/AH1/hdbclient/install/installation.ini je správný

### <a name="errors-and-warnings-during-a-system-copy"></a>Chyby a upozornění během kopie systému

* Došlo k chybě při ověřování systému zřizování kroku
  * Způsobené: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException volání "/ usr/sap/hostctrl/exe/sapacext - a ShowHanaBackups -m HN1 -f 50 - h hn1-db - o úroveň = 0\;stav = 5\;port = 35013 pf = / usr/sap/hostctrl / exe/host_profile -R -u -T dev_lvminfo SYSTEM -p zavěšení - r. | /USR/SAP/hostctrl/exe/sapacext - a ShowHanaBackups -m 50 - h -f HN1 hn1-db - o úroveň = 0\;stav = 5\;port = 35013 pf = / usr/sap/hostctrl/exe/host_profile - R -u -T dev_lvminfo SYSTEM -p zavěšení - r
  * Řešení  
    Vytvořit zálohu všech databází ve zdroji systém HANA

* Krok kopie systému *Start* instance databáze
  * Operace agenta hostitele '000D3A282BC91EE8A1D76CF1F92E2944' se nezdařila (OperationException. FaultCode: "127", zpráva: "spuštění příkazu se nezdařilo. : [Microsoft] [SQL Server ovladače ODBC] [SQL Server] uživatel nemá oprávnění změnit databázi 'AS2', databáze neexistuje nebo databáze není ve stavu umožňujícím kontroly přístupu. ")
  * Řešení  
    Ujistěte se, že *NT AUTHORITY\SYSTEM* má přístup k SQL serveru. Viz poznámka SAP [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Chyby a upozornění během klonování systému

* Došlo k chybě při pokusu o registraci instance agenta v kroku *vynucené registrace a spuštění agenta Instance* aplikačního serveru nebo ASC
  * Při pokusu o registraci instance agenta došlo k chybě. (RemoteException: "Nepovedlo se načíst instance data z profilu"\\as1 ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1 di 0': Nelze získat přístup k profilu "\\as1 ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1 di 0': žádný odpovídající soubor nebo adresář.")
  * Řešení  
   Ujistěte se, že sapmnt sdílené složky na ASCS/SCS má plný přístup pro SAP_AS1_GlobalAdmin

* Chyba v kroku *povolení ochrany po spuštění pro klon*
  * Nepovedlo se otevřít soubor "\\as1 ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1 di 0' příčina: žádný odpovídající soubor nebo adresář
  * Řešení  
    Účet počítače serveru aplikace potřebuje oprávnění k zápisu do profilu

### <a name="errors-and-warnings-during-create-system-replication"></a>Chyby a upozornění při vytváření systémové replikace

* Výjimka při kliknutí na vytvořit systémové replikace
  * Způsobené: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException volání "/ usr/sap/hostctrl/exe/sapacext - a ShowHanaBackups -m HN1 -f 50 - h hn1-db - o úroveň = 0\;stav = 5\;port = 35013 pf = / usr/sap/hostctrl / exe/host_profile -R -u -T dev_lvminfo SYSTEM -p zavěšení - r. | /USR/SAP/hostctrl/exe/sapacext - a ShowHanaBackups -m 50 - h -f HN1 hn1-db - o úroveň = 0\;stav = 5\;port = 35013 pf = / usr/sap/hostctrl/exe/host_profile - R -u -T dev_lvminfo SYSTEM -p zavěšení - r
  * Řešení  
    Test Pokud sapacext můžete provést, protože `<hanasid`> adm

* Chyba při úplné kopie není povolena v kroku úložiště
  * Došlo k chybě při hlášení zprávu atribut kontextu pro cestu IStorageCopyData.storageVolumeCopyList:1 a targetStorageSystemId pole
  * Řešení  
    Ignorovat upozornění v kroku a zkuste to znovu. Tento problém bude opraven v nové podpory balíčku nebo opravu z LaMa SAP.

### <a name="errors-and-warnings-during-relocate"></a>Chyby a upozornění během přemístit

* Cesta "/ usr/sap/AH1" není povolena pro reexports systému souborů nfs.
  * Zkontrolujte poznámky SAP [2628497] podrobnosti.
  * Řešení  
    Přidáte že ASCS exportuje ASCS HostAgent profilu. Viz poznámka SAP [2628497]

* Není implementováno po přemístění ASCS – funkce
  * Výstup příkazu: exportfs: hostitele: / usr/sap/AX1: funkce není implementována
  * Řešení  
    Ujistěte se, že je na cílovém virtuálním počítači přemístit povolená služba server systému souborů NFS

### <a name="errors-and-warnings-during-application-server-installation"></a>Chyby a upozornění během instalace serveru aplikace

* Chyba při provádění kroku SAPinst: getProfileDir
  * Chyba: (poslední chybu v kroku: byla zachycena ESAPinstException ve volání modulu: validátoru krok ' | NW_DI | ajít | ajít | ajít | ajít | 0 | 0 | NW_GetSidFromProfiles | ajít | ajít | ajít | ajít | getSid | 0 | NW_readProfileDir | ajít | ajít | ajít | ajít | readProfile | 0 | getProfileDir "oznámil chybu: uzel \\\as1-ascs\sapmnt\AS1\SYS\profile neexistuje. Spusťte v interaktivním režimu pro vyřešení tohoto problému SAPinst)
  * Řešení  
    Ujistěte se, že SWPM je spuštěn jako uživatel, který má přístup k profilu. Dá se tento uživatel v Průvodci instalací serveru aplikace

* Chyba při provádění kroku SAPinst: askUnicode
  * Chyba: (poslední chybu v kroku: byla zachycena ESAPinstException ve volání modulu: validátoru krok ' | NW_DI | ajít | ajít | ajít | ajít | 0 | 0 | NW_GetSidFromProfiles | ajít | ajít | ajít | ajít | getSid | 0 | NW_getUnicode | ajít | ajít | ajít | ajít | unicode | 0 | askUnicode "oznámil chybu: SAPinst spusťte v interaktivním režimu pro vyřešení tohoto problému)
  * Řešení  
    Pokud používáte novější jádro SAP, SWPM nelze určit, zda je systém unicode systému už server zpráv ascs. Viz poznámka SAP [2445033] další podrobnosti.  
    Tento problém bude opraven v nové podpory balíčku nebo opravu z LaMa SAP.  
    Nastavte profil parametr OS_UNICODE = uc ve výchozím profilu systému SAP a chcete tento problém vyřešit.

* Chyba při provádění kroku SAPinst: dCheckGivenServer
  * Chyba při provádění kroku SAPinst: dCheckGivenServer "verze ="1.0"Chyba: (poslední Chyba hlášená krok: \<p > Instalace byla zrušena uživatelem. \</p >
  * Řešení  
    Ujistěte se, že SWPM je spuštěn jako uživatel, který má přístup k profilu. Dá se tento uživatel v Průvodci instalací serveru aplikace

* Chyba při provádění kroku SAPinst: checkClient
  * Chyba při provádění kroku SAPinst: checkClient "verze ="1.0"Chyba: (poslední Chyba hlášená krok: \<p > Instalace byla zrušena uživatelem. \</p >)
  * Řešení  
    Ujistěte se, že ovladač Microsoft ODBC pro SQL Server je nainstalovaný na virtuálním počítači, na kterém chcete nainstalovat aplikační server

* Chyba při provádění kroku SAPinst: copyScripts
  * Poslední krok Hlášená chyba: volání systému se nezdařilo. Podrobnosti: Chyba 13 (0x0000000d) (oprávnění odepřeno) při provádění systému volání 'fopenU"s parametrem (\\\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w), řádek (494) v souboru (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/ src/syslib/FileSystem/syxxcfstrm2.cpp), trasování zásobníku:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect::callMemberFunction (iastring const & název, args_t const & args)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect::newFileStream (args_t const & _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp: 233: CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 29: CSyFileStreamImpl::CSyFileStreamImpl(CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 265: CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp: 58: CSyFileStream2Impl::CSyFileStream2Impl (const CSyPath & \\\aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd 0x4)  
  syxxcfstrm2.cpp: 456: CSyFileStream2Impl::open()
  * Řešení  
    Ujistěte se, že SWPM je spuštěn jako uživatel, který má přístup k profilu. Dá se tento uživatel v Průvodci instalací serveru aplikace

* Chyba při provádění kroku SAPinst: askPasswords
  * Poslední krok Hlášená chyba: volání systému se nezdařilo. Podrobnosti: Chyba 5 (0x00000005) (přístup byl odepřen.) při provádění volání systému "NetValidatePasswordPolicy" s parametrem (...), řádek (359) v souboru (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), trasování zásobníku:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  CSiStepExecute.cpp: 764: CSiStepExecute::invokeDialog()  
  DarkModeGuiEngine.cpp: 56: DarkModeGuiEngine::showDialogCalledByJs()  
  DarkModeDialog.cpp: 85: DarkModeDialog::submit()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcaccount.cpp: 107: iastring CIaOsAccountConnect::callMemberFunction (iastring const & název, args_t const & args)  
  iaxxcaccount.cpp: 1186: iastring CIaOsAccountConnect::validatePasswordPolicy (args_t const & _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp: 297: ISyAccountMgt::PasswordValidationMessage CSyAccountMgtImpl::validatePasswordPolicy(saponazure,***) const)
  * Řešení  
    Ujistěte se, že přidáte pravidlo hostitele v kroku *izolace* umožňující navázat komunikaci z virtuálního počítače pro řadič domény

## <a name="next-steps"></a>Další postup
* [SAP HANA v Azure Provozní příručka][hana-ops-guide]
* [Azure Virtual Machines, plánování a implementace SAP][planning-guide]
* [Nasazení virtuálních počítačů pro SAP v Azure][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]