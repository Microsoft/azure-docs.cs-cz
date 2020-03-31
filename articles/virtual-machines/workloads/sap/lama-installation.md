---
title: Konektor SAP LaMa pro Azure | Dokumenty společnosti Microsoft
description: Správa SAP Systems v Azure pomocí SAP LaMa
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: sedusch
ms.openlocfilehash: fda62ff0af29c7cf681d9438b02420d299535701
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293951"
---
# <a name="sap-lama-connector-for-azure"></a>Konektor SAP LaMa pro Azure

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[2815988]:https://launchpad.support.sap.com/#/notes/2815988
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Obecné prohlášení o podpoře: Pokud potřebujete podporu pro SAP LaMa nebo konektor Azure, vždy otevřete incident se systémem SAP na komponentě BC-VCM-LVM-HYPERV.

SAP LaMa je používán mnoha zákazníky k provozu a sledování jejich SAP krajiny. Od SAP LaMa 3.0 SP05, dodává se s konektorem do Azure ve výchozím nastavení. Tento konektor můžete použít k navrátit a spustit virtuální počítače, kopírovat a přemístit spravované disky a odstranit spravované disky. Pomocí těchto základních operací můžete přemístit, zkopírovat, klonovat a aktualizovat systémy SAP pomocí SAP LaMa.

Tato příručka popisuje, jak nastavit konektor Azure pro SAP LaMa, vytvořit virtuální počítače, které lze použít k instalaci adaptivních systémů SAP a jak je nakonfigurovat.

> [!NOTE]
> Konektor je k dispozici pouze v sap LaMa Enterprise Edition

## <a name="resources"></a>Prostředky

Následující poznámky SAP se vztahují k tématu SAP LaMa v Azure:

| Číslo poznámky | Nadpis |
| --- | --- |
| [2343511] |Konektor Microsoft Azure pro sap správu krajiny (LaMa) |
| [2350235] |SAP Landscape Management 3.0 - Enterprise edition |

Přečtěte si také [portál nápovědy SAP pro SAP LaMa](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Obecné poznámky

* Ujistěte se, že v nastavení -> Nastavení -> Engine povolit *automatické vytváření přípojného bodu*  
  Pokud SAP LaMa připojí svazky pomocí adaptivních rozšíření SAP na virtuálním počítači, musí přípojný bod existovat, pokud toto nastavení není povoleno.

* Použijte samostatnou podsíť a nepoužívejte dynamické IP adresy, abyste zabránili krádeži IP adresy při nasazování nových virtuálních počítačů a instance SAP nejsou připravené  
  Pokud používáte dynamické přidělení IP adres v podsíti, která je také používána SAP LaMa, může selhat příprava systému SAP s SAP LaMa. Pokud systém SAP není připraven, IP adresy nejsou vyhrazeny a mohou být přiděleny jiným virtuálním počítačům.

* Pokud se přihlásíte ke spravovaným hostitelům, neblokujte odpojení systémů souborů.  
  Pokud se přihlásíte k virtuálním počítačům Linux a změníte pracovní adresář do adresáře v přípojném bodě, například /usr/sap/AH1/ASCS00/exe, svazek nelze odpojit a přemístění nebo nepřipravení se nezdaří.

* Ujistěte se, že zakázat CLOUD_NETCONFIG_MANAGE na virtuálních počítačích SUSE SLES Linux. Další podrobnosti naleznete [v tématu SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633).

## <a name="set-up-azure-connector-for-sap-lama"></a>Nastavení konektoru Azure pro SAP LaMa

Konektor Azure je dodáván od SAP LaMa 3.0 SP05. Doporučujeme vždy nainstalovat nejnovější balíček podpory a opravu pro SAP LaMa 3.0.

Konektor Azure používá rozhraní API Azure Resource Manager ke správě prostředků Azure. SAP LaMa můžete použít instanční objekt nebo spravované identity k ověření proti tomuto rozhraní API. Pokud váš SAP LaMa běží na virtuálním počítači Azure, doporučujeme použít spravovanou identitu, jak je popsáno v kapitole [Použití spravované identity k získání přístupu k rozhraní API Azure](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d). Pokud chcete použít instanční objekt, postupujte podle kroků v kapitole [Použití instančního objektu pro získání přístupu k rozhraní AZURE API](lama-installation.md#913c222a-3754-487f-9c89-983c82da641e).

### <a name="use-a-service-principal-to-get-access-to-the-azure-api"></a><a name="913c222a-3754-487f-9c89-983c82da641e"></a>Použití instančního objektu k získání přístupu k rozhraní Azure API

Konektor Azure můžete použít instanční objekt k autorizaci proti Microsoft Azure. Následujícím postupem vytvořte instanční objekt pro sap landscape management (LaMa).

1. Přejděte na https://portal.azure.com.
1. Otevření okna Služby Azure Active Directory
1. Klikněte na registrace aplikací
1. Klikněte na Novou registraci
1. Zadejte jméno a klikněte na Registrovat
1. Vyberte novou aplikaci a klikněte na certifikáty & tajných kódů na kartě Nastavení
1. Vytvořte nový tajný klíč klienta, zadejte popis nového klíče, vyberte, kdy má platnost tajného klíče vypršet, a klikněte na Uložit
1. Poznamenejte si hodnotu. Používá se jako heslo pro instanční objekt
1. Poznamenejte si ID aplikace. Používá se jako uživatelské jméno instančního objektu

Instanční objekt nemá oprávnění k přístupu k prostředkům Azure ve výchozím nastavení. Je třeba udělit instanční ho oprávnění pro přístup k nim.

1. Přejděte na https://portal.azure.com.
1. Otevření okna Skupiny prostředků
1. Vyberte skupinu prostředků, kterou chcete použít.
1. Klikněte na ovládací prvek přístupu (IAM)
1. Klikněte na Přidat přiřazení role
1. Vyberte přispěvatele role
1. Zadejte název aplikace, kterou jste vytvořili výše.
1. Kliknutí na Uložit
1. Opakujte krok 3 až 8 pro všechny skupiny prostředků, které chcete použít v SAP LaMa.

### <a name="use-a-managed-identity-to-get-access-to-the-azure-api"></a><a name="af65832e-6469-4d69-9db5-0ed09eac126d"></a>Použití spravované identity k získání přístupu k rozhraní Azure API

Aby bylo možné používat spravovanou identitu, musí vaše instance SAP LaMa běžet na virtuálním počítači Azure, který má identitu přiřazenou systému nebo uživateli. Další informace o spravovaných identitách načtou [co je spravované identity pro prostředky Azure?](../../../active-directory/managed-identities-azure-resources/overview.md) a [Nakonfigurujte spravované identity pro prostředky Azure na virtuálním počítači pomocí portálu Azure](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md).

Spravovaná identita nemá ve výchozím nastavení oprávnění k přístupu k vašim prostředkům Azure. Musíte mu udělit oprávnění k přístupu k nim.

1. Přejděte na https://portal.azure.com.
1. Otevření okna Skupiny prostředků
1. Vyberte skupinu prostředků, kterou chcete použít.
1. Klikněte na ovládací prvek přístupu (IAM)
1. Klikněte na Přidat -> Přiřazení role
1. Vyberte přispěvatele role
1. Vyberte možnost Virtuální počítač pro možnost Přiřadit přístup k aplikaci .
1. Vyberte virtuální počítač, na kterém běží vaše instance SAP LaMa.
1. Kliknutí na Uložit
1. Opakování kroků pro všechny skupiny prostředků, které chcete použít v SAP LaMa

V konfiguraci konektoru SAP LaMa Azure vyberte možnost Použít spravovanou identitu, abyste povolili využití spravované identity. Pokud chcete použít systém přiřazenou identitu, ponechte pole Uživatelské jméno prázdné. Pokud chcete použít identitu přiřazenou uživateli, zadejte ID identity přiřazené uživateli do pole Uživatelské jméno.

### <a name="create-a-new-connector-in-sap-lama"></a>Vytvoření nového konektoru v SAP LaMa

Otevřete web SAP LaMa a přejděte na Infrastrukturu. Přejděte na kartu Cloud Managers a klikněte na Přidat. Vyberte cloudový adaptér Microsoft Azure a klikněte na Další. Zadejte následující informace:

* Popisek: Zvolte název instance konektoru.
* Uživatelské jméno: ID aplikace instančního objektu nebo ID uživatele přiřazené identity virtuálního počítače. Další informace naleznete v tématu [Použití systémové nebo přiřazené identity uživatelem]
* Heslo: Hlavní klíč/heslo služby. Toto pole můžete ponechat prázdné, pokud používáte systémovou nebo uživatelskou identitu přiřazenou.
* Adresa URL: Zachovat výchozí`https://management.azure.com/`
* Interval sledování (v sekundách): Mělo by být alespoň 300
* Použití spravované identity: SAP LaMa může k ověření pomocí identity přiřazené systému nebo uživateli použít k ověření podle rozhraní AZURE API. Viz kapitola [Použití spravované identity k získání přístupu k rozhraní Azure API](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d) v této příručce.
* ID předplatného: ID předplatného Azure
* ID klienta Služby Azure active Directory: ID klienta služby Active Directory
* Proxy host: Hostname proxy, pokud SAP LaMa potřebuje proxy pro připojení k internetu
* Port proxy: Port TCP proxy
* Změnit typ úložiště pro úsporu nákladů: Toto nastavení povolte, pokud by měl adaptér Azure změnit typ úložiště spravovaných disků, aby se ušetřily náklady, když se disky nepoužívají. U datových disků, na které se odkazuje v konfiguraci instance SAP, adaptér změní typ disku na standardní úložiště během nepřípravy instance a zpět na původní typ úložiště během přípravy instance. Pokud zastavíte virtuální počítač v SAP LaMa, adaptér změní typ úložiště všech připojených disků, včetně disku operačního systému, na standardní úložiště. Pokud spustíte virtuální počítač v SAP LaMa, adaptér změní typ úložiště zpět na původní typ úložiště.

Klikněte na Test Configuration pro ověření vašeho vstupu. Měli byste vidět

Připojení úspěšné: Připojení ke cloudu Microsoftu bylo úspěšné. Bylo nalezeno 7 skupin prostředků (bylo požadováno pouze 10 skupin)

v dolní části webových stránek.

## <a name="provision-a-new-adaptive-sap-system"></a>Zřízení nového adaptivního systému SAP

Můžete ručně nasadit nový virtuální počítač nebo použít jednu ze šablon Azure v [úložišti rychlého startu](https://github.com/Azure/azure-quickstart-templates). Obsahuje šablony pro [SAP NetWeaver ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), [aplikační servery SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)a [databázi](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). Tyto šablony můžete také použít k zřízení nových hostitelů jako součást systémové kopie/klonování atd.

Doporučujeme používat samostatnou podsíť pro všechny virtuální počítače, které chcete spravovat pomocí SAP LaMa a nepoužívejte dynamické IP adresy, abyste zabránili krádeži IP adresy při nasazování nových virtuálních počítačů a instance SAP nejsou připravené.

> [!NOTE]
> Pokud je to možné, odeberte všechna rozšíření virtuálních počítačů, protože mohou způsobit dlouhé runtimes pro odpojení disků od virtuálního počítače.

Ujistěte se, že uživatel \<hanasid>adm, \<sapsid>adm a skupiny sapsys existují na cílovém počítači se stejným ID a gid nebo použít LDAP. Povolte a spusťte server systému nfs na virtuálních počítačích, které by měly být použity ke spuštění SAP NetWeaver (A)SCS.

### <a name="manual-deployment"></a>Ruční nasazení

SAP LaMa komunikuje s virtuálním počítačem pomocí sap host agenta. Pokud nasadíte virtuální počítače ručně nebo nepoužíváte šablonu Azure Resource Manager z úložiště rychlého startu, nezapomeňte nainstalovat nejnovější ho agenta hostitele SAP a adaptivní rozšíření SAP. Další informace o požadovaných úrovních oprav pro Azure najdete v tématu SAP Note [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Ruční nasazení virtuálního počítače s Linuxem

Vytvořte nový virtuální počítač s jedním z podporovaných operačních systémů uvedených v poznámce SAP [2343511]. Přidejte další konfigurace IP adres pro instance SAP. Každá instance potřebuje alespoň na IP adresu a musí být nainstalována pomocí virtuálního názvu hostitele.

Instance SAP NetWeaver ASCS potřebuje disky pro\</sapmnt/ SAPSID\<>, /usr/sap/ SAPSID>, /usr/sap/trans a /usr/sap/\<sapsid>adm. Aplikační servery SAP NetWeaver nepotřebují další disky. Vše, co souvisí s instancí SAP, musí být uloženo v ASCS a exportováno přes NFS. V opačném případě není v současné době možné přidat další aplikační servery pomocí SAP LaMa.

![SAP NetWeaver ASCS na Linuxu](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Ruční nasazení pro SAP HANA

Vytvořte nový virtuální počítač s jedním z podporovaných operačních systémů pro SAP HANA, jak je uvedeno v poznámce SAP [2343511]. Přidejte jednu další konfiguraci IP pro SAP HANA a jednu na klienta HANA.

SAP HANA potřebuje disky pro /hana/shared, /hana/backup, /hana/data a /hana/log

![SAP HANA na Linuxu](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Ruční nasazení pro databázi Oracle database na Linuxu

Vytvořte nový virtuální počítač s jedním z podporovaných operačních systémů pro databáze Oracle, jak je uvedeno v poznámce SAP [2343511]. Přidejte jednu další konfiguraci IP adresy pro databázi Oracle.

Databáze Oracle potřebuje disky pro /oracle, /home/oraod1 a /home/oracle

![Databáze Oracle na Linuxu](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Ruční nasazení pro Microsoft SQL Server

Vytvořte nový virtuální počítač s jedním z podporovaných operačních systémů pro Microsoft SQL Server, jak je uvedeno v poznámce SAP [2343511]. Přidejte jednu další konfiguraci IP pro instanci serveru SQL Server.

Databázový server SERVERU SQL Server potřebuje disky pro data databáze a soubory protokolu a disky pro c:\usr\sap.

![Databáze Oracle na Linuxu](media/lama/sap-lama-db-sql.png)

Nezapomeňte nainstalovat podporovaný ovladač Microsoft ODBC pro SQL Server do virtuálního počítače, který chcete použít k přemístění aplikačního serveru SAP NetWeaver na cíl systémové kopie/klonování.

SAP LaMa nelze přemístit SQL Server sám tak virtuální počítač, který chcete použít k přemístění instance databáze nebo jako cíl kopie/klonování systému potřebuje SQL Server předinstalován.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Nasazení virtuálního počítače pomocí šablony Azure

Stáhněte si následující nejnovější dostupné archivy z [sap software marketplace](https://support.sap.com/swdc) pro operační systém virtuálních počítačů:

1. SAPCAR 7,21
1. HOSTITELSKÝ AGENT SAP 7.21
1. SAP ADAPTIVNÍ ROZŠÍŘENÍ 1,0 EXT

Stáhněte si také následující součásti ze služby [Stažení softwaru](https://www.microsoft.com/download)

1. Redistribuovatelný balíček Microsoft Visual C++ 2010 (x64) (pouze systém Windows)
1. Ovladač OdBC společnosti Microsoft pro SQL Server (pouze sql server)

Součásti jsou nutné k nasazení šablony. Nejjednodušší způsob, jak je zpřístupnit šabloně, je nahrát je do účtu úložiště Azure a vytvořit sdílený přístupový podpis (SAS).

Šablony mají následující parametry:

* sapSystemId: ID systému SAP. Používá se k vytvoření rozložení disku (například /usr/sap/\<sapsid>).

* Název_počítače: Název počítače nového virtuálního počítače. Tento parametr používá také SAP LaMa. Při použití této šablony zřídit nový virtuální počítač jako součást systémové kopie SAP LaMa čeká, dokud hostitel s tímto názvem počítače je k zastižení.

* osType: Typ operačního systému, který chcete nasadit.

* dbtype: Typ databáze. Tento parametr slouží k určení, kolik dalších konfigurací protokolu IP je třeba přidat a jak by mělo vypadat rozložení disku.

* sapSystemSize: Velikost systému SAP, který chcete nasadit. Používá se k určení typu a velikosti instance virtuálního počítače.

* adminUsername: Uživatelské jméno pro virtuální počítač.

* adminPassword: Heslo pro virtuální počítač. Můžete také zadat veřejný klíč pro SSH.

* sshKeyData: Veřejný klíč SSH pro virtuální počítače. Podporováno pouze pro operační systémy Linux.

* Podsíť Id: ID podsítě, kterou chcete použít.

* deployEmptyTarget: Můžete nasadit prázdný cíl, pokud chcete použít virtuální počítač jako cíl pro přemístit instance nebo podobné. V tomto případě nejsou připojeny žádné další disky nebo konfigurace IP.

* sapcarLocation: Umístění aplikace sapcar, která odpovídá operačnímu systému, který nasadíte. sapcar se používá k extrahování archivů, které zadáte v jiných parametrech.

* sapHostAgentArchiveLocation: Umístění archivu hostitele SAP. Sap Host Agent je nasazen jako součást tohoto nasazení šablony.

* sapacExtLocation: Umístění adaptivních rozšíření SAP. SAP Note [2343511] uvádí minimální úroveň opravy požadovanou pro Azure.

* vcRedistLocation: Umístění modulu Runtime VC, který je nutný k instalaci adaptivních rozšíření SAP. Tento parametr je vyžadován pouze pro systém Windows.

* odbcDriverLocation: Umístění ovladače ODBC, který chcete nainstalovat. Je podporován pouze ovladač Microsoft ODBC pro SQL Server.

* sapadmPassword: Heslo pro uživatele sapadm.

* sapadmId: Linux User ID uživatele sapadm. Není vyžadováno pro systém Windows.

* sapsysGid: Linux group ID skupiny sapsys. Není vyžadováno pro systém Windows.

* _artifactsLocation: Základní identifikátor URI, kde jsou umístěny artefakty vyžadované touto šablonou. Při nasazení šablony pomocí doprovodných skriptů bude použito soukromé umístění v předplatném a tato hodnota bude automaticky generována. Je potřeba pouze v případě, že šablonu nenasazujete z GitHubu.

* _artifactsLocationSasToken: SasToken potřebné pro přístup k _artifactsLocation. Při nasazení šablony pomocí doprovodných skriptů bude automaticky vygenerován sasToken. Je potřeba pouze v případě, že šablonu nenasazujete z GitHubu.

### <a name="sap-hana"></a>SAP HANA

V níže uvedených příkladech předpokládáme, že nainstalujete SAP HANA s ID systému HN1 a systém SAP NetWeaver se systémovým ID AH1. Názvy virtuálních hostitelů jsou hn1-db pro instanci HANA, ah1-db pro tenanta HANA používaného systémem SAP NetWeaver, ah1-ascs pro SAP NetWeaver ASCS a ah1-di-0 pro první aplikační server SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>Instalace SAP NetWeaver ASCS pro SAP HANA pomocí spravovaných disků Azure

Před spuštěním SAP Software Provisioning Manager (SWPM) je třeba připojit IP adresu virtuálního názvu hostitele ASCS. Doporučeným způsobem je použití sapacext. Pokud připojíte IP adresu pomocí sapacext, ujistěte se, že znovu připojit IP adresu po restartu.

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

Spusťte SWPM a použijte *ah1-ascs* pro *název hostitele instance ASCS*.

![Linux][Logo_Linux] Linux  
Přidejte následující parametr profilu do profilu agenta hostitele SAP, který je umístěn na adrese /usr/sap/hostctrl/exe/host_profile. Další informace naleznete v poznámce SAP [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>Instalace SAP NetWeaver ASCS pro SAP HANA na Azure NetAppFiles (ANF) BETA

> [!NOTE]
> Tato funkce ještě není ani GA. Další informace naleznete v poznámce SAP [2815988] (viditelné pouze pro zákazníky s náhledem).
Otevření incidentu SAP na komponentě BC-VCM-LVM-HYPERV a žádost o připojení k adaptéru úložiště LaMa pro náhled souborů Azure NetApp

ANF poskytuje nfs pro Azure. V kontextu SAP LaMa to zjednodušuje tvorbu instancí ABAP Central Services (ASCS) a následnou instalaci aplikačních serverů. Dříve musela instance ASCS fungovat také jako nfs server a parametr acosprep/nfs_paths musel být přidán do host_profile SAP Hostagent.

#### <a name="anf-is-currently-available-in-these-regions"></a>ANF je v současné době k dispozici v těchto oblastech:

Austrálie – východ, střed USA, východní USA, východní USA 2, severní Evropa, střední šířka USA, západní Evropa a západní USA 2.

#### <a name="network-requirements"></a>Požadavky na síť

ANF vyžaduje delegovanou podsíť, která musí být součástí stejné virtuální sítě jako servery SAP. Zde je příklad pro takovou konfiguraci.
Tato obrazovka zobrazuje vytvoření virtuální sítě a první podsítě:

![SAP LaMa vytvořit virtuální síť pro Azure ANF ](media/lama/sap-lama-createvn-50.png)

Dalším krokem se vytvoří delegovaná podsíť pro Microsoft.NetApp/svazky.

![SAP LaMa přidat delegovanou podsíť ](media/lama/sap-lama-addsubnet-50.png)

![SAP LaMa seznam podsítí ](media/lama/sap-lama-subnets.png)

Teď je potřeba vytvořit účet NetApp v rámci portálu Azure:

![SAP LaMa vytvořit účet NetApp ](media/lama/sap-lama-create-netappaccount-50.png)

![Účet SAP LaMa NetApp vytvořen ](media/lama/sap-lama-netappaccount.png)

V rámci účtu NetApp fond kapacity určuje velikost a typ disků pro každý fond:

![SAP LaMa vytvořit fond kapacity NetApp ](media/lama/sap-lama-capacitypool-50.png)

![Vytvořený kapacitní fond SAP LaMa NetApp ](media/lama/sap-lama-capacitypool-list.png)

Svazky nfs lze nyní definovat. Vzhledem k tomu, že budou existovat svazky pro více systémů v jednom fondu, by mělo být zvoleno samovysvětlující schéma pojmenování. Přidání sid pomáhá seskupit související svazky dohromady. Pro ascs a as instance jsou potřeba následující připojení: */sapmnt/\<SID\>*, */usr/sap/\<SID\>* a */home/\<sid\>adm*. Volitelně */usr/sap/trans* je potřeba pro centrální přenosový adresář, který je alespoň používán všemi systémy jednoho prostředí.

> [!NOTE]
> Během fáze BETA název svazků musí být jedinečný v rámci předplatného.

![SAP LaMa vytvořit svazek 1 ](media/lama/sap-lama-createvolume-80.png)

![SAP LaMa vytvořit svazek 2 ](media/lama/sap-lama-createvolume2-80.png)

![SAP LaMa vytvořit svazek 3 ](media/lama/sap-lama-createvolume3-80.png)

Tyto kroky je třeba opakovat i pro ostatní svazky.

![SAP LaMa seznam vytvořených svazků ](media/lama/sap-lama-volumes.png)

Nyní je třeba tyto svazky namontovat do systémů, kde bude provedena počáteční instalace s SAP SWPM.

Nejprve je třeba vytvořit přípojné body. V tomto případě je SID AN1, takže je třeba provést následující příkazy:

```bash
mkdir -p /home/an1adm
mkdir -p /sapmnt/AN1
mkdir -p /usr/sap/AN1
mkdir -p /usr/sap/trans
```
Dále budou svazky ANF připojeny pomocí následujících příkazů:

```bash
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-home-sidadm /home/an1adm
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-sapmnt-sid /sapmnt/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-usr-sap-sid /usr/sap/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/global-usr-sap-trans /usr/sap/trans
```
Příkazy připojení lze také odvodit z portálu. Je třeba upravit místní přípojné body.

K ověření použijte příkaz df -h.

![Sap LaMa přípojné body Úroveň operačního systému ](media/lama/sap-lama-mounts.png)

Nyní musí být provedena instalace s SWPM.

Stejné kroky musí být provedeny alespoň pro jednu instanci AS.

Po úspěšné instalaci musí být systém objeven v rámci SAP LaMa.

Přípojné body by měly vypadat takto pro ascs a as instance:

![SAP LaMa přípojné ](media/lama/sap-lama-ascs.png) body v LaMa (Toto je příklad. IP adresy a cesta exportu se liší od adres, které byly použity dříve)


#### <a name="install-sap-hana"></a>Instalace SAP HANA

Pokud nainstalujete SAP HANA pomocí nástroje příkazového řádku hdblcm, použijte parametr --hostname k poskytnutí virtuálního názvu hostitele. Je třeba přidat IP adresu virtuálního názvu hostitele databáze do síťového rozhraní. Doporučeným způsobem je použití sapacext. Pokud připojíte IP adresu pomocí sapacext, ujistěte se, že znovu připojit IP adresu po restartu.

Přidejte další virtuální název hostitele a adresu IP pro název, který používají aplikační servery pro připojení k tenantovi HANA.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Spusťte instalaci instance databáze SWPM na virtuálním počítači aplikačního serveru, nikoli na virtuálním počítači HANA. Použijte *ah1-db* pro *hostitele databáze* v dialogovém okně Databáze pro systém *SAP*.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Instalace aplikačního serveru SAP NetWeaver pro SAP HANA

Před spuštěním SAP Software Provisioning Manager (SWPM) je třeba připojit IP adresu virtuálního názvu hostitele aplikačního serveru. Doporučeným způsobem je použití sapacext. Pokud připojíte IP adresu pomocí sapacext, ujistěte se, že znovu připojit IP adresu po restartu.

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

K nastavení identity, která se používá k nalezení klíče v úložišti uživatelů HDB, se doporučuje použít parametr profilu SAP NetWeaver dbs/hdb/hdb_use_ident. Tento parametr můžete přidat ručně po instalaci instance databáze s SWPM nebo spustit SWPM s

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Pokud jej nastavíte ručně, budete také muset vytvořit nové položky úložiště uživatelů HDB.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Použijte *ah1-di-0* pro *název hostitele instance PAS* v dialogovém okně Instance *primárního aplikačního serveru*.

#### <a name="post-installation-steps-for-sap-hana"></a>Kroky po instalaci pro SAP HANA

Ujistěte se, že zálohovat SYSTEMDB a všechny databáze klientů před pokusem o kopírování klienta, přesunutí klienta nebo vytvoření replikace systému.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

V níže uvedených příkladech předpokládáme, že nainstalujete systém SAP NetWeaver se systémovým ID AS1. Názvy virtuálních hostitelů jsou as1-db pro instanci SERVERU SQL Server používanou systémem SAP NetWeaver, as1-ascs pro SAP NetWeaver ASCS a as1-di-0 pro první aplikační server SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Instalace sap netweaverascs pro SQL Server

Před spuštěním SAP Software Provisioning Manager (SWPM) je třeba připojit IP adresu virtuálního názvu hostitele ASCS. Doporučeným způsobem je použití sapacext. Pokud připojíte IP adresu pomocí sapacext, ujistěte se, že znovu připojit IP adresu po restartu.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Spusťte SWPM a použijte *as1-ascs* pro *název hostitele instance ASCS*.

#### <a name="install-sql-server"></a>Instalace SQL Serveru

Je třeba přidat IP adresu virtuálního názvu hostitele databáze do síťového rozhraní. Doporučeným způsobem je použití sapacext. Pokud připojíte IP adresu pomocí sapacext, ujistěte se, že znovu připojit IP adresu po restartu.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Spusťte instalaci instance databáze SWPM na virtuálním počítači serveru SQL. Pomocí SAPINST_USE_HOSTNAME=*as1-db* přepište název hostitele používaný pro připojení k serveru SQL Server. Pokud jste virtuální počítač nasadili pomocí šablony Správce prostředků Azure, nastavte adresář použitý pro datové soubory databáze na *C:\sql\data* a soubor protokolu databáze do *c:\sql\log*.

Ujistěte se, že uživatel *NT AUTHORITY\SYSTEM* má přístup k serveru SQL Server a má server *role sysadmin*. Další informace naleznete v sap note [1877727] a [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Instalace aplikačního serveru SAP NetWeaver

Před spuštěním SAP Software Provisioning Manager (SWPM) je třeba připojit IP adresu virtuálního názvu hostitele aplikačního serveru. Doporučeným způsobem je použití sapacext. Pokud připojíte IP adresu pomocí sapacext, ujistěte se, že znovu připojit IP adresu po restartu.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Použijte *as1-di-0* pro *název hostitele instance PAS* v dialogovém okně Instance *primárního aplikačního serveru*.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="errors-and-warnings-during-discover"></a>Chyby a upozornění během zjišťování

* Oprávnění SELECT bylo odepřeno.
  * [Microsoft] [Ovladač serveru SQL SERVER ODBC] [SQL Server] Oprávnění SELECT bylo odepřeno u objektu "log_shipping_primary_databases", databáze 'msdb', schéma 'dbo'. [SOAPFaultException]  
  Oprávnění SELECT bylo odepřeno u objektu "log_shipping_primary_databases", databáze 'msdb', schéma 'dbo'.
  * Řešení  
    Ujistěte se, že *NT AUTHORITY\SYSTEM* má přístup k serveru SQL Server. Viz SAP Note [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Chyby a upozornění pro ověření instance

* Při ověřování úložiště uživatelů HDB byla vyvolána výjimka.  
  * viz Prohlížeč protokolů  
    com.sap.nw.lm.aci.monitor.api.validation.RuntimeValidationException: Výjimka v validátoru s ID RuntimeHDBConnectionValidator (Ověření: VALIDATION_HDB_USERSTORE): Nelze načíst hdbuserstore  
    Úložiště uživatelů HANA není ve správném umístění
  * Řešení  
    Ujistěte se, že /usr/sap/AH1/hdbclient/install/installation.ini je správná

### <a name="errors-and-warnings-during-a-system-copy"></a>Chyby a upozornění během systémové kopie

* Při ověřování kroku zřizování systému došlo k chybě.
  * Příčinou: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h\;hn1-db -o level=0 status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p háček -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p h -r
  * Řešení  
    Zálohování všech databází ve zdrojovém systému HANA

* Krok kopírování systému *Spuštění* instance databáze
  * Operace agenta hostitele '000D3A282BC91EE8A1D76CF1F92E2944' se nezdařila (OperationException. FaultCode: '127', Zpráva: 'Spuštění příkazu se nezdařilo. : [Microsoft][ODBC SQL Server Driver][SQL Server]Uživatel nemá oprávnění ke změně databáze AS2, databáze neexistuje nebo databáze není ve stavu, který umožňuje kontroly přístupu.')
  * Řešení  
    Ujistěte se, že *NT AUTHORITY\SYSTEM* má přístup k serveru SQL Server. Viz SAP Note [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Chyby a varování během klonování systému

* Při pokusu o registraci agenta instance v kroku *Vynucený registr a Agent instance spuštění* aplikačního serveru nebo ASCS došlo k chybě.
  * Při pokusu o registraci agenta instance došlo k chybě. (RemoteException: 'Failed to load instance\\data from profile ' as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': Nelze získat přístup k profilu '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': Žádný takový soubor nebo adresář.')
  * Řešení  
   Ujistěte se, že sdílená sapmnt na ASCS/SCS má úplný přístup pro SAP_AS1_GlobalAdmin

* Chyba v kroku *Povolit ochranu při spuštění pro klonování*
  * Nepodařilo se\\otevřít soubor ' as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0' Příčina: Žádný takový soubor nebo adresář
  * Řešení  
    Účet počítače aplikačního serveru potřebuje přístup pro zápis do profilu

### <a name="errors-and-warnings-during-create-system-replication"></a>Chyby a upozornění během vytváření replikace systému

* Výjimka při klepnutí na vytvořit systémovou replikaci
  * Příčinou: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h\;hn1-db -o level=0 status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p háček -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p h -r
  * Řešení  
    Otestujte, zda lze sapacext provést jako `<hanasid`>adm

* Chyba, pokud v kroku úložiště není povolena úplná kopie.
  * Při hlášení zprávy o atributu kontextu pro cestu IStorageCopyData.storageVolumeCopyList:1 a target StorageSystemId došlo k chybě.
  * Řešení  
    Ignorovat upozornění v kroku a zkuste to znovu. Tento problém bude vyřešen v novém balíčku podpory/záplatě SAP LaMa.

### <a name="errors-and-warnings-during-relocate"></a>Chyby a upozornění během přemístění

* Cesta '/usr/sap/AH1' není povolena pro reexportnituniny nfs.
  * Podrobnosti najdete v poznámce SAP [2628497.]
  * Řešení  
    Přidejte exporty ASCS do profilu agenta hostagenta ASCS. Viz SAP Note [2628497]

* Funkce není implementována při přemístění ASCS
  * Výstup příkazu: exportfs: host:/usr/sap/AX1: Funkce není implementována
  * Řešení  
    Ujistěte se, že je v cílovém virtuálním počítači přemístit cílový počítač povolena služba nfs server.

### <a name="errors-and-warnings-during-application-server-installation"></a>Chyby a upozornění během instalace aplikačního serveru

* Při provádění kroku SAPinst došlo k chybě: getProfileDir
  * Chyba: (Poslední chyba hlášená krokem: Zachyceno ESAPinstException v volání modulu: Validátor kroku '| NW_DI|ind|ind|ind|ind|0|0| NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0| NW_readProfileDir|ind|ind|ind|ind|readProfile|0|getProfileDir" ohlásil chybu: \\Uzel \as1-ascs\sapmnt\AS1\SYS\profile neexistuje. Spustit SAPinst v interaktivním režimu k vyřešení tohoto problému)
  * Řešení  
    Ujistěte se, že SWPM běží s uživatelem, který má přístup k profilu. Tohoto uživatele lze nakonfigurovat v Průvodci instalací aplikačního serveru.

* Při provádění kroku SAPinst došlo k chybě: askUnicode
  * Chyba: (Poslední chyba hlášená krokem: Zachyceno ESAPinstException v volání modulu: Validátor kroku '| NW_DI|ind|ind|ind|ind|0|0| NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0| NW_getUnicode|ind|ind|ind|ind|ind|unicode|0|askUnicode" ohlásil chybu: Spuštění sapintu v interaktivním režimu k vyřešení tohoto problému)
  * Řešení  
    Pokud používáte poslední jádro SAP, SWPM nemůže určit, zda je systém unicode systém již pomocí serveru zpráv ASCS. Další podrobnosti najdete v poznámce SAP [2445033.]  
    Tento problém bude vyřešen v novém balíčku podpory/záplatě SAP LaMa.  
    Nastavte parametr profilu OS_UNICODE=uc ve výchozím profilu systému SAP, abyste tento problém vyřešili.

* Při provádění kroku SAPinst došlo k chybě: dCheckGivenServer
  * Chyba při provádění kroku SAPinst: dCheckGivenServer" version="1.0" ERROR: \<(Poslední chyba hlášená krokem: p> Instalace byla zrušena uživatelem. \</p>
  * Řešení  
    Ujistěte se, že SWPM běží s uživatelem, který má přístup k profilu. Tohoto uživatele lze nakonfigurovat v Průvodci instalací aplikačního serveru.

* Při provádění kroku SAPinst došlo k chybě: checkClient
  * Chyba při provádění kroku SAPinst: checkClient" version="1.0" ERROR: \<(Poslední chyba hlášená krokem: p> Instalace byla zrušena uživatelem. \</p>)
  * Řešení  
    Zkontrolujte, zda je ovladač Microsoft ODBC pro SQL Server nainstalován ve virtuálním počítači, do kterého chcete nainstalovat aplikační server.

* Při provádění kroku SAPinst došlo k chybě: copyScripts
  * Poslední chyba hlášená krokem: Systémové volání se nezdařilo. PODROBNOSTI: Chyba 13 (0x0000000d) (Oprávnění odepřeno) při provádění systémového volání 'fopenU' s parametrem (\\\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w), řádek (494) v souboru (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/filesystem/syxxcfstrm2.cpp), trasování zásobníku:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect::newFileStream (args_t const& _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp: 233: CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 29: CSyFileStreamImpl::CSyFileStreamImpl(CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 265: CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp: 58: CSyFileStream2Impl::CSyFileStream2Impl(const CSyPath & \\\aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd, 0x4)  
  syxxcfstrm2.cpp: 456: CSyFileStream2Impl::open()
  * Řešení  
    Ujistěte se, že SWPM běží s uživatelem, který má přístup k profilu. Tohoto uživatele lze nakonfigurovat v Průvodci instalací aplikačního serveru.

* Při provádění kroku SAPinst došlo k chybě: askPasswords
  * Poslední chyba hlášená krokem: Systémové volání se nezdařilo. PODROBNOSTI: Chyba 5 (0x00000005) (Přístup je odepřen.) při provádění systémového volání 'NetValidatePasswordPolicy' s parametrem (...), řádek (359) v souboru (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), trasovací zásobník:  
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
  iaxxcaccount.cpp: 107: iastring CIaOsAccountConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcaccount.cpp: 1186: iastring CIaOsAccountConnect::validatePasswordPolicy(args_t const& _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp: 297: ISyAccountMgt::PasswordValidationMessage CSyAccountMgtImpl::validatePasswordPolicy(saponazure,*****) const )
  * Řešení  
    Nezapomeňte přidat pravidlo hostitele v kroku *Izolace* povolit komunikaci z virtuálního zařízení do řadiče domény

## <a name="next-steps"></a>Další kroky
* [Průvodce provozem SAP HANA v Azure][hana-ops-guide]
* [Plánování a implementace virtuálních počítačů Azure pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP][dbms-guide]
