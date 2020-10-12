---
title: Architektury pro nasazení aplikací Oracle v Azure Virtual Machines | Microsoft Docs
description: Architektury aplikací pro nasazení aplikací Oracle, včetně E-Business Suite, řešení JD Edwards EnterpriseOne a PeopleSoft spouštěných místně na Microsoft Azure virtuálních počítačích s databázemi v Azure nebo v architektuře Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: dbakevlar
manager: ''
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: kegorman
ms.custom: ''
ms.openlocfilehash: 838bd2014f543747a3c3ec7edee7b278f5f4d8df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91274598"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Architektury pro nasazení aplikací Oracle v Azure

Společnosti Microsoft a Oracle společně spolupracovaly, aby zákazníkům umožnily nasazovat aplikace Oracle, jako je Oracle E-Business Suite, řešení JD Edwards EnterpriseOne a PeopleSoft spouštěných místně v cloudu. Díky zavedení verze Preview [Private Network vzájemné propojení](configure-azure-oci-networking.md) mezi Microsoft Azure a Oracle Cloud Infrastructure (OCI) se teď dají aplikace Oracle nasadit na Azure s jejich back-endové databáze v Azure nebo OCI. Aplikace Oracle je také možné integrovat s Azure Active Directory, což vám umožní nastavit jednotné přihlašování, aby se uživatelé mohli přihlašovat k aplikaci Oracle pomocí svých přihlašovacích údajů Azure Active Directory (Azure AD).

OCI nabízí více možností databáze Oracle pro aplikace Oracle, včetně DBaaS, cloudové služby Exadata, Oracle RAC a infrastruktury jako služby (IaaS). V současné době není autonomní databáze podporovaná back-end aplikacemi Oracle.

Pro nasazení aplikací Oracle v Azure je k dispozici [několik možností](oracle-overview.md) , včetně vysoce dostupného a zabezpečeného způsobu. Azure také nabízí [image virtuálních počítačů Oracle Database](oracle-vm-solutions.md) , které můžete nasadit, pokud se rozhodnete spouštět aplikace Oracle výhradně v Azure.

Následující oddíly popisují doporučení architektury od Microsoftu a Oracle k nasazení Oracle E-Business Suite, řešení JD Edwards EnterpriseOne a PeopleSoft spouštěných místně v konfiguraci mezi cloudy nebo výhradně v Azure. Společnost Microsoft a Oracle testovaly tyto aplikace a potvrzují, že výkon splňuje standardy nastavené Oracle pro tyto aplikace.

## <a name="architecture-considerations"></a>Požadavky architektury

Aplikace Oracle se skládají z několika služeb, které je možné hostovat na stejném nebo několika virtuálních počítačích v Azure a volitelně v rozhraní OCI. 

Instance aplikace lze nastavit pomocí privátních nebo veřejných koncových bodů. Microsoft a Oracle doporučují nastavit *virtuální počítač hostitele bastionu* s veřejnou IP adresou v samostatné podsíti pro správu aplikace. Pak přiřaďte k ostatním počítačům jenom soukromé IP adresy, včetně databázové vrstvy. 

Při nastavování aplikace v architektuře mezi cloudy se vyžaduje plánování, aby se zajistilo, že adresní prostor IP adres ve virtuální síti Azure nepřekrývá privátní adresní prostor IP adres ve virtuální cloudové síti OCI.

Pro zvýšení zabezpečení nastavte skupiny zabezpečení sítě na úrovni podsítě, aby se zajistilo, že bude povolený jenom provoz na konkrétních portech a IP adresách. Například počítače na střední úrovni by měly přijímat jenom přenosy z virtuální sítě. Žádný externí provoz by neměl přímo kontaktovat počítače střední vrstvy.

Pro zajištění vysoké dostupnosti můžete nastavit redundantní instance různých serverů ve stejné skupině dostupnosti nebo v různých zónách dostupnosti. Zóny dostupnosti umožňují dosáhnout smlouvy SLA o 99,99% provozu, zatímco sady dostupnosti vám umožňují dosáhnout 99,95% provozu smlouvy SLA v oblasti. Ukázkové architektury uvedené v tomto článku jsou nasazené ve dvou zónách dostupnosti.

Při nasazování aplikace pomocí propojení mezi cloudy můžete nadále používat stávající okruh ExpressRoute k připojení prostředí Azure k místní síti. Pro propojení na rozhraní OCI ale potřebujete samostatný okruh ExpressRoute, než je ten, který se připojuje k vaší místní síti.

## <a name="e-business-suite"></a>Sada E-Business

Oracle E-Business Suite (EBS) je sada aplikací, včetně správy dodavatelských řetězců (SCM) a správy vztahů se zákazníky (CRM). Aby bylo možné využít portfolio spravované databáze OCI, EBS se dá nasadit pomocí propojení mezi cloudy mezi Microsoft Azure a OCI. V této konfiguraci jsou prezentační a aplikační vrstvy spuštěné v Azure a databázová vrstva v rozhraní OCI, jak je znázorněno v následujícím diagramu architektury (obrázek 1).

![Architektura elektronického cloudu pro E-Business Suite](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*Obrázek 1: architektura elektronického cloudu sady E-Business Suite* 

V této architektuře je virtuální síť v Azure připojená k virtuálnímu cloudové síti v rozhraní OCI pomocí propojení mezi cloudy. Aplikační vrstva je nastavená v Azure, zatímco databáze je nastavená v rozhraní OCI. Doporučuje se nasazovat jednotlivé komponenty do vlastní podsítě se skupinami zabezpečení sítě, aby se povolily přenosy jenom z konkrétních podsítí na konkrétní porty.

Architekturu je taky možné přizpůsobit pro nasazení výhradně v Azure s vysoce dostupnými databázemi Oracle nakonfigurovanými pomocí Oracle data Guard ve dvou zónách dostupnosti v oblasti. Následující diagram (obrázek 2) je příkladem tohoto modelu architektury:

![Architektura E-Business Suite pouze pro Azure](media/oracle-oci-applications/ebs-arch-azure.png)

*Obrázek 2: architektura E-Business Suite pouze pro Azure*

V následujících částech jsou popsány různé komponenty na nejvyšší úrovni.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Úroveň aplikace (střední)

Aplikační vrstva je izolovaná ve vlastní podsíti. Pro zajištění odolnosti proti chybám a správu snadné opravy je nastaveno několik virtuálních počítačů. Tyto virtuální počítače se dají zálohovat pomocí sdíleného úložiště, které nabízí Azure NetApp Files a Ultra SSD. Tato konfigurace umožňuje snazší nasazení oprav bez výpadků. Na počítače v aplikační vrstvě by měl být umístěn veřejný Nástroj pro vyrovnávání zatížení, aby se požadavky na aplikační vrstvu EBS zpracovaly i v případě, že je jeden počítač v této vrstvě offline z důvodu chyby.

### <a name="load-balancer"></a>Nástroj pro vyrovnávání zatížení

Nástroj pro vyrovnávání zatížení Azure umožňuje distribuovat provoz napříč několika instancemi vašich úloh, aby se zajistila vysoká dostupnost. V takovém případě je nastaven veřejný Nástroj pro vyrovnávání zatížení, protože uživatelé mají povolený přístup k aplikaci EBS přes web. Nástroj pro vyrovnávání zatížení distribuuje zatížení do obou počítačů ve střední vrstvě. Pro zvýšení zabezpečení povolte provoz pouze uživatelům, kteří přistupují k systému z podnikové sítě pomocí sítě VPN typu Site-to-site nebo ExpressRoute a skupin zabezpečení sítě.

### <a name="database-tier"></a>Databázová vrstva

Tato vrstva je hostitelem databáze Oracle a je rozdělená do vlastní podsítě. Doporučuje se přidat skupiny zabezpečení sítě, které povolují jenom provoz z aplikační vrstvy, do databázové vrstvy na portu databáze pro konkrétní Oracle 1521.

Microsoft a Oracle doporučují nastavení vysoké dostupnosti. Vysokou dostupnost v Azure je možné dosáhnout nastavením dvou databází Oracle ve dvou zónách dostupnosti pomocí Oracle data Guard nebo pomocí cloudové služby Oracle Database Exadata v OCI. Když používáte cloudovou službu Oracle Database Exadata, vaše databáze je nasazená ve dvou podsítích. Můžete také nastavit Oracle Database ve virtuálních počítačích v OCI ve dvou doménách dostupnosti pomocí Oracle data Guard.


### <a name="identity-tier"></a>Úroveň identity

Vrstva identity obsahuje virtuální počítač EBS Assert. EBS Assert umožňuje synchronizovat identity ze služby Oracle identity Cloud Service (IDCS) a Azure AD. Je potřeba EBS Assert, protože EBS nepodporuje protokoly jednotného přihlašování jako SAML 2,0 nebo OpenID Connect. EBS Assert spotřebovává token OpenID Connect (generovaný IDCS), ověří ho a pak vytvoří relaci pro uživatele v EBS. 

I když tato architektura ukazuje integraci IDCS, jednotné přístupu ke službě Azure AD a jednotné přihlašování je taky možné povolit pomocí Oracle Access Manageru s použitím adresáře Oracle Internet Directory nebo Oracle Unified Directory. Další informace najdete v dokumentu White paper o [nasazení Oracle EBS s integrací IDCS](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) nebo [nasazení Oracle EBS s integrací OAM](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf).

Pro zajištění vysoké dostupnosti doporučujeme nasadit redundantní servery EBSého vyhodnocení v několika zónách dostupnosti s nástrojem pro vyrovnávání zatížení před ním.

Po nastavení infrastruktury se dá E-Business Suite nainstalovat pomocí instalační příručky, kterou poskytuje Oracle.

## <a name="jd-edwards-enterpriseone"></a>ŘEŠENÍ JD Edwards EnterpriseOne

Oracle řešení JD Edwards EnterpriseOne je integrovaná aplikace pro komplexní software pro plánování podnikových prostředků. Jedná se o vícevrstvou aplikaci, kterou je možné nastavit buď pomocí back-endu Oracle, nebo SQL Server databáze. Tato část popisuje podrobnosti o nasazení řešení JD Edwards EnterpriseOne pomocí back-endu databáze Oracle buď v rozhraní OCI nebo v Azure.

V následující doporučené architektuře (obrázek 3) jsou Správa, prezentace a prostřední vrstvy nasazené do virtuální sítě v Azure. Databáze je nasazena ve virtuálním cloudové síti v rozhraní OCI.

Stejně jako u E-Business Suite můžete nastavit volitelnou bastionu vrstvu pro účely zabezpečené správy. Pro přístup k instancím aplikace a databáze použijte jako můstek server hostitele virtuálního počítače bastionu.

![ŘEŠENÍ JD Edwards EnterpriseOne mezi cloudovou architekturou](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*Obrázek 3: řešení JD Edwards EnterpriseOne architektura mezi cloudy*

V této architektuře je virtuální síť v Azure připojená k virtuálnímu cloudové síti v rozhraní OCI pomocí propojení mezi cloudy. Aplikační vrstva je nastavená v Azure, zatímco databáze je nastavená v rozhraní OCI. Doporučuje se nasazovat jednotlivé komponenty do vlastní podsítě se skupinami zabezpečení sítě, aby se povolily přenosy jenom z konkrétních podsítí na konkrétní porty.

Architekturu je taky možné přizpůsobit pro nasazení výhradně v Azure s vysoce dostupnými databázemi Oracle nakonfigurovanými pomocí Oracle data Guard ve dvou zónách dostupnosti v oblasti. Následující diagram (obrázek 4) je příkladem tohoto modelu architektury:

![Architektura řešení JD Edwards EnterpriseOne jenom pro Azure](media/oracle-oci-applications/jdedwards-arch-azure.png)

*Obrázek 4: řešení JD Edwards EnterpriseOne architektura jenom pro Azure*

V následujících částech jsou popsány různé komponenty na nejvyšší úrovni.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Úroveň správy

Jak název navrhuje, tato vrstva se používá pro úlohy správy. Pro vrstvu správy můžete vyčlenit samostatnou podsíť. Služby a servery v této vrstvě se primárně používají k instalaci a správě aplikace. Jednotlivé instance těchto serverů jsou proto dostačující. Redundantní instance nejsou požadovány pro vysokou dostupnost vaší aplikace.

Komponenty této vrstvy jsou následující:
    
 - **Zřizování serveru** – tento server se používá pro komplexní nasazení různých komponent aplikace. Komunikuje s instancemi v ostatních vrstvách, včetně instancí v databázové vrstvě, přes port 22. Hostuje konzolu Správce serveru řešení JD Edwards EnterpriseOne.
 - **Server nasazení** – tento server je primárně vyžadován pro instalaci řešení JD Edwards EnterpriseOne. Během procesu instalace tento server funguje jako centrální úložiště pro požadované soubory a instalační balíčky. Software se distribuuje nebo nasadí na jiné servery a klienty z tohoto serveru.
 - **Vývojový klient** – tento server obsahuje komponenty, které běží ve webovém prohlížeči, i v nativních aplikacích.

### <a name="presentation-tier"></a>Prezentační úroveň

Tato vrstva obsahuje různé komponenty, jako jsou aplikační rozhraní (AIS), Application Development Framework (ADF) a aplikační servery Java (agenti úloh). Servery v této vrstvě komunikují se servery ve střední vrstvě. Jsou založené na nástroji pro vyrovnávání zatížení, který směruje provoz na potřebný Server na základě čísla portu a adresy URL, na které se příchozí přenosy dostanou. Doporučuje se nasadit více instancí každého typu serveru pro zajištění vysoké dostupnosti.

V této vrstvě jsou tyto komponenty:
    
- **Služby aplikačního rozhraní (ais)** – Server AIS poskytuje komunikační rozhraní mezi řešení JD Edwards EnterpriseOne Mobile Enterprise Applications a řešení JD Edwards EnterpriseOne.
- **Aplikační server Java (agenti úloh)** – agenti úloh přijímá žádosti od nástroje pro vyrovnávání zatížení a předává je do prostřední úrovně pro provádění složitých úloh. AGENTI úloh má možnost spouštět jednoduchou obchodní logiku.
- **Server BI Publisher (BIP)** – tento server prezentuje sestavy na základě dat shromažďovaných aplikací řešení JD Edwards EnterpriseOne. Můžete navrhovat a řídit, jak sestava prezentuje data na základě různých šablon.
- **Server pro obchodní služby (BSS)** – služba BSS umožňuje výměnu informací a spolupráci s ostatními aplikacemi Oracle.
- **Server událostí v reálném čase (RTE)** – Server Rte umožňuje nastavit oznámení pro externí systémy o transakcích, ke kterým dochází v systému jde EnterpriseOne. Používá model odběratele a umožňuje systémům třetích stran přihlásit se k odběru událostí. Pro vyrovnávání zatížení žádostí na oba servery RTE zajistěte, aby byly servery v clusteru.
- **Server pro vývoj aplikací (ADF)** – Server ADF se používá ke spouštění aplikací řešení JD Edwards EnterpriseOne vyvinutých pomocí nástroje Oracle ADF. Tento postup je nasazený na serveru Oracle WebLogic Server s modulem runtime ADF.

### <a name="middle-tier"></a>Střední vrstva

Střední vrstva obsahuje server logiky a server Batch. V takovém případě jsou oba servery nainstalovány na stejném virtuálním počítači. V produkčních scénářích se však doporučuje nasadit server Logic Server a Batch na samostatné servery. Více serverů je nasazeno v prostřední vrstvě v rámci dvou zón dostupnosti pro zajištění vyšší dostupnosti. Vytvoří se nástroj pro vyrovnávání zatížení Azure a tyto servery by se měly umístit do back-endu fondu, aby se zajistilo, že oba servery budou aktivní a zpracovávají se požadavky.

Servery v prostřední vrstvě přijímají požadavky ze serverů v prezentační vrstvě a veřejného nástroje pro vyrovnávání zatížení. Pravidla skupiny zabezpečení sítě musí být nastavená tak, aby odepřela provoz z jiné adresy než z podsítě prezentační vrstvy a nástroje pro vyrovnávání zatížení. Je také možné nastavit pravidlo NSG, které povoluje provoz na portu 22 z hostitele bastionu pro účely správy. K vyrovnávání zatížení žádostí mezi virtuálními počítači v prostřední vrstvě můžete použít veřejný Nástroj pro vyrovnávání zatížení.

Střední vrstva má následující dvě komponenty:

- **Logic Server** – obsahuje obchodní logiku nebo obchodní funkce.
- **Batch Server** – používá se pro dávkové zpracování

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft spouštěných místně

Sada PeopleSoft spouštěných místně aplikací Oracle obsahuje software pro lidské zdroje a finanční řízení. Sada aplikací je Vícevrstvá a aplikace zahrnuje systémy správy lidských zdrojů (HRMS), řízení vztahů se zákazníky (CRM), finance a Správa dodavatelských řetězců (FSCM) a Enterprise Performance Management (EPM).

Doporučuje se, aby se jednotlivé úrovně sady softwaru nasadily ve vlastní podsíti. Jako back-end databáze aplikace je třeba databáze Oracle nebo Microsoft SQL Server. Tato část popisuje podrobnosti o nasazení PeopleSoft spouštěných místně s back-endu Oracle Database.

Následuje kanonická architektura pro nasazení aplikace PeopleSoft spouštěných místně v architektuře pro více cloudů (obrázek 5).

![Architektura PeopleSoft spouštěných místně mezi cloudy](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*Obrázek 5: architektura PeopleSoft spouštěných místně mezi cloudy*

V této ukázkové architektuře je virtuální síť v Azure připojená k virtuálnímu cloudové síti v rozhraní OCI pomocí propojení mezi cloudy. Aplikační vrstva je nastavená v Azure, zatímco databáze je nastavená v rozhraní OCI. Doporučuje se nasazovat jednotlivé komponenty do vlastní podsítě se skupinami zabezpečení sítě, aby se povolily přenosy jenom z konkrétních podsítí na konkrétní porty.

Architekturu je taky možné přizpůsobit pro nasazení výhradně v Azure s vysoce dostupnými databázemi Oracle nakonfigurovanými pomocí Oracle data Guard ve dvou zónách dostupnosti v oblasti. Následující diagram (obrázek 6) je příkladem tohoto modelu architektury:

![PeopleSoft spouštěných místně architektura jenom pro Azure](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*Obrázek 6: PeopleSoft spouštěných místně architektura jenom pro Azure*

V následujících částech jsou popsány různé komponenty na nejvyšší úrovni.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Aplikační úroveň

Aplikační vrstva obsahuje instance aplikačních serverů PeopleSoft spouštěných místně, webových serverů PeopleSoft spouštěných místně, elastického hledání a plánovače procesů PeopleSoft spouštěných místně. Nástroj pro vyrovnávání zatížení Azure je nastavený tak, aby přijímal požadavky od uživatelů, které jsou směrované na příslušný server v aplikační vrstvě.

Pro zajištění vysoké dostupnosti zvažte nastavení redundantních instancí každého serveru v aplikační vrstvě v různých zónách dostupnosti. Nástroj pro vyrovnávání zatížení Azure je možné nastavit s více fondy back-endu, aby každý požadavek nasměroval na správný server.

### <a name="peopletools-client"></a>Klient PeopleTools

Klient PeopleTools se používá k provádění aktivit správy, jako je vývoj, migrace a upgrade. Vzhledem k tomu, že klient PeopleTools není pro dosažení vysoké dostupnosti vaší aplikace nutný, nejsou potřeba redundantní servery klienta PeopleTools.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Další kroky

Pomocí [skriptů terraformu](https://github.com/microsoft/azure-oracle) můžete nastavit aplikace Oracle v Azure a navázat připojení mezi cloudem pomocí OCI.

Další informace a dokumenty White paper o rozhraní OCI najdete v dokumentaci ke [cloudu Oracle](https://docs.cloud.oracle.com/iaas/Content/home.htm) .
