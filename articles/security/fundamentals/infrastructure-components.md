---
title: Součásti a hranice informačního systému Azure
description: Tento článek obsahuje obecný popis architektury a správy Microsoft Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727199"
---
# <a name="azure-information-system-components-and-boundaries"></a>Součásti a hranice informačního systému Azure
Tento článek obsahuje obecný popis architektury azure a správy. Systémové prostředí Azure se skládá z následujících sítí:

- Produkční síť Microsoft Azure (síť Azure)
- Podniková síť společnosti Microsoft (corpnet)

Samostatné IT týmy jsou odpovědné za provoz a údržbu těchto sítí.

## <a name="azure-architecture"></a>Architektura Azure
Azure je platforma cloud computingu a infrastruktura pro vytváření, nasazování a správu aplikací a služeb prostřednictvím sítě datových center. Společnost Microsoft spravuje tato datová centra. Na základě počtu prostředků, které zadáte, Azure vytvoří virtuální počítače (VM) na základě potřeby prostředků. Tyto virtuální počítače běží na hypervisoru Azure, který je určený pro použití v cloudu a není přístupný veřejnosti.

Na každém uzlu fyzického serveru Azure je hypervisor, který běží přímo přes hardware. Hypervisor rozdělí uzel na proměnný počet virtuálních virtuálních mv hosta. Každý uzel má také jeden kořenový virtuální virtuální ms, který spouští hostitelský operační systém. Brána Windows Firewall je povolena na každém virtuálním počítači. Konfigurace souboru definice služby definujete, které porty lze adresovat. Tyto porty jsou jediné otevřené a adresovatelné, interně nebo externě. Veškerý provoz a přístup k disku a síti je zprostředkován hypervisorem a kořenovým operačním systémem.

Na hostitelské vrstvě virtuální počítače Azure spustit vlastní a tvrzené verze nejnovější Windows Server. Azure používá verzi Windows Serveru, která obsahuje jenom ty součásti potřebné k hostování virtuálních počítačů. To zlepšuje výkon a snižuje prostor pro útok. Hranice počítače jsou vynuceny hypervisorem, který nezávisí na zabezpečení operačního systému.

### <a name="azure-management-by-fabric-controllers"></a>Správa Azure pomocí řadičů infrastruktury

V Azure jsou virtuální počítače spuštěné na fyzických serverech (blades/nodes) seskupeny do clusterů o 1000. Virtuální počítače jsou nezávisle spravované škálovatavanou a redundantní softwarovou komponentou platformy, která se nazývá řadič prostředků (FC).

Každý FC spravuje životní cyklus aplikací spuštěných v jeho clusteru a zřazuje a monitoruje stav hardwaru pod jeho kontrolou. Spouští autonomní operace, jako je reinkarnace instancí virtuálních počítačů na serverech v pořádku, když zjistí, že server selhal. Fc také provádí operace správy aplikací, jako je například nasazení, aktualizace a horizontální navýšení kapacity aplikací.

Datové centrum je rozděleno do clusterů. Clustery izolují chyby na úrovni FC a zabraňují určitým třídám chyb, které ovlivňují servery mimo cluster, ve kterém k nim dochází. Řadiče domény, které obsluhují konkrétní cluster Azure, jsou seskupeny do clusteru FC.

### <a name="hardware-inventory"></a>Inventář hardwaru

Fc připravuje soupis hardwaru azure a síťových zařízení během procesu konfigurace bootstrap. Všechny nové hardwarové a síťové součásti vstupující do produkčního prostředí Azure musí postupovat podle procesu konfigurace zaváděcího modulu. Fc je zodpovědný za správu celého inventáře uvedeného v konfiguračním souboru datacenter.xml.

### <a name="fc-managed-operating-system-images"></a>Bitové kopie operačního systému spravované fc

Tým operačního systému poskytuje image ve formě virtuálních pevných disků nasazených na všech hostitelských a hostovaných virtuálních počítačích v produkčním prostředí Azure. Tým vytvoří tyto základní image prostřednictvím automatizovaného procesu offline sestavení. Základní bitová kopie je verze operačního systému, ve kterém byly změněny a optimalizovány součásti jádra a další součásti jádra pro podporu prostředí Azure.

Existují tři typy bitových kopií operačního systému spravované ho fabric:

- Hostitel: Přizpůsobený operační systém, který běží na hostitelských virtuálních počítačích.
- Nativní: Nativní operační systém, který běží na klienty (například Azure Storage). Tento operační systém nemá žádný hypervisor.
- Host: Hostovaný operační systém, který běží na hostincích.

Hostitelské a nativní operační systémy spravované fc jsou navrženy pro použití v cloudu a nejsou veřejně přístupné.

#### <a name="host-and-native-operating-systems"></a>Hostitelské a nativní operační systémy

Host a nativní jsou zpevněné image operačního systému, které jsou hostiteli agentů infrastruktury a běží na výpočetní uzel (běží jako první virtuální počítač na uzlu) a uzly úložiště. Výhodou použití optimalizovaných základních bitových kopií hostitele a nativního je, že snižuje plochu vystavenou pomocí api nebo nepoužívaných součástí. Ty mohou představovat vysoká bezpečnostní rizika a zvýšit nároky na operační systém. Operační systémy se sníženou půdou obsahují jenom součásti nezbytné pro Azure.

#### <a name="guest-operating-system"></a>Hostovaný operační systém

Interní součásti Azure spuštěné na virtuálních počítačích hostovaného operačního systému nemají možnost spustit protokol Rd Desktop Protocol. Všechny změny nastavení konfigurace směrného plánu musí projít procesem správy změn a verzí.

## <a name="azure-datacenters"></a>Datová centra Azure
Tým Microsoft Cloud Infrastructure and Operations (MCIO) spravuje fyzickou infrastrukturu a zařízení datových center pro všechny online služby Microsoftu. MCIO je primárně zodpovědný za správu fyzických a environmentálních ovládacích prvků v rámci datových center, stejně jako za správu a podporu zařízení vnější hraniční sítě (jako jsou hraniční směrovače a směrovače datových center). MCIO je také zodpovědný za nastavení minimální holé serverhardware na regálech v datovém centru. Zákazníci nemají žádnou přímou interakci s Azure.

## <a name="service-management-and-service-teams"></a>Týmy pro řízení služeb a služby
Podporu služby Azure spravují různé technické skupiny, známé jako servisní týmy. Každý servisní tým je zodpovědný za oblast podpory Azure. Každý servisní tým musí zpřístupnit inženýra 24 hodin denně, 7 dní v za provozu, aby mohl prozkoumat a vyřešit chyby ve službě. Servisní týmy nemají ve výchozím nastavení fyzický přístup k hardwaru pracujícímu v Azure.

Servisní týmy jsou:

- Aplikační platforma
- Azure Active Directory
- Azure Compute
- Síť Azure
- Cloudinženýrské služby
- ISSD: Zabezpečení
- Vícefaktorové ověřování
- Databáze SQL
- Úložiště

## <a name="types-of-users"></a>Typy uživatelů
Zaměstnanci (nebo dodavatelé) společnosti Microsoft jsou považováni za interní uživatele. Všichni ostatní uživatelé jsou považováni za externí uživatele. Všichni interní uživatelé Azure mají svůj stav zaměstnance zařazen do kategorií s úrovní citlivosti, která definuje jejich přístup k zákaznickým datům (přístup nebo žádný přístup). Uživatelská oprávnění k Azure (oprávnění k autorizaci po ověření) jsou popsána v následující tabulce:

| Role | Interní nebo externí | Úroveň citlivosti | Autorizovaná práva a vykonávané funkce | Typ přístupu
| --- | --- | --- | --- | --- |
| Inženýr datového centra Azure | Interní | Žádný přístup k zákaznickým datům | Správa fyzického zabezpečení areálu. Proveďte hlídky v datovém centru a vyvezte je a sledujte všechna vstupní místa. Doprovoďte do datového centra a z něj určité nevyčištěné pracovníky, kteří poskytují obecné služby (například stravování nebo úklid) nebo IT práce v rámci datového centra. Prováděte rutinní monitorování a údržbu síťového hardwaru. Provádějte správu incidentů a opravu přerušení pomocí různých nástrojů. Prováděte rutinní monitorování a údržbu fyzického hardwaru v datových centrech. Přístup k prostředí na vyžádání od vlastníků nemovitostí. Schopnost provádět forenzní vyšetřování, protokolovat zprávy o incidentech a vyžadovat povinné školení zabezpečení a požadavky na zásady. Provozní vlastnictví a údržba kritických bezpečnostních nástrojů, jako jsou skenery a sběr protokolů. | Trvalý přístup k životnímu prostředí. |
| Třídění incidentů azure (inženýři rychlé reakce) | Interní | Přístup k zákaznickým datům | Spravujte komunikaci mezi týmy MCIO, podpory a inženýrských týmů. Třídění incidentů platformy, problémy s nasazením a požadavky na služby. | Přístup k životnímu prostředí za v čase s omezeným trvalým přístupem k systémům, které nejsou zákazníky. |
| Inženýři nasazení Azure | Interní | Přístup k zákaznickým datům | Nasazujte a upgradujte komponenty platformy, software a plánované změny konfigurace v podpoře Azure. | Přístup k životnímu prostředí za v čase s omezeným trvalým přístupem k systémům, které nejsou zákazníky. |
| Podpora výpadku zákazníků Azure (tenant) | Interní | Přístup k zákaznickým datům | Ladění a diagnostika výpadky platformy a chyby pro jednotlivé výpočetní klienty a účty Azure. Analyzujte poruchy. Podpořte důležité opravy platformy nebo zákazníka a povzneste technická vylepšení v rámci podpory. | Přístup k životnímu prostředí za v čase s omezeným trvalým přístupem k systémům, které nejsou zákazníky. |
| Inženýři webu Azure pro živé sítě (monitorovací inženýři) a incidenty | Interní | Přístup k zákaznickým datům | Diagnostikujte a zmírňujte stav platformy pomocí diagnostických nástrojů. Opravy jednotek pro ovladače svazků, opravy položek vyplývajících z výpadků a pomoc při akcích obnovení výpadku. | Přístup k životnímu prostředí za v čase s omezeným trvalým přístupem k systémům, které nejsou zákazníky. |
|Zákazníci Azure | Externí | Není dostupné. | Není dostupné. | Není dostupné. |

Azure používá jedinečné identifikátory k ověřování uživatelů a zákazníků z organizace (nebo procesů jednajících jménem uživatelů organizace). To platí pro všechny prostředky a zařízení, které jsou součástí prostředí Azure.

### <a name="azure-internal-authentication"></a>Interní ověřování Azure

Komunikace mezi interními součástmi Azure je chráněna šifrováním TLS. Ve většině případů jsou certifikáty X.509 podepsány vlastním podpisem. Certifikáty s připojeními, ke kterým lze přistupovat mimo síť Azure, jsou výjimkou, stejně jako certifikáty pro řadiče domény. Řadiče domény mají certifikáty vydané certifikační autoritou společnosti Microsoft, která je podporována důvěryhodnou kořenovou certifikační autoritou. To umožňuje FC veřejné klíče, které mají být převráceny snadno. Nástroje pro vývojáře společnosti Microsoft navíc používají veřejné klíče FC. Když vývojáři odešlou nové bitové kopie aplikací, jsou obrázky zašifrovány pomocí veřejného klíče FC, aby byly chráněny všechny vložené tajné klíče.

### <a name="azure-hardware-device-authentication"></a>Ověřování hardwarových zařízení Azure

Fc udržuje sadu přihlašovacích údajů (klíče nebo hesla) používá k ověření sám na různých hardwarových zařízení pod jeho kontrolou. Společnost Microsoft používá systém, který brání přístupu k těmto pověřením. Konkrétně přenos, trvalost a použití těchto přihlašovacích údajů je navržen tak, aby zabránit vývojářům Azure, správci a služby zálohování a osobní přístup k citlivé, důvěrné nebo soukromé informace.

Společnost Microsoft používá šifrování založené na veřejném klíči hlavní identity fc. K tomu dochází v době změny konfigurace fc a FC pro přenos pověření používaných pro přístup k síťovým hardwarovým zařízením. Když FC potřebuje pověření, FC načte a dešifruje je.

### <a name="network-devices"></a>Síťová zařízení

Síťový tým Azure konfiguruje účty síťových služeb tak, aby klient Azure mohl ověřovat síťová zařízení (směrovače, přepínače a nástroje pro vyrovnávání zatížení).

## <a name="secure-service-administration"></a>Bezpečná správa služeb
Provozní pracovníci Azure jsou povinni používat zabezpečené pracovní stanice pro správu (SAWs). Zákazníci mohou implementovat podobné ovládací prvky pomocí pracovních stanic s privilegovaným přístupem. S SAWs, administrativní pracovníci používají individuálně přiřazený účet pro správu, který je oddělený od standardního uživatelského účtu uživatele. SAW staví na této praxi oddělení účtu tím, že poskytuje důvěryhodnou pracovní stanici pro tyto citlivé účty.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá, aby zabezpečil infrastrukturu Azure, najdete v tématu:

- [Zařízení Azure, prostory a fyzické zabezpečení](physical-security.md)
- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Produkční síť Azure](production-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Produkční operace a správa Azure](infrastructure-operations.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)
