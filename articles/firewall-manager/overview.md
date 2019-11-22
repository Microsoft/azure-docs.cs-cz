---
title: Co je verze Preview Azure Firewall Manageru?
description: Další informace o funkcích Azure Firewall Manageru
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 11/21/2019
ms.author: victorh
ms.openlocfilehash: 897819928ab0bcf48b58428014c03aea6b2145fd
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74267956"
---
# <a name="what-is-azure-firewall-manager-preview"></a>Co je verze Preview Azure Firewall Manageru?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Verze Preview služby Azure Firewall Manager je služba pro správu zabezpečení, která poskytuje centrální zásady zabezpečení a správu směrování pro hraniční zabezpečení na základě cloudu. Funguje s [Azure Virtual WAN hub](../virtual-wan/virtual-wan-about.md#resources), což je prostředek spravovaný Microsoftem, který umožňuje snadno vytvářet architektury hub a paprsků. Pokud jsou zásady zabezpečení a směrování přidruženy k takovému centru, označuje se jako *[zabezpečené virtuální rozbočovač](secured-virtual-hub.md)* . 

![firewall – správce](media/overview/firewallmanagerv3.png)

## <a name="azure-firewall-manager-preview-features"></a>Funkce Preview nástroje Azure Firewall Manager

Verze Preview nástroje Azure Firewall Manager nabízí tyto funkce:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Nasazení a konfigurace centrálního Azure Firewall

Můžete centrálně nasazovat a konfigurovat víc instancí Azure Firewall, které zahrnují různé oblasti a odběry Azure. 

### <a name="hierarchical-policies-global-and-local"></a>Hierarchické zásady (globální a místní)

Pomocí nástroje Azure Firewall Manager Preview můžete centrálně spravovat zásady Azure Firewall napříč několika zabezpečenými virtuálními rozbočovači. Vaše centrální týmy IT můžou vytvářet globální zásady brány firewall, aby vynutily zásady brány firewall pro celou organizaci napříč týmy. Místně vytvořené zásady brány firewall umožňují pro lepší flexibilitu DevOps model samoobslužných služeb.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integrace se zabezpečením od jiných výrobců jako služby pro pokročilé zabezpečení

Kromě Azure Firewall můžete integrovat poskytovatele zabezpečení jako služby (SECaaS) třetích stran a poskytnout tak další ochranu sítě pro připojení k virtuální síti a pobočkám.

- Filtrování provozu virtuální sítě k Internetu (V2I)

   - Filtrujte odchozí přenosy virtuální sítě s vaším preferovaným poskytovatelem zabezpečení třetí strany.
   - Využijte pokročilou aplikaci Internet Protection pro vaše cloudové úlohy běžící v Azure.

- Filtrování provozu z větve na Internet (B2I)

   Využijte možnosti připojení a globální distribuce Azure a jednoduše přidejte filtrování třetích stran pro scénáře do Internetu.

Další informace o důvěryhodných poskytovatelích zabezpečení najdete v tématu [co jsou důvěryhodné bezpečnostní partneři Azure firewall Manageru (Preview)?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Centralizovaná správa tras

Snadné směrování provozu do zabezpečeného centra pro filtrování a protokolování bez nutnosti ručního nastavení tras definovaných uživatelem (UDR) ve virtuálních sítích paprsků. Můžete použít poskytovatele třetích stran pro filtrování přenosů větví na Internet (B2I) vedle sebe s Azure Firewall pro větvení na virtuální síť (B2V), virtuální síť a virtuální síť (V2V) a virtuální síť k Internetu (V2I). Pro filtrování provozu V2I můžete také použít poskytovatele třetích stran, pokud pro B2V nebo V2V není vyžadováno Azure Firewall. 

## <a name="region-availability"></a>Dostupnost v oblastech

Verze Public Preview podporuje následující oblasti:

- Západní Evropa, Severní Evropa, Francie – střed, Francie – jih, Velká Británie – jih Velká Británie – západ
- Austrálie – východ, Austrálie – střed, Austrálie – střed 2, Austrálie – jihovýchod
- Kanada – střed
- Východní USA, Západní USA, Východní USA 2, Střed USA – jih, Západní USA 2, Střed USA, Střed USA – sever, Středozápadní USA

Zásady Azure Firewall lze vytvořit pouze v těchto oblastech, ale lze je použít v různých oblastech. Můžete například vytvořit zásadu v Západní USA a použít ji v Východní USA. 

## <a name="known-issues"></a>Známé problémy

Verze Preview Azure Firewall Manageru má následující známé problémy:

|Problém  |Popis  |Omezení rizik  |
|---------|---------|---------|
|Ruční vytvoření centrálního virtuální sítě se nepodporuje.|V současné době Azure Firewall Manager podporuje sítě vytvořené pomocí virtuálních Center. Používání vlastní virtuální sítě rozbočovače se ještě nepodporují.|Prozatím použijte Azure Firewall Manager se sítěmi hub a paprsků vytvořených s virtuálními rozbočovači.<br>Probíhá šetření.
|Omezení filtrování třetích stran|Filtrování přenosů V2I s poskytovateli třetích stran není podporované u Azure Firewallch B2V a V2V.|Probíhá šetření.|
|Rozdělení provozu se momentálně nepodporuje.|Rozdělování provozu Office 365 a Azure Public PaaS se v tuto chvíli nepodporuje. V takovém případě je třeba vybrat poskytovatele třetí strany pro V2I nebo B2I také odeslat veškerý provoz Azure Public PaaS a Office 365 prostřednictvím partnerské služby.|Probíhá šetření rozdělení provozu v centru.
|Jedno centrum na oblast|Nemůžete mít více než jedno centrum na oblast.|Vytvoření více virtuálních sítí WAN v určité oblasti.|
|Základní zásady musí být ve stejné oblasti jako místní zásada.|Vytvořte všechny místní zásady ve stejné oblasti jako základní zásady. Můžete přesto použít zásadu, která byla vytvořena v jedné oblasti v zabezpečeném centru z jiné oblasti.|Probíhá šetření.|
|Komunikace mezi rozbočovači nefunguje s zabezpečeným virtuálním rozbočovačem|Zabezpečené virtuální rozbočovače na zabezpečenou komunikaci virtuálního rozbočovače ještě není podporovaná.|Probíhá šetření.|

## <a name="next-steps"></a>Další kroky

- [Přehled nasazení Preview Azure firewall Manageru](deployment-overview.md)
- Přečtěte si o [zabezpečených virtuálních rozbočovačích](secured-virtual-hub.md).