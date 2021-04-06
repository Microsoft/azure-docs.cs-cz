---
title: Architektury připojení Virtual WAN a SD-WAN
titleSuffix: Azure Virtual WAN
description: Další informace o propojení privátního SD-WAN s Azure Virtual WAN
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sukishen
ms.openlocfilehash: ea9ddd05fe6fc745a3eefc29ab4f1d6aababc936
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94564697"
---
# <a name="sd-wan-connectivity-architecture-with-azure-virtual-wan"></a>Architektura připojení SD-WAN s Azure Virtual WAN

Azure Virtual WAN je síťová služba, která spojuje celou cloudovou konektivitu a služby zabezpečení s jedním provozním rozhraním. Mezi tyto služby patří větev (prostřednictvím sítě Site-to-Site VPN), vzdálený uživatel (připojení VPN typu Point-to-site), privátní připojení (ExpressRoute), přenosné připojení mezi cloudem pro virtuální sítě, VPN a ExpressRoute vzájemné propojení, směrování, Azure Firewall a šifrování pro privátní připojení.

I když je samotný Azure Virtual WAN sám o softwaru definovaná síť WAN (SD-WAN), je také navržena tak, aby umožňovala bezproblémové propojení s místními technologiemi a službami SD-WAN. Mnohé takové služby nabízí náš virtuální ekosystém sítě [WAN](virtual-wan-locations-partners.md) a partneři Azure Networking Managed Services [(účastníci programu MSP)](../networking/networking-partners-msp.md). Podniky, které transformují privátní síť WAN na SD-WAN, mají možnosti při vzájemném propojení privátního SD-WAN s Azure Virtual WAN. Podniky si můžou vybrat z těchto možností:

* Model přímého propojení
* Model přímého propojení s síťové virtuální zařízení-in-VWAN-hub
* Model nepřímých propojení
* Spravovaný hybridní model sítě WAN pomocí svého oblíbeného poskytovatele spravované služby [MSP](../networking/networking-partners-msp.md)

Ve všech těchto případech je propojení virtuální sítě WAN s SD-WAN podobné jako na straně připojení, ale může se lišit v orchestraci a provozní straně.

## <a name="direct-interconnect-model"></a><a name="direct"></a>Model přímého propojení

:::image type="content" source="./media/sd-wan-connectivity-architecture/direct.png" alt-text="Model přímého propojení":::

V tomto modelu architektury se k virtuálním rozbočovačům WAN prostřednictvím připojení IPsec připojuje přímo místní zařízení (CPE). Větev CPE může být také připojena k ostatním větvím prostřednictvím privátního SD-WAN nebo prostřednictvím virtuální sítě WAN pro větev připojení k síti. Větve, které potřebují přístup ke svým úlohám v Azure, budou moci přímo a bezpečně přistupovat k Azure prostřednictvím tunelů IPsec, které jsou ukončeny ve virtuálních sítích WAN.

Pomocí SD-WAN CPE můžou partneři povolit automatizaci, aby se v příslušných zařízeních CPE automatizují běžně únavné a náchylné k chybám připojení IPsec. Automatizace umožňuje, aby řadič SD-WAN komunikoval se službou Azure prostřednictvím rozhraní API služby Virtual WAN a nakonfiguroval virtuální sítě WAN a napředal CPEs konfiguraci tunelu IPsec do větve. Popis automatizace propojení virtuálních sítí WAN v různých partnerech SD-WAN najdete v tématu [pokyny pro automatizaci](virtual-wan-configure-automation-providers.md) .

SD-WAN CPE pokračuje v místě, kde se implementuje a vynutila optimalizace provozu i výběr cesty. 

V tomto modelu nemusí být některé z optimalizace provozu spravovaného dodavatelem na základě charakteristik provozu v reálném čase podporované, protože připojení k virtuální síti WAN je nad protokolem IPsec a síť VPN s protokolem IPsec je ve virtuální síti VPN Gateway ukončena. Například volba dynamické cesty ve větvi CPE je proveditelná v důsledku toho, že zařízení vyměňuje různé informace o síťovém paketu s jiným uzlem SD-WAN, a proto identifikuje nejlepší odkaz na použití pro různé prioritní přenosy dynamicky ve větvi. Tato funkce může být užitečná v oblastech, kde je potřeba optimalizace poslední kilometry (větev k nejbližšímu programu Microsoft POP).

Pomocí virtuální sítě WAN můžou uživatelé získat výběr cest Azure, což je výběr cesty založený na zásadách mezi více odkazy poskytovatele internetových služeb od větve CPE ke branám VPN od společnosti WAN. Virtuální síť WAN umožňuje nastavení více odkazů (cest) ze stejné větve SD-WAN CPE; každé propojení představuje připojení s duálním tunelem z jedinečné veřejné IP adresy SD-WAN CPE do dvou různých instancí služby Azure Virtual WAN VPN Gateway. Dodavatelé SD-WAN můžou implementovat optimální cestu k Azure, a to na základě zásad provozu stanovených jejich modulem zásad na odkazech CPE. Na konci Azure se všechna připojení, která přicházejí v systému, považují za stejnou.

## <a name="direct-interconnect-model-with-nva-in-vwan-hub"></a><a name="direct"></a>Model přímého propojení s síťové virtuální zařízení-in-VWAN-hub

:::image type="content" source="./media/sd-wan-connectivity-architecture/direct-nva.png" alt-text="Model přímého propojení s síťové virtuální zařízení-in-VWAN-hub":::

Tento model architektury podporuje nasazení [síťového virtuálního zařízení (síťové virtuální zařízení) jiného výrobce přímo do virtuálního rozbočovače](./about-nva-hub.md). To umožňuje zákazníkům, kteří chtějí připojit svou pobočku CPE ke stejné značce síťové virtuální zařízení ve virtuálním centru, aby při připojování k úlohám Azure mohli využít výhod všech komplexních funkcí SD-WAN. 

Některé virtuální partneři sítě WAN pracovali tak, aby poskytovaly prostředí, které v rámci procesu nasazení automaticky konfiguruje síťové virtuální zařízení. Jakmile se síťové virtuální zařízení zřídí do virtuálního rozbočovače, všechny další konfigurace, které může být potřeba pro síťové virtuální zařízení, se musí provádět prostřednictvím portálu síťové virtuální zařízení partner nebo aplikace pro správu. Přímý přístup k síťové virtuální zařízení není k dispozici. Síťová virtuální zařízení, které jsou k dispozici pro nasazení přímo do služby Azure Virtual WAN, jsou navržené speciálně pro použití ve virtuálním centru. Pro partnery, kteří podporují síťové virtuální zařízení v centru VWAN a jejich Průvodci nasazením, si přečtěte článek věnované [virtuálním partnerům sítě WAN](virtual-wan-locations-partners.md#partners-with-integrated-virtual-hub-offerings) .

SD-WAN CPE pokračuje v místě, kde se implementuje a vynutila optimalizace provozu i výběr cesty.
V tomto modelu je podporovaná optimalizace provozu od dodavatele, která je založená na charakteristikách provozu v reálném čase, protože připojení k virtuální síti WAN je prostřednictvím síťové virtuální zařízení SD-WAN v centru.

## <a name="indirect-interconnect-model"></a><a name="indirect"></a>Model nepřímých propojení

:::image type="content" source="./media/sd-wan-connectivity-architecture/indirect.png" alt-text="Model nepřímých propojení":::

V tomto modelu architektury se k virtuálním rozbočovačům sítě WAN nepřímo připojí CPEsá větev SD-WAN. Jak ukazuje obrázek, virtuální CPE SD-WAN se nasadí do virtuální sítě typu Enterprise. Tento virtuální počítač se pak připojí k virtuálním rozbočovačům WAN pomocí protokolu IPsec. Virtuální CPE slouží jako brána SD-WAN do Azure. Větve, které potřebují přístup ke svým úlohám v Azure, budou k nim mít přístup přes bránu v-CPE.

Vzhledem k tomu, že připojení k Azure je přes bránu v-CPE (síťové virtuální zařízení), veškerý provoz do a z úlohy Azure virtuální sítě na jiné větve SD-WAN přecházejí přes síťové virtuální zařízení. V tomto modelu je uživatel zodpovědný za správu a provozování síťové virtuální zařízení SD-WAN, včetně vysoké dostupnosti, škálovatelnosti a směrování.
  
## <a name="managed-hybrid-wan-model"></a><a name="hybrid"></a>Spravovaný hybridní model sítě WAN

:::image type="content" source="./media/sd-wan-connectivity-architecture/hybrid.png" alt-text="Spravovaný hybridní model sítě WAN":::

V tomto modelu architektury můžou podniky využívat spravované služby SD-WAN nabízené partnerem spravovaného poskytovatele služeb (MSP). Tento model je podobný přímým nebo nepřímým modelům popsaným výše. V tomto modelu se ale návrh SD-WAN, Orchestrace a operace doručí poskytovateli SD-WAN.

[Partneři Azure Networking MSP](../networking/networking-partners-msp.md) můžou použít [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) k implementaci služby SD-WAN a virtuální sítě WAN v předplatném Azure pro podnikové zákazníky a také provozovat koncovou síť WAN v zastoupení zákazníka. Tyto účastníci programu MSP můžou také implementovat Azure ExpressRoute do virtuální sítě WAN a provozovat je jako kompletní spravovaná služba.

## <a name="additional-information"></a>Další informace

* [Zahrnout Nejčastější dotazy](virtual-wan-faq.md)
* [Řešení vzdáleného připojení](work-remotely-support.md)