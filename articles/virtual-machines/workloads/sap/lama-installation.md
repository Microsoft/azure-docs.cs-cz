---
title: Konektor SAP LaMa pro Azure | Microsoft Docs
description: Správa systémů SAP v Azure pomocí SAP LaMa
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
ms.openlocfilehash: 5e20863cd971a55142283676fe035d3238520ae1
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361360"
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
> Obecná podpora: Pokud potřebujete podporu pro SAP LaMa nebo Azure Connector, vždy prosím otevřete incident s protokolem SAP v komponentě BC-VCM-LVM-HYPERV.

SAP LaMa používá mnoho zákazníků k provozování a monitorování jejich technologie SAP na šířku. Vzhledem k tomu, že SAP LaMa 3,0 SP05, je ve výchozím nastavení dodáván s konektorem do Azure. Pomocí tohoto konektoru můžete navrátit a spustit virtuální počítače, zkopírovat a přemístit spravované disky a odstranit spravované disky. Pomocí těchto základních operací můžete přemístit, kopírovat, klonovat a aktualizovat systémy SAP pomocí SAP LaMa.

V této příručce se dozvíte, jak nastavit konektor Azure pro SAP LaMa, vytvořit virtuální počítače, které se dají použít k instalaci adaptivních systémů SAP a jak je nakonfigurovat.

> [!NOTE]
> Konektor je k dispozici pouze v edici SAP LaMa Enterprise.

## <a name="resources"></a>Zdroje a prostředky

Následující poznámky SAP souvisejí s tématem SAP LaMa v Azure:

| Číslo poznámky | Nadpis |
| --- | --- |
| [2343511] |Konektor Microsoft Azure pro správu SAP na šířku (LaMa) |
| [2350235] |SAP na šířku – Správa 3,0 – Enterprise Edition |

Také si přečtěte [portál pro podporu SAP pro SAP Lama](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Obecné poznámky

* Ujistěte se, že jste povolili *Automatické vytváření přípojný bod* v nastavení-> nastavení – modul >  
  Pokud SAP LaMa připojí svazky pomocí adaptivního rozšíření SAP na virtuálním počítači, musí přípojný bod existovat, pokud toto nastavení není povolené.

* Použijte samostatnou podsíť a nepoužívejte dynamické IP adresy, abyste zabránili krádeži IP adresy při nasazení nových virtuálních počítačů a instance SAP nejsou připravené.  
  Pokud použijete dynamické přidělování IP adres v podsíti, kterou používá i SAP LaMa, může dojít k selhání přípravy systému SAP pomocí SAP LaMa. Pokud je systém SAP nepřipravený, IP adresy nejsou rezervované a můžou se přidělit jiným virtuálním počítačům.

* Pokud se přihlásíte ke spravovaným hostitelům, nezapomeňte zabránit odpojení systémů souborů.  
  Pokud se přihlásíte k virtuálním počítačům se systémem Linux a změníte pracovní adresář na adresář v přípojném bodu, například/usr/sap/AH1/ASCS00/exe, svazek nelze odpojit a operace přemístění nebo odpříprava se nezdařila.

* Nezapomeňte zakázat CLOUD_NETCONFIG_MANAGE na virtuálních počítačích s SUSE SLES Linux. Další podrobnosti najdete v článku [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633).

## <a name="set-up-azure-connector-for-sap-lama"></a>Nastavení konektoru Azure pro SAP LaMa

Konektor Azure se dodává jako SAP LaMa 3,0 SP05. Doporučujeme vždycky nainstalovat nejnovější balíček pro podporu a opravu pro SAP LaMa 3,0.

Konektor Azure používá rozhraní Azure Resource Manager API ke správě prostředků Azure. SAP LaMa může k ověřování pomocí tohoto rozhraní API použít instanční objekt nebo spravovanou identitu. Pokud vaše SAP LaMa běží na VIRTUÁLNÍm počítači Azure, doporučujeme použít spravovanou identitu, jak je popsáno v kapitole [použití spravované identity pro získání přístupu k rozhraní API Azure](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d). Pokud chcete použít instanční objekt, postupujte podle kroků v části [použití instančního objektu k získání přístupu k rozhraní API Azure](lama-installation.md#913c222a-3754-487f-9c89-983c82da641e).

### <a name="use-a-service-principal-to-get-access-to-the-azure-api"></a><a name="913c222a-3754-487f-9c89-983c82da641e"></a>Použití instančního objektu k získání přístupu k rozhraní API Azure

Služba Azure Connector může k autorizaci na Microsoft Azure použít instanční objekt. Pomocí těchto kroků můžete vytvořit instanční objekt pro SAP na šířku Management (LaMa).

1. Přejděte na https://portal.azure.com.
1. Otevřete okno Azure Active Directory
1. Klikněte na Registrace aplikací
1. Klikněte na nová registrace.
1. Zadejte název a klikněte na zaregistrovat.
1. Vyberte novou aplikaci a klikněte na certifikáty & tajné klíče na kartě nastavení.
1. Vytvořte nový tajný klíč klienta, zadejte popis nového klíče, vyberte, kdy má tajný klíč vypršet, a klikněte na Uložit.
1. Zapište hodnotu. Používá se jako heslo instančního objektu.
1. Poznamenejte si ID aplikace. Používá se jako uživatelské jméno objektu služby.

Objekt služby nemá ve výchozím nastavení oprávnění pro přístup k prostředkům Azure. Pro přístup k nim musíte mít oprávnění instančního objektu.

1. Přejděte na https://portal.azure.com.
1. Otevřete okno skupiny prostředků.
1. Vyberte skupinu prostředků, kterou chcete použít.
1. Klikněte na řízení přístupu (IAM).
1. Klikněte na přidat přiřazení role.
1. Vybrat přispěvatele rolí
1. Zadejte název aplikace, kterou jste vytvořili výše.
1. Kliknutí na Uložit
1. Opakujte krok 3 až 8 pro všechny skupiny prostředků, které chcete používat v SAP LaMa

### <a name="use-a-managed-identity-to-get-access-to-the-azure-api"></a><a name="af65832e-6469-4d69-9db5-0ed09eac126d"></a>Použití spravované identity k získání přístupu k rozhraní API Azure

Aby bylo možné používat spravovanou identitu, vaše instance SAP LaMa musí běžet na virtuálním počítači Azure, který má identitu přiřazenou systémem nebo uživatelem. Další informace o spravovaných identitách najdete v článku [co jsou spravované identity pro prostředky Azure?](../../../active-directory/managed-identities-azure-resources/overview.md) a [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí Azure Portal](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md).

Spravovaná identita nemá ve výchozím nastavení oprávnění pro přístup k prostředkům Azure. Musíte mu udělit oprávnění k přístupu.

1. Přejděte na https://portal.azure.com.
1. Otevřete okno skupiny prostředků.
1. Vyberte skupinu prostředků, kterou chcete použít.
1. Klikněte na řízení přístupu (IAM).
1. Klikněte na Přidat > přidat přiřazení role.
1. Vybrat přispěvatele rolí
1. Vyberte virtuální počítač pro přiřazení přístupu k
1. Vyberte virtuální počítač, na kterém je spuštěná instance SAP LaMa.
1. Kliknutí na Uložit
1. Opakujte kroky u všech skupin prostředků, které chcete používat v SAP LaMa

V konfiguraci konektoru Azure LaMa pro SAP vyberte použít spravovanou identitu a povolte tak použití spravované identity. Pokud chcete použít identitu přiřazenou systémem, ujistěte se, že pole uživatelské jméno zůstane prázdné. Pokud chcete použít identitu přiřazenou uživatelem, zadejte ID identity přiřazené uživateli do pole uživatelské jméno.

### <a name="create-a-new-connector-in-sap-lama"></a>Vytvoření nového konektoru v SAP LaMa

Otevřete web SAP LaMa a přejděte na infrastruktura. Přejděte na kartu cloudové správce a klikněte na Přidat. Vyberte Microsoft Azure Adaptér cloudu a klikněte na další. Zadejte následující informace:

* Popisek: vyberte název instance konektoru.
* Uživatelské jméno: ID nebo ID aplikace instančního objektu, který je přiřazený identitě uživatele virtuálního počítače. Další informace najdete v tématu [použití identity přiřazené systémem nebo uživatelem].
* Heslo: klíč nebo heslo instančního objektu služby. Toto pole můžete nechat prázdné, pokud použijete identitu přiřazenou systémem nebo uživatelem.
* Adresa URL: zachovat výchozí `https://management.azure.com/`
* Interval monitorování (sekundy): mělo by být aspoň 300.
* Použití spravované identity: SAP LaMa může k ověřování pomocí rozhraní API Azure použít identitu přiřazenou systémem nebo uživatelem. V této příručce najdete v části [použití spravované identity k získání přístupu k rozhraní Azure API](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d) .
* ID předplatného: ID předplatného Azure
* ID tenanta Azure Active Directory: ID tenanta služby Active Directory
* Hostitel proxy serveru: název hostitele proxy serveru, pokud SAP LaMa potřebuje proxy server pro připojení k Internetu.
* Port proxy serveru: port TCP serveru proxy
* Změnit typ úložiště na Uložit náklady: Toto nastavení povolte, pokud má adaptér Azure změnit typ úložiště Managed Disks tak, aby ušetřil náklady, když se disky nepoužívají. U datových disků, na které se odkazuje v konfiguraci instance SAP, adaptér při přípravě instance změní typ disku na standardní úložiště během přípravy instance a zpátky na původní typ úložiště. Pokud zastavíte virtuální počítač v SAP LaMa, adaptér změní typ úložiště všech připojených disků včetně disku s operačním systémem na úložiště Standard. Pokud spustíte virtuální počítač v SAP LaMa, adaptér změní typ úložiště zpátky na původní typ úložiště.

Kliknutím na test konfigurace Ověřte zadání. Měli byste vidět

Připojení bylo úspěšné: připojení k Microsoft cloudu bylo úspěšné. bylo nalezeno 7 skupin prostředků (je požadováno pouze 10 skupin).

v dolní části webu.

## <a name="provision-a-new-adaptive-sap-system"></a>Zřízení nového adaptivního systému SAP

Můžete ručně nasadit nový virtuální počítač nebo použít jednu ze šablon Azure v [úložišti rychlého](https://github.com/Azure/azure-quickstart-templates)startu. Obsahuje šablony pro [SAP NETWEAVER ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), [SAP NetWeaver Application Servers](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)a [databázi](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). Tyto šablony můžete také použít k zřizování nových hostitelů v rámci kopírování či klonování systému atd.

Doporučujeme použít samostatnou podsíť pro všechny virtuální počítače, které chcete spravovat pomocí SAP LaMa, a nepoužívat dynamické IP adresy k zabránění krádeži IP adresy při nasazení nových virtuálních počítačů a instancích SAP nejsou připravené.

> [!NOTE]
> Pokud je to možné, odeberte všechna rozšíření virtuálních počítačů, protože by mohla způsobit, že se odpojí disky z virtuálního počítače s dlouhou dobou běhu.

Ujistěte se, že \<hanasid> \<sapsid> na cílovém počítači existuje ADM, ADM a skupina sapsys se stejným ID a GID nebo používá protokol LDAP. Povolte a spusťte server NFS na virtuálních počítačích, které by měly být použity ke spuštění SAP NetWeaver (A) SCS.

### <a name="manual-deployment"></a>Ruční nasazení

SAP LaMa komunikuje s virtuálním počítačem pomocí agenta hostitele SAP. Pokud nasadíte virtuální počítače ručně nebo nepoužíváte šablonu Azure Resource Manager z úložiště pro rychlé zprovoznění, nezapomeňte nainstalovat nejnovějšího agenta hostitele SAP a adaptivní rozšíření SAP. Další informace o požadovaných úrovních opravy pro Azure najdete v tématu SAP Note [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Ruční nasazení virtuálního počítače se systémem Linux

Vytvořte nový virtuální počítač s jedním z podporovaných operačních systémů uvedených v části SAP Note [2343511]. Přidejte další konfigurace protokolu IP pro instance SAP. Každá instance potřebuje aspoň na IP adresu a musí se nainstalovat pomocí virtuálního hostitele.

Instance SAP NetWeaver ASCS potřebuje disky pro/sapmnt/ \<SAPSID> ,/usr/SAP/ \<SAPSID> ,/usr/SAP/trans a/usr/SAP/ \<sapsid> ADM. Aplikační servery SAP NetWeaver nepotřebují další disky. Všechno, co souvisí s instancí SAP, se musí ukládat na ASCS a exportovat přes systém souborů NFS. V opačném případě není nyní možné přidat další aplikační servery pomocí SAP LaMa.

![SAP NetWeaver ASCS v systému Linux](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Ruční nasazení pro SAP HANA

Vytvořte nový virtuální počítač s jedním z podporovaných operačních systémů pro SAP HANA, jak je uvedeno v části SAP Note [2343511]. Přidejte další konfiguraci IP adresy pro SAP HANA a jednoho tenanta na HANA.

SAP HANA potřebuje disky pro/Hana/Shared,/Hana/Backup,/Hana/data a/Hana/log.

![SAP HANA v systému Linux](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Ruční nasazení pro Oracle Database v systému Linux

Vytvořte nový virtuální počítač s jedním z podporovaných operačních systémů pro databáze Oracle, jak je uvedeno v části SAP Note [2343511]. Přidejte další konfiguraci IP adresy pro databázi Oracle.

Databáze Oracle potřebuje disky pro/Oracle,/Home/oraod1 a/Home/Oracle.

![Oracle Database v systému Linux](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Ruční nasazení pro Microsoft SQL Server

Vytvořte nový virtuální počítač s jedním z podporovaných operačních systémů pro Microsoft SQL Server, jak je uvedeno v části SAP Note [2343511]. Přidejte další konfiguraci IP adresy pro instanci SQL Server.

Server SQL Server Database potřebuje disky pro data databáze a soubory protokolů a disky pro c:\usr\sap..

![Oracle Database v systému Linux](media/lama/sap-lama-db-sql.png)

Nezapomeňte nainstalovat podporovaný ovladač Microsoft ODBC pro SQL Server na virtuálním počítači, který chcete použít k přemístění aplikačního serveru SAP NetWeaver do nebo jako cíle kopírování/klonování systému.

SAP LaMa nemůže přemístit SQL Server sám sebe, takže virtuální počítač, který chcete použít k přemístění instance databáze do nebo jako cíl pro kopírování/klonování systému, musí SQL Server předinstalované.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Nasazení virtuálního počítače pomocí šablony Azure

Stáhněte si z [webu SAP software Marketplace](https://support.sap.com/swdc) pro operační systém virtuálních počítačů následující nejnovější dostupné archivy:

1. SAPCAR 7,21
1. AGENT HOSTITELE SAP 7,21
1. ADAPTIVNÍ ROZŠÍŘENÍ SAP 1,0 – EXT

Stáhněte si také následující komponenty z [webu Microsoft Download Center](https://www.microsoft.com/download) .

1. Distribuovatelný balíček Microsoft Visual C++ 2010 (x64) (jenom Windows)
1. Ovladač Microsoft ODBC pro SQL Server (jenom SQL Server)

K nasazení šablony jsou nutné tyto součásti. Nejjednodušší způsob, jak je zpřístupnit pro šablonu, je odeslat je do účtu služby Azure Storage a vytvořit sdílený přístupový podpis (SAS).

Šablony mají následující parametry:

* sapSystemId: ID systému SAP. Slouží k vytvoření rozložení disku (například/usr/SAP/ \<sapsid> ).

* názevpočítače: název počítače nového virtuálního počítače. Tento parametr je také používán SAP LaMa. Když použijete tuto šablonu k zřízení nového virtuálního počítače v rámci systémové kopie, SAP LaMa počká, dokud nebude dostupný hostitel s tímto názvem počítače.

* osType: typ operačního systému, který chcete nasadit.

* DbType: typ databáze. Tento parametr slouží k určení počtu dalších konfigurací IP adres, které je třeba přidat, a způsobu, jakým by mělo vypadat rozložení disku.

* sapSystemSize: velikost systému SAP, který chcete nasadit. Slouží k určení typu a velikosti instance virtuálního počítače.

* adminUsername: uživatelské jméno pro virtuální počítač.

* adminPassword: heslo pro virtuální počítač. Můžete také zadat veřejný klíč pro SSH.

* sshKeyData: veřejný klíč SSH pro virtuální počítače. Podporováno pouze pro operační systémy Linux.

* subnetId: ID podsítě, kterou chcete použít.

* deployEmptyTarget: Pokud chcete virtuální počítač použít jako cíl pro opětovné umístění instance nebo podobného, můžete nasadit prázdný cíl. V takovém případě nejsou připojené žádné další disky ani konfigurace IP adres.

* sapcarLocation: umístění aplikace sapcar, která odpovídá operačnímu systému, který nasadíte. sapcar se používá k extrakci archivů, které zadáte v jiných parametrech.

* sapHostAgentArchiveLocation: umístění archivu agenta hostitele SAP. Agent hostitele SAP je nasazený jako součást nasazení této šablony.

* sapacExtLocation: umístění adaptivních rozšíření SAP. V části SAP Note [2343511] je uvedena minimální úroveň opravy požadovaná pro Azure.

* vcRedistLocation: umístění modulu runtime VC, který je nutný k instalaci adaptivních rozšíření SAP. Tento parametr je vyžadován pouze pro systém Windows.

* odbcDriverLocation: umístění ovladače ODBC, který chcete nainstalovat. Podporuje se jenom ovladač Microsoft ODBC Driver for SQL Server.

* sapadmPassword: heslo pro uživatele sapadm.

* sapadmId: ID uživatele pro Linux uživatele sapadm. Není vyžadováno pro Windows.

* sapsysGid: ID skupiny Linux skupiny sapsys. Není vyžadováno pro Windows.

* _artifactsLocation: základní identifikátor URI, kde jsou umístěny artefakty vyžadované touto šablonou. Při nasazení šablony pomocí doprovodných skriptů se použije privátní umístění v předplatném a tato hodnota se automaticky vygeneruje. Potřebuje se jenom v případě, že šablonu nasazujete z GitHubu.

* _artifactsLocationSasToken: sasToken vyžaduje přístup k _artifactsLocation. Když se šablona nasadí pomocí doprovodných skriptů, sasToken se automaticky vygeneruje. Potřebuje se jenom v případě, že šablonu nasazujete z GitHubu.

### <a name="sap-hana"></a>SAP HANA

V níže uvedených příkladech předpokládáme, že nainstalujete SAP HANA se systémem ID HN1 a systémem SAP NetWeaver s ID systému AH1. Virtuální názvy hostitelů jsou HN1-DB pro instanci HANA, ah1-DB pro tenanta HANA používané systémem SAP NetWeaver, ah1-ASCS pro SAP NetWeaver ASCS a ah1-di-0 pro první aplikační Server SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>Instalace SAP NetWeaver ASCS pro SAP HANA s využitím Azure Managed Disks

Než začnete se správcem SAP software Provisioning Manager (SWPM), musíte připojit IP adresu virtuálního hostitele ASCS. Doporučeným způsobem je použít sapacext. Pokud IP adresu připojíte pomocí sapacext, nezapomeňte po restartování připojit IP adresu znovu.

![Logo Linux.][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Logo Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

Spusťte SWPM a pro *název hostitele instance ASCS*použijte *ah1-ASCS* .

![Logo Linux.][Logo_Linux] Linux  
Do profilu agenta hostitele SAP, který se nachází na adrese/usr/SAP/hostctrl/exe/host_profile, přidejte následující parametr profilu. Další informace najdete v tématu SAP Note [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>Instalace SAP NetWeaver ASCS pro SAP HANA na Azure NetAppFiles (ANF) BETA verze

> [!NOTE]
> Tato funkce ještě není GA. Další informace najdete v tématu SAP Note [2815988] (viditelné pouze pro zákazníky ve verzi Preview).
Otevřete incident SAP na komponentě BC-VCM-LVM-HYPERV a žádost o připojení k adaptéru úložiště LaMa pro verzi Preview služby Azure NetApp Files

ANF poskytuje systém souborů NFS pro Azure. V kontextu SAP LaMa to zjednodušuje vytváření instancí ABAP Central Services (ASCS) a následnou instalaci aplikačních serverů. Dřív se ASCS instance musela jednat jako server systému souborů NFS a také se musí přidat parametr acosprep/nfs_paths do host_profile SAP Hostagent.

#### <a name="anf-is-currently-available-in-these-regions"></a>ANF je v tuto chvíli k dispozici v těchto oblastech:

Austrálie – východ, Střed USA, Východní USA, Východní USA 2, Severní Evropa, Střed USA – jih, Západní Evropa a Západní USA 2.

#### <a name="network-requirements"></a>Požadavky na síť

ANF vyžaduje delegovanou podsíť, která musí být součástí stejné virtuální sítě jako servery SAP. Tady je příklad pro takovou konfiguraci.
Tato obrazovka ukazuje vytvoření virtuální sítě a první podsítě:

![SAP LaMa vytvoření virtuální sítě pro Azure ANF ](media/lama/sap-lama-createvn-50.png)

V dalším kroku se vytvoří delegovaná podsíť pro svazky Microsoft. NetApp/.

![SAP LaMa přidat delegovanou podsíť ](media/lama/sap-lama-addsubnet-50.png)

![Seznam podsítí SAP LaMa ](media/lama/sap-lama-subnets.png)

Teď je potřeba vytvořit účet NetApp v rámci Azure Portal:

![LaMa vytváření účtů NetApp SAP ](media/lama/sap-lama-create-netappaccount-50.png)

![Vytvořil se účet SAP LaMa NetApp. ](media/lama/sap-lama-netappaccount.png)

V rámci účtu NetApp rozsah kapacity určuje velikost a typ disků pro každý fond:

![LaMa SAP vytvořit NetApp fond kapacit ](media/lama/sap-lama-capacitypool-50.png)

![Fond kapacit SAP LaMa NetApp se vytvořil. ](media/lama/sap-lama-capacitypool-list.png)

Svazky NFS se teď dají definovat. Vzhledem k tomu, že budou existovat svazky pro více systémů v jednom fondu, je třeba zvolit schéma pojmenování na základě sebe. Přidáním identifikátoru zabezpečení (SID) lze seskupit související svazky dohromady. Pro ASCS a instanci as jsou potřeba následující připojení: */sapmnt/ \<SID\> *, */usr/SAP/ \<SID\> *a */Home/ \<sid\> ADM*. Volitelně se */usr/SAP/trans* potřebuje pro adresář centrálního přenosu, který se aspoň používá pro všechny systémy na jednom místě.

> [!NOTE]
> Během fáze BETA verze musí být názvy svazků v rámci předplatného jedinečné.

![SAP LaMa vytvoření svazku 1 ](media/lama/sap-lama-createvolume-80.png)

![SAP LaMa vytvořit svazek 2 ](media/lama/sap-lama-createvolume2-80.png)

![SAP LaMa vytvořit svazek 3 ](media/lama/sap-lama-createvolume3-80.png)

Tyto kroky je potřeba zopakovat i pro ostatní svazky.

![Seznam vytvořených svazků SAP LaMa ](media/lama/sap-lama-volumes.png)

Nyní je třeba tyto svazky připojit k systémům, kde bude provedena počáteční instalace pomocí SAP SWPM.

Nejprve je třeba vytvořit přípojné body. V tomto případě je AN1 identifikátor SID, takže je potřeba provést následující příkazy:

```bash
mkdir -p /home/an1adm
mkdir -p /sapmnt/AN1
mkdir -p /usr/sap/AN1
mkdir -p /usr/sap/trans
```
Další svazky ANF budou připojené k následujícím příkazům:

```bash
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-home-sidadm /home/an1adm
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-sapmnt-sid /sapmnt/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-usr-sap-sid /usr/sap/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/global-usr-sap-trans /usr/sap/trans
```
Příkazy Mount lze také odvodit z portálu. Body místního připojení je potřeba upravit.

K ověření použijte příkaz df-h.

![Úroveň operačního systému přípojných bodů SAP LaMa ](media/lama/sap-lama-mounts.png)

Nyní je třeba provést instalaci pomocí SWPM.

Stejný postup je třeba provést alespoň pro jednu instanci.

Po úspěšné instalaci musí být systém zjištěn v rámci SAP LaMa.

Přípojné body by měly vypadat jako v případě ASCS a instance AS:

![Přípojné body SAP LaMa v LaMa ](media/lama/sap-lama-ascs.png) (Toto je příklad. IP adresy a cesta exportu se liší od těch, které se použily před).


#### <a name="install-sap-hana"></a>Instalace SAP HANA

Pokud instalujete SAP HANA pomocí nástroje příkazového řádku hdblcm, použijte k zadání virtuálního hostitele parametr--hostname. Musíte přidat IP adresu virtuálního hostitele databáze do síťového rozhraní. Doporučeným způsobem je použít sapacext. Pokud IP adresu připojíte pomocí sapacext, nezapomeňte po restartování připojit IP adresu znovu.

Přidejte další virtuální název hostitele a IP adresu pro název, který používají aplikační servery pro připojení k tenantovi HANA.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Spusťte instalaci instance databáze SWPM na virtuálním počítači aplikačního serveru, ne na virtuálním počítači HANA. Použijte *ah1-DB* pro *hostitele databáze* v dialogovém okně *databáze pro systém SAP*.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Instalace aplikačního serveru SAP NetWeaver pro SAP HANA

Před spuštěním Správce SAP software Provisioning (SWPM) musíte připojit IP adresu virtuálního hostitele aplikačního serveru. Doporučeným způsobem je použít sapacext. Pokud IP adresu připojíte pomocí sapacext, nezapomeňte po restartování připojit IP adresu znovu.

![Logo Linux.][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Logo Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

Pro nastavení identity, která se používá k nalezení klíče v userStore HDB, se doporučuje použít parametr profilu SAP NetWeaver databáze/HDB/hdb_use_ident. Tento parametr můžete přidat ručně po instalaci instance databáze s SWPM nebo spuštěním SWPM s

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Pokud ho nastavíte ručně, budete také muset vytvořit nové položky userStore HDB.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Pro *název hostitele instance služby pas* v dialogu *instance primárního aplikačního serveru*použijte *ah1-di-0* .

#### <a name="post-installation-steps-for-sap-hana"></a>Kroky po instalaci pro SAP HANA

Nezapomeňte zálohovat SYSTEMDB a všechny databáze tenantů před tím, než se pokusíte provést kopii tenanta, přesunout klienta nebo vytvořit replikaci systému.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

V níže uvedených příkladech předpokládáme, že instalujete systém SAP NetWeaver s ID systému AS1. Virtuální názvy hostitelů jsou AS1-DB pro instanci SQL Server, kterou používá systém SAP NetWeaver, AS1-ASCS pro SAP NetWeaver ASCS a AS1-di-0 pro první aplikační Server SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Instalace SAP NetWeaver ASCS pro SQL Server

Než začnete se správcem SAP software Provisioning Manager (SWPM), musíte připojit IP adresu virtuálního hostitele ASCS. Doporučeným způsobem je použít sapacext. Pokud IP adresu připojíte pomocí sapacext, nezapomeňte po restartování připojit IP adresu znovu.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Spusťte SWPM a pro *název hostitele instance ASCS*použijte *AS1-ASCS* .

#### <a name="install-sql-server"></a>Instalace SQL Serveru

Musíte přidat IP adresu virtuálního hostitele databáze do síťového rozhraní. Doporučeným způsobem je použít sapacext. Pokud IP adresu připojíte pomocí sapacext, nezapomeňte po restartování připojit IP adresu znovu.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Spusťte instalaci instance databáze SWPM na virtuálním počítači SQL serveru. Pomocí SAPINST_USE_HOSTNAME =*AS1-DB* přepište název hostitele, který se používá pro připojení k SQL Server. Pokud jste virtuální počítač nasadili pomocí šablony Azure Resource Manager, ujistěte se, že jste nastavili adresář, který se používá pro datové soubory databáze, do *C:\sql\data* a soubor protokolu databáze na *C:\SQL\LOG*.

Ujistěte se, že uživatel *NT AUTHORITY\SYSTEM* má přístup k SQL Server a má roli role serveru *sysadmin*. Další informace najdete v tématu SAP Note [1877727] a [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Instalace aplikačního serveru SAP NetWeaver

Před spuštěním Správce SAP software Provisioning (SWPM) musíte připojit IP adresu virtuálního hostitele aplikačního serveru. Doporučeným způsobem je použít sapacext. Pokud IP adresu připojíte pomocí sapacext, nezapomeňte po restartování připojit IP adresu znovu.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Pro *název hostitele instance služby pas* v dialogu *instance primárního aplikačního serveru*použijte *AS1-di-0* .

## <a name="troubleshooting"></a>Řešení potíží

### <a name="errors-and-warnings-during-discover"></a>Chyby a varování během zjišťování

* Oprávnění SELECT bylo odepřeno.
  * Microsoft [Ovladač SQL Server ODBC] [SQL Server] Oprávnění SELECT bylo odepřeno pro objekt log_shipping_primary_databases, databázi msdb a schéma dbo. [SOAPFaultException]  
  Oprávnění SELECT bylo odepřeno pro objekt log_shipping_primary_databases, databázi msdb a schéma dbo.
  * Řešení  
    Ujistěte se, že má přístup k SQL Server *NT AUTHORITY\SYSTEM* . Viz SAP Note [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Chyby a upozornění pro ověřování instance

* Při ověřování HDB userStore byla vyvolána výjimka.  
  * viz Log Viewer  
    com. SAP. NW. LM. ACI. monitor. API. Validation. RuntimeValidationException: výjimka ve validátoru s ID ' RuntimeHDBConnectionValidator ' (ověření: ' VALIDATION_HDB_USERSTORE '): nelze načíst hdbuserstore  
    HANA userStore není ve správném umístění.
  * Řešení  
    Ujistěte se, že je/usr/sap/AH1/hdbclient/install/installation.ini správné

### <a name="errors-and-warnings-during-a-system-copy"></a>Chyby a varování během kopírování ze systému

* Při ověřování kroku zřizování systému došlo k chybě.
  * Příčinou je: com. SAP. NW. LM. ACI. Engine. Base. API. util. Exception. HAOperationException Calling '/usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 \; status = 5 \; port = 35013 PF =/usr/SAP/hostctrl/exe/Host_profile-R-T dev_lvminfo-u System-p Hook-R ' |/usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 \; status = 5 \; port = 35013 PF =/usr/SAP/hostctrl/exe/Host_profile-R-T dev_lvminfo-u System-p Hook-R
  * Řešení  
    Pořídit zálohu všech databází ve zdrojovém systému HANA

* Systémový *začátek* Krok kopírování instance databáze
  * Operace agenta hostitele 000D3A282BC91EE8A1D76CF1F92E2944 se nezdařila (OperationException. FaultCode: ' 127 ', zpráva: ' spuštění příkazu se nezdařilo. : [Microsoft] [ODBC SQL Server Driver] [SQL Server] uživatel nemá oprávnění ke změně databáze AS2, databáze neexistuje nebo databáze není ve stavu, který umožňuje kontroly přístupu. ')
  * Řešení  
    Ujistěte se, že má přístup k SQL Server *NT AUTHORITY\SYSTEM* . Viz SAP Note [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Chyby a varování během klonování systému

* Došlo k chybě při pokusu o registraci agenta instance v kroku *vynucené registrace a spuštění agenta instance* aplikačního serveru nebo ASCS.
  * Při pokusu o registraci agenta instance došlo k chybě. (RemoteException: ' nepovedlo se načíst data instance z profilu ' \\ as1-ascs\sapmnt\as1\sys\profile\ AS1_D00_as1-di-0 ': nejde získat přístup k profilu ' \\ AS1-ascs\sapmnt\as1\sys\profile\ AS1_D00_as1-di-0 ': žádný takový soubor nebo adresář. ')
  * Řešení  
   Ujistěte se, že sdílená složka sapmnt na ASCS/SCS má úplný přístup k SAP_AS1_GlobalAdmin

* Chyba v kroku *Povolení ochrany při spuštění pro klon*
  * Nepovedlo se otevřít soubor \\ as1-ascs\sapmnt\as1\sys\profile\ AS1_D00_as1-di-0. Příčina: žádný takový soubor nebo adresář.
  * Řešení  
    Účet počítače aplikačního serveru potřebuje k profilu přístup pro zápis.

### <a name="errors-and-warnings-during-create-system-replication"></a>Chyby a varování během vytváření replikace systému

* Výjimka při kliknutí na vytvoření systémové replikace
  * Příčinou je: com. SAP. NW. LM. ACI. Engine. Base. API. util. Exception. HAOperationException Calling '/usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 \; status = 5 \; port = 35013 PF =/usr/SAP/hostctrl/exe/Host_profile-R-T dev_lvminfo-u System-p Hook-R ' |/usr/SAP/hostctrl/exe/sapacext-a ShowHanaBackups-m HN1-f 50-h HN1-DB-o Level = 0 \; status = 5 \; port = 35013 PF =/usr/SAP/hostctrl/exe/Host_profile-R-T dev_lvminfo-u System-p Hook-R
  * Řešení  
    Test, zda lze sapacext spustit jako `<hanasid`>ADM

* Chyba v případě, že v kroku úložiště není povolené úplné kopírování
  * Došlo k chybě při vytváření zprávy atributu kontextu pro cestu IStorageCopyData. storageVolumeCopyList: 1 a pole targetStorageSystemId
  * Řešení  
    Ignorujte upozornění v kroku a zkuste to znovu. Tento problém bude opravený v novém balíčku nebo opravě podpory SAP LaMa.

### <a name="errors-and-warnings-during-relocate"></a>Chyby a varování během přemístění

* Pro přeexporty NFS není povolená cesta/usr/sap/AH1.
  * Podrobnosti najdete v poznámce ke SAP Note [2628497] .
  * Řešení  
    Přidejte exporty ASCS do profilu HostAgent ASCS. Viz SAP Note [2628497]

* Funkce není implementována při přemístění ASCS
  * Výstup příkazu: exportfs: Host:/usr/SAP/AX1: funkce není implementovaná.
  * Řešení  
    Ujistěte se, že je na cílovém virtuálním počítači povolená služba serveru NFS.

### <a name="errors-and-warnings-during-application-server-installation"></a>Chyby a upozornění při instalaci aplikačního serveru

* Chyba při provádění kroku SAPinst: getProfileDir
  * Chyba: (poslední chyba hlášená krokem: zachycená ESAPinstException v modulu volání: validátor kroku | NW_DI | najít | nenajít | | 0 | 0 | NW_GetSidFromProfiles | najít | najít | najít | getSid | 0 | NW_readProfileDir | zaznamenal | možnost | nenašlo | zareadProfile | 0 | getProfileDir ohlásil chybu: uzel \\ \as1-ascs\sapmnt\AS1\SYS\profile neexistuje. Pokud chcete tento problém vyřešit, spusťte v interaktivním režimu SAPinst.)
  * Řešení  
    Ujistěte se, že je SWPM spuštěný s uživatelem, který má přístup k tomuto profilu. Tento uživatel se dá nakonfigurovat v Průvodci instalací aplikačního serveru.

* Chyba při provádění kroku SAPinst: askUnicode
  * Chyba: (poslední chyba hlášená krokem: zachycená ESAPinstException v modulu volání: validátor kroku | NW_DI | najít | nenajít | | 0 | 0 | NW_GetSidFromProfiles | najít | najít | najít | getSid | 0 | NW_getUnicode | nenáročné | možnost | zakódování | zaznamenala | | 0 | askUnicode ' ohlásil chybu: Chcete-li vyřešit tento problém, spusťte SAPinst v interaktivním režimu)
  * Řešení  
    Pokud používáte nedávné jádro SAP, SWPM nemůže určit, jestli systém je systémem kódování Unicode, který už používá server zpráv ASCS. Další podrobnosti najdete v tématu SAP Note [2445033] .  
    Tento problém bude opravený v novém balíčku nebo opravě podpory SAP LaMa.  
    Pokud chcete tento problém obejít, nastavte parametr profilu OS_UNICODE = UC ve výchozím profilu systému SAP.

* Chyba při provádění kroku SAPinst: dCheckGivenServer
  * Při provádění kroku SAPinst došlo k chybě: dCheckGivenServer "Version =" 1.0 "ERROR: (poslední chyba hlášená krokem: \<p> Instalace byla zrušena uživatelem. \</p>
  * Řešení  
    Ujistěte se, že je SWPM spuštěný s uživatelem, který má přístup k tomuto profilu. Tento uživatel se dá nakonfigurovat v Průvodci instalací aplikačního serveru.

* Chyba při provádění kroku SAPinst: checkClient
  * Při provádění kroku SAPinst došlo k chybě: checkClient "Version =" 1.0 "ERROR: (poslední chyba hlášená krokem: \<p> Instalace byla zrušena uživatelem. \</p>)
  * Řešení  
    Ujistěte se, že je na virtuálním počítači, na který chcete nainstalovat aplikační server, nainstalovaný ovladač Microsoft ODBC Driver for SQL Server.

* Chyba při provádění kroku SAPinst: copyScripts
  * Poslední chyba hlášená krokem: systémové volání selhalo. Podrobnosti: Chyba 13 (0x0000000d) (zamítnuté oprávnění) při provádění systémového volání "fopenU" s parametrem ( \\ \ AS1-ASCS/sapmnt/AS1/sys/exe/UC/NTAMD64/strdbs. cmd, w), line (494) v souboru (\ Bas/Bas/749_REL/bc_749_REL/src/ins/sapinst/impl/src/SysLib/FileSystem/syxxcfstrm2.cpp), trasování zásobníku:  
  CThrThread. cpp: 85: CThrThread:: threadFunction ()  
  CSiServiceSet. cpp: 63: CSiServiceSet:: executeService ()  
  CSiStepExecute. cpp: 913: CSiStepExecute:: Execute ()  
  EJSController. cpp: 179: EJSControllerImpl:: metodu ExecuteScript ()  
  JSExtension. HPP: 1136: CallFunctionBase:: Call ()  
  iaxxcfile. cpp: 183: iastring CIaOsFileConnect:: callMemberFunction (iastring const& Name; args_t const& args)  
  iaxxcfile. cpp: 1849: iastring CIaOsFileConnect:: newFileStream (args_t const& _args)  
  iaxxbfile. cpp: 773: CIaOsFile:: newFileStream_impl (4)  
  syxxcfile. cpp: 233: CSyFileImpl:: openStream (ISyFile:: eFileOpenMode)  
  syxxcfstrm. cpp: 29: CSyFileStreamImpl:: CSyFileStreamImpl (CSyFileStream *, iastring, ISyFile:: eFileOpenMode)  
  syxxcfstrm. cpp: 265: CSyFileStreamImpl:: Open ()  
  syxxcfstrm2. cpp: 58: CSyFileStream2Impl:: CSyFileStream2Impl (const CSyPath & \\ \ AW1-ASCS/sapmnt/AW1/sys/exe/UC/NTAMD64/strdbs. cmd, 0x4)  
  syxxcfstrm2. cpp: 456: CSyFileStream2Impl:: Open ()
  * Řešení  
    Ujistěte se, že je SWPM spuštěný s uživatelem, který má přístup k tomuto profilu. Tento uživatel se dá nakonfigurovat v Průvodci instalací aplikačního serveru.

* Chyba při provádění kroku SAPinst: askPasswords
  * Poslední chyba hlášená krokem: systémové volání selhalo. Podrobnosti: Chyba 5 (0x00000005) (přístup byl odepřen.) při provádění systémového volání NetValidatePasswordPolicy s parametrem (...), řádkem (359) v souboru (\ Bas/Bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), trasování zásobníku:  
  CThrThread. cpp: 85: CThrThread:: threadFunction ()  
  CSiServiceSet. cpp: 63: CSiServiceSet:: executeService ()  
  CSiStepExecute. cpp: 913: CSiStepExecute:: Execute ()  
  EJSController. cpp: 179: EJSControllerImpl:: metodu ExecuteScript ()  
  JSExtension. HPP: 1136: CallFunctionBase:: Call ()  
  CSiStepExecute. cpp: 764: CSiStepExecute:: invokeDialog ()  
  DarkModeGuiEngine. cpp: 56: DarkModeGuiEngine:: showDialogCalledByJs ()  
  DarkModeDialog. cpp: 85: DarkModeDialog:: Submit ()  
  EJSController. cpp: 179: EJSControllerImpl:: metodu ExecuteScript ()  
  JSExtension. HPP: 1136: CallFunctionBase:: Call ()  
  iaxxcaccount. cpp: 107: iastring CIaOsAccountConnect:: callMemberFunction (iastring const& Name; args_t const& args)  
  iaxxcaccount. cpp: 1186: iastring CIaOsAccountConnect:: validatePasswordPolicy (args_t const& _args)  
  iaxxbaccount. cpp: 430: CIaOsAccount:: validatePasswordPolicy_impl ()  
  synxcaccmg. cpp: 297: ISyAccountMgt::P asswordValidationMessage CSyAccountMgtImpl:: validatePasswordPolicy (saponazure, * * * * *) const)
  * Řešení  
    Nezapomeňte přidat pravidlo hostitele v rámci *izolace* krok za účelem povolení komunikace z virtuálního počítače s řadičem domény.

## <a name="next-steps"></a>Další kroky
* [Průvodce provozem SAP HANA v Azure][hana-ops-guide]
* [Plánování a implementace Azure Virtual Machines pro SAP][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
