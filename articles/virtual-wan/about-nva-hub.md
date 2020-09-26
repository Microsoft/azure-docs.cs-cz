---
title: 'Azure Virtual WAN: informace o síťovém virtuálním zařízení v centru'
description: V tomto článku se dozvíte o síťových virtuálních zařízeních ve virtuálním centru WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: scottnap
Customer intent: As someone with a networking background, I want to learn about Network Virtual Appliances in the Virtual WAN hub.
ms.openlocfilehash: 1e4b8a2d801d7d7eccfaf558c3926ead1ab0a953
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91313769"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Virtuální síťové zařízení ve službě Azure Virtual WAN hub (Preview)

Azure Virtual WAN spolupracuje se síťovými partnery a vytváří automatizaci, která usnadňuje připojení místního zařízení (CPE) zákazníka ke službě Azure VPN Gateway ve virtuálním centru. Azure pracuje s vybranými síťovými partnery, aby zákazníkům umožnila nasadit síťové virtuální zařízení (síťové virtuální zařízení) třetí strany přímo do virtuálního centra. To umožňuje zákazníkům, kteří se chtějí připojit ke své pobočce, na stejném síťové virtuální ZAŘÍZENÍi ve virtuálním centru, aby mohli využívat výhod všech komplexních funkcí SD-WAN.

Barracuda Networks je první partner, který poskytuje síťové virtuální zařízení nabídku, kterou je možné nasadit přímo do virtuální sítě WAN pomocí svého [Barracuda CloudGene produktu WAN](https://www.barracuda.com/products/cloudgenwan) . Azure pracuje s větším partnerem, takže očekává, že bude sledovat další nabídky.

> [!NOTE]
> Do virtuálního centra WAN se můžou nasadit jenom síťové virtuální zařízení nabídky, které se dají nasadit do virtuálního centra WAN. Nejde je nasadit do libovolné virtuální sítě v Azure.

## <a name="how-does-it-work"></a><a name="how"></a>Jak to funguje?

Síťová virtuální zařízení, které jsou k dispozici pro nasazení přímo do služby Azure Virtual WAN, jsou navržené speciálně pro použití ve virtuálním centru. Nabídka síťové virtuální zařízení je publikována v Azure Marketplace jako spravovaná aplikace a zákazníci mohou tuto nabídku nasadit přímo z Azure Marketplace nebo mohou nasadit tuto nabídku z virtuálního rozbočovače prostřednictvím Azure Portal.

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="Přehled procesu":::

Nabídka síťové virtuální zařízení každého partnera bude mít mírně různé možnosti a funkčnost na základě požadavků na jejich nasazení. Existují však některé věci, které jsou společné v rámci všech partnerských nabídek pro síťové virtuální zařízení ve virtuálním centru WAN.

* Prostředí spravované aplikace nabízené prostřednictvím Azure Marketplace.
* Kapacita a fakturace na základě jednotky infrastruktury síťové virtuální zařízení.
* Metriky stavu jsou Surface Azure Monitor.

### <a name="managed-application"></a><a name="managed"></a>Spravovaná aplikace

Všechny nabídky síťové virtuální zařízení, které jsou k dispozici pro nasazení do virtuálního centra sítě WAN, budou mít **spravovanou aplikaci** , která je k dispozici v Azure Marketplace. Spravované aplikace umožňují partnerům provádět tyto akce:

* Sestavte si vlastní prostředí nasazení pro své síťové virtuální zařízení.
* Poskytněte specializovanou šablonu Správce prostředků, která jim umožní vytvořit síťové virtuální zařízení přímo ve virtuálním centru WAN.
* Účtujte náklady na licencování softwaru přímo nebo prostřednictvím Azure Marketplace.
* Zveřejněte vlastní vlastnosti a měřiče prostředků.

Partneři síťové virtuální zařízení můžou vytvářet různé prostředky v závislosti na nasazení zařízení, licencování konfigurace a potřebě správy. Když zákazník vytvoří síťové virtuální zařízení ve virtuálním centru WAN, stejně jako všechny spravované aplikace, ve svém předplatném se vytvoří dvě skupiny prostředků.

* **Skupina prostředků zákazníka** – Tato akce bude obsahovat zástupný symbol aplikace pro spravovanou aplikaci. Partneři můžou využít tuto možnost k vystavení libovolných vlastností zákazníka, které si vyberou.
* **Spravovaná skupina prostředků** – zákazníci nemůžou konfigurovat ani měnit prostředky v této skupině prostředků přímo, protože to řídí Vydavatel spravované aplikace. Tato skupina prostředků bude obsahovat prostředek **NetworkVirtualAppliances** .

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="Skupiny prostředků spravovaných aplikací":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>Jednotky infrastruktury síťové virtuální zařízení

Když ve virtuálním centru sítě WAN vytvoříte síťové virtuální zařízení, musíte zvolit počet jednotek infrastruktury síťové virtuální zařízení, pomocí kterých ho chcete nasadit. **Jednotka infrastruktury síťové virtuální zařízení** je jednotka agregované kapacity šířky pásma pro síťové virtuální zařízení ve virtuálním centru WAN. **Jednotka infrastruktury síťové virtuální zařízení** se podobá [jednotce škálování](pricing-concepts.md#scale-unit) VPN podle toho, jak si myslíte o kapacitě a velikosti.

* 1 jednotka infrastruktury síťové virtuální zařízení představuje 500 MB/s agregované šířky pásma pro všechna připojení lokalit braná do této síťové virtuální zařízení, a to za cenu po výši $0,25 za hodinu.
* Azure podporuje jednotky infrastruktury síťové virtuální zařízení 1-80 pro dané nasazení virtuálního rozbočovače síťové virtuální zařízení.
* Každý partner může nabízet různé sady jednotek infrastruktury síťové virtuální zařízení, které jsou podmnožinou všech podporovaných konfigurací jednotek infrastruktury síťové virtuální zařízení.

Podobně jako jednotky škálování sítě VPN: Pokud vyberete *1 síťové virtuální zařízení infrastrukturu = 500 MB/s*, znamená to, že se vytvoří dvě instance redundance, z nichž každá má maximální propustnost 500 MB/s. Pokud byste například měli pět větví, z nichž každý bude mít 10 MB/s na větvi, budete potřebovat agregaci 50 MB/s na konci hlavního umístění. Plánování agregované kapacity síťové virtuální zařízení by mělo probíhat po vyhodnocení kapacity potřebné k podpoře počtu větví do centra.

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>Proces konfigurace síťového virtuálního zařízení

Partneři pracovali, aby poskytovali prostředí, které automaticky konfiguruje síťové virtuální zařízení jako součást procesu nasazení. Jakmile se síťové virtuální zařízení zřídí do virtuálního rozbočovače, všechny další konfigurace, které může být potřeba pro síťové virtuální zařízení, se musí provádět prostřednictvím portálu síťové virtuální zařízení partner nebo aplikace pro správu. Přímý přístup k síťové virtuální zařízení není k dispozici.

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>Prostředky lokalit a připojení pomocí síťová virtuální zařízení

Na rozdíl od konfigurací Azure VPN Gateway nemusíte vytvářet prostředky **lokality** , prostředky **připojení typu Site-to-site** nebo prostředky **připojení typu Point-to-site** , aby se vaše pobočky připojovaly k vašemu síťové virtuální zařízení ve virtuálním centru WAN. To je všechno spravované prostřednictvím partnera síťové virtuální zařízení.

Pro připojení vašeho virtuálního rozbočovače WAN k virtuálním sítím Azure stále potřebujete vytvořit připojení typu hub-to-VNet.

## <a name="supported-regions"></a><a name="regions"></a>Podporované oblasti

SÍŤOVÉ virtuální zařízení ve virtuálním rozbočovači je k dispozici pro verzi Preview v následujících oblastech:

|Geopolitická oblast | Oblast Azure|
|---|---|
| Severní Amerika| Západní USA, Střed USA – jih Východní USA 2   |
| Jižní Amerika | Brazil South |
| Evropa | Západní Evropa Velká Británie – jih|
|  Střední východ | Spojené arabské emiráty sever |
| Asie | Japan East |
| Austrálie | Austrálie – východ |

## <a name="faq"></a>Časté otázky

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>Jsem partnerem síťového zařízení a chcete získat naše síťové virtuální ZAŘÍZENÍy v centru.  Můžu se připojit k tomuto partnerskému programu?

V tuto chvíli bohužel nemusíme mít na zadarmo žádné nové nabídky partnerů. V listopadu se vraťte s námi.

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>Můžu nasazovat jakékoli síťové virtuální zařízení z Azure Marketplace do virtuálního centra sítě WAN?

No. V tuto chvíli je k nasazení do virtuálního centra WAN dostupná jenom [Barracuda CLOUDGEN WAN](https://aka.ms/BarracudaMarketPlaceOffer) .

### <a name="what-is-the-cost-of-the-nva"></a>Jaké jsou náklady na síťové virtuální zařízení?

Musíte si koupit licenci pro Barracuda CloudGen WAN síťové virtuální zařízení od Barracuda. Další informace o licencování najdete na [stránce CLOUDGEN WAN pro Barracuda](https://www.barracuda.com/products/cloudgenwan). Navíc se vám od Microsoftu účtují poplatky za jednotky infrastruktury síťové virtuální zařízení, které spotřebujete, a všechny další prostředky, které využijete. Další informace najdete v tématu [cenové koncepty](pricing-concepts.md).

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>Můžu nasazovat síťové virtuální zařízení do základního rozbočovače?

No. Pokud chcete nasadit síťové virtuální zařízení, musíte použít standardní centrum.

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>Můžu síťové virtuální zařízení nasadit do zabezpečeného centra?

Ano. Barracuda CloudGen WAN se dá nasadit do centra s Azure Firewall.

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>Můžu na svém firemním pobočce připojit libovolné zařízení CPE, aby se Barracuda CloudGen WAN síťové virtuální zařízení v centru?

No. Barracuda CloudGen WAN je kompatibilní jenom se zařízeními Barracuda CPE. Další informace o požadavcích na CloudGen WAN najdete na [stránce CLOUDGEN WAN pro Barracuda](https://www.barracuda.com/products/cloudgenwan).

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>Jaké scénáře směrování podporuje síťové virtuální zařízení v centru?

Všechny scénáře směrování podporované virtuální sítí WAN jsou podporované v síťová virtuální zařízení v centru.

## <a name="next-steps"></a>Další kroky

Další informace o virtuální síti WAN najdete v článku [Přehled virtuálních sítí WAN](virtual-wan-about.md) .