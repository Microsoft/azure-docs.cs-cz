---
title: Komponenty a hranice informačních systémů Azure
description: Tento článek poskytuje obecný popis Microsoft Azure architektury a správy.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 68535f70507e7a81d217f4148314a3d76ec832ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "68727199"
---
# <a name="azure-information-system-components-and-boundaries"></a>Komponenty a hranice informačních systémů Azure
Tento článek poskytuje obecný popis architektury a správy Azure. Prostředí systému Azure je tvořeno následujícími sítěmi:

- Microsoft Azure produkční síť (síť Azure)
- Podniková síť Microsoft (Corpnet)

Oddělení IT oddělení zodpovídá za provoz a údržbu těchto sítí.

## <a name="azure-architecture"></a>Architektura Azure
Azure je platforma pro cloud computing a infrastruktura pro sestavování, nasazování a správu aplikací a služeb prostřednictvím sítě datových center. Tato datová centra spravuje společnost Microsoft. Na základě počtu prostředků, které zadáte, Azure vytvoří virtuální počítače podle potřeby prostředků. Tyto virtuální počítače běží na hypervisoru Azure, který je určený pro použití v cloudu a není přístupný pro veřejnost.

Na každém uzlu fyzického serveru Azure je k dispozici hypervisor, který běží přímo na hardwaru. Hypervisor rozdělí uzel na proměnný počet hostovaných virtuálních počítačů. Každý uzel má také jeden kořenový virtuální počítač, který spouští hostitelský operační systém. Na každém virtuálním počítači je povolená brána Windows Firewall. Můžete definovat, které porty jsou adresovatelné konfigurací definičního souboru služby. Tyto porty jsou jediné otevřené a adresovatelné, interně nebo externě. Veškerý provoz a přístup k disku a síti je vypravuje hypervisorem a kořenovým operačním systémem.

Na hostitelské vrstvě používají virtuální počítače Azure přizpůsobenou a posílenou verzi nejnovějšího Windows serveru. Azure používá verzi Windows serveru, která zahrnuje jenom ty součásti, které jsou potřebné pro hostování virtuálních počítačů. Tím se zlepší výkon a omezí se na něj útok. Hypervisor vynutil hranice počítačů, které nejsou závislé na zabezpečení operačního systému.

### <a name="azure-management-by-fabric-controllers"></a>Správa Azure podle řadičů infrastruktury

V Azure se virtuální počítače běžící na fyzických serverech (na uzlech a uzlech) seskupují do clusterů o asi 1000. Virtuální počítače se nezávisle spravují komponentou se škálováním na více instancí a redundantní platformou softwaru označované jako kontroleru prostředků infrastruktury (FC).

Každá funkce FC spravuje životní cyklus aplikací spuštěných ve svém clusteru a zřizuje a monitoruje stav hardwaru pod jeho kontrolou. Spouští samostatné operace, například instance virtuálních počítačů reincarnating na zdravých serverech, když zjistí, že se server nezdařil. FC také provádí operace správy aplikací, jako je nasazení, aktualizace a škálování aplikací.

Datové centrum je rozdělené do clusterů. Clustery izolují chyby na úrovni FC a zabrání určitým třídám chyb v tom, aby ovlivnily servery přesahující cluster, ve kterém k nim dochází. FCs, který obsluhuje konkrétní cluster Azure, se seskupí do clusteru FC.

### <a name="hardware-inventory"></a>Inventář hardwaru

Technologie FC připraví inventář hardwarových a síťových zařízení Azure během procesu konfigurace zavedení. Všechny nové hardwarové a síťové součásti, které vstupují do produkčního prostředí Azure, se musí řídit procesem konfigurace Bootstrap. Technologie FC zodpovídá za správu celého inventáře, který je uvedený v konfiguračním souboru datacenter.xml.

### <a name="fc-managed-operating-system-images"></a>Bitové kopie operačního systému spravované FC

Tým operačního systému poskytuje image ve formě virtuálních pevných disků, které jsou nasazené na všech hostitelích a virtuálních počítačích hosta v produkčním prostředí Azure. Tým sestaví tyto základní Image pomocí automatizovaného procesu sestavení v režimu offline. Základní Image je verze operačního systému, ve kterém se jádro a další základní součásti změnily a optimalizují tak, aby podporovaly prostředí Azure.

Existují tři typy imagí operačního systému spravovaných prostředky infrastruktury:

- Hostitel: přizpůsobený operační systém, který běží na virtuálních počítačích hostitele.
- Nativní: nativní operační systém, který běží na klientech (například Azure Storage). Tento operační systém nemá žádný hypervisor.
- Host: hostovaný operační systém, který běží na virtuálních počítačích hosta.

Hostitelské a nativní operační systémy spravované pomocí technologie FC jsou navržené pro použití v cloudu a nejsou veřejně přístupné.

#### <a name="host-and-native-operating-systems"></a>Hostitelské a nativní operační systémy

Hostitel a nativní jsou posílené image operačních systémů, které hostují agenty prostředků infrastruktury, a běží na výpočetním uzlu (spouští se jako první virtuální počítač v uzlu) a na uzlech úložiště. Výhodou použití optimalizovaných základních imagí hostitele a nativního je to, že snižuje plochu vystavenou rozhraními API nebo nepoužívanými součástmi. Ty mohou představovat vysoce zabezpečená rizika a zvyšovat nároky na operační systém. Omezené operační systémy obsahují pouze součásti, které jsou nezbytné pro Azure.

#### <a name="guest-operating-system"></a>Hostovaný operační systém

U interních komponent Azure, které běží na virtuálních počítačích hostovaných operačních systémů, nejsou žádné možnosti pro spuštění protokol RDP (Remote Desktop Protocol). Všechny změny nastavení konfigurace standardních hodnot musí projít procesem správy změn a vydaných verzí.

## <a name="azure-datacenters"></a>datacentra Azure
Tým Microsoft Cloud infrastruktury a provozu (MCIO) spravuje fyzickou infrastrukturu a zařízení Datacenter pro všechny online služby společnosti Microsoft. MCIO je primárně zodpovědný za správu fyzického a environmentálního řízení v datových centrech a také pro správu a podporu zařízení s vnější hraniční sítí (jako jsou hraniční směrovače a směrovače Datacenter). MCIO je také zodpovědný za nastavení minimálního hardwarového serveru v rackech v datacentru. Zákazníci nemají žádnou přímou interakci s Azure.

## <a name="service-management-and-service-teams"></a>Týmy pro správu a údržbu služeb
Různé technické skupiny označované jako týmy služeb spravují podporu služby Azure. Každý tým služby je zodpovědný za oblast podpory Azure. Každý tým služby musí učinit nepřetržitě dostupný inženýr k prozkoumání a řešení chyb ve službě. Týmy služby ve výchozím nastavení nemají fyzický přístup k hardwaru pracujícímu v Azure.

Týmy služby jsou:

- Aplikační platforma
- Azure Active Directory
- Azure Compute
- Azure NET
- Služby pro cloudovou inženýry
- ISSD: zabezpečení
- Vícefaktorového ověřování
- Databáze SQL
- Storage

## <a name="types-of-users"></a>Typy uživatelů
Zaměstnanci (nebo dodavatelé) Microsoftu se považují za interní uživatele. Všichni ostatní uživatelé se považují za externí uživatele. Všichni interní uživatelé Azure mají svůj stav zaměstnanců zařazený do kategorie s úrovní citlivosti definující přístup k zákaznickým datům (přístup nebo přístup bez přístupu). Uživatelská oprávnění k Azure (oprávnění k autorizaci po provedení ověření) jsou popsána v následující tabulce:

| Role | Interní nebo externí | Úroveň citlivosti | Prováděná oprávnění a funkce | Typ přístupu
| --- | --- | --- | --- | --- |
| Inženýr datacenter Azure | Interní | Žádný přístup k zákaznickým datům | Spravujte fyzické zabezpečení místního prostředí. V datovém centru se budete řídit a sledovat všechny vstupní body. Členové se dodávají do datového centra a vymazali zaměstnanci, kteří poskytují obecné služby (například stravování nebo čištění) nebo pracují v rámci datového centra. Provádějte běžné monitorování a údržbu síťového hardwaru. Pomocí nejrůznějších nástrojů proveďte správu incidentů a opravte práci. Provádějte běžné monitorování a údržbu fyzického hardwaru v datových centrech. Přístup k prostředí na vyžádání z vlastníků vlastností. Umožňuje provádět Forenzní vyšetřování, zaznamenávat zprávy o incidentech a vyžadovat požadavky na povinné školení a zásady zabezpečení. Provozní vlastnictví a údržba důležitých nástrojů zabezpečení, jako jsou například skenery a shromažďování protokolů. | Trvalý přístup k prostředí. |
| Třídění incidentů v Azure (technici rychlých reakcí) | Interní | Přístup k zákaznickým datům | Spravujte komunikaci mezi MCIO, podporou a technickými týmy. Incidenty platforem, problémy s nasazením a žádosti o služby. | Přístup k prostředí za běhu s omezeným trvalým přístupem k systémům, kteří nejsou zákazníky. |
| Technici nasazení Azure | Interní | Přístup k zákaznickým datům | Nasazení a upgrade součástí platformy, softwaru a plánovaných změn konfigurace v podpoře Azure. | Přístup k prostředí za běhu s omezeným trvalým přístupem k systémům, kteří nejsou zákazníky. |
| Podpora výpadků zákazníků Azure (tenant) | Interní | Přístup k zákaznickým datům | Ladění a diagnostika výpadků platforem a chyb pro jednotlivé výpočetní klienty a účty Azure. Analyzovat chyby. Důležité opravy pro platformu nebo zákazníka a technická vylepšení pro celou podporu. | Přístup k prostředí za běhu s omezeným trvalým přístupem k systémům, kteří nejsou zákazníky. |
| Technici na webu Azure Live (technici pro sledování) a incident | Interní | Přístup k zákaznickým datům | Diagnostikujte a zmírnit stav platforem pomocí diagnostických nástrojů. Opravy disků pro ovladače svazků, opravné položky vyplývající z výpadků a napomáhají při nečinnosti při obnovení. | Přístup k prostředí za běhu s omezeným trvalým přístupem k systémům, kteří nejsou zákazníky. |
|Zákazníci Azure | Externí | N/A | N/A | N/A |

Azure používá k ověřování uživatelů a zákazníků organizace jedinečné identifikátory (nebo procesy, které jednají jménem uživatelů organizace). To platí pro všechny prostředky a zařízení, které jsou součástí prostředí Azure.

### <a name="azure-internal-authentication"></a>Interní ověřování Azure

Komunikace mezi interními součástmi Azure je chráněná šifrováním TLS. Ve většině případů jsou certifikáty X. 509 podepsané svým držitelem. Certifikáty s připojeními, která jsou dostupná mimo síť Azure, představují výjimku, stejně jako certifikáty pro FCs. FCs obsahuje certifikáty vydané certifikační autoritou (CA) Microsoftu, která je zajištěna důvěryhodnou kořenovou certifikační autoritou. To umožňuje, aby se veřejné klíče FC převedly v rámci snadného zavedení. Kromě toho Microsoft Developer Tools používá veřejné klíče FC. Když vývojáři odesílají nové image aplikace, obrázky se šifrují pomocí veřejného klíče FC, aby se chránily jakékoli vložené tajné kódy.

### <a name="azure-hardware-device-authentication"></a>Ověřování hardwarových zařízení Azure

FC udržuje sadu přihlašovacích údajů (klíčů a hesel), které se používají k ověřování na různých hardwarových zařízeních pod jejím ovládacím prvkem. Microsoft používá systém k tomu, aby se zabránilo přístupu k těmto přihlašovacím údajům. Konkrétně je přenos, trvalost a používání těchto přihlašovacích údajů navržený tak, aby se zabránilo vývojářům, správcům a službám zálohování v Azure a přístup k citlivým, důvěrným nebo soukromým informacím.

Microsoft používá šifrování na základě veřejného klíče hlavní identity FC. K přenosu přihlašovacích údajů používaných pro přístup k síťovým hardwarovým zařízením dojde v časech nastavení FC a doba rekonfigurace FC. Když FC potřebuje přihlašovací údaje, FC je načte a dešifruje.

### <a name="network-devices"></a>Síťová zařízení

Tým sítě Azure nakonfiguruje účty síťové služby, aby se mohl klient Azure ověřit u síťových zařízení (směrovačů, přepínačů a nástrojů pro vyrovnávání zatížení).

## <a name="secure-service-administration"></a>Správa zabezpečené služby
Pro používání zabezpečených pracovních stanic pro správu (SAWs) se vyžadují pracovní pracovníky Azure. Zákazníci můžou implementovat podobné ovládací prvky pomocí pracovních stanic s privilegovaným přístupem. S SAWs používají administrativní personál samostatně přiřazený účet správce, který je oddělený od standardního uživatelského účtu uživatele. Vytyčené sestavení na tomto účtu rozdělující postupy poskytují pro tyto citlivé účty důvěryhodné pracovní stanice.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá k zabezpečení infrastruktury Azure, najdete tady:

- [Zařízení, místní a fyzické zabezpečení Azure](physical-security.md)
- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Produkční síť Azure](production-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Provozní provoz a Správa Azure](infrastructure-operations.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)
