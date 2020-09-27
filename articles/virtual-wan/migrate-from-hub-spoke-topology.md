---
title: 'Architektura: migrace do Azure Virtual WAN'
description: Naučte se migrovat na Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: b07ed4589a54948ef87f516ac4bb97ef8492283e
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398831"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migrace do Azure Virtual WAN

Azure Virtual WAN umožňuje společnostem zjednodušit globální připojení, aby bylo možné využívat škálování globální sítě Microsoftu. Tento článek poskytuje technické podrobnosti pro společnosti, které chtějí migrovat z existující topologie centra a paprsků spravované zákazníkem, do návrhu, který využívá virtuální sítě WAN spravované Microsoftem.

Informace o výhodách, které Azure Virtual WAN umožňuje podnikům, které přijímají moderní cloudovou globální síť v cloudu, najdete v tématu [globální přenosová architektura sítě a virtuální síť WAN](virtual-wan-global-transit-network-architecture.md).

![střed a paprskový ](./media/migrate-from-hub-spoke-topology/hub-spoke.png)
 **obrázek: Azure Virtual WAN**

Model připojení centra Azure a paprsků přijaly tisíce našich zákazníků, aby využili výchozí chování přenositelného směrování sítí Azure, aby bylo možné vytvářet jednoduché a škálovatelné cloudové sítě. Azure Virtual WAN staví na těchto konceptech a zavádí nové funkce, které umožňují globální topologie připojení, nejen mezi místními umístěními a Azure, ale také umožňují zákazníkům využít škálování sítě Microsoftu k rozšíření stávajících globálních sítí.

V tomto článku se dozvíte, jak migrovat existující hybridní prostředí na virtuální síť WAN.

## <a name="scenario"></a>Scénář

Contoso je globální finanční organizace s kancelářemi v Evropě i v Asii. Plánují přesun svých stávajících aplikací z místního datového centra do Azure a vytvořili jsme základní návrh na základě ruční architektury centra a paprsků, včetně místních virtuálních sítí centra spravovaných zákazníkem pro hybridní připojení. V rámci přechodu na cloudové technologie byly síťovému týmu zajišťovat, aby bylo zajištěno, že je jejich připojení optimalizované pro pohyb vpřed.

Následující obrázek znázorňuje nejdůležitější pohled na stávající globální síť, včetně připojení k několika oblastem Azure.

![Obrázek stávající síťové topologie společnosti Contoso ](./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png)
 **: síťová topologie contoso stávající sítě**

Z existující topologie sítě se dají chápat tyto body:

- Topologie centra a paprsků se používá v několika oblastech, včetně okruhů ExpressRoute Premium pro připojení zpátky ke společné privátní síti WAN.

- Některé z těchto lokalit mají také tunely VPN přímo v Azure pro přístup k aplikacím hostovaným v cloudu Microsoftu.

## <a name="requirements"></a>Požadavky

Síťový tým byl vytvořen s poskytováním globálního síťového modelu, který může podporovat migraci společnosti Contoso do cloudu a musí být optimalizován v oblastech nákladů, škálování a výkonu. V souhrnu jsou splněné následující požadavky:

- Poskytněte sídel (hlavní čtvrtletí) i pobočky s optimalizovanou cestou k aplikacím hostovaným v cloudu.
- Odeberte závislosti na stávajících místních datových centrech (DC) pro ukončení sítě VPN a zachovejte následující cesty připojení:
  - **Větvení na virtuální síť**: pobočky připojené k síti VPN musí být schopné získat přístup k aplikacím, které jsou migrovány do cloudu v místní oblasti Azure.
  - **Větvení**do sítě VNet: pobočky připojené k síti VPN musí být schopné získat přístup k aplikacím migrovaných do cloudu ve vzdálené oblasti Azure.
  - **Větev-do větve**: pobočky připojené k síti VPN musí být schopné komunikovat mezi ostatními a ExpressRoute připojenými sídel/řadiči DC.
  - **Větvení na větvení**: globálně oddělené pobočky připojené k síti VPN musí být schopné vzájemně komunikovat a všechny EXPRESSROUTE připojené sídel/DC servery.
  - Připojení **k Internetu**: připojené lokality musí být schopné komunikovat s internetem. Tento provoz se musí filtrovat a protokolovat.
  - **VNet-to-VNet**: virtuální sítě rozbočovače ve stejné oblasti musí být schopné vzájemně komunikovat.
  - **VNet-to**-hub-peer-to-VNet: virtuální sítě rozbočovače v různých oblastech musí být schopné vzájemně komunikovat.
- Umožněte uživatelům pro roaming společnosti Contoso přístup k prostředkům společnosti, a to i v případě, že nejsou v podnikové síti.

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Architektura Azure Virtual WAN

Následující obrázek ukazuje podrobný pohled na aktualizovanou cílovou topologii pomocí Azure Virtual WAN, který splňuje požadavky popsané v předchozí části.

![Virtuální architektura společnosti Contoso Virtual WAN ](./media/migrate-from-hub-spoke-topology/vwan-architecture.png)
 **obrázek: architektura Azure Virtual WAN**

Souhrn:

- SÍDEL v Evropě zůstává ExpressRoute připojená, Evropa místní řadič domény se úplně migruje do Azure a teď je vyřazený z provozu.
- Asie a sídel pro asijské domény zůstávají připojené k privátní síti WAN. Azure Virtual WAN se teď používá k rozšíření místní sítě dopravců a k zajištění globálního připojení.
- Virtuální rozbočovače Azure Virtual WAN nasazené v oblastech Západní Evropa a jih Východní Asie Azure pro poskytování centra připojení pro zařízení připojená k síti VPN ExpressRoute a VPN.
- Rozbočovače také poskytují ukončení sítě VPN pro roaming uživatelů napříč různými typy klientů pomocí připojení OpenVPN k globální síti sítě a umožňují přístup nejen k aplikacím migrováným do Azure, ale i k jakýmkoli místním prostředkům.
- Připojení k Internetu pro prostředky v rámci virtuální sítě poskytované službou Azure Virtual WAN.

Připojení k Internetu pro vzdálené lokality, které poskytuje i služba Azure Virtual WAN. Místní internetový užitečných podporované prostřednictvím integrace partnerů pro optimalizaci přístupu ke službám SaaS, jako je Microsoft 365.

## <a name="migrate-to-virtual-wan"></a>Migrace na Virtual WAN

V této části se dozvíte o různých krocích migrace do Azure Virtual WAN.

### <a name="step-1-single-region-customer-managed-hub-and-spoke"></a>Krok 1: samoobslužné centrum a paprsky spravované zákazníky v jedné oblasti

Následující obrázek ukazuje topologii jedné oblasti pro společnost Contoso před zavedením Azure Virtual WAN:

![Obrázek topologie s jednou oblastí ](./media/migrate-from-hub-spoke-topology/figure1.png)
 **1: ruční rozbočovač s jednou oblastí a paprsek**

V souladu s přístupem k rozbočovači a paprsku má virtuální síť centra spravovaná zákazníkem několik bloků funkcí:

- Sdílené služby (jakákoli společná funkce požadovaná více paprsky). Příklad: contoso používá řadiče domény Windows serveru na virtuálních počítačích infrastruktury jako služba (IaaS).
- Služby brány firewall protokolu IP/směrování jsou poskytovány virtuálním síťovým zařízením třetí strany a umožňují směrování IP s paprskovou a koncovou vrstvou 1.
- Internetové příchozí/odchozí služby, včetně Azure Application Gateway pro příchozí požadavky HTTPS a služby proxy třetích stran běžící na virtuálních počítačích pro filtrovaný odchozí přístup k internetovým prostředkům.
- Brána virtuální sítě VPN ExpressRoute a VPN pro připojení k místním sítím.

### <a name="step-2-deploy-virtual-wan-hubs"></a>Krok 2: nasazení virtuálních rozbočovačů sítě WAN

Nasaďte v každé oblasti virtuální síť WAN hub. Nastavte virtuální centrum sítě WAN pomocí VPN Gateway a brány ExpressRoute, jak je popsáno v následujících článcích:

- [Kurz: Vytvoření připojení typu site-to-site pomocí služby Azure Virtual WAN](virtual-wan-site-to-site-portal.md)
- [Kurz: vytvoření přidružení ExpressRoute pomocí Azure Virtual WAN](virtual-wan-expressroute-portal.md)

> [!NOTE]
> Azure Virtual WAN musí používat standardní SKU k povolení některých cest k provozu, které jsou uvedené v tomto článku.

![Obrázek 2: nasazení virtuálních rozbočovačů WAN ](./media/migrate-from-hub-spoke-topology/figure2.png)
 **na úrovni zákazníka a připojení k virtuální síti WAN spravované zákazníkem**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Krok 3: připojení vzdálených lokalit (ExpressRoute a VPN) k virtuální síti WAN

Připojte virtuální síť WAN ke stávajícím okruhům ExpressRoute a nastavte VPN typu Site-to-site přes Internet do všech vzdálených větví.

> [!NOTE]
> Okruhy Express Routes musí být upgradovány na typ SKU úrovně Premium pro připojení k virtuálnímu centru sítě WAN.

![Připojení vzdálených lokalit k virtuální síti WAN ](./media/migrate-from-hub-spoke-topology/figure3.png)
 **Obrázek 3: migrace centra a připojení k virtuální síti WAN spravovaná zákazníkem**

V tomto okamžiku začnou místní síťové zařízení přijímat trasy odrážející adresní prostor IP adres přiřazený virtuální síti rozbočovače spravovanému přes síť WAN. Vzdálené větve připojené k síti VPN v této fázi uvidí dvě cesty k jakýmkoli existujícím aplikacím ve virtuálních sítích paprsků. Tato zařízení by měla být nakonfigurovaná tak, aby pokračovala v používání tunelu u zákaznicky spravovaného centra, aby se zajistilo symetrické směrování během fáze přechodu.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>Krok 4: testování hybridního připojení přes virtuální síť WAN

Před použitím spravovaného virtuálního centra sítě WAN pro připojení k provozu doporučujeme nastavit virtuální síť s koncovým paprskem a připojení k virtuální síti WAN. Než budete pokračovat v dalších krocích, ověřte, že připojení k tomuto testovacímu prostředí fungují přes ExpressRoute a Site-to Site VPN.

![Testování hybridního připojení přes virtuální síť WAN ](./media/migrate-from-hub-spoke-topology/figure4.png)
 **na obrázku 4: migrace centra a připojení k virtuální síti WAN spravovaná zákazníkem**

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>Krok 5: přechod k virtuálnímu centru WAN

![Přechod připojení na virtuální síť WAN – ](./media/migrate-from-hub-spoke-topology/figure5.png)
 **Obrázek 5: migrace centra a paprsků na virtuální síť WAN**

**a**. Odstraňte existující připojení partnerského vztahu z virtuálních sítí s koncovými zákazníky k původnímu centru spravovanému zákazníkem. Přístup k aplikacím ve virtuálních sítích s paprsky není k dispozici, dokud nebudou dokončeny kroky a-c.

**b**. Virtuální sítě rozbočovače připojte k virtuální síti WAN prostřednictvím připojení virtuální sítě.

**c**. Odeberte všechny trasy definované uživatelem (UDR) dříve používané v rámci virtuálních sítí paprsků pro komunikaci mezi paprsky a paprsky. Tato cesta je teď povolená dynamickým směrováním dostupným ve virtuálním centru WAN.

**d**. Stávající brány ExpressRoute a VPN v centru spravovaném zákazníkem jsou teď vyřazené z provozu a umožňují další krok (e).

**e**. Připojte původní centrum spravované zákazníkem (virtuální síť rozbočovače) k virtuálnímu rozbočovači WAN prostřednictvím nového připojení virtuální sítě.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>Krok 6: starý rozbočovač se bude nacházet s paprsky sdílených služeb

Nyní jsme převedli návrh naší sítě Azure, aby virtuální síť WAN měla centrální bod v naší nové topologii.

![Starý rozbočovač se bude nacházet jako sdílené služby Shared ](./media/migrate-from-hub-spoke-topology/figure6.png)
 **– Obrázek 6: migrace centra a připojení k virtuální síti WAN spravovaná zákazníkem**

Vzhledem k tomu, že virtuální síť WAN je spravovaná entita a neumožňuje nasazení vlastních prostředků, jako jsou virtuální počítače, blokuje služba Shared Services jako virtuální síť paprsků a hostuje funkce, jako je třeba internetové příchozí přenosy prostřednictvím Azure Application Gateway nebo síťové virtualizované zařízení. Přenos dat mezi prostředím sdílených služeb a back-end virtuálními počítači teď projíždějí virtuálním centrem spravovanému přes síť WAN.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Krok 7: Optimalizujte místní připojení, aby bylo možné plně využívat virtuální síť WAN.

V této fázi společnost Contoso většinou dokončila své migrace obchodních aplikací do Microsoft Cloud a v místním řadiči domény zbývá jenom několik starších verzí aplikací.

![Optimalizuje místní připojení, aby se plně využila virtuální síť WAN ](./media/migrate-from-hub-spoke-topology/figure7.png)
 **Obrázek 7: migrace centra a paprsků do virtuální sítě WAN spravovaná zákazníkem.**

Aby bylo možné využívat všechny funkce Azure Virtual WAN, společnost Contoso se rozhodla vyřadit z provozu starší místní připojení VPN. Všechny větve, které budou mít nadále přístup k sítím sídel nebo DC, můžou přesměrovat globální síť Microsoft pomocí integrovaného tranzitního směrování Azure Virtual WAN.

> [!NOTE]
> ExpressRoute Global Reach je alternativní volbou pro zákazníky, kteří chtějí využít páteřní síť Microsoftu k doplnění stávajících privátních sítí WAN.

## <a name="end-state-architecture-and-traffic-paths"></a>Architektura koncových stavů a cest provozu

![Architektura koncových stavů a cest provozu ](./media/migrate-from-hub-spoke-topology/figure8.png)
 **: virtuální síť Dual pro duální oblast**

Tato část poskytuje přehled o tom, jak tato topologie splňuje původní požadavky, a to tak, že se podíváme na několik ukázkových toků provozu.

### <a name="path-1"></a>Cesta 1

Cesta 1 zobrazuje tok přenosů z větve připojení S2S VPN v Asii do virtuální sítě Azure v oblasti jih Východní Asie.

Provoz se směruje takto:

- Větev Asie je připojená prostřednictvím tunelového propojení s povoleným protokolem S2S BGP do centra Východní Asie virtuální sítě WAN v jihoasijských sítích.

- Asie z virtuálních sítí WAN směrují provoz místně do připojené virtuální sítě.

![Flow 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>Cesta 2

Cesta 2 ukazuje tok provozu z ExpressRoute s připojeným Evropským sídel k virtuální síti Azure v oblasti jih Východní Asie.

Provoz se směruje takto:

- Evropská sídel je prostřednictvím okruhu Premium ExpressRoute připojená k Západní Evropa virtuálnímu centru WAN.

- Globální připojení typu rozbočovač sítě WAN do rozbočovače umožňuje přenos provozu do sítě VNet připojené ve vzdálené oblasti.

![Flow 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>Cesta 3

Cesta 3 ukazuje přenosový tok z místního řadiče domény Asie, který je připojený k privátní síti WAN, do Evropské připojené větve S2S.

Provoz se směruje takto:

- Asie – řadič domény je připojený k místnímu privátnímu nosiči WAN.

- Okruh ExpressRoute místně končí v privátní síti WAN a připojuje se k rozbočovači Východní Asie virtuální síti WAN v jihoasijských sítích.

- Globální připojení z rozbočovače WAN na rozbočovač umožňuje přenos provozu.

![Tok 3](./media/migrate-from-hub-spoke-topology/flow3.png)

### <a name="path-4"></a>Cesta 4

Cesta 4 ukazuje přenosový tok z virtuální sítě Azure v oblasti Jižní Východní Asie do virtuální sítě Azure v oblasti Západní Evropa.

Provoz se směruje takto:

- Globální připojení z rozbočovače WAN na rozbočovač umožňuje nativní přenos všech připojených Azure virtuální sítě bez dalších konfiguračních konfigurací uživatele.

![Tok 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>Cesta 5

Cesta 5 zobrazuje tok přenosů z uživatelů cestovní sítě VPN (P2S) do virtuální sítě Azure v oblasti Západní Evropa.

Provoz se směruje takto:

- Uživatelé přenosných počítačů a mobilních zařízení používají klienta OpenVPN pro transparentní připojení k bráně VPN P2S v Západní Evropa.

- Západní Evropa virtuální síť WAN směruje provoz místně do připojené virtuální sítě.

![Tok 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Zabezpečení a řízení zásad prostřednictvím Azure Firewall

Společnost Contoso nyní ověřila připojení mezi všemi větvemi a virtuální sítě v souladu s požadavky uvedenými výše v tomto článku. Aby bylo možné splnit požadavky na řízení zabezpečení a izolaci sítě, musí nadále oddělit a Protokolovat provoz přes síť centrální sítě. Dřív se tato funkce prováděla síťovým virtuálním zařízením (síťové virtuální zařízení). Společnost Contoso také chce vyřadit existující proxy služby a využívat nativní služby Azure pro odchozí internetové filtrování.

![Zabezpečení a řízení zásad prostřednictvím Azure Firewall ](./media/migrate-from-hub-spoke-topology/security-policy.png)
 **obrázek: Azure firewall ve virtuální síti WAN (zabezpečené virtuální rozbočovač)**

Následující kroky vysoké úrovně jsou nutné k zavedení Azure Firewall do virtuálních rozbočovačů sítě WAN, aby bylo možné povolit sjednocený bod řízení zásad. Další informace o tomto procesu a konceptu zabezpečených virtuálních rozbočovačů najdete v tématu [Azure firewall Manager](../firewall-manager/index.yml).

1. Vytvořte zásady Azure Firewall.
2. Připojte zásady brány firewall ke službě Azure Virtual WAN hub. Tento krok umožňuje stávajícímu virtuálnímu rozbočovači sítě WAN fungovat jako zabezpečené virtuální rozbočovač a nasadí požadované prostředky Azure Firewall.

> [!NOTE]
> Pokud je Azure Firewall nasazena ve standardním virtuálním rozbočovači sítě WAN (SKU: Standard): zásady V2V, B2V, V2I a B2I FW se budou uplatňovat jenom na provoz pocházející z virtuální sítě a větví připojených ke konkrétnímu centru, kde je rozhraní Azure FW nasazené (zabezpečené centrum). Přenosy pocházející ze vzdálených virtuální sítě a větví, které jsou připojené k ostatním virtuálním rozbočovačům WAN ve stejné virtuální síti WAN, nebudou "brány firewall", a to i v případě, že jsou vzdálené větve a virtuální sítě propojené přes virtuální síť WAN až po odkazy na rozbočovače. Podpora brány firewall mezi rozbočovači je v plánu Azure Virtual WAN a Správce brány firewall.

Následující cesty ukazují cesty k připojení, které jsou povolené pomocí zabezpečených virtuálních rozbočovačů Azure:

### <a name="path-6"></a>Cesta 6

Cesta 6 zobrazuje zabezpečený tok provozu mezi virtuální sítě ve stejné oblasti.

Provoz se směruje takto:

- Virtuální sítě připojené ke stejnému zabezpečenému virtuálnímu rozbočovači nyní směrují provoz do přes Azure Firewall.

- Azure Firewall můžou pro tyto toky použít zásady.

![Tok 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>Cesta 7

Cesta 7 ukazuje tok přenosů z virtuální sítě Azure do Internetu nebo služby zabezpečení třetích stran.

Provoz se směruje takto:

- Virtuální sítě připojené k zabezpečenému virtuálnímu rozbočovači mohou odesílat provoz do veřejné sítě, jejichž cílem je síť Internet, a to pomocí zabezpečeného centra jako centrálního bodu přístupu k Internetu.

- Tento provoz je možné filtrovat místně pomocí Azure Firewall pravidel plně kvalifikovaného názvu domény nebo odeslat službě zabezpečení třetí strany pro kontrolu.

![Tok 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>Cesta 8

Cesta 8 zobrazuje tok přenosů z meziinternetu nebo služby zabezpečení třetích stran.

Provoz se směruje takto:

- Větve připojené k zabezpečenému virtuálnímu rozbočovači mohou odesílat provoz do veřejných cílů na internetu pomocí zabezpečeného centra jako centrálního bodu přístupu k Internetu.

- Tento provoz je možné filtrovat místně pomocí Azure Firewall pravidel plně kvalifikovaného názvu domény nebo odeslat službě zabezpečení třetí strany pro kontrolu.

![Tok 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>Další kroky

Další informace o [Azure Virtual WAN](virtual-wan-about.md)
