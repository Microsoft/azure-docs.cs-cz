---
title: Azure Firewall vynucené tunelování
description: Můžete nakonfigurovat vynucené tunelování pro směrování internetového provozu do další brány firewall nebo síťového virtuálního zařízení pro další zpracování.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/24/2020
ms.author: victorh
ms.openlocfilehash: e51f6de370a5340082f64a0ca15c61583f75962b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77597268"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Azure Firewall vynucené tunelování (Preview)

Azure Firewall můžete nakonfigurovat tak, aby směroval veškerý provoz vázaný na Internet na určený další segment směrování a nemuseli jít přímo na Internet. Můžete mít například místní hraniční bránu firewall nebo jiné síťové virtuální zařízení (síťové virtuální zařízení) pro zpracování síťového provozu před předáním na Internet.

> [!IMPORTANT]
> Azure Firewall vynucené tunelování je momentálně ve verzi Public Preview.
>
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, mohou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ve výchozím nastavení se na Azure Firewall vynucené tunelování nepovoluje, aby se zajistilo splnění všech odchozích závislostí Azure. Konfigurace trasy definované uživatelem (UDR) na *AzureFirewallSubnet* , které mají výchozí trasu nepřímou na Internet, jsou zakázané.

## <a name="forced-tunneling-configuration"></a>Konfigurace vynuceného tunelování

V případě podpory vynuceného tunelování je provoz správy služeb oddělený od provozu zákazníků. Pro vlastní přidruženou veřejnou IP adresu se vyžaduje další vyhrazená podsíť s názvem *AzureFirewallManagementSubnet* (minimální velikost podsítě/26). Jediná trasa povolená v této podsíti je výchozí trasa k Internetu a šíření trasy protokolu BGP musí být zakázané.

Pokud máte výchozí trasu inzerovanou přes protokol BGP, abyste vynutili provoz do místního prostředí, musíte před nasazením brány firewall vytvořit *AzureFirewallSubnet* a *AZUREFIREWALLMANAGEMENTSUBNET* a mít udr s výchozí trasou k Internetu a **šíření trasy brány virtuální sítě** je zakázané.

V rámci této konfigurace může *AzureFirewallSubnet* nyní zahrnovat trasy k jakékoli místní bráně firewall nebo síťové virtuální zařízení ke zpracování provozu před předáním na Internet. Tyto trasy můžete také publikovat prostřednictvím protokolu BGP do *AzureFirewallSubnet* , pokud je v této podsíti povolená **šíření tras brány virtuální sítě** .

Můžete například vytvořit výchozí trasu v *AzureFirewallSubnet* s bránou VPN jako další segment, který se dostane k místnímu zařízení. Nebo můžete povolit **šíření tras brány virtuální sítě** , abyste získali vhodné trasy k místní síti.

![Šíření tras brány virtuální sítě](media/forced-tunneling/route-propagation.png)

Jakmile nakonfigurujete Azure Firewall pro podporu vynuceného tunelování, nemůžete konfiguraci vrátit zpátky. Pokud v bráně firewall odeberete všechny ostatní konfigurace IP adresy, odeberou se i konfigurace IP adresy pro správu a oddělí se brána firewall. Veřejnou IP adresu přiřazenou ke konfiguraci IP pro správu nejde odebrat, ale můžete přiřadit jinou veřejnou IP adresu.

## <a name="next-steps"></a>Další kroky

- [Kurz: nasazení a konfigurace Azure Firewall v hybridní síti pomocí Azure Portal](tutorial-hybrid-portal.md)