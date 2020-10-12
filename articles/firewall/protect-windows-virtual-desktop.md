---
title: Použití Azure Firewall k ochraně virtuálního klienta Windows
description: Naučte se používat Azure Firewall k ochraně nasazení virtuálních ploch Windows
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: ae33d763bda49756e9f90a05feda5089b63ef28b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400152"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>Ochrana nasazení virtuálních ploch s Windows pomocí Azure Firewallu

Virtuální plocha Windows je služba virtualizace plochy a aplikací, která běží v Azure. Když se koncový uživatel připojí k prostředí virtuálních počítačů s Windows, jejich relace se spustí fondem hostitelů. Fond hostitelů je kolekce virtuálních počítačů Azure, které se jako hostitelé relací registrují na virtuální plochu Windows. Tyto virtuální počítače běží ve vaší virtuální síti a podléhají ovládacím prvkům zabezpečení virtuální sítě. Potřebují odchozí internetový přístup ke službě Virtual Desktop systému Windows, aby fungovaly správně a mohly by pro koncové uživatele vyžadovat odchozí přístup k Internetu. Azure Firewall vám může pomáhat uzamknout prostředí a filtrovat odchozí provoz.

[![Architektura ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) virtuálních klientů Windows](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

Postupujte podle pokynů v tomto článku a poskytněte další ochranu fondu hostitelů virtuálních počítačů s Windows pomocí Azure Firewall.

## <a name="prerequisites"></a>Požadavky


 - Nasazené prostředí virtuálních počítačů s Windows a fond hostitelů.

   Další informace najdete v tématu [kurz: Vytvoření fondu hostitelů pomocí Azure Marketplace](../virtual-desktop/create-host-pools-azure-marketplace.md) a [Vytvoření fondu hostitelů se šablonou Azure Resource Manager](../virtual-desktop/virtual-desktop-fall-2019/create-host-pools-arm-template.md).

Další informace o prostředích virtuálních počítačů s Windows najdete v tématu [prostředí virtuálních počítačů s Windows](../virtual-desktop/environment-setup.md).

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>Odchozí přístup fondu hostitelů k virtuálnímu počítači s Windows

Virtuální počítače Azure, které vytvoříte pro virtuální počítače s Windows, musí mít přístup k podrobnějšímu fungování několika plně kvalifikovaných názvů domén (FQDN). Azure Firewall poskytuje pro zjednodušení této konfigurace značku plně kvalifikovaného názvu domény virtuálního počítače s Windows. Pro povolení odchozích přenosů platformy virtuálních počítačů s Windows použijte následující postup:

- Nasaďte Azure Firewall a nakonfigurujte trasu (UDR) podsítě fondu virtuálních počítačů s Windows pro směrování veškerého provozu přes Azure Firewall. Vaše výchozí trasa teď odkazuje na bránu firewall.
- Vytvořte kolekci pravidel aplikace a přidejte pravidlo, které povolí značku *WindowsVirtualDesktop* plně kvalifikovaného názvu domény. Zdrojový rozsah IP adres je virtuální síť fondu hostitelů, protokol je **https**a cíl je **WindowsVirtualDesktop**.

- Sada požadovaných účtů úložiště a služby Service Bus pro fond hostitelů virtuálních počítačů s Windows je specifická pro nasazení, takže ještě není zachycena ve značce WindowsVirtualDesktop plně kvalifikovaného názvu domény. Můžete to vyřešit jedním z následujících způsobů:

   - Povolte přístup HTTPS z podsítě fondu hostitelů na * xt.blob.core.windows.net, * eh.servicebus.windows.net a * xt.table.core.windows.net. Tyto plně kvalifikované názvy domén zástupných znaků umožňují požadovaný přístup, ale jsou méně omezující.
   - Pomocí následujícího dotazu Log Analytics můžete zobrazit seznam přesně požadovaných plně kvalifikovaných názvů domén a pak je v pravidlech aplikací brány firewall výslovně zapnout:
   ```
   AzureDiagnostics
   | where Category == "AzureFirewallApplicationRule"
   | search "Deny"
   | search "gsm*eh.servicebus.windows.net" or "gsm*xt.blob.core.windows.net" or "gsm*xt.table.core.windows.net"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" *
   | project TimeGenerated,Protocol,FQDN
   ```

- Vytvořte kolekci pravidel sítě a přidejte následující pravidla:

   - Povolí DNS – povolí provoz z vaší adresy pro přidání privátní IP adresy * pro porty TCP a UDP 53.
   - Povolení služby správy klíčů – povolí přenos z virtuálních počítačů s virtuálními počítači s Windows do aktivační služby Windows port TCP 1688. Další informace o cílových IP adresách najdete v tématu [Aktivace systému Windows se nezdařila ve scénáři vynucené tunelování](../virtual-machines/troubleshooting/custom-routes-enable-kms-activation.md#solution).

> [!NOTE]
> Některá nasazení nemusí potřebovat pravidla DNS, například Azure Active Directory řadiče domény předávají dotazy DNS do Azure DNS na 168.63.129.16.

## <a name="host-pool-outbound-access-to-the-internet"></a>Odchozí přístup k Internetu z fondu hostitelů

V závislosti na potřebách vaší organizace možná budete chtít povolit zabezpečený odchozí přístup k Internetu koncovým uživatelům. V případech, kdy je seznam povolených cílů dobře definovaný (například [Microsoft 365 přístup](/microsoft-365/enterprise/microsoft-365-ip-web-service)), můžete pomocí Azure firewall aplikace a pravidel sítě nakonfigurovat požadovaný přístup. Tento postup směruje přenosy koncového uživatele přímo na Internet za účelem dosažení nejlepšího výkonu.

Pokud chcete filtrovat odchozího internetového provozu pomocí existující místní zabezpečené webové brány, můžete nakonfigurovat webové prohlížeče nebo jiné aplikace běžící na fondu hostitelů virtuálních ploch Windows s explicitní konfigurací proxy serveru. Například viz [jak použít možnosti příkazového řádku Microsoft Edge ke konfiguraci nastavení proxy serveru](https://docs.microsoft.com/deployedge/edge-learnmore-cmdline-options-proxy-settings). Tato nastavení proxy mají vliv na přístup k Internetu koncovým uživatelům, což umožňuje, aby odchozí přenosy platformy virtuálních počítačů s Windows přímo prostřednictvím Azure Firewall.

## <a name="additional-considerations"></a>Další aspekty

V závislosti na vašich požadavcích možná budete muset nakonfigurovat další pravidla brány firewall:

- Přístup k serveru NTP

   Ve výchozím nastavení se virtuální počítače se systémem Windows Connect time.windows.com přes port UDP 123 pro synchronizaci času. Vytvořte síťové pravidlo, které povolí tento přístup, nebo pro časový server, který ve svém prostředí používáte.


## <a name="next-steps"></a>Další kroky

- Další informace o virtuálním počítači s Windows: [co je to virtuální počítač s Windows?](../virtual-desktop/overview.md)