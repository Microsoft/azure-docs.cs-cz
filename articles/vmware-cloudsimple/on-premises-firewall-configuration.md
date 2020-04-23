---
title: Přístup k řešení Azure VMware od CloudSimple z místního prostředí
titleSuffix: Azure VMware Solution by CloudSimple
description: Přístup k řešení Azure VMware pomocí CloudSimple z místní sítě přes bránu firewall
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 539665c4756a7dc87078922421b45a88404f58f1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868143"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>Přístup k prostředí cloudu CloudSimple privátního cloudu a aplikacím z místního prostředí

Připojení lze nastavit z místní sítě na CloudSimple pomocí Azure ExpressRoute nebo Site-to-Site VPN.  Získejte přístup k virtuálnímu počítači CloudSimple Private Cloud vCenter a všem úlohám, které spustíte v privátním cloudu pomocí připojení.  Můžete určit, jaké porty se otevřou při připojení pomocí brány firewall v místní síti.  Tento článek popisuje některé typické požadavky na port aplikace.  Pro všechny ostatní aplikace, naleznete v dokumentaci k aplikaci pro požadavky na port.

## <a name="ports-required-for-accessing-vcenter"></a>Porty potřebné pro přístup k vCenter

Chcete-li získat přístup k priviontnímu cloudu vCenter a správci NSX-T, musí být porty definované v následující tabulce otevřeny v místní bráně firewall.  

| Port       | Zdroj                           | Cíl                      | Účel                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Místní servery DNS          | Privátní cloudové servery DNS        | Vyžadováno pro předávání vyhledávání *DNS az.cloudsimple.io* servery DNS privátního cloudu z místní sítě.       |
| 53 (UDP)   | Privátní cloudové servery DNS        | Místní servery DNS          | Vyžadováno pro předávání dns vyhledávání názvů místních domén z private cloudu vCenter na místní servery DNS. |
| 80 (TCP)   | Místní síť              | Privátní síť pro správu cloudu | Povinné pro přesměrování adresy URL vCenter z *http* na *https*.                                                           |
| 443 (TCP)  | Místní síť              | Privátní síť pro správu cloudu | Vyžadováno pro přístup k vCenter a NSX-T manager z místní sítě.                                             |
| 8000 (TCP) | Místní síť              | Privátní síť pro správu cloudu | Vyžadováno pro vMotion virtuálních počítačů z místního do privátního cloudu.                                            |
| 8000 (TCP) | Privátní síť pro správu cloudu | Místní síť              | Vyžadováno pro vMotion virtuálních počítačů z Privátního cloudu do místního.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Porty potřebné pro použití místního aktivního adresáře jako zdroje identity

Chcete-li nakonfigurovat místní active adresář jako zdroj identity v programu Private Cloud vCenter, musí být otevřeny porty definované v tabulce.  Viz [Použití Azure AD jako zprostředkovatele identity pro vCenter na CloudSimple Privátní cloud](https://docs.microsoft.com/azure/vmware-cloudsimple/azure-ad/) pro kroky konfigurace.

| Port         | Zdroj                           | Cíl                                         | Účel                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Privátní cloudové servery DNS        | Místní servery DNS                             | Vyžadováno pro předávání názvů domén služby DNS v místním prostředí služby Active Directory z centra Private Cloud vCenter na místní servery DNS.          |
| 389 (TCP/UDP) | Privátní síť pro správu cloudu | Místní řadiče domény služby Active Directory     | Vyžadováno pro komunikaci LDAP ze serveru vCenter privátního cloudu do řadičů domény služby Active Directory pro ověřování uživatelů.                |
| 636 (TCP)     | Privátní síť pro správu cloudu | Místní řadiče domény služby Active Directory     | Vyžadováno pro zabezpečenou komunikaci LDAP (LDAPS) ze serveru vCenter privátního cloudu do řadičů domény služby Active Directory pro ověřování uživatelů. |
| 3268 (TCP)    | Privátní síť pro správu cloudu | Místní servery globálního katalogu služby Active Directory | Vyžadováno pro komunikaci LDAP v nasazení řadičů s více doménami.                                                                        |
| 3269 (TCP)    | Privátní síť pro správu cloudu | Místní servery globálního katalogu služby Active Directory | Vyžadováno pro komunikaci LDAPS v nasazení řadičů s více doménami.                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Běžné porty potřebné pro přístup k virtuálním počítačům úloh

Přístup k virtuálním počítačům s úlohami spuštěné v privátním cloudu vyžaduje, aby se porty otevíralo v místní bráně firewall.  Níže uvedená tabulka ukazuje některé požadované společné porty a jejich účel.  Všechny požadavky na porty specifické pro aplikaci naleznete v dokumentaci k aplikaci.

| Port         | Zdroj                         | Cíl                          | Účel                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Místní síť            | Síť úloh privátního cloudu       | Zabezpečte shellový přístup k virtuálním počítačům Linuxu běžícím na Privátním cloudu.              |
| 3389 (TCP)    | Místní síť            | Síť úloh privátního cloudu       | Vzdálená plocha virtuálním počítačům windows spuštěnými v privátním cloudu.                 |
| 80 (TCP)      | Místní síť            | Síť úloh privátního cloudu       | Získejte přístup ke všem webovým serverům nasazeným na virtuálních počítačích spuštěných v privátním cloudu.        |
| 443 (TCP)     | Místní síť            | Síť úloh privátního cloudu       | Získejte přístup ke zabezpečeným webovým serverům nasazeným na virtuálních počítačích spuštěných v privátním cloudu. |
| 389 (TCP/UDP) | Síť úloh privátního cloudu | Místní síť aktivních adresářů | Připojte virtuální počítače s úlohami Windows k místní doméně služby Active Directory.       |
| 53 (UDP)      | Síť úloh privátního cloudu | Místní síť                  | Přístup ke službě DNS pro virtuální počítače s úlohami k místním serverům DNS.         |

## <a name="next-steps"></a>Další kroky

* [Vytvoření a správa sítí VLAN a podsítí](https://docs.microsoft.com/azure/vmware-cloudsimple/create-vlan-subnet/)
* [Připojení k místní síti pomocí Azure ExpressRoute](https://docs.microsoft.com/azure/vmware-cloudsimple/on-premises-connection/)
* [Nastavení sítě VPN mezi lokalitami z místního prostředí](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway/)
