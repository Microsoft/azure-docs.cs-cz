---
title: Integrace Microsoft Azure s cloudovou infrastrukturou Oracle | Microsoft Docs
description: Seznamte se s řešeními, která integrují aplikace Oracle běžící na Microsoft Azure s databázemi v Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: borisb
ms.custom: ''
ms.openlocfilehash: e70eedcfcdf548965b79e4a48a3a8bfa643f0396
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687429"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>Řešení aplikací Oracle integrující Microsoft Azure a Oracle cloudovou infrastrukturu

Microsoft a Oracle mají partnerství, aby poskytovaly nízkou latenci a vysokou propustnost připojení mezi cloudy, což vám umožní využít výhod obou cloudů. 

Pomocí tohoto připojení mezi cloudy můžete rozdělit vícevrstvou aplikaci tak, aby běžela vaše databázová vrstva na platformě Oracle Cloud Infrastructure (OCI), a na Microsoft Azure aplikace a další vrstvy. Prostředí je podobné jako spuštění celého zásobníku řešení v jednom cloudu. 

Pokud vás zajímá nasazení řešení Oracle výhradně do infrastruktury Azure, přečtěte si téma [image virtuálních počítačů Oracle a jejich nasazení na Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Přehled scénáře

Připojení mezi cloudy poskytuje řešení pro spouštění špičkových aplikací od Oracle a vašich vlastních aplikací na virtuálních počítačích Azure a současně přináší výhody hostovaných databázových služeb v rozhraní OCI. 

> [!IMPORTANT]
> Oracle tyto aplikace osvědčí, aby běžely v Azure při použití řešení Cloud Interconnect Azure/Oracle od května 2020.

Mezi aplikace, které můžete spustit v konfiguraci mezi cloudy patří:

* Sada E-Business
* ŘEŠENÍ JD Edwards EnterpriseOne
* PeopleSoft spouštěných místně
* Maloobchodní aplikace Oracle
* Hyperion finanční správa Oracle

Následující diagram představuje podrobný přehled připojeného řešení. Pro zjednodušení diagram zobrazuje pouze aplikační vrstvu a datovou vrstvu. V závislosti na architektuře aplikace může vaše řešení zahrnovat další úrovně, jako je například webová vrstva v Azure. Další informace najdete v následujících oddílech.

![Přehled řešení Azure OCI](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Dostupnost oblasti 

Připojení mezi cloudy je omezené na tyto oblasti:
* Azure Východní USA (eastus) & OCI Ashburn (USA – východ)
* Azure Velká Británie – jih (uksouth) & OCI Londýn (Velká Británie – jih)
* Azure Canada Central (canadacentral) & OCI Toronto (Kanada – jihovýchod)
* Azure Západní Evropa (westeurope) & OCI Amsterdam (Nizozemsko – severozápadní)
* Azure Japonsko – východ (japaneast) & OCI – Tokio (Japonsko – východ)

## <a name="networking"></a>Sítě

Podnikoví zákazníci se často rozsazují a nasazují úlohy do několika cloudů pro různé obchodní a provozní důvody. K diverzifikaci zákazníci propojovat cloudové sítě pomocí Internetu, IPSec VPN nebo pomocí přímého připojení poskytovatele cloudu prostřednictvím místní sítě. Propojení cloudových sítí může vyžadovat významné investice v čase, peníze, návrh, nákup, instalaci, testování a provoz. 

Pro řešení těchto výzev zákazníků Oracle a Microsoft povolili integrované prostředí pro více cloudů. Síť mezi cloudy se vytváří připojením okruhu [ExpressRoute](../../../expressroute/expressroute-introduction.md) v Microsoft Azure s okruhem [FASTCONNECT](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) v rozhraní OCI. Toto připojení je možné v případě, že umístění partnerského vztahu Azure ExpressRoute je v blízkosti nebo ve stejném umístění partnerského vztahu jako rozhraní OCI FastConnect. Tato instalace umožňuje bezpečné a rychlé připojení mezi těmito dvěma cloudy bez nutnosti poskytovatele zprostředkujících služeb.

Pomocí ExpressRoute a FastConnect můžou zákazníci vytvořit partnerský vztah k virtuální síti v Azure s virtuální cloudovou sítí v rozhraní OCI za předpokladu, že se privátní adresní prostor IP adres nepřekrývá. Partnerský vztah ke dvěma sítím umožňuje prostředku ve virtuální síti komunikovat s prostředkem ve virtuální cloudové síti OCI, jako by byly ve stejné síti.

## <a name="network-security"></a>Zabezpečení sítě

Zabezpečení sítě je zásadní součástí libovolné podnikové aplikace a je centrální k tomuto řešení pro více cloudů. Veškerý provoz procházející ExpressRoute a FastConnect se předává přes soukromou síť. Tato konfigurace umožňuje zabezpečenou komunikaci mezi virtuální sítí Azure a virtuální cloudovou sítí Oracle. Nemusíte zadávat veřejnou IP adresu pro žádné virtuální počítače v Azure. Obdobně nepotřebujete internetovou bránu v rozhraní OCI. Veškerá komunikace probíhá prostřednictvím privátní IP adresy počítačů.

Kromě toho můžete nastavit [seznamy zabezpečení](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) ve vaší virtuální cloudové síti OCI a pravidla zabezpečení (připojené ke [skupinám zabezpečení sítě](../../../virtual-network/security-overview.md)Azure). Pomocí těchto pravidel můžete řídit tok provozu mezi počítači ve virtuálních sítích. Pravidla zabezpečení sítě je možné přidat na úrovni počítače, na úrovni podsítě a také na úrovni virtuální sítě.
 
## <a name="identity"></a>Identita

Identita je jedním z hlavních pilířů partnerství mezi společnostmi Microsoft a Oracle. Pro integraci [služby Oracle identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) pomocí služby [Azure Active Directory](../../../active-directory/index.yml) (Azure AD) se udělala významná práce. Azure AD je cloudová služba pro správu identit a přístupu od Microsoftu. Pomáhá uživatelům přihlašovat se k různým prostředkům a přistupovat k nim. Azure AD také umožňuje spravovat uživatele a jejich oprávnění.

V současné době tato integrace umožňuje správu v jednom centrálním umístění, které je Azure Active Directory. Azure AD synchronizuje všechny změny v adresáři s odpovídajícím adresářem Oracle a používá se pro jednotné přihlašování do řešení Oracle pro více cloudů.

## <a name="next-steps"></a>Další kroky

Začněte s mezi [cloudovou sítí](configure-azure-oci-networking.md) mezi Azure a OCI. 

Další informace a dokumenty White paper o rozhraní OCI najdete v dokumentaci ke [cloudu Oracle](https://docs.cloud.oracle.com/iaas/Content/home.htm) .
