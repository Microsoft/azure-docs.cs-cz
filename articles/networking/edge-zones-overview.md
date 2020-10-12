---
title: O verzi Preview zóny Azure Edge
description: 'Přečtěte si o nabídkách hraničního zpracování od Microsoftu: zóna Azure Edge.'
services: vnf-manager
author: ganesr
ms.service: vnf-manager
ms.topic: article
ms.date: 07/07/2020
ms.author: ganesr
ms.openlocfilehash: 68aa3342ab09be73a82f4f896ffdff99d15a5350
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329564"
---
# <a name="about-azure-edge-zone-preview"></a>O verzi Preview zóny Azure Edge

Hraniční zóna Azure je skupina nabídek od Microsoft Azure, které umožňují zpracování dat blízko uživateli. Virtuální počítače, kontejnery a jiné vybrané služby Azure můžete nasadit do hraničních zón a řešit tak požadavky aplikací s nízkou latencí a vysokou propustností.

Mezi typické scénáře použití pro hraniční zóny patří:

- Příkazy a ovládací prvky v reálném čase v robotech.
- Analýzy v reálném čase a Inferencing prostřednictvím umělé Intelligence a strojového učení.
- Strojní Vision.
- Vzdálené vykreslování pro hybridní realitu a scénáře VDI
- Moderní hry pro více hráčů.
- Streamování médií a doručování obsahu.
- Dohled a zabezpečení.

Existují tři typy hraničních zón Azure:

- Azure Edge Zone
- Hraniční zóny Azure s operátorem
- Zóny privátních hraničních zařízení Azure

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zone

![Azure Edge Zone](./media/edge-zones-overview/edge-zones.png "Azure Edge Zone")

Hraniční zóny Azure jsou rozšíření Azure, která jsou umístěná v centrech, která jsou mnohem mimo oblasti Azure. Azure Edge Zones podporuje virtuální počítače, kontejnery a vybranou sadu služeb Azure, které umožňují spouštět aplikace náročné na latenci a propustnosti blízko koncovým uživatelům. Hraniční zóny Azure jsou součástí globální sítě Microsoftu. Poskytují zabezpečené a spolehlivé připojení s vysokou šířkou pásma mezi aplikacemi, které běží na hraniční zóně blízko uživatele. A nabízejí kompletní sadu služeb Azure běžící v rámci oblastí Azure. Hraniční zóny Azure jsou vlastněné a provozované společností Microsoft. Pomocí stejné sady nástrojů Azure a stejného portálu můžete spravovat a nasazovat služby do hraničních zón.

Mezi typické případy použití patří:

- Hraní herních a herních dat.
- Streamování médií a doručování obsahu.
- Analýzy v reálném čase a Inferencing prostřednictvím umělé Intelligence a strojového učení.
- Vykreslování pro Mixed realitu.

Zóny Azure Edge budou k dispozici v následujících oblastech Metro:

- New York, NY
- Los Angeles, CA
- Miami, FL

Další informace [vám poskytne tým hraničních zón](https://aka.ms/EdgeZones) .

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>Hraniční zóny Azure s operátorem

![Hraniční zóny s operátorem](./media/edge-zones-overview/edge-carrier.png "Hraniční zóny s operátorem")

Hraniční zóny Azure s dopravcem jsou rozšíření Azure, která jsou umístěná v datových centrech mobilní operátory v umístěních na populaci. Hraniční zóna Azure s infrastrukturou dopravců je jediným směrováním ze sítě 5G mobilního operátora. Toto umístění nabízí latenci méně než 10 milisekund pro aplikace z mobilních zařízení.

Hraniční zóny Azure s dopravcem se nasazují v datových centrech mobilní operátory a připojují se k globální síti Microsoft. Poskytují zabezpečené a spolehlivé připojení s vysokou šířkou pásma mezi aplikacemi, které běží blízko uživateli. A nabízejí kompletní sadu služeb Azure běžící v rámci oblastí Azure. Vývojáři můžou pomocí stejné sady známých nástrojů sestavovat a nasazovat služby do hraničních zón.

Mezi typické případy použití patří:

- Hraní herních a herních dat.
- Streamování médií a doručování obsahu.
- Analýzy v reálném čase a Inferencing prostřednictvím umělé Intelligence a strojového učení.
- Vykreslování pro Mixed realitu.
- Připojená mobilní zařízení.
- Tele – Medicine

Hraniční zóny budou nabídnuty ve spolupráci s následujícími operátory:

- AT&T (Brno, Praha a Los Angeles)

Nezávislý výrobce softwaru pracující na optimalizovaných a škálovatelných aplikacích, které jsou připojené k sítím 5G, teď můžou používat nové umístění Azure Edge ve verzi Preview, které se nachází na&T při sestavování a experimentování s extrémně nízkou latencí, mobilními a propojenými scénáři. Zaregistrujte se do programu předčasného přihlašování, abyste mohli využít zabezpečené připojení s vysokou šířkou pásma.

Další informace [vám poskytne tým hraničních zón](https://aka.ms/EdgeZones) .

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Zóny privátních hraničních zařízení Azure

![Zóny privátního okraje](./media/edge-zones-overview/private-edge.png "Zóny privátního okraje")

Zóny privátních hraničních zařízení Azure jsou rozšířeními Azure, která jsou umístěná v místním prostředí. Zóna privátního Edge Azure je založená na platformě [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) . Umožňuje přístup s nízkou latencí k výpočetním a místně nasazeným službám úložiště. Privátní hraniční zóna také umožňuje nasazovat aplikace od nezávislého výrobce softwaru a funkce virtualizované sítě (VNFs) jako [spravované aplikace Azure](https://azure.microsoft.com/services/managed-applications/) spolu s virtuálními počítači a kontejnery místně. Tyto VNFs můžou zahrnovat jádra mobilních paketů, směrovače, brány firewall a zařízení SD-WAN. Zóna privátního Edge Azure je dodávána s řešením orchestrace v nativním cloudu, které umožňuje spravovat životní cyklus VNFs a aplikací z Azure Portal.

Zóna privátního okraje Azure umožňuje vyvíjet a nasazovat aplikace místně pomocí stejných známých nástrojů, které používáte k sestavování a nasazování aplikací v Azure.

Umožňuje vám také:

- Spusťte soukromé mobilní sítě (Private LTE, Private 5G).
- Implementujte funkce zabezpečení, jako jsou brány firewall.
- Rozšíříte své místní sítě napříč několika pobočkami a Azure pomocí zařízení SD-WAN na stejných zařízeních privátních hraničních zařízení a spravujte je z Azure.

Mezi typické případy použití patří:

- Příkazy a ovládací prvky v reálném čase v robotech.
- Analýzy v reálném čase a Inferencing s umělou logikou a strojovým učením.
- Strojní Vision.
- Vzdálené vykreslování pro hybridní realitu a scénáře VDI
- Dohled a zabezpečení.

Máme bohatou ekosystém VNF dodavatelů, ISV a MSP a umožníme tak kompletní řešení, která používají zóny privátního okraje. Další informace [vám poskytne tým zóny privátního Edge](https://aka.ms/EdgeZonesPartner) .

### <a name="private-edge-zone-partners"></a><a name="private-edge-partners"></a>Partneři zóny privátního okraje

![Partneři zóny privátního okraje](./media/edge-zones-overview/partners.png "Partneři privátních hraničních zón")

#### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>Virtualizované síťové funkce (VNFs)

##### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>Virtualizovaný vEPC (virtualized Packet Core) pro mobilní sítě

- [Potvrzující sítě](https://www.affirmednetworks.com/)
- [Celona](https://www.celona.io/azure-edge)
- [Druid software](https://www.druidsoftware.com/)
- [Expeto](https://www.expeto.io/)
- [Mavenir](https://mavenir.com/)
- [Metaswitch](https://www.metaswitch.com/)
- [Cloud pro digitální automatizaci Nokia](https://www.dac.nokia.com/)

##### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>Partneři mobilních přepínačů

- [Celona](https://www.celona.io/azure-edge)
- [Commscope Ruckus](https://support.ruckuswireless.com/)

##### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>Dodavatelé SD-WAN

- [128 Technology](https://www.128technology.com/)
- [NetFoundry](https://netfoundry.io/)
- [Nuage sítě z Nokia](https://www.nuagenetworks.net/)
- [Sítě – odrážky](https://www.versa-networks.com/)
- [VMware SD-WAN podle Velocloud](https://www.velocloud.com/)

##### <a name="router-vendors"></a><a name="router-vendors"></a>Dodavatelé směrovačů

- [Arista](https://www.arista.com/)

##### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>Dodavatelé brány firewall

- [Palo Alto Networks](https://www.paloaltonetworks.com/)

##### <a name="managed-solutions-providers-mobile-operators-and-global-system-integrators-gsis"></a><a name="msp-mobile"></a>Poskytovatelé spravovaných řešení: mobilní operátory a globální systémové integrátory (GSIs)

| GSIs a operátory | Mobilní operátory |
| --- | --- |
| Amdocs                       | Etisalat             |
| Americká věž               | NTT Communications   |
| CenturyLink                  | Proximus             |
| Expeto                       | Rogers               |
| Federované bezdrátové sítě           | SK Telecom           |
| Infosys                      | Telefonica           |
| Tech Mahindra                | Telstra              |
|                              | Vodafone             |

Informace o tom, jak se stát partnerem, [získáte od týmu zóny privátního Edge](https://aka.ms/EdgeZonesPartner) .

### <a name="private-edge-zone-solutions"></a><a name="solutions-private-edge"></a>Řešení zóny privátního okraje

#### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>Privátní mobilní síť na zónách privátního okraje

![Privátní mobilní síť na zónách privátního okraje](./media/edge-zones-overview/mobile-networks.png "Privátní mobilní síť na zónách privátního okraje")

V privátních hraničních zónách teď můžete nasadit privátní mobilní síť. Privátní mobilní sítě umožňují extrémně nízkou latenci, vysokou kapacitu a spolehlivou a zabezpečenou bezdrátovou síť, která je potřebná pro důležité obchodní aplikace.

Privátní mobilní sítě můžou povolit scénáře jako:
- Příkaz a řízení autořízených vozidel (AGVs) v skladech.
- Komunikace mezi roboty v rámci inteligentních továren v reálném čase.
- Rozšířená realita a aplikace Edge pro virtuální realitu

Virtualizovaná funkce sítě vEPC (virtualized Packet Core) je mozkem privátní mobilní sítě. Teď můžete nasadit vEPC na zóny privátních okrajů. Seznam partnerů vEPC, kteří jsou k dispozici v privátních hraničních zónách, najdete v tématu [VEPC ISV](#vEPC).

Nasazení privátního řešení mobilní sítě v privátních hraničních zónách vyžaduje další komponenty, jako jsou mobilní přístupové body, SIM karty a další VNFs, jako jsou směrovače. Přístup k Licencovanému nebo nelicencovaným spektru je zásadní pro nastavení privátní mobilní sítě. A možná budete potřebovat pomoc s plánováním RF, fyzickým rozložením, instalací a podporou. Seznam partnerů najdete v tématu [partneři mobilních přepínačů](#mobile-radio).

Společnost Microsoft poskytuje partnerský ekosystém, který může pomáhat se všemi aspekty tohoto procesu. Partneři můžou pomáhat s plánováním sítě, nákupem požadovaných zařízení, nastavením hardwaru a správou konfigurace z Azure. Sada ověřených partnerů, které jsou úzce integrované s Microsoftem, zajistí, že vaše řešení bude spolehlivé a bude snadné ho používat. Můžete se zaměřit na základní scénáře a spoléhat na to, že společnost Microsoft a její partneři budou pomáhat s ostatními.

#### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>SD – WAN v zónách privátního okraje

![SD – WAN v zónách privátního okraje](./media/edge-zones-overview/sd-wan.png "SD – WAN v zónách privátního okraje")

SD-WAN umožňuje vytvářet sítě WAN (Wide Area Network) na podnikové úrovni, které mají tyto výhody:

- Větší šířka pásma
- Vysoce výkonný přístup ke cloudu
- Vložení služby
- Spolehlivost
- Správa zásad
- Rozsáhlá viditelnost sítě

SD-WAN poskytuje bezproblémové připojení k firemní pobočce, které je Orchestrované z redundantních centrálních kontrolérů za sníženou cenu vlastnictví.
SD-WAN na soukromých hraničních zónách umožňuje přejít z modelu zaměřeného na CAPEX na model softwaru jako služby (SaaS), aby se snížily jeho rozpočty. Můžete použít své možnosti partnerů SD-WAN, Orchestrator nebo Controller, a povolit tak nové služby a rozšířit je v celé síti hned.

## <a name="next-steps"></a>Další kroky

Další informace získáte v následujících týmech:

* [Tým hraničních zón](https://aka.ms/EdgeZones)
* [Tým zóny privátního okraje, který se stane partnerem](https://aka.ms/EdgeZonesPartner)
