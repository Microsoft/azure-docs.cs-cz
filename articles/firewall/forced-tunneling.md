---
title: Vynucené tunelové propojení Azure Firewall
description: Vynucené tunelové propojení můžete nakonfigurovat tak, aby směrovalo přenosy vázané na Internet na další bránu firewall nebo síťové virtuální zařízení pro další zpracování.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/24/2020
ms.author: victorh
ms.openlocfilehash: e51f6de370a5340082f64a0ca15c61583f75962b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597268"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Vynucené tunelové propojení Azure Firewall (náhled)

Bránu Azure Firewall můžete nakonfigurovat tak, aby směrovala veškerý internetový provoz na určený další směrování, místo aby přecšel přímo k Internetu. Můžete mít například místní hraniční bránu firewall nebo jiné síťové virtuální zařízení (NVA) pro zpracování síťového provozu před jeho předáním do Internetu.

> [!IMPORTANT]
> Vynucené tunelové propojení Azure Firewall je aktuálně ve verzi Public Preview.
>
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, mohou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ve výchozím nastavení není vynucené tunelové propojení ve službě Azure Firewall povoleno, aby bylo zajištěno splnění všech odchozích závislostí Azure. Konfigurace trasy definované uživatelem (UDR) na *AzureFirewallSubnet,* které mají výchozí trasu, která nevede přímo k Internetu, jsou zakázány.

## <a name="forced-tunneling-configuration"></a>Konfigurace vynuceného tunelového propojení

Pro podporu vynuceného tunelového propojení je provoz správy služeb oddělen od provozu zákazníka. Další vyhrazená podsíť s názvem *AzureFirewallManagementSubnet* (minimální velikost podsítě /26) je vyžadována s vlastní přidruženou veřejnou IP adresou. Jediná trasa povolená v této podsíti je výchozí trasa do Internetu a šíření trasy Protokolu BGP musí být zakázáno.

Pokud máte výchozí trasu inzerovanou prostřednictvím protokolu BGP, která vynutí provoz do místního prostředí, musíte před nasazením brány firewall vytvořit síť **Virtual network gateway route propagation** *AzureFirewallSubnet* a *AzureFirewallManagementSubnet* a mít udr.

V rámci této konfigurace *azurefirewallsubnet* nyní může obsahovat trasy k libovolné místní brány firewall nebo síťové virtuální zařízení pro zpracování provozu před jeho předáním do Internetu. Tyto trasy můžete také publikovat prostřednictvím protokolu BGP do *sítě AzureFirewallSubnet,* pokud je v této podsíti povoleno **šíření trasy brány virtuální sítě.**

Můžete například vytvořit výchozí trasu na *AzureFirewallSubnet* s bránou VPN jako další směrování, abyste se dostali do místního zařízení. Nebo můžete povolit **šíření trasy brány virtuální sítě** získat příslušné trasy do místní sítě.

![Šíření trasy brány virtuální sítě](media/forced-tunneling/route-propagation.png)

Jakmile nakonfigurujete bránu Azure Firewall tak, aby podporovala vynucené tunelové propojení, nelze konfiguraci vrátit. Pokud odeberete všechny ostatní konfigurace IP v bráně firewall, bude odebrána také konfigurace IP adresy pro správu a brána firewall je přidělena. Veřejnou IP adresu přiřazenou konfiguraci IP adres pro správu nelze odebrat, ale můžete přiřadit jinou veřejnou IP adresu.

## <a name="next-steps"></a>Další kroky

- [Kurz: Nasazení a konfigurace Azure Firewall v hybridní síti pomocí portálu Azure](tutorial-hybrid-portal.md)