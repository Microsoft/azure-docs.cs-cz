---
title: Co jsou důvěryhodní partneři zabezpečení Azure Firewall Manager (preview)
description: Informace o důvěryhodných partnerech azure firewall manageru pro zabezpečení
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: b92242ce9086579d0397f78853402cfc08453f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436775"
---
# <a name="what-are-trusted-security-partners-preview"></a>Kdo jsou důvěryhodní partneři pro zabezpečení (Preview)?

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Důvěryhodní partneři zabezpečení (preview)* ve Správci Azure Firewall vám pomohou používat známé nabídky zabezpečení třetích stran jako služby (SECaaS) jako služby k ochraně přístupu k internetu pro vaše uživatele.

Díky rychlé konfiguraci můžete zabezpečit rozbočovač s podporovaným partnerem zabezpečení a směrovat a filtrovat internetový provoz z virtuálních sítí (Virtuálnísítě) nebo umístění poboček v rámci oblasti. To se provádí pomocí automatizované správy tras bez nastavení a správy uživatelem definovaných tras (UDR).

Zabezpečená rozbočovače nakonfigurovaná s partnerem zabezpečení, který si vyberete, můžete nasadit ve více oblastech Azure, abyste získali připojení a zabezpečení pro své uživatele kdekoli na světě v těchto oblastech. Díky možnosti používat nabídku partnera zabezpečení pro přenos aplikací přes Internet/SaaS a bránu Azure Firewall pro privátní provoz v zabezpečených rozbočovačích teď můžete začít vytvářet výhodu zabezpečení v Azure, která je blízká globálně distribuovaným uživatelům a aplikacím.

Pro tento náhled jsou podporováni partneři zabezpečení **ZScaler** a **iboss**. Podporované oblasti jsou WestCentralUS, NorthCentralUS, WestUS, WestUS2 a EastUS.

![Důvěryhodní partneři pro zabezpečení](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Klíčové scénáře

Partneři zabezpečení můžete použít k filtrování internetového provozu v následujících scénářích:

- Virtuální síť (Virtuální síť) k Internetu

   Využijte pokročilou internetovou ochranu s požadavky uživatelů pro vaše cloudové úlohy spuštěné v Azure.

- Pobočka na Internet

   Využijte připojení Azure a globální distribuci ke snadnému přidání filtrování NSaaS třetích stran pro větve do internetových scénářů. Globální přenosovou síť a okraj zabezpečení můžete vytvořit pomocí azure virtuální sítě WAN.

Jsou podporovány následující scénáře:
-   Virtuální síť k internetu prostřednictvím nabídky partnera třetí strany.
-   Pobočka na internetu prostřednictvím nabídky partnera třetí strany.
-   Pobočka k Internetu prostřednictvím nabídky partnera třetí strany, zbytek soukromého provozu (Spoke-to-Spoke, Spoke-to-Branches, Branch-to-Spokes) přes Azure Firewall.

Následující scénář není podporován:

- Virtuální síť k internetu prostřednictvím nabídky partnerů nelze kombinovat s Azure Firewall pro privátní provoz. Podívejte se na následující omezení.

## <a name="current-limitations"></a>Aktuální omezení

- Pro virtuální síť k Internetu, nelze kombinovat přidání Azure Firewall pro privátní provoz a partnera nabízí pro internetový provoz. Internetový provoz můžete odeslat do brány Azure Firewall nebo partnera zabezpečení jiného výrobce, který nabízí v zabezpečeném virtuálním rozbočovači, ale ne obojí. 
- Můžete nasadit maximálně jednoho partnera zabezpečení na virtuální rozbočovač. Pokud potřebujete změnit zprostředkovatele, musíte odebrat stávajícího partnera a přidat nového.

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Doporučené postupy pro filtrování internetového provozu v zabezpečených virtuálních rozbočovačích

Internetový provoz obvykle zahrnuje webový provoz. Ale zahrnuje také provoz určený pro aplikace SaaS, jako je Office 365 (O365) a veřejné služby Azure PaaS, jako je Azure Storage, Azure Sql a tak dále. Následující jsou doporučení osvědčených postupů pro zpracování provozu na tyto služby:

### <a name="handling-azure-paas-traffic"></a>Zpracování provozu Azure PaaS
 
- Azure Firewall pro ochranu použijte, pokud se váš provoz skládá převážně z Azure PaaS a přístup k prostředkům pro vaše aplikace lze filtrovat pomocí IP adres, skupinových skupin kvedu, značek služeb nebo značek FQDN.

- Pokud se váš provoz skládá z přístupu k aplikacím SaaS nebo potřebujete filtrování podle uživatelů (například pro úlohy infrastruktury virtuálních desktopů (VDI), nebo potřebujete pokročilé možnosti filtrování internetu, použijte ve svých rozbočovačích řešení pro partnery třetích stran.

![Všechny scénáře pro Správce azure firewall](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Manipulace s provozem Office 365 (O365)

V globálně distribuovaných scénářích umístění větev byste měli přesměrovat provoz Office 365 přímo na pobočce před odesláním zbývajícího internetového provozu zabezpečeného centra Azure.

Pro Office 365 je latence sítě a výkon důležité pro úspěšné uživatelské prostředí. K dosažení těchto cílů kolem optimální výkon a uživatelské prostředí, zákazníci musí implementovat Office 365 přímé a místní escape před zvážením směrování zbytek internetového provozu přes Azure.

[Principy připojení k síti Office 365](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) vyžadují, aby klíčová síťová připojení Office 365 byla směrována místně z pobočky uživatele nebo mobilního zařízení a přímo přes Internet do nejbližšího síťového bodu Microsoftu.

Připojení Office 365 jsou navíc silně šifrovaná z důvodu ochrany osobních údajů a používají efektivní proprietární protokoly z důvodů výkonu. Díky tomu je nepraktické a působivé, aby tato připojení podléhala tradičním řešením zabezpečení na úrovni sítě. Z těchto důvodů důrazně doporučujeme, aby zákazníci odeslali provoz Office 365 přímo z poboček, než odešlou zbytek provozu prostřednictvím Azure. Microsoft spolupracuje s několika poskytovateli řešení SD-WAN, kteří se integrují s Azure a Office 365 a zákazníkům usnadňují povolení přímého a místního internetu Office 365. Podrobnosti najdete [v tématu Jak nastavím zásady O365 přes virtuální WAN?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)


## <a name="next-steps"></a>Další kroky

[Nasazení nabídky důvěryhodného zabezpečení v zabezpečeném rozbočovači pomocí Správce azure firewallu](deploy-trusted-security-partner.md).
