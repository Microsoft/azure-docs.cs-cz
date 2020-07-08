---
title: Co jsou poskytovatelé partnerů zabezpečení Azure Firewall Manageru?
description: Další informace o poskytovatelích partnerů zabezpečení Azure Firewall Manageru
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 34da82510f96ef7bde65ceec397b048c941e3234
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563610"
---
# <a name="what-are-security-partner-providers"></a>Kdo jsou poskytovatelé partnerů pro zabezpečení?

*Poskytovatelé zabezpečení* v nástroji Azure firewall Manager vám umožňují používat vaše známé nabídky zabezpečení jako služby (SECaaS) třetích stran k ochraně internetového přístupu pro vaše uživatele.

S rychlou konfigurací můžete zabezpečit centrum s podporovaným partnerem zabezpečení a směrovat a filtrovat internetový provoz z virtuálních sítí (virtuální sítě) nebo umístění větví v rámci jedné oblasti. Můžete to provést pomocí automatizované správy směrování, aniž byste nastavili a spravovali trasy definované uživatelem (udr).

Můžete nasadit zabezpečená centra nakonfigurovaná s partnerem zabezpečení podle vašeho výběru ve více oblastech Azure, abyste získali připojení a zabezpečení pro uživatele kdekoli na celém světě v těchto oblastech. Díky možnosti použít nabídku partnera zabezpečení pro provoz aplikace Internet/SaaS a Azure Firewall privátních přenosů v zabezpečených centrech teď můžete začít sestavovat hraniční zabezpečení v Azure, které je blízko globálně distribuovaných uživatelů a aplikací.

Podporovaní partneři zabezpečení jsou **ZScaler**, **Check Point** (Preview) a **iboss** (Preview).

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

Internetový provoz obvykle zahrnuje webový provoz. Zahrnuje to ale i provoz, který je určený pro SaaS aplikace, jako je Office 365 (O365) a Azure Public PaaS Services, jako je například Azure Storage, Azure SQL atd. Níže jsou uvedená doporučení osvědčených postupů pro zpracování provozu do těchto služeb:

### <a name="handling-azure-paas-traffic"></a>Zpracování provozu Azure PaaS
 
- Použijte Azure Firewall pro ochranu, pokud se váš provoz skládá hlavně z Azure PaaS a přístup k prostředkům pro vaše aplikace se dá filtrovat pomocí IP adres, plně kvalifikovaných názvů domén, značek služeb nebo značek plně kvalifikovaného názvu domény.

- Pokud se váš provoz skládá z SaaS přístupu k aplikacím nebo potřebujete uživatelsky definované filtrování (například pro úlohy infrastruktury virtuálních klientských počítačů (VDI)) nebo potřebujete rozšířené možnosti pro filtrování Internetu, použijte na vašich centrech Partnerské řešení třetí strany.

![Všechny scénáře pro nástroj Azure Firewall Manager](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Zpracování provozu Office 365 (O365)

Ve scénářích umístění globálně distribuované větve byste měli před odesláním zbývajícího internetového provozu do zabezpečeného centra Azure přesměrovat provoz Office 365 přímo do větve.

V případě systému Office 365 jsou latence sítě a výkon zásadní pro úspěšné uživatelské prostředí. Aby bylo možné dosáhnout těchto cílů z důvodu optimálního výkonu a uživatelského prostředí, musí zákazníci před tím, než se zaměřením na zbytek internetového provozu přes Azure, implementovat přímo a místní řídicí sekvence Office 365.

[Principy připojení k síti office 365](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) volání síťových připojení sady Office 365 k místnímu směrování z uživatelské větve nebo mobilního zařízení a přímo přes Internet do nejbližšího síťového bodu Microsoftu.

Kromě toho jsou připojení k Office 365 zašifrovaná z hlediska ochrany osobních údajů a využívají efektivní a proprietární protokoly z důvodů výkonu. To je nepraktické a mělo by to mít vliv na to, aby se tato připojení mohla vztahovat na tradiční řešení zabezpečení na úrovni sítě. Z těchto důvodů důrazně doporučujeme, aby zákazníci odesílali provoz Office 365 přímo z větví, a to ještě před odesláním zbytku dat prostřednictvím Azure. Microsoft spolupracuje s několika poskytovateli řešení SD-WAN, kteří se integrují s Azure a Office 365, a usnadňuje zákazníkům možnost Povolit sadu Office 365 Direct a Local Internet užitečných. Podrobnosti najdete v tématu [návody nastavení zásad O365 přes virtuální síť WAN?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)

## <a name="next-steps"></a>Další kroky

K [nasazení nabídky partnera zabezpečení v zabezpečeném centru použijte nástroj Azure firewall Manager](deploy-trusted-security-partner.md).
