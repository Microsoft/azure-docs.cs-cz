---
title: Azure Firewall vynucené tunelování
description: Můžete nakonfigurovat vynucené tunelování pro směrování internetového provozu do další brány firewall nebo síťového virtuálního zařízení pro další zpracování.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/19/2020
ms.author: victorh
ms.openlocfilehash: da2b206bf24cb33180305e32e270b989eb64dfa3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88612587"
---
# <a name="azure-firewall-forced-tunneling"></a>Azure Firewall vynucené tunelování

Při konfiguraci nové služby Azure Firewall můžete nastavit směrování veškerého provozu směřujícího na internet do vyhrazeného dalšího segmentu směrování místo toho, aby se přenášel přímo na internet. Můžete mít například místní hraniční bránu firewall nebo jiné síťové virtuální zařízení (síťové virtuální zařízení) pro zpracování síťového provozu před předáním na Internet. Pro vynucené tunelování ale nemůžete nakonfigurovat existující bránu firewall.

Ve výchozím nastavení se na Azure Firewall vynucené tunelování nepovoluje, aby se zajistilo splnění všech odchozích závislostí Azure. Konfigurace trasy definované uživatelem (UDR) na *AzureFirewallSubnet* , které mají výchozí trasu nepřímou na Internet, jsou zakázané.

## <a name="forced-tunneling-configuration"></a>Konfigurace vynuceného tunelování

V případě podpory vynuceného tunelování je provoz správy služeb oddělený od provozu zákazníků. Pro vlastní přidruženou veřejnou IP adresu se vyžaduje další vyhrazená podsíť s názvem *AzureFirewallManagementSubnet* (minimální velikost podsítě/26). Jediná trasa povolená v této podsíti je výchozí trasa k Internetu a šíření trasy protokolu BGP musí být zakázané.

Pokud máte výchozí trasu inzerovanou přes protokol BGP, abyste vynutili provoz do místního prostředí, musíte před nasazením brány firewall vytvořit *AzureFirewallSubnet* a *AZUREFIREWALLMANAGEMENTSUBNET* a nastavit udr s výchozí trasou na Internet a **rozšířit trasy brány** jako zakázané.

V rámci této konfigurace může *AzureFirewallSubnet* nyní zahrnovat trasy k jakékoli místní bráně firewall nebo síťové virtuální zařízení ke zpracování provozu před předáním na Internet. Tyto trasy můžete také publikovat prostřednictvím protokolu BGP do *AzureFirewallSubnet* , pokud jsou v této podsíti povoleny **trasy rozšíření brány** .

Můžete například vytvořit výchozí trasu v *AzureFirewallSubnet* s bránou VPN jako další segment směrování, který se dostane na vaše místní zařízení. Nebo můžete povolit **směrování brány** , abyste získali vhodné trasy k místní síti.

:::image type="content" source="media/forced-tunneling/route-propagation.png" alt-text="Šíření tras brány virtuální sítě":::

Pokud povolíte vynucené tunelování, před jejich vstupem se internetový provoz na jednu z privátních IP adres brány firewall v AzureFirewallSubnet a skryje se zdroj z místní brány firewall.

Pokud vaše organizace používá pro privátní sítě rozsah veřejných IP adres, Azure Firewall SNATs provoz na jednu z privátních IP adres brány firewall v AzureFirewallSubnet. Můžete ale nakonfigurovat Azure Firewall **, aby** nesnat na svůj rozsah veřejných IP adres. Další informace najdete v tématu [Azure firewall rozsahy privátních IP adres SNAT](snat-private-range.md).

Jakmile nakonfigurujete Azure Firewall pro podporu vynuceného tunelování, nemůžete konfiguraci vrátit zpátky. Pokud v bráně firewall odeberete všechny ostatní konfigurace IP adresy, odeberou se i konfigurace IP adresy pro správu a oddělí se brána firewall. Veřejnou IP adresu přiřazenou ke konfiguraci IP pro správu nejde odebrat, ale můžete přiřadit jinou veřejnou IP adresu.

## <a name="next-steps"></a>Další kroky

- [Kurz: nasazení a konfigurace Azure Firewall v hybridní síti pomocí Azure Portal](tutorial-hybrid-portal.md)
