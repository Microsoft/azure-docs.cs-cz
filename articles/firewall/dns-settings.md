---
title: Azure Firewall nastavení DNS (Preview)
description: Azure Firewall můžete nakonfigurovat pomocí serveru DNS a nastavení serveru proxy DNS.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 9c7182205df8d276bece4758d6d4430864883d32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85610638"
---
# <a name="azure-firewall-dns-settings-preview"></a>Azure Firewall nastavení DNS (Preview)

> [!IMPORTANT]
> Nastavení DNS Azure Firewall je v současnosti ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Můžete nakonfigurovat vlastní server DNS a Povolit proxy server DNS pro Azure Firewall. Tato nastavení můžete nakonfigurovat při nasazení brány firewall nebo později ze stránky **nastavení DNS** .

## <a name="dns-servers"></a>Servery DNS

Server DNS udržuje a překládá názvy domén na IP adresy. Ve výchozím nastavení používá Azure Firewall k překladu názvů Azure DNS. Nastavení **serveru DNS** umožňuje konfigurovat vlastní servery DNS pro překlad Azure firewall názvů. Můžete nakonfigurovat jeden nebo více serverů.

### <a name="configure-custom-dns-servers-preview"></a>Konfigurace vlastních serverů DNS (verze Preview)

1. V části **nastavení**Azure firewall vyberte **nastavení DNS**.
2. V části **servery DNS**můžete zadat nebo přidat existující servery DNS, které jste dříve zadali v Virtual Network.
3. Vyberte **Uložit**.
4. Brána firewall nyní směruje provoz DNS na zadaný server DNS pro překlad IP adres.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="Servery DNS":::

## <a name="dns-proxy-preview"></a>Proxy server DNS (Preview)

Azure Firewall můžete nakonfigurovat tak, aby fungoval jako proxy server DNS. DNS proxy funguje jako prostředník pro žádosti DNS z klientských virtuálních počítačů na server DNS. Pokud nakonfigurujete vlastní server DNS, měli byste povolit proxy server DNS, aby nedocházelo k neshodě překladu názvů DNS, a povolit filtrování plně kvalifikovaného názvu domény v síťových pravidlech.

Pokud nepovolíte proxy server DNS, žádosti DNS z klienta se můžou cestovat na server DNS v jinou dobu nebo vracet jinou odpověď v porovnání s bránou firewall. Proxy DNS umísťuje Azure Firewall v cestě klientských požadavků, aby nedocházelo k nekonzistenci.

Konfigurace proxy serveru DNS vyžaduje tři kroky:
1. Povolte DNS proxy v Azure Firewall nastavení DNS.
2. Volitelně můžete nakonfigurovat vlastní server DNS nebo použít poskytnutý výchozí.
3. Nakonec musíte nakonfigurovat privátní IP adresu Azure Firewall jako vlastní adresu DNS v nastavení serveru DNS virtuální sítě. Tím se zajistí přesměrování provozu DNS na Azure Firewall.

### <a name="configure-dns-proxy-preview"></a>Konfigurace proxy serveru DNS (Preview)

Pokud chcete nakonfigurovat proxy server DNS, musíte nakonfigurovat nastavení serveru DNS virtuální sítě tak, aby používalo privátní IP adresu brány firewall. Pak povolte proxy server DNS v **nastavení DNS**zásad Azure firewall.

#### <a name="configure-virtual-network-dns-servers"></a>Konfigurace serverů DNS virtuální sítě

1. Vyberte virtuální síť, ve které se budou směrovat přenosy DNS prostřednictvím Azure Firewall.
2. V části **Nastavení**vyberte **servery DNS**.
3. V části **servery DNS**vyberte **vlastní** .
4. Zadejte privátní IP adresu brány firewall.
5. Vyberte **Uložit**.

#### <a name="enable-dns-proxy-preview"></a>Povolit proxy server DNS (Preview)

1. Vyberte Azure Firewall.
2. V části **Nastavení**vyberte **nastavení DNS**.
3. Ve výchozím nastavení je **proxy server DNS** zakázaný. Když je tato možnost povolená, brána firewall naslouchá na portu 53 a přepošle požadavky DNS na nakonfigurované servery DNS.
4. Zkontrolujte konfiguraci **serverů DNS** a ujistěte se, že nastavení jsou vhodná pro vaše prostředí.
5. Vyberte **Uložit**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="Servery DNS":::

## <a name="next-steps"></a>Další kroky

[Filtrování plně kvalifikovaného názvu domény v síťových pravidlech](fqdn-filtering-network-rules.md)