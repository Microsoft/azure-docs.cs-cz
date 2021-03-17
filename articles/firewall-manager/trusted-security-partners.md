---
title: Co jsou poskytovatelé partnerů zabezpečení Azure Firewall Manageru?
description: Další informace o poskytovatelích partnerů zabezpečení Azure Firewall Manageru
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: victorh
ms.openlocfilehash: 923c6b685d20ff68788e7d9cfcb45ebaecb535e3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490104"
---
# <a name="what-are-security-partner-providers"></a>Kdo jsou poskytovatelé partnerů pro zabezpečení?

*Poskytovatelé zabezpečení* v nástroji Azure firewall Manager vám umožňují používat vaše známé nabídky zabezpečení jako služby (SECaaS) třetích stran k ochraně internetového přístupu pro vaše uživatele.

S rychlou konfigurací můžete zabezpečit centrum s podporovaným partnerem zabezpečení a směrovat a filtrovat internetový provoz z virtuálních sítí (virtuální sítě) nebo umístění větví v rámci jedné oblasti. Můžete to provést pomocí automatizované správy směrování, aniž byste nastavili a spravovali trasy definované uživatelem (udr).

Můžete nasadit zabezpečená centra nakonfigurovaná s partnerem zabezpečení podle vašeho výběru ve více oblastech Azure, abyste získali připojení a zabezpečení pro uživatele kdekoli na celém světě v těchto oblastech. Díky možnosti použít nabídku partnera zabezpečení pro provoz aplikace Internet/SaaS a Azure Firewall privátních přenosů v zabezpečených centrech teď můžete začít sestavovat hraniční zabezpečení v Azure, které je blízko globálně distribuovaných uživatelů a aplikací.

Podporovaní partneři zabezpečení jsou **Zscaler**, **[Check Point](check-point-overview.md)** a **iboss**.

![Poskytovatelé partnerů pro zabezpečení](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Klíčové scénáře

Pomocí partnerů zabezpečení můžete filtrovat internetový provoz v následujících situacích:

- Virtual Network (VNet) na Internet

   Využijte pokročilou aplikaci Internet Protection pro vaše cloudové úlohy běžící v Azure.

- Z větve k Internetu

   Využijte možnosti připojení a globální distribuce Azure a jednoduše přidejte NSaaS filtrování třetích stran pro scénáře sítě Internet. Pomocí Azure Virtual WAN můžete vytvořit globální tranzitní síť a hraniční zabezpečení.

Podporovány jsou následující scénáře:
- Síť VNet/větev k Internetu prostřednictvím poskytovatele zabezpečení a dalšího přenosu dat (paprsky až paprsky, paprsky do větve, větvení na paprsky) prostřednictvím Azure Firewall.
- Virtuální síť/větev k Internetu prostřednictvím poskytovatele zabezpečení partnera

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Osvědčené postupy pro filtrování internetového provozu na zabezpečených virtuálních rozbočovačích

Internetový provoz obvykle zahrnuje webový provoz. Zahrnuje to ale i provoz určený k SaaS aplikacím, jako je Microsoft 365 a Azure Public PaaS Services, jako je Azure Storage, Azure SQL a tak dále. Níže jsou uvedená doporučení osvědčených postupů pro zpracování provozu do těchto služeb:

### <a name="handling-azure-paas-traffic"></a>Zpracování provozu Azure PaaS
 
- Použijte Azure Firewall pro ochranu, pokud se váš provoz skládá hlavně z Azure PaaS a přístup k prostředkům pro vaše aplikace se dá filtrovat pomocí IP adres, plně kvalifikovaných názvů domén, značek služeb nebo značek plně kvalifikovaného názvu domény.

- Pokud se váš provoz skládá z SaaS přístupu k aplikacím nebo potřebujete uživatelsky definované filtrování (například pro úlohy infrastruktury virtuálních klientských počítačů (VDI)) nebo potřebujete rozšířené možnosti pro filtrování Internetu, použijte na vašich centrech Partnerské řešení třetí strany.

![Všechny scénáře pro nástroj Azure Firewall Manager](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-microsoft-365-traffic"></a>Zpracování Microsoft 365 provozu

Ve scénářích umístění s globálně distribuovanou větví byste měli přesměrovat Microsoft 365 provoz přímo ve větvi před odesláním zbývajících internetových přenosů do zabezpečeného centra Azure.

V případě Microsoft 365 je latence sítě a výkon zásadní pro úspěšné uživatelské prostředí. Aby bylo možné dosáhnout těchto cílů z důvodu optimálního výkonu a uživatelského prostředí, musí zákazníci implementovat Microsoft 365 Direct a Local Escape před tím, než se zaměřením na směrování zbývajících internetových přenosů přes Azure.

[Zásady Microsoft 365 připojení k síti](/microsoft-365/enterprise/microsoft-365-network-connectivity-principles) volají připojení k síťovým Microsoft 365m, která se budou směrovat místně z uživatelské větve nebo mobilního zařízení a přímo přes Internet do nejbližšího síťového bodu Microsoftu.

Kromě toho se Microsoft 365 připojení šifrují pro ochranu osobních údajů a využívají efektivní a proprietární protokoly z důvodů výkonu. To je nepraktické a mělo by to mít vliv na to, aby se tato připojení mohla vztahovat na tradiční řešení zabezpečení na úrovni sítě. Z těchto důvodů důrazně doporučujeme, aby zákazníci odesílali Microsoft 365 provoz přímo z větví, před odesláním zbývajících přenosů přes Azure. Microsoft spolupracuje s několika poskytovateli řešení SD-WAN, kteří se integrují s Azure a Microsoft 365 a usnadňuje zákazníkům možnost Microsoft 365 přímé a místní užitečných Internetu. Podrobnosti najdete v tématu [co je Azure Virtual WAN?](../virtual-wan/virtual-wan-about.md)

## <a name="next-steps"></a>Další kroky

K [nasazení nabídky partnera zabezpečení v zabezpečeném centru použijte nástroj Azure firewall Manager](deploy-trusted-security-partner.md).
