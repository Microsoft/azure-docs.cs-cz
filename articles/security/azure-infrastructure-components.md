---
title: Součásti systému Azure informace a hranice
description: Tento článek obsahuje obecný popis architektury Microsoft Azure a správu.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: b390dc9bd2b690837a85a5bab361a534b9c9d5a5
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118135"
---
# <a name="azure-information-system-components-and-boundaries"></a>Součásti systému Azure informace a hranice
Tento článek obsahuje obecný popis Azure architekturu a správu. Prostředí systému Azure se skládá z následující sítě:

- Microsoft Azure produkční sítě (Azure)
- Microsoft podnikové sítě (corpnet)

Samostatné týmy IT zodpovídají za provoz a údržba těchto sítích.

## <a name="azure-architecture"></a>Architektura služby Azure
Azure je Cloudová výpočetní platforma a infrastruktura pro vytváření, nasazování a správu aplikací a služeb prostřednictvím sítě datových center. Tato datová centra, spravuje Microsoft. Podle počtu prostředků, kterou zadáte, Azure vytvoří virtuální počítače (VM) podle potřeb prostředků. Tyto virtuální počítače spustit v Azure hypervisoru, který je určený pro použití v cloudu a není přístupná veřejně.

Na každý uzel fyzického serveru Azure je hypervisoru, který běží přímo nad hardwarem. Hypervisor rozdělí uzel proměnný počet virtuálních počítačů hosta. Každý uzel má také jeden kořenový virtuální počítač, který používá operační systém hostitele. Brána Windows Firewall je povolena na každém virtuálním počítači. Můžete definovat, které porty jsou adresovatelné nakonfigurováním definičního souboru služby. Tyto porty jsou pouze ty, které otevřete a adresovatelný, interně nebo externě. Všechny přenosy a přístup k disku a sítě zprostředkována hypervisoru a kořenové operační systém.

Ve vrstvě hostitele, virtuální počítače Azure s vlastní a posílené verzí nejnovější Windows serveru. Azure používá verzi systému Windows Server, který obsahuje pouze součásti potřebné pro hostování virtuálních počítačů. To zvyšuje výkon a snižuje prostor pro napadení. Počítač hranice se vynucují na hypervisoru, které nejsou závislé na zabezpečení operačního systému.

### <a name="azure-management-by-fabric-controllers"></a>Azure management od kontrolerů prostředků infrastruktury

V Azure jsou virtuální počítače spuštěné na fyzických serverech (servery blade/uzly) seskupeny do clusterů přibližně 1 000. Virtuální počítače jsou spravovány nezávisle na sobě komponenty platformy horizontálním navýšením kapacity a redundantní software s názvem kontroler prostředků infrastruktury (FC).

Každý FC spravuje životní cyklus aplikací běžících v jeho cluster a zřizuje a sledovat stav hardwaru pod svou kontrolou. Spouští autonomic operace, jako je například reincarnating instancí virtuálních počítačů na serverech v pořádku, když zjistí, že serveru se nezdařilo. FC také provádí operace správy aplikací, jako je například nasazení, aktualizace nebo horizontální navýšení kapacity aplikace.

Datového centra je rozdělené do clusterů. Clustery izolovat chyby na úrovni FC a zabránit určité třídy chyb by to ovlivnilo servery mimo cluster, ve kterém k nim dojde. FCs, který bude sloužit konkrétní cluster Azure jsou seskupené do clusteru služby FC.

### <a name="hardware-inventory"></a>Inventář hardwaru

FC připraví inventář hardwaru a sítě k zařízením Azure během procesu konfigurace spuštění. Žádný nový hardware a síťové součásti zadávání Azure produkčního prostředí musí následovat po spuštění konfigurace procesu. FC zodpovídá za správu uvedená v konfiguračním souboru datacenter.xml celý inventář.

### <a name="fc-managed-operating-system-images"></a>Bitové kopie operačního systému spravované FC

Operační systém týmu poskytuje obrázků ve formuláři z virtuálních pevných disků, nasadit na všechny hostitele a hostů virtuálních počítačů v Azure produkčního prostředí. Tým vytváří tyto základní Image prostřednictvím automatizovaného offline procesu sestavení. Základní image je verze operačního systému, ve kterém jádra a další součásti core byly upravit a optimalizovaná pro podporu prostředí Azure.

Existují tři typy imagí operačních systémů spravovaných prostředků infrastruktury:

- Hostitel: Vlastní operační systém, který běží na hostiteli virtuálních počítačů.
- Nativní: Nativní operační systém, který běží na klientům (například Azure Storage). Tento operační systém nemá jakéhokoli hypervisoru.
- Host: Hostovaný operační systém, který běží na virtuálních počítačích hosta.

Hostitele a nativní FC spravovat operační systémy jsou určeny k použití v cloudu a nejsou veřejně přístupné.

#### <a name="host-and-native-operating-systems"></a>Hostitele a nativní operačních systémů

Hostitele a nativní jsou Image posílené operačního systému, které hostitele prostředků infrastruktury agentů a běží na výpočetním uzlu (spouští jako první virtuální počítač na uzlu) a uzly úložiště. Výhodou použití optimalizované základní Image z hostitele a nativní je, že ji snižuje plochu vystavenou ani rozhraní API nepoužívané komponenty. Tyto může představovat riziko zabezpečení, vysoké a zvyšovat nároky na operační systém. Operační systémy nízkými nároky na prostředky pouze obsahují součásti potřebné k Azure.

#### <a name="guest-operating-system"></a>Hostovaný operační systém

Interní komponenty Azure běží na hostovaného operačního systému virtuálních počítačů mají nebude mít možnost spustit Remote Desktop Protocol. Jakékoli změny nastavení konfigurace standardních hodnot musí projít změn a správy proces vydávání verzí.

## <a name="azure-datacenters"></a>Datacentra Azure
Tým Microsoft cloudové infrastruktury a operací (MCIO) spravuje fyzických systémů infrastruktury a datacenter pro všechny Microsoft online services. MCIO primárně zodpovídá za správu fyzických a prostředí ovládací prvky v rámci datových center, jakož i správě a podpoře vnější hraniční síti zařízení (např. hraniční směrovače a datacenter směrovače). MCIO zodpovídá také za nastavení úplné minimální serverový hardware v stojanů v datovém centru. Zákazníci mají bez přímé interakce s Azure.

## <a name="service-management-and-service-teams"></a>Správa služeb a týmy služeb
Různé technických skupin, označované jako týmy služeb spravovat podporu služby Azure. Každý tým služba je zodpovědná za oblasti podpory pro Azure. Každý tým služby musíte provést zpětnou analýzu k dispozici 24 x 7 prozkoumat a vyřešit chyby ve službě. Týmy služeb, ve výchozím nastavení, nemají fyzický přístup k hardwaru fungování v Azure.

Týmy služeb jsou:

- Aplikační platforma
- Azure Active Directory
- Azure Compute
- Azure Net
- Cloud Engineering Services
- ISSD: Zabezpečení
- Multi-factor Authentication
- SQL Database
- Storage

## <a name="types-of-users"></a>Typy uživatelů
Zaměstnanci (nebo dodavatele) společnosti Microsoft jsou považovány za interní uživatele. Všichni ostatní uživatelé jsou považovány za externí uživatele. Všichni Azure interní uživatelé mají stav jejich zaměstnance zařazený do kategorie s úrovní citlivosti, který definuje přístup k zákaznickým datům (přístup nebo žádný přístup). Oprávnění uživatele pro Azure (povolení oprávnění po provedení ověřování) jsou popsány v následující tabulce:

| Role | Interní nebo externí | Úroveň citlivosti | Autorizované oprávnění a funkce prováděné | Typ přístupu
| --- | --- | --- | --- | --- |
| Technika datové centrum Azure | Interní | Žádný přístup k zákaznickým datům | Spravujte fyzického zabezpečení místním prostředí. Chování hlídky do a z datového centra a monitorovat všechny vstupní body. Přepravu do proměnné a z datového centra určité-vymazat pracovníky, kteří poskytují obecné služby (například jídlo nebo čištění) nebo IT práce v rámci datového centra. Provádění rutiny monitorování a údržby síťového hardwaru. Provedení incidentu správy a typu break-fix práce pomocí různých nástrojů. Provádění rutiny monitorování a údržby fyzického hardwaru v datových centrech. Přístup k prostředí na vyžádání z vlastníkům nemovitostí. Dokáže provádět forenzní šetření, protokolování incidentů sestavy a vyžadují povinné zabezpečení školení a zásad požadavků. Provozní vlastnictví a údržby důležité zabezpečení nástrojů, jako je například skenery a shromažďování protokolů. | Trvalý přístup k prostředí. |
| Azure incidentu třídění (rychlá reakce engineers) | Interní | Přístup k zákaznickým datům | Spravujte komunikaci mezi MCIO, podpory a vývojové týmy. Třídění platformu, incidentů, problémů s nasazením a žádosti o služby. | Just-in-time přístup k prostředí, s omezenou trvalého přístupu k systémům jiných zákazníků. |
| Techniky nasazení Azure | Interní | Přístup k zákaznickým datům | Nasazení a upgrade komponenty platformy, softwaru a změny konfigurace plánované podporu Azure. | Just-in-time přístup k prostředí, s omezenou trvalého přístupu k systémům jiných zákazníků. |
| Podpora výpadek zákazníka v systému Azure (tenant) | Interní | Přístup k zákaznickým datům | Ladění a Diagnostika platformy výpadky a chyb pro tenanty jednotlivé výpočetní prostředky a účty Azure. Analýza chyb. Jednotky důležité opravy na platformy nebo zákazníků a vylepšovat technické napříč podpory. | Just-in-time přístup k prostředí, s omezenou trvalého přístupu k systémům jiných zákazníků. |
| Technici Azure živého webu (monitorování engineers) a incident | Interní | Přístup k zákaznickým datům | Diagnostikovat a zmírnit stavu platformy pomocí diagnostických nástrojů. Jednotka opravy ovladačů svazků, opravte položek vyplývající z výpadky a pomáhají akce obnovení výpadek. | Just-in-time přístup k prostředí, s omezenou trvalého přístupu k systémům jiných zákazníků. |
|Zákazníci Azure | Externí | neuvedeno | neuvedeno | neuvedeno |

Azure používá jedinečné identifikátory pro ověřování organizace uživatelů a zákazníků (nebo procesy, které jednají jménem organizace uživatelů). To platí pro všechny prostředky a zařízení, které jsou součástí prostředí Azure.

### <a name="azure-internal-authentication"></a>Interní ověřování Azure

Komunikace mezi komponentami Azure interní jsou chráněné pomocí šifrování TLS. Ve většině případů jsou podepsané svým držitelem certifikáty X.509. Certifikáty s připojeními, která je přístupná z mimo síť Azure jsou výjimku, jako jsou certifikáty pro FCs. FCs mít certifikáty vystavené infrastrukturou Certificate Microsoft z Certifikační autority, která je založená na důvěryhodné kořenové certifikační Autority. To umožňuje snadno převracet FC veřejné klíče. Kromě toho vývojovými nástroji společnosti Microsoft pomocí veřejných klíčů FC. Při vývojářům odeslat nové Image aplikací, obrázky budou šifrované s veřejným klíčem FC, pokud chcete chránit všechny vložené tajné klíče.

### <a name="azure-hardware-device-authentication"></a>Ověřování Azure hardwaru zařízení

FC udržuje sadu přihlašovacích údajů (klíče a hesla) používá ke svému ověření ke různých hardwarových zařízení pod svou kontrolou. Společnost Microsoft používá systém zabránit přístupu k těmto přihlašovacím údajům. Konkrétně přenosu, trvalé a použijte tyto přihlašovací údaje slouží k zabránění vývojáře v Azure, správce a zálohování služby a pracovníky přístup k citlivé, důvěrné nebo soukromé informace.

Společnost Microsoft používá šifrování založené na veřejný klíč FC hlavní identity. K tomu dochází v nastavení FC a čas Rekonfigurace FC, přenášet pověření používaná pro přístup k síti hardwarovým zařízením. Když FC potřebuje přihlašovací údaje, FC načte a dešifruje je.

### <a name="network-devices"></a>Síťová zařízení

Tým Azure sítě konfiguruje účty síťové služby pro umožnění spolupráce klienta Azure k ověření do síťových zařízení (směrovače, přepínače a nástroje pro vyrovnávání zatížení).

## <a name="secure-service-administration"></a>Zabezpečení správy služeb
Pracovníci na provoz Azure je potřeba pomocí zabezpečených pracovních stanic správců (Microsoftu). Zákazníci můžou implementovat podobně jako ovládací prvky pomocí pracovní stanice s privilegovaným přístupem. S Microsoftu pracovníci správy použít jednotlivě přiřazený účet pro správu, která je oddělená od standardního uživatelského účtu uživatele. SAW navazuje na tento postup oddělení účet tím, že poskytuje pro citlivé účty důvěryhodné pracovní stanice.

## <a name="next-steps"></a>Další postup
Další informace o co společnost Microsoft k zajištění infrastruktury Azure, najdete v tématech:

- [Zařízení Azure, místním prostředí a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Funkce zabezpečení Azure SQL Database](azure-infrastructure-sql.md)
- [Operace Azure výroby a správu](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integrity infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrana dat zákazníků Azure](azure-protection-of-customer-data.md)
