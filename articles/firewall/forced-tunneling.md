---
title: Azure Firewall vynucené tunelování
description: Můžete nakonfigurovat vynucené tunelování pro směrování internetového provozu do další brány firewall nebo síťového virtuálního zařízení pro další zpracování.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/01/2020
ms.author: victorh
ms.openlocfilehash: a467aa60b131e47e9251366369b3fae8dd95c004
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84267694"
---
# <a name="azure-firewall-forced-tunneling"></a>Azure Firewall vynucené tunelování

Když nakonfigurujete novou Azure Firewall, můžete směrovat veškerý provoz vázaný na Internet na určený další segment směrování, a nemusíte jít přímo na Internet. Můžete mít například místní hraniční bránu firewall nebo jiné síťové virtuální zařízení (síťové virtuální zařízení) pro zpracování síťového provozu před předáním na Internet. Pro vynucené tunelování ale nemůžete nakonfigurovat existující bránu firewall.

Ve výchozím nastavení se na Azure Firewall vynucené tunelování nepovoluje, aby se zajistilo splnění všech odchozích závislostí Azure. Konfigurace trasy definované uživatelem (UDR) na *AzureFirewallSubnet* , které mají výchozí trasu nepřímou na Internet, jsou zakázané.

## <a name="forced-tunneling-configuration"></a>Konfigurace vynuceného tunelování

V případě podpory vynuceného tunelování je provoz správy služeb oddělený od provozu zákazníků. Pro vlastní přidruženou veřejnou IP adresu se vyžaduje další vyhrazená podsíť s názvem *AzureFirewallManagementSubnet* (minimální velikost podsítě/26). Jediná trasa povolená v této podsíti je výchozí trasa k Internetu a šíření trasy protokolu BGP musí být zakázané.

Pokud máte výchozí trasu inzerovanou přes protokol BGP, abyste vynutili provoz do místního prostředí, musíte před nasazením brány firewall vytvořit *AzureFirewallSubnet* a *AZUREFIREWALLMANAGEMENTSUBNET* a mít udr s výchozí trasou k Internetu a **šíření trasy brány virtuální sítě** je zakázané.

V rámci této konfigurace může *AzureFirewallSubnet* nyní zahrnovat trasy k jakékoli místní bráně firewall nebo síťové virtuální zařízení ke zpracování provozu před předáním na Internet. Tyto trasy můžete také publikovat prostřednictvím protokolu BGP do *AzureFirewallSubnet* , pokud je v této podsíti povolená **šíření tras brány virtuální sítě** .

Můžete například vytvořit výchozí trasu v *AzureFirewallSubnet* s bránou VPN jako další segment směrování, který se dostane na vaše místní zařízení. Nebo můžete povolit **šíření tras brány virtuální sítě** , abyste získali vhodné trasy k místní síti.

![Šíření tras brány virtuální sítě](media/forced-tunneling/route-propagation.png)

Pokud povolíte vynucené tunelování, před jejich vstupem se internetový provoz na jednu z privátních IP adres brány firewall v AzureFirewallSubnet a skryje se zdroj z místní brány firewall.

Pokud vaše organizace používá pro privátní sítě rozsah veřejných IP adres, Azure Firewall SNATs provoz na jednu z privátních IP adres brány firewall v AzureFirewallSubnet. Můžete ale nakonfigurovat Azure Firewall **, aby** nesnat na svůj rozsah veřejných IP adres. Další informace najdete v tématu [Azure firewall rozsahy privátních IP adres SNAT](snat-private-range.md).

Jakmile nakonfigurujete Azure Firewall pro podporu vynuceného tunelování, nemůžete konfiguraci vrátit zpátky. Pokud v bráně firewall odeberete všechny ostatní konfigurace IP adresy, odeberou se i konfigurace IP adresy pro správu a oddělí se brána firewall. Veřejnou IP adresu přiřazenou ke konfiguraci IP pro správu nejde odebrat, ale můžete přiřadit jinou veřejnou IP adresu.

## <a name="next-steps"></a>Další kroky

- [Kurz: nasazení a konfigurace Azure Firewall v hybridní síti pomocí Azure Portal](tutorial-hybrid-portal.md)
