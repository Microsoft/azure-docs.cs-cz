---
title: Nastavení DNS zásad Azure Firewall
description: Zásady Azure Firewall můžete nakonfigurovat pomocí serveru DNS a nastavení serveru proxy DNS.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 02/17/2021
ms.author: victorh
ms.openlocfilehash: a02879c5322add16f89f77e2da39daec7d2b5f31
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633644"
---
# <a name="azure-firewall-policy-dns-settings"></a>Nastavení DNS zásad Azure Firewall

Můžete nakonfigurovat vlastní server DNS a Povolit proxy server DNS pro zásady Azure Firewall. Tato nastavení můžete nakonfigurovat při nasazení brány firewall nebo později ze stránky **nastavení DNS** .

## <a name="dns-servers"></a>Servery DNS

Server DNS udržuje a překládá názvy domén na IP adresy. Ve výchozím nastavení používá Azure Firewall k překladu názvů Azure DNS. Nastavení **serveru DNS** umožňuje konfigurovat vlastní servery DNS pro překlad Azure firewall názvů. Můžete nakonfigurovat jeden nebo více serverů.

### <a name="configure-custom-dns-servers"></a>Konfigurace vlastních serverů DNS

1. Vyberte zásady brány firewall.
2. V části **Nastavení** vyberte **nastavení DNS**.
3. V části **servery DNS** můžete zadat nebo přidat existující servery DNS, které jste dříve zadali v Virtual Network.
4. Vyberte **Uložit**.
5. Brána firewall nyní směruje provoz DNS na zadaný server DNS pro překlad IP adres.

## <a name="dns-proxy"></a>Proxy server DNS

Azure Firewall můžete nakonfigurovat tak, aby fungoval jako proxy server DNS. DNS proxy funguje jako prostředník pro žádosti DNS z klientských virtuálních počítačů na server DNS. Pokud nakonfigurujete vlastní server DNS, měli byste povolit proxy server DNS, aby nedocházelo k neshodě překladu názvů DNS, a povolit filtrování plně kvalifikovaného názvu domény v síťových pravidlech.

Pokud nepovolíte proxy server DNS, žádosti DNS z klienta se můžou cestovat na server DNS v jinou dobu nebo vracet jinou odpověď v porovnání s bránou firewall. Proxy DNS umísťuje Azure Firewall v cestě klientských požadavků, aby nedocházelo k nekonzistenci.

Konfigurace proxy serveru DNS vyžaduje tři kroky:

1. Povolte DNS proxy v Azure Firewall nastavení DNS.
2. Volitelně můžete nakonfigurovat vlastní server DNS nebo použít poskytnutý výchozí.
3. Nakonec musíte nakonfigurovat privátní IP adresu Azure Firewall jako vlastní adresu DNS v nastavení serveru DNS virtuální sítě. Tím se zajistí přesměrování provozu DNS na Azure Firewall.

### <a name="configure-dns-proxy"></a>Konfigurace proxy serveru DNS

Pokud chcete nakonfigurovat proxy server DNS, musíte nakonfigurovat nastavení serveru DNS virtuální sítě tak, aby používalo privátní IP adresu brány firewall. Pak povolte proxy server DNS v **nastavení DNS** zásad Azure firewall.

#### <a name="configure-virtual-network-dns-servers"></a>Konfigurace serverů DNS virtuální sítě

1. Vyberte virtuální síť, ve které se budou směrovat přenosy DNS prostřednictvím Azure Firewall.
2. V části **Nastavení** vyberte **servery DNS**.
3. V části **servery DNS** vyberte **vlastní** .
4. Zadejte privátní IP adresu brány firewall.
5. Vyberte **Uložit**.

#### <a name="enable-dns-proxy"></a>Povolit proxy server DNS

1. Vyberte zásady Azure Firewall.
2. V části **Nastavení** vyberte **nastavení DNS**.
3. Ve výchozím nastavení je **proxy server DNS** zakázaný. Když je tato možnost povolená, brána firewall naslouchá na portu 53 a přepošle požadavky DNS na nakonfigurované servery DNS.
4. Zkontrolujte konfiguraci **serverů DNS** a ujistěte se, že nastavení jsou vhodná pro vaše prostředí.
5. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

[Filtrování plně kvalifikovaného názvu domény v síťových pravidlech](fqdn-filtering-network-rules.md)