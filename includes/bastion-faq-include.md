---
title: zahrnout soubor
description: zahrnout soubor
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/26/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 95c55f92304ddd3ec2b3d44d4f07fb64ab815c6d
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755948"
---
### <a name="which-regions-are-available"></a><a name="regions"></a>Které oblasti jsou k dispozici?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="do-i-need-a-public-ip-on-my-virtual-machine"></a><a name="publicip"></a>Potřebuji na svém virtuálním počítači veřejnou IP adresu?

Když se připojíte k VIRTUÁLNÍmu počítači pomocí Azure bastionu, nepotřebujete veřejnou IP adresu na virtuálním počítači Azure, ke kterému se připojujete. Služba bastionu otevře relaci RDP/SSH nebo připojení k virtuálnímu počítači přes soukromou IP adresu vašeho virtuálního počítače v rámci vaší virtuální sítě.

### <a name="is-ipv6-supported"></a>Je podporován protokol IPv6?

V současné době se nepodporuje protokol IPv6. Azure bastionu podporuje jenom protokol IPv4.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Potřebuji klienta RDP nebo SSH?

Pro přístup k RDP/SSH ke svému virtuálnímu počítači Azure ve vašem Azure Portal nepotřebujete klienta RDP nebo SSH. Pomocí [Azure Portal](https://portal.azure.com) můžete získat přístup RDP/SSH k virtuálnímu počítači přímo v prohlížeči.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Potřebuji na virtuálním počítači Azure agenta, který je spuštěný?

V prohlížeči nebo na virtuálním počítači Azure nemusíte instalovat agenta ani žádný software. Služba bastionu je bez agentů a nevyžaduje žádný další software pro RDP/SSH.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Kolik souběžných relací protokolu RDP a SSH podporují jednotlivé služby Azure bastionu?

Protokol RDP i SSH jsou protokolem založeným na využití. Vysoké využití relací způsobí, že hostitel bastionu bude podporovat nižší celkový počet relací. Následující čísla předpokládají běžné každodenní pracovní postupy.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>Jaké funkce jsou podporovány v relaci RDP?

V současné době se podporuje pouze kopírování a vkládání textu. Funkce, jako je kopírování souborů, se nepodporují. O své názory na nové funkce se můžete podělit na [stránce pro názory na službu Azure Bastion](https://feedback.azure.com/forums/217313-networking?category_id=367303).

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>Podporuje bastionu posílení práce s virtuálními počítači připojenými k rozšíření virtuálních počítačů AADJ?

Tato funkce nefunguje s počítači připojenými k rozšíření virtuálních počítačů s AADJ pomocí uživatelů Azure AD. Další informace najdete v tématu [virtuální počítače s Windows Azure a Azure AD](../articles/active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements).

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Které prohlížeče se podporují?

Použijte prohlížeč Microsoft Edge nebo Google Chrome ve Windows. Pro Apple Mac použijte prohlížeč Google Chrome. Microsoft Edge chrom se podporuje i v obou oknech i v počítačích Mac.

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Kde Azure bastionu Store data Customer?

Azure bastionu nepřesouvá ani neukládá zákaznická data mimo oblast, ve které je nasazená.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Vyžadují se pro přístup k virtuálnímu počítači nějaké role?

Aby bylo možné vytvořit připojení, jsou vyžadovány následující role:

* Role čtenář na virtuálním počítači
* Role čtecího zařízení na síťové kartě s privátní IP adresou virtuálního počítače
* Role čtenář v prostředku Azure bastionu

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Jaké jsou ceny?

Další informace najdete na [stránce s cenami](https://aka.ms/BastionHostPricing).

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Vyžaduje Azure bastionu licenci VP CAL pro účely správy na virtuálních počítačích hostovaných v Azure?
Ne, přístup k virtuálním počítačům s Windows serverem pomocí Azure bastionu nevyžaduje oprávnění [VP CAL](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) , pokud se používá výhradně pro účely správy.

### <a name="what-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Jaká rozložení klávesnice jsou podporovaná během vzdálené relace bastionu?

Azure bastionu v současné době podporuje rozložení klávesnice en-US-QWERTY uvnitř virtuálního počítače.  Podpora pro jiné národní prostředí pro rozložení klávesnice probíhá v práci.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Je uživatelsky definované směrování (UDR) podporované v podsíti Azure bastionu?

Ne. UDR se nepodporuje v podsíti Azure bastionu.
V případě scénářů, které zahrnují virtuální zařízení Azure bastionu i Azure Firewall/sítě (síťové virtuální zařízení) ve stejné virtuální síti, nemusíte vynutit provoz z podsítě Azure bastionu Azure Firewall, protože komunikace mezi Azure bastionu a virtuálními počítači je privátní. Další informace najdete v tématu [přístup k virtuálním počítačům za Azure firewall pomocí bastionu](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Proč se zobrazí chybová zpráva vypršela platnost vaší relace před spuštěním relace bastionu?

Relace by měla být iniciována pouze z Azure Portal. Přihlaste se k Azure Portal a znovu spusťte relaci. Pokud přejdete na adresu URL přímo z jiné relace nebo karty prohlížeče, tato chyba se očekává. Pomáhá zajistit, aby byla relace bezpečnější a aby k ní bylo možné přicházet pouze prostřednictvím Azure Portal.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Návody zvládnout selhání nasazení?

Zkontrolujte všechny chybové zprávy a podle potřeby [vyvolejte požadavek na podporu v Azure Portal](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) . Selhání nasazení může vést k [omezením předplatného Azure, kvót a omezení](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Konkrétně zákazníci mohou narazit na limit počtu veřejných IP adres povolených pro každé předplatné, které způsobí selhání nasazení služby Azure bastionu.
