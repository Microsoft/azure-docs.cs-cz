---
title: Použití Azure Firewall k ochraně virtuálního klienta Windows
description: Naučte se používat Azure Firewall k ochraně nasazení virtuálních ploch Windows
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: victorh
ms.openlocfilehash: c5d7281d50c151722303b48b2b28a597eec72d79
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254171"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>Použití Azure Firewall k ochraně nasazení virtuálních ploch Windows

Virtuální desktop Windows (WVD) je služba virtualizace plochy a aplikací, která běží v Azure. Když se koncový uživatel připojí k prostředí virtuálních počítačů s Windows, jejich relace se spustí fondem hostitelů. Fond hostitelů je kolekce virtuálních počítačů Azure, které se jako hostitelé relací registrují na virtuální plochu Windows. Tyto virtuální počítače běží ve vaší virtuální síti a podléhají ovládacím prvkům zabezpečení virtuální sítě. Potřebují odchozí internetový přístup ke službě WVD, aby fungovaly správně a mohly také vyžadovat odchozí přístup k Internetu koncovým uživatelům. Azure Firewall vám může pomáhat uzamknout prostředí a filtrovat odchozí provoz.

[![Architektura](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) virtuálních klientů Windows](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

Postupujte podle pokynů v tomto článku a poskytněte pro fond hostitelů WVD další ochranu pomocí Azure Firewall.

## <a name="prerequisites"></a>Požadavky


 - Nasazené prostředí WVD a fond hostitelů.

   Další informace najdete v tématu [kurz: Vytvoření fondu hostitelů pomocí Azure Marketplace](../virtual-desktop/create-host-pools-azure-marketplace.md) a [Vytvoření fondu hostitelů se šablonou Azure Resource Manager](../virtual-desktop/create-host-pools-arm-template.md).

Další informace o prostředích WVD najdete v tématu [prostředí virtuálních počítačů s Windows](../virtual-desktop/environment-setup.md).

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>Odchozí přístup fondu hostitelů k virtuálnímu počítači s Windows

Virtuální počítače Azure, které vytvoříte pro virtuální počítače s Windows, musí mít přístup k podrobnějšímu fungování několika plně kvalifikovaných názvů domén (FQDN). Azure Firewall poskytuje pro zjednodušení této konfigurace značku plně kvalifikovaného názvu domény virtuálního počítače s Windows. Pro povolení odchozího provozu platformy WVD použijte následující postup:

- Nasaďte Azure Firewall a nakonfigurujte trasu UDR (User Defined WVD Subnet) pro směrování veškerého provozu přes Azure Firewall. Vaše výchozí trasa teď odkazuje na bránu firewall.
- Vytvořte kolekci pravidel aplikace a přidejte pravidlo, které povolí značku *WindowsVirtualDesktop* plně kvalifikovaného názvu domény. Zdrojový rozsah IP adres je virtuální síť fondu hostitelů, protokol je **https**a cíl je **WindowsVirtualDesktop**.

- Sada požadovaných účtů úložiště a služby Service Bus pro fond hostitelů WVD je specifická pro nasazení, takže ještě není zachycena ve značce plně kvalifikovaného názvu domény WindowsVirtualDesktop. Můžete to vyřešit jedním z následujících způsobů:

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
   - Povolte službu správy klíčů – povolte provoz z virtuálních počítačů s WVD do aktivační služby Windows port TCP 1688. Další informace o cílových IP adresách najdete v tématu [Aktivace systému Windows se nezdařila ve scénáři vynucené tunelování](../virtual-machines/troubleshooting/custom-routes-enable-kms-activation.md#solution).

> [!NOTE]
> Některá nasazení nemusí potřebovat pravidla DNS, například Azure Active Directory řadiče domény předávají dotazy DNS do Azure DNS na 168.63.129.16.

## <a name="host-pool-outbound-access-to-the-internet"></a>Odchozí přístup k Internetu z fondu hostitelů

V závislosti na potřebách vaší organizace možná budete chtít povolit zabezpečený odchozí přístup k Internetu koncovým uživatelům. V případech, kdy je seznam povolených cílů dobře definovaný (například [přístup k Office 365](https://docs.microsoft.com/Office365/Enterprise/office-365-ip-web-service)), můžete ke konfiguraci požadovaného přístupu použít Azure firewallová pravidla aplikace a sítě. Tento postup směruje přenosy koncového uživatele přímo na Internet za účelem dosažení nejlepšího výkonu.

Pokud chcete filtrovat odchozího internetového provozu pomocí existující místní zabezpečené webové brány, můžete nakonfigurovat webové prohlížeče nebo jiné aplikace běžící ve fondu hostitelů WVD s explicitní konfigurací proxy serveru. Například viz [jak použít možnosti příkazového řádku Microsoft Edge ke konfiguraci nastavení proxy serveru](https://docs.microsoft.com/deployedge/edge-learnmore-cmdline-options-proxy-settings). Tato nastavení proxy mají vliv jenom na přístup k Internetu koncovým uživatelům, což umožňuje odchozí přenosy platformy WVD přímo prostřednictvím Azure Firewall.

## <a name="additional-considerations"></a>Další aspekty

V závislosti na vašich požadavcích možná budete muset nakonfigurovat další pravidla brány firewall:

- Přístup k serveru NTP

   Ve výchozím nastavení se virtuální počítače se systémem Windows Connect time.windows.com přes port UDP 123 pro synchronizaci času. Vytvořte síťové pravidlo, které povolí tento přístup, nebo pro časový server, který ve svém prostředí používáte.


## <a name="next-steps"></a>Další kroky

- Další informace o virtuálním počítači s Windows: [co je to virtuální počítač s Windows?](../virtual-desktop/overview.md)