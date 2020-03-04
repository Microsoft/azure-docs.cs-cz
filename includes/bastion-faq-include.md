---
title: zahrnout soubor
description: zahrnout soubor
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 03/02/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 17d391a7e6b8ef0558fb73afe363cd96deb60a7d
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262592"
---
### <a name="regions"></a>Které oblasti jsou k dispozici?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>Potřebuji na svém virtuálním počítači veřejnou IP adresu?

Když se připojíte k VIRTUÁLNÍmu počítači pomocí Azure bastionu, nepotřebujete veřejnou IP adresu na virtuálním počítači Azure, ke kterému se připojujete. Služba bastionu otevře relaci RDP/SSH nebo připojení k virtuálnímu počítači přes soukromou IP adresu vašeho virtuálního počítače v rámci vaší virtuální sítě.

### <a name="is-ipv6-supported"></a>Je podporován protokol IPv6?

V současné době se nepodporuje protokol IPv6. Azure bastionu podporuje jenom protokol IPv4.

### <a name="rdpssh"></a>Potřebuji klienta RDP nebo SSH?

Pro přístup k RDP/SSH ke svému virtuálnímu počítači Azure ve vašem Azure Portal nepotřebujete klienta RDP nebo SSH. Pomocí [Azure Portal](https://portal.azure.com) můžete získat přístup RDP/SSH k virtuálnímu počítači přímo v prohlížeči.

### <a name="rdscal"></a>Vyžaduje Azure bastionu licenci VP CAL pro účely správy na virtuálních počítačích hostovaných v Azure?
Ne, přístup k virtuálním počítačům s Windows serverem pomocí Azure bastionu nevyžaduje oprávnění [VP CAL](https://www.microsoft.com/en-us/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) , pokud se používá výhradně pro účely správy.

### <a name="agent"></a>Potřebuji na virtuálním počítači Azure agenta, který je spuštěný?

V prohlížeči nebo na virtuálním počítači Azure nemusíte instalovat agenta ani žádný software. Služba bastionu je bez agentů a nevyžaduje žádný další software pro RDP/SSH.

### <a name="browsers"></a>Které prohlížeče se podporují?

Použijte prohlížeč Microsoft Edge nebo Google Chrome ve Windows. Pro Apple Mac použijte prohlížeč Google Chrome. Microsoft Edge chrom se podporuje i v obou oknech i v počítačích Mac.

### <a name="roles"></a>Vyžadují se pro přístup k virtuálnímu počítači nějaké role?

Aby bylo možné vytvořit připojení, jsou vyžadovány následující role:

* Role čtenář na virtuálním počítači
* Role čtecího zařízení na síťové kartě s privátní IP adresou virtuálního počítače
* Role čtenář v prostředku Azure bastionu

### <a name="pricingpage"></a>Jaké jsou ceny?

Další informace najdete na [stránce s cenami](https://aka.ms/BastionHostPricing).

### <a name="session"></a>Proč se zobrazí chybová zpráva vypršela platnost vaší relace před spuštěním relace bastionu?

Relace by měla být iniciována pouze z Azure Portal. Přihlaste se k Azure Portal a znovu spusťte relaci. Pokud přejdete na adresu URL přímo z jiné relace nebo karty prohlížeče, tato chyba se očekává. Pomáhá zajistit, aby byla relace bezpečnější a aby k ní bylo možné přicházet pouze prostřednictvím Azure Portal.

### <a name="keyboard"></a>Jaká rozložení klávesnice jsou podporovaná během vzdálené relace bastionu?

Azure bastionu v současné době podporuje rozložení klávesnice en-US-QWERTY uvnitř virtuálního počítače.  Podpora pro jiné národní prostředí pro rozložení klávesnice probíhá v práci.

### <a name="udr"></a>Je uživatelsky definované směrování (UDR) podporované v podsíti Azure bastionu?

Ne. UDR se nepodporuje v podsíti Azure bastionu.
V případě scénářů, které zahrnují virtuální zařízení Azure bastionu i Azure Firewall/sítě (síťové virtuální zařízení) ve stejné virtuální síti, nemusíte vynutit provoz z podsítě Azure bastionu Azure Firewall, protože komunikace mezi Azure bastionu a virtuálními počítači je privátní. Další informace najdete v tématu [přístup k virtuálním počítačům za Azure firewall pomocí bastionu](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="filetransfer"></a>Podporuje se přenos souborů v relaci Azure bastionu RDP?

Těžko pracujeme na přidávání nových funkcí. Od tohoto okamžiku není přenos souborů podporovaný, ale je součástí našeho plánu. Můžete si prosím zdarma sdílet svůj názor na nové funkce na [stránce s názory na Azure bastionu](https://feedback.azure.com/forums/217313-networking?category_id=367303).
