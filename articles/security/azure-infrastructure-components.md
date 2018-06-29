---
title: Součásti systému Azure informace a hranice
description: Tento článek obsahuje obecné popisu architektury Microsoft Azure a správu.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 8db1dce5fcc56c229d1fdd746bafbd2fae2c9bad
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102212"
---
# <a name="azure-information-system-components-and-boundaries"></a>Součásti systému Azure informace a hranice
Tento článek obsahuje obecné popisu architektury Microsoft Azure a správu. Prostředí Azure systému se skládá z následujících sítí:

- Microsoft Azure produkční síť (síť Azure)
- Microsoft Corporate sítě (Corpnet síť)

Samostatné IT oddělení jsou zodpovědní za provoz a údržba síť Azure a CorpNet sítí.

## <a name="azure-architecture"></a>Architektura Azure
Microsoft Azure je Cloudová platforma a infrastruktury pro vytváření, nasazení a Správa aplikací a služeb prostřednictvím sítě datových center spravovaných společností Microsoft. Na základě počtu prostředky zadané zákazníků, Azure vytvoří virtuální počítače podle potřeb prostředků. Tyto virtuální počítače, spusťte v Microsoft Azure hypervisoru, který je určený pro použití v cloudu a není přístupný na veřejnost.

Na každém uzlu Azure fyzický server je Hypervisor, který běží přímo nad hardwarem. Hypervisor rozděluje uzlu do proměnné počet hostované virtuální počítače (VM). Každý uzel má také jeden speciální "Root" virtuální počítač, který se spouští hostitelským operačním systémem. Pro každý virtuální počítač je povolená brána Windows Firewall. Interně nebo externě, jsou pouze porty otevřené a adresovatelné, porty explicitně definované v souboru definice služby konfigurovat tak, že zákazník. Všechny přenosy a přístup k disku a sítě je zprostředkovaného hypervisoru a kořenové operačního systému.

Ve vrstvě hostitele, virtuální počítače Azure, které používají vlastní verzi posílené nejnovější Windows Server. Microsoft Azure používá stripped-down verzi systému Windows Server, který obsahuje pouze součásti potřebné k hostiteli virtuálních počítačů. Děje se tak ke zlepšení výkonu a omezte prostor pro útoky. Počítač hranice vynucuje hypervisoru, které nezávisí na zabezpečení operačního systému.

**Azure správu Kontrolerů prostředků infrastruktury (FCs)**: V Azure, virtuální počítače běžící na fyzických serverech (oken nebo uzly) jsou seskupené do asi 1000 "clustery". Virtuální počítače se spravují nezávisle škálovat na více systémů a redundantní platformy součástí softwaru s názvem FC.

Každý FC spravuje životní cyklus aplikací běžících v jeho clusteru a předpisy a sledovat stav hardwaru pod její kontrolou. Se provede oba autonomic operace, jako je například reincarnating instance virtuálních počítačů na serverech v pořádku, když zjistí, že serveru se nezdařilo. FC také provádí operace správy aplikací jako je nasazení, aktualizace a škálování aplikací.

Datového centra je rozdělené do clusterů. Clustery izolovat chyb na úrovni FC a zabránit určité třídy chyb, které mají vliv na servery nad rámec clusteru, ve kterém k nim dojde. FCs, která sloužit konkrétní cluster Azure jsou seskupeny do clusteru služby FC.

**Inventář hardwaru**: shromážděny během procesu spuštění konfigurace a zdokumentované v konfiguračním souboru datacenter.xml inventář Azure hardwaru a síťových zařízení. Proces zavedení konfigurace musí následovat žádný nový hardware a síťové součásti zadávání Azure produkčního prostředí. FC je zodpovědný za správu celý inventář uveden v konfiguračním souboru datacenter.xml.

**Spravované FC OS**: team operačního systému poskytuje bitové kopie operačního systému, ve tvaru z virtuálních pevných disků (VHD), které jsou nasazeny na všechny hostitele a virtuální počítače hostované v Azure provozním prostředí. Tým služby operačního systému tyto vytvoří "Základní Image" prostřednictvím procesu automatizované offline sestavení. Základní Image je verze operačního systému, ve kterém jádra a další komponenty jádra byly upravit a optimalizované pro podporu prostředí Azure.

Existují tři typy bitových kopií operačního systému spravovaných prostředků infrastruktury:

- Operační systém hostitele – operační systém hostitele je upravený operační systém, který běží na hostiteli virtuálních počítačů
- Nativní OS – nativní operační systém, který běží na klienty (například Azure Storage), nemá jakéhokoli hypervisoru
- Hostovaného operačního systému – hostovaného operačního systému, který běží na virtuálních počítačích hosta

Hostitele a nativní FC spravované operační systémy jsou navrženy pro použití v cloudu a nejsou veřejně přístupná.

**Hostitele a nativní OS**: hostitelským operačním systémem a nativní operačního systému jsou posílené bitové kopie operačního systému, které jsou hostiteli infrastruktury agenty (IM) a spusťte na výpočetním uzlu (spouští jako první virtuální počítač na uzlu) a uzly úložiště. Výhody použití optimalizované základní bitové kopie hostitele a nativní operačního systému je to, že snižuje možnosti útoku na zveřejněný prostřednictvím rozhraní API nebo nepoužívané součásti, které mohou představovat vysoké bezpečnostní rizika a zvýšit nároky na operačního systému. Tyto operační systémy snížení nároků zahrnují jenom součásti potřebné k Azure. To zlepšuje výkon a snižuje prostor pro útoky.

**Hostovaného operačního systému**: Azure interních komponent běžící na virtuálních počítačích hostovaného operačního systému mít žádná možnost spustit protokol RDP (Remote Desktop) na rozdíl od externí zákazníky. Jakékoli změny nastavení konfigurace standardních hodnot by byly zapotřebí k projít změn a procesu správy verzí.

## <a name="azure-datacenters"></a>Datacentra Azure
Tým Microsoft cloudové infrastruktury a operací (MCIO) spravuje společnosti Microsoft fyzické infrastruktury a datacenter zařízení pro všechny Microsoft online services. MCIO především zodpovídá za správu fyzických a prostředí ovládacích prvků v datových centrech, jakož i správa a podpůrné vnější hraniční síťových zařízení (hraniční směrovače a Datacenter směrovače). MCIO zodpovídá taky za nastavení hardwaru úplného serveru minimálně na stojany v datovém centru. Zákazníci, mají bez přímé interakce s Azure.

## <a name="service-management--service-teams"></a>Správa služby a služby týmy
Počet engineering skupin, které jsou známé jako "Service týmy, spravuje podporu služby Azure. Každý týmů služba je zodpovědná za oblast podpory pro Azure. Každý tým služby musíte nastavit technika k dispozici 24 x 7 prozkoumat a vyřešit chyby ve službě. Týmy služeb, ve výchozím nastavení, nemají fyzický přístup k hardwaru v Azure.

Týmy služby jsou:

- Aplikační platforma
- Azure Active Directory
- Azure Compute
- Azure Net
- Cloudové služby inženýrství
- ISSD: zabezpečení
- Multi-factor Authentication
- SQL Database
- Úložiště

## <a name="types-of-users"></a>Typy uživatelů
Všechny Azure interní uživatelé mají stav zaměstnanec zařazené do kategorie s úrovní citlivosti, který definuje přístup pro data zákazníků (přístupu nebo žádný přístup). Zaměstnanci (nebo dodavatelů) společnosti Microsoft jsou považovány za interní uživatele. Všichni ostatní uživatelé jsou považovány za externí uživatele. Oprávnění uživatele do Azure (autorizace oprávnění po provedení ověřování) jsou popsané v následující tabulce:

| Role | Interní nebo externí | Úroveň velkých a malých písmen | Autorizovaný oprávnění a funkce prováděné | Typ přístupu
| --- | --- | --- | --- | --- |
| Azure pracovníka Datacenter | Interní | Žádný přístup k datům zákazníka | Správa fyzického zabezpečení místní; Proveďte hlídky směřující datového centra a monitorovat všechny vstupní body; Poskytovat služby doprovodu do a z datacenter u některých jiných vymazat pracovníky, kteří poskytují obecné služby (jídlo, čištění) nebo pracovní IT v rámci datového centra; Proveďte běžné monitorování a údržby síťový hardware; Provádění správy incidentů a opravu práce pomocí různých nástrojů; Proveďte běžné monitorování a údržby fyzického hardwaru v datových centrech; Přístup k prostředí na vyžádání od vlastníků vlastnost. Umožňující provádět forenzní šetření, protokolování incidentů sestavy a vyžadují povinné bezpečnostního školení & požadavky zásad; Provozní vlastnictví a údržba nástroje důležité zabezpečení, například skenery a shromáždění protokolů. | Trvalý přístup do prostředí |
| Microsoft Azure incidentu třídění (rychlé odpovědi Engineers) | Interní | Přístup k datům zákazníka | Správa komunikace mezi týmy operace infrastruktury, podpory a Azure Engineering; Třídění platformy incidenty, problémy při nasazení a žádosti o služby. | Jenom při běhu je přístup k prostředí - s omezeným přístupem trvalé pro systémy bez zákazníka |
| Techniky nasazení Microsoft Azure | Interní | Přístup k datům zákazníka | Proveďte nasazení/upgrade součástí platformy, softwaru a změny konfigurace naplánované na podporu Microsoft Azure. | Jenom při běhu je přístup k prostředí - s omezeným přístupem trvalé pro systémy bez zákazníka |
| Microsoft Azure výpadku oddělení podpory zákazníků (klientů) | Interní | Přístup k datům zákazníka | Ladění a diagnostikovat výpadků platformy a chyb pro jednotlivé výpočetní klientů a účty Microsoft Azure; Analýza chyb a jednotky důležité opravy na platformy nebo zákazníka, jednotky technické vylepšení napříč podpory. | Jenom při běhu je přístup k prostředí - s omezeným přístupem trvalé pro systémy bez zákazníka |
| Technici živý web Microsoft Azure (monitorování Engineers) & Incident | Interní | Přístup k datům zákazníka | Odpovědnost za pro diagnostiku a zmírnění platformy stavu pomocí diagnostických nástrojů; Jednotka opravy pro svazek ovladače, opravte položky vyplývající z výpadky a pomůže akce obnovení výpadku. | Jenom při běhu je přístup k prostředí - s omezeným přístupem trvalé pro systémy bez zákazníka |
|Zákazníci společnosti Microsoft Azure | Externí | neuvedeno | neuvedeno | neuvedeno |

Azure k ověřování organizace uživatelů a zákazníky (nebo procesy, které jednají jménem organizace uživatelů) používá jedinečné identifikátory pro všechny prostředky nebo zařízení, které jsou součástí prostředí Azure.

**Interní ověřování Microsoft Azure**: komunikace mezi součástmi Azure interní jsou chráněné pomocí šifrování TLS. Ve většině případů jsou podepsané svým držitelem certifikáty X.509. Výjimky jsou vytvářeny pro certifikáty pomocí připojení, která může být subjekty mimo síť Azure a FCs. FCs mít certifikáty vydané Microsoft Certificate z autority (CA), která je zálohovaný díky důvěryhodné kořenové certifikační Autority. To umožňuje snadno převracet FC veřejné klíče. Veřejné klíče FC kromě toho jsou používány vývojové nástroje společnosti Microsoft, tak, že když vývojáři odeslání nové Image aplikací, se šifrují s veřejným klíčem FC ochráníte žádné embedded tajných klíčů.

**Ověřování Microsoft Azure hardwaru zařízení**: FC udržuje sadu přihlašovacích údajů (klíče nebo hesla) používá k ověření samotné různé hardwarová zařízení pod její kontrolou. Systém používaný pro přenos, uchování a používání těchto přihlašovacích údajů je určeno k ochraně Azure vývojáře, správci a zálohování služby nebo pracovníky přístup k citlivým, důvěrné nebo soukromé informace.

Šifrování na základě hlavní identity FC, veřejný klíč se používá v nastavení FC a Rekonfigurace FC časový limit pro přenos přihlašovací údaje používané pro přístup k síťové hardwarová zařízení. Přihlašovací údaje jsou načteny a dešifrovat FC, když je potřebuje.

**Síťová zařízení**: účty služby sítě jsou nakonfigurované tým sítí Azure povolení klienta Microsoft Azure k ověření do síťových zařízení (směrovače, přepínače a nástroje pro vyrovnávání zatížení).

## <a name="secure-service-administration"></a>Zabezpečení služby správy
Microsoft Azure operations pracovníky nutné použít Správce zabezpečení pracovních stanic (pily; zákazníkům může implementovat podobné ovládacích prvků pomocí privilegovaného přístupu pracovní stanice nebo PAWs). Přístup pila je rozšířením zavedené doporučený postup pro správu pracovníky používat samostatný správce a uživatelské účty. Tento postup používá jednotlivě přiřazené účtu správce, která je oddělená od uživatele standardní uživatelský účet. VIDĚLI sestavení na tento účet oddělení postupů tím, že poskytuje trustworthy pracovní stanice pro tyto citlivé účty.

## <a name="next-steps"></a>Další postup
Další informace o funkci Microsoft pro zabezpečení infrastruktury Azure najdete v tématu:

- [Azure zařízení, místní a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Zabezpečení funkce Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Azure produkční operace a Správa](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integritu infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrana dat zákazníka v Azure](azure-protection-of-customer-data.md)
