---
title: Přístup k řešením Azure VMware (AVS) z místního prostředí
description: Přístup k řešením Azure VMware (AVS) z místní sítě přes bránu firewall
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a4a9760b5c7a70c58a1afe1b14b781a35f2b9b18
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2020
ms.locfileid: "77082967"
---
# <a name="accessing-your-avs-private-cloud-environment-and-applications-from-on-premises"></a>Přístup k prostředí privátního cloudu služby AVS a aplikacím z místního prostředí

Připojení můžete nastavit z místní sítě na službu AVS pomocí Azure ExpressRoute nebo VPN typu Site-to-site. Přístup ke službě AVS Private Cloud vCenter a jakýmkoli úlohám, které spouštíte v privátním cloudu služby AVS, pomocí připojení. Pomocí brány firewall v místní síti můžete řídit, které porty se v připojení otevřou. Tento článek popisuje některé z typických požadavků na porty aplikací. Pro všechny ostatní aplikace vyhledejte požadavky na port v dokumentaci k aplikaci.

## <a name="ports-required-for-accessing-vcenter"></a>Porty vyžadované pro přístup k serveru vCenter

Aby bylo možné přistupovat ke službě AVS Private Cloud vCenter a správce NSX-T, musí se v místní bráně firewall otevřít porty definované v následující tabulce. 

| Port       | Zdroj                           | Cíl                      | Účel                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Místní servery DNS          | Servery DNS privátního cloudu služby AVS        | Vyžaduje se pro předávání vyhledávání DNS *AZ.cloudsimple.IO* do služby AVS servery DNS privátního cloudu z místní sítě.     |
| 53 (UDP)   | Servery DNS privátního cloudu služby AVS        | Místní servery DNS          | Vyžaduje se pro přesměrování DNS z hledání místních názvů domén z služby AVS Private Cloud vCenter na místní servery DNS. |
| 80 (TCP)   | Místní síť              | Síť pro správu privátního cloudu pro funkci AVS | Vyžaduje se pro přesměrování adresy URL vCenter z *http* na *https*.                                                         |
| 443 (TCP)  | Místní síť              | Síť pro správu privátního cloudu pro funkci AVS | Vyžaduje se pro přístup správce vCenter a NSX-T z místní sítě.                                           |
| 8000 (TCP) | Místní síť              | Síť pro správu privátního cloudu pro funkci AVS | Vyžaduje se pro vMotion virtuálních počítačů z místního prostředí do služby AVS privátní cloud.                                          |
| 8000 (TCP) | Síť pro správu privátního cloudu pro funkci AVS | Místní síť              | Vyžaduje se pro vMotion virtuálních počítačů z privátního cloudu služby AVS do místního prostředí.                                          |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Porty vyžadované pro použití místní služby Active Directory jako zdroje identity

Pokud chcete nakonfigurovat místní službu Active Directory jako zdroj identity v rámci služby AVS Private Cloud vCenter, musí se otevřít porty definované v tabulce. Postup konfigurace najdete v tématu [použití Azure AD jako zprostředkovatele identity pro vCenter v privátním cloudu služby](https://docs.azure.cloudsimple.com/azure-ad/) AVS.

| Port         | Zdroj                           | Cíl                                         | Účel                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Servery DNS privátního cloudu služby AVS        | Místní servery DNS                             | Vyžaduje se pro přesměrování DNS k vyhledání místních názvů domén služby Active Directory ze služby AVS Private Cloud vCenter na místní servery DNS.        |
| 389 (TCP/UDP) | Síť pro správu privátního cloudu pro funkci AVS | Místní řadiče domény služby Active Directory     | Vyžaduje se pro komunikaci protokolu LDAP z neaktivního cloudového serveru vCenter do řadičů domény služby Active Directory pro ověřování uživatelů.              |
| 636 (TCP)     | Síť pro správu privátního cloudu pro funkci AVS | Místní řadiče domény služby Active Directory     | Vyžaduje se pro komunikaci pomocí protokolu LDAP (LDAPs) z privátního cloudu služby AVS do řadičů domény služby Active Directory za účelem ověření uživatele. |
| 3268 (TCP)    | Síť pro správu privátního cloudu pro funkci AVS | Místní servery globálního katalogu služby Active Directory | Vyžaduje se pro komunikaci protokolu LDAP v nasazeních s více řadiči domény.                                                                      |
| 3269 (TCP)    | Síť pro správu privátního cloudu pro funkci AVS | Místní servery globálního katalogu služby Active Directory | Vyžaduje se pro komunikaci LDAPs v nasazeních s více řadiči domény.                                                                     |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Běžné porty vyžadované pro přístup k virtuálním počítačům s úlohami

Přístup k virtuálním počítačům s úlohami spuštěnými v privátním cloudu služby AVS vyžaduje otevření portů na místní bráně firewall. V tabulce níže jsou uvedeny některé z běžných potřebných portů a jejich účel. Všechny požadavky na porty specifické pro aplikaci najdete v dokumentaci k aplikaci.

| Port         | Zdroj                         | Cíl                          | Účel                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Místní síť            | Síť úlohy privátního cloudu pro funkci AVS       | Zabezpečený přístup k prostředí pro virtuální počítače se systémem Linux běžící na privátním cloudu služby AVS            |
| 3389 (TCP)    | Místní síť            | Síť úlohy privátního cloudu pro funkci AVS       | Vzdálená plocha na virtuální počítače s Windows, které běží na privátním cloudu služby AVS.               |
| 80 (TCP)      | Místní síť            | Síť úlohy privátního cloudu pro funkci AVS       | Přístup k jakémukoli webovému serveru nasazenému na virtuálních počítačích běžících na privátním cloudu služby AVS      |
| 443 (TCP)     | Místní síť            | Síť úlohy privátního cloudu pro funkci AVS       | Přístup k jakýmkoli zabezpečeným webovým serverům nasazeným na virtuálních počítačích běžících na privátním cloudu služby AVS |
| 389 (TCP/UDP) | Síť úlohy privátního cloudu pro funkci AVS | Místní síť služby Active Directory | Připojte virtuální počítače s úlohou Windows k místní doméně Active Directory.     |
| 53 (UDP)      | Síť úlohy privátního cloudu pro funkci AVS | Místní síť                  | Přístup ke službě DNS pro virtuální počítače úlohy na místní servery DNS.       |

## <a name="next-steps"></a>Další kroky

* [Vytváření a správa sítí VLAN a podsítí](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [Připojení k místní síti pomocí Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Nastavení VPN typu Site-to-Site z místního prostředí](https://docs.azure.cloudsimple.com/vpn-gateway/)
