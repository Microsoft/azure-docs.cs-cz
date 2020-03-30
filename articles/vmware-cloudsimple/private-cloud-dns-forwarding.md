---
title: Řešení Azure VMware – přeposílání DNS z privátního cloudu do místního
description: Popisuje, jak povolit serveru DNS CloudSimple Private Cloud pro dopředné vyhledávání místních prostředků.
author: sharaths-cs
ms.author: b-shsury
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aa2af4302613aad23bfd78b4883bbb46c5e5ddbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961124"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>Povolení serverů DNS CloudSimple Pricloud Cloud k předávání vyhledávání DNS místních prostředků na vaše servery DNS

Servery DNS privátního cloudu mohou předávat vyhledávání DNS pro všechny místní prostředky na vaše servery DNS.  Povolení vyhledávání umožňuje privcloudových komponent vSphere vyhledat všechny služby spuštěné v místním prostředí a komunikovat s nimi pomocí plně kvalifikovaných názvů domén (FQDN).

## <a name="scenarios"></a>Scénáře 

Předávání vyhledávání DNS pro místní server DNS umožňuje používat privátní cloud pro následující scénáře:

* Použití privátního cloudu jako nastavení zotavení po havárii pro vaše místní řešení VMware
* Použití místní služby Active Directory jako zdroje identity pro privourný cloud vSphere
* Použití HCX pro migraci virtuálních počítačů z místního do privátního cloudu

## <a name="before-you-begin"></a>Než začnete

Aby přesměrování DNS fungovalo, musí být k dispozici síťové připojení ze sítě Privátního cloudu do místní sítě.  Síťové připojení můžete nastavit pomocí:

* [Připojení z místního prostředí ke CloudSimple pomocí ExpressRoute](on-premises-connection.md)
* [Nastavení brány VPN mezi lokalitami](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway#set-up-a-site-to-site-vpn-gateway)

Aby přesměrování DNS fungovalo, musí být v tomto připojení otevřeny porty brány firewall.  Použité porty jsou port TCP 53 nebo Port UDP 53.

> [!NOTE]
> Pokud používáte síť VPN site-to-site, musí být vaše místní podsíť serveru DNS přidána jako součást místních předpon.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>Žádost o přesměrování DNS z privátního cloudu do místního

Chcete-li povolit předávání DNS z privátního cloudu do místního, odešlete [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)a zadejte následující informace.

* Typ problému: **Technické**
* Předplatné: **Předplatné, kde se nasadí služba CloudSimple**
* Služba: **Řešení VMware od CloudSimple**
* Typ problému: **Informační zpravodaj nebo Jak mám...**
* Problém podtyp: **Potřebujete pomoc s NW**
* V podokně podrobností zadejte název domény místní domény.
* V podokně podrobností zadejte seznam místních serverů DNS, na které bude vyhledávání předáváno z vašeho privátního cloudu.

## <a name="next-steps"></a>Další kroky

* [Další informace o místní konfiguraci brány firewall](on-premises-firewall-configuration.md)
* [Konfigurace místního serveru DNS](on-premises-dns-setup.md)
