---
title: Co je Azure Firewall Manager Preview?
description: Informace o funkcích Správce azure firewall
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 03/13/2020
ms.author: victorh
ms.openlocfilehash: 149782f627d586e927c828506a7d4f1b5437b987
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79366270"
---
# <a name="what-is-azure-firewall-manager-preview"></a>Co je Azure Firewall Manager Preview?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Firewall Manager Preview je služba správy zabezpečení, která poskytuje centrální zásady zabezpečení a správu tras pro obvody cloudového zabezpečení. 

Správce brány firewall může zajistit správu zabezpečení pro dva typy architektury sítě:

- **zabezpečené virtuální rozbočovač**

   Azure [Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) je prostředek spravovaný microsoftem, který umožňuje snadno vytvářet architektury rozbočovače a paprsků. Pokud jsou k takovému rozbočovači přidruženy zásady zabezpečení a směrování, označuje se jako *[zabezpečené virtuální rozbočovač](secured-virtual-hub.md)*. 
- **centrální virtuální síť**

   Jedná se o standardní virtuální síť Azure, kterou vytvoříte a spravujete sami. Pokud jsou k takovému rozbočovači přidruženy zásady zabezpečení, označuje se jako *virtuální síť rozbočovače*. V současné době je podporována pouze zásady Azure Firewall. Můžete peer mluvil virtuální sítě, které obsahují servery úlohy a služby. Můžete také spravovat brány firewall v samostatných virtuálních sítích, které nejsou peered na žádné paprsku.

Podrobné porovnání *architektur virtuálních rozbočovačů* a *virtuálních sítí v centru* najdete v [tématu Jaké jsou možnosti architektury Správce brány Firewall?](vhubs-and-vnets.md).

![správce brány firewall](media/overview/firewallmanagerv5.png)

## <a name="azure-firewall-manager-preview-features"></a>Funkce Azure Firewall Manager Preview

Azure Firewall Manager Preview nabízí následující funkce:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Nasazení a konfigurace centrální brány Azure Firewall

Můžete centrálně nasadit a nakonfigurovat více instancí Azure Firewall, které pokrývají různé oblasti A předplatná Azure. 

### <a name="hierarchical-policies-global-and-local"></a>Hierarchické zásady (globální a místní)

Azure Firewall Manager Preview můžete použít k centrální správě zásad Azure Firewall napříč několika zabezpečených virtuálních rozbočovačů. Vaše centrální IT týmy mohou vytvářet globální zásady brány firewall a vynucovat zásady brány firewall pro celou organizaci napříč týmy. Místně vytvořené zásady brány firewall umožňují samoobslužný model DevOps pro lepší agilitu.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integrovaná se službou zabezpečení jako služby jiného výrobce pro pokročilé zabezpečení

Kromě Azure Firewall můžete integrovat poskytovatele zabezpečení třetích stran jako služby (SECaaS) a poskytovat další ochranu sítě pro připojení virtuální sítě a pobočky internetu.

Tato funkce je k dispozici pouze se zabezpečenými nasazeními virtuálních rozbočovačů.

- Filtrování přenosů virtuální sítě k Internetu (V2I)

   - Odfiltrujte odchozí provoz virtuální sítě u preferovaného poskytovatele zabezpečení třetí strany.
   - Využijte pokročilou internetovou ochranu s požadavky uživatelů pro vaše cloudové úlohy spuštěné v Azure.

- Filtrování přenosů větvení na Internet (B2I)

   Využijte připojení Azure a globální distribuci ke snadnému přidání filtrování větve od jiných výrobců do internetových scénářů.

Další informace o důvěryhodných poskytovatelích zabezpečení najdete v tématu [Co jsou důvěryhodní partneři zabezpečení Azure Firewall Manager (preview)?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Centralizovaná správa tras

Snadno směrovat provoz do zabezpečeného rozbočovače pro filtrování a protokolování bez nutnosti ručně nastavit uživatelem definované trasy (UDR) na stojánkových virtuálních sítích. 

Tato funkce je k dispozici pouze se zabezpečenými nasazeními virtuálních rozbočovačů.

Můžete použít zprostředkovatele třetích stran pro filtrování provozu Větvení k Internetu (B2I), vedle azure firewall pro pobočku na virtuální síť (B2V), virtuální síť na virtuální síť (V2V) a virtuální síť na internet (V2I). Můžete také použít zprostředkovatele třetích stran pro filtrování provozu V2I tak dlouho, dokud Není vyžadována brána Azure Firewall pro B2V nebo V2V. 

## <a name="region-availability"></a>Dostupnost v oblastech

Zásady Azure Firewall lze použít napříč oblastmi. Můžete například vytvořit zásadu v USA – západ a použít ji ve východní CHV. 

## <a name="known-issues"></a>Známé problémy

Azure Firewall Manager Preview má následující známé problémy:

|Problém  |Popis  |Omezení rizik  |
|---------|---------|---------|
|Omezení filtrování třetích stran.|Filtrování provozu V2I u poskytovatelů třetích stran není podporováno pomocí Azure Firewall B2V a V2V.|Prověřování|
|Rozdělení přenosů není aktuálně podporováno.|Rozdělení provozu Office 365 a Azure Public PaaS není aktuálně podporované. Jako takové výběr poskytovatele třetí strany pro V2I nebo B2I také odešle všechny Azure Public PaaS a Office 365 provoz prostřednictvím partnerské služby.|Prošetřuji rozdělení provozu v rozbočovači.
|Jeden zabezpečené virtuální rozbočovač na oblast.|Nemůžete mít více než jeden zabezpečené virtuální rozbočovač na oblast.|Vytvořte více virtuálních wanů v oblasti.|
|Základní zásady musí být ve stejné oblasti jako místní zásady.|Vytvořte všechny místní zásady ve stejné oblasti jako základní zásady. Stále můžete použít zásadu, která byla vytvořena v jedné oblasti na zabezpečené maty z jiné oblasti.|Prověřování|
|Komunikace mezi rozbočovači nefunguje se zabezpečeným virtuálním rozbočovačem|Komunikace zabezpečeného virtuálního rozbočovače do zabezpečeného virtuálního rozbočovače ještě není podporována.|Prověřování|
|Všechna zabezpečená virtuální centra sdílející stejnou virtuální wan musí být ve stejné skupině prostředků.|Toto chování je zarovnán s virtuální WAN rozbočovače dnes.|Vytvořte více virtuálních souborů WAN, které umožní vytváření zabezpečených virtuálních rozbočovačů v různých skupinách prostředků.|
|Skupiny IP nejsou v zásadách brány firewall podporovány.|Skupiny IP jsou ve verzi Public Preview a v současné době jsou podporovány pouze tradičními pravidly brány firewall.|Probíhá oprava.
|Předplatná zprostředkovatele cloudových řešení (CSP) nejsou podporována.|V současné době nejsou [podporovány odběry CSP.](https://azure.microsoft.com/offers/ms-azr-0145p/)|Prověřování

## <a name="next-steps"></a>Další kroky

- Kontrola [přehledu nasazení nástroje Azure Firewall Manager Preview](deployment-overview.md)
- Informace o [zabezpečených virtuálních rozbočovačích](secured-virtual-hub.md).