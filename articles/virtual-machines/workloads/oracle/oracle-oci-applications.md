---
title: Architektury pro nasazení aplikací Oracle na virtuálních počítačích Azure | Dokumenty společnosti Microsoft
description: Aplikační architektury pro nasazení aplikací Oracle, včetně E-Business Suite, JD Edwards EnterpriseOne a PeopleSoft, na virtuálních počítačích Microsoft Azure s databázemi v Azure nebo v Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: borisb
ms.custom: ''
ms.openlocfilehash: f36dfe0092e3447053871ee0e5b4d659bb443779
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687487"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Architektury pro nasazení aplikací Oracle v Azure

Společnosti Microsoft a Oracle spolupracovaly na tom, aby zákazníkům umožnily nasadit v cloudu aplikace Oracle, jako jsou Oracle E-Business Suite, JD Edwards EnterpriseOne a PeopleSoft. Se zavedením propojení [privátních sítí](configure-azure-oci-networking.md) preview mezi Microsoft Azure a Oracle Cloud Infrastructure (OCI) teď lze aplikace Oracle nasadit do Azure s jejich back-enddatabázemi v Azure nebo OCI. Aplikace Oracle lze také integrovat s Azure Active Directory, což vám umožní nastavit jednotné přihlašování tak, aby se uživatelé mohli přihlásit k aplikaci Oracle pomocí svých přihlašovacích údajů služby Azure Active Directory (Azure AD).

Společnost OCI nabízí několik možností databáze Oracle pro aplikace Oracle, včetně DBaaS, Exadata Cloud Service, Oracle RAC a Infrastructure-as-a-Service (IaaS). V současné době autonomní databáze není podporován back-end pro aplikace Oracle.

Existuje [několik možností](oracle-overview.md) pro nasazení aplikací Oracle v Azure, včetně vysoce dostupným a bezpečným způsobem. Azure také nabízí [image virtuálních počítačových virtuálních zařízení databáze Oracle,](oracle-vm-solutions.md) které můžete nasadit, pokud se rozhodnete spouštět aplikace Oracle zcela v Azure.

V následujících částech jsou nastíněna doporučení architektury společností Microsoft i Oracle k nasazení oracle e-business suite, JD Edwards EnterpriseOne a PeopleSoft v konfiguraci napříč cloudy nebo zcela v Azure. Společnosti Microsoft a Oracle tyto aplikace testovaly a potvrdily, že výkon splňuje standardy stanovené společností Oracle pro tyto aplikace.

## <a name="architecture-considerations"></a>Aspekty architektury

Aplikace Oracle se složí z více služeb, které mohou být hostovány na stejných nebo více virtuálních počítačích v Azure a volitelně v OCI. 

Instance aplikace lze nastavit pomocí soukromých nebo veřejných koncových bodů. Společnosti Microsoft a Oracle doporučují pro správu aplikace nastavit *hostitelský virtuální počítač bašty* s veřejnou IP adresou v samostatné podsíti. Potom přiřaďte ostatním počítačům pouze privátní IP adresy, včetně databázové vrstvy. 

Při nastavování aplikace v architektuře mezi cloudy je nutné plánování zajistit, aby ip adresní prostor ve virtuální síti Azure nepřekrýval privátní IP adresní prostor ve virtuální cloudové síti OCI.

Chcete-li získat vyšší zabezpečení, nastavte skupiny zabezpečení sítě na úrovni podsítě, abyste zajistili, že je povolen pouze provoz na určitých portech a adresách IP. Například počítače ve střední vrstvě by měly přijímat pouze provoz z virtuální sítě. Žádný externí provoz by se neměl dostat přímo k počítačům střední vrstvy.

Pro vysokou dostupnost můžete nastavit redundantní instance různých serverů ve stejné sadě dostupnosti nebo v různých zónách dostupnosti. Zóny dostupnosti umožňují dosáhnout 99,99% doby sla, zatímco sady dostupnosti umožňují dosáhnout 99,95% dostupnosti sla v oblasti. Ukázkové architektury uvedené v tomto článku jsou nasazeny ve dvou zónách dostupnosti.

Při nasazování aplikace pomocí propojení mezi cloudy můžete nadále používat existující okruh ExpressRoute k připojení prostředí Azure k místní síti. Pro propojení s OCI však potřebujete samostatný okruh ExpressRoute než ten, který se připojuje k místní síti.

## <a name="e-business-suite"></a>E-Business Suite

Oracle E-Business Suite (EBS) je sada aplikací včetně řízení dodavatelského řetězce (SCM) a řízení vztahů se zákazníky (CRM). Chcete-li využít portfolio spravovaných databází OCI, ebs lze nasadit pomocí propojení mezi cloudy mezi Microsoft Azure a OCI. V této konfiguraci prezentace a aplikační vrstvy spustit v Azure a databázové vrstvy v OCI, jak je znázorněno v následujícím diagramu architektury (obrázek 1).

![Architektura E-Business Suite napříč cloudy](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*Obrázek 1: Architektura e-Business Suite napříč cloudy* 

V této architektuře je virtuální síť v Azure připojena k virtuální cloudové síti v OCI pomocí propojení mezi cloudy. Aplikační vrstva se nastavuje v Azure, zatímco databáze je nastavená v OCI. Doporučujeme nasadit každou komponentu do vlastní podsítě se skupinami zabezpečení sítě, aby byl povolen provoz pouze z určitých podsítí na konkrétních portech.

Architekturu lze také přizpůsobit pro úplné nasazení v Azure s vysoce dostupnými databázemi Oracle nakonfigurovanými pomocí služby Oracle Data Guard ve dvou zónách dostupnosti v oblasti. Následující diagram (obrázek 2) je příkladem tohoto architektonického vzoru:

![Architektura pouze pro E-Business Suite azure](media/oracle-oci-applications/ebs-arch-azure.png)

*Obrázek 2: Architektura pouze pro Azure e-Business Suite*

Následující části popisují různé součásti na vysoké úrovni.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Aplikační (střední) vrstva

Aplikační vrstva je izolovaná ve vlastní podsíti. Existuje několik virtuálních počítačů nastavených pro odolnost proti chybám a snadnou správu oprav. Tyto virtuální počítače můžou být zálohované sdíleným úložištěm, které nabízí soubory Azure NetApp a Ultra SSD. Tato konfigurace umožňuje snadnější nasazení oprav bez prostojů. Počítače v aplikační vrstvě by měly být fronted veřejné vyrovnávání zatížení tak, aby požadavky na aplikační vrstvu EBS jsou zpracovány i v případě, že jeden počítač ve vrstvě je offline z důvodu poruchy.

### <a name="load-balancer"></a>Nástroj pro vyrovnávání zatížení

Azure vyrovnávání zatížení umožňuje distribuovat provoz napříč více instancí vašeho pracovního vytížení k zajištění vysoké dostupnosti. V tomto případě je nastaven veřejný vyrovnávání zatížení, protože uživatelé mají přístup k aplikaci EBS přes web. Vyrovnávání zatížení distribuuje zatížení do obou počítačů ve střední vrstvě. Chcete-li získat větší zabezpečení, povolte přenos y pouze od uživatelů, kteří přistupují k systému z podnikové sítě pomocí sítě VPN site-to-site nebo ExpressRoute a skupin zabezpečení sítě.

### <a name="database-tier"></a>Databázová vrstva

Tato úroveň je hostitelem databáze Oracle a je rozdělena do vlastní podsítě. Doporučujeme přidat skupiny zabezpečení sítě, které povolují pouze provoz z aplikační vrstvy na databázovou vrstvu na databázovém portu 1521 specifickém pro Oracle.

Společnosti Microsoft a Oracle doporučují nastavení s vysokou dostupností. Vysoké dostupnosti v Azure lze dosáhnout nastavením dvou databází Oracle ve dvou zónách dostupnosti pomocí služby Oracle Data Guard nebo použitím služby Oracle Database Exadata Cloud Service v OCI. Při použití služby Oracle Database Exadata Cloud Service se databáze nasazuje ve dvou podsítích. Databázi Oracle Database můžete také nastavit ve virtuálních počítačích v OCI ve dvou doménách dostupnosti pomocí služby Oracle Data Guard.


### <a name="identity-tier"></a>Úroveň identity

Úroveň identity obsahuje virtuální ms EBS Asserter. EBS Asserter umožňuje synchronizovat identity ze služby Oracle Identity Cloud Service (IDCS) a Azure AD. Ebs Asserter je potřeba, protože EBS nepodporuje protokoly jednotného přihlášení, jako je SAML 2.0 nebo OpenID Connect. Ebs Asserter spotřebovává token připojení OpenID (generovaný IDCS), ověří jej a pak vytvoří relaci pro uživatele v EBS. 

Zatímco tato architektura ukazuje integraci IDCS, jednotný přístup Azure AD a jednotné přihlašování lze také povolit pomocí správce oracle access manageru s adresářem Oracle Internet Directory nebo Oracle Unified Directory. Další informace naleznete v dokumentech white paper o [nasazení systému Oracle EBS s integrací IDCS](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) nebo [nasazení systému Oracle EBS s aplikací OAM Integration](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf).

Pro vysokou dostupnost se doporučuje nasadit redundantní servery ebs asserteru napříč více zónami dostupnosti s nástroje pro vyrovnávání zatížení před ním.

Jakmile je vaše infrastruktura nastavena, lze sadu E-Business Suite nainstalovat podle pokynů k instalaci, které poskytuje společnost Oracle.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

Oracle JD Edwards EnterpriseOne je integrovaná sada aplikací komplexního softwaru pro plánování podnikových zdrojů. Jedná se o vícevrstvou aplikaci, kterou lze nastavit pomocí back-endu databáze Oracle nebo SQL Server. Tato část popisuje podrobnosti o nasazení JD Edwards EnterpriseOne s back-endem databáze Oracle v OCI nebo v Azure.

V následující doporučené architektuře (obrázek 3) se pro správu, prezentaci a střední vrstvy nasazují do virtuální sítě v Azure. Databáze se nasadí ve virtuální cloudové síti v OCI.

Stejně jako u e-business suite můžete nastavit volitelnou úroveň bastionu pro bezpečné administrativní účely. Použijte hostitele virtuálního počítači bastionu jako skokový server pro přístup k instancím aplikací a databáze.

![Architektura JD Edwards EnterpriseOne napříč cloudy](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*Obrázek 3: Architektura JD Edwards EnterpriseOne mezi cloudy*

V této architektuře je virtuální síť v Azure připojena k virtuální cloudové síti v OCI pomocí propojení mezi cloudy. Aplikační vrstva se nastavuje v Azure, zatímco databáze je nastavená v OCI. Doporučujeme nasadit každou komponentu do vlastní podsítě se skupinami zabezpečení sítě, aby byl povolen provoz pouze z určitých podsítí na konkrétních portech.

Architekturu lze také přizpůsobit pro úplné nasazení v Azure s vysoce dostupnými databázemi Oracle nakonfigurovanými pomocí služby Oracle Data Guard ve dvou zónách dostupnosti v oblasti. Následující diagram (obrázek 4) je příkladem tohoto architektonického vzoru:

![Architektura JD Edwards EnterpriseOne pouze pro Azure](media/oracle-oci-applications/jdedwards-arch-azure.png)

*Obrázek 4: Architektura jd Edwards EnterpriseOne pouze pro Azure*

Následující části popisují různé součásti na vysoké úrovni.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Administrativní vrstva

Jak název napovídá, tato úroveň se používá pro úkoly správy. Můžete vyřezat samostatnou podsíť pro úroveň správy. Služby a servery v této vrstvě se používají především pro instalaci a správu aplikace. Proto jsou postačující jednotlivé instance těchto serverů. Redundantní instance nejsou vyžadovány pro vysokou dostupnost vaší aplikace.

Součásti této vrstvy jsou následující:
    
 - **Zřizovací server** – tento server se používá pro nasazení mezi koncovými místy různých součástí aplikace. Komunikuje s instancemi v jiných vrstvách, včetně instancí v databázové vrstvě přes port 22. Je hostitelem konzoly Správce serveru pro JD Edwards EnterpriseOne.
 - **Nasazení serveru** - Tento server je primárně vyžadován pro instalaci JD Edwards EnterpriseOne. Během procesu instalace tento server funguje jako centrální úložiště pro požadované soubory a instalační balíčky. Software je distribuován nebo nasazen na jiné servery a klienty z tohoto serveru.
 - **Vývojový klient** - Tento server obsahuje součásti, které běží ve webovém prohlížeči, stejně jako nativní aplikace.

### <a name="presentation-tier"></a>Prezentační úroveň

Tato úroveň obsahuje různé součásti, jako je například Application Interface Services (AIS), Application Development Framework (ADF) a Java Aplikační servery (JAS). Servery v této vrstvě komunikují se servery ve střední vrstvě. Jsou předváděny systémem vyrovnávání zatížení, který směruje provoz na potřebný server na základě čísla portu a adresy URL, na které je provoz přijat. Doporučujeme nasadit více instancí každého typu serveru pro vysokou dostupnost.

Níže jsou komponenty v této vrstvě:
    
- **Application Interface Services (AIS)** - Server AIS poskytuje komunikační rozhraní mezi mobilními podnikovými aplikacemi JD Edwards EnterpriseOne a JD Edwards EnterpriseOne.
- **Java Aplikační server (JAS)** - JAS přijímá požadavky z vykladače zatížení a předá je střední vrstvě k provádění složitých úloh. JAS má schopnost provádět jednoduchou obchodní logiku.
- **BI Publisher Server (BIP)** - Tento server prezentuje sestavy založené na datech shromážděných aplikací JD Edwards EnterpriseOne. Můžete navrhnout a řídit, jak sestava prezentuje data na základě různých šablon.
- **Server podnikových služeb (BSS)** – BSS umožňuje výměnu informací a interoperabilitu s jinými aplikacemi Oracle.
- **Real-Time Events Server (RTE)** - RTE Server umožňuje nastavit oznámení externím systémům o transakcích, ke kterým dochází v systému JDE EnterpriseOne. Používá model odběratele a umožňuje systémům třetích stran přihlásit k odběru událostí. Chcete-li požadavky na vyrovnávání zatížení obou serverů RTE, ujistěte se, že jsou servery v clusteru.
- **Server ADF (Application Development Framework)** – Server ADF se používá ke spouštění aplikací JD Edwards EnterpriseOne vyvinutých s aplikací Oracle ADF. Tato možnost je nasazena na serveru Oracle WebLogic s modulem runtime ADF.

### <a name="middle-tier"></a>Střední vrstva

Střední vrstva obsahuje logický server a dávkový server. V tomto případě jsou oba servery nainstalovány ve stejném virtuálním počítači. Pro produkční scénáře se však doporučuje nasadit logický server a dávkový server na samostatných serverech. Více serverů jsou nasazeny ve střední vrstvě ve dvou zónách dostupnosti pro vyšší dostupnost. Azure vyrovnávání zatížení by měla být vytvořena a tyto servery by měly být umístěny v back-endovém fondu zajistit, že oba servery jsou aktivní a zpracování požadavků.

Servery střední vrstvy přijímají požadavky od serverů v prezentační vrstvě a pouze z veřejného vykladače zatížení. Pravidla skupiny zabezpečení sítě musí být nastavena tak, aby odepřela provoz z jiné adresy než podsítě prezentační vrstvy a vykladače zatížení. Pravidlo nsg lze také nastavit tak, aby umožňovalo provoz na portu 22 z hostitele bastionu pro účely správy. Můžete být schopni použít veřejný vyrovnávání zatížení k vyrovnávání zatížení požadavky mezi virtuálními virtuálními stránkami ve střední vrstvě.

Následující dvě součásti jsou ve střední vrstvě:

- **Logický server** - Obsahují obchodní logiku nebo obchodní funkce.
- **Dávkový server** – používá se pro dávkové zpracování

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>Peoplesoft

Sada aplikací PeopleSoft společnosti Oracle obsahuje software pro lidské zdroje a finanční řízení. Sada aplikací je vícevrstvá a aplikace zahrnují systémy řízení lidských zdrojů (HRMS), řízení vztahů se zákazníky (CRM), finance a řízení dodavatelského řetězce (FSCM) a řízení výkonnosti podniku (EPM).

Doporučuje se, aby každá úroveň softwarové sady byla nasazena ve vlastní podsíti. Jako back-endová databáze aplikace je vyžadována databáze Oracle nebo Microsoft SQL Server. Tato část popisuje podrobnosti o nasazení PeopleSoft s back-endem databáze Oracle.

Následuje kanonické architektury pro nasazení sady aplikací PeopleSoft v architektuře mezi cloudy (Obrázek 5).

![Architektura PeopleSoft napříč cloudy](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*Obrázek 5: Architektura peoplesoft cross-cloud*

V této ukázkové architektuře je virtuální síť v Azure připojená k virtuální cloudové síti v OCI pomocí propojení mezi cloudy. Aplikační vrstva se nastavuje v Azure, zatímco databáze je nastavená v OCI. Doporučujeme nasadit každou komponentu do vlastní podsítě se skupinami zabezpečení sítě, aby byl povolen provoz pouze z určitých podsítí na konkrétních portech.

Architekturu lze také přizpůsobit pro úplné nasazení v Azure s vysoce dostupnými databázemi Oracle nakonfigurovanými pomocí služby Oracle Data Guard ve dvou zónách dostupnosti v oblasti. Následující diagram (obrázek 6) je příkladem tohoto architektonického vzoru:

![Architektura pouze pro PeopleSoft Azure](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*Obrázek 6: Architektura pouze pro PeopleSoft Azure*

Následující části popisují různé součásti na vysoké úrovni.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Aplikační úroveň

Aplikační vrstva obsahuje instance aplikačních serverů PeopleSoft, webových serverů PeopleSoft, elastického vyhledávání a plánovače procesů PeopleSoft. Azure vyrovnávání zatížení je nastavena tak, aby přijímat požadavky od uživatelů, které jsou směrovány na příslušný server v aplikační vrstvě.

Pokud hledáte vysokou dostupnost, zvažte nastavení redundantních instancí jednotlivých serverů v aplikační vrstvě v různých zónách dostupnosti. Nástroj pro vyrovnávání zatížení Azure lze nastavit s více back-endové fondy nasměrovat každý požadavek na správný server.

### <a name="peopletools-client"></a>Klient PeopleTools

Klient PeopleTools se používá k provádění činností správy, jako je vývoj, migrace a upgrade. Vzhledem k tomu, že klient PeopleTools není vyžadován pro dosažení vysoké dostupnosti vaší aplikace, redundantní servery peopletools klienta nejsou potřeba.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Další kroky

Pomocí [skriptů Terraform](https://github.com/microsoft/azure-oracle) můžete nastavit aplikace Oracle v Azure a navázat připojení mezi cloudy pomocí OCI.

Další informace a dokumenty white paper o oci najdete v dokumentaci [k řešení Oracle Cloud.](https://docs.cloud.oracle.com/iaas/Content/home.htm)
