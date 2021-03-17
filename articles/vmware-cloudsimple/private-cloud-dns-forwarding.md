---
title: Řešení Azure VMware – předávání DNS z privátního cloudu do místního prostředí
description: Popisuje, jak povolit serveru DNS privátního cloudu CloudSimple, aby bylo možné dopředné vyhledávání místních prostředků.
author: Ajayan1008
ms.author: v-hborys
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3481e21a93fbf5f658c5ca00513ea179c8f95400
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899197"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>Povolit serverům DNS privátního cloudu CloudSimple přesměrování vyhledávání místních prostředků DNS na servery DNS

Servery DNS privátního cloudu můžou přeslat vyhledávání DNS pro všechny místní prostředky na servery DNS.  Povolením vyhledávání umožníte součástem privátního cloudu vSphere vyhledat jakékoli služby běžící v místním prostředí a komunikovat s nimi pomocí plně kvalifikovaných názvů domény (FQDN).

## <a name="scenarios"></a>Scénáře 

Předávání vyhledávání DNS pro místní server DNS vám umožní používat privátní cloud v následujících situacích:

* Použití privátního cloudu jako nastavení zotavení po havárii pro vaše místní řešení VMware
* Použití místní služby Active Directory jako zdroje identity pro privátní cloud vSphere
* Použití HCX pro migraci virtuálních počítačů z místního do privátního cloudu

## <a name="before-you-begin"></a>Než začnete

Aby předávání DNS fungovalo, musí být síťové připojení k vaší místní síti přítomné z vaší privátní cloudové sítě.  Připojení k síti můžete nastavit pomocí:

* [Připojení z místního prostředí k CloudSimple pomocí ExpressRoute](on-premises-connection.md)
* [Nastavení brány VPN typu Site-to-site](./vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)

Aby bylo možné přeposlání DNS fungovat, musí být v tomto připojení otevřené porty brány firewall.  Používané porty jsou port TCP 53 nebo port UDP 53.

> [!NOTE]
> Pokud používáte síť Site-to-Site VPN, musí být místní podsíť DNS serveru přidaná jako součást místních předpon.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>Vyžádat předávání DNS z privátního cloudu do místního prostředí

Pokud chcete povolit předávání DNS z privátního cloudu do místního prostředí, odešlete [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), která poskytne následující informace.

* Typ problému: **technický**
* Předplatné: **předplatné, ve kterém je nasazená služba CloudSimple**
* Služba: **řešení VMware podle CloudSimple**
* Typ problému: **Advisor nebo návody...**
* Podtyp problému: **potřebujete pomáhat s typem NW** .
* V podokně podrobností zadejte název domény vaší místní domény.
* Zadejte seznam místních serverů DNS, pro které bude vyhledávání předáváno z vašeho privátního cloudu v podokně podrobností.

## <a name="next-steps"></a>Další kroky

* [Další informace o místní konfiguraci brány firewall](on-premises-firewall-configuration.md)
* [Konfigurace místního serveru DNS](on-premises-dns-setup.md)
