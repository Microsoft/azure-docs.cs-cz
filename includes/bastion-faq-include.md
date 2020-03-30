---
title: zahrnout soubor
description: zahrnout soubor
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 03/25/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57a764b62fcda333f042794e176c24c8e6cc5526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80374031"
---
### <a name="which-regions-are-available"></a><a name="regions"></a>Které oblasti jsou k dispozici?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="do-i-need-a-public-ip-on-my-virtual-machine"></a><a name="publicip"></a>Potřebuji veřejnou IP adresu na svém virtuálním počítači?

Když se připojíte k virtuálnímu počítači pomocí Azure Bastion, nepotřebujete veřejnou IP adresu na virtuálním počítači Azure, ke kterému se připojujete. Služba Bastion otevře relaci/připojení RDP/SSH k vašemu virtuálnímu počítači přes privátní IP adresu vašeho virtuálního počítače v rámci vaší virtuální sítě.

### <a name="is-ipv6-supported"></a>Je iPv6 podporován?

V současné době není podporována IPv6. Azure Bastion podporuje jenom IPv4.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Potřebuji klienta RDP nebo SSH?

K přístupu k RDP/SSH do virtuálního počítače Azure na webu Azure Portal nepotřebujete klienta RDP nebo SSH. Pomocí [portálu Azure](https://portal.azure.com) můžete získat přístup RDP/SSH k virtuálnímu počítači přímo v prohlížeči.

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Vyžaduje Azure Bastion pro účely správy služby RDS cal na virtuálních počítačích hostovaných v Azure?
Ne, přístup k virtuálním počítačům Se systémem Windows Server pomocí Azure Bastion nevyžaduje službu [RDS CAL,](https://www.microsoft.com/en-us/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) pokud se používá výhradně pro účely správy.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Kolik souběžných relací RDP a SSH podporuje každá bašta Azure?
Protokol RDP i SSH jsou protokol založený na využití. Vysoké využití relací způsobí, že hostitel bastionu podporuje nižší celkový počet relací. Níže uvedená čísla předpokládají běžné každodenní pracovní postupy.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Potřebuji agenta spuštěného ve virtuálním počítači Azure?

Do prohlížeče nebo virtuálního počítače Azure nemusíte instalovat agenta ani žádný software. Služba Bastion je bez agentů a nevyžaduje žádný další software pro RDP/SSH.

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Které prohlížeče jsou podporovány?

Používejte prohlížeč Microsoft Edge nebo Google Chrome ve Windows. Pro Apple Mac použijte prohlížeč Google Chrome. Microsoft Edge Chromium je také podporováno na Windows a Mac, resp.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Jsou pro přístup k virtuálnímu počítači vyžadovány nějaké role?

Chcete-li navázat spojení, jsou vyžadovány následující role:

* Role čtenáře na virtuálním počítači
* Role čtečky na nic s privátní IP adresou virtuálního počítače
* Role čtečky v prostředku Azure Bastion

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Jaké jsou ceny?

Další informace najdete na [stránce s cenami](https://aka.ms/BastionHostPricing).

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Proč se před zahájením relace Bastion zobrazí chybová zpráva "Vaše relace vypršela"?

Relace by měla být inicializována jenom z portálu Azure. Přihlaste se k portálu Azure a začněte relaci znovu. Pokud přejdete na adresu URL přímo z jiné relace prohlížeče nebo karty, očekává se tato chyba. Pomáhá zajistit, že vaše relace je bezpečnější a že k relaci lze přistupovat pouze prostřednictvím portálu Azure.

### <a name="what-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Jaká rozložení klávesnice jsou podporována během vzdálené relace Bastion?

Azure Bastion aktuálně podporuje rozložení klávesnice en-us-qwerty uvnitř virtuálního počítače.  Podpora pro další národní prostředí pro rozložení klávesnice probíhá.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Je v podsíti Azure Bastion podporováno uživatelem definované směrování (UDR)?

Ne. UDR není podporována v podsíti Azure Bastion.
Pro scénáře, které zahrnují Azure Bastion a Azure Firewall/network virtual appliance (NVA) ve stejné virtuální síti, není nutné vynutit provoz z podsítě Azure Bastion na Azure Firewall, protože komunikace mezi Azure Bastion a virtuálními počítači je privátní. Další informace najdete [v tématu Přístup k virtuálním počítačům za Azure Firewall s bastionem](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="is-file-transfer-supported-with-azure-bastion-rdp-session"></a><a name="filetransfer"></a>Je přenos souborů podporován v relaci RDP Azure Bastion?

Usilovně pracujeme na přidání nových funkcí. Od této chvíle není přenos souborů podporován, ale je součástí našeho plánu. Prosím, neváhejte a podělte se o své názory na nové funkce na [stránce Azure Bastion Feedback](https://feedback.azure.com/forums/217313-networking?category_id=367303).

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Jak zpracovat selhání nasazení?

Zkontrolujte všechny chybové zprávy a [zvýšit žádost o podporu na webu Azure Portal](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) podle potřeby. Selhání nasazení může být důsledkem [omezení předplatného Azure, kvót a omezení](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Konkrétně zákazníci mohou setkat omezení počtu veřejných IP adres povolených na jedno předplatné, které způsobí selhání nasazení Azure Bastion.
