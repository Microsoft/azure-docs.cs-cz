---
title: 'Architektura: Migrace do virtuální sítě Azure WAN'
description: Přečtěte si, jak migrovat do virtuální sítě Azure WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8aa4fe143c78d2053ce8c48e4866a5522057aa0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062933"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migrace do virtuální sítě Azure WAN

Azure Virtual WAN umožňuje společnostem zjednodušit jejich globální připojení, aby mohli těžit z rozsahu globální sítě Microsoftu. Tento článek obsahuje technické podrobnosti pro společnosti, které chtějí migrovat z existující topologie hub-and-spoke spravované zákazníkem, do návrhu, který využívá virtuální chody sítě Microsoft.

Informace o výhodách, které Azure Virtual WAN umožňuje podnikům, které přijímají moderní globální podnikovou síť zaměřenou na cloud, naleznete [v tématu Globální architektura tranzitní sítě a Virtuální síť WAN](virtual-wan-global-transit-network-architecture.md).

![rozbočovač a](./media/migrate-from-hub-spoke-topology/hub-spoke.png)
**paprsek Obrázek: Virtuální WAN Azure**

Model připojení azure virtuálního datového centra (VDC) přijaly tisíce našich zákazníků, aby využili výchozí hodovacího chování Azure Networking u vytváření jednoduchých a škálovatelných cloudových sítí. Azure Virtual WAN staví na těchto konceptech a zavádí nové funkce, které umožňují globální topologie připojení, a to nejen mezi místními lokalitami a Azure, ale také umožňuje zákazníkům využít škálování sítě Microsoftu k rozšíření jejich stávajících globálních sítí.

Tento článek ukazuje, jak migrovat existující hybridní prostředí do virtuální sítě WAN.

## <a name="scenario"></a>Scénář

Contoso je globální finanční organizace s pobočkami v Evropě i Asii. Plánují přesunout své stávající aplikace z místního datového centra do Azure a vytvořili návrh základů založený na architektuře VDC, včetně místních virtuálních sítí hubu spravovaného zákazníkem pro hybridní připojení. V rámci přechodu na cloudové technologie byl síťový tým pověřen zajištěním toho, aby jejich připojení bylo optimalizováno pro podnikání.

Následující obrázek znázorňuje zobrazení na vysoké úrovni existující globální sítě včetně připojení k více oblastí Azure.

![Contoso existující topologie](./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png)
sítě**Obrázek: Contoso existující topologie sítě**

Ze stávající topologie sítě lze porozumět následujícím bodům:

- Topologie rozbočovače a paprsku se používá ve více oblastech, včetně okruhů ExpressRoute Premium pro připojení zpět ke společné privátní síti WAN.

- Některé z těchto webů mají také tunely VPN přímo do Azure, aby se dostaly k aplikacím hostovaným v cloudu Microsoftu.

## <a name="requirements"></a>Požadavky

Síťový tým byl pověřen poskytováním modelu globální sítě, který podporuje migraci Contoso do cloudu a musí optimalizovat v oblasti nákladů, škálování a výkonu. Souhrnně se musí splnit tyto požadavky:

- Poskytněte vedoucím čtvrť (HQ) i pobočkám optimalizovanou cestu k cloudovým hostovaným aplikacím.
- Odeberte závislost na existujících místních datových centrech (DC) pro ukončení sítě VPN při zachování následujících cest připojení:
  - **Pobočka -to- Virtuální síť**: Připojené kanceláře VPN musí mít přístup k aplikacím migrovaných do cloudu v místní oblasti Azure.
  - **Pobočka -to- Hub -to- Hub -to- VNet**: Připojené kanceláře VPN musí mít přístup k aplikacím migrovaných do cloudu ve vzdálené oblasti Azure.
  - **Pobočka -to- pobočka**: Regionální kanceláře připojené vpn musí být schopny komunikovat mezi sebou a ExpressRoute připojené HQ / DC lokality.
  - **Pobočka -to- Hub -to- Hub -to- branch**: Globálně oddělené kanceláře připojené vpn musí být schopny komunikovat mezi sebou navzájem a všemi připojenými hq/DC lokalitami ExpressRoute.
  - **Pobočka -to- Internet**: Připojené weby musí být schopny komunikovat s Internetem. Tento provoz musí být filtrován a protokolován.
  - **Virtuální síť -to- virtuální síť**: Virtuální sítě Paprsku ve stejné oblasti musí být schopny vzájemně komunikovat.
  - **Virtuální síť -to- Hub -to- Hub -to- VNet**: Virtuální sítě S paprskem v různých oblastech musí být schopny komunikovat mezi sebou.
- Poskytněte roamingovým uživatelům contoso (přenosný počítač a telefon) přístup k prostředkům společnosti, když nejsou v podnikové síti.

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Architektura virtuální sítě WAN Azure

Následující obrázek znázorňuje zobrazení na vysoké úrovni aktualizované cílové topologie pomocí Azure Virtual WAN ke splnění požadavků popsaných v předchozí části.

![Architektura virtuální sítě](./media/migrate-from-hub-spoke-topology/vwan-architecture.png)
WAN contoso**Obrázek: Architektura virtuální sítě AZURE**

Souhrn:

- Centrála v Evropě zůstává připojena k ExpressRoute, místní řadič domény v Evropě se plně migruje do Azure a nyní je vyřazen z provozu.
- Asie DC a HQ zůstávají připojeny k private WAN. Virtuální síť Azure WAN se teď používá k rozšíření místní sítě operátorů a k zajištění globální konektivity.
- Azure Virtual WAN rozbočovače nasazené v oblastech Azure v západní Evropě i jihovýchodní Asii, aby poskytovaly centrum připojení pro zařízení připojená přes ExpressRoute a VPN.
- Rozbočovače také poskytují ukončení VPN pro roamingové uživatele napříč více typy klientů pomocí připojení OpenVPN ke globální síti, což umožňuje přístup nejen k aplikacím migrovaným do Azure, ale také ke všem prostředkům, které zůstávají místně.
- Připojení k internetu pro prostředky v rámci virtuální sítě poskytované Azure Virtual WAN.

Připojení k internetu pro vzdálené weby poskytuje také Azure Virtual WAN. Místní internet breakout podporovaný prostřednictvím integrace partnera pro optimalizovaný přístup ke službám SaaS, jako je Office 365.

## <a name="migrate-to-virtual-wan"></a>Migrace na Virtual WAN

Tato část ukazuje různé kroky pro migraci do virtuální sítě Azure WAN.

### <a name="step-1-vdc-hub-and-spoke-single-region"></a>Krok 1: VDC oblast hub-and-spoke

Zkontrolujte architekturu. Následující obrázek znázorňuje jednu topologii oblasti pro Contoso před zavedením virtuální sítě Azure WAN:

![Topologie](./media/migrate-from-hub-spoke-topology/figure1.png)
jedné oblasti**Obrázek 1: VDC oblast s rozbočovačem a paprskem**

V souladu s přístupem Virtuální datové centrum (VDC) virtuální síť centra spravované zákazníkem obsahuje několik funkčních bloků:

- Sdílené služby (jakákoli společná funkce vyžadovaná více paprsky). Příklad: Společnost Contoso používá řadiče domény systému Windows Server na virtuálních počítačích Infrastructure-as-a-service (IaaS).
- Služby brány firewall IP/Routing jsou poskytovány síťovým virtuálním zařízením jiného výrobce, které umožňuje směrování IP vrstvy 3 s paprskem na paprsky.
- Služby příchozího a odchozího přenosu dat v Internetu, včetně Azure Application Gateway pro příchozí požadavky HTTPS a proxy služby třetích stran spuštěné na virtuálních počítačích pro odfiltrovaný odchozí přístup k internetovým prostředkům.
- Brána virtuální sítě ExpressRoute a VPN pro připojení k místním sítím.

### <a name="step-2-deploy-virtual-wan-hubs"></a>Krok 2: Nasazení virtuálních rozbočovačů WAN

Nasazení centra Virtuální WAN v každé oblasti. Nastavte rozbočovač Virtual WAN s bránou VPN gateway a bránou ExpressRoute, jak je popsáno v následujících článcích:

- [Kurz: Vytvoření připojení typu site-to-site pomocí služby Azure Virtual WAN](virtual-wan-site-to-site-portal.md)
- [Kurz: Vytvoření přidružení ExpressRoute pomocí azure virtuální sítě WAN](virtual-wan-expressroute-portal.md)

> [!NOTE]
> Virtuální wan Azure musí používat standardní skladovou položku k povolení některých cest provozu uvedených v tomto článku.

![Nasazení virtuálních](./media/migrate-from-hub-spoke-topology/figure2.png)
rozbočovačů WAN**Obrázek 2: VDC rozbočovač a mluvil o migraci virtuální sítě WAN**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Krok 3: Připojení vzdálených serverů (ExpressRoute a VPN) k virtuální síti WAN

Připojte rozbočovač Virtuální WAN k existujícím okruhům ExpressRoute a nastavte sítě VIRTUÁLNÍ SÍTĚ site-to-site přes Internet do všech vzdálených větví.

> [!NOTE]
> Pro připojení k virtuálnímu rozbočovači WAN musí být okruhy expresních tras upgradovány na typ sku Premium.

![Připojení vzdálených lokalit](./media/migrate-from-hub-spoke-topology/figure3.png)
k virtuální síti WAN**Obrázek 3: Rozbočovač v řadiči Domény a promlouvací sítě k migraci virtuální sítě WAN**

V tomto okamžiku místní síťové zařízení začne přijímat trasy odrážející adresní prostor IP přiřazený virtuální virtuální virtuální síti pro rozbočovače spravované pomocí sítě WAN. Vzdálené větve připojené k síti VPN v této fázi uvidí dvě cesty ke všem existujícím aplikacím ve virtuálních sítích s paprsky. Tato zařízení by měla být nakonfigurována tak, aby i nadále používala tunelové propojení k rozbočovači VDC, aby bylo zajištěno symetrické směrování během fáze přechodu.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>Krok 4: Testování hybridního připojení přes virtuální WAN

Před použitím spravovaného centra Virtual WAN hub pro produkční připojení doporučujeme nastavit virtuální síť s testovacím paprskem a připojení virtuální sítě WAN. Před pokračováním v dalších krocích ověřte, zda připojení k tomuto testovacímu prostředí fungují prostřednictvím technologie ExpressRoute a sítě VPN.

![Testování hybridního připojení](./media/migrate-from-hub-spoke-topology/figure4.png)
přes virtuální WAN**Obrázek 4: VDC rozbočovač a mluvil s migrací virtuální sítě WAN**

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>Krok 5: Přechod na připojení k virtuálnímu rozbočovači WAN

![Připojení k přechodu](./media/migrate-from-hub-spoke-topology/figure5.png)
do centra Virtuální WAN**Obrázek 5: VDC rozbočovač a mluvil o migraci virtuální sítě WAN**

**a**. Odstraňte existující připojení partnerského vztahu z virtuálních sítí Spoke do starého rozbočovače VDC. Přístup k aplikacím ve virtuálních sítích paprsku není k dispozici, dokud nebudou dokončeny kroky a-c.

**b**. Připojte virtuální sítě s paprsky k rozbočovači Virtual WAN prostřednictvím připojení virtuální sítě.

**c**. Odeberte všechny uživatelem definované trasy (UDR), které byly dříve používány ve virtuálních sítích s paprsky pro komunikaci s paprskem. Tato cesta je nyní povolena dynamickým směrováním, které je k dispozici v rozbočovači Virtuální síť WAN.

**d**. Existující brány ExpressRoute a VPN v centru VDC jsou nyní vyřazeny z provozu, aby umožnily další krok (e).

**e**. Připojte staré rozbočovač VDC (virtuální síť rozbočovače) k rozbočovači Virtual WAN prostřednictvím nového připojení virtuální sítě.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>Krok 6: Staré centrum se stává sdílenými službami

Nyní jsme přepracovali naši síť Azure tak, aby se centrum Virtuální WAN staly ústředním bodem naší nové topologie.

![Staré rozbočovač](./media/migrate-from-hub-spoke-topology/figure6.png)
se stane sdílenými službami**paprskem Obrázek 6: VDC hub-and-spoke to Virtual WAN migration**

Vzhledem k tomu, že centrum Virtual WAN je spravovaná entita a neumožňuje nasazení vlastních prostředků, jako jsou virtuální počítače, blok sdílených služeb nyní existuje jako virtuální síť pro paprsky a hostuje funkce, jako je příchozí internet prostřednictvím brány aplikace Azure nebo virtualizovaného síťového zařízení. Provoz mezi prostředím sdílených služeb a virtuálními počítači back-endu nyní prochází virtuálním centrem spravovaném wanem.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Krok 7: Optimalizace místního připojení pro plné využití virtuální sítě WAN

V této fázi společnost Contoso většinou dokončila migrace podnikových aplikací do cloudu Microsoft, přičemž v místním řadiči domény zůstalo pouze několik starších aplikací.

![Optimalizace místního připojení pro](./media/migrate-from-hub-spoke-topology/figure7.png)
plné využití virtuální sítě WAN**Obrázek 7: VDC rozbočovač a mluvil s migrací virtuální sítě WAN**

Aby contoso využil a využil plnou funkčnost virtuální sítě Azure WAN, rozhodne se vyřadit z provozu jejich starší místní připojení VPN. Všechny pobočky, které nadále přistupují k sítím HQ nebo DC, mohou přejíždět globální síť Microsoftu pomocí integrovaného tranzitního směrování virtuální sítě Azure WAN.

> [!NOTE]
> ExpressRoute Global Reach je alternativní volbou pro zákazníky, kteří chtějí využít páteřní sítě Microsoftu k doplnění svých stávajících privátních sítě WAN.

## <a name="end-state-architecture-and-traffic-paths"></a>Architektura koncového stavu a cesty provozu

![Architektura koncového stavu](./media/migrate-from-hub-spoke-topology/figure8.png)
a cesty provozu**Obrázek: Virtuální wan ve duální oblasti**

Tato část obsahuje souhrn toho, jak tato topologie splňuje původní požadavky při pohledu na některé příklady toků provozu.

### <a name="path-1"></a>Cesta 1

Cesta 1 zobrazuje tok provozu z pobočky s připojením K2S VPN v Asii do virtuální sítě Azure v oblasti jihovýchodní Asie.

Provoz je směrován takto:

- Asijská pobočka je propojena prostřednictvím odolných tunelů s podporou S2S BGP do virtuálního wan centra jihovýchodní Asie.

- Centrum Asia Virtual WAN směruje provoz místně do připojené virtuální sítě.

![Průtok 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>Cesta 2

Cesta 2 zobrazuje tok provozu z ExpressRoute připojené evropské hq k virtuální síti Azure v oblasti jihovýchodní Asie.

Provoz je směrován takto:

- Evropské velitelství je připojeno přes prémiový okruh ExpressRoute do virtuálního WAN centra západní Evropy.

- Globální připojení virtuální sítě WAN rozbočovače k rozbočovači umožňuje přenos do virtuální sítě připojené ve vzdálené oblasti.

![Průtok 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>Cesta 3

Cesta 3 zobrazuje tok provozu z místního řadiče domény asie připojeného k privátní síti WAN do evropské pobočky připojené pro S2S.

Provoz je směrován takto:

- Společnost Asia DC je připojena k místnímu soukromému operátorovi WAN.

- Okruh ExpressRoute místně končí v privátní síti WAN, která se připojuje k virtuálnímu wan rozbočovači jihovýchodní Asie.

- Globální připojení virtuální sítě WAN z rozbočovače k rozbočovači umožňuje přenos.

![Průtok 3](./media/migrate-from-hub-spoke-topology/flow3.png)

### <a name="path-4"></a>Cesta 4

Cesta 4 zobrazuje tok provozu z virtuální sítě Azure v oblasti jihovýchodní Asie do oblasti Azure Virtuální síť v západní Evropě.

Provoz je směrován takto:

- Globální připojení virtuální sítě WAN rozbočovače k rozbočovači umožňuje nativní přenos všech připojených virtuálních sítí Azure bez dalšího konfigurace uživatele.

![Průtok 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>Cesta 5

Cesta 5 zobrazuje tok provozu z roamingových uživatelů VPN (P2S) do virtuální sítě Azure v oblasti západní Evropy.

Provoz je směrován takto:

- Uživatelé notebooků a mobilních zařízení používají klienta OpenVPN pro transparentní připojení k bráně P2S VPN v západní Evropě.

- Virtuální rozbočovač WAN v západní Evropě směruje provoz místně do připojené virtuální sítě.

![Průtok 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Řízení zabezpečení a zásad přes Azure Firewall

Společnost Contoso nyní ověřila připojení mezi všemi větvemi a virtuálními sítěmi v souladu s požadavky popsanými dříve v tomto článku. Aby splnili své požadavky na řízení zabezpečení a izolaci sítě, musí pokračovat v oddělení a protokolování provozu prostřednictvím centrální sítě. Dříve byla tato funkce prováděna síťovým virtuálním zařízením (NVA). Contoso také chce vyřadit z provozu své stávající proxy služby a využívat nativní služby Azure pro filtrování odchozího Internetu.

![Zabezpečení a řízení zásad](./media/migrate-from-hub-spoke-topology/security-policy.png)
přes Azure Firewall**Obrázek: Azure Firewall ve virtuální mantinele (zabezpečené virtuální centrum)**

Následující kroky vysoké úrovně jsou nutné k zavedení brány Azure Firewall do center Virtuální WAN, aby bylo možné vytvořit jednotný bod řízení zásad. Další informace o tomto procesu a konceptu zabezpečených virtuálních rozbočovačů najdete ve [Správci brány firewall Azure](../firewall-manager/index.yml).

1. Vytvořte zásady Azure Firewall.
2. Propojte zásady brány firewall s centrem Azure Virtual WAN. Tento krok umožňuje existující virtuální WAN rozbočovač fungovat jako zabezpečené virtuální rozbočovač a nasazuje požadované prostředky Azure Firewall.

> [!NOTE]
> Pokud se brána Azure Firewall nasadí ve standardním virtuálním wan rozbočovači (SKU : Standard): Zásady V2V, B2V, V2I a B2I FW se vynucují jenom na provozech pocházejících z virtuálních sítí a větví připojených ke konkrétnímu rozbočovači, kde se nasazuje Azure FW (Zabezpečené centrum). Přenosy pocházející ze vzdálených virtuálních sítí a větví, které jsou připojené k jiným virtuálním rozbočovačům WAN ve stejné virtuální síti WAN, nebudou "brány firewall", i když jsou vzdálené větve a virtuální sítě propojeny prostřednictvím virtuálního rozbočovače WAN na propojení rozbočovačů. Podpora brány firewall mezi centry je na plánu Azure Virtual WAN a Firewall Manager.

Následující cesty zobrazují cesty připojení povolené pomocí virtuálních rozbočovačů Zabezpečené Azure:

### <a name="path-6"></a>Cesta 6

Cesta 6 zobrazuje tok zabezpečeného provozu mezi virtuálními sítěmi ve stejné oblasti.

Provoz je směrován takto:

- Virtuální sítě připojené ke stejnému zabezpečenému virtuálnímu portálu teď směrují provoz přes Azure Firewall.

- Azure Firewall můžete použít zásady pro tyto toky.

![Průtok 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>Cesta 7

Cesta 7 zobrazuje tok provozu z virtuální sítě Azure do Internetu nebo služby zabezpečení jiného výrobce.

Provoz je směrován takto:

- Virtuální sítě připojené k zabezpečenému virtuálnímu portálu mohou odesílat provoz do veřejných cílů na Internetu pomocí zabezpečeného centra jako centrálního bodu přístupu k Internetu.

- Tento provoz lze filtrovat místně pomocí pravidel azure firewall fqdn nebo odeslat službě zabezpečení třetí strany ke kontrole.

![Průtok 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>Cesta 8

Cesta 8 zobrazuje tok provozu z pobočky na Internet nebo služby zabezpečení jiného výrobce.

Provoz je směrován takto:

- Pobočky připojené k zabezpečenému virtuálnímu portálu mohou odesílat provoz do veřejných destinací na Internetu pomocí služby Secure Hub jako centrálního bodu přístupu k Internetu.

- Tento provoz lze filtrovat místně pomocí pravidel azure firewall fqdn nebo odeslat službě zabezpečení třetí strany ke kontrole.

![Průtok 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>Další kroky

Další informace o [virtuální mašaleu Azure](virtual-wan-about.md)
