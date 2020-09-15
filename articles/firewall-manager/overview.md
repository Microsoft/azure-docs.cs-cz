---
title: Co je Azure Firewall Manager?
description: Další informace o funkcích Azure Firewall Manageru
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 09/15/2020
ms.author: victorh
ms.openlocfilehash: 6dc22086ed97a301e225518465482e9cd7300e11
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90563824"
---
# <a name="what-is-azure-firewall-manager"></a>Co je Azure Firewall Manager?

Azure Firewall Manager je služba pro správu zabezpečení, která poskytuje centrální zásady zabezpečení a správu směrování pro hraniční zabezpečení na základě cloudu. 

Správce brány firewall může poskytovat správu zabezpečení pro dva typy síťové architektury:

- **Zabezpečené virtuální centrum**

   [Azure Virtual WAN hub](../virtual-wan/virtual-wan-about.md#resources) je prostředek spravovaný Microsoftem, který umožňuje snadno vytvářet architektury hub a paprsků. Pokud jsou zásady zabezpečení a směrování přidruženy k takovému centru, označuje se jako *[zabezpečené virtuální rozbočovač](secured-virtual-hub.md)*. 
- **Virtuální síť centra**

   Toto je standardní virtuální síť Azure, kterou vytváříte a spravujete sami. Pokud jsou zásady zabezpečení přidružené k takovému centru, označuje se jako *virtuální síť rozbočovače*. V tuto chvíli se podporují jenom zásady Azure Firewall. Můžete vytvořit partnerský paprsek virtuálních sítí, které obsahují vaše servery a služby pro úlohy. Brány firewall můžete spravovat i v samostatných virtuálních sítích, které nesousedí s žádnými partnerskými servery.

Podrobné porovnání *zabezpečeného virtuálního rozbočovače* a architektury *virtuální sítě rozbočovače* najdete v tématu [co jsou možnosti architektury Azure firewall Manageru?](vhubs-and-vnets.md).

![firewall – správce](media/overview/trusted-security-partners.png)

## <a name="azure-firewall-manager-features"></a>Funkce Azure Firewall Manageru

Azure Firewall Manager nabízí následující funkce:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Nasazení a konfigurace centrálního Azure Firewall

Můžete centrálně nasazovat a konfigurovat víc instancí Azure Firewall, které zahrnují různé oblasti a odběry Azure. 

### <a name="hierarchical-policies-global-and-local"></a>Hierarchické zásady (globální a místní)

Pomocí nástroje Azure Firewall Manager můžete centrálně spravovat zásady Azure Firewall napříč několika zabezpečenými virtuálními rozbočovači. Vaše centrální týmy IT můžou vytvářet globální zásady brány firewall, aby vynutily zásady brány firewall pro celou organizaci napříč týmy. Místně vytvořené zásady brány firewall umožňují pro lepší flexibilitu DevOps model samoobslužných služeb.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integrace se zabezpečením od jiných výrobců jako služby pro pokročilé zabezpečení

Kromě Azure Firewall můžete integrovat poskytovatele zabezpečení jako služby (SECaaS) třetích stran a poskytnout tak další ochranu sítě pro připojení k virtuální síti a pobočkám.

Tato funkce je dostupná jenom u zabezpečených nasazení virtuálních rozbočovačů.

- Filtrování provozu virtuální sítě k Internetu (V2I)

   - Filtrujte odchozí přenosy virtuální sítě s vaším preferovaným poskytovatelem zabezpečení třetí strany.
   - Využijte pokročilou aplikaci Internet Protection pro vaše cloudové úlohy běžící v Azure.

- Filtrování provozu z větve na Internet (B2I)

   Využijte možnosti připojení a globální distribuce Azure a jednoduše přidejte filtrování třetích stran pro scénáře do Internetu.

Další informace o zprostředkovatelích zabezpečení najdete v tématu [co jsou poskytovatelé partnerů zabezpečení Azure firewall Manageru?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Centralizovaná správa tras

Snadné směrování provozu do zabezpečeného centra pro filtrování a protokolování bez nutnosti ručního nastavení tras definovaných uživatelem (UDR) ve virtuálních sítích paprsků. 

Tato funkce je dostupná jenom u zabezpečených nasazení virtuálních rozbočovačů.

Můžete použít poskytovatele třetích stran pro filtrování přenosů větví na Internet (B2I) vedle sebe s Azure Firewall pro větvení na virtuální síť (B2V), virtuální síť a virtuální síť (V2V) a virtuální síť k Internetu (V2I). Pro filtrování provozu V2I můžete také použít poskytovatele třetích stran, pokud pro B2V nebo V2V není vyžadováno Azure Firewall. 

## <a name="region-availability"></a>Dostupnost v oblastech

Zásady Azure Firewall lze používat napříč oblastmi. Můžete například vytvořit zásadu v Západní USA a použít ji v Východní USA. 

## <a name="known-issues"></a>Známé problémy

Azure Firewall Manager má následující známé problémy:

|Problém  |Description  |Omezení rizik  |
|---------|---------|---------|
|Rozdělení provozu|Rozdělování provozu Office 365 a Azure Public PaaS se v tuto chvíli nepodporuje. V takovém případě je třeba vybrat poskytovatele třetí strany pro V2I nebo B2I také odeslat veškerý provoz Azure Public PaaS a Office 365 prostřednictvím partnerské služby.|Zkoumání rozdělení provozu v centru.
|Jeden zabezpečený virtuální rozbočovač na oblast|Pro každou oblast nemůžete mít více než jedno zabezpečené virtuální rozbočovače.|Vytvoření více virtuálních sítí WAN v určité oblasti.|
|Základní zásady musí být ve stejné oblasti jako místní zásada.|Vytvořte všechny místní zásady ve stejné oblasti jako základní zásady. Můžete přesto použít zásadu, která byla vytvořena v jedné oblasti v zabezpečeném centru z jiné oblasti.|Prověřování|
|Filtrování provozu mezi rozbočovači v nasazeních zabezpečených virtuálních rozbočovačů|Zabezpečené virtuální rozbočovače na zabezpečené filtrování komunikace virtuálního rozbočovače se ještě nepodporuje. Nicméně komunikace centra s centrem funguje i v případě, že filtrování privátních přenosů prostřednictvím Azure Firewall není povolené.|Prověřování|
|Paprsky v jiné oblasti než virtuální rozbočovač|Paprsky v jiné oblasti, než je virtuální rozbočovač, nejsou podporovány.|Prověřování<br><br>Vytvořte rozbočovač na oblast a partnerský virtuální sítě ve stejné oblasti jako centrum.|
|Provoz větví do větvení s povoleným filtrováním privátních přenosů|Komunikace větví s větví není podporovaná, pokud je povolené filtrování privátních přenosů. |Zkoumání.<br><br>Nezabezpečte privátní provoz, pokud je připojení větví k pobočce kritické.|
|Všechna zabezpečená virtuální centra, která sdílejí stejnou virtuální síť WAN, musí být ve stejné skupině prostředků.|Toto chování je v současné době zarovnané na virtuální rozbočovače WAN.|Vytvořte více virtuálních sítí WAN, aby bylo možné vytvořit zabezpečené virtuální rozbočovače v různých skupinách prostředků.|
|Přidání hromadné IP adresy se nezdařilo.|Brána firewall zabezpečeného centra přejde do stavu selhání, pokud přidáte více veřejných IP adres.|Přidejte menší zvýšení veřejných IP adres. Přidejte například 10 po dobu.|
|Pravidla aplikací selžou v zabezpečeném centru s nakonfigurovaným vlastním DNS (Preview).|Vlastní DNS (Preview) nefunguje v nasazeních zabezpečeného centra a v nasazeních virtuálních sítí rozbočovače, kde je povolené vynucené tunelování.|Opravte šetření.|
|DDoS Protection Standard není podporován u zabezpečených virtuálních rozbočovačů.|DDoS Protection Standard není integrovaný do vWANs.|Prověřování

## <a name="next-steps"></a>Další kroky

- [Přehled nasazení Azure firewall Manageru](deployment-overview.md)
- Přečtěte si o [zabezpečených virtuálních rozbočovačích](secured-virtual-hub.md).
