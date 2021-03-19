---
title: Přístup k řešení Azure VMware pomocí CloudSimple z místního prostředí
titleSuffix: Azure VMware Solution by CloudSimple
description: Přístup k řešení Azure VMware pomocí CloudSimple z místní sítě přes bránu firewall
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6ff057d99e29c7c6fe30e77f38a0bff265dbe7bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86998883"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>Přístup k prostředí privátního cloudu a k aplikacím v CloudSimple z místního prostředí

Připojení můžete nastavit z místní sítě na CloudSimple s využitím Azure ExpressRoute nebo VPN typu Site-to-site.  Přístup k CloudSimple privátnímu cloudu vCenter a jakýmkoli úlohám, které spouštíte v privátním cloudu pomocí připojení.  Pomocí brány firewall v místní síti můžete řídit, které porty se v připojení otevřou.  Tento článek popisuje některé z typických požadavků na porty aplikací.  Pro všechny ostatní aplikace vyhledejte požadavky na port v dokumentaci k aplikaci.

## <a name="ports-required-for-accessing-vcenter"></a>Porty vyžadované pro přístup k serveru vCenter

Aby bylo možné přistupovat ke Správci privátního cloudu vCenter a NSX-T, musí se v místní bráně firewall otevřít porty definované v následující tabulce.  

| Port       | Zdroj                           | Cíl                      | Účel                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Místní servery DNS          | Servery DNS privátního cloudu        | Vyžaduje se pro přesměrování vyhledávání DNS *AZ.cloudsimple.IO* na servery DNS privátního cloudu z místní sítě.       |
| 53 (UDP)   | Servery DNS privátního cloudu        | Místní servery DNS          | Vyžaduje se pro přesměrování DNS hledání místních názvů domén z privátního cloudu vCenter na místní servery DNS. |
| 80 (TCP)   | Místní síť              | Síť pro správu privátního cloudu | Vyžaduje se pro přesměrování adresy URL vCenter z *http* na *https*.                                                           |
| 443 (TCP)  | Místní síť              | Síť pro správu privátního cloudu | Vyžaduje se pro přístup správce vCenter a NSX-T z místní sítě.                                             |
| 8000 (TCP) | Místní síť              | Síť pro správu privátního cloudu | Vyžaduje se pro vMotion virtuálních počítačů z místního do privátního cloudu.                                            |
| 8000 (TCP) | Síť pro správu privátního cloudu | Místní síť              | Vyžaduje se pro vMotion virtuálních počítačů z privátního cloudu do místního prostředí.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Porty vyžadované pro použití místní služby Active Directory jako zdroje identity

Pokud chcete nakonfigurovat místní službu Active Directory jako zdroj identity v privátním cloudu vCenter, musí se otevřít porty definované v tabulce.  Postup konfigurace najdete v tématu [použití Azure AD jako zprostředkovatele identity pro vCenter v CloudSimple privátním cloudu](./azure-ad.md) .

| Port         | Zdroj                           | Cíl                                         | Účel                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Servery DNS privátního cloudu        | Místní servery DNS                             | Vyžaduje se pro přesměrování DNS k vyhledání místních názvů domén služby Active Directory z privátního cloudu vCenter na místní servery DNS.          |
| 389 (TCP/UDP) | Síť pro správu privátního cloudu | Místní řadiče domény služby Active Directory     | Vyžaduje se pro komunikaci protokolu LDAP z privátního cloudového serveru vCenter do řadičů domény služby Active Directory pro ověřování uživatelů.                |
| 636 (TCP)     | Síť pro správu privátního cloudu | Místní řadiče domény služby Active Directory     | Vyžaduje se pro komunikaci zabezpečeného protokolu LDAP (LDAPs) z privátního cloudového serveru vCenter do řadičů domény služby Active Directory pro ověřování uživatelů. |
| 3268 (TCP)    | Síť pro správu privátního cloudu | Místní servery globálního katalogu služby Active Directory | Vyžaduje se pro komunikaci protokolu LDAP v nasazeních s více řadiči domény.                                                                        |
| 3269 (TCP)    | Síť pro správu privátního cloudu | Místní servery globálního katalogu služby Active Directory | Vyžaduje se pro komunikaci LDAPs v nasazeních s více řadiči domény.                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Běžné porty vyžadované pro přístup k virtuálním počítačům s úlohami

Přístup k virtuálním počítačům s úlohami, které běží v privátním cloudu, vyžaduje otevření portů na místní bráně firewall.  V tabulce níže jsou uvedeny některé z běžných potřebných portů a jejich účel.  Všechny požadavky na porty specifické pro aplikaci najdete v dokumentaci k aplikaci.

| Port         | Zdroj                         | Cíl                          | Účel                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Místní síť            | Síť s úlohou privátního cloudu       | Zabezpečený přístup k prostředí pro virtuální počítače se systémem Linux běžící v privátním cloudu.              |
| 3389 (TCP)    | Místní síť            | Síť s úlohou privátního cloudu       | Vzdálená plocha do virtuálních počítačů s Windows, které běží na privátním cloudu.                 |
| 80 (TCP)      | Místní síť            | Síť s úlohou privátního cloudu       | Přístup k jakémukoli webovému serveru nasazenému na virtuálních počítačích běžících na privátním cloudu.        |
| 443 (TCP)     | Místní síť            | Síť s úlohou privátního cloudu       | Přístup k jakýmkoli zabezpečeným webovým serverům nasazeným na virtuálních počítačích běžících na privátním cloudu. |
| 389 (TCP/UDP) | Síť s úlohou privátního cloudu | Místní síť služby Active Directory | Připojte virtuální počítače s úlohou Windows k místní doméně Active Directory.       |
| 53 (UDP)      | Síť s úlohou privátního cloudu | Místní síť                  | Přístup ke službě DNS pro virtuální počítače úlohy na místní servery DNS.         |

## <a name="next-steps"></a>Další kroky

* [Vytváření a správa sítí VLAN a podsítí](./create-vlan-subnet.md)
* [Připojení k místní síti pomocí Azure ExpressRoute](./on-premises-connection.md)
* [Nastavení VPN typu Site-to-Site z místního prostředí](./vpn-gateway.md)
